# Delta Load trong Data Warehouse

> **Delta Load** (hay **Incremental Load**) là chiến lược chỉ tải và xử lý **phần dữ liệu đã thay đổi kể từ lần chạy trước**, thay vì đọc và nạp lại toàn bộ dữ liệu nguồn.

> **Lưu ý:** Delta Load ở đây **không phải Delta Lake**.  
> “Delta” đơn giản có nghĩa là **phần chênh lệch / phần thay đổi**.

---

## 1. Vì sao Delta Load ra đời?

Cách đơn giản nhất để đưa dữ liệu từ hệ thống OLTP vào Data Warehouse là **Full Load**:

```text
OLTP
 │
 │ SELECT *
 ▼
ETL
 │
 ▼
Data Warehouse
```

Ví dụ database production có:

```text
customers    20 triệu rows
orders       500 triệu rows
payments     400 triệu rows
...
```

Nếu mỗi đêm pipeline chạy:

```sql
SELECT * FROM orders;
```

thì toàn bộ bảng `orders` được đọc lại.

Ban đầu database nhỏ thì cách này có thể chấp nhận được. Nhưng khi dữ liệu lớn dần:

```text
Ngày 1:       1 triệu rows
Ngày 1000:  500 triệu rows
```

trong khi từ hôm qua đến hôm nay có thể chỉ có một phần rất nhỏ thực sự thay đổi.

Pipeline lúc này đang làm:

```text
500M rows
████████████████████████████████████████

Dữ liệu thực sự mới/thay đổi
█
```

Vấn đề:

- Tốn CPU trên source database.
- Tốn I/O.
- Tốn network bandwidth.
- Pipeline chạy lâu hơn.
- Tăng chi phí compute.
- Có thể ảnh hưởng hệ thống OLTP đang phục vụ application.

Vì vậy ta chuyển từ:

```text
Load EVERYTHING
```

sang:

```text
Load WHAT CHANGED
```

Đó chính là **Delta Load / Incremental Load**.

---

# 2. Delta thực chất là gì?

Giả sử Data Warehouse đang có:

```text
orders
─────────────────────
order_id   amount
1          100
2          200
3          300
```

Sau đó OLTP xảy ra:

```text
INSERT
order_id = 4

UPDATE
order_id = 2
amount: 200 → 250

DELETE
order_id = 3
```

Delta là:

```text
+ order 4
~ order 2
- order 3
```

Chứ không phải toàn bộ:

```text
order 1
order 2
order 3
order 4
```

Có thể hiểu:

```text
Current Warehouse
       +
Changes since last run
       ↓
New Warehouse State
```

---

# 3. Full Load và Delta Load

## Full Load

```text
Source
┌──────────────────────────┐
│ ████████████████████████ │
│ ████████████████████████ │
│ ████████████████████████ │
└──────────────────────────┘
             │
         LOAD ALL
             ▼
        Warehouse
```

## Delta Load

```text
Source
┌──────────────────────────┐
│ old old old old old      │
│ old old CHANGED old      │
│ NEW NEW old old old      │
└──────────────────────────┘
             │
        only delta
             ▼
        Warehouse
```

Delta Load **không phải một Data Warehouse Architecture**.

Nó là một **chiến lược ingestion/loading**:

```text
OLTP
 ↓
Extraction
 ↓
Delta / Incremental Load
 ↓
Staging
 ↓
Transformation
 ↓
Dimensional Model
 ↓
Data Warehouse
```

---

# 4. Bài toán khó nhất của Delta Load

Câu hỏi khó nhất không phải:

> Làm sao load dữ liệu?

Mà là:

> **Làm sao biết dữ liệu nào đã thay đổi?**

Ví dụ:

```text
Pipeline trước:
10:00

Pipeline tiếp theo:
11:00
```

Pipeline phải xác định:

```text
Trong khoảng 10:00 → 11:00

row nào INSERT?
row nào UPDATE?
row nào DELETE?
```

Có 4 cách phổ biến:

1. WAL / Transaction Log / CDC
2. Timestamp (`created_at`, `updated_at`)
3. Database Trigger
4. Scan + Hash

---

# 5. Cách 1 — WAL / Transaction Log / CDC

Database vốn đã ghi lại các transaction.

Ví dụ PostgreSQL có **WAL — Write-Ahead Log**.

```text
Application
    │
    ▼
PostgreSQL
    │
    ├── Tables
    │
    └── WAL
         │
         ├── INSERT customer 100
         ├── UPDATE order 25
         ├── DELETE payment 8
         ▼
        CDC
         │
         ▼
     Warehouse
```

Thay vì chạy:

```sql
SELECT *
FROM orders;
```

để tự tìm thay đổi, ta đọc log của database.

Log vốn đã biết:

```text
INSERT
UPDATE
DELETE
```

### Ưu điểm

- Không cần scan toàn bộ bảng.
- Có thể phát hiện INSERT, UPDATE, DELETE.
- Latency có thể thấp.
- Phù hợp near-real-time / streaming.
- Ít tạo workload truy vấn trực tiếp lên bảng nguồn.

### Nhược điểm

Infrastructure phức tạp hơn.

Ví dụ:

```text
OLTP
 ↓
Transaction Log
 ↓
CDC Connector
 ↓
Kafka / Stream / File
 ↓
Warehouse
```

Phải xử lý thêm:

```text
offset
checkpoint
ordering
duplicate events
schema evolution
recovery
initial snapshot
```

Các công cụ thường gặp:

- Debezium
- Airbyte
- Kafka Connect
- AWS DMS
- Fivetran

---

# 6. Cách 2 — Timestamp

Đây là cách đơn giản và rất phổ biến.

Source table có:

```sql
orders
────────────────────────
id
customer_id
amount
created_at
updated_at
```

Ví dụ:

```text
id   amount   created_at           updated_at
1    100      2026-08-10 08:00     2026-08-10 08:00
2    200      2026-08-10 09:00     2026-08-18 19:20
3    300      2026-08-11 10:00     2026-08-11 10:00
4    500      2026-08-18 20:00     2026-08-18 20:00
```

Giả sử lần load trước kết thúc tại:

```text
2026-08-18 19:00
```

Pipeline tiếp theo:

```sql
SELECT *
FROM orders
WHERE updated_at > '2026-08-18 19:00';
```

Kết quả:

```text
2
4
```

Có nghĩa:

```text
2 → UPDATE
4 → INSERT
```

Không cần đọc toàn bộ bảng.

---

# 7. Watermark

Để biết lần trước pipeline đã xử lý đến đâu, ta lưu một giá trị gọi là:

**Watermark**

Ví dụ:

```text
Last successful load
        ↓
     Watermark
```

Run 1:

```sql
SELECT *
FROM orders
WHERE updated_at > :last_watermark;
```

Sau khi pipeline thành công:

```text
max(updated_at)
=
2026-08-18 19:42:15
```

Lưu:

```text
watermark = 2026-08-18 19:42:15
```

Run tiếp theo:

```sql
SELECT *
FROM orders
WHERE updated_at > '2026-08-18 19:42:15';
```

Flow:

```text
        Metadata Table
             │
             │ last_watermark
             ▼
Source ──► SELECT changes
             │
             ▼
          Staging
             │
             ▼
         Warehouse
             │
        success?
          │      │
         yes     no
          │
          ▼
 update watermark
```

## Quy tắc quan trọng

**Chỉ update watermark sau khi pipeline thành công.**

Sai:

```text
Extract ✓
Watermark updated ✓

Transform ❌
Load ❌
```

Lần chạy sau pipeline tưởng dữ liệu đó đã được xử lý.

Kết quả:

```text
→ có thể mất dữ liệu
```

Đúng hơn:

```text
Extract
 ↓
Transform
 ↓
Load
 ↓
SUCCESS
 ↓
Update Watermark
```

---

# 8. Load Delta vào Warehouse bằng UPSERT / MERGE

Giả sử staging nhận:

```text
order_id   amount   updated_at
2          250      20:10
4          500      20:15
```

Warehouse đang có:

```text
1   100
2   200
3   300
```

Không thể chỉ:

```sql
INSERT INTO warehouse_orders
SELECT *
FROM staging_orders;
```

vì `order_id = 2` đã tồn tại.

Ta cần:

```text
Nếu key đã tồn tại
    → UPDATE

Nếu key chưa tồn tại
    → INSERT
```

Đó gọi là:

**UPSERT**

Một pattern phổ biến là `MERGE`:

```sql
MERGE INTO warehouse_orders AS target
USING staging_orders AS source
ON target.order_id = source.order_id

WHEN MATCHED THEN
    UPDATE SET
        amount = source.amount,
        updated_at = source.updated_at

WHEN NOT MATCHED THEN
    INSERT (order_id, amount, updated_at)
    VALUES (
        source.order_id,
        source.amount,
        source.updated_at
    );
```

Flow:

```text
Incremental Extract
        ↓
   Delta Dataset
        ↓
      MERGE
        ↓
     Warehouse
```

---

# 9. Vấn đề của Timestamp: DELETE

Đây là nhược điểm quan trọng nhất của:

```sql
WHERE updated_at > last_watermark
```

Giả sử source:

```text
id
1
2
3
```

Warehouse:

```text
id
1
2
3
```

Sau đó application chạy:

```sql
DELETE FROM orders
WHERE id = 3;
```

Source còn:

```text
1
2
```

Nhưng query:

```sql
SELECT *
FROM orders
WHERE updated_at > :last_watermark;
```

sẽ không bao giờ nhìn thấy `id = 3`.

Vì row đó đã biến mất khỏi source.

---

# 10. Soft Delete

Một cách giải quyết là không hard delete.

Thay vì:

```sql
DELETE FROM orders
WHERE id = 3;
```

ta dùng:

```text
is_deleted = true
```

hoặc:

```text
deleted_at = timestamp
```

Ví dụ:

```text
id   amount   updated_at   deleted_at
3    300      ...          2026-08-18 20:30
```

Khi đó DELETE về mặt business trở thành một UPDATE:

```text
DELETE

→

UPDATE is_deleted = true
```

Incremental pipeline có thể phát hiện được.

Nếu business bắt buộc hard delete thật, CDC / Transaction Log thường phù hợp hơn.

---

# 11. Cách 3 — Database Trigger

Có thể tạo trigger để ghi các thay đổi vào một bảng log.

```text
orders
customers
payments
     │
     │ INSERT / UPDATE / DELETE
     ▼
Database Trigger
     │
     ▼
change_log
```

Ví dụ:

```text
change_log

table      key   operation
orders     100   INSERT
orders     25    UPDATE
customer   10    DELETE
```

Pipeline sau đó chỉ cần đọc bảng log:

```sql
SELECT *
FROM change_log
WHERE ...
```

### Ưu điểm

- Bắt được INSERT.
- Bắt được UPDATE.
- Bắt được DELETE.
- Có thể tùy chỉnh cấu trúc log.

### Nhược điểm

Trigger chạy ngay trên production database:

```text
User Checkout
     ↓
INSERT orders
     ↓
Trigger
     ↓
INSERT change_log
```

Điều này tạo thêm workload cho OLTP.

Nguyên tắc quan trọng:

> **OLTP phục vụ application trước, analytics sau.**

Không nên để pipeline analytics làm chậm production system.

---

# 12. Cách 4 — Scan + Hash

Nếu source không có:

```text
updated_at ❌
CDC ❌
trigger ❌
```

ta có thể tạo hash cho mỗi row.

Ví dụ:

```text
id   name     city
1    An       HCM
2    Bình     Hà Nội
```

Hash:

```text
hash(name, city)
```

Kết quả:

```text
id   hash
1    ABC123
2    XYZ789
```

Ngày hôm sau:

```text
id   name     city
1    An       Đà Nẵng
2    Bình     Hà Nội
```

Hash mới:

```text
1    QWE456
2    XYZ789
```

So sánh:

```text
source_hash != warehouse_hash
```

suy ra:

```text
row changed
```

### Ưu điểm

Dùng được ngay cả khi source không hỗ trợ change tracking.

### Nhược điểm

Muốn biết row nào thay đổi vẫn phải:

```text
đọc toàn bộ rows
       ↓
hash toàn bộ rows
       ↓
compare toàn bộ rows
```

Do đó khó scale khi source rất lớn.

---

# 13. So sánh các cách phát hiện Delta

| Cách | Ý tưởng | Ưu điểm | Nhược điểm |
|---|---|---|---|
| WAL / CDC | Đọc transaction log | Hiệu quả, bắt INSERT/UPDATE/DELETE | Infrastructure phức tạp |
| Timestamp | Dùng `created_at`, `updated_at` | Đơn giản, dễ vận hành | Hard DELETE khó phát hiện |
| Trigger | Ghi thay đổi vào bảng log | Bắt đầy đủ changes | Tác động OLTP |
| Scan + Hash | So sánh hash source/target | Linh hoạt | Vẫn phải full scan |

---

# 14. Vì sao Timestamp Approach khá đẹp?

Full Load:

```sql
SELECT *
FROM orders;
```

Delta Load:

```sql
SELECT *
FROM orders
WHERE updated_at > :watermark;
```

Pipeline gần như giống nhau:

```text
                 ┌──── Full Load
                 │
Source → Extract ├──── Delta Load
        same code│       + WHERE filter
                 ▼
              Transform
                 ↓
                Load
```

Không cần duy trì quá nhiều code path riêng biệt.

Điều đó giúp hệ thống dễ:

```text
test
debug
backfill
recover
operate
```

---

# 15. Không nên chỉ dùng `updated_at > last_timestamp` một cách ngây thơ

Ví dụ:

```text
last watermark = 10:00:00
```

Query:

```sql
WHERE updated_at > '10:00:00'
```

Trong thực tế có thể xuất hiện:

```text
late transaction
clock precision
same timestamps
concurrent writes
job retry
network failure
```

Một pattern an toàn hơn là dùng **overlap window**.

Ví dụ watermark:

```text
10:00
```

Nhưng lần sau query từ:

```text
09:55
```

```text
09:55 ─────────────── 11:00
      ↑
   overlap
```

Ta chấp nhận đọc dư một ít để giảm nguy cơ bỏ sót dữ liệu.

Sau đó downstream:

```text
MERGE
+
deduplicate
```

---

# 16. Idempotency

Một Delta Pipeline tốt nên có tính:

**Idempotent**

Có nghĩa:

```text
Run data A

→ Result X
```

Nếu retry:

```text
Run A
Run A
Run A
```

kết quả vẫn phải là:

```text
Result X
```

chứ không phải:

```text
X
X
X
```

Ví dụ sai:

```sql
INSERT INTO fact_sales
SELECT *
FROM staging_sales;
```

Retry 3 lần có thể tạo:

```text
sale 100
sale 100
sale 100
```

Trong khi sử dụng:

```sql
MERGE ... ON sale_id
```

có thể giúp pipeline an toàn hơn khi retry.

Nguyên tắc Data Engineering:

> **Thiết kế pipeline với giả định rằng job chắc chắn sẽ có lúc fail và phải chạy lại.**

---

# 17. Delta Load và Fact / Dimension

Delta được extract từ OLTP:

```text
customers changed
orders changed
products changed
```

Warehouse:

```text
customers
    ↓
dim_customer

products
    ↓
dim_product

orders
    ↓
fact_sales
```

Flow:

```text
OLTP
 │
 │ Incremental Extraction
 ▼
Staging
 │
 ├── customer_delta
 ├── product_delta
 └── order_delta
       │
       ▼
Transformation
       │
       ├── SCD processing
       ├── Lookup surrogate keys
       └── Business rules
               │
               ▼
      Data Warehouse
```

---

# 18. Delta Load và SCD

Hai khái niệm này không giống nhau.

## Delta Load

Trả lời:

> **Dữ liệu nào đã thay đổi?**

Ví dụ:

```text
customer 100
city: HCM → Hà Nội
```

## SCD

Trả lời:

> **Khi Dimension thay đổi, Warehouse nên lưu thay đổi đó như thế nào?**

Ví dụ với SCD Type 2:

```text
Old row
customer_sk = 10
city = HCM
valid_to = 2026-08-18
```

Tạo row mới:

```text
New row
customer_sk = 25
city = Hà Nội
valid_from = 2026-08-18
```

Tóm lại:

```text
Delta Load
    ↓
What changed?

SCD
    ↓
How should the change be stored?
```

---

# 19. Delta Load khác CDC

Hai khái niệm này thường bị nhầm.

## Delta / Incremental Load

Là một **strategy / pattern**:

```text
Chỉ process những gì đã thay đổi.
```

## CDC — Change Data Capture

Là một **kỹ thuật để tìm ra những thay đổi đó**.

Có thể hình dung:

```text
          Delta Load
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
Timestamp    CDC      Trigger
```

Do đó câu:

> Tôi sử dụng CDC để thực hiện Delta Load.

là đúng.

Nhưng:

```text
Delta Load = CDC
```

là chưa chính xác.

---

# 20. Pipeline Delta Load hoàn chỉnh

```text
┌──────────────────────┐
│        OLTP          │
│ orders               │
│ customers            │
│ products             │
└──────────┬───────────┘
           │
           │
     Detect Changes
           │
 ┌─────────┼────────────┐
 │         │            │
timestamp CDC        trigger
 │         │            │
 └─────────┼────────────┘
           ▼
┌──────────────────────┐
│      Delta Data      │
│                      │
│ INSERT               │
│ UPDATE               │
│ DELETE               │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│       Staging        │
└──────────┬───────────┘
           ▼
     Transformation
           │
    ┌──────┴──────┐
    ▼             ▼
Dimension        Fact
SCD              MERGE
    │             │
    └──────┬──────┘
           ▼
┌──────────────────────┐
│    Data Warehouse    │
└──────────┬───────────┘
           ▼
       BI / OLAP
```

---

# 21. Cách Data Engineer nên nhìn Delta Load

Delta Load không chỉ đơn giản là:

```sql
WHERE updated_at > yesterday
```

Mà là cả một bài toán về **state management**.

### 1. Warehouse hiện đã xử lý đến đâu?

```text
→ Watermark
```

### 2. Source đã thay đổi những gì?

```text
→ Timestamp
→ CDC
→ Trigger
→ Hash
```

### 3. Nếu job fail thì sao?

```text
→ Checkpoint
→ Retry
```

### 4. Nếu chạy lại thì sao?

```text
→ Idempotency
```

### 5. UPDATE xử lý thế nào?

```text
→ UPSERT / MERGE
```

### 6. DELETE phát hiện thế nào?

```text
→ CDC
→ Soft Delete
→ Change Log
```

### 7. Dimension thay đổi thì sao?

```text
→ Slowly Changing Dimension
```

### 8. Dữ liệu đến muộn thì sao?

```text
→ Overlap Window
→ Event Timestamp
→ Deduplication
```

---

# 22. Chuỗi logic tổng thể

```text
OLTP
 │
 │ dữ liệu ngày càng lớn
 ▼
Full Load
 │
 │ quá tốn tài nguyên
 ▼
Delta / Incremental Load
 │
 │ cần biết "What changed?"
 ▼
┌──────────────────────────┐
│ Timestamp / CDC / Trigger│
└────────────┬─────────────┘
             ▼
          Staging
             │
             ▼
         ETL / ELT
             │
             ▼
       Data Modeling
             │
       ┌─────┴─────┐
       ▼           ▼
      Fact      Dimension
                   │
                   ▼
                  SCD
             │
             ▼
       Data Warehouse
             │
             ▼
          BI / OLAP
```

---

# 23. Câu cần nhớ

> **Full Load hỏi:**  
> “Tôi có những dữ liệu gì?”

> **Delta Load hỏi:**  
> “Kể từ lần cuối tôi xử lý, cái gì đã thay đổi?”

Khi dữ liệu ngày càng lớn, Delta Load giúp Data Pipeline:

- chạy nhanh hơn;
- giảm tải OLTP;
- giảm network I/O;
- giảm compute cost;
- scale tốt hơn;
- hỗ trợ near-real-time;
- và tránh phải xử lý lại hàng triệu hoặc hàng tỷ row không thay đổi.

---

# 24. Từ khóa cần nhớ

```text
Delta Load
Incremental Load
Full Load
CDC
Change Data Capture
WAL
Transaction Log
created_at
updated_at
Watermark
Checkpoint
UPSERT
MERGE
Soft Delete
Hard Delete
Database Trigger
Hash Comparison
Idempotency
Overlap Window
Late-arriving Data
SCD
Fact
Dimension
Staging
```

---

## Tài liệu tham khảo

- Simon Späti — **Delta Load a Data Warehouse**  
  https://www.ssp.sh/brain/delta-load-a-data-warehouse/

- Databricks Documentation — **MERGE INTO for Delta Lake**  
  https://docs.databricks.com/aws/en/delta/merge
