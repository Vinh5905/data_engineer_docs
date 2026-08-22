# Materialized Views — Giải thích theo góc nhìn Data Engineer

## 1. Vấn đề ban đầu

Giả sử Data Warehouse có một bảng fact rất lớn:

```text
fact_sales
────────────────────────────────
date
customer_id
product_id
store_id
quantity
amount
```

Ví dụ bảng có:

```text
5 tỷ rows
```

Business thường xuyên hỏi:

> Doanh thu từng ngày của từng cửa hàng là bao nhiêu?

Query:

```sql
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM fact_sales
GROUP BY
    date,
    store_id;
```

Luồng xử lý:

```text
5,000,000,000 rows
        ↓
     GROUP BY
        ↓
       SUM
        ↓
   20,000 rows
```

Nếu dashboard được mở 100 lần:

```text
Query #1   → scan 5B rows
Query #2   → scan 5B rows
Query #3   → scan 5B rows
...
Query #100 → scan 5B rows
```

Vấn đề:

> Ta đang tính đi tính lại cùng một kết quả rất đắt.

---

# 2. View sinh ra để làm gì?

Thay vì mọi dashboard đều phải viết lại một câu SQL dài:

```sql
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM fact_sales
GROUP BY date, store_id;
```

Ta có thể tạo một View:

```sql
CREATE VIEW daily_store_sales AS
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM fact_sales
GROUP BY date, store_id;
```

Sau đó chỉ cần:

```sql
SELECT *
FROM daily_store_sales;
```

## Bản chất của View

View có thể hiểu đơn giản là:

```text
VIEW
=
saved SQL query
```

Không phải:

```text
VIEW
=
saved query result
```

Nó chủ yếu giúp:

- abstraction
- tái sử dụng logic SQL
- đơn giản hóa query
- security / giới hạn cột hoặc rows người dùng được nhìn thấy
- tạo một logical layer cho người dùng downstream

Nhưng View thông thường **không giải quyết trực tiếp việc phải tính lại query nặng**.

---

# 3. Điều gì xảy ra khi query một View?

Bạn viết:

```sql
SELECT *
FROM daily_store_sales;
```

Về logic, database vẫn phải thực hiện:

```sql
SELECT
    date,
    store_id,
    SUM(amount)
FROM fact_sales
GROUP BY date, store_id;
```

Luồng:

```text
User
 │
 │ SELECT * FROM view
 ▼
VIEW
 │
 │ query definition
 ▼
fact_sales
5 billion rows
 │
 │ scan
 │ group
 │ aggregate
 ▼
Result
```

Do đó:

> View giúp tái sử dụng logic, nhưng không nhất thiết giúp giảm chi phí compute.

---

# 4. Vì sao Materialized View ra đời?

Giả sử:

```text
fact_sales = 5B rows
```

Nhưng kết quả aggregate cuối cùng chỉ có:

```text
20K rows
```

Nếu business hỏi cùng một câu rất nhiều lần thì không hợp lý khi cứ:

```text
5B rows
   ↓
GROUP BY
   ↓
20K rows
```

mỗi lần query.

Ý tưởng:

> Tính kết quả trước một lần, lưu lại, rồi các query sau đọc trực tiếp kết quả đã tính.

Đây chính là Materialized View.

---

# 5. Materialized View là gì?

Ví dụ:

```sql
CREATE MATERIALIZED VIEW daily_store_sales AS
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM fact_sales
GROUP BY date, store_id;
```

Khi materialize:

```text
fact_sales
5B rows
    │
    │ compute
    ▼
GROUP BY + SUM
    │
    ▼
daily_store_sales
20K rows
```

Khác với View, kết quả được persist.

Có thể nhớ:

```text
VIEW
=
saved query
```

```text
MATERIALIZED VIEW
=
saved query
+
saved result
```

---

# 6. Query Materialized View nhanh hơn ở đâu?

## Normal View

```text
User
 ↓
View
 ↓
fact_sales 5B
 ↓
GROUP BY
 ↓
20K rows
```

## Materialized View

```text
User
 ↓
Materialized View
 ↓
20K persisted rows
 ↓
Result
```

Điểm chính:

> Thay vì scan và aggregate hàng tỷ rows mỗi lần, query có thể đọc kết quả đã precompute.

Đây là một dạng:

```text
PRECOMPUTATION
```

---

# 7. View vs Materialized View

| Đặc điểm | View | Materialized View |
|---|---|---|
| Lưu SQL definition | Có | Có |
| Lưu query result | Không | Có |
| Chiếm storage cho result | Gần như không | Có |
| Query source khi sử dụng | Thường có | Giảm đáng kể |
| Dữ liệu phản ánh source hiện tại | Có | Có thể bị stale |
| Query phức tạp | Có thể chậm | Thường nhanh hơn |
| Cần refresh | Không | Có |
| Mục tiêu chính | Abstraction / reuse | Performance / precomputation |

Câu nhớ nhanh:

```text
View = lưu cách tính

Materialized View = lưu cách tính + kết quả đã tính
```

---

# 8. Vấn đề mới: Staleness

Giả sử lúc 10:00:

```text
fact_sales revenue = 100M
```

Materialized View được tạo:

```text
MV revenue = 100M
```

Đến 10:05 có thêm giao dịch:

```text
fact_sales revenue = 110M
```

Nhưng Materialized View có thể vẫn là:

```text
MV revenue = 100M
```

Đây gọi là:

```text
staleness
```

Tức là:

> Dữ liệu trong MV không còn mới bằng source.

---

# 9. Refresh Materialized View

Để cập nhật kết quả:

```text
Source changed
      ↓
   Refresh
      ↓
Materialized View updated
```

Ví dụ PostgreSQL:

```sql
REFRESH MATERIALIZED VIEW daily_store_sales;
```

Luồng:

```text
                ┌───────────────┐
                │  fact_sales   │
                │    5B rows    │
                └───────┬───────┘
                        │
                        │ refresh
                        ▼
               GROUP BY / SUM
                        │
                        ▼
            ┌───────────────────────┐
            │ Materialized View     │
            │      20K rows         │
            └───────────────────────┘
                        ▲
                        │
                  Dashboard / BI
```

---

# 10. Refresh bao lâu một lần?

Đây không chỉ là technical decision mà còn là business decision.

Ví dụ:

## Dashboard tài chính

```text
Freshness requirement <= 24h
```

Có thể:

```text
refresh mỗi ngày
```

## Dashboard vận hành

```text
Freshness requirement <= 15 phút
```

Có thể:

```text
refresh mỗi 5–15 phút
```

## Hệ thống gần real-time

```text
Freshness requirement < vài giây
```

Materialized View kiểu batch refresh truyền thống có thể không phù hợp.

Câu hỏi đúng là:

> Business chấp nhận dữ liệu cũ bao lâu?

---

# 11. Full Refresh

Cách đơn giản nhất:

```text
Materialized View hiện tại
       ↓
      rebuild
       ↓
run query lại toàn bộ
       ↓
Materialized View mới
```

Nếu source có:

```text
5B rows
```

thì full refresh có thể rất đắt.

---

# 12. Incremental Refresh

Giả sử MV đã tính toàn bộ dữ liệu lịch sử.

Hôm qua:

```text
fact_sales = 5B rows
```

Hôm nay chỉ thêm:

```text
1M rows
```

Nếu full refresh:

```text
scan 5,001,000,000 rows
```

Nếu incremental refresh, ý tưởng là:

```text
old materialized state
        +
new / changed data
        ↓
updated materialized state
```

Ví dụ:

```text
Yesterday:

HCM revenue = 100M
```

Hôm nay có thêm:

```text
HCM +5M
```

Thay vì tính lại toàn bộ:

```text
HCM revenue = 105M
```

Incremental refresh rất gần với concept:

```text
Delta Load
```

Cùng tư tưởng:

> Chỉ xử lý phần dữ liệu thay đổi thay vì xử lý lại toàn bộ.

---

# 13. Trade-off của Materialized View

Materialized View đánh đổi:

```text
storage
+
refresh cost
+
maintenance
+
potential stale data
```

để lấy:

```text
faster query
+
less repeated compute
```

Có thể hiểu:

```text
Normal View

Storage thấp
Compute mỗi query cao
```

```text
Materialized View

Storage cao hơn
Compute mỗi query thấp hơn
```

---

# 14. Ví dụ Data Warehouse

Giả sử:

```text
FactInternetSales
500M rows
```

Dashboard liên tục hỏi:

```sql
SELECT
    YEAR(OrderDate),
    MONTH(OrderDate),
    ProductCategory,
    SUM(SalesAmount)
FROM FactInternetSales
JOIN DimProduct ...
JOIN DimCategory ...
GROUP BY ...;
```

Mỗi query phải:

```text
500M fact rows
    │
    ├── JOIN DimProduct
    ├── JOIN DimCategory
    │
    └── GROUP BY
          │
          ▼
        SUM()
```

Ta có thể tạo:

```text
mv_monthly_category_sales
```

với grain:

```text
year
month
category
```

và measure:

```text
revenue
```

Ví dụ:

```text
2026 | 01 | Laptop | 15B
2026 | 01 | Phone  | 20B
2026 | 02 | Laptop | 17B
```

Dashboard có thể chỉ đọc vài nghìn rows thay vì hàng trăm triệu rows.

---

# 15. Materialized View rất phù hợp với aggregation

Ví dụ:

```sql
SELECT
    customer_id,
    COUNT(*) AS order_count,
    SUM(amount) AS lifetime_value
FROM orders
GROUP BY customer_id;
```

Nếu:

```text
orders = 2B rows
customers = 10M
```

Normal query:

```text
2B rows
   ↓
GROUP BY
   ↓
10M rows
```

Nếu dùng MV:

```text
2B rows
   ↓
precompute
   ↓
MV = 10M rows
```

Các query downstream chỉ cần đọc MV.

Materialized View đặc biệt hữu ích cho:

```text
SUM()
COUNT()
AVG()
GROUP BY
JOIN
large scans
repeated analytics
```

---

# 16. Không phải query chậm nào cũng cần Materialized View

Ví dụ:

```sql
SELECT *
FROM orders
WHERE order_id = 123;
```

Nếu `order_id` đã có index phù hợp thì query vốn đã nhanh.

Tạo Materialized View cho trường hợp này thường không có ý nghĩa.

Heuristic:

```text
Expensive computation
        +
Repeated computation
        ↓
Consider Materialized View
```

Nếu query:

```text
large scan
+
JOIN
+
GROUP BY
+
aggregation
+
same pattern repeated frequently
```

thì MV đáng cân nhắc hơn.

---

# 17. Materialized View và OLAP

OLTP thường:

```text
find customer
find order
create booking
update payment
```

Thường chỉ thao tác một số lượng rows nhỏ.

OLAP thường:

```text
SUM revenue
GROUP BY country
GROUP BY month
JOIN huge fact table
```

Đặc điểm:

```text
large scan
+
aggregation
+
repeated analytics
```

Đây chính là loại workload phù hợp với precomputation.

---

# 18. Materialized View và OLAP Cube

OLAP Cube cũng có tư tưởng:

> Precompute những aggregation thường xuyên được dùng.

Ví dụ:

```text
Revenue by:
- day + store
- month + store
- month + product
- year + region
```

Materialized View cũng thực hiện một ý tưởng tương tự:

```text
expensive aggregation
       ↓
precompute
       ↓
reuse many times
```

---

# 19. Query Rewrite

Một số database/data warehouse có optimizer đủ thông minh để dùng Materialized View dù user không query trực tiếp MV.

Giả sử đã có:

```sql
CREATE MATERIALIZED VIEW mv_sales AS
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM sales
GROUP BY date, store_id;
```

Analyst vẫn viết:

```sql
SELECT
    date,
    store_id,
    SUM(amount)
FROM sales
GROUP BY date, store_id;
```

Optimizer có thể nhận ra:

```text
Kết quả này đã được precompute
```

và execution plan có thể chuyển từ:

```text
scan fact_sales
```

sang:

```text
read materialized view
```

Concept:

```text
User query
    │
    ▼
Optimizer
    │
    ├── scan base table
    │
    └── use MV
```

Đây được gọi là query rewrite / smart tuning tùy hệ thống.

---

# 20. Materialized View khác Cache thế nào?

Cả hai cùng có mục tiêu:

```text
avoid recomputation
```

Nhưng khác bản chất.

## Query Cache

Thường là:

```text
Query
  ↓
Result
  ↓
Temporary cache
```

Lần sau nếu query tương tự hoặc giống nhau:

```text
cache hit
```

Cache thường:

```text
temporary
engine-managed
query-result-oriented
```

## Materialized View

Là một database object có chủ đích:

```text
schema object
+
known query definition
+
persisted result
+
refresh lifecycle
```

Nó là một phần của data architecture chứ không chỉ là cache tạm.

---

# 21. Tại sao không dùng TABLE luôn?

Đây là câu hỏi rất quan trọng.

Ta hoàn toàn có thể tạo:

```sql
CREATE TABLE daily_store_sales (
    date DATE,
    store_id INT,
    revenue DECIMAL
);
```

Sau đó ETL:

```sql
INSERT INTO daily_store_sales
SELECT
    date,
    store_id,
    SUM(amount)
FROM fact_sales
GROUP BY date, store_id;
```

Kết quả vật lý có thể giống Materialized View.

Vậy khác ở đâu?

---

# 22. Aggregate Table

Giả sử:

```text
fact_sales
     │
     │ ETL / dbt / Airflow
     ▼
daily_store_sales TABLE
```

Database nhìn thấy:

```text
fact_sales

daily_store_sales
```

như hai table độc lập.

Database không nhất thiết biết:

```text
daily_store_sales
=
SUM(fact_sales.amount)
GROUP BY date, store
```

Logic này nằm ở:

```text
Airflow DAG
dbt model
SQL script
Spark job
Git repository
```

Bạn phải tự quản lý:

```text
refresh
incremental logic
backfill
retry
data quality
dependencies
orchestration
```

---

# 23. Materialized View khác Aggregate Table ở đâu?

Materialized View được khai báo trực tiếp bằng query:

```sql
CREATE MATERIALIZED VIEW daily_store_sales AS
SELECT
    date,
    store_id,
    SUM(amount) AS revenue
FROM fact_sales
GROUP BY date, store_id;
```

Database biết:

```text
daily_store_sales
```

là kết quả của:

```text
SELECT ...
FROM fact_sales
GROUP BY ...
```

Nói cách khác:

```text
Materialized View
=
stored data
+
stored derivation logic
```

Trong khi aggregate table thường là:

```text
TABLE
=
stored data
```

và derivation logic nằm ngoài table.

---

# 24. Analogy với Excel

## Aggregate Table

Bạn:

```text
Sales Sheet
    ↓
Pivot Table
    ↓
Copy
    ↓
Paste Values vào Sheet B
```

Sheet B chỉ chứa:

```text
HCM = 300
HN  = 150
```

Nó không biết vì sao có các con số này.

Nếu Sales thay đổi:

```text
HCM = 800
```

Sheet B vẫn giữ giá trị cũ nếu bạn không chạy pipeline lại.

---

## Materialized View

Gần giống một Pivot Table vẫn giữ:

```text
Source = Sales
Rows = Date
Columns = Store
Value = SUM(amount)
```

và có thể:

```text
Refresh
```

Hệ thống biết cần tính lại từ đâu và theo logic nào.

---

# 25. Source of Truth

Giả sử:

```text
fact_sales
```

là source of truth.

Materialized View:

```text
daily_store_sales
```

chỉ là derived data:

```text
fact_sales
     │
     │ GROUP BY + SUM
     ▼
daily_store_sales
```

Nếu MV bị mất, về nguyên tắc ta có thể rebuild từ source.

Do đó:

```text
Source of Truth
      ↓
fact_sales

Derived representation
      ↓
materialized view
```

---

# 26. Một khác biệt về semantics

## TABLE

Có thể hiểu:

```text
"I own these rows."
```

Rows chính là state của object.

Ví dụ bạn có thể:

```sql
UPDATE daily_store_sales
SET revenue = 999999
WHERE store_id = 1;
```

Table đơn giản nhận dữ liệu mới.

---

## MATERIALIZED VIEW

Có thể hiểu:

```text
"I own this query.
These rows are the materialized result of that query."
```

Query definition mới là logic cốt lõi.

Rows chỉ là:

```text
persisted representation
```

của kết quả query đó.

---

# 27. Ai chịu trách nhiệm maintain?

Đây là khác biệt rất quan trọng với Data Engineer.

## Aggregate Table

```text
fact_sales
     │
     ▼
Airflow / dbt / Spark / SQL Pipeline
     │
     ▼
aggregate TABLE
```

Data Engineer chịu trách nhiệm:

```text
schedule
refresh
incremental logic
retry
backfill
quality checks
auditing
dependency
```

---

## Materialized View

```text
fact_sales
     │
     ▼
Database Materialized View mechanism
     │
     ▼
persisted derived result
```

Database hiểu:

```text
object này được derive từ query nào
```

và cung cấp cơ chế materialization/refresh.

Điều này **không có nghĩa** mọi database đều tự động refresh hoàn toàn.

Ý chính là:

> Database hiểu quan hệ giữa MV và query tạo ra nó.

---

# 28. Khi nào TABLE aggregate tốt hơn?

Trong Data Engineering thực tế, aggregate table rất phổ biến.

Ví dụ pipeline:

```text
01:00 ingest raw data

01:15 build dimensions

01:30 build fact_sales

01:45 build daily_sales

02:00 run data quality

02:10 publish BI dataset
```

Hoặc business logic phức tạp:

```text
fact_sales
      +
refund
      +
promotion
      +
exchange_rate
      +
business calendar
      +
late arriving data
      ↓
daily revenue
```

Ngoài transformation còn cần:

```text
validate
retry
audit
backfill
alert
data quality
lineage
```

Trong trường hợp này:

```text
TABLE
+
dbt / Airflow / Spark
```

thường linh hoạt hơn.

---

# 29. Khi nào Materialized View thuận tiện hơn?

Nếu bài toán chỉ là:

```text
Có một query SQL nặng
+
query đó được dùng lặp đi lặp lại
+
database hỗ trợ MV tốt
```

thì tạo một Materialized View có thể đơn giản hơn rất nhiều so với xây nguyên một pipeline riêng.

Concept:

```text
"Database ơi,
query này tôi dùng rất nhiều.
Hãy materialize kết quả của nó."
```

---

# 30. Table vs View vs Materialized View

| Đặc điểm | Table | View | Materialized View |
|---|---|---|---|
| Lưu rows vật lý | Có | Không | Có |
| Có query definition | Không bắt buộc | Có | Có |
| Dữ liệu derived từ source | Có thể | Có | Có |
| Query source mỗi lần | Không | Có | Thường không |
| Dữ liệu có thể stale | Có thể | Không theo nghĩa MV | Có |
| Cần refresh | Theo pipeline | Không | Có |
| Logic maintenance | Pipeline/app | DB query execution | DB + refresh mechanism |
| Phù hợp abstraction | Không phải mục tiêu chính | Rất phù hợp | Có |
| Phù hợp precomputation | Có thể | Không | Rất phù hợp |
| Linh hoạt ETL phức tạp | Cao | Thấp | Thấp hơn table pipeline |

---

# 31. Một sơ đồ để nhớ

```text
                    SOURCE
                  fact_sales
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
   MATERIALIZED VIEW         ETL / dbt
          │                       │
          │ DB knows              │ Engineer writes
          │ query definition      │ maintenance logic
          │                       │
          ▼                       ▼
    persisted result       aggregate TABLE
```

Hai bên có thể chứa cùng dữ liệu:

```text
date | store | revenue
```

Nhưng khác ở:

```text
ownership
+
semantics
+
maintenance
+
dependency
```

---

# 32. Có thể hiểu Materialized View là một shortcut

Nếu dùng Table:

```text
Query logic
   ↓
ETL code
   ↓
TABLE
   ↓
Scheduling
   ↓
Maintenance
```

Nếu dùng Materialized View:

```text
Query definition
      ↓
DATABASE
      ↓
Persisted result
```

Materialized View cung cấp một abstraction ở database level cho bài toán:

```text
precompute + persist + refresh
```

---

# 33. Liên hệ với dbt Materialization

Trong dbt, một model SQL:

```sql
SELECT
    store_id,
    SUM(amount) AS revenue
FROM {{ ref('fact_sales') }}
GROUP BY store_id;
```

có thể được materialize dưới nhiều dạng:

```text
VIEW
TABLE
INCREMENTAL TABLE
```

Câu hỏi chung là:

> Logical transformation này nên được physically persist như thế nào?

Đây cũng là tư tưởng đằng sau Materialized View.

---

# 34. Trade-off tam giác

Có thể hình dung:

```text
             Freshness
                ▲
               / \
              /   \
             /     \
            /       \
           /         \
Performance ────────── Cost
```

Muốn query rất nhanh:

```text
materialize nhiều
```

nhưng:

```text
storage ↑
refresh cost ↑
maintenance ↑
```

Muốn dữ liệu rất fresh:

```text
refresh thường xuyên
```

nhưng:

```text
compute cost ↑
```

Không có lựa chọn miễn phí.

---

# 35. Rule of Thumb cho Data Engineer

Hãy hỏi 3 câu:

## 1. Query có đắt không?

Ví dụ:

```text
large scan
JOIN
GROUP BY
aggregation
```

Nếu không đắt → thường không cần MV.

## 2. Query có được gọi thường xuyên không?

Nếu chỉ chạy một lần mỗi tháng → chưa chắc cần precompute.

Nếu chạy hàng nghìn lần/ngày → rất đáng xem xét.

## 3. Business chấp nhận dữ liệu cũ bao lâu?

Ví dụ:

```text
24h
1h
15 phút
1 phút
real-time
```

Điều này quyết định refresh strategy.

---

# 36. Khi nào nên cân nhắc Materialized View?

Có pattern:

```text
Expensive query
      +
Repeated query
      +
Acceptable staleness
      ↓
Materialized View
```

Ví dụ:

```text
Sales dashboard
Revenue by day
Revenue by region
Top products
Monthly aggregation
Customer lifetime value
```

---

# 37. Khi nào không nên dùng?

Không nên mặc định tạo MV cho mọi query.

Không phù hợp khi:

- query vốn đã rất nhanh
- dữ liệu cần real-time tuyệt đối
- logic transformation rất phức tạp
- cần orchestration nhiều bước
- cần custom retry / backfill / validation
- hệ quản trị có hạn chế lớn về refresh hoặc SQL hỗ trợ
- dữ liệu thay đổi quá thường xuyên khiến refresh tốn hơn lợi ích nhận được

---

# 38. Liên hệ với các concept Data Engineering khác

## Delta Load

```text
Delta Load
    │
    └── chỉ process dữ liệu thay đổi
                 │
                 ▼
        Incremental Refresh
```

---

## Granularity

Raw fact có grain:

```text
1 row = 1 transaction
```

MV có thể có grain cao hơn:

```text
1 row = 1 store / 1 day
```

Tức là:

```text
transaction grain
      ↓
aggregate
      ↓
daily-store grain
```

---

## Data Modeling

```text
Fact + Dimensions
        ↓
expensive analytical query
        ↓
Materialized View
```

---

## Batch Processing

Có thể:

```text
refresh MV mỗi giờ / mỗi ngày
```

---

## Streaming

Streaming system có thể maintain continuously updated derived state.

Ý tưởng tương tự:

```text
source events
      ↓
incrementally maintained state
```

---

## OLAP

Materialized View đặc biệt hợp với:

```text
large scans
aggregations
repeated analytical queries
```

---

# 39. Câu nhớ quan trọng nhất

```text
VIEW
=
stored SQL
```

```text
MATERIALIZED VIEW
=
stored SQL
+
stored/precomputed result
```

```text
AGGREGATE TABLE
=
stored result
+
pipeline-managed transformation logic
```

---

# 40. Chốt bản chất

Materialized View tồn tại vì:

```text
same expensive transformation
          ↓
executed repeatedly
          ↓
waste compute
```

Giải pháp:

```text
compute once
    ↓
persist result
    ↓
reuse many times
```

Đổi lại:

```text
storage
+
refresh cost
+
potential stale data
```

Điểm quan trọng nhất không phải:

> Materialized View có lưu data hay không?

Mà là:

> **Materialized View là một derived result mà database biết query định nghĩa của nó và hỗ trợ cơ chế materialization/refresh.**

Trong khi một aggregate table thông thường thường được Data Engineer/pipeline tự quản lý.

---

# 41. Mental Model cuối cùng

```text
TABLE
"I own these rows."
```

```text
VIEW
"I own this query."
```

```text
MATERIALIZED VIEW
"I own this query,
and I persist its result."
```

Đây là mental model dễ nhớ nhất khi học Materialized View.
