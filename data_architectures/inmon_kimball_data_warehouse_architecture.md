# Data Warehouse Architecture: Inmon và Kimball

> Tài liệu tổng hợp đầy đủ phần giải thích về **Inmon Architecture** và **Kimball Architecture** theo đúng cách học:  
> **Mỗi tầng tồn tại để giải quyết vấn đề gì? Nếu bỏ tầng đó thì chuyện gì xảy ra?**

---

# Mục lục

- [Phần I — Inmon Architecture](#phần-i--inmon-architecture)
  - [1. Toàn cảnh kiến trúc Inmon](#1-toàn-cảnh-kiến-trúc-inmon)
  - [2. Sources — nguồn dữ liệu](#2-sources--nguồn-dữ-liệu)
  - [3. Tại sao không cho BI query thẳng Source Database?](#3-tại-sao-không-cho-bi-query-thẳng-source-database)
  - [4. Staging — vùng trung gian](#4-staging--vùng-trung-gian)
  - [5. Tại sao cần Staging?](#5-tại-sao-cần-staging)
  - [6. Staging giúp tách ingestion khỏi transformation](#6-staging-giúp-tách-ingestion-khỏi-transformation)
  - [7. Staging thường chứa dữ liệu như thế nào?](#7-staging-thường-chứa-dữ-liệu-như-thế-nào)
  - [8. Enterprise Data Warehouse — trái tim của Inmon](#8-enterprise-data-warehouse--trái-tim-của-inmon)
  - [9. Enterprise nghĩa là gì?](#9-enterprise-nghĩa-là-gì)
  - [10. Tại sao phải có EDW ở giữa?](#10-tại-sao-phải-có-edw-ở-giữa)
  - [11. Tại sao EDW lại Normalized?](#11-tại-sao-edw-lại-normalized)
  - [12. Tại sao Inmon muốn normalize EDW?](#12-tại-sao-inmon-muốn-normalize-edw)
  - [13. Nhược điểm của Normalization](#13-nhược-điểm-của-normalization)
  - [14. Data Mart](#14-data-mart)
  - [15. Tại sao không cho Analyst query EDW?](#15-tại-sao-không-cho-analyst-query-edw)
  - [16. Ví dụ Data Mart thực tế](#16-ví-dụ-data-mart-thực-tế)
  - [17. Tại sao lại có nhiều Data Mart?](#17-tại-sao-lại-có-nhiều-data-mart)
  - [18. Tại sao Data Mart phải lấy từ EDW?](#18-tại-sao-data-mart-phải-lấy-từ-edw)
  - [19. Analytics](#19-analytics)
  - [20. Tại sao Analytics đặt cuối?](#20-tại-sao-analytics-đặt-cuối)
  - [21. Tại sao mũi tên đi từ trái sang phải?](#21-tại-sao-mũi-tên-đi-từ-trái-sang-phải)
  - [22. Toàn bộ pipeline bằng một ví dụ](#22-toàn-bộ-pipeline-bằng-một-ví-dụ)
  - [23. Inmon và Kimball khác nhau ở đâu?](#23-inmon-và-kimball-khác-nhau-ở-đâu)
  - [24. Kimball nhìn sơ bộ](#24-kimball-nhìn-sơ-bộ)
  - [25. So sánh bản chất Inmon và Kimball](#25-so-sánh-bản-chất-inmon-và-kimball)
  - [26. Tại sao Inmon normalize rồi lại denormalize?](#26-tại-sao-inmon-normalize-rồi-lại-denormalize)
  - [27. Tại sao không chỉ dùng Data Mart?](#27-tại-sao-không-chỉ-dùng-data-mart)
  - [28. Tại sao không chỉ dùng EDW?](#28-tại-sao-không-chỉ-dùng-edw)
  - [29. Separation of Concerns](#29-separation-of-concerns)
  - [30. Cách nhớ Inmon khi học Data Engineer](#30-cách-nhớ-inmon-khi-học-data-engineer)
- [Phần II — Kimball Architecture](#phần-ii--kimball-architecture)
  - [1. Toàn cảnh kiến trúc Kimball](#1-toàn-cảnh-kiến-trúc-kimball)
  - [2. Sources — nơi dữ liệu sinh ra](#2-sources--nơi-dữ-liệu-sinh-ra)
  - [3. Tại sao không cho BI đọc thẳng source?](#3-tại-sao-không-cho-bi-đọc-thẳng-source)
  - [4. Staging / ETL — Back Room](#4-staging--etl--back-room)
  - [5. Tại sao Kimball vẫn cần Staging?](#5-tại-sao-kimball-vẫn-cần-staging)
  - [6. Kimball bắt đầu khác Inmon từ đâu?](#6-kimball-bắt-đầu-khác-inmon-từ-đâu)
  - [7. Kimball bắt đầu từ Business Process](#7-kimball-bắt-đầu-từ-business-process)
  - [8. Tại sao Kimball bắt đầu từ Business Process?](#8-tại-sao-kimball-bắt-đầu-từ-business-process)
  - [9. Tại sao Kimball thích Star Schema?](#9-tại-sao-kimball-thích-star-schema)
  - [10. Trung tâm của Kimball: Fact Table](#10-trung-tâm-của-kimball-fact-table)
  - [11. Tại sao Fact Table ở giữa?](#11-tại-sao-fact-table-ở-giữa)
  - [12. Dimension Table dùng để làm gì?](#12-dimension-table-dùng-để-làm-gì)
  - [13. Tại sao không ghi hết vào Fact?](#13-tại-sao-không-ghi-hết-vào-fact)
  - [14. Grain — khái niệm cực kỳ quan trọng](#14-grain--khái-niệm-cực-kỳ-quan-trọng)
  - [15. Tại sao Grain quan trọng?](#15-tại-sao-grain-quan-trọng)
  - [16. Ví dụ thiết kế Sales Mart từ đầu](#16-ví-dụ-thiết-kế-sales-mart-từ-đầu)
  - [17. Đây là nơi Kimball nhanh hơn Inmon](#17-đây-là-nơi-kimball-nhanh-hơn-inmon)
  - [18. Vấn đề khi xây nhiều Mart](#18-vấn-đề-khi-xây-nhiều-mart)
  - [19. Conformed Dimension là gì?](#19-conformed-dimension-là-gì)
  - [20. Tại sao Conformed Dimensions quan trọng?](#20-tại-sao-conformed-dimensions-quan-trọng)
  - [21. Kimball tạo Enterprise Data Warehouse bằng cách nào?](#21-kimball-tạo-enterprise-data-warehouse-bằng-cách-nào)
  - [22. Kimball Bus Architecture](#22-kimball-bus-architecture)
  - [23. Tại sao cần Bus Matrix?](#23-tại-sao-cần-bus-matrix)
  - [24. Điểm hay của Kimball](#24-điểm-hay-của-kimball)
  - [25. Ví dụ xây Sales rồi mở rộng Inventory](#25-ví-dụ-xây-sales-rồi-mở-rộng-inventory)
  - [26. Mở rộng thêm Returns](#26-mở-rộng-thêm-returns)
  - [27. Surrogate Key](#27-surrogate-key)
  - [28. Tại sao không dùng customer_id từ source?](#28-tại-sao-không-dùng-customer_id-từ-source)
  - [29. Slowly Changing Dimension](#29-slowly-changing-dimension)
  - [30. SCD Type 1](#30-scd-type-1)
  - [31. SCD Type 2](#31-scd-type-2)
  - [32. Tại sao SCD Type 2 hữu ích?](#32-tại-sao-scd-type-2-hữu-ích)
  - [33. Date Dimension](#33-date-dimension)
  - [34. Tại sao không tính thời gian hoàn toàn trong BI?](#34-tại-sao-không-tính-thời-gian-hoàn-toàn-trong-bi)
  - [35. Fact không phải lúc nào cũng là Transaction](#35-fact-không-phải-lúc-nào-cũng-là-transaction)
  - [36. Periodic Snapshot Fact](#36-periodic-snapshot-fact)
  - [37. Accumulating Snapshot Fact](#37-accumulating-snapshot-fact)
  - [38. Toàn bộ ETL Kimball](#38-toàn-bộ-etl-kimball)
  - [39. Tại sao thường load Dimension trước Fact?](#39-tại-sao-thường-load-dimension-trước-fact)
  - [40. Một row đi xuyên Kimball Architecture](#40-một-row-đi-xuyên-kimball-architecture)
  - [41. Analytics trên Star Schema](#41-analytics-trên-star-schema)
  - [42. Kimball không phải mỗi department một mart](#42-kimball-không-phải-mỗi-department-một-mart)
  - [43. Model theo Business Process](#43-model-theo-business-process)
  - [44. Các Fact kết hợp với nhau thế nào?](#44-các-fact-kết-hợp-với-nhau-thế-nào)
  - [45. Ví dụ Enterprise Dimensional Warehouse lớn hơn](#45-ví-dụ-enterprise-dimensional-warehouse-lớn-hơn)
  - [46. Kimball và Performance](#46-kimball-và-performance)
  - [47. Tại sao Dimension thường wide?](#47-tại-sao-dimension-thường-wide)
  - [48. Denormalization trong Kimball](#48-denormalization-trong-kimball)
  - [49. Fact thường narrow nhưng rất tall](#49-fact-thường-narrow-nhưng-rất-tall)
  - [50. Descriptive data và Numeric Event](#50-descriptive-data-và-numeric-event)
  - [51. Kimball có Data Mart không?](#51-kimball-có-data-mart-không)
  - [52. Independent Data Marts khác Kimball thế nào?](#52-independent-data-marts-khác-kimball-thế-nào)
  - [53. Quy trình Kimball từ đầu tới cuối](#53-quy-trình-kimball-từ-đầu-tới-cuối)
  - [54. Kimball không cố xây mọi thứ ngay từ đầu](#54-kimball-không-cố-xây-mọi-thứ-ngay-từ-đầu)
  - [55. Vì sao gọi Kimball là Bottom-Up?](#55-vì-sao-gọi-kimball-là-bottom-up)
  - [56. Bottom-Up không có nghĩa là làm lung tung](#56-bottom-up-không-có-nghĩa-là-làm-lung-tung)
  - [57. So với Inmon bằng cùng một project](#57-so-với-inmon-bằng-cùng-một-project)
  - [58. Kimball tích hợp ở đâu?](#58-kimball-tích-hợp-ở-đâu)
  - [59. Phép so sánh thành phố](#59-phép-so-sánh-thành-phố)
  - [60. Tại sao Kimball được dùng nhiều?](#60-tại-sao-kimball-được-dùng-nhiều)
  - [61. Rủi ro của Kimball](#61-rủi-ro-của-kimball)
  - [62. Cách nhớ Kimball bằng 6 câu hỏi](#62-cách-nhớ-kimball-bằng-6-câu-hỏi)
  - [63. Bốn khái niệm phải nắm chắc](#63-bốn-khái-niệm-phải-nắm-chắc)
  - [64. Tóm toàn bộ Kimball bằng một project](#64-tóm-toàn-bộ-kimball-bằng-một-project)
  - [65. Câu chốt phân biệt Inmon và Kimball](#65-câu-chốt-phân-biệt-inmon-và-kimball)

---

# Phần I — Inmon Architecture

## 1. Toàn cảnh kiến trúc Inmon

Hình kiến trúc Inmon thường được mô tả theo luồng:

```text
Sources
   ↓
Staging
   ↓
Enterprise Data Warehouse
   ↓
Data Marts
   ↓
Analytics
```

Một cách trực quan hơn:

```text
                    INMON ARCHITECTURE

 Operational world                       Analytical world
──────────────────────────────────────────────────────────────

 Sources
    │
    │ Extract
    ▼
┌───────────────┐
│    Staging    │
└───────────────┘
    │
    │ Clean + Standardize + Integrate
    ▼
┌──────────────────────────────────┐
│ Enterprise Data Warehouse        │
│                                  │
│ Customer                         │
│ Order                            │
│ Product                          │
│ Employee                         │
│ Payment                          │
│ ...                              │
│                                  │
│ NORMALIZED MODEL                 │
└──────────────────────────────────┘
       │          │          │
       ▼          ▼          ▼
   Sales Mart Finance Mart Marketing Mart
       │          │          │
       ▼          ▼          ▼
 Dashboard      Report      Analytics
```

Điểm quan trọng nhất của Inmon không phải chỉ là có nhiều tầng, mà là:

> **Đầu tiên xây một kho dữ liệu trung tâm chuẩn hóa cho toàn doanh nghiệp, sau đó mới tạo các Data Mart phục vụ từng nhu cầu phân tích.**

Có thể nhớ:

```text
SOURCE
  ↓
STAGING
  ↓
EDW
  ↓
DATA MART
  ↓
BI
```

Muốn thực sự hiểu kiến trúc này, đừng chỉ học thuộc tên tầng. Hãy luôn hỏi:

> **Nếu bỏ tầng này thì chuyện gì xảy ra?**

---

## 2. Sources — nguồn dữ liệu

Sources là nơi dữ liệu nghiệp vụ được sinh ra.

Ví dụ một công ty thương mại điện tử:

```text
PostgreSQL
└── Order Service
    ├── orders
    └── order_items

MySQL
└── Customer Service
    └── customers

MongoDB
└── Product Service
    └── products

Salesforce
└── CRM

Google Ads
└── Advertising data

CSV / Excel
└── Financial reports
```

Các hệ thống này tồn tại chủ yếu để chạy nghiệp vụ.

Ví dụ Order Service:

```text
Customer đặt hàng
        ↓
INSERT order
        ↓
INSERT order_items
        ↓
Update stock
        ↓
Create payment
```

Đây là workload kiểu **OLTP — Online Transaction Processing**.

Trong khi Data Analyst muốn hỏi:

```text
Doanh thu theo:
    year
    → quarter
        → month
            → country
                → product category
```

Đây là workload phân tích.

Hai loại workload hoàn toàn khác nhau.

---

## 3. Tại sao không cho BI query thẳng Source Database?

Giả sử Dashboard chạy:

```sql
SELECT
    category,
    SUM(quantity * price)
FROM orders
JOIN order_items ...
JOIN products ...
JOIN customers ...
WHERE created_at BETWEEN ...
GROUP BY category;
```

Giả sử production database có:

```text
orders         500 triệu rows
order_items    1.5 tỷ rows
customers      20 triệu rows
products       5 triệu rows
```

Nếu BI query trực tiếp production:

```text
BI query
       │
       ▼
Production PostgreSQL
       │
       ├── API đang SELECT
       ├── User đang INSERT order
       ├── Payment đang UPDATE
       └── BI đang scan 1 tỷ rows
```

Hệ quả:

```text
CPU ↑
Disk IO ↑
Locks ↑
API latency ↑
Database overloaded
```

Vì vậy:

> **Operational database phục vụ application; Data Warehouse phục vụ analytics.**

Đây là lý do đầu tiên cần Data Warehouse.

---

## 4. Staging — vùng trung gian

Staging là nơi dữ liệu từ Source được đưa vào trước khi xử lý sâu hơn.

```text
Sources
   ↓
┌─────────┐
│ Staging │
└─────────┘
```

Có thể hiểu Staging là:

> **Nơi nhận dữ liệu từ source trước khi đưa vào Data Warehouse chính thức.**

Ví dụ:

```text
PostgreSQL.orders
        ↓
staging.orders_raw
```

hoặc:

```text
Salesforce.customer
        ↓
staging.salesforce_customer
```

---

## 5. Tại sao cần Staging?

Một câu hỏi quan trọng:

> **Sao không Source → EDW luôn?**

Giả sử pipeline:

```text
PostgreSQL
     ↓
 transform
     ↓
EDW
```

Có 500 triệu records.

```text
đã xử lý:
300 triệu
    ↓
job crash
```

Nếu không có Staging, có thể phải quay lại Source và extract lại.

Nếu có:

```text
Source
   ↓
Staging
   ↓
Transform
   ↓
EDW
```

thì:

```text
Source → Staging      ✓ hoàn thành

Staging → EDW         ✗ fail
```

Chỉ cần chạy lại:

```text
Staging → EDW
```

không cần đánh vào production source lần nữa.

Staging vì vậy hỗ trợ:

- retry;
- recovery;
- tách phụ thuộc với source;
- giảm tải cho production;
- kiểm tra dữ liệu;
- debug pipeline.

---

## 6. Staging giúp tách ingestion khỏi transformation

Không có Staging:

```text
Extract + Transform + Integrate + Load
                 ↓
                EDW
```

Tất cả dính vào một pipeline.

Có Staging:

```text
             INGESTION

Source ─────────────→ Staging


           TRANSFORMATION

Staging ─────────────→ EDW
```

Hai phần độc lập hơn.

Ví dụ source đổi API:

```text
Salesforce API v1
        ↓
Salesforce API v2
```

chủ yếu phải sửa ingestion.

EDW không nhất thiết cần biết API source đã thay đổi thế nào.

Đây là **decoupling**.

---

## 7. Staging thường chứa dữ liệu như thế nào?

Source:

```text
orders
-----------------------------
order_id
user_id
created_at
status
amount
```

Staging:

```text
stg_orders
-----------------------------
order_id
user_id
created_at
status
amount
_ingested_at
_source
_batch_id
```

Staging thường khá gần Source nhưng có thể bổ sung metadata cho pipeline.

Staging thường không phải nơi Data Analyst query.

Mục tiêu của nó:

```text
landing
recovery
validation
transformation
integration preparation
```

---

## 8. Enterprise Data Warehouse — trái tim của Inmon

Phần trung tâm:

```text
┌────────────────────────────────────┐
│     Enterprise Data Warehouse      │
│                                    │
│        Normalized Modeling         │
└────────────────────────────────────┘
```

Trong Inmon:

> **Enterprise Data Warehouse — EDW — là kho dữ liệu tích hợp trung tâm của toàn doanh nghiệp.**

Điểm đặc trưng:

- dữ liệu từ nhiều nguồn được tích hợp;
- core warehouse thường được mô hình hóa normalized;
- EDW hướng tới consistency và enterprise-wide view;
- từ EDW mới tạo Data Mart phục vụ analytics.

---

## 9. Enterprise nghĩa là gì?

Không phải:

```text
Sales Warehouse
Marketing Warehouse
Finance Warehouse
```

mỗi phòng ban một sự thật riêng.

Mà là:

```text
                 Enterprise Data Warehouse

                      CUSTOMER
                         │
             ┌───────────┼───────────┐
             │           │           │
           ORDER      PAYMENT     CAMPAIGN
             │
           PRODUCT
```

Tất cả cùng sử dụng các business concepts chung.

---

## 10. Tại sao phải có EDW ở giữa?

Giả sử không có EDW.

Sales lấy customer từ CRM.

Marketing lấy customer từ Website.

Finance lấy customer từ Billing.

Ví dụ:

```text
CRM
customer_id = 123
name = Nguyen Van A

Website
user_id = U93821
email = a@gmail.com

Billing
account_id = AC552
email = a@gmail.com
```

Ba record có thể cùng là một người.

Nhưng:

```text
Sales:      1 customer
Marketing:  1 user
Finance:    1 account
```

Không ai biết chúng giống nhau.

EDW thực hiện integration:

```text
CRM customer 123 ─────┐
                      │
Website U93821 ───────┼──→ enterprise_customer_id = C00001
                      │
Billing AC552 ────────┘
```

Từ đó có một định nghĩa enterprise thống nhất.

Đây là lý do rất lớn của EDW:

> **Integration trước, analytics sau.**

---

## 11. Tại sao EDW lại Normalized?

Ví dụ dữ liệu ban đầu:

```text
order_id
customer_name
customer_city
product_name
category
supplier
quantity
price
```

Nếu nhét tất cả vào một bảng:

```text
ORDER_DATA

order_id | customer | city | product | category | supplier
-----------------------------------------------------------
1        | An       | HN   | iPhone  | Phone    | Apple
2        | An       | HN   | MacBook | Laptop   | Apple
3        | An       | HN   | iPad    | Tablet   | Apple
```

Thông tin:

```text
An
HN
Apple
```

lặp lại nhiều lần.

Normalize có thể tách thành:

```text
CUSTOMER
----------------
customer_id
name
city_id


CITY
----------------
city_id
city_name


ORDER
----------------
order_id
customer_id
order_date


ORDER_ITEM
----------------
order_id
product_id
quantity
price


PRODUCT
----------------
product_id
name
category_id


CATEGORY
----------------
category_id
name
```

---

## 12. Tại sao Inmon muốn normalize EDW?

### 12.1. Giảm redundancy

Thay vì:

```text
Apple
Apple
Apple
Apple
Apple
...
10 triệu lần
```

có thể dùng:

```text
supplier_id = 15
```

và:

```text
SUPPLIER

15 | Apple
```

### 12.2. Giữ data model gần với business entity

Doanh nghiệp có:

```text
Customer
Product
Order
Supplier
Employee
Store
```

Normalized model thể hiện rõ relationship:

```text
Customer
   │
   └── Order
         │
         └── OrderItem
                │
                └── Product
                       │
                       └── Category
```

### 12.3. Tránh mỗi analytics model tự định nghĩa dữ liệu

Customer có thể xuất hiện trong:

```text
Sales Mart
Marketing Mart
Finance Mart
Support Mart
```

Nếu không có core quản lý thống nhất:

```text
customer_country = "VN"
customer_country = "Vietnam"
customer_country = "Viet Nam"
customer_country = "704"
```

EDW có vai trò chuẩn hóa khái niệm chung.

---

## 13. Nhược điểm của Normalization

Giả sử Analyst hỏi:

> Doanh thu theo Category và Country.

Normalized EDW có thể phải join:

```sql
SELECT
    c.country,
    cat.category_name,
    SUM(oi.quantity * oi.unit_price)
FROM orders o
JOIN order_items oi
    ON o.order_id = oi.order_id
JOIN customers c
    ON o.customer_id = c.customer_id
JOIN products p
    ON oi.product_id = p.product_id
JOIN categories cat
    ON p.category_id = cat.category_id
GROUP BY
    c.country,
    cat.category_name;
```

Có thể phải đi qua:

```text
Order
 ↓
OrderItem
 ↓
Product
 ↓
Category

+

Order
 ↓
Customer
 ↓
Country
```

BI user thường không muốn hiểu hàng loạt relationship như vậy.

Đây chính là lý do Data Mart xuất hiện.

---

## 14. Data Mart

Từ EDW tạo ra nhiều Data Mart:

```text
                 EDW
            /     |      \
           /      |       \
          ▼       ▼        ▼
      Data Mart Data Mart Data Mart
```

Ví dụ:

```text
                EDW

         ┌────────┼─────────┐
         ▼        ▼         ▼

      SALES    FINANCE   MARKETING
       MART      MART      MART
```

Data Mart là:

> **Một tập dữ liệu được thiết kế cho một domain hoặc nhu cầu phân tích cụ thể.**

---

## 15. Tại sao không cho Analyst query EDW?

EDW tối ưu cho:

```text
integration
consistency
enterprise modeling
historical storage
```

không nhất thiết tối ưu cho:

```text
easy BI query
dashboard performance
business usability
```

Ví dụ EDW:

```text
Customer
   │
Address
   │
Country

Order
   │
OrderItem
   │
Product
   │
ProductCategory
   │
Category
```

Analyst có thể phải join nhiều bảng.

Data Mart có thể biến thành:

```text
             dim_customer
                   │
                   │
dim_date ─── fact_sales ─── dim_product
                   │
                   │
               dim_store
```

Đây là **Star Schema**.

---

## 16. Ví dụ Data Mart thực tế

Sales Data Mart:

```text
fact_sales
--------------------------------
date_key
customer_key
product_key
store_key
quantity
revenue
cost
profit
```

Dimensions:

```text
dim_customer
-----------------
customer_key
customer_name
city
country
segment
```

```text
dim_product
-----------------
product_key
product_name
category
brand
```

```text
dim_date
-----------------
date_key
date
month
quarter
year
```

Query:

```sql
SELECT
    p.category,
    SUM(f.revenue)
FROM fact_sales f
JOIN dim_product p
    ON f.product_key = p.product_key
GROUP BY p.category;
```

So với normalized EDW:

```text
5–10 JOIN
```

Data Mart có thể chỉ cần:

```text
1–3 JOIN
```

---

## 17. Tại sao lại có nhiều Data Mart?

Mỗi bộ phận/domain quan tâm những thứ khác nhau.

### Sales

```text
revenue
quantity sold
average order value
sales by product
sales by region
```

### Finance

```text
revenue
cost
profit
tax
accounts receivable
cash flow
```

### Marketing

```text
campaign
click
impression
conversion
CAC
ROAS
```

Nếu nhét tất cả vào một giant model:

```text
            ONE GIANT DATA MART
                    │
       ┌────────────┼──────────────┐
       │            │              │
     Sales       Finance       Marketing
```

model sẽ khó hiểu và khó maintain.

Do đó thường chia theo domain hoặc business process.

---

## 18. Tại sao Data Mart phải lấy từ EDW?

Trong Inmon, không nên làm:

```text
CRM ───────────→ Sales Mart

Website ───────→ Marketing Mart

Billing ───────→ Finance Mart
```

vì từng Mart có thể tự định nghĩa metric.

Sales:

```text
Revenue = Order Amount
```

Finance:

```text
Revenue = Paid Amount - Refund
```

Marketing:

```text
Revenue = Conversion Value
```

Kết quả:

```text
Sales:      $10M
Finance:     $9M
Marketing:  $12M
```

Inmon muốn:

```text
                       EDW
                  revenue logic
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼

      Sales         Finance       Marketing
       Mart           Mart           Mart
```

Tức là:

> **Central integration trước → departmental views sau.**

Đây là ý tưởng **Top-Down**.

---

## 19. Analytics

Từ Data Mart đi tới Analytics:

```text
Data Mart
     ↓
Analytics
```

Analytics có thể là:

```text
Power BI
Tableau
Looker
Metabase
Superset

Jupyter
Python
R

ML model
Forecasting
Recommendation
```

---

## 20. Tại sao Analytics đặt cuối?

Analytics cần dữ liệu:

```text
clean
+
consistent
+
historical
+
business-friendly
```

Nếu Dashboard tự xử lý:

```text
Power BI
    ↓
JOIN CRM
    ↓
JOIN PostgreSQL
    ↓
JOIN Excel
    ↓
clean country
    ↓
map customer
    ↓
calculate revenue
```

thì mỗi Dashboard trở thành một ETL pipeline.

Khi đó:

```text
Dashboard A
Revenue Logic A

Dashboard B
Revenue Logic B

Dashboard C
Revenue Logic C
```

Thay vào đó:

```text
Data Engineering
      ↓
Data Mart
      ↓
Business metric đã chuẩn hóa
      ↓
BI chỉ visualization
```

Responsibility rõ ràng hơn.

---

## 21. Tại sao mũi tên đi từ trái sang phải?

Luồng dữ liệu:

```text
Operational data
       ↓
Raw-ish data
       ↓
Integrated data
       ↓
Business-oriented data
       ↓
Information
```

Hay:

```text
Data                      Information

raw ───────────────────────────────→ semantic
```

Mỗi tầng bổ sung thêm business meaning.

### Source

```text
order_id = 10001
amount = 120
status = 3
```

### Staging

```text
order_id = 10001
amount = 120
status = 3
source = shop_api
```

### EDW

```text
Order 10001
customer = C001
currency = USD
status = COMPLETED
```

### Data Mart

```text
revenue = $120
country = Vietnam
category = Electronics
month = 2026-08
```

### Analytics

```text
Electronics revenue increased 18% MoM
```

Có thể nhớ:

```text
RAW DATA
   ↓
CLEAN DATA
   ↓
INTEGRATED DATA
   ↓
BUSINESS DATA
   ↓
INSIGHT
```

---

## 22. Toàn bộ pipeline bằng một ví dụ

Giả sử có order:

```json
{
  "order_id": 123,
  "user_id": 88,
  "product_id": 999,
  "quantity": 2,
  "price": 5000000
}
```

### Step 1 — Source

PostgreSQL:

```text
orders
order_items
```

Mục tiêu:

> application chạy nhanh.

### Step 2 — Staging

```text
stg_orders
stg_order_items
```

Mục tiêu:

> tách ingestion khỏi transformation và hỗ trợ retry.

### Step 3 — EDW

Normalize:

```text
Customer
    │
    └── Order
          │
          └── OrderItem
                    │
                    └── Product
```

Mục tiêu:

> tạo enterprise model thống nhất.

### Step 4 — Sales Mart

```text
                dim_customer
                     │
                     │
dim_date ───── fact_sales ───── dim_product
                     │
                     │
                 dim_store
```

Mục tiêu:

> phục vụ analytics đơn giản và hiệu quả.

### Step 5 — Analytics

```text
Revenue
────────────
Jan   █████
Feb   ███████
Mar   █████████
```

Mục tiêu:

> chuyển data thành information phục vụ quyết định.

---

## 23. Inmon và Kimball khác nhau ở đâu?

### Inmon

```text
Sources
   ↓
Staging
   ↓
       Enterprise Data Warehouse
            NORMALIZED
                ↓
        ┌───────┼────────┐
        ▼       ▼        ▼
      Sales   Finance  Marketing
       Mart    Mart      Mart
        ↓       ↓        ↓
       BI      BI        BI
```

Logic:

> **Enterprise first → department later.**

---

## 24. Kimball nhìn sơ bộ

Kimball có thể được nhìn đơn giản:

```text
Sources
      ↓
Staging
      ↓
Dimensional Models
      ↓
┌──────────┬──────────┬───────────┐
│ Sales    │ Orders   │ Inventory │
│ Fact     │ Fact     │ Fact      │
└──────────┴──────────┴───────────┘
      ↑          ↑
   shared / conformed
      dimensions
```

Kimball không yêu cầu normalized EDW core giống Inmon.

Kimball phát triển dimensional models theo business process rồi tích hợp thông qua **conformed dimensions**.

---

## 25. So sánh bản chất Inmon và Kimball

| Tiêu chí | Inmon | Kimball |
|---|---|---|
| Philosophy | Top-down | Bottom-up / incremental |
| Xây cái gì trước | Enterprise DW | Business process / dimensional model |
| Core model | Normalized | Dimensional |
| Integration | Trong EDW | Conformed dimensions/facts |
| Analytics | Qua Data Mart | Trực tiếp dimensional models |
| Model thường gặp | 3NF / ER | Star Schema |
| Time to first BI | Thường lâu hơn | Thường nhanh hơn |
| Enterprise consistency | Rất mạnh | Phụ thuộc discipline về conformed dimensions |
| Query usability | EDW khó hơn | Rất tốt |
| Complexity | Cao | Thấp hơn ban đầu |

---

## 26. Tại sao Inmon normalize rồi lại denormalize?

Nhìn qua có vẻ:

```text
Source
 ↓
EDW normalized
 ↓
Mart denormalized
```

Normalize xong lại denormalize, có vẻ thừa.

Nhưng hai model giải quyết hai mục tiêu khác nhau:

```text
                  EDW
                   │
                   │
        Optimize for integration
        consistency
        enterprise history
                   │
                   ▼
               Data Mart
                   │
                   │
        Optimize for analytics
        usability
        query patterns
                   │
                   ▼
                   BI
```

Có thể nhớ:

> **EDW được thiết kế để lưu sự thật.**  
> **Data Mart được thiết kế để hỏi sự thật.**

---

## 27. Tại sao không chỉ dùng Data Mart?

Nếu chỉ có:

```text
Sales Mart
Finance Mart
Marketing Mart
```

ban đầu rất nhanh.

Nhưng sau nhiều năm có thể thành:

```text
Customer definition:
Sales      → version A
Marketing  → version B
Finance    → version C
Support    → version D

Product:
Sales      → version A
Inventory  → version B
Finance    → version C

Revenue:
Sales      → version A
Finance    → version B
Marketing  → version C
```

Đây là data silo.

Inmon giải quyết bằng:

```text
              ENTERPRISE MODEL
                     │
             Single integrated core
                     │
       ┌─────────────┼──────────────┐
       ▼             ▼              ▼

     Sales         Finance       Marketing
```

---

## 28. Tại sao không chỉ dùng EDW?

Ngược lại:

```text
BI
 ↓
EDW normalized
```

Analyst có thể gặp:

```text
2000 tables
5000 relationships
hundreds of business entities
```

Chỉ để tính revenue có thể phải hiểu:

```text
order
order_line
product
product_version
product_category
customer
customer_address
country
payment
payment_transaction
refund
currency
exchange_rate
...
```

Không thân thiện cho analytics.

Vì thế cần Data Mart.

---

## 29. Separation of Concerns

Có thể liên hệ Software Engineering:

```text
Controller
     ↓
Service
     ↓
Repository
     ↓
Database
```

Không nhét tất cả vào Controller.

Data Architecture cũng vậy:

```text
Sources
   │
   │ operational responsibility
   ▼

Staging
   │
   │ ingestion responsibility
   ▼

EDW
   │
   │ integration responsibility
   ▼

Data Mart
   │
   │ analytics modeling responsibility
   ▼

BI
   │
   │ visualization / decision responsibility
   ▼
User
```

Mỗi layer có một trách nhiệm riêng.

---

## 30. Cách nhớ Inmon khi học Data Engineer

Đừng chỉ học:

```text
Sources → Staging → EDW → Mart → Analytics
```

Hãy học bằng 5 câu hỏi:

```text
1. Sources
   "Dữ liệu SINH RA ở đâu?"

2. Staging
   "Dữ liệu vừa lấy về ĐẶT TẠM ở đâu?"

3. EDW
   "Làm sao biến nhiều nguồn thành
    MỘT SỰ THẬT DOANH NGHIỆP?"

4. Data Mart
   "Làm sao biến sự thật đó thành
    MODEL DỄ PHÂN TÍCH?"

5. Analytics
   "Business sẽ DÙNG dữ liệu như thế nào?"
```

Lý do tồn tại:

```text
Sources
   │
   │ Vì business systems tạo ra data
   ▼
Staging
   │
   │ Vì cần decouple ingestion
   ▼
EDW
   │
   │ Vì cần integration + consistency
   ▼
Data Mart
   │
   │ Vì EDW quá phức tạp cho BI
   ▼
Analytics
       Vì data cuối cùng phải tạo ra insight
```

Câu tóm tắt Inmon:

> **Inmon xây một nguồn sự thật cấp doanh nghiệp được tích hợp và normalized trước, rồi từ nguồn sự thật đó tạo ra các Data Mart tối ưu riêng cho từng nhu cầu phân tích.**

---

# Phần II — Kimball Architecture

## 1. Toàn cảnh kiến trúc Kimball

Kimball cũng nên được học theo câu hỏi:

> **Tầng này tồn tại để giải quyết vấn đề gì? Nếu bỏ nó thì chuyện gì xảy ra?**

Điểm quan trọng:

> **Kimball không xây một Enterprise Data Warehouse normalized ở giữa giống Inmon.**

Thay vào đó, Kimball xây trực tiếp **dimensional models** phục vụ analytics rồi làm cho chúng kết nối với nhau bằng **conformed dimensions**.

```text
                         KIMBALL ARCHITECTURE

Operational world                            Analytical world
────────────────────────────────────────────────────────────────

Sources
   │
   │ Extract
   ▼
Staging / ETL
   │
   │ Clean
   │ Standardize
   │ Integrate
   ▼
Dimensional Data Warehouse
   │
   ├── Sales
   │     └── fact_sales
   │         + dim_customer
   │         + dim_product
   │         + dim_date
   │
   ├── Orders
   │     └── fact_orders
   │         + dim_customer
   │         + dim_product
   │         + dim_date
   │
   └── Inventory
         └── fact_inventory
             + dim_product
             + dim_date

            ↓

       BI / Analytics
```

So với Inmon:

```text
INMON

Source
  ↓
Staging
  ↓
Normalized EDW
  ↓
Dimensional Data Mart
  ↓
BI
```

Kimball:

```text
KIMBALL

Source
  ↓
Staging / ETL
  ↓
Dimensional Models
  ↓
BI
```

Có thể nhớ:

> **Inmon: tích hợp enterprise trước, analytics sau.**

> **Kimball: xây analytics model theo business process trước, nhưng thiết kế chúng sao cho toàn enterprise vẫn tích hợp được.**

---

## 2. Sources — nơi dữ liệu sinh ra

Ví dụ e-commerce:

```text
PostgreSQL
└── Order Service
    ├── orders
    └── order_items

MySQL
└── Customer Service
    └── customers

MongoDB
└── Product Service
    └── products

Payment Gateway
└── transactions

CRM
└── customer information

CSV / Excel
└── financial reports
```

Đây là operational systems.

Chúng phục vụ:

```text
create order
update customer
process payment
update inventory
cancel order
```

chứ không phải:

```text
doanh thu theo tháng
LTV theo customer segment
doanh số theo product category
profit theo region
```

---

## 3. Tại sao không cho BI đọc thẳng source?

Giả sử Power BI muốn tính revenue.

Nếu phải query:

```text
Order DB
+
Customer DB
+
Product MongoDB
+
Payment API
```

Dashboard đang tự biến mình thành integration system.

Có thể dẫn tới:

```text
Dashboard A
 ├── tự join dữ liệu
 ├── tự xử lý NULL
 ├── tự define revenue
 └── tự xử lý refund

Dashboard B
 ├── cũng làm y như vậy
 └── nhưng logic hơi khác

Dashboard C
 └── lại có logic khác nữa
```

Kết quả:

```text
Dashboard A revenue = $10M
Dashboard B revenue = $9.7M
Dashboard C revenue = $10.3M
```

Kimball cũng muốn tránh điều này.

---

## 4. Staging / ETL — Back Room

Kimball thường có cách nhìn:

```text
BACK ROOM
    ↓
Data Engineering / ETL

FRONT ROOM
    ↓
Dimensional model / BI
```

Staging nằm trong Back Room.

Ví dụ:

```text
PostgreSQL.orders
       ↓
stg_orders

MongoDB.products
       ↓
stg_products

CRM.customers
       ↓
stg_customers
```

Staging có thể gần source:

```text
stg_orders
--------------------------------
order_id
customer_id
created_at
status
amount
_source_system
_ingested_at
_batch_id
```

---

## 5. Tại sao Kimball vẫn cần Staging?

Kimball bỏ normalized EDW ở giữa, nhưng không có nghĩa là bỏ Staging.

```text
Source
  ↓
Staging
  ↓
Dimensional Model
```

Staging tách:

```text
Extract
```

khỏi:

```text
Transform + dimensional modeling
```

Ví dụ:

```text
Source → Staging        ✓
Staging → fact_sales    ✗
```

thì chỉ cần chạy lại:

```text
Staging → fact_sales
```

không cần query production lại.

Vì vậy:

> **Staging tồn tại để decouple ingestion khỏi transformation.**

---

## 6. Kimball bắt đầu khác Inmon từ đâu?

Inmon sẽ đi:

```text
Staging
   ↓
Customer
Order
OrderItem
Product
Category
Payment
...
```

vào normalized enterprise model.

Kimball hỏi trước:

> **Business đang muốn phân tích cái gì?**

Không bắt đầu từ:

```text
"What entities exist?"
```

mà từ:

```text
"What business process do we want to analyze?"
```

Ví dụ:

```text
Sales
Orders
Inventory
Shipment
Payment
Marketing Campaign
Customer Support
```

---

## 7. Kimball bắt đầu từ Business Process

Giả sử công ty nói:

> Tôi muốn phân tích doanh số.

Kimball chọn business process:

```text
SALES
```

rồi thiết kế dimensional model:

```text
                  dim_customer
                       │
                       │
dim_date ─────── fact_sales ─────── dim_product
                       │
                       │
                   dim_store
```

Đây là **Star Schema**.

Không cần trước đó phải xây normalized EDW hoàn chỉnh.

---

## 8. Tại sao Kimball bắt đầu từ Business Process?

Vì Data Warehouse cuối cùng phải phục vụ business questions.

Sales muốn:

```text
Doanh thu theo tháng?

Sản phẩm nào bán tốt nhất?

Country nào tạo nhiều revenue nhất?

Khách hàng VIP mua bao nhiêu?

Store nào có profit cao nhất?
```

Kimball thiết kế model trực tiếp quanh những câu hỏi đó.

Fact:

```text
fact_sales
--------------------------------
date_key
customer_key
product_key
store_key

quantity
unit_price
revenue
cost
profit
```

Dimension:

```text
dim_product
--------------------------------
product_key
product_id
product_name
brand
category
subcategory
```

Query:

```sql
SELECT
    p.category,
    SUM(f.revenue)
FROM fact_sales f
JOIN dim_product p
    ON f.product_key = p.product_key
GROUP BY p.category;
```

---

## 9. Tại sao Kimball thích Star Schema?

Normalized:

```text
Order
 ↓
OrderItem
 ↓
Product
 ↓
ProductCategory
 ↓
Category
```

Kimball có thể flatten thành:

```text
dim_product
--------------------------------
product_key
product_name
subcategory
category
brand
manufacturer
```

Query chỉ cần:

```text
fact_sales
    │
    └── dim_product
```

Kimball cố tình denormalize dimensions.

Không phải normalization sai, mà vì hai loại model tối ưu cho hai việc khác nhau:

> **Normalized model tối ưu cho consistency và transaction/integration.**  
> **Dimensional model tối ưu cho analytics.**

---

## 10. Trung tâm của Kimball: Fact Table

Star Schema thường:

```text
                  Dimensions

                dim_customer
                     │
                     │
dim_date ─────── FACT ─────── dim_product
                     │
                     │
                  dim_store
```

`fact_sales`:

```text
fact_sales
------------------------------------------------
date_key
customer_key
product_key
store_key

quantity
gross_amount
discount_amount
revenue
cost
profit
```

Fact thường chứa:

```text
Foreign keys
+
Measurements
```

Foreign keys:

```text
customer_key
product_key
date_key
```

Measurements:

```text
quantity
revenue
cost
profit
```

---

## 11. Tại sao Fact Table ở giữa?

Business analytics thường xoay quanh một event có thể đo được.

Ví dụ:

```text
Customer A
bought
Product B
on
Date C
at
Store D

quantity = 2
revenue = 10,000,000
profit = 1,500,000
```

Có thể nhìn:

```text
                 WHO?
            dim_customer
                 │
                 │
WHEN? ────── fact_sales ────── WHAT?
dim_date                       dim_product
                 │
                 │
               WHERE?
              dim_store
```

Có thể nhớ:

> **Fact = chuyện gì đã xảy ra và nó đo được bao nhiêu.**

---

## 12. Dimension Table dùng để làm gì?

Dimension trả lời:

```text
Who?
What?
Where?
When?
How?
Why?
```

Ví dụ:

```text
dim_customer
--------------------------------
customer_key
customer_id
customer_name
gender
age_group
city
country
segment
```

Fact có:

```text
customer_key = 981
```

Dimension giải thích:

```text
981
→ Nguyen Van An
→ Hanoi
→ Vietnam
→ Premium
```

Có thể nhớ:

> **Fact chứa số liệu. Dimension cung cấp ngữ cảnh cho số liệu.**

---

## 13. Tại sao không ghi hết vào Fact?

Nếu fact chứa mọi thông tin:

```text
fact_sales

order_id
customer_name
customer_city
customer_country
customer_segment
product_name
product_category
product_brand
...
revenue
```

Một customer mua 1000 lần thì:

```text
Nguyen Van A
Vietnam
Hanoi
Premium
```

lặp 1000 lần.

Một product bán 1 triệu lần:

```text
iPhone 17
Apple
Smartphone
```

lặp 1 triệu lần.

Thay vào đó:

```text
fact_sales
customer_key = 123
product_key = 456
```

rồi lookup qua dimensions.

Ngoài giảm duplication, dimension còn giúp quản lý history.

---

## 14. Grain — khái niệm cực kỳ quan trọng

Trước khi tạo Fact, phải trả lời:

> **Một row trong fact table đại diện cho cái gì?**

Đó là **grain**.

Ví dụ:

```text
1 row = 1 order
```

hoặc:

```text
1 row = 1 product trong một order
```

Hai grain khác nhau hoàn toàn.

Order 100:

```text
Order 100

iPhone     x1
AirPods    x2
MacBook    x1
```

Nếu grain:

```text
1 row = 1 order
```

thì:

```text
order_id | revenue
100      | 50M
```

Nếu grain:

```text
1 row = 1 order line
```

thì:

```text
order | product | quantity | revenue
100   | iPhone  | 1        | 20M
100   | AirPods | 2        | 10M
100   | MacBook | 1        | 20M
```

---

## 15. Tại sao Grain quan trọng?

Nếu muốn hỏi:

> Sản phẩm nào bán chạy nhất?

Fact grain:

```text
1 row = 1 order
```

sẽ khó vì một order có nhiều product.

Fact grain:

```text
1 row = 1 order line
```

thì:

```sql
SELECT
    product_key,
    SUM(quantity)
FROM fact_sales
GROUP BY product_key;
```

rất tự nhiên.

Workflow kinh điển:

```text
Business Process
        ↓
Declare Grain
        ↓
Identify Dimensions
        ↓
Identify Facts
```

---

## 16. Ví dụ thiết kế Sales Mart từ đầu

Business yêu cầu:

> Phân tích Sales.

Chọn:

```text
Business Process
= Sales
```

Declare grain:

```text
1 row =
1 product line
trong 1 completed order
```

Identify dimensions:

```text
Ai mua?
→ customer

Mua gì?
→ product

Khi nào?
→ date

Ở đâu?
→ store

Qua channel nào?
→ channel
```

Dimensions:

```text
dim_customer
dim_product
dim_date
dim_store
dim_channel
```

Identify facts:

```text
quantity
unit_price
gross_amount
discount
net_revenue
cost
profit
```

Star Schema:

```text
                         dim_customer
                              │
                              │
dim_date ─────────────── fact_sales ───────────── dim_product
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
                dim_store          dim_channel
```

---

## 17. Đây là nơi Kimball nhanh hơn Inmon

Nếu business cần Sales Dashboard gấp:

### Inmon

```text
Customer system ─┐
Order system ────┼──→ Enterprise integration
Product system ──┤
Payment system ──┤
CRM ─────────────┘

          ↓

Normalized EDW

          ↓

Sales Mart

          ↓

Dashboard
```

### Kimball

```text
Order
Customer
Product

   ↓

Sales dimensional model

   ↓

Dashboard
```

Kimball có thể giải quyết business process cần thiết trước.

Vì thế Kimball phù hợp với **incremental delivery**.

---

## 18. Vấn đề khi xây nhiều Mart

Hôm nay xây Sales.

Tháng sau Orders.

Sau nữa Inventory.

```text
Sales Mart
Orders Mart
Inventory Mart
```

Nếu mỗi team tự làm:

```text
Sales
customer table A

Orders
customer table B

Support
customer table C
```

thì lại thành data silos.

Kimball giải quyết vấn đề này bằng **Conformed Dimensions**.

---

## 19. Conformed Dimension là gì?

Giả sử có:

```text
fact_sales
fact_orders
fact_support
```

và cùng dùng:

```text
                        dim_customer
                       /     |      \
                      /      |       \
                     ▼       ▼        ▼
              fact_sales fact_orders fact_support
```

`dim_customer` là một **conformed dimension**.

Ví dụ:

```text
dim_customer
--------------------------------
customer_key
customer_id
customer_name
city
country
segment
```

Sales dùng nó.

Orders dùng nó.

Support dùng nó.

Khi đó:

```text
customer_key = 1001
```

luôn mang cùng business meaning.

---

## 20. Tại sao Conformed Dimensions quan trọng?

Nếu không conform:

Sales:

```text
country
Vietnam
USA
Japan
```

Marketing:

```text
country
VN
US
JP
```

Finance:

```text
country
704
840
392
```

Muốn phân tích cross-domain sẽ khó.

Có conformed dimension:

```text
dim_country
--------------------------------
country_key
country_code
country_name
region
```

tất cả có thể dùng:

```text
country_key = 704
country_name = Vietnam
```

Enterprise data vì vậy có thể kết nối.

---

## 21. Kimball tạo Enterprise Data Warehouse bằng cách nào?

Một hiểu nhầm phổ biến:

```text
Sales Mart
Marketing Mart
Finance Mart
```

không có nghĩa là ba database độc lập.

Kimball muốn:

```text
                    Shared Dimensions

           dim_date
              │
              │
dim_customer ─┼──────── dim_product
              │
       ┌──────┼──────────────┐
       │      │              │
       ▼      ▼              ▼
     Sales  Orders       Inventory
      Fact    Fact           Fact
```

Các dimensional models kết nối thành một architecture chung.

Enterprise DW theo Kimball có thể hiểu là:

> **Tập hợp các dimensional models tích hợp với nhau thông qua conformed dimensions và các định nghĩa nhất quán.**

Không cần normalized core.

---

## 22. Kimball Bus Architecture

Có thể hình dung bằng Bus Matrix:

| Business Process | Date | Customer | Product | Store | Employee |
|---|---:|---:|---:|---:|---:|
| Sales | ✓ | ✓ | ✓ | ✓ | |
| Orders | ✓ | ✓ | ✓ | ✓ | |
| Inventory | ✓ | | ✓ | ✓ | |
| Shipment | ✓ | ✓ | ✓ | ✓ | |
| Support | ✓ | ✓ | | | ✓ |

Rows:

```text
business processes
```

Columns:

```text
dimensions
```

Ví dụ Sales cần:

```text
Date
Customer
Product
Store
```

Inventory cần:

```text
Date
Product
Store
```

Những dimensions chung phải được conform.

---

## 23. Tại sao cần Bus Matrix?

Không có Bus Matrix:

```text
Team Sales
→ dim_product_sales

Team Inventory
→ dim_inventory_product

Team Marketing
→ product_master_marketing
```

Ba cách định nghĩa Product.

Bus Matrix giúp nhận ra:

```text
Product xuất hiện trong:
Sales
Inventory
Returns
Shipment
```

→ cần một `conformed dim_product`.

Có thể nhớ:

> **Bus Matrix là bản thiết kế để xây Data Warehouse từng phần mà không mất tính enterprise.**

---

## 24. Điểm hay của Kimball

Kimball cố cân bằng:

```text
Nhanh
+
Enterprise consistency
```

Nếu hoàn toàn bottom-up không kiểm soát:

```text
Mart A
Mart B
Mart C
```

→ silo.

Nếu chờ xây toàn enterprise model rồi mới analytics:

```text
Enterprise Model
        ↓
everything finished
        ↓
analytics
```

→ chậm.

Kimball chọn:

```text
Business Process 1
      ↓
deliver

Business Process 2
      ↓
deliver

Business Process 3
      ↓
deliver
```

nhưng tất cả đi theo Bus Architecture chung.

---

## 25. Ví dụ xây Sales rồi mở rộng Inventory

Giai đoạn đầu:

```text
Sources
   ↓
Staging
   ↓

dim_date
dim_customer
dim_product

        ↓

    fact_sales

        ↓

Sales Dashboard
```

Sau đó muốn Inventory.

Không cần xây lại:

```text
dim_date
dim_product
```

mà reuse:

```text
                     dim_product
                    /           \
                   /             \
                  ▼               ▼
           fact_sales       fact_inventory
```

Thêm `fact_inventory`.

---

## 26. Mở rộng thêm Returns

Thêm:

```text
fact_returns
```

Reuse:

```text
                  dim_customer
                       │
                       │
                  dim_product
                       │
           ┌───────────┼───────────┐
           │           │           │
           ▼           ▼           ▼
         Sales       Returns     Orders
```

Data Warehouse lớn dần theo chiều ngang.

---

## 27. Surrogate Key

Dimensions thường có warehouse key riêng.

Ví dụ:

```text
dim_customer

customer_key | customer_id | name
----------------------------------
101          | C123        | An
```

`customer_key = 101` là **surrogate key** do Data Warehouse tạo.

---

## 28. Tại sao không dùng customer_id từ source?

Giả sử:

CRM:

```text
customer_id = 123
```

E-commerce:

```text
user_id = 8899
```

Billing:

```text
account_id = AC888
```

Cùng một người.

Warehouse có thể tạo:

```text
customer_key = 10001
```

và map:

```text
CRM 123
      \
Web 8899 ─────→ customer_key 10001
      /
AC888
```

Surrogate key giúp:

- tích hợp nhiều source key;
- giữ warehouse độc lập với source;
- hỗ trợ SCD;
- xử lý lịch sử tốt hơn.

---

## 29. Slowly Changing Dimension

Giả sử ngày 01/01:

```text
Customer A
city = Hanoi
segment = Silver
```

Ngày 01/06:

```text
Customer A
city = Da Nang
segment = Gold
```

Business hỏi:

> Khi customer mua hàng vào tháng 3, lúc đó họ thuộc segment nào?

Nếu chỉ UPDATE:

```text
Silver → Gold
```

thì mất lịch sử.

Đây là bài toán Slowly Changing Dimension.

---

## 30. SCD Type 1

Type 1 overwrite giá trị cũ.

```text
Old value
Silver

UPDATE

New value
Gold
```

Kết quả:

```text
customer_key | customer | segment
----------------------------------
101          | An       | Gold
```

Không giữ history.

Phù hợp:

```text
sửa typo
fix data quality
correction
```

Ví dụ:

```text
Ho Chi Min
```

sửa thành:

```text
Ho Chi Minh
```

Không cần giữ typo.

---

## 31. SCD Type 2

Type 2 giữ lịch sử bằng row mới.

Ban đầu:

```text
customer_key | customer_id | segment | from       | to
--------------------------------------------------------
101          | C001        | Silver  | 2025-01-01 | 9999
```

Khi customer thành Gold:

```text
customer_key | customer_id | segment | from       | to
--------------------------------------------------------
101          | C001        | Silver  | 2025-01-01 | 2026-06-01
205          | C001        | Gold    | 2026-06-01 | 9999
```

Cùng:

```text
customer_id = C001
```

nhưng hai `customer_key` khác nhau.

---

## 32. Tại sao SCD Type 2 hữu ích?

Tháng 3:

```text
fact_sales

customer_key = 101
revenue = 10M
```

Tháng 7:

```text
fact_sales

customer_key = 205
revenue = 20M
```

Bây giờ hỏi:

```text
Revenue khi customer là Silver?
Revenue khi customer là Gold?
```

Kết quả:

```text
Silver → 10M
Gold   → 20M
```

Nếu chỉ lưu trạng thái hiện tại là Gold, lịch sử sẽ sai.

---

## 33. Date Dimension

Thay vì chỉ dùng:

```text
fact_sales.created_at
```

Kimball thường có:

```text
dim_date
```

Ví dụ:

```text
date_key
date
day
day_of_week
week
month
month_name
quarter
year
is_weekend
is_holiday
fiscal_year
fiscal_quarter
```

Fact:

```text
date_key = 20260813
```

BI có thể hỏi:

```text
Revenue by quarter
Revenue weekdays vs weekends
Revenue during holidays
Revenue fiscal year
```

---

## 34. Tại sao không tính thời gian hoàn toàn trong BI?

Power BI có thể tự tính:

```text
quarter
week
fiscal period
holiday
```

nhưng Tableau cũng phải tính.

Looker cũng phải tính.

Python cũng phải tính.

Khi đó có:

```text
Power BI logic
Tableau logic
Looker logic
Python logic
```

Kimball đưa common semantics xuống Dimension:

```text
                 dim_date
                    │
     ┌──────────────┼───────────────┐
     ▼              ▼               ▼

Power BI         Tableau          Python
```

Một logic dùng chung.

---

## 35. Fact không phải lúc nào cũng là Transaction

Ví dụ `fact_sales`:

```text
1 row = 1 sale line
```

đây là transactional fact.

Nhưng Kimball còn có snapshot facts.

---

## 36. Periodic Snapshot Fact

Ví dụ Inventory:

```text
Ngày 13/08

Product A
Warehouse HN
stock = 500
```

Ngày 14/08:

```text
stock = 470
```

Ta có:

```text
fact_inventory_snapshot
```

grain:

```text
1 product
×
1 warehouse
×
1 day
```

Ví dụ:

```text
date       product   warehouse   stock
13/08      A         HN          500
14/08      A         HN          470
15/08      A         HN          430
```

Đây là **Periodic Snapshot Fact**.

---

## 37. Accumulating Snapshot Fact

Ví dụ order lifecycle:

```text
Order Created
      ↓
Payment
      ↓
Packed
      ↓
Shipped
      ↓
Delivered
```

Business muốn đo:

```text
time to pay
time to pack
time to ship
time to deliver
```

Có thể tạo:

```text
fact_order_pipeline

order_key
created_date_key
paid_date_key
packed_date_key
shipped_date_key
delivered_date_key

payment_days
packing_days
shipping_days
```

Một row được update khi process tiến triển.

Đây là **Accumulating Snapshot Fact**.

---

## 38. Toàn bộ ETL Kimball

Ví dụ source:

```text
orders
order_items
customers
products
```

Pipeline:

```text
              OPERATIONAL SYSTEMS

 orders    order_items    customer    product
    \           |            |           /
     \          |            |          /
      └─────────┴─────┬──────┴─────────┘
                      │
                    Extract
                      │
                      ▼
                  STAGING
                      │
             Clean + Standardize
                      │
              ┌───────┴─────────┐
              │                 │
              ▼                 ▼
        Load Dimensions      Build Facts

        dim_customer         fact_sales
        dim_product
        dim_date
        dim_store
              │                 │
              └────────┬────────┘
                       ▼

                STAR SCHEMA

                       ↓

                 BI / Analytics
```

---

## 39. Tại sao thường load Dimension trước Fact?

Source order:

```text
order_id = 500
customer_id = C123
product_id = P88
```

Fact muốn lưu warehouse keys:

```text
customer_key = 1021
product_key = 501
```

ETL phải lookup:

```text
C123
 ↓
lookup dim_customer
 ↓
customer_key = 1021
```

và:

```text
P88
 ↓
lookup dim_product
 ↓
product_key = 501
```

sau đó mới insert Fact:

```text
fact_sales

customer_key = 1021
product_key = 501
```

Vì vậy thường:

```text
Dimensions
    ↓
Facts
```

---

## 40. Một row đi xuyên Kimball Architecture

Source Order:

```text
order_id = 100
customer_id = C001
date = 2026-08-13
```

Order item:

```text
product_id = P100
quantity = 2
price = 5M
```

Customer source:

```text
C001
Nguyen Van An
Hanoi
Vietnam
```

Product source:

```text
P100
iPhone
Phone
Apple
```

Staging:

```text
stg_order
stg_order_item
stg_customer
stg_product
```

Dimension Customer:

```text
dim_customer

customer_key = 101
customer_id = C001
city = Hanoi
country = Vietnam
```

Dimension Product:

```text
dim_product

product_key = 501
product_id = P100
product_name = iPhone
category = Phone
brand = Apple
```

Dimension Date:

```text
dim_date

date_key = 20260813
month = 8
quarter = 3
year = 2026
```

Fact:

```text
fact_sales

date_key     = 20260813
customer_key = 101
product_key  = 501

quantity     = 2
revenue      = 10M
```

---

## 41. Analytics trên Star Schema

Revenue theo country:

```sql
SELECT
    c.country,
    SUM(f.revenue)
FROM fact_sales f
JOIN dim_customer c
    ON f.customer_key = c.customer_key
GROUP BY c.country;
```

Revenue theo category:

```sql
SELECT
    p.category,
    SUM(f.revenue)
FROM fact_sales f
JOIN dim_product p
    ON f.product_key = p.product_key
GROUP BY p.category;
```

Revenue theo year:

```sql
SELECT
    d.year,
    SUM(f.revenue)
FROM fact_sales f
JOIN dim_date d
    ON f.date_key = d.date_key
GROUP BY d.year;
```

Pattern:

```text
FACT
 +
DIMENSION
```

Đây là điều Kimball cố tình thiết kế để query predictable.

---

## 42. Kimball không phải mỗi department một mart

Không nên hiểu:

```text
Sales Department
      ↓
Sales Mart

Marketing Department
      ↓
Marketing Mart

Finance Department
      ↓
Finance Mart
```

mỗi team tự xây một kiểu.

Kimball thiên về tổ chức theo:

```text
BUSINESS PROCESS
```

Ví dụ:

```text
Sales
Orders
Shipment
Inventory
Returns
Payments
```

---

## 43. Model theo Business Process

Organizational structure có thể thay đổi.

Hôm nay:

```text
Sales Team
Marketing Team
Finance Team
```

mai công ty restructure.

Nhưng các business events:

```text
customer placed order
product shipped
payment received
product returned
```

vẫn tồn tại.

Vì vậy Fact thường đặt theo process:

```text
fact_sales
fact_orders
fact_shipments
fact_inventory
fact_returns
fact_payments
```

Không nhất thiết:

```text
fact_sales_department
fact_finance_department
fact_marketing_department
```

Có thể nhớ:

> **Model the business, not the organization chart.**

---

## 44. Các Fact kết hợp với nhau thế nào?

Giả sử:

```text
fact_sales
```

dùng:

```text
dim_customer
dim_product
dim_date
```

và:

```text
fact_returns
```

cũng dùng các dimension này.

```text
                 dim_customer
                /            \
               /              \
              ▼                ▼
        fact_sales        fact_returns
              ▲                ▲
               \              /
                \            /
                 dim_product
```

Business có thể so sánh:

```text
Sales by product
vs
Returns by product
```

vì `dim_product` là conformed.

---

## 45. Ví dụ Enterprise Dimensional Warehouse lớn hơn

Sau một thời gian:

```text
                     dim_date
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
   fact_sales       fact_orders      fact_inventory
       │               │                 │
       │               │                 │
       └─────────────── ▼ ────────────────┘
                   dim_product
                       │
                       │
                  dim_customer
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
    fact_returns              fact_support
```

Không có normalized EDW core.

Nhưng vẫn là enterprise warehouse vì các model được conform.

---

## 46. Kimball và Performance

Normalized:

```text
Order
 ↓
OrderItem
 ↓
Product
 ↓
ProductCategory
 ↓
Category

+

Order
 ↓
Customer
 ↓
Address
 ↓
Country
```

Dimensional:

```text
          dim_customer
               │
               │
dim_date ─ fact_sales ─ dim_product
```

Lợi ích:

- ít join hơn;
- query predictable hơn;
- BI tool dễ hiểu hơn;
- filter / group by tự nhiên hơn;
- phù hợp analytical workload.

---

## 47. Tại sao Dimension thường wide?

Ví dụ:

```text
dim_product

product_key
product_id
name
brand
subcategory
category
department
manufacturer
package_type
...
```

Người quen normalize có thể hỏi:

> Sao không tách Category, Brand, Manufacturer?

Vì đây là presentation layer cho analytics.

Kimball muốn:

```text
fact_sales
     ↓
dim_product
```

thay vì:

```text
fact_sales
 ↓
product
 ↓
subcategory
 ↓
category
 ↓
department
```

Dimension wide là có chủ ý.

---

## 48. Denormalization trong Kimball

Source:

```text
PRODUCT
   │
CATEGORY
   │
DEPARTMENT
```

Kimball có thể flatten:

```text
dim_product
----------------------------------
product_name
category
department
brand
supplier
```

Tức là:

```text
Normalized operational model
            ↓
        flatten
            ↓
Dimensional analytical model
```

Lý do:

> **Optimize for reading rather than writing.**

---

## 49. Fact thường narrow nhưng rất tall

Dimension:

```text
dim_product

có thể 100 columns
nhưng chỉ 1 triệu rows
```

Fact:

```text
fact_sales

có thể chỉ 15–30 columns
nhưng 10 tỷ rows
```

Có thể nhớ:

```text
DIMENSION
wide
shorter

FACT
narrow
very tall
```

Vì mỗi transaction/event sinh ra fact row.

---

## 50. Descriptive data và Numeric Event

Descriptive data:

```text
Product

"iPhone 17 Pro"
"Apple"
"Smartphone"
"Premium"
```

→ Dimension.

Numeric event:

```text
quantity = 2
revenue = 50M
discount = 3M
profit = 8M
```

→ Fact.

Đây là cách phân chia rất tự nhiên của dimensional modeling.

---

## 51. Kimball có Data Mart không?

Có.

Một dimensional model cho business process có thể được coi là một Data Mart.

Sales Mart:

```text
fact_sales

dim_date
dim_product
dim_customer
dim_store
```

Inventory Mart:

```text
fact_inventory

dim_date
dim_product
dim_store
```

Điểm quan trọng:

```text
dim_date
dim_product
dim_store
```

phải conform hoặc reuse.

Không phải mỗi Mart tạo Dimension riêng một kiểu.

---

## 52. Independent Data Marts khác Kimball thế nào?

Sai kiểu:

```text
Sales Mart
├── sales_customer
├── sales_product
└── sales_date

Inventory Mart
├── inventory_product
├── inventory_store
└── inventory_date
```

Đây dễ thành silo.

Kimball:

```text
               dim_product
              /           \
             ▼             ▼
        Sales Fact    Inventory Fact

               dim_date
              /       \
             ▼         ▼
        Sales Fact Inventory Fact
```

Đây mới là enterprise dimensional architecture.

---

## 53. Quy trình Kimball từ đầu tới cuối

Một project thực tế có thể đi:

```text
Business requirement

"Phân tích Sales"

        ↓

Choose Business Process

SALES

        ↓

Declare Grain

1 row =
1 product
in 1 completed order

        ↓

Identify Dimensions

Date
Customer
Product
Store
Channel

        ↓

Identify Facts

Quantity
Revenue
Cost
Profit
Discount

        ↓

Design Star Schema

        ↓

Build ETL

        ↓

Load Dimensions

        ↓

Load Fact

        ↓

BI

        ↓

Next business process

Inventory

        ↓

Reuse conformed dimensions
```

Đây là workflow nên nhớ.

---

## 54. Kimball không cố xây mọi thứ ngay từ đầu

Doanh nghiệp có thể có nhiều process:

```text
Sales
Orders
Inventory
Shipping
Returns
Payment
Support
Marketing
...
```

Không cần đợi xây hết mới dùng.

Phase 1:

```text
Sales
↓
Dashboard
```

Phase 2:

```text
Inventory
↓
Dashboard
```

Phase 3:

```text
Returns
```

Nhưng tất cả tuân theo:

```text
Bus Matrix
+
Conformed Dimensions
```

---

## 55. Vì sao gọi Kimball là Bottom-Up?

Bởi vì bắt đầu từ một business process cụ thể rồi mở rộng:

```text
Sales
    ↓
Sales + Inventory
    ↓
Sales + Inventory + Returns
    ↓
Sales + Inventory + Returns + Shipment
    ↓
Enterprise Data Warehouse
```

Xây enterprise warehouse từ các dimensional models nhỏ hơn.

Trong khi Inmon:

```text
Enterprise Model
      ↓
Departmental Data Marts
```

Top-down.

---

## 56. Bottom-Up không có nghĩa là làm lung tung

Sai:

```text
Team A tự design
Team B tự design
Team C tự design
```

Đó là:

```text
Independent Data Marts
```

không phải Kimball architecture tốt.

Kimball đúng hơn:

```text
                 Enterprise Bus Architecture
                            │
             ┌──────────────┼───────────────┐
             │              │               │
             ▼              ▼               ▼
          Sales          Inventory       Returns
             │              │               │
             └──────── Conformed ───────────┘
                       Dimensions
```

---

## 57. So với Inmon bằng cùng một project

Sources:

```text
Customer DB
Order DB
Product DB
Payment DB
```

### Inmon

```text
Sources
   ↓
Staging
   ↓

Normalized EDW

Customer
Order
OrderLine
Product
Category
Payment
Address
Country

   ↓

Sales Mart

fact_sales
dim_customer
dim_product
dim_date

   ↓

Power BI
```

### Kimball

```text
Sources
   ↓
Staging
   ↓

Sales dimensional model

fact_sales
dim_customer
dim_product
dim_date

   ↓

Power BI
```

Kimball bỏ bước normalized enterprise core.

---

## 58. Kimball tích hợp ở đâu?

Đây là câu cực kỳ quan trọng.

### Inmon

```text
Integration
    ↓
Normalized EDW
```

### Kimball

```text
Integration
    ↓
Conformed dimensions
+
consistent facts
+
bus architecture
```

Hai bên đều giải quyết integration.

Khác nhau ở:

> **Nơi integration xảy ra trong architecture.**

---

## 59. Phép so sánh thành phố

### Inmon

Đầu tiên xây:

```text
Hệ thống quy hoạch trung tâm
Đường
Điện
Nước
Hạ tầng
```

rồi mới xây:

```text
Khu thương mại
Khu tài chính
Khu dân cư
```

Đây là top-down.

### Kimball

Bắt đầu xây:

```text
Khu thương mại
```

nhưng từ đầu đã tuân theo:

```text
chuẩn đường
chuẩn điện
chuẩn nước
```

Sau đó xây:

```text
khu tài chính
khu dân cư
```

và chúng kết nối được.

Các chuẩn chung tương đương:

```text
Conformed Dimensions
+
Bus Architecture
```

---

## 60. Tại sao Kimball được dùng nhiều?

Business thường không nói:

> Hãy xây cho tôi enterprise information architecture hoàn hảo trong ba năm.

Business thường nói:

> Tôi cần Sales Dashboard tháng này.

Kimball cho phép:

```text
Business need
     ↓
Business process
     ↓
Dimensional model
     ↓
Dashboard
```

khá nhanh.

Sau đó mở rộng dần.

Đây là lợi thế lớn của **incremental delivery**.

---

## 61. Rủi ro của Kimball

Nếu team thiếu discipline về:

```text
Conformed Dimensions
Grain
Business Definitions
Naming
Surrogate Keys
SCD
Bus Matrix
```

thì dễ thành:

```text
Sales Mart
Marketing Mart
Finance Mart
Inventory Mart
```

mỗi nơi một định nghĩa.

Khi đó cái gọi là "Kimball" thực chất là một đống **independent data marts**.

---

## 62. Cách nhớ Kimball bằng 6 câu hỏi

```text
Sources
"Data sinh ra ở đâu?"

        ↓

Staging
"Làm sao lấy data khỏi source
mà không phụ thuộc trực tiếp vào source?"

        ↓

Business Process
"Business muốn phân tích sự kiện nào?"

        ↓

Grain
"Mỗi row đại diện cho điều gì?"

        ↓

Dimensions + Facts
"Cần nhìn sự kiện theo góc nào
và cần đo cái gì?"

        ↓

Conformed Dimensions
"Làm sao model hôm nay vẫn nối được
với những model xây ngày mai?"

        ↓

Analytics
"Business sử dụng nó để ra quyết định thế nào?"
```

---

## 63. Bốn khái niệm phải nắm chắc

Nếu chỉ nhớ bốn khái niệm về Kimball:

```text
             KIMBALL

1. Business Process
       ↓
   Model theo process

2. Grain
       ↓
   Một fact row nghĩa là gì?

3. Fact + Dimension
       ↓
   Star Schema

4. Conformed Dimensions
       ↓
   Ghép các process lại thành
   Enterprise Data Warehouse
```

Nếu chưa hiểu bốn thứ này, rất dễ hiểu Kimball thành:

> "Cứ tạo Star Schema là Kimball."

Trong khi như vậy là thiếu phần architecture quan trọng nhất.

---

## 64. Tóm toàn bộ Kimball bằng một project

Giả sử e-commerce.

Business cần:

```text
Sales Analytics
```

Kiến trúc:

```text
                        SOURCES

          Order DB   Customer DB   Product DB
               \        |          /
                \       |         /
                 └──────┼────────┘
                        │
                        ▼
                     STAGING
                        │
                  Transformation
                        │
                        ▼

                   dim_customer
                         │
                         │
          dim_date ── fact_sales ── dim_product
                         │
                         │
                     dim_store
                         │
                         ▼
                      Power BI
```

Một tháng sau cần Inventory:

```text
                     dim_product
                    /           \
                   /             \
                  ▼               ▼
           fact_sales       fact_inventory
                  ▲               ▲
                   \             /
                    \           /
                      dim_date
```

Sau đó Returns:

```text
                      dim_product
                /         |          \
               ▼          ▼           ▼
           Sales      Inventory     Returns
```

Sau đó Orders:

```text
                   Conformed Dimensions

          Customer    Product    Date    Store
             │           │        │       │
             └───────────┼────────┼───────┘
                         │
              ┌──────────┼────────────┐
              ▼          ▼            ▼
            Sales      Orders      Inventory
             Fact        Fact         Fact
```

Cuối cùng:

```text
ENTERPRISE DIMENSIONAL DATA WAREHOUSE
```

Đó chính là Kimball Architecture.

---

## 65. Câu chốt phân biệt Inmon và Kimball

### INMON

```text
                     Enterprise first

Sources
  ↓
Staging
  ↓
Normalized EDW
  ↓
Dimensional Data Marts
  ↓
Analytics
```

Trong đầu đọc:

> **Đầu tiên tạo một phiên bản sự thật thống nhất cho cả công ty, rồi mới tối ưu nó cho analytics.**

### KIMBALL

```text
                     Business process first

Sources
  ↓
Staging
  ↓
Dimensional Models
  ↓
Analytics

Dimensional Models
      +
Conformed Dimensions
      ↓
Enterprise DW
```

Trong đầu đọc:

> **Đầu tiên giải quyết một business process bằng Star Schema, sau đó dùng Conformed Dimensions để các process kết nối thành một Data Warehouse toàn doanh nghiệp.**

Điểm quan trọng cuối cùng:

> **Điểm cốt lõi của Kimball không phải chỉ là Star Schema.**

Star Schema chỉ là cách model dữ liệu.

Thứ làm nên Kimball Architecture đầy đủ là:

```text
Business Process
       +
Grain
       +
Dimensional Modeling
       +
Conformed Dimensions
       +
Bus Architecture
       +
Incremental Delivery
```

---

# Bảng tổng kết cuối cùng

| Vấn đề | Inmon | Kimball |
|---|---|---|
| Điểm bắt đầu | Toàn doanh nghiệp | Một business process |
| Cách tiếp cận | Top-Down | Bottom-Up / Incremental |
| Core | Normalized EDW | Dimensional Models |
| Mô hình chính | 3NF / ER | Star Schema |
| Fact/Dimension | Chủ yếu ở Data Mart | Là trung tâm architecture |
| Integration | EDW | Conformed Dimensions + Bus Architecture |
| BI query | Chủ yếu query Data Mart | Query dimensional model trực tiếp |
| Delivery ban đầu | Thường chậm hơn | Thường nhanh hơn |
| Enterprise consistency | Rất mạnh từ core | Mạnh nếu conform tốt |
| Rủi ro chính | EDW phức tạp, time-to-value lâu | Independent marts nếu thiếu discipline |
| Tư duy | Enterprise first | Business process first |

---

# Cách nhớ ngắn nhất

## Inmon

```text
SOURCE
  ↓
STAGING
  ↓
NORMALIZED EDW
  ↓
DATA MART
  ↓
BI
```

> **Integration trước, analytics sau.**

## Kimball

```text
SOURCE
  ↓
STAGING
  ↓
BUSINESS PROCESS
  ↓
GRAIN
  ↓
FACT + DIMENSION
  ↓
STAR SCHEMA
  ↓
BI

Các model kết nối bằng:
CONFORMED DIMENSIONS
+
BUS ARCHITECTURE
```

> **Analytics theo business process trước, enterprise integration thông qua conformed dimensions.**

---

# Tài liệu tham khảo đã được nhắc trong phần học

- SSP — Inmon vs Kimball:  
  https://www.ssp.sh/brain/inmon-vs-kimball
- Kimball Group — Dimensional Modeling Techniques:  
  https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/
- Kimball Group — Data Warehouse Bus Architecture:  
  https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/kimball-data-warehouse-bus-architecture/
