# Backfill trong Data Engineering

## 1. Backfill là gì?

**Backfill** là quá trình **xử lý hoặc tính toán lại dữ liệu lịch sử** cho một data asset/pipeline vốn bình thường chỉ được cập nhật theo kiểu incremental.

Mental model:

```text
Normal incremental processing

15 → 16 → 17 → 18 → 19 →
                   ↑
                  now

Backfill

15 → [16] → [17] → 18 → 19
      ↑      ↑
   chạy lại dữ liệu lịch sử
```

Nói ngắn gọn:

```text
Incremental Load
= xử lý delta mới

Backfill
= xử lý lại delta cũ

Full Refresh
= tính toán lại toàn bộ dữ liệu
```

---

# 2. Vì sao Backfill tồn tại?

Giả sử pipeline chạy mỗi ngày:

```text
Source
  ↓
Raw
  ↓
Transform
  ↓
fact_orders
```

Ngày 18/08:

```text
fact_orders

2026-08-15 ✅
2026-08-16 ✅
2026-08-17 ✅
2026-08-18 ✅
```

Pipeline bình thường chỉ xử lý dữ liệu mới:

```sql
WHERE order_date = '2026-08-18'
```

hoặc:

```sql
WHERE updated_at > last_processed_timestamp
```

Đây là **incremental processing**.

Nhưng nếu phát hiện:

```text
2026-08-15 ✅
2026-08-16 ❌ dữ liệu sai
2026-08-17 ❌ dữ liệu thiếu
2026-08-18 ✅
```

Ngày mai pipeline chỉ xử lý:

```text
2026-08-19
```

thì dữ liệu ngày 16 và 17 sẽ không tự động được sửa.

Lúc này cần:

```text
BACKFILL
2026-08-16 → 2026-08-17
```

---

# 3. Backfill không phải một pipeline hoàn toàn khác

Về bản chất, pipeline tốt nên được thiết kế như:

```python
process(partition)
```

Ví dụ:

```python
process_orders("2026-08-18")
```

Ngày hôm sau:

```python
process_orders("2026-08-19")
```

Khi cần backfill:

```python
process_orders("2026-08-01")
process_orders("2026-08-02")
process_orders("2026-08-03")
...
process_orders("2026-08-17")
```

Thay vì viết pipeline phụ thuộc cứng vào:

```python
process(today)
```

nên hướng đến:

```python
process(date)
```

Điều này giúp pipeline:

- chạy incremental;
- rerun khi failure;
- backfill dữ liệu lịch sử;
- test một partition cụ thể.

---

# 4. Ví dụ thực tế

Giả sử có bảng:

```text
fact_sales
```

Logic cũ:

```python
revenue = price
```

Nhưng business đúng phải là:

```python
revenue = price * quantity
```

Pipeline trước đây đã chạy:

```text
01/08
02/08
03/08
...
17/08
```

Tất cả revenue đều sai.

Bạn sửa code vào ngày 18/08.

Từ ngày 18/08 trở đi:

```text
18/08 → đúng
19/08 → đúng
...
```

Nhưng dữ liệu lịch sử vẫn sai:

```text
01/08 → sai
02/08 → sai
...
17/08 → sai
18/08 → đúng
```

Nếu business muốn toàn bộ lịch sử sử dụng logic đúng, cần:

```text
BACKFILL
2026-08-01 → 2026-08-17
```

Sau đó:

```text
01/08 ✅
02/08 ✅
...
17/08 ✅
18/08 ✅
```

---

# 5. Những trường hợp thường phải Backfill

## 5.1. Tạo pipeline/data asset mới

Bạn có raw data:

```text
2024 → 2026
```

Nhưng hôm nay mới tạo:

```text
fact_customer_activity
```

Nếu chỉ chạy incremental từ hôm nay thì table chỉ có dữ liệu từ hiện tại trở đi.

Nếu business cần lịch sử 2 năm:

```text
BACKFILL
2024 → 2026
```

Đây thường được gọi là **Greenfield Backfill**.

---

## 5.2. Logic transformation thay đổi

Ví dụ logic cũ:

```sql
revenue = price
```

Logic mới:

```sql
revenue = price * quantity
```

Nếu business yêu cầu dữ liệu lịch sử cũng tuân theo logic mới:

```text
→ Backfill
```

---

## 5.3. Pipeline bị lỗi

Ví dụ:

```text
01 Aug ✅
02 Aug ✅
03 Aug ❌
04 Aug ❌
05 Aug ❌
06 Aug ✅
```

Sau khi sửa lỗi:

```text
Backfill
03 Aug → 05 Aug
```

Thay vì reload toàn bộ warehouse.

---

# 6. Incremental Load và Backfill

Hai khái niệm này liên quan cực kỳ chặt.

```text
Incremental Load
=
xử lý delta mới

Backfill
=
xử lý lại delta cũ
```

Ví dụ incremental:

```sql
WHERE event_date = CURRENT_DATE
```

Backfill:

```sql
WHERE event_date >= '2026-07-01'
  AND event_date < '2026-08-01'
```

Có thể hình dung:

```text
Past ------------------------------------ Present

     historical partitions                  new partition
           ↓                                     ↓
        Backfill                            Incremental
```

---

# 7. Backfill khác Full Refresh

Giả sử table chứa dữ liệu:

```text
2020
2021
2022
2023
2024
2025
2026
```

Bug chỉ ảnh hưởng:

```text
July 2026
```

## Full Refresh

```text
DELETE / rebuild EVERYTHING

2020 ┐
2021 │
2022 │
2023 ├─ chạy lại
2024 │
2025 │
2026 ┘
```

## Backfill

```text
2020 ✅
2021 ✅
2022 ✅
2023 ✅
2024 ✅
2025 ✅

2026-07 ❌
        ↓
     rebuild

2026-08 ✅
```

Tóm lại:

```text
Full Refresh
= rebuild toàn bộ dataset

Backfill
= rebuild một historical subset
```

Với dữ liệu lớn, khác biệt về compute, thời gian và chi phí có thể rất lớn.

---

# 8. Partitioning rất quan trọng với Backfill

Giả sử table partition theo:

```text
event_date
```

Storage:

```text
fact_sales/
├── date=2026-08-15/
├── date=2026-08-16/
├── date=2026-08-17/
└── date=2026-08-18/
```

Muốn backfill ngày 16/08:

```text
date=2026-08-16
```

thì chỉ cần xử lý partition đó.

Không cần scan toàn bộ table.

Vì vậy:

```text
Partitioning
     ↓
xác định subset
     ↓
Backfill hiệu quả
```

---

# 9. Vấn đề lớn nhất: Duplicate

Giả sử dữ liệu ngày 16/08:

```text
order_id
--------
101
102
103
```

Nếu backfill bằng:

```sql
INSERT INTO fact_orders
SELECT ...
```

thì có thể thành:

```text
101
102
103
101
102
103
```

Bạn đã tạo duplicate.

Do đó backfill phải gắn với khái niệm:

# Idempotency

---

# 10. Idempotency

Một pipeline **idempotent** nghĩa là chạy lại nhiều lần vẫn tạo ra cùng trạng thái cuối.

```text
run once
```

và:

```text
run 10 times
```

nên cho cùng kết quả.

Về mặt ý tưởng:

```text
f(f(x)) = f(x)
```

Ví dụ không an toàn:

```sql
INSERT INTO fact_sales
SELECT ...
```

Chạy lại nhiều lần có thể tạo duplicate.

Một cách đơn giản hơn:

```sql
DELETE FROM fact_sales
WHERE sale_date = '2026-08-16';

INSERT INTO fact_sales
SELECT ...
WHERE sale_date = '2026-08-16';
```

Hoặc sử dụng:

```sql
MERGE INTO fact_sales target
USING source_sales source
ON target.order_id = source.order_id

WHEN MATCHED THEN
    UPDATE ...

WHEN NOT MATCHED THEN
    INSERT ...
```

Mental model:

```text
Retry
Backfill
Reprocessing
Recovery
      ↓
đều dễ hơn
      ↓
nếu pipeline IDEMPOTENT
```

---

# 11. Backfill nên chạy theo Partition

Giả sử phải backfill:

```text
2 năm dữ liệu
```

Không nhất thiết chạy một job khổng lồ.

Có thể chia:

```text
2025-01-01
2025-01-02
2025-01-03
...
2026-08-18
```

Hoặc chia theo tháng:

```text
Jan 2025
Feb 2025
Mar 2025
...
```

Backfill granularity có thể là:

```text
hour
day
week
month
```

Tùy:

- volume dữ liệu;
- SLA;
- chi phí;
- partition strategy;
- khả năng retry.

---

# 12. Không nên chạy tất cả Partition cùng lúc

Giả sử có:

```text
365 partitions
```

Không phải cứ:

```text
365 workers
```

là tốt.

Production còn nhiều workload khác:

```text
daily pipeline
dashboard
streaming
BI queries
other ETLs
```

Backfill có thể chiếm:

```text
CPU
Memory
I/O
Warehouse slots
Spark executors
Database connections
```

Nếu không giới hạn concurrency:

```text
Backfill
████████████████████

Production pipeline
██ ❌
```

Vì vậy thường cần:

```text
max_concurrency = 5
```

hoặc một giới hạn tương tự.

Các vấn đề cần quan tâm:

```text
Backfill
   ↓
Compute cost
Memory
I/O
Concurrency
Production SLA
Cloud cost
```

---

# 13. Dependency rất quan trọng

Giả sử pipeline:

```text
raw_orders
    ↓
stg_orders
    ↓
fact_orders
    ↓
daily_sales
    ↓
dashboard
```

Nếu chỉ backfill:

```text
raw_orders
```

nhưng không xử lý lại downstream:

```text
stg_orders
fact_orders
daily_sales
```

thì có thể xảy ra:

```text
Raw         ✅ new
Staging     ❌ old
Fact        ❌ old
Aggregate   ❌ old
```

Data warehouse trở nên inconsistent.

Do đó phải nghĩ theo **dependency graph**:

```text
orders
  ↓
fact_sales
  ↓
daily_sales
  ↓
dashboard
```

Nếu:

```text
orders[July]
```

thay đổi, có thể cần:

```text
orders[July]
     ↓
fact_sales[July]
     ↓
daily_sales[July]
     ↓
dashboard
```

Đây cũng là lý do orchestration tools như Airflow, Dagster... sử dụng DAG/dependency graph.

---

# 14. Schema Evolution

Historical data có thể sử dụng schema khác schema hiện tại.

Ví dụ dữ liệu năm 2024:

```json
{
  "id": 1,
  "price": 100
}
```

Schema năm 2026:

```json
{
  "id": 1,
  "unit_price": 100,
  "quantity": 2,
  "currency": "USD"
}
```

Nếu chạy code hiện tại trực tiếp lên dữ liệu 2024:

```text
có thể crash
```

hoặc nguy hiểm hơn:

```text
chạy thành công nhưng tính sai
```

Một pattern thường gặp:

```text
Historical schema
       ↓
Normalization
       ↓
Canonical schema
       ↓
Transformation
```

Do đó trước khi backfill cần kiểm tra:

- schema historical source;
- column rename;
- data type;
- nullability;
- field bị thiếu;
- business logic cũ/mới.

---

# 15. Chạy Code cũ hay Code mới?

Đây là một vấn đề business rất quan trọng.

Ví dụ năm 2024:

```text
VIP = spending > $5,000
```

Năm 2026 business đổi:

```text
VIP = spending > $10,000
```

Nếu backfill dữ liệu 2024 bằng logic hiện tại:

```text
VIP threshold = $10,000
```

thì bạn đang **rewrite history**.

Có hai câu hỏi business hoàn toàn khác nhau:

```text
Nếu áp dụng định nghĩa hiện tại,
khách hàng năm 2024 có phải VIP không?
```

và:

```text
Tại thời điểm năm 2024,
khách hàng có được business xem là VIP không?
```

Hai kết quả có thể khác nhau.

Vì vậy backfill không đơn giản chỉ là:

```text
rerun old data
```

mà còn phải xác định:

```text
historical semantics
```

---

# 16. Backfill với SCD

Giả sử customer:

```text
Customer A

2025:
country = Vietnam

2026:
country = Japan
```

Fact sales năm 2025 đáng lẽ phải join với:

```text
Vietnam
```

Nếu backfill bằng current dimension:

```text
Japan
```

thì historical fact có thể bị đổi nghĩa.

Do đó backfill liên quan chặt với:

```text
Backfill
SCD
Event Time
Temporal Data
Snapshot
```

Đặc biệt nếu sử dụng **SCD Type 2**, phải join dimension theo khoảng thời gian hiệu lực thích hợp.

---

# 17. Event Time và Processing Time

Giả sử event xảy ra:

```text
event_time = 15 Aug
```

nhưng pipeline nhận được:

```text
processing_time = 18 Aug
```

Event đến trễ 3 ngày.

Khi backfill, thường cần quan tâm:

```text
event_time
```

tức:

```text
dữ liệu thuộc business partition nào?
```

không chỉ:

```text
pipeline chạy ngày nào?
```

Điều này đặc biệt quan trọng với:

- streaming;
- late-arriving data;
- window aggregation;
- event-time partitioning.

---

# 18. Backfill trong Streaming

Streaming và backfill không mâu thuẫn.

Ví dụ:

```text
Kafka
 ↓
Streaming pipeline
 ↓
Bronze
 ↓
Silver
 ↓
Gold
```

Streaming bắt đầu hôm nay:

```text
18 Aug →
```

Nhưng object storage vẫn có dữ liệu lịch sử:

```text
2024 → 17 Aug
```

Có thể chạy:

```text
Historical Files
       ↓
    Backfill
       ↓
     Bronze
       ↑
      Stream
       ↑
      Kafka
```

Tức là:

```text
Historical path
        +
Real-time path
        ↓
same downstream pipeline
```

Điều quan trọng là tránh:

- duplicate;
- ordering problem;
- inconsistent state;
- conflicting writes.

---

# 19. Backfill phải có khả năng Resume

Giả sử cần xử lý:

```text
365 partitions
```

Đã chạy:

```text
1   ✅
2   ✅
...
199 ✅
200 ❌
```

Một hệ thống tốt không nên bắt:

```text
restart 1 → 365
```

Mà nên:

```text
resume:
200 → 365
```

Có thể lưu metadata:

```text
partition       status
-----------------------
2026-01-01      SUCCESS
2026-01-02      SUCCESS
2026-01-03      FAILED
2026-01-04      PENDING
```

Nhờ vậy có thể:

- retry partition thất bại;
- resume;
- audit;
- monitor progress;
- tránh xử lý lại không cần thiết.

---

# 20. Backfill với Airflow

Giả sử DAG chạy hàng ngày:

```text
sales_pipeline
schedule = daily
```

Normal scheduler tạo:

```text
Aug 16
Aug 17
Aug 18
```

Backfill có thể tạo historical DAG runs:

```text
Aug 01
Aug 02
Aug 03
...
Aug 15
```

Mental model:

```text
                Airflow DAG
                     ↓

            logical date / interval

      ┌──────┬──────┬──────┬──────┐
      │ Aug1 │ Aug2 │ Aug3 │ Aug4 │
      └──────┴──────┴──────┴──────┘
          ↓      ↓      ↓      ↓

             Backfill runs
```

Một lợi ích lớn của orchestration là quản lý:

- dependency;
- retry;
- concurrency;
- failed runs;
- historical intervals.

---

# 21. Những điều phải kiểm tra trước khi Backfill

Có thể dùng mental checklist sau:

```text
                 BACKFILL
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
     Scope         Safety       Resources
       │             │             │
partition?      idempotent?     CPU?
date range?     duplicate?      memory?
dependency?     overwrite?      cost?
       │             │             │
       └─────────────┼─────────────┘
                     ↓
               Observability
                     │
             success partition
             failed partition
             retry / resume
                     │
                     ↓
                 Validate
```

Trước khi chạy nên trả lời được:

1. Backfill khoảng thời gian nào?
2. Partition nào bị ảnh hưởng?
3. Pipeline có idempotent không?
4. Có tạo duplicate không?
5. Downstream dependency nào cần chạy lại?
6. Historical schema có tương thích không?
7. Logic cũ hay logic mới phải được sử dụng?
8. Có ảnh hưởng SCD/historical dimension không?
9. Batch size bao nhiêu?
10. Concurrency bao nhiêu?
11. Chi phí compute dự kiến có quá lớn không?
12. Có thể resume nếu job fail giữa chừng không?
13. Sau backfill validate bằng cách nào?

---

# 22. Các Failure Mode phổ biến

## 22.1. Target sai historical range

Ví dụ cần:

```text
July
```

nhưng vô tình xử lý:

```text
January → August
```

Có thể gây:

- overwrite dữ liệu đúng;
- chi phí lớn;
- thời gian chạy dài.

---

## 22.2. Resource Overload

Backfill lớn có thể làm:

```text
production workload
```

bị thiếu:

- CPU;
- memory;
- I/O;
- executors;
- DB connections.

---

## 22.3. Cost Overload

Ví dụ scan hàng chục TB historical data trên cloud warehouse có thể tạo chi phí rất lớn.

---

## 22.4. Lost in the Middle

Job chạy:

```text
1 → 199 ✅
200 ❌
```

nhưng không biết partition nào đã hoàn tất.

Kết quả:

```text
phải chạy lại từ đầu
```

hoặc điều tra thủ công.

Do đó cần tracking metadata và resumability.

---

# 23. Backfill liên quan với các Concept Data Engineering nào?

```text
Delta / Incremental Load
        │
        ↓
chỉ xử lý phần dữ liệu thay đổi
        │
        ↓
Backfill
        │
        ↓
xử lý lại delta lịch sử
```

Backfill kết nối với:

```text
Backfill
   │
   ├── Partitioning
   │      └── xác định vùng dữ liệu
   │
   ├── Granularity
   │      └── hour/day/week/month
   │
   ├── Idempotency
   │      └── chạy lại không duplicate
   │
   ├── Orchestration
   │      └── dependency + retry
   │
   ├── Event Time
   │      └── historical interval
   │
   ├── Schema Evolution
   │      └── data cũ vs schema mới
   │
   └── SCD
          └── historical semantics
```

---

# 24. Mental Model quan trọng nhất

Đừng chỉ nhớ:

```text
Backfill
= chạy lại dữ liệu cũ
```

Hãy hiểu:

> **Backfill là khả năng tái tính toán một phần trạng thái lịch sử của data system một cách có kiểm soát.**

Một pipeline tốt nên hướng tới:

```text
Historical partition
        +
deterministic logic
        ↓
same correct result
```

dù chạy:

```text
hôm nay
```

hay:

```text
6 tháng sau
```

---

# 25. So sánh Incremental Load, Backfill và Full Refresh

| Concept | Xử lý gì? | Ví dụ |
|---|---|---|
| Incremental Load | Dữ liệu mới / thay đổi mới | Load dữ liệu ngày hôm nay |
| Backfill | Một subset dữ liệu lịch sử | Chạy lại July 2026 |
| Full Refresh | Toàn bộ dataset | Rebuild 2020–2026 |

Mental model:

```text
Incremental Load
= process NEW delta

Backfill
= reprocess OLD delta

Full Refresh
= recompute EVERYTHING
```

---

# 26. Câu trả lời phỏng vấn Data Engineer

Nếu interviewer hỏi:

> What is a backfill?

Có thể trả lời:

```text
Backfill is the process of reprocessing historical
data for a pipeline that normally processes data
incrementally.

Typical reasons include:
- creating a new data asset,
- fixing historical data after logic changes,
- recovering missing or failed historical runs.

Major concerns are:
- choosing the correct historical scope,
- idempotency,
- avoiding duplicates,
- downstream dependencies,
- schema compatibility,
- resource and cost control,
- and resumability.
```

---

# 27. Tóm tắt

```text
              Normal Pipeline

Past ----------------------→ Present
                              ↓
                         Incremental


                 Something wrong
                       ↓

Past ------ [affected range] → Present
                  ↓
               BACKFILL
                  ↓
          recompute historical
              partitions
```

Điều quan trọng nhất:

```text
Backfill không chỉ là rerun job.

Backfill tốt cần:
- đúng historical scope;
- idempotent;
- partition-aware;
- dependency-aware;
- schema-aware;
- resource-controlled;
- observable;
- resumable;
- validated.
```

---

# Nguồn tham khảo

- Simon Späti — Backfill: https://www.ssp.sh/brain/backfill
- Apache Airflow — Backfill: https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/backfill.html
- dbt — Incremental Models: https://docs.getdbt.com/docs/build/incremental-models
- dbt — Idempotence: https://docs.getdbt.com/best-practices/idempotence
- Databricks — Backfill historical data: https://docs.databricks.com/
