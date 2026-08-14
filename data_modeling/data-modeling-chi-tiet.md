# Data Modeling — từ business đến mô hình dữ liệu thực tế

> **Ý chính cần nhớ:** Data Modeling không bắt đầu từ table. Nó bắt đầu từ business.

---

## 1. Trước khi có Data Modeling, vấn đề là gì?

Giả sử công ty bán hàng có database vận hành như sau:

```text
customers
orders
order_items
products
payments
employees
stores
promotions
shipments
```

Ở góc độ application thì hoàn toàn bình thường.

Ví dụ:

```text
customers
---------
customer_id
name
email
phone

orders
------
order_id
customer_id
order_date
status

order_items
-----------
order_id
product_id
quantity
unit_price
discount

products
--------
product_id
product_name
category_id
supplier_id
cost
```

Nhưng CEO hỏi:

> Doanh thu tháng 7 theo category, thành phố và nhóm khách hàng là bao nhiêu?

Data Analyst phải:

```text
orders
  JOIN order_items
  JOIN products
  JOIN categories
  JOIN customers
  JOIN addresses
```

Sau đó lại phải tự hiểu:

```text
Revenue = quantity × unit_price ?

Hay:
Revenue = quantity × unit_price - discount ?

Hay:
Revenue = amount_paid ?

Đơn cancelled có tính không?

Đơn refund thì xử lý thế nào?
```

Đây mới chính là vấn đề.

**Có dữ liệu không có nghĩa là dữ liệu đã sẵn sàng cho phân tích.**

Data Modeling tồn tại để biến:

```text
DATA
```

thành:

```text
DATA CÓ CẤU TRÚC
        ↓
CÓ Ý NGHĨA BUSINESS
        ↓
CÓ RELATIONSHIP RÕ RÀNG
        ↓
CÓ THỂ QUERY NHẤT QUÁN
```

---

## 2. Data Modeling thực chất là gì?

Có thể hiểu đơn giản:

> **Data Modeling = quyết định dữ liệu nào tồn tại, ý nghĩa của nó là gì, nó liên hệ với dữ liệu khác như thế nào, và cuối cùng phải lưu nó ra sao.**

Ví dụ business nói:

```text
Khách hàng mua sản phẩm.
```

Đó mới chỉ là business concept.

Ta modeling nó thành:

```text
Customer
Product
Order
```

rồi relationship:

```text
Customer
   |
   | places
   v
Order
   |
   | contains
   v
Product
```

Sau đó mới tiến tới database:

```text
dim_customer
dim_product
fact_sales
```

Và cuối cùng:

```sql
CREATE TABLE dim_customer (...);

CREATE TABLE dim_product (...);

CREATE TABLE fact_sales (...);
```

Data Modeling thường đi qua ba mức:

```text
Conceptual
    ↓
Logical
    ↓
Physical
```

---

## 3. Tại sao không nhảy thẳng vào `CREATE TABLE`?

Đây là lỗi rất phổ biến.

Business nói:

> Tôi muốn phân tích doanh thu.

Data Engineer ngay lập tức tạo:

```sql
CREATE TABLE sales (...)
```

Nhưng chưa trả lời:

```text
Một row là gì?

Một order?
Một sản phẩm trong order?
Một payment?
Một ngày?
Một customer?

Revenue nghĩa là gì?

Có tính refund không?

Có lưu lịch sử customer không?
```

Nếu những câu này chưa được trả lời thì schema rất dễ sai.

Cho nên data modeling thường đi:

```text
BUSINESS
    ↓
Conceptual Model
    ↓
Logical Model
    ↓
Physical Model
    ↓
Database / Warehouse
```

---

# 4. Ba level quan trọng: Conceptual → Logical → Physical

## 4.1. Conceptual Data Model

Conceptual trả lời:

> **Business của chúng ta có những thứ gì?**

Chưa quan tâm database.

Ví dụ hệ thống bán hàng:

```text
Customer
Product
Order
Store
Employee
Payment
```

Relationship:

```text
Customer ─── places ─── Order

Order ─── contains ─── Product

Order ─── paid by ─── Payment

Order ─── occurs at ─── Store
```

Ở đây chưa cần:

```text
VARCHAR(100)
INT
BIGINT
PRIMARY KEY
INDEX
PARTITION
```

Thậm chí có thể chưa cần column.

Conceptual model chủ yếu giúp business và technical team thống nhất **ngôn ngữ của domain**.

### Ví dụ

Business nói:

> Customer có thể đặt nhiều Order.

Conceptual:

```text
CUSTOMER
    |
    | places
    ↓
ORDER
```

Không cần biết:

```text
customer_id INT
```

hay:

```text
customer_uuid UUID
```

Chuyện đó chưa quan trọng.

---

## 4.2. Logical Data Model

Bây giờ hỏi:

> **Data cần có cấu trúc như thế nào để thể hiện đúng business?**

Ta bắt đầu xuất hiện:

- entity
- attribute
- primary key
- foreign key
- cardinality
- relationship

Ví dụ:

```text
CUSTOMER
----------------
customer_id
name
email
birth_date

        1
        |
        |
        N

ORDER
----------------
order_id
customer_id
order_date
status
```

Ta biết:

```text
Customer 1 ───── N Order
```

và:

```text
Order.customer_id
        ↓
Customer.customer_id
```

Nhưng logical model vẫn chưa nhất thiết quan tâm DBMS cụ thể.

Chưa cần quyết định:

```text
PostgreSQL?
SQL Server?
Snowflake?
BigQuery?
Fabric Warehouse?
```

---

## 4.3. Physical Data Model

Physical mới hỏi:

> **Thực sự implement nó trong database như thế nào?**

Ví dụ:

```sql
CREATE TABLE customer (
    customer_id BIGINT PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    email VARCHAR(255),
    birth_date DATE
);
```

```sql
CREATE TABLE orders (
    order_id BIGINT PRIMARY KEY,
    customer_id BIGINT NOT NULL,
    order_date TIMESTAMP NOT NULL,

    CONSTRAINT fk_customer
        FOREIGN KEY (customer_id)
        REFERENCES customer(customer_id)
);
```

Bây giờ mới quan tâm:

```text
Data type
Index
Partition
Compression
Cluster
Primary key
Foreign key
Storage
Database engine
```

Physical model phụ thuộc vào công nghệ đích.

---

# 5. Nhìn một ví dụ xuyên suốt

Business:

> Một khách hàng mua nhiều sản phẩm trong một đơn hàng.

### Conceptual

```text
Customer
   |
 Order
   |
Product
```

### Logical

```text
Customer
---------
CustomerID
Name
Email


Order
---------
OrderID
CustomerID
OrderDate


OrderItem
---------
OrderID
ProductID
Quantity
Price


Product
---------
ProductID
ProductName
Category
```

### Physical

```sql
CREATE TABLE order_item (
    order_id BIGINT,
    product_id BIGINT,
    quantity INT,
    unit_price DECIMAL(12,2)
);
```

Đó chính là:

```text
Business concept
      ↓
Data structure
      ↓
Database implementation
```

---

# 6. Data Modeling trong Data Engineering rộng hơn database schema

Modeling ngày nay không chỉ xảy ra ở database schema. Nó có thể xuất hiện ở nhiều lớp:

```text
Source database
       ↓
Data Integration
       ↓
ETL / ELT
       ↓
Data Warehouse
       ↓
Data Lake / Lakehouse
       ↓
BI Semantic Layer
       ↓
ML / AI Features
```

Ví dụ:

```text
MySQL
customers + orders + products
        ↓
Bronze
raw_customer
raw_order
raw_product
        ↓
Silver
clean_customer
clean_order
clean_product
        ↓
Gold
dim_customer
dim_product
fact_sales
        ↓
Power BI
Revenue
Profit
Orders
AOV
```

Data Modeling đặc biệt quan trọng ở Gold / Analytics layer vì đây là nơi dữ liệu bắt đầu mang **business meaning rõ ràng**.

---

# 7. Relational Modeling và Dimensional Modeling khác nhau ở đâu?

## Relational Modeling

Thường hướng tới:

```text
Consistency
Integrity
Reduce redundancy
Transaction
```

Ví dụ application database:

```text
Customer
   ↓
CustomerAddress
   ↓
Address
   ↓
City
   ↓
Province
   ↓
Country
```

Rất normalized.

Ưu điểm:

```text
Ít duplicate
Update dễ
Data integrity cao
```

Nhưng analytics query:

```sql
SELECT ...
FROM orders
JOIN customers
JOIN customer_address
JOIN address
JOIN city
JOIN province
JOIN country
...
```

có thể trở nên phức tạp.

---

# 8. Dimensional Modeling xuất hiện để giải quyết bài toán Analytics

Thay vì:

```text
database optimized for WRITE
```

ta muốn:

```text
database optimized for ANALYSIS
```

Ví dụ:

```text
              DimDate
                 |
                 |
DimCustomer ─ FactSales ─ DimProduct
                 |
                 |
              DimStore
```

Đây là:

# Star Schema

Star Schema là cấu trúc rất phổ biến trong analytical warehouse, gồm **fact tables và dimension tables**.

---

# 9. Fact là gì?

Một cách cực dễ nhớ:

> **Fact = cái gì đã xảy ra và có thể đo được.**

Ví dụ:

```text
Customer Nguyễn Văn A
mua
iPhone
2 cái
giá 25 triệu/cái
ngày 14/08
ở Store HCM
```

Phần đo được:

```text
quantity = 2
unit_price = 25,000,000
sales_amount = 50,000,000
discount = 2,000,000
profit = ...
```

Đó là Facts.

Ví dụ:

```text
FactSales
--------------------------------
DateKey
CustomerKey
ProductKey
StoreKey
OrderNumber

Quantity
UnitPrice
DiscountAmount
SalesAmount
CostAmount
ProfitAmount
```

---

# 10. Dimension là gì?

Dimension trả lời:

> **Fact xảy ra với ai, cái gì, ở đâu, khi nào, thuộc loại nào?**

Ví dụ:

```text
WHO?
→ Customer

WHAT?
→ Product

WHERE?
→ Store

WHEN?
→ Date
```

Dimension chứa descriptive information giúp filter, group, slice dữ liệu.

Ví dụ:

```text
DimCustomer
-----------------------
CustomerKey
CustomerID
FullName
Gender
Education
City
Country
CustomerSegment
```

```text
DimProduct
-----------------------
ProductKey
ProductID
ProductName
Brand
Category
Subcategory
```

---

# 11. Liên hệ FactInternetSales và DimCustomer

Ví dụ AdventureWorks:

```text
FactInternetSales
       |
       | CustomerKey
       ↓
DimCustomer
```

Fact:

```text
SalesAmount
OrderQuantity
TaxAmt
Freight
```

Dimension:

```text
FirstName
LastName
Gender
EnglishEducation
Occupation
```

Do đó câu:

> Doanh thu theo trình độ học vấn khách hàng

trở thành:

```sql
SELECT
    c.EnglishEducation,
    SUM(f.SalesAmount)
FROM FactInternetSales f
JOIN DimCustomer c
    ON f.CustomerKey = c.CustomerKey
GROUP BY c.EnglishEducation;
```

Business đang hỏi:

```text
MEASURE
Revenue
   ↓
GROUP BY
Education
```

Hay nói theo dimensional modeling:

```text
FACT
SalesAmount
   ↓
DIMENSION ATTRIBUTE
EnglishEducation
```

---

# 12. Grain — phần quan trọng nhất

**Grain = một row trong fact table đại diện cho cái gì?**

Ví dụ:

```text
FactSales
```

Một row có thể là:

### Grain A

```text
1 Order
```

hoặc:

### Grain B

```text
1 Product trong 1 Order
```

hoặc:

### Grain C

```text
1 Product × 1 Store × 1 Day
```

Ba thứ này **hoàn toàn khác nhau**.

---

# 13. Vì sao grain sai rất nguy hiểm?

Order:

```text
Order #1001

iPhone      2 cái
AirPods     1 cái
Case        2 cái
```

Nếu grain:

```text
one row = one order line
```

Fact:

```text
Order | Product | Quantity
1001  | iPhone  | 2
1001  | AirPods | 1
1001  | Case    | 2
```

Hoàn toàn hợp lý.

Nhưng nếu bạn thêm:

```text
OrderTotal = 60 triệu
```

vào từng row:

```text
1001 | iPhone  | 2 | 60m
1001 | AirPods | 1 | 60m
1001 | Case    | 2 | 60m
```

Rồi:

```sql
SUM(OrderTotal)
```

ra:

```text
180 triệu
```

trong khi order thực tế:

```text
60 triệu
```

Đó là **grain mismatch**.

---

# 14. Atomic Grain

Atomic grain là mức chi tiết thấp nhất mà business process ghi nhận.

Ví dụ:

```text
Sales transaction:

1 row =
1 product
trong 1 order
tại 1 thời điểm
```

Atomic grain tốt vì sau này có thể:

```text
SUM theo ngày
SUM theo tháng
SUM theo năm
SUM theo product
SUM theo category
SUM theo customer
```

Từ dữ liệu atomic, ta có thể tổng hợp lên nhiều mức khác nhau.

---

# 15. Quy trình Dimensional Modeling

Một cách rất hữu ích:

```text
1. Chọn business process
        ↓
2. Declare grain
        ↓
3. Identify dimensions
        ↓
4. Identify facts
```

## Bước 1 — Business Process

Không phải:

```text
Tôi muốn tạo bảng sales.
```

Mà là:

```text
Business process nào đang xảy ra?
```

Ví dụ:

```text
Sales
Payment
Shipment
Inventory
Customer Support
Website Session
Hotel Booking
```

## Bước 2 — Grain

Ví dụ:

```text
One row represents:
one product sold
in one order
at one store.
```

## Bước 3 — Dimensions

Hỏi:

```text
Who?
What?
When?
Where?
How?
Why?
```

→

```text
Customer
Product
Date
Store
Promotion
Salesperson
```

## Bước 4 — Facts

Hỏi:

```text
Ta đo cái gì?
```

→

```text
Quantity
SalesAmount
DiscountAmount
CostAmount
ProfitAmount
```

Kết quả:

```text
                 DimDate
                    |
                    |
DimCustomer ─── FactSales ─── DimProduct
                    |
           ┌────────┼────────┐
           |        |        |
        DimStore DimPromo DimEmployee
```

---

# 16. Ba loại Fact Table quan trọng

## 16.1. Transaction Fact Table

Một row cho mỗi event.

Ví dụ:

```text
Date    Order Product Qty Revenue
---------------------------------
14/8    1001  A       2    200
14/8    1001  B       1    500
```

Phù hợp với:

```text
purchase
payment
click
transaction
```

---

## 16.2. Periodic Snapshot Fact

Không lưu event mà lưu **trạng thái theo một khoảng thời gian cố định**.

Ví dụ inventory cuối mỗi ngày:

```text
Date       Product Store Inventory
----------------------------------
14/08      A       HCM   100
15/08      A       HCM    83
16/08      A       HCM    65
```

Một row:

```text
1 Product
× 1 Store
× 1 Day
```

---

## 16.3. Accumulating Snapshot Fact

Theo dõi một process có nhiều milestone.

Ví dụ shipment:

```text
OrderID
OrderDate
PaymentDate
PackDate
ShipDate
DeliveryDate
```

Một row sẽ được update dần:

```text
Order created
     ↓
Paid
     ↓
Packed
     ↓
Shipped
     ↓
Delivered
```

Phù hợp với:

```text
order lifecycle
loan application
insurance claim
shipment
hotel booking lifecycle
```

---

# 17. Fact theo khả năng SUM

## Additive Fact

SUM được trên tất cả dimensions.

Ví dụ:

```text
SalesAmount
Quantity
Cost
Profit
```

```sql
SUM(SalesAmount)
```

theo:

```text
time
customer
product
store
```

đều có ý nghĩa.

---

## Semi-additive Fact

SUM được theo một số dimension nhưng không phải tất cả.

Ví dụ:

```text
BankBalance
InventoryLevel
```

Store A:

```text
Product A = 20
Product B = 10
```

Có thể:

```text
SUM Inventory = 30
```

Nhưng:

```text
Inventory 14/8 = 20
Inventory 15/8 = 18
```

không thể nói:

```text
20 + 18 = 38 inventory
```

vì đó là snapshot của hai thời điểm.

---

## Non-additive Fact

Không nên SUM.

Ví dụ:

```text
Percentage
Ratio
Average
Margin %
```

Ví dụ:

```text
Store A margin = 10%
Store B margin = 30%
```

Không thể:

```text
10% + 30% = 40%
```

---

# 18. Các loại Dimension

## 18.1. Conformed Dimension

Một dimension dùng chung cho nhiều fact tables.

Ví dụ:

```text
             DimCustomer
               /      \
              /        \
      FactSales      FactSupport
```

Cùng một khái niệm:

```text
Customer
```

được sử dụng xuyên nhiều business process.

Điều này rất quan trọng để enterprise analytics nhất quán.

---

## 18.2. Role-playing Dimension

Cùng một dimension nhưng đóng nhiều vai.

Ví dụ:

```text
DimDate
```

FactOrder có:

```text
OrderDateKey
ShipDateKey
DeliveryDateKey
```

Ba foreign keys đều trỏ về:

```text
DimDate
```

DimDate lúc này đóng vai:

```text
Order Date
Ship Date
Delivery Date
```

---

## 18.3. Degenerate Dimension

Có identifier mang ý nghĩa business nhưng không cần dimension table riêng.

Ví dụ:

```text
OrderNumber
InvoiceNumber
TicketNumber
```

Đưa thẳng vào fact:

```text
FactSales
----------------
OrderNumber
DateKey
ProductKey
CustomerKey
SalesAmount
```

Không nhất thiết phải tạo:

```text
DimOrder
```

nếu nó chỉ chứa `OrderNumber`.

---

## 18.4. Junk Dimension

Nhiều flag nhỏ:

```text
is_gift
is_online
is_member
payment_type
delivery_type
```

Thay vì để một loạt attribute nhỏ trong Fact:

```text
FactSales
---------
is_gift
is_online
is_member
...
```

có thể gom:

```text
DimTransactionProfile
---------------------
ProfileKey
IsGift
IsOnline
IsMember
DeliveryType
```

---

# 19. Surrogate Key — tại sao Dimension thường có Key riêng?

Source:

```text
CustomerID = C001
```

Warehouse thường thêm:

```text
CustomerKey = 128731
```

Ví dụ:

```text
DimCustomer

CustomerKey | CustomerID | Name
--------------------------------
1           | C001       | An
2           | C002       | Bình
```

Trong đó:

```text
CustomerID
```

= **Natural / Business Key**

Còn:

```text
CustomerKey
```

= **Surrogate Key**

Fact thường dùng:

```text
CustomerKey
```

chứ không dùng business key trực tiếp.

---

# 20. Vì sao cần Surrogate Key?

Một lý do rất quan trọng là **history**.

Customer:

```text
CustomerID = C001
City = HCM
```

Năm sau:

```text
CustomerID = C001
City = Đà Nẵng
```

Nếu overwrite:

```text
C001 | Đà Nẵng
```

thì historical report có thể tưởng:

> Customer lúc mua hàng năm ngoái cũng ở Đà Nẵng.

Trong khi thực tế lúc đó ở HCM.

---

# 21. Slowly Changing Dimension — SCD

Đây là bài toán:

> Dimension thay đổi theo thời gian thì làm gì?

## SCD Type 1

Overwrite.

Before:

```text
CustomerKey | Name | City
1           | An   | HCM
```

After:

```text
1 | An | Đà Nẵng
```

Không giữ history.

Dùng khi history không quan trọng:

```text
fix typo
incorrect email formatting
data cleaning
```

---

## SCD Type 2

Tạo row mới.

```text
CustomerKey | CustomerID | City      | From | To   | Current
----------------------------------------------------------------
101         | C001       | HCM       | 2024 | 2025 | false
205         | C001       | Đà Nẵng   | 2025 | null | true
```

Fact năm 2024:

```text
CustomerKey = 101
```

Fact năm 2026:

```text
CustomerKey = 205
```

Nhờ vậy historical report đúng.

---

# 22. Normalization — tại sao OLTP thích normalize?

Giả sử bảng:

```text
Order
------------------------------------------------------
OrderID
CustomerName
CustomerCity
CustomerCountry
ProductName
ProductCategory
SupplierName
```

Customer Nguyễn Văn A mua 10,000 lần:

```text
Nguyễn Văn A | HCM | Vietnam
Nguyễn Văn A | HCM | Vietnam
Nguyễn Văn A | HCM | Vietnam
...
```

Duplicate rất nhiều.

Ta normalize:

```text
Customer
Order
OrderItem
Product
Category
Supplier
```

Sau đó dùng FK.

Mục tiêu chủ yếu:

```text
Reduce redundancy
Improve integrity
Avoid update anomaly
```

---

# 23. Vì sao Warehouse lại hay Denormalize?

Ví dụ OLTP:

```text
Product
   ↓
Subcategory
   ↓
Category
```

Warehouse có thể:

```text
DimProduct
-----------------
Product
Subcategory
Category
Brand
Color
Size
```

Tức là cố tình duplicate.

Vì analytics ưu tiên:

```text
Read
Aggregation
Simple query
Understandability
```

hơn:

```text
INSERT
UPDATE
DELETE
```

---

# 24. Star Schema vs Snowflake Schema

## Star Schema

```text
                 DimDate
                    |
                    |
DimCustomer ─── FactSales ─── DimProduct
                    |
                 DimStore
```

DimProduct:

```text
Product
Category
Subcategory
Brand
```

## Snowflake Schema

Dimension tiếp tục normalize:

```text
FactSales
    |
 DimProduct
    |
DimSubcategory
    |
DimCategory
```

Snowflake giảm redundancy hơn nhưng query/model trở nên phức tạp hơn.

Star Schema thường dễ hiểu, dễ query và rất phổ biến trong BI.

---

# 25. Cardinality cũng là một phần của Data Modeling

Các relationship:

```text
1 : 1
1 : N
N : N
```

Ví dụ:

```text
Customer 1 : N Order
```

Một customer:

```text
Nguyễn Văn A
```

có:

```text
Order 1
Order 2
Order 3
```

---

## Many-to-many

Ví dụ:

```text
Student N : N Course
```

Không làm trực tiếp:

```text
Student ←→ Course
```

mà thường dùng bridge:

```text
Student
   |
   1
   |
   N
Enrollment
   N
   |
   1
   |
Course
```

Tương tự:

```text
Order
   ↓
OrderItem
   ↓
Product
```

---

# 26. Data Modeling không đồng nghĩa với Dimensional Modeling

```text
DATA MODELING
```

là umbrella concept.

Bên dưới có thể có:

```text
Relational Modeling
Dimensional Modeling
Data Vault
Anchor Modeling
Graph Modeling
Entity-Centric Modeling
One Big Table
...
```

Có thể hình dung:

```text
Data Modeling
      |
      ├── Conceptual / Logical / Physical
      |
      ├── Relational
      |
      ├── Dimensional
      |
      ├── Data Vault
      |
      └── ...
```

---

# 27. Inmon/Kimball nằm ở đâu?

Có thể xếp như sau:

```text
                 DATA MODELING
                      |
          DATA WAREHOUSE MODELING
                /             \
             INMON          KIMBALL
               |               |
              3NF        DIMENSIONAL MODEL
                               |
                       Fact + Dimension
                               |
                          Star Schema
```

Kimball rất gắn với dimensional modeling và cách tổ chức warehouse theo business process.

---

# 28. Medallion nằm ở đâu?

Medallion không phải cùng loại khái niệm với Dimensional Modeling.

Medallion trả lời:

> **Data đi qua các layer như thế nào?**

```text
Bronze
 ↓
Silver
 ↓
Gold
```

Data Modeling trả lời:

> **Data bên trong các layer nên được cấu trúc như thế nào?**

Ví dụ:

```text
Bronze
raw_orders
raw_customer
raw_product

       ↓

Silver
orders_clean
customers_clean
products_clean

       ↓

Gold

       DimCustomer
            |
DimDate─ FactSales ─DimProduct
            |
         DimStore
```

Do đó:

```text
Medallion
= data flow / architecture pattern

Dimensional Modeling
= data structure / modeling technique
```

---

# 29. Data Mesh thì sao?

Data Mesh hỏi:

> Ai sở hữu dữ liệu? Dữ liệu được tổ chức theo domain thế nào?

Trong khi Data Modeling hỏi:

> Data Product của domain đó được cấu trúc ra sao?

Ví dụ:

```text
Sales Domain
    |
    └── Sales Data Product
             |
             ├── DimCustomer
             ├── DimProduct
             └── FactSales
```

Hai khái niệm nằm ở level khác nhau.

---

# 30. Platform như Microsoft Fabric nằm ở đâu?

Fabric là **platform**.

Bạn có thể xây:

```text
Data Source
    ↓
Data Factory
    ↓
OneLake
    ↓
Lakehouse / Warehouse
    ↓
Dimensional Model
    ↓
Semantic Model
    ↓
Power BI
```

Có thể nhớ:

```text
Fabric
= dùng cái gì để xây

Medallion
= data đi qua layer nào

Data Modeling
= data được cấu trúc ra sao

Kimball
= cách tiếp cận DW

Star Schema
= hình dạng dimensional model
```

---

# 31. Ví dụ hoàn chỉnh: E-commerce

Source OLTP:

```text
customers
orders
order_items
products
categories
stores
payments
shipments
```

Business muốn:

```text
Revenue theo:
- Month
- Product
- Category
- Customer
- City
- Store
```

## Step 1: Business process

```text
Sales
```

## Step 2: Grain

Quyết định:

> Một row FactSales đại diện cho **một product line trong một completed order**.

Viết thành:

```text
one row =
one product
× one order
```

## Step 3: Dimensions

```text
DimDate
DimCustomer
DimProduct
DimStore
DimPromotion
```

## Step 4: Facts

```text
Quantity
UnitPrice
GrossAmount
DiscountAmount
NetAmount
CostAmount
ProfitAmount
```

## Kết quả

```text
                        DimDate
                           |
                           |
                           |
DimCustomer ────────── FactSales ────────── DimProduct
                           |
                           |
                      DimStore
                           |
                     DimPromotion
```

Fact:

```text
FactSales
---------------------------------
DateKey             FK
CustomerKey         FK
ProductKey          FK
StoreKey            FK
PromotionKey        FK

OrderNumber

Quantity
UnitPrice
GrossAmount
DiscountAmount
NetAmount
CostAmount
ProfitAmount
```

Dimension:

```text
DimProduct
----------------------
ProductKey
ProductID
ProductName
Subcategory
Category
Brand
Color
Size
```

---

# 32. Query trên Dimensional Model

### Doanh thu theo category

```sql
SELECT
    p.Category,
    SUM(f.NetAmount) AS Revenue
FROM FactSales f
JOIN DimProduct p
    ON f.ProductKey = p.ProductKey
GROUP BY p.Category;
```

### Doanh thu theo education

```sql
SELECT
    c.Education,
    SUM(f.NetAmount) AS Revenue
FROM FactSales f
JOIN DimCustomer c
    ON f.CustomerKey = c.CustomerKey
GROUP BY c.Education;
```

### Doanh thu theo tháng

```sql
SELECT
    d.Year,
    d.Month,
    SUM(f.NetAmount) AS Revenue
FROM FactSales f
JOIN DimDate d
    ON f.DateKey = d.DateKey
GROUP BY
    d.Year,
    d.Month;
```

Đó là sức mạnh của dimensional modeling:

```text
Measure
    ↓
Fact

Context / Filter / Group
    ↓
Dimension
```

---

# 33. Cách dịch Business Requirement thành Fact/Dimension

Ví dụ:

> Tổng doanh thu của khách hàng trình độ High School theo năm.

Tách:

```text
"Tổng doanh thu"
        ↓
FACT
SalesAmount

"khách hàng"
        ↓
DIMENSION
Customer

"High School"
        ↓
DIMENSION ATTRIBUTE
Education

"theo năm"
        ↓
DIMENSION
Date
```

Rồi schema:

```text
DimCustomer
      |
      |
FactSales
      |
      |
DimDate
```

Đây là cách đọc **business requirement thành data model**.

---

# 34. Một Data Engineer khi modeling thực chất đang ra quyết định gì?

Không chỉ là:

```text
VARCHAR hay INT?
```

Mà là:

```text
Business process là gì?

Một row đại diện cho gì?

Metric nào cần lưu?

Dimension nào cần có?

History cần giữ không?

Natural hay surrogate key?

Relationship 1:N hay N:N?

Data có được SUM không?

Có cần snapshot không?

Normalize hay denormalize?

Query pattern là gì?

Consumer cần drill-down đến đâu?
```

---

# 35. Sai lầm thường gặp: bắt đầu từ column thay vì business

Sai:

```text
Tôi có:
customer_id
product_id
amount

→ tạo FactSales.
```

Đúng hơn:

```text
Business Process
     ↓
Sales

Grain
     ↓
One product sold per order line

Dimensions
     ↓
Customer
Product
Date
Store

Facts
     ↓
Quantity
SalesAmount
Discount
Cost
```

Sau đó mới mapping source columns.

---

# 36. Mental model rất dễ nhớ

Hãy coi Data Warehouse như một câu:

```text
CUSTOMER
bought
PRODUCT
at
STORE
on
DATE
for
$100
```

Trong đó:

```text
CUSTOMER
PRODUCT
STORE
DATE
```

là:

**Dimensions**

Còn:

```text
bought / transaction
```

là:

**Business Process**

Và:

```text
$100
quantity = 2
discount = 10
```

là:

**Facts**

Tất cả được ghi lại ở một mức chi tiết cụ thể:

**Grain**

---

# 37. Công thức tư duy cực ngắn

Khi design data model, hỏi theo thứ tự:

```text
1. WHAT HAPPENED?
   → Business Process

2. WHAT DOES ONE ROW MEAN?
   → Grain

3. WHO / WHAT / WHEN / WHERE / HOW?
   → Dimensions

4. WHAT CAN WE MEASURE?
   → Facts

5. HOW DO THESE THINGS RELATE?
   → Relationships / Cardinality

6. DO WE NEED HISTORY?
   → SCD

7. HOW SHOULD IT BE STORED?
   → Physical Model
```

Đây là một framework rất tốt để giải hầu hết bài Data Warehouse.

---

# 38. Ghép toàn bộ bức tranh

```text
                         BUSINESS
                            │
                            ▼
                     DATA MODELING
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
         Conceptual      Logical       Physical
              │             │             │
              │             │             ▼
              │             │       Tables / Columns
              │             │       Keys / Indexes
              │             │       Partitions
              │             │
              ▼             ▼
        Business Entity   Relationships
                            │
                            ▼
                  DIMENSIONAL MODELING
                            │
                   ┌────────┴────────┐
                   ▼                 ▼
               DIMENSION            FACT
                   │                 │
          Who / What / When      Measurements
          Where / How           Transactions
                   │                 │
                   └────────┬────────┘
                            ▼
                       STAR SCHEMA
                            │
                            ▼
                   ANALYTICS / BI
```

Còn architecture:

```text
Sources
   ↓
Bronze
   ↓
Silver
   ↓
Gold
   ↓
Fact + Dimension
   ↓
Semantic Model
   ↓
Power BI
```

---

# 39. Bảng phân biệt các khái niệm

| Khái niệm | Nó trả lời câu hỏi gì? |
|---|---|
| **Data Modeling** | Dữ liệu nên được cấu trúc như thế nào? |
| **Conceptual Model** | Business có những entity/concept gì? |
| **Logical Model** | Entity, attribute và relationship ra sao? |
| **Physical Model** | Implement thực tế trong DB thế nào? |
| **Relational Modeling** | Tổ chức relational data thế nào? |
| **Dimensional Modeling** | Tổ chức dữ liệu analytics thành facts/dimensions thế nào? |
| **Fact** | Ta đang đo cái gì? |
| **Dimension** | Ta phân tích fact theo góc nhìn nào? |
| **Grain** | Một row đại diện cho cái gì? |
| **Star Schema** | Fact ở giữa, dimensions xung quanh |
| **Normalization** | Tách dữ liệu để giảm redundancy |
| **Denormalization** | Ghép/bổ sung redundancy để query thuận tiện hơn |
| **Kimball** | DW bottom-up theo business process, dùng dimensional modeling |
| **Inmon** | DW top-down, enterprise-oriented |
| **Medallion** | Data đi qua Bronze → Silver → Gold như thế nào |
| **Data Mesh** | Domain nào sở hữu và chịu trách nhiệm dữ liệu |
| **Fabric** | Platform để triển khai data workloads |

---

# 40. Cách trả lời khi đi học/phỏng vấn

Nếu người ta hỏi:

> **What is Data Modeling?**

Đừng chỉ nói:

> Data modeling is the process of creating tables and relationships.

Cách hiểu tốt hơn:

> Data Modeling là quá trình biến business concepts và business processes thành một cấu trúc dữ liệu rõ ràng, xác định entities, attributes, relationships, grain, facts, dimensions và constraints; sau đó triển khai cấu trúc đó thành physical tables phù hợp với workload.

Nếu là Analytics/Data Warehouse, hãy nghĩ ngay:

```text
Business Process
       ↓
Grain
       ↓
Dimensions
       ↓
Facts
       ↓
Star Schema
```

---

# 41. Kết luận

Trong toàn bộ Data Modeling, câu quan trọng nhất là:

> **Đừng hỏi “tôi cần tạo những table nào?” trước. Hãy hỏi “business process nào đang xảy ra, và một row của tôi đại diện cho cái gì?”**

Khi **grain đúng**, fact và dimension thường bắt đầu trở nên tự nhiên.

Khi **grain sai**, dù SQL có viết đẹp đến đâu thì kết quả business vẫn có thể sai.

---

## Nguồn tham khảo

- Simon Späti — Data Modeling: https://www.ssp.sh/brain/data-modeling/
- Simon Späti — Dimensional Modeling: https://www.ssp.sh/brain/dimensional-modeling/
- Simon Späti — Fact Table: https://www.ssp.sh/brain/fact-table/
- Simon Späti — Dimensions: https://www.ssp.sh/brain/dimensions/
- Simon Späti — Granularity: https://www.ssp.sh/brain/granularity/
- Simon Späti — Logical Data Model: https://www.ssp.sh/brain/logical-data-model
- Simon Späti — Physical Data Model: https://www.ssp.sh/brain/physical-data-model
- IBM — Data Modeling overview
- Microsoft Learn — Dimensional modeling / Star schema guidance
