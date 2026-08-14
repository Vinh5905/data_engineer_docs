# Phần III — Medallion Architecture

Tiếp tục đúng cách chúng ta đã đi với **Inmon** và **Kimball**: không học Medallion bằng cách nhớ:

> Bronze = Raw  
> Silver = Clean  
> Gold = Business

Cách đó đúng nhưng **chưa giải thích được tại sao kiến trúc này tồn tại**.

Ta sẽ bắt đầu từ vấn đề của doanh nghiệp, rồi để kiến trúc tự xuất hiện.

---

# 1. Trước tiên: Medallion Architecture thực chất là gì?

Điểm này rất quan trọng.

**Medallion Architecture không hẳn là một kiến trúc Data Warehouse hoàn chỉnh giống Inmon hay Kimball.**

Nó đúng hơn là một **data design pattern / processing pattern**:

```text
Raw
 ↓
Validated / Cleaned
 ↓
Business-ready
```

và người ta đặt tên ba trạng thái đó là:

```text
Bronze
  ↓
Silver
  ↓
Gold
```

Mục tiêu cốt lõi là:

> **Dữ liệu càng đi xuống pipeline thì chất lượng, cấu trúc và mức độ tin cậy của nó càng tăng.**

Databricks hiện cũng mô tả Medallion là một **data design pattern**, hay còn gọi là multi-hop architecture, thay vì xem Bronze/Silver/Gold như những tầng bắt buộc tuyệt đối.

---

# 2. Business problem — tại sao lại cần Medallion?

Giả sử chúng ta vẫn có một hệ thống e-commerce.

Nguồn dữ liệu:

```text
MySQL
 ├── customers
 ├── orders
 └── order_items

PostgreSQL
 └── products

MongoDB
 └── product_reviews

Kafka
 └── clickstream

External API
 └── advertising_data

CSV từ Marketing
 └── campaign.csv
```

Business hỏi:

> Doanh thu tháng này bao nhiêu?

Nếu đơn giản, bạn có thể nghĩ:

```text
MySQL
    ↓
Power BI
```

Nhưng thực tế dữ liệu sẽ gặp đủ thứ vấn đề.

Ví dụ `orders`:

```text
order_id | customer_id | order_date           | total
--------------------------------------------------------
1001     | C01         | 2026-08-01           | 500000
1002     | C02         | 01/08/2026           | 700000
1002     | C02         | 01/08/2026           | 700000
1003     | NULL        | invalid_date         | -100000
```

`customers`:

```text
customer_id | name        | country
---------------------------------------
C01         | Nguyen An   | VN
C02         | Tran Binh   | Vietnam
C03         | Le Chi      | Việt Nam
```

Kafka:

```json
{
  "user": "C01",
  "event": "view_product",
  "timestamp": 1786000000
}
```

Marketing lại gửi:

```text
campaign_id;campaign_name;spend
001;Summer Sale;20000000
```

Dữ liệu:

- khác format;
- duplicate;
- null;
- sai datatype;
- country không thống nhất;
- có late-arriving data;
- có schema thay đổi;
- có batch;
- có streaming;
- có structured;
- có semi-structured.

Nếu chúng ta vừa ingest vừa clean vừa join vừa tính KPI rồi ghi thẳng ra table cho Power BI:

```text
Sources
   ↓
Huge ETL Job
   ↓
Dashboard
```

thì một ngày đẹp trời dashboard sai.

Bạn hỏi:

> Tại sao Revenue tháng 7 hôm qua là 10.2 tỷ mà hôm nay còn 9.8 tỷ?

Data Engineer mở pipeline.

```text
extract
clean
cast
deduplicate
join
business rules
aggregate
...
```

Tất cả nằm chung một nơi.

Và vấn đề lớn nhất xuất hiện:

> **Không biết dữ liệu đã sai từ nguồn, lúc ingestion, lúc cleaning, lúc join hay lúc tính KPI.**

Đây chính là một trong những vấn đề mà cách tổ chức dữ liệu theo nhiều tầng giải quyết.

---

# 3. Ý tưởng quan trọng nhất của Medallion

Thay vì biến:

```text
Dirty Data
```

thành:

```text
Business Data
```

trong **một bước**, ta chia thành nhiều trạng thái.

```text
                     DATA QUALITY
                          ↑

Sources
   │
   ▼
┌─────────────┐
│   BRONZE    │
│    RAW      │
└──────┬──────┘
       │ clean
       │ validate
       │ deduplicate
       ▼
┌─────────────┐
│   SILVER    │
│ CLEAN DATA  │
└──────┬──────┘
       │ business rules
       │ dimensional model
       │ aggregate
       ▼
┌─────────────┐
│    GOLD     │
│ BUSINESS    │
└──────┬──────┘
       │
       ▼
 BI / ML / API / Applications
```

SSP mô tả quá trình này rất hay:

```text
dirty        → clean
granular     → aggregated
technical    → business-oriented
```

Gold thường chứa các mô hình đọc tối ưu, dimensional model, facts và dimensions; tuy nhiên đây là xu hướng thiết kế chứ không phải luật rằng mọi Silver phải normalized hay mọi Gold phải aggregate.

---

# 4. Layer 1 — Bronze

## 4.1 Bronze để làm gì?

Bronze có nhiệm vụ:

> **Lưu dữ liệu gần với trạng thái mà source gửi đến nhất có thể.**

Ví dụ MySQL:

```text
orders
```

được CDC sang Bronze:

```text
bronze.orders
```

Kafka:

```text
bronze.clickstream
```

API:

```text
bronze.advertising
```

CSV:

```text
bronze.marketing_campaign
```

Pipeline:

```text
MySQL ──────────┐
PostgreSQL ─────┤
MongoDB ────────┤
Kafka ──────────┼──→ BRONZE
REST API ───────┤
CSV ────────────┘
```

Bronze chủ yếu tập trung vào:

```text
GET THE DATA IN
```

chứ **chưa phải làm cho dữ liệu đẹp**.

Databricks hiện khuyến nghị Bronze giữ raw, unvalidated data, append incrementally, giữ lịch sử để có thể audit và reprocess; workload chính đọc Bronze thường là pipeline tạo Silver chứ không phải business analyst.

---

# 5. Tại sao Bronze phải giữ dữ liệu Raw?

Đây mới là câu quan trọng.

Giả sử source gửi:

```text
customer_id = C001
country     = VN
```

Pipeline cleaning chuyển:

```text
VN
↓
Vietnam
```

Sau 6 tháng business nói:

> Chuẩn mới của công ty không dùng `Vietnam`, phải sử dụng ISO alpha-3 là `VNM`.

Nếu bạn **không giữ raw**:

```text
VN
```

đã biến mất.

Bạn chỉ còn:

```text
Vietnam
```

Nhưng nếu Bronze vẫn giữ:

```text
bronze.customer

customer_id | country
---------------------
C001        | VN
```

thì chỉ cần viết lại Silver:

```text
VN → VNM
```

Sau đó rebuild:

```text
Bronze
   ↓
Silver
   ↓
Gold
```

Không cần quay lại source.

Đây là một lợi ích rất lớn của Bronze.

---

# 6. Nếu bỏ Bronze thì sao?

Giả sử:

```text
Source
   ↓
Silver
```

Trong quá trình ingestion:

```python
if customer_id is null:
    drop()
```

Sau một tháng business hỏi:

> Tại sao có 20,000 giao dịch bị mất?

Bạn không thể điều tra.

Bởi vì record gốc đã bị drop ngay lúc ingestion.

Nếu có Bronze:

```text
Source

1001 | NULL | 500000
             ↓

Bronze

1001 | NULL | 500000
             ↓

Silver

quarantine / invalid
```

Bạn vẫn chứng minh được:

> Source thực sự đã gửi record đó.

Cho nên Bronze còn đóng vai trò như **bằng chứng lịch sử của dữ liệu**.

---

# 7. Bronze không có nghĩa là hoàn toàn không được đụng vào dữ liệu

Một lỗi hiểu phổ biến:

> Bronze bắt buộc phải là byte-for-byte y hệt source.

Không nhất thiết.

Ví dụ source gửi JSON:

```json
{
    "id": 1001,
    "amount": 500000
}
```

Bạn hoàn toàn có thể lưu dưới Delta/Iceberg/Parquet.

Điều quan trọng là:

> **Không làm mất ý nghĩa và fidelity của dữ liệu nguồn.**

Có thể thêm metadata:

```text
_ingestion_time
_source_system
_source_file
_batch_id
_offset
_partition
```

Ví dụ:

```text
order_id | amount | _source | _ingestion_time
----------------------------------------------------
1001     | 500000 | mysql   | 2026-08-14 08:00
```

Databricks cũng khuyến nghị hạn chế validation tại Bronze và có thể thêm metadata như provenance hoặc tên source file; trong một số trường hợp còn giữ field ở dạng string/VARIANT/binary để tránh làm rơi dữ liệu khi source bất ngờ thay schema.

---

# 8. Bronze giải quyết một vấn đề cực lớn: Replay

Giả sử:

```text
Bronze
  ↓
Silver v1
  ↓
Gold
```

Sau đó phát hiện:

```text
Silver v1
```

có bug.

Ví dụ code:

```sql
WHERE amount > 1000
```

nhưng business rule đúng phải là:

```sql
WHERE amount >= 1000
```

Nếu không có Bronze:

```text
¯\_(ツ)_/¯
```

Có Bronze:

```text
Bronze
  ↓
Silver v2
  ↓
Gold rebuild
```

Đây là lý do khả năng **recreate downstream tables from raw data** là một lợi ích quan trọng của pattern này.

---

# 9. Một cách nhớ Bronze

Đừng nhớ:

> Bronze = dữ liệu bẩn.

Hãy nhớ:

> **Bronze = dữ liệu mà tôi tin rằng source đã thực sự gửi cho tôi.**

Nó có thể bẩn.

Nhưng chính vì nó bẩn mà nó có giá trị.

```text
Bronze ≠ trustworthy business data

Bronze = trustworthy representation
         of what source gave us
```

Hai chữ **trustworthy** ở đây mang hai nghĩa hoàn toàn khác nhau.

---

# 10. Layer 2 — Silver

Bây giờ Bronze đang có:

```text
bronze.orders
bronze.customers
bronze.products
bronze.reviews
bronze.clickstream
bronze.campaign
```

Nhưng business analyst có nên query trực tiếp không?

Không nên.

Ví dụ:

```sql
SELECT *
FROM bronze.orders;
```

ra:

```text
1001 | C01  | 2026-08-01 | 500000
1002 | C02  | 01/08/26   | 700000
1002 | C02  | 01/08/26   | 700000
1003 | null | blah       | -100000
```

Business không muốn mỗi dashboard phải tự:

```text
deduplicate
cast datatype
handle NULL
standardize
validate
join identities
```

Cho nên cần Silver.

---

# 11. Silver giải quyết câu hỏi gì?

Silver trả lời:

> **Dữ liệu hợp lệ và nhất quán của doanh nghiệp thực sự là gì?**

Pipeline:

```text
Bronze
   │
   │ schema enforcement
   │ type casting
   │ deduplicate
   │ null handling
   │ standardization
   │ validation
   │ joining
   │ enrichment
   ▼
Silver
```

Các thao tác như schema enforcement, xử lý null, deduplication, late/out-of-order records, type casting, data-quality checks và joins chủ yếu xuất hiện tại Silver.

---

# 12. Ví dụ cleaning ở Silver

Bronze:

```text
customer_id | country
---------------------
C01         | VN
C02         | Vietnam
C03         | Việt Nam
```

Silver:

```text
customer_id | country_code
--------------------------
C01         | VN
C02         | VN
C03         | VN
```

---

Bronze:

```text
order_date
----------
2026-08-01
01/08/2026
Aug 1 2026
```

Silver:

```text
order_date
----------
2026-08-01
2026-08-01
2026-08-01
```

---

Bronze:

```text
order_id
--------
1001
1002
1002
```

Silver:

```text
order_id
--------
1001
1002
```

Đây là lúc:

```text
raw representation
        ↓
valid business entity
```

---

# 13. Nhưng Silver không chỉ là "clean data"

Đây là phần rất dễ học thiếu.

Giả sử:

```text
CRM customer:

customer_id = 777
email       = an@gmail.com
```

E-commerce:

```text
user_id = C001
email   = an@gmail.com
```

Marketing:

```text
contact_id = M999
email      = an@gmail.com
```

Ba source nói về cùng **một người** nhưng dùng ba ID.

Bronze vẫn nên giữ:

```text
crm_customer
ecommerce_user
marketing_contact
```

Nhưng Silver có thể tạo:

```text
silver.customer
```

```text
customer_key | email        | crm_id | ecommerce_id | marketing_id
-------------------------------------------------------------------
12345        | an@gmail.com | 777    | C001         | M999
```

Tức là Silver bắt đầu hình thành:

> **Enterprise view of business entities.**

Silver có thể join/enrich nhiều Bronze dataset để tạo những representation phù hợp hơn cho downstream analytics.

---

# 14. Đây là chỗ Medallion bắt đầu hơi giống Inmon

Bạn còn nhớ Inmon:

```text
Sources
   ↓
Staging
   ↓
Enterprise Data Warehouse
   ↓
Data Mart
```

Trong EDW:

```text
Customer
Product
Order
Store
...
```

được integrate và chuẩn hóa ở mức enterprise.

Medallion:

```text
Sources
   ↓
Bronze
   ↓
Silver
   ↓
Gold
```

Silver đôi khi cũng tạo:

```text
Customer
Product
Order
```

ở mức enterprise.

Cho nên nhìn qua rất giống:

```text
Inmon Core
    ≈
Silver
```

Nhưng **không được kết luận Silver = Inmon EDW**.

Vì Silver chỉ nói:

> Đây là tầng dữ liệu đã được validated/refined/integrated.

Nó **không ép bạn phải dùng 3NF**.

Modeling có thể bắt đầu ngay ở Silver và có thể chọn flatten, normalize, structs, arrays, maps hay các representation khác tùy dữ liệu.

---

# 15. Vì sao không làm luôn Star Schema trong Silver?

Thực ra **có thể**.

Medallion không cấm.

Nhưng thường bạn muốn Silver giữ dữ liệu ở mức tương đối chi tiết:

```text
silver.orders
silver.order_items
silver.customers
silver.products
```

để rất nhiều downstream consumer có thể tái sử dụng.

Nếu biến nó ngay thành:

```text
fact_sales
dim_customer
dim_product
```

với nhiều KPI và aggregate phục vụ riêng Sales Dashboard thì bạn đang đưa **business-consumption concern** vào quá sớm.

Nên thường:

```text
Silver
    ↓
reusable detailed data
```

còn:

```text
Gold
    ↓
purpose-built business data
```

---

# 16. Nếu bỏ Silver thì sao?

Giả sử:

```text
Bronze
   ↓
Gold Sales
Gold Finance
Gold Marketing
```

Gold Sales viết:

```sql
CASE
    WHEN country = 'VN' THEN 'Vietnam'
...
```

Gold Marketing cũng viết:

```sql
CASE
    WHEN country IN ('VN', 'Việt Nam')
         THEN 'Vietnam'
...
```

Gold Finance lại viết logic khác.

Kết quả:

```text
             Bronze
          /     |      \
         /      |       \
      Sales   Finance   Marketing
       │         │          │
    clean A   clean B    clean C
```

Bạn có **ba định nghĩa về dữ liệu sạch**.

Silver giải quyết chuyện đó:

```text
                  Bronze
                     │
                     ▼
                  Silver
              clean once here
                /    |    \
               /     |     \
              ▼      ▼      ▼
           Sales  Finance Marketing
```

Nguyên tắc:

> **Cleaning và conformance dùng chung nên làm trước khi phân nhánh thành business-specific datasets.**

---

# 17. Một cách nhớ Silver

Đừng chỉ nhớ:

> Silver = clean data.

Hãy nhớ:

> **Silver = phiên bản dữ liệu mà Data Engineer đã đủ tự tin để cho các downstream system tái sử dụng.**

Bronze:

```text
"Source said this."
```

Silver:

```text
"We have validated what this means."
```

Gold:

```text
"This is how the business wants to consume it."
```

Đây là ba câu cực kỳ đáng nhớ.

---

# 18. Layer 3 — Gold

Đến Silver, chúng ta có:

```text
silver.customer
silver.product
silver.order
silver.order_item
silver.store
```

Giả sử CEO hỏi:

> Doanh thu theo tháng, category và region là bao nhiêu?

Bạn hoàn toàn có thể:

```sql
SELECT ...
FROM silver.order
JOIN silver.order_item
JOIN silver.product
JOIN silver.customer
...
```

Nhưng nếu mỗi analyst tự viết lại query đó thì xuất hiện vấn đề giống Kimball:

```text
Analyst A
   ↓
Revenue formula A

Analyst B
   ↓
Revenue formula B

Finance
   ↓
Revenue formula C
```

Cho nên cần Gold.

---

# 19. Gold có nhiệm vụ gì?

Gold biến:

```text
technically correct data
```

thành:

```text
business-ready data
```

Ví dụ:

```text
Silver
 ├── customer
 ├── order
 ├── order_item
 └── product
```

Gold:

```text
Gold Sales
 ├── fact_sales
 ├── dim_customer
 ├── dim_product
 └── dim_date
```

hoặc:

```text
gold.daily_sales
gold.customer_360
gold.product_performance
gold.monthly_revenue
```

Gold là tầng phục vụ analytics/dashboard/ML/application, được tổ chức quanh business functions, business logic và thường được tối ưu cho query performance. Dimensional modeling và aggregation thường xuất hiện tại đây.

---

# 20. Đây chính là nơi Kimball rất hợp với Medallion

Nhớ Kimball:

```text
Business Process
       ↓
Declare Grain
       ↓
Dimensions
       ↓
Facts
       ↓
Star Schema
```

Medallion không bảo bạn:

> Đừng dùng Kimball.

Ngược lại, hai thứ có thể kết hợp rất đẹp:

```text
Sources
   ↓
Bronze
   ↓
Silver
   ↓
Gold
   │
   ├── Dimensional Modeling
   │
   └── Kimball Star Schema
```

Ví dụ:

```text
                    dim_customer
                         │
                         │
dim_date ─────── fact_sales ─────── dim_product
                         │
                         │
                     dim_store
```

Tức là:

> **Medallion nói dữ liệu nên trưởng thành qua pipeline như thế nào.**

Trong khi:

> **Kimball nói dữ liệu analytics nên được model như thế nào.**

Đây là hai câu rất quan trọng.

---

# 21. Ví dụ từ Silver sang Gold

Silver:

```text
silver.orders

order_id
customer_id
order_date
status
```

```text
silver.order_items

order_id
product_id
quantity
unit_price
discount
```

```text
silver.products

product_id
product_name
category
brand
```

Sau đó ta xác định business process:

```text
Sales
```

Grain:

> Một row trong FactSales = một product line trong một order.

Tạo:

```text
gold.fact_sales
```

```text
date_key
customer_key
product_key
order_id
quantity
gross_amount
discount_amount
net_amount
```

Dimensions:

```text
gold.dim_customer
gold.dim_product
gold.dim_date
```

Như vậy Gold chính là nơi chúng ta có thể đem **Kimball** vào.

---

# 22. Ví dụ SQL Gold

Ví dụ tạo dữ liệu doanh thu theo ngày:

```sql
SELECT
    o.order_date,
    p.category,
    SUM(
        oi.quantity * oi.unit_price - oi.discount
    ) AS revenue
FROM silver.orders o
JOIN silver.order_items oi
    ON o.order_id = oi.order_id
JOIN silver.products p
    ON oi.product_id = p.product_id
WHERE o.status = 'COMPLETED'
GROUP BY
    o.order_date,
    p.category;
```

Sau đó Gold có thể lưu:

```text
date       | category    | revenue
------------------------------------
2026-08-01 | Electronics | 500M
2026-08-01 | Fashion     | 200M
2026-08-02 | Electronics | 450M
```

Dashboard không cần biết:

```text
JOIN thế nào?
discount tính sao?
order status nào tính revenue?
timezone nào?
duplicate xử lý sao?
```

Nó chỉ cần:

```sql
SELECT *
FROM gold.daily_category_revenue;
```

---

# 23. Tại sao aggregate thường nằm ở Gold?

Giả sử dashboard luôn hỏi:

```text
monthly revenue by category
```

Có 5 tỷ rows order_items.

Nếu mỗi lần dashboard chạy:

```text
5 billion rows
      ↓
JOIN
      ↓
GROUP BY
      ↓
SUM
```

thì rất tốn.

Gold có thể precompute:

```text
month   | category | revenue
-----------------------------
2026-01 | Phone    | 50B
2026-01 | Laptop   | 35B
...
```

Dashboard chỉ đọc vài nghìn rows.

---

# 24. Nhưng Gold không đồng nghĩa với "aggregate"

Điểm này cần sửa một hiểu lầm rất phổ biến.

Không phải:

```text
Silver = detailed
Gold   = aggregated
```

một cách tuyệt đối.

Gold có thể là một dimensional model ở transaction grain:

```text
fact_sales

order_line_key
customer_key
product_key
quantity
revenue
```

vẫn rất chi tiết.

Điều quan trọng hơn là:

```text
Gold = business consumption ready
```

Aggregation chỉ là **một cách** để làm dữ liệu consumption-ready.

---

# 25. Nếu bỏ Gold thì sao?

Bạn vẫn có thể cho analyst dùng Silver.

Nhưng mỗi analyst sẽ phải hiểu:

```text
Customer table nào?
Revenue tính như thế nào?
Order cancelled có tính không?
Refund xử lý sao?
Timezone nào?
Currency conversion rate nào?
```

Silver có thể:

> đúng về mặt dữ liệu.

Nhưng chưa chắc:

> thuận tiện và nhất quán về mặt business.

Gold giải quyết khoảng cách:

```text
Data Engineer understanding
            ↓
      Business meaning
```

---

# 26. Toàn bộ flow bây giờ

Ta có:

```text
Operational Sources
│
├── MySQL
├── PostgreSQL
├── MongoDB
├── Kafka
├── API
└── Files
       │
       ▼
┌────────────────────────────┐
│          BRONZE            │
│                            │
│ Raw / source-aligned       │
│ Historical                 │
│ Replayable                 │
│ Auditable                  │
│ Minimal transformation     │
└────────────┬───────────────┘
             │
             │ Clean
             │ Cast
             │ Deduplicate
             │ Validate
             │ Standardize
             │ Integrate
             ▼
┌────────────────────────────┐
│          SILVER            │
│                            │
│ Clean                      │
│ Validated                  │
│ Conformed                  │
│ Detailed                   │
│ Reusable                   │
│ Enterprise entities        │
└────────────┬───────────────┘
             │
             │ Business rules
             │ Model
             │ Metrics
             │ Aggregate
             │ Optimize
             ▼
┌────────────────────────────┐
│           GOLD             │
│                            │
│ Business-oriented          │
│ Dimensional models         │
│ Aggregates                 │
│ Data products              │
│ Query optimized            │
└────────────┬───────────────┘
             │
       ┌─────┼───────────────┐
       ▼     ▼               ▼
      BI    ML             Apps
```

Đây chính là logic cốt lõi của Medallion.

---

# 27. Đừng hiểu Medallion là ba database bắt buộc

Không nhất thiết phải có:

```text
bronze_database
silver_database
gold_database
```

Medallion nói về **logical layers**.

Bạn có thể implement:

```text
catalog
├── bronze
├── silver
└── gold
```

hoặc:

```text
s3://data/bronze/
s3://data/silver/
s3://data/gold/
```

hoặc:

```text
database.bronze_orders
database.silver_orders
database.gold_sales
```

Điều quan trọng là **boundary và responsibility**, không phải tên folder.

---

# 28. Tại sao người ta hay dùng Medallion với Data Lake / Lakehouse?

Vì một đặc điểm rất hữu ích của Data Lake/Lakehouse là:

```text
cheap scalable storage
+
structured / semi-structured / unstructured-ish ingestion
+
batch
+
streaming
```

Ta có thể giữ raw data lâu dài ở Bronze, rồi từng bước tạo representation tốt hơn.

Medallion có thể xem như một sự tiến hóa của mô hình classical warehouse layers:

```text
stage
 → cleansing
 → core
 → mart
```

nhưng thích nghi với data lake và modern data processing.

---

# 29. Batch và Streaming đi đâu?

Một điểm quan trọng:

Medallion **không có nghĩa chỉ dành cho batch**.

Ta có thể có:

```text
Batch ──────┐
            ├──→ Bronze
Streaming ──┘
```

Sau đó:

```text
Bronze
  ↓ incremental processing
Silver
  ↓ incremental processing
Gold
```

Ví dụ:

```text
Kafka clickstream
       │
       ▼
bronze.click_events
       │
       ▼
silver.valid_click_events
       │
       ▼
gold.hourly_product_views
```

Không cần chờ cuối ngày.

---

# 30. CDC nằm ở đâu?

Ví dụ database:

```text
orders
```

thay đổi:

```text
INSERT
UPDATE
DELETE
```

CDC capture:

```text
I | order 1001
U | order 1001
D | order 1002
```

Bronze có thể giữ toàn bộ lịch sử change event:

```text
bronze.orders_cdc

operation
timestamp
order_id
...
```

Sau đó Silver reconstruct:

```text
current valid state
```

hoặc lịch sử phù hợp yêu cầu.

Flow:

```text
OLTP Database
     │
     │ CDC
     ▼
Bronze Change Log
     │
     │ merge / deduplicate /
     │ ordering
     ▼
Silver Current State
     │
     ▼
Gold Analytics
```

Ở đây ta thấy rất rõ:

> Bronze quan tâm **source đã thay đổi như thế nào**.

Silver quan tâm:

> **business entity hiện tại/hợp lệ là gì**.

Gold quan tâm:

> **business muốn phân tích điều gì**.

---

# 31. Một bug thực tế để thấy sức mạnh của các layer

Dashboard báo:

```text
Revenue = 20 tỷ
```

Finance bảo:

```text
Không, phải là 18 tỷ.
```

Ta debug ngược.

### Gold

```text
20 tỷ
```

Kiểm tra công thức.

Phát hiện:

```text
status IN (
    'COMPLETED',
    'REFUNDED'
)
```

Oops.

Refunded đang được tính revenue.

Silver:

```text
order_id | status   | amount
-----------------------------
1001     | COMPLETE | 1M
1002     | REFUNDED | 2M
```

Silver đúng.

Bronze:

```text
1002 | REFUNDED | 2M
```

Bronze cũng đúng.

Vậy bug ở:

```text
Gold business rule
```

Sửa Gold.

Nếu tất cả transformation nằm chung:

```text
Source
 ↓
5000-line ETL
 ↓
Dashboard
```

việc xác định bug khó hơn rất nhiều.

Đây là ý nghĩa quan trọng của **separation of concerns**.

---

# 32. Data quality tăng dần như thế nào?

Có thể hình dung:

```text
             Quality
                ▲
                │
Gold       ██████████
Silver     ███████
Bronze     ███
                │
                └──────────→ Processing
```

Nhưng Bronze có chất lượng thấp **không có nghĩa Bronze kém giá trị**.

Nó có một loại giá trị khác:

```text
Bronze → source fidelity

Silver → data correctness

Gold → business usefulness
```

Tôi khuyên bạn nhớ bộ ba này.

---

# 33. Medallion so với Inmon

Bây giờ ghép với phần đã học.

## Inmon

```text
Sources
   ↓
Staging
   ↓
Enterprise Data Warehouse
   ↓
Department Data Marts
   ↓
BI
```

Trọng tâm:

> **Xây dựng integrated enterprise warehouse trước.**

---

## Medallion

```text
Sources
   ↓
Bronze
   ↓
Silver
   ↓
Gold
   ↓
BI / ML / Apps
```

Trọng tâm:

> **Progressively refine data quality and usability.**

Hai cái nhìn có thể ánh xạ tương đối:

```text
Inmon                 Medallion

Sources                Sources
   │                      │
Staging        ~        Bronze
   │                      │
EDW Core       ~        Silver
   │                      │
Data Mart      ~         Gold
```

Nhưng dấu `~` ở đây cực kỳ quan trọng.

Nó có nghĩa:

> **giống về vai trò ở một số hệ thống**, không phải **hai khái niệm tương đương hoàn toàn**.

---

# 34. Medallion so với Kimball

Kimball:

```text
Sources
   ↓
ETL
   ↓
Dimensional Data Marts
   ↓
BI
```

Gold của Medallion rất thích hợp để chứa:

```text
Facts
Dimensions
Star Schema
Aggregates
Semantic datasets
```

Nên bạn hoàn toàn có thể có:

```text
             MEDALLION
                 │
Sources
   ↓
Bronze
   ↓
Silver
   ↓
Gold
   │
   │     KIMBALL
   │        ↓
   ├── fact_sales
   ├── dim_customer
   ├── dim_product
   └── dim_date
```

Cho nên:

> **Kimball và Medallion không phải hai đối thủ để chọn một trong hai.**

Chúng giải quyết hai loại câu hỏi khác nhau.

---

# 35. Inmon vs Kimball vs Medallion — nhìn bằng câu hỏi mà chúng trả lời

| Approach | Câu hỏi chính |
|---|---|
| **Inmon** | Enterprise Data Warehouse nên được tổ chức từ enterprise xuống department như thế nào? |
| **Kimball** | Dữ liệu analytics nên được dimensional model theo business process như thế nào? |
| **Medallion** | Dữ liệu nên trưởng thành qua các mức raw → validated → business-ready như thế nào? |

Đây là cách phân biệt tốt hơn nhiều so với học thuộc sơ đồ.

---

# 36. Chúng thậm chí có thể cùng tồn tại

Một hệ thống có thể dùng cả ba tư tưởng:

```text
                 MEDALLION FLOW
                       │
                       ▼

Sources
   │
   ▼
Bronze
Raw data
   │
   ▼
Silver
Integrated enterprise entities
   │
   │   ← tư tưởng giống INMON ở đây:
   │      enterprise consistency
   │
   ▼
Gold
Dimensional Data Products
   │
   │   ← dùng KIMBALL ở đây:
   │      grain
   │      facts
   │      dimensions
   │      star schema
   ▼
BI
```

Vậy nếu ai hỏi:

> "Công ty dùng Medallion thì có dùng Kimball được không?"

Câu trả lời:

**Hoàn toàn được.**

Thậm chí đó là một combination rất tự nhiên.

---

# 37. Một ví dụ hoàn chỉnh từ đầu đến cuối

Giả sử business:

> Tôi muốn dashboard doanh thu theo customer, product, date.

## Step 1 — Sources

```text
MySQL.orders
MySQL.order_items
CRM.customers
PostgreSQL.products
```

---

## Step 2 — Bronze

```text
bronze.orders_raw
bronze.order_items_raw
bronze.customers_raw
bronze.products_raw
```

Mục tiêu:

> Capture source faithfully.

Không quan tâm dashboard.

---

## Step 3 — Silver

```text
silver.orders
silver.order_items
silver.customers
silver.products
```

Thực hiện:

```text
orders
├── deduplicate
├── valid status
├── timestamp normalization
└── datatype casting

customers
├── merge CRM identities
├── normalize country
├── validate email
└── remove duplicates

products
├── standardize category
├── handle missing attributes
└── integrate product IDs
```

Mục tiêu:

> Tạo các business entity sạch và tái sử dụng được.

---

## Step 4 — Gold

Business process:

```text
Sales
```

Declare grain:

```text
one row per order line
```

Dimensions:

```text
dim_customer
dim_product
dim_date
```

Facts:

```text
quantity
gross_amount
discount_amount
net_amount
```

Kết quả:

```text
                dim_customer
                     │
                     │
dim_date ─────── fact_sales ─────── dim_product
```

Mục tiêu:

> Business consumption.

---

## Step 5 — BI

Power BI:

```sql
SELECT
    d.year,
    d.month,
    p.category,
    SUM(f.net_amount)
FROM gold.fact_sales f
JOIN gold.dim_date d
    ON f.date_key = d.date_key
JOIN gold.dim_product p
    ON f.product_key = p.product_key
GROUP BY
    d.year,
    d.month,
    p.category;
```

Dashboard không phải biết dữ liệu ban đầu đến từ:

```text
MySQL
CRM
PostgreSQL
Kafka
CSV
API
```

Đó chính là abstraction mà architecture tạo ra.

---

# 38. Tại sao không cho Gold đọc thẳng Bronze?

Về kỹ thuật:

```text
Bronze
   ↓
Gold
```

có thể chạy.

Nhưng bạn mất reusable clean layer.

Hôm nay:

```text
Gold Sales
```

Ngày mai:

```text
Gold Marketing
```

Ngày kia:

```text
ML Churn Model
```

Nếu tất cả đều đọc Bronze:

```text
                Bronze
             /    |     \
            /     |      \
        Sales Marketing   ML
          ↓       ↓       ↓
        clean   clean   clean
```

Cleaning logic bị duplicate.

Có Silver:

```text
                Bronze
                   │
                   ▼
                Silver
             clean once
             /    |    \
            /     |     \
        Sales Marketing  ML
```

Đây chính là lý do Silver có giá trị rất lớn ở hệ thống lớn.

---

# 39. Vậy có bắt buộc phải có đúng 3 layer?

**Không.**

Tùy doanh nghiệp có thể là:

```text
Bronze
Silver
Gold
```

hoặc:

```text
Landing
Bronze
Silver
Gold
```

hoặc:

```text
Bronze
Silver
Gold
Platinum
```

hoặc thậm chí mỗi logical stage chứa nhiều layer con.

Medallion là pattern linh hoạt; có hệ thống dùng hai, ba hoặc bốn tầng và một số table không nhất thiết khớp hoàn hảo với đúng một tầng.

---

# 40. Platinum Layer là gì?

Một số variation có thêm:

```text
Bronze
   ↓
Silver
   ↓
Gold
   ↓
Platinum
```

Trong đó:

```text
Platinum
├── Semantic Layer
├── Highly Aggregated
└── Very fast consumption
```

Consumer:

```text
Excel
BI
ML/AI
Data Apps
```

Ý tưởng là tách:

```text
Gold
=
business data products
```

với:

```text
Platinum
=
final consumption / semantic / serving layer
```

Ví dụ:

```text
Gold

fact_sales
dim_product
dim_customer
```

↓

```text
Platinum

monthly_executive_sales
customer_kpi
product_kpi
semantic_model
```

Nhưng nhớ:

> **Platinum không phải tầng canonical bắt buộc của Medallion.**

Canonical pattern phổ biến vẫn là:

```text
Bronze → Silver → Gold
```

---

# 41. Lợi ích lớn của Medallion

Nếu hiểu toàn bộ những phần trên thì các benefit sẽ trở nên rất tự nhiên.

### Reprocess

```text
Bronze
  ↓
rebuild Silver
  ↓
rebuild Gold
```

Không cần đọc lại source.

### Incremental processing

Chỉ xử lý dữ liệu mới:

```text
Yesterday Bronze
+
Today's Bronze
       ↓
incremental Silver
       ↓
incremental Gold
```

### Audit / lineage

```text
Gold metric
   ↑
Silver entity
   ↑
Bronze source record
```

### Fault isolation

Sai ở đâu có thể khoanh vùng:

```text
Source?
Bronze?
Silver?
Gold?
```

### Reuse

```text
Silver Customer
      │
 ┌────┼────┐
 ↓    ↓    ↓
BI    ML  Marketing
```

---

# 42. Nhưng Medallion không tự động làm data architecture tốt

Đây là điều tôi muốn bạn đặc biệt chú ý.

Một team hoàn toàn có thể tạo:

```text
bronze_*
silver_*
gold_*
```

rồi nói:

> "Chúng tôi dùng Medallion Architecture."

Nhưng Silver chứa:

```text
silver_table_001
silver_table_final
silver_table_final_v2
silver_test
```

Gold chứa:

```text
gold_revenue
gold_revenue_new
gold_revenue_final
gold_revenue_finance
```

thì kiến trúc vẫn tệ.

Tên Bronze/Silver/Gold **không giải quyết**:

- data modeling;
- ownership;
- business definitions;
- grain;
- data contracts;
- governance;
- lineage;
- orchestration;
- data quality ownership;
- semantic consistency.

Đó cũng là lý do gọi Medallion là **pattern** rất hợp lý.

Nó giải quyết một phần của data architecture:

> **How data progresses through refinement stages.**

Không giải quyết mọi thứ.

---

# 43. Một lỗi thiết kế nữa: biến Silver thành "Gold chưa hoàn thành"

Ví dụ Silver:

```text
silver_sales_dashboard_temp
silver_marketing_report
silver_finance_kpi
```

Đây là dấu hiệu boundary bị sai.

Silver nên thiên về:

```text
customer
order
product
payment
shipment
```

tức những reusable concepts.

Gold mới thiên về:

```text
sales_performance
customer_profitability
monthly_revenue
marketing_attribution
```

tức business consumption.

Một cách test rất hay:

> **Nếu ngày mai dashboard hiện tại biến mất, dataset này còn giá trị cho những use case khác không?**

Nếu:

```text
Có
```

→ có xu hướng phù hợp Silver.

Nếu:

```text
Không, nó được thiết kế riêng cho dashboard này
```

→ có xu hướng phù hợp Gold.

Không phải luật tuyệt đối, nhưng là mental model rất tốt.

---

# 44. Mental model cuối cùng

Nếu chỉ được nhớ **một sơ đồ**, hãy nhớ cái này:

```text
SOURCE
"What happened in operational systems?"
        │
        ▼
┌──────────────────┐
│      BRONZE      │
│                  │
│ "What exactly   │
│  did the source │
│  send us?"      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│      SILVER      │
│                  │
│ "What does this │
│  data actually  │
│  mean?"         │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│       GOLD       │
│                  │
│ "How does the   │
│  business want  │
│  to consume it?"│
└────────┬─────────┘
         │
         ▼
      CONSUMERS
```

Đây là bản chất của Medallion.

---

# 45. Và ghép cả ba kiến trúc bạn vừa học

Bây giờ bạn đã có thể nhìn chúng như ba **lăng kính khác nhau**.

```text
                         DATA ARCHITECTURE
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
        INMON               KIMBALL             MEDALLION
          │                    │                    │
          ▼                    ▼                    ▼
 Enterprise-first      Analytics modeling      Data refinement
          │                    │                    │
          ▼                    ▼                    ▼
 EDW → Data Mart      Facts + Dimensions    Bronze → Silver → Gold
```

## Inmon hỏi:

> **Tôi tổ chức dữ liệu toàn doanh nghiệp thế nào?**

## Kimball hỏi:

> **Tôi model business process cho analytics thế nào?**

## Medallion hỏi:

> **Tôi đưa dữ liệu từ trạng thái raw thành trusted/business-ready như thế nào?**

Và vì chúng trả lời **ba câu hỏi khác nhau**, bạn hoàn toàn có thể gặp một modern data platform kiểu:

```text
Sources
   │
   ▼
Bronze
Raw / immutable-ish history
   │
   ▼
Silver
Integrated enterprise entities
   │
   │  ← Inmon-like enterprise thinking
   ▼
Gold
Kimball Dimensional Models
   │
   ├── fact_sales
   ├── fact_inventory
   ├── dim_customer
   ├── dim_product
   └── dim_date
   │
   ▼
Semantic Layer
   │
   ▼
Power BI / Tableau / ML / Applications
```

**Đây mới là cách nên hiểu Medallion:** không phải "Bronze là đồng, Silver là bạc, Gold là vàng", mà là một cơ chế **tách các mức độ trách nhiệm trong data pipeline**, để dữ liệu đi từ **source fidelity → validated reusable data → business-consumption data** một cách có kiểm soát.

---

# Ghi nhớ nhanh

```text
BRONZE
= What did the source send?
= Raw / history / replay

SILVER
= What does the data actually mean?
= Clean / validate / conform / integrate

GOLD
= How does the business want to use it?
= Model / metrics / aggregate / optimize
```

Và:

```text
Inmon
→ Enterprise organization

Kimball
→ Dimensional modeling

Medallion
→ Progressive data refinement
```

Đó là ba khái niệm **có thể bổ sung cho nhau**, chứ không nhất thiết là ba lựa chọn loại trừ lẫn nhau.

---

# Tham khảo

- SSP — Medallion Architecture: https://www.ssp.sh/brain/medallion-architecture
- Databricks — Medallion Architecture: https://docs.databricks.com/aws/en/lakehouse/medallion
