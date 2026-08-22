# ETL, ELT, EtLT và cách lựa chọn trong Data Engineering

## 1. Bối cảnh: Vì sao ETL ra đời?

Ban đầu, doanh nghiệp có nhiều hệ thống vận hành khác nhau:

```text
MySQL
├── orders
└── customers

PostgreSQL
└── inventory

CRM
├── leads
└── campaigns

Excel / CSV
└── monthly_budget
```

Các hệ thống này chủ yếu phục vụ hoạt động nghiệp vụ hằng ngày, tức là workload kiểu **OLTP (Online Transaction Processing)**:

- INSERT/UPDATE liên tục
- transaction nhanh
- nhiều concurrent users
- yêu cầu ACID
- thường query theo một hoặc một vài record

Ví dụ:

```sql
INSERT INTO orders ...
UPDATE inventory ...
SELECT * FROM orders WHERE id = ...
```

Nhưng phía business lại cần những câu hỏi phân tích như:

> Doanh thu từng tháng trong 3 năm gần đây theo quốc gia, loại khách hàng và category sản phẩm là bao nhiêu?

Nếu chạy trực tiếp những query lớn như JOIN, GROUP BY, aggregation trên production database thì workload phân tích có thể tranh tài nguyên với ứng dụng.

```text
Application
    │
    ▼
Production DB
    │
    ├── Orders
    ├── Payments
    ├── Inventory
    └── BI chạy JOIN + GROUP BY lớn
```

Vì vậy xuất hiện một hệ thống riêng cho phân tích:

```text
OLTP
  │
  │ ???
  ▼
OLAP / Data Warehouse
```

Phần `???` chính là nơi ETL ra đời.

---

# 2. ETL là gì?

ETL:

```text
Extract
   ↓
Transform
   ↓
Load
```

Flow:

```text
Source
  │
  │ Extract
  ▼
ETL Server
  │
  │ Transform
  ▼
Clean / Structured Data
  │
  │ Load
  ▼
Data Warehouse
```

Điểm quan trọng nhất:

> **Dữ liệu được transform trước khi được load vào destination cuối.**

---

# 3. ETL hoạt động như thế nào?

Giả sử dữ liệu nguồn:

```text
orders.csv

order_id | customer | amount | date
-----------------------------------
1        | An       | $100   | 01/02/2026
2        | Binh     | 200000 | 2026-02-01
3        | AN       | null   | 02-01-26
```

## Extract

Lấy dữ liệu từ nhiều nguồn:

```text
MySQL
CSV
API
CRM
 ↓
ETL Engine
```

## Transform

Ví dụ:

### Chuẩn hóa tiền tệ

```text
$100
↓
2,500,000 VND
```

### Chuẩn hóa ngày

```text
01/02/2026
2026-02-01
02-01-26

↓

2026-02-01
```

### Dedupe

```text
An
AN
an

↓

AN
```

### Validate

```text
amount != null
customer_id exists
order_date valid
```

## Load

Sau khi dữ liệu sạch, mới load vào Data Warehouse:

```text
Data Warehouse

dim_customer
dim_product
dim_date
fact_sales
```

---

# 4. Vì sao ETL từng rất phù hợp?

Data Warehouse truyền thống thường có:

```text
Storage: đắt
Compute: đắt
```

Ví dụ các hệ thống on-premise như Oracle hoặc Teradata.

Khi storage đắt, người ta không muốn lưu:

```text
10 TB raw
+ 10 TB staging
+ 10 TB cleaned
+ 10 TB marts
```

Thay vào đó:

```text
Source
 ↓
lọc
 ↓
clean
 ↓
aggregate
 ↓
chỉ giữ dữ liệu cần thiết
 ↓
Warehouse
```

Tức là:

> **Transform trước để giảm dữ liệu cần lưu và đảm bảo Warehouse chỉ chứa dữ liệu đã được chuẩn hóa.**

---

# 5. Vấn đề của ETL

Giả sử hiện tại business chỉ cần:

```text
revenue
```

Data Engineer transform:

```text
Raw Order
   ↓
SELECT
    customer_id,
    date,
    price * quantity AS revenue
   ↓
Warehouse
```

Các column khác như:

```text
discount
campaign_id
device
metadata
```

có thể bị bỏ.

Ba tháng sau Data Scientist hỏi:

> Discount ảnh hưởng doanh thu như thế nào?

Nếu `discount` không được load vào Warehouse, Data Engineer phải:

```text
Source
 ↓
extract lại
 ↓
transform lại
 ↓
load lại
```

Nếu source chỉ giữ lịch sử trong một thời gian ngắn thì dữ liệu thậm chí có thể mất.

Đây là nhược điểm lớn của tư duy:

```text
Transform → rồi mới lưu
```

---

# 6. Vì sao ELT xuất hiện?

Sau này Cloud phát triển:

```text
Amazon S3
Google Cloud Storage
Azure Data Lake Storage
```

và các cloud analytical platforms như:

```text
Snowflake
BigQuery
Redshift
Databricks
Microsoft Fabric
```

Hai điều thay đổi lớn:

```text
Storage ↓ rẻ hơn

Compute ↑ mạnh và scalable hơn
```

Lúc này xuất hiện câu hỏi:

> Nếu storage đã rẻ và analytical platform có compute rất mạnh, tại sao phải transform trước khi lưu?

Thay vì:

```text
Extract
 ↓
Transform
 ↓
Load
```

có thể làm:

```text
Extract
 ↓
Load raw data
 ↓
Transform later
```

Đó là **ELT**.

---

# 7. ELT là gì?

ELT:

```text
Extract
   ↓
Load
   ↓
Transform
```

Flow:

```text
Source
   │
   │ Extract
   ▼
Raw Data
   │
   │ Load
   ▼
Data Platform
   │
   │ Transform
   ▼
Clean / Modelled Data
```

Ví dụ:

```text
PostgreSQL
Salesforce
Stripe
Google Ads
CSV
      │
      │ Extract + Load
      ▼
┌─────────────────────────────┐
│       Data Platform         │
│                             │
│ raw_orders                  │
│ raw_customers               │
│ raw_payments                │
│                             │
│          ↓ SQL / dbt        │
│                             │
│ stg_orders                  │
│ stg_customers               │
│                             │
│          ↓                  │
│                             │
│ dim_customer                │
│ dim_product                 │
│ fact_sales                  │
└─────────────────────────────┘
```

Điểm chính:

> **Dữ liệu được load vào platform trước, sau đó mới transform bằng compute của chính platform đó.**

---

# 8. Ví dụ ETL vs ELT

Giả sử source có bảng:

```text
orders

id | customer | quantity | price | discount
```

Business hiện tại chỉ cần:

```text
revenue = quantity * price
```

## ETL

```text
MySQL
 ↓ Extract

ETL Server
 ↓
quantity * price
 ↓
remove unused columns
 ↓
clean null
 ↓

Warehouse

fact_sales
---------------------
customer_id
revenue
date
```

Warehouse có thể không còn raw data ban đầu.

---

## ELT

```text
MySQL
 ↓

Warehouse / Lakehouse

raw_orders
----------------------
id
customer
quantity
price
discount
...
```

Sau đó transform:

```sql
CREATE TABLE fact_sales AS
SELECT
    customer,
    quantity * price AS revenue
FROM raw_orders;
```

Nếu sau này cần phân tích discount:

```sql
SELECT
    customer,
    quantity * price AS gross_revenue,
    quantity * price * (1 - discount) AS net_revenue
FROM raw_orders;
```

Không cần extract lại source.

---

# 9. Sức mạnh chính của ELT

ELT tách hai vấn đề:

```text
Data Movement
```

và:

```text
Data Transformation
```

Flow:

```text
Source
 ↓
Raw
```

sau đó:

```text
Raw
 ↓
Staging
 ↓
Intermediate
 ↓
Marts
```

Đây là một tư duy rất quan trọng trong Data Engineering hiện đại.

---

# 10. Vì sao Data Engineer thường thích ELT?

Data Engineer thường không biết trước tất cả use case tương lai.

Hôm nay:

```text
BI
```

cần revenue.

Ngày mai:

```text
Data Science
```

cần clickstream.

Ngày kia:

```text
Fraud Detection
```

cần payment metadata.

Nếu ETL loại bỏ dữ liệu quá sớm:

```text
Source

A B C D E F G

       ETL

A B C

Warehouse
```

thì:

```text
D E F G → mất
```

Với ELT:

```text
Source

A B C D E F G

       ↓

RAW

A B C D E F G
```

Sau đó mỗi consumer có thể tự lấy phần mình cần.

---

# 11. Separation of Concerns trong ELT

Trong ETL truyền thống:

```text
Extraction
     +
Transformation
     +
Loading
```

thường gắn chặt với nhau.

Ví dụ:

```text
Oracle
 ↓
Python
 ↓
clean
 ↓
join
 ↓
aggregate
 ↓
Snowflake
```

Nếu transformation lỗi:

```text
Oracle → Transform X → Warehouse
```

pipeline ingestion có thể dừng.

Trong ELT:

```text
Oracle
   ↓
LOAD
   ↓
raw_orders
```

hoàn thành trước.

Sau đó transformation:

```text
raw_orders
   ↓
stg_orders
   ↓
fact_sales
```

chạy độc lập.

Có thể hiểu:

```text
Ingestion ≠ Transformation
```

Đây là một separation of concerns rất tốt.

---

# 12. ELT giúp backfill và reprocessing dễ hơn

Giả sử ban đầu logic revenue là:

```sql
revenue = quantity * price
```

nhưng sau đó phát hiện đúng phải là:

```sql
revenue = quantity * price * (1 - discount)
```

Nếu dùng ETL và không giữ raw:

```text
Source
 ↓
extract lại
 ↓
re-run ETL
```

Nếu dùng ELT:

```text
raw_orders
     │
     │ vẫn còn
     ▼
change SQL
     │
     ▼
rebuild fact_sales
```

Đây gọi là:

```text
reprocessing / backfill
```

Trong analytics thực tế, việc sửa logic và backfill dữ liệu lịch sử xảy ra rất thường xuyên.

---

# 13. ETL có chết không?

**Không.**

Không nên nghĩ:

```text
ETL = cũ ❌
ELT = hiện đại ✅
```

Câu hỏi đúng là:

> **Transform ở đâu là hợp lý nhất?**

---

# 14. Khi nào nên Transform trước Load?

## Trường hợp 1: Sensitive Data

Source có:

```text
name
email
password
credit_card
medical_information
```

Không phải field nào cũng nên được đưa nguyên bản vào analytical platform.

Ta có thể:

```text
Extract
 ↓
Mask / redact / filter
 ↓
Load
```

Đây là ETL-style processing.

---

## Trường hợp 2: Data quá lớn

Giả sử source tạo:

```text
10 TB logs/day
```

nhưng:

```text
90%
```

chỉ là debug log không cần thiết.

ELT thuần:

```text
10 TB
 ↓ network
 ↓ storage
 ↓ processing
```

Có thể tốn kém không cần thiết.

Có thể filter trước:

```text
10 TB
 ↓
filter debug
 ↓
1 TB
 ↓
load
```

Trong trường hợp này transform trước load hoàn toàn hợp lý.

---

# 15. EtLT là gì?

EtLT có thể viết:

```text
E t L T
```

Trong đó chữ `t` nhỏ thể hiện một **small transform** trước khi Load.

Flow:

```text
Extract
   ↓
small transform
   ↓
Load
   ↓
Transform
```

Có thể hiểu EtLT là một hybrid approach giữa ETL và ELT.

---

# 16. Small `t` làm gì?

Small `t` không phải business transformation lớn như:

```text
dim_customer
fact_sales
customer_lifetime_value
monthly_revenue
```

Mà thường là những xử lý kỹ thuật cần thiết trước ingestion:

```text
decompress
decrypt
mask sensitive fields
rename invalid columns
convert encoding
basic type normalization
remove corrupt records
add ingestion_timestamp
```

Sau đó mới Load.

Big `T` phía sau mới xử lý:

```text
JOIN
business rules
dimensions
facts
aggregation
metrics
```

---

# 17. Ví dụ EtLT

API trả về:

```json
{
  "USER-ID": "123",
  "TOTAL$": "100.50",
  "EMAIL": "abc@gmail.com",
  "DATE": "18/08/2026"
}
```

Có thể Warehouse không chấp nhận tên column:

```text
USER-ID
TOTAL$
```

và doanh nghiệp không muốn lưu email nguyên bản.

Small transform:

```text
USER-ID
→ user_id

TOTAL$
→ total

EMAIL
→ hashed_email

DATE
→ standardized string
```

Sau đó:

```text
Load
 ↓

raw_orders
```

Rồi big Transform:

```text
raw_orders
 ↓
stg_orders
 ↓
join customers
 ↓
calculate revenue
 ↓
fact_sales
```

Đó chính là:

```text
E → t → L → T
```

---

# 18. So sánh ETL, ELT và EtLT

| Tiêu chí | ETL | ELT | EtLT |
|---|---|---|---|
| Flow | E → T → L | E → L → T | E → t → L → T |
| Transform chính | Trước Load | Sau Load | Sau Load |
| Pre-processing | Nhiều | Ít | Một ít |
| Giữ raw tại target | Thường ít hơn | Thường có | Gần như raw |
| Flexibility | Thấp hơn | Cao | Cao |
| Reprocessing | Có thể khó | Dễ | Dễ |
| Data quality trước Load | Cao | Có thể thấp | Trung gian |
| Cloud-friendly | Có | Rất cao | Rất cao |
| Governance trước ingestion | Mạnh | Có thể yếu hơn | Mạnh hơn ELT thuần |
| Use case điển hình | Traditional DW | Modern DW / Lake / Lakehouse | Modern hybrid |

---

# 19. Cách nhớ bản chất

## ETL

> **Biết dữ liệu cần trông như thế nào trước khi lưu.**

```text
Schema first

Source
 ↓
MAKE IT RIGHT
 ↓
STORE IT
```

---

## ELT

> **Lưu trước, quyết định cách dùng sau.**

```text
Raw first

Source
 ↓
STORE IT
 ↓
MAKE IT USEFUL
```

---

## EtLT

> **Làm tối thiểu những gì bắt buộc trước khi lưu, còn business logic để sau.**

```text
Source
 ↓
MAKE IT LOADABLE / SAFE
 ↓
STORE IT
 ↓
MAKE IT USEFUL
```

---

# 20. ETL và Schema-on-Write

ETL rất gần với tư duy:

```text
Schema-on-Write
```

Trước khi lưu:

```text
Data
 ↓
conform schema
 ↓
Warehouse
```

Ví dụ:

```text
fact_sales

date_key      INT
customer_key  INT
product_key   INT
quantity      INT
revenue       DECIMAL
```

Data phải được biến đổi thành đúng structure trước khi load.

---

# 21. ELT và tư duy Preserve Raw

ELT gần với tư duy:

```text
Preserve raw first
```

Ví dụ:

```text
/raw
   /orders
   /customers
   /payments
```

Sau đó mới:

```text
raw
 ↓
clean
 ↓
curated
```

Điều này rất gần với Medallion Architecture:

```text
Bronze
 ↓
Silver
 ↓
Gold
```

Có thể hình dung:

```text
        EL
Source ──────→ Bronze
                 │
                 │ T
                 ▼
              Silver
                 │
                 │ T
                 ▼
               Gold
```

---

# 22. Mối quan hệ giữa ELT và Medallion Architecture

ELT và Medallion rất hợp nhau vì cả hai cùng ưu tiên:

> **Lưu raw data trước rồi progressively transform.**

Flow:

```text
              Extract + Load
Source ─────────────────────────→ Bronze

Bronze
  │
  │ Transform
  ▼
Silver
  │
  │ Transform
  ▼
Gold
```

---

# 23. Modern Data Stack có thể trông như thế nào?

```text
             SOURCE
      ┌────────┼─────────┐
      │        │         │
 PostgreSQL  Stripe   Salesforce
      │        │         │
      └────────┼─────────┘
               │
               │ Extract + Load
               ▼
         ┌───────────┐
         │ RAW       │
         │ Warehouse │
         └─────┬─────┘
               │
               │ SQL / dbt
               ▼
         ┌───────────┐
         │ Staging   │
         └─────┬─────┘
               ▼
         Intermediate
               │
               ▼
             Marts
               │
       ┌───────┼───────┐
       ▼       ▼       ▼
      BI      ML     Analytics
```

Transformation có thể tận dụng compute của chính destination platform thay vì cần một ETL server riêng.

---

# 24. Vì sao Data Engineer hiện đại thường nghiêng về ELT?

Không phải vì ELT "mới hơn".

Mà vì hệ thống hiện đại thường có:

```text
Cheap storage
+
Powerful scalable compute
+
Many consumers
+
Changing requirements
```

Trong môi trường đó:

```text
keep raw
+
transform later
```

thường rất có giá trị.

---

# 25. Cách chọn ETL hay ELT dưới góc nhìn Data Engineer

Không nên hỏi trước:

> Chúng ta dùng ETL hay ELT?

Nên hỏi những câu sau.

## 1. Có cần giữ raw không?

Nếu cần:

```text
audit
reprocessing
ML
future analytics
```

→ thiên về:

```text
ELT / EtLT
```

---

## 2. Có dữ liệu không được phép load raw không?

Ví dụ:

```text
PII
secret
password
regulated data
```

→ cần transform hoặc mask trước.

→ thiên về:

```text
ETL / EtLT
```

---

## 3. Destination có compute mạnh không?

Nếu là:

```text
Snowflake
BigQuery
Databricks
Fabric
modern Lakehouse
```

thì transform trong destination thường hợp lý.

→ thiên về:

```text
ELT
```

Nếu target yếu nhưng processing engine bên ngoài mạnh, ví dụ Spark cluster riêng, ETL có thể hợp lý hơn.

---

## 4. Data có quá lớn để load nguyên bản không?

Ví dụ:

```text
100 TB
↓
chỉ cần 2 TB
```

thì filter/aggregate trước có thể hợp lý.

---

## 5. Business logic có thay đổi thường xuyên không?

Nếu:

```text
metric thay đổi
dimension thay đổi
model thay đổi
```

giữ raw giúp rebuild dữ liệu dễ hơn.

→ thiên về:

```text
ELT
```

---

# 26. Một kiến trúc thực tế nên chọn thế nào?

Với một hệ thống analytics hiện đại:

```text
PostgreSQL
MySQL
APIs
Kafka
CSV
```

đổ vào:

```text
Data Lake
Lakehouse
Cloud Warehouse
```

một default hợp lý thường là:

```text
        small t
           ↓
E ─────────────→ L ─────────→ T
```

Hay:

```text
Extract
   ↓
minimum processing
   ├── security
   ├── serialization
   ├── schema normalization
   └── corruption handling
   ↓
Raw / Bronze
   ↓
Transform
   ├── clean
   ├── dedupe
   ├── join
   ├── business rules
   ├── dimensional model
   └── aggregation
   ↓
Silver / Gold
```

Có thể xem đây là **EtLT-style architecture**.

---

# 27. Raw không nhất thiết là hoàn toàn không xử lý

Trong thực tế:

```text
raw ≠ bắt buộc byte-for-byte source
```

Data Engineer đôi khi vẫn phải:

```text
decompress
decode
deserialize
normalize envelope
add metadata
mask sensitive field
```

để dữ liệu có thể được lưu và quản lý.

Điểm quan trọng là phân biệt:

```text
Technical Transformation
```

với:

```text
Business Transformation
```

Ví dụ:

```text
"user-id" → user_id
```

là technical transformation.

Trong khi:

```text
customer_lifetime_value
```

là business transformation.

EtLT là một cách rất hay để thể hiện sự khác biệt này.

---

# 28. Cách nhìn của Data Engineer

Thay vì chỉ nghĩ:

```text
E T L
```

hãy nhìn pipeline thành nhiều concern:

```text
        DATA MOVEMENT
             │
Source ───────────────→ Raw
                         │
                         │
                DATA TRANSFORMATION
                         │
                         ▼
                      Curated
                         │
                         │
                   DATA SERVING
                         ▼
                BI / ML / API
```

Data Engineer nên đặt các câu hỏi:

```text
How do I move it?
How do I preserve it?
How do I transform it?
How do I serve it?
How do I rerun it?
How do I govern it?
```

---

# 29. Kết nối với các kiến thức Data Engineering khác

Có thể ghép các chủ đề lại như sau:

```text
                  DATA ENGINEERING
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
     Source          Integration       Storage
                       ETL/ELT
                                          │
                                          ▼
                                     Data Lake
                                     Lakehouse
                                     Warehouse
                                          │
                                          ▼
                                      Medallion
                                 Bronze → Silver → Gold
                                          │
                                          ▼
                                    Data Modeling
                                    Kimball / etc.
                                          │
                                          ▼
                                      Fact / Dim
                                          │
                                          ▼
                                          BI
```

Ví dụ:

```text
OLTP PostgreSQL
       │
       │ CDC
       ▼
     Kafka
       │
       │ E + L
       ▼
┌───────────────────────┐
│ Bronze                │
│ Raw Orders            │
│ Raw Customers         │
│ Raw Payments          │
└──────────┬────────────┘
           │
           │ T
           ▼
┌───────────────────────┐
│ Silver                │
│ Clean Orders          │
│ Clean Customers       │
└──────────┬────────────┘
           │
           │ T
           ▼
┌───────────────────────┐
│ Gold                  │
│ dim_customer          │
│ dim_product           │
│ fact_sales            │
└──────────┬────────────┘
           │
           ▼
         Power BI
```

---

# 30. Phân biệt vai trò của ELT, Medallion và Kimball

Ba thứ này không cạnh tranh nhau.

Chúng trả lời ba câu hỏi khác nhau.

## ETL / ELT

> **Dữ liệu được di chuyển và transform vào thời điểm nào?**

---

## Medallion Architecture

> **Dữ liệu trưởng thành qua các quality layer như thế nào?**

```text
Bronze → Silver → Gold
```

---

## Kimball / Dimensional Modeling

> **Dữ liệu cuối cùng nên được tổ chức như thế nào để phục vụ analytics?**

```text
Fact
+
Dimension
```

---

# 31. Tóm tắt từng mô hình

## ETL

```text
Extract → Transform → Load
```

> **Clean/model trước, rồi mới đưa vào target.**

Phù hợp khi:

- target yêu cầu dữ liệu có schema chặt
- raw data không được phép lưu
- cần giảm volume trước khi truyền/lưu
- destination compute không mạnh

---

## ELT

```text
Extract → Load → Transform
```

> **Lưu trước, xử lý sau.**

Phù hợp khi:

- storage rẻ
- destination compute mạnh
- cần giữ raw
- business logic thường xuyên thay đổi
- cần backfill/reprocessing
- có nhiều downstream consumers

---

## EtLT

```text
Extract → small Transform → Load → Transform
```

> **Làm tối thiểu những gì cần thiết trước khi lưu; business transformation lớn để sau.**

Phù hợp khi:

- muốn giữ lợi ích của ELT
- nhưng vẫn cần masking, filtering hoặc technical cleanup trước ingestion

---

# 32. Ý quan trọng nhất cần nhớ

> **ETL vs ELT không đơn thuần là đổi vị trí chữ T. Nó phản ánh sự thay đổi của Data Architecture.**

ETL phù hợp với bối cảnh:

```text
storage đắt
compute đắt
schema xác định trước
curated data first
```

ELT phát triển mạnh khi:

```text
storage rẻ
compute scalable
cloud warehouse/lakehouse mạnh
raw data cần được preserve
business requirement thay đổi liên tục
```

EtLT là cách tiếp cận hybrid:

```text
technical/security transform nhỏ
            ↓
           Load
            ↓
business transformation lớn
```

---

# 33. Flow tổng quát nên nhớ

```text
OLTP
  ↓
CDC / Batch / API
  ↓
Extract
  ↓
small transform      ← optional
  ↓
Load
  ↓
Bronze / Raw
  ↓
Transform
  ↓
Silver
  ↓
Transform + Modeling
  ↓
Gold
  ↓
Fact / Dimension
  ↓
Semantic Layer
  ↓
BI / Analytics / ML
```

Có thể ghi nhớ bằng ba câu:

```text
ETL / ELT
→ Data được đưa vào và transform khi nào?

Medallion
→ Data trưởng thành qua các layer ra sao?

Kimball
→ Data phục vụ analytics cuối cùng được model thế nào?
```

---

# 34. Bảng quyết định nhanh

| Tình huống | Hướng phù hợp |
|---|---|
| Warehouse truyền thống, storage hạn chế | ETL |
| Cloud Warehouse có compute mạnh | ELT |
| Data Lake / Lakehouse | ELT |
| Cần giữ raw để audit/backfill | ELT |
| Sensitive data không được phép lưu raw | ETL / EtLT |
| Cần mask hoặc normalize trước ingestion | EtLT |
| Data volume cực lớn nhưng chỉ cần một phần nhỏ | ETL / EtLT |
| Business logic thay đổi thường xuyên | ELT |
| Nhiều team dùng cùng một raw dataset | ELT |
| Muốn Bronze → Silver → Gold | ELT / EtLT |

---

# 35. Nguồn tham khảo

- SSP — ETL: https://www.ssp.sh/brain/etl
- SSP — ELT: https://www.ssp.sh/brain/elt
- SSP — EtLT: https://www.ssp.sh/brain/etlt
- SSP — ETL vs ELT: https://www.ssp.sh/brain/etl-vs-elt
