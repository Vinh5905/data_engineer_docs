# Granularity và Cardinality trong Data Modeling

## 1. Granularity là gì?

**Granularity** là **mức độ chi tiết của dữ liệu**.

Trong Dimensional Modeling, ta thường dùng khái niệm **Grain** để mô tả chính xác:

> **Một dòng (row) trong bảng đang đại diện cho điều gì?**

Ví dụ:

- Một dòng = một đơn hàng.
- Một dòng = một sản phẩm trong một đơn hàng.
- Một dòng = doanh thu của một sản phẩm trong một ngày.
- Một dòng = tồn kho của một sản phẩm tại một cửa hàng vào cuối ngày.

Có thể hiểu ngắn gọn:

```text
Granularity = Mức độ chi tiết của dữ liệu

Grain = Business meaning của một row
```

---

# 2. Tại sao cần Granularity?

Giả sử một đơn hàng:

```text
Order #1001
Customer: Nguyễn Văn A

Product          Quantity     Price
-----------------------------------
iPhone 15            1        20tr
AirPods              2         4tr
Case                  1       500k
```

Ta muốn xây dựng bảng:

```text
FactSales
```

Nhưng có một vấn đề:

> Một row trong `FactSales` sẽ đại diện cho cái gì?

Có nhiều cách thiết kế.

---

## 2.1. Một row = một Order

```text
OrderID    Customer    TotalAmount
1001       A           28.5tr
```

Grain:

> Một dòng đại diện cho một đơn hàng.

---

## 2.2. Một row = một Product trong Order

```text
OrderID   Product    Qty    Amount
1001      iPhone      1     20tr
1001      AirPods     2      8tr
1001      Case        1    0.5tr
```

Grain:

> Một dòng đại diện cho một dòng sản phẩm trong một đơn hàng.

---

## 2.3. Một row = doanh thu một ngày

```text
Date          Revenue
2026-08-01    5 billion
2026-08-02    6 billion
```

Grain:

> Một dòng đại diện cho doanh thu của một ngày.

---

## 2.4. Một row = doanh thu mỗi Product mỗi ngày

```text
Date         Product     Revenue
2026-08-01   iPhone      2 billion
2026-08-01   AirPods     500 million
```

Grain:

> Một dòng đại diện cho doanh thu của một sản phẩm trong một ngày.

---

Tất cả các thiết kế trên đều có thể đúng.

Nhưng chúng phục vụ **những câu hỏi business khác nhau**.

Đó chính là lý do cần xác định Grain trước khi thiết kế Fact Table.

---

# 3. Grain chính xác là gì?

Khi thiết kế một bảng Fact, hãy hoàn thành câu:

> **One row represents ________.**

Ví dụ:

```text
One row represents one product line in a sales transaction.
```

Hoặc:

```text
One row represents the inventory level
of one product
at one store
at the end of one day.
```

Hoặc:

```text
One row represents one customer's monthly account balance.
```

Đây gọi là:

```text
Grain Declaration
```

Nếu chưa trả lời được:

```text
One row represents WHAT?
```

thì chưa nên thiết kế dimensions và measures.

---

# 4. Granularity giống mức Zoom

Có thể hình dung Granularity giống Google Maps.

```text
Vietnam
   ↓
Ho Chi Minh City
   ↓
District 1
   ↓
Ward
   ↓
Street
   ↓
House
```

Càng đi xuống:

```text
Granularity càng fine
→ dữ liệu càng chi tiết
→ thường có nhiều rows hơn
```

Càng đi lên:

```text
Granularity càng coarse
→ dữ liệu càng tổng hợp
→ thường có ít rows hơn
```

Trong dữ liệu thời gian:

```text
Year
 ↓
Quarter
 ↓
Month
 ↓
Day
 ↓
Hour
 ↓
Minute
 ↓
Second
 ↓
Event
```

---

# 5. Fine Grain và Coarse Grain

## 5.1. Fine Grain

Fine-grained data là dữ liệu rất chi tiết.

Ví dụ:

```text
FactSales
```

| Order | Product | Customer | Date | Qty | Amount |
|---|---|---|---|---:|---:|
| O1 | P1 | C1 | 01/08 | 1 | 100 |
| O1 | P2 | C1 | 01/08 | 2 | 200 |
| O2 | P1 | C2 | 01/08 | 1 | 100 |

Grain:

> Một Product Line trong một transaction.

Ưu điểm:

```text
+ Giữ nhiều thông tin chi tiết
+ Có thể drill-down
+ Linh hoạt cho ad-hoc analysis
+ Có thể aggregate theo nhiều dimensions
```

Nhược điểm:

```text
- Nhiều rows
- Tốn storage hơn
- Query aggregate có thể cần scan nhiều dữ liệu
```

---

## 5.2. Coarse Grain

Coarse-grained data là dữ liệu đã được tổng hợp.

Ví dụ:

```text
DailySales
```

| Date | Revenue |
|---|---:|
| 01/08 | 400 |
| 02/08 | 520 |
| 03/08 | 610 |

Grain:

> Một ngày.

Ưu điểm:

```text
+ Ít rows
+ Query dashboard tổng hợp nhanh
+ Ít dữ liệu cần scan
```

Nhược điểm:

```text
- Mất thông tin chi tiết
- Không thể trả lời nhiều câu hỏi drill-down
```

Ví dụ khi chỉ còn:

```text
Date
Revenue
```

thì không thể biết:

```text
Ai mua?
Mua sản phẩm nào?
Ở cửa hàng nào?
Order nào?
```

---

# 6. Atomic Grain là gì?

**Atomic Grain** là mức chi tiết thấp nhất mà business process thực sự ghi nhận được.

Ví dụ hóa đơn:

```text
Receipt #001
--------------------------------
Coca Cola      2     $4
Bread          1     $3
Milk           1     $2
```

Atomic Grain phù hợp có thể là:

> Một dòng sản phẩm trong một receipt.

Không phải:

```text
one receipt
```

và cũng không phải:

```text
daily revenue
```

Atomic-level data thường giúp hệ thống analytical linh hoạt hơn.

---

# 7. Tại sao không chỉ lưu dữ liệu tổng hợp?

Giả sử hệ thống có:

```text
1 billion transactions
```

Nếu aggregate thành:

```text
365 daily records
```

thì query tổng doanh thu sẽ rất nhanh.

Ví dụ:

```sql
SELECT SUM(revenue)
FROM daily_sales;
```

Nhưng nếu ngày mai CEO hỏi:

> Doanh thu của khách hàng nữ 25–35 tuổi mua iPhone tại TP.HCM qua Mobile App là bao nhiêu?

Nếu bảng chỉ còn:

```text
Date
Revenue
```

thì không thể trả lời.

Các dimensions như:

```text
Customer
Age
Gender
Product
Location
Channel
```

đã bị aggregate mất.

---

# 8. Ví dụ quan trọng: Order Grain vs Order Line Grain

Giả sử:

```text
OrderID = 1001
Customer = A
Date = 2026-08-01
```

Các sản phẩm:

```text
Product      Qty     Price
P1            2       100
P2            1       200
P3            3        50
```

---

## 8.1. Grain = Order

```text
FactOrder
```

| OrderID | CustomerKey | DateKey | TotalAmount |
|---|---|---|---:|
| 1001 | 10 | 20260801 | 550 |

Grain:

> Một row = một Order.

Có thể trả lời:

```text
Doanh thu theo customer?
Số lượng order?
Average Order Value?
```

Nhưng khó trả lời:

```text
Product nào bán chạy nhất?
```

vì Product detail đã mất.

---

## 8.2. Grain = Order Line

```text
FactSales
```

| OrderID | ProductKey | CustomerKey | DateKey | Qty | Amount |
|---|---|---|---|---:|---:|
| 1001 | P1 | 10 | 20260801 | 2 | 200 |
| 1001 | P2 | 10 | 20260801 | 1 | 200 |
| 1001 | P3 | 10 | 20260801 | 3 | 150 |

Grain:

> Một row = một Product Line trong một Order.

Có thể phân tích:

```text
Revenue by Product
Revenue by Customer
Revenue by Date
Revenue by Store
Quantity by Product
Revenue by Category
...
```

Vẫn có thể tính Order Total:

```sql
SELECT
    OrderID,
    SUM(Amount)
FROM FactSales
GROUP BY OrderID;
```

Nguyên tắc:

> **Fine grain có thể roll-up thành coarse grain.**

Nhưng:

> **Coarse grain không thể drill-down trở lại fine grain nếu detail đã bị mất.**

---

# 9. Roll-up và Drill-down

Granularity liên quan trực tiếp đến OLAP.

Ví dụ hierarchy:

```text
Day
 ↓
Month
 ↓
Quarter
 ↓
Year
```

---

## 9.1. Roll-up

Đi từ mức chi tiết lên mức tổng hợp.

```text
Day
→ Month
→ Year
```

Ví dụ:

```sql
SELECT
    YEAR(Date),
    MONTH(Date),
    SUM(Revenue)
FROM FactSales
GROUP BY
    YEAR(Date),
    MONTH(Date);
```

Đây là:

```text
Fine Grain
    ↓
Coarse Grain
```

---

## 9.2. Drill-down

Đi từ tổng quan xuống chi tiết.

```text
Year
→ Quarter
→ Month
→ Day
→ Transaction
```

Ví dụ dashboard:

```text
2026 Revenue
    ↓
August
    ↓
August 14
    ↓
Transactions
```

Drill-down chỉ có thể thực hiện nếu dữ liệu phía dưới vẫn còn đủ chi tiết.

---

# 10. Grain quyết định Dimension nào được phép có

Giả sử Fact Table:

```text
FactDailyStoreSales
```

Grain:

```text
Date + Store
```

| DateKey | StoreKey | Revenue |
|---|---|---:|
| 20260801 | S01 | 10000 |

Nếu thêm:

```text
ProductKey
```

thì Grain thay đổi thành:

```text
Date + Store + Product
```

Một row không còn là:

```text
Daily sales of a store
```

mà trở thành:

```text
Daily sales
per store
per product
```

Như vậy, thêm một dimension có thể làm thay đổi grain của Fact Table.

---

# 11. Grain quyết định Measure nào hợp lệ

Giả sử Grain là:

```text
one product
per store
per day
```

Các measure hợp lý:

```text
QuantitySold
Revenue
InventoryQuantity
```

Nhưng nếu thêm:

```text
CustomerAge
```

thì không hợp lý.

Tại sao?

Vì một Product tại một Store trong một Day có thể được nhiều Customer mua.

Không có một giá trị:

```text
CustomerAge
```

duy nhất ứng với row đó.

Do đó measure/attribute phải phù hợp với Grain.

---

# 12. Tại sao Grain phải xác định trước Dimensions và Facts?

Quy trình Kimball thường là:

```text
1. Select Business Process

        ↓

2. Declare Grain

        ↓

3. Identify Dimensions

        ↓

4. Identify Facts
```

Không nên làm:

```text
Dimensions
    ↓
Facts
    ↓
"Grain là gì?"
```

Mà phải:

```text
BUSINESS PROCESS
        ↓
Một row đại diện cho cái gì?
        ↓
GRAIN
        ↓
Dimensions nào mô tả event?
        ↓
Facts nào đo event?
```

---

# 13. Ví dụ thiết kế Fact Table từ đầu

Business requirement:

> Phân tích hoạt động bán hàng.

---

## Step 1 — Business Process

```text
Sales
```

---

## Step 2 — Declare Grain

Không ghi:

```text
Sales
```

vì quá mơ hồ.

Nên ghi:

> One row represents one product line in a customer sales transaction.

---

## Step 3 — Identify Dimensions

```text
Who?
→ Customer

What?
→ Product

Where?
→ Store

When?
→ Date

How?
→ Sales Channel

Who sold it?
→ Employee
```

Dimensions:

```text
DimCustomer
DimProduct
DimStore
DimDate
DimChannel
DimEmployee
```

---

## Step 4 — Identify Facts

Các measures:

```text
Quantity
UnitPrice
DiscountAmount
SalesAmount
CostAmount
```

Fact Table:

```text
FactSales
--------------------------------
DateKey
CustomerKey
ProductKey
StoreKey
EmployeeKey
ChannelKey
OrderNumber

Quantity
UnitPrice
DiscountAmount
SalesAmount
CostAmount
```

Mọi dimension và measure phải phù hợp với Grain:

```text
ONE PRODUCT LINE
IN ONE SALES TRANSACTION
```

---

# 14. Mixing Grain — lỗi rất nguy hiểm

Ví dụ:

```text
Product     Date        Revenue
--------------------------------
iPhone      01/08       1000
AirPods     01/08        500
ALL         01/08       1500
```

Hai dòng đầu có Grain:

```text
Date + Product
```

Dòng cuối có Grain:

```text
Date
```

Nếu chạy:

```sql
SELECT SUM(Revenue)
FROM Sales;
```

kết quả:

```text
1000 + 500 + 1500
= 3000
```

Trong khi doanh thu thật:

```text
1500
```

Đây là:

```text
Double Counting
```

Nguyên nhân:

> Một bảng đang chứa nhiều Grain khác nhau.

---

# 15. Cách đúng khi cần Summary Grain

Nên tách thành các bảng khác nhau.

---

## Atomic Fact

```text
FactSales
```

Grain:

```text
one order line
```

---

## Daily Aggregate

```text
FactDailyProductSales
```

Grain:

```text
one product per day
```

---

## Monthly Aggregate

```text
FactMonthlyStoreSales
```

Grain:

```text
one store per month
```

Kiến trúc:

```text
              FactSales
             Atomic Grain
                  │
          ┌───────┴───────┐
          ↓               ↓
FactDailyProduct    FactDailyStore
                          │
                          ↓
                  FactMonthlyStore
```

---

# 16. Granularity không chỉ là Time

Một lỗi phổ biến:

```text
Granularity
=
Day / Month / Year
```

Granularity không chỉ phụ thuộc vào thời gian.

Ví dụ:

```text
Product
Store
Date
Customer
```

Có thể có:

```text
Grain A:
Product
```

```text
Grain B:
Product + Day
```

```text
Grain C:
Product + Store + Day
```

```text
Grain D:
Product + Store + Day + Customer
```

Grain D chi tiết hơn Grain C.

---

# 17. Cardinality là gì?

**Cardinality** mô tả:

> Số lượng giá trị khác nhau (distinct values) của một column hoặc số lượng quan hệ giữa các entity.

Ví dụ:

```text
CustomerID
```

có:

```text
10,000,000 distinct values
```

→ High Cardinality.

Trong khi:

```text
Gender
```

có:

```text
Male
Female
Other
```

→ Low Cardinality.

---

# 18. Granularity vs Cardinality

Hai khái niệm này có liên quan nhưng không giống nhau.

## Granularity hỏi:

> Một row đại diện cho cái gì?

Ví dụ:

```text
One Product
per Store
per Day
```

---

## Cardinality hỏi:

> Có bao nhiêu giá trị khác nhau?

Ví dụ:

```text
ProductID
100,000 unique values
```

---

Có thể nhớ:

```text
Granularity
→ Meaning của một row

Cardinality
→ Số lượng distinct values
```

---

# 19. Ví dụ về Cardinality

Giả sử bảng Customer:

| CustomerID | Gender | Country |
|---|---|---|
| C001 | Male | Vietnam |
| C002 | Female | Vietnam |
| C003 | Male | Japan |
| C004 | Female | Vietnam |

Cardinality:

```text
CustomerID
→ 4

Gender
→ 2

Country
→ 2
```

Nếu thực tế có:

```text
CustomerID
→ 50,000,000 distinct values
```

thì CustomerID là high-cardinality column.

---

# 20. Granularity ảnh hưởng đến số rows

Giả sử chỉ có Dimension Date:

```text
365 days
```

Nếu Grain:

```text
Date
```

thì khoảng:

```text
365 rows
```

Nếu Grain:

```text
Date × Store
```

với:

```text
1000 Stores
```

thì có thể có:

```text
365 × 1000
= 365,000 combinations
```

Nếu Grain:

```text
Date × Store × Product
```

với:

```text
100,000 Products
```

thì không gian kết hợp tiềm năng rất lớn:

```text
365
× 1,000
× 100,000
```

Granularity càng fine thì thường số row càng lớn.

Nhưng:

> Granularity và Cardinality vẫn là hai khái niệm khác nhau.

---

# 21. Cardinality trong Relationship

Cardinality còn được dùng để mô tả quan hệ giữa bảng.

Các dạng phổ biến:

```text
1 : 1
1 : N
N : 1
N : N
```

---

## 21.1. One-to-Many

Ví dụ:

```text
DimCustomer
    1
    │
    │
    N
FactSales
```

Một Customer có thể có nhiều Sale Transactions.

```text
Customer
1
↓
Orders
N
```

---

## 21.2. Many-to-One

Nhìn từ Fact sang Dimension:

```text
FactSales
N
↓
DimProduct
1
```

Nhiều Fact rows có thể cùng tham chiếu đến một Product.

---

## 21.3. Many-to-Many

Ví dụ:

```text
Student
N
↔
N
Course
```

Một Student có nhiều Course.

Một Course có nhiều Student.

Thường phải dùng Bridge Table:

```text
Student
   1
   │
   N
StudentCourse
   N
   │
   1
Course
```

---

# 22. Granularity và Join

Một trong những lỗi phổ biến nhất trong Data Engineering là JOIN hai bảng có Grain khác nhau.

Ví dụ:

```text
FactOrder
```

Grain:

```text
one Order
```

| OrderID | Revenue |
|---|---:|
| 1 | 1000 |

Bảng:

```text
OrderItem
```

Grain:

```text
one Order Line
```

| OrderID | Product |
|---|---|
| 1 | P1 |
| 1 | P2 |
| 1 | P3 |

JOIN:

```sql
SELECT *
FROM FactOrder o
JOIN OrderItem i
    ON o.OrderID = i.OrderID;
```

Kết quả:

```text
OrderID   Product   Revenue
1         P1        1000
1         P2        1000
1         P3        1000
```

Sau đó:

```sql
SELECT SUM(Revenue)
...
```

ra:

```text
3000
```

Trong khi doanh thu thật:

```text
1000
```

Nguyên nhân:

```text
FactOrder Grain
=
Order

OrderItem Grain
=
Order Line
```

JOIN đã làm Revenue bị nhân lên.

---

# 23. Grain → Join → Double Counting

Có thể nhớ chuỗi sau:

```text
Không hiểu Grain
      ↓
Không hiểu uniqueness
      ↓
JOIN sai level
      ↓
Row explosion
      ↓
Duplicate facts
      ↓
SUM bị phóng đại
      ↓
Dashboard sai
```

Trước một JOIN analytical quan trọng, hãy hỏi:

```text
1. Left table grain là gì?

2. Right table grain là gì?

3. Sau JOIN grain sẽ là gì?
```

Câu số 3 đặc biệt quan trọng.

---

# 24. Grain và Primary Key

Giả sử Grain:

```text
one Product
per Store
per Day
```

Ta có thể nghĩ đến uniqueness:

```text
DateKey
+
StoreKey
+
ProductKey
```

Nhưng:

```text
Grain ≠ Primary Key
```

Grain là:

> Business meaning của một row.

Primary Key là:

> Cách database nhận diện row duy nhất.

Ví dụ Grain:

> Một sản phẩm tại một cửa hàng vào cuối một ngày.

Từ đó mới suy ra tổ hợp:

```text
DateKey + StoreKey + ProductKey
```

có thể đóng vai trò unique key.

---

# 25. Grain trong Dimension Table

Granularity không chỉ hữu ích cho Fact Table.

Ví dụ:

```text
DimProduct
```

Nếu dùng SCD Type 1:

```text
One row per Product
```

Nhưng nếu dùng SCD Type 2:

| ProductKey | ProductID | Price | From | To |
|---|---|---:|---|---|
| 1 | P01 | 100 | Jan | Mar |
| 2 | P01 | 120 | Apr | Dec |

Grain gần hơn với:

> Một historical version của một Product trong một khoảng thời gian.

Do đó:

```text
ProductID
```

không unique.

Mà:

```text
ProductKey
```

đại diện cho từng version.

---

# 26. Liên hệ với 3 loại Fact Table

## 26.1. Transaction Fact Table

Ví dụ:

```text
FactSales
```

Grain:

> Một dòng sản phẩm trong một transaction.

```text
Order  Product
1001   P1
1001   P2
1002   P3
```

---

## 26.2. Periodic Snapshot Fact

Ví dụ:

```text
FactDailyInventory
```

Grain:

> Một Product tại một Store tại cuối mỗi ngày.

```text
Date      Store    Product    Inventory
01/08     S1       P1         100
02/08     S1       P1          93
03/08     S1       P1          85
```

---

## 26.3. Accumulating Snapshot Fact

Ví dụ:

```text
FactOrderLifecycle
```

Grain:

> Một Order.

Columns:

```text
OrderID
OrderDate
PaymentDate
ShipDate
DeliveryDate
```

Một row được update dần:

```text
Order created
     ↓
Paid
     ↓
Shipped
     ↓
Delivered
```

---

# 27. Ví dụ AdventureWorks

Giả sử bảng:

```text
FactInternetSales
```

có:

```text
SalesOrderNumber
SalesOrderLineNumber
ProductKey
CustomerKey
OrderDateKey
OrderQuantity
SalesAmount
```

Có thể suy luận Grain:

> Một dòng sản phẩm trong một Sales Order.

Ví dụ:

```text
SO1001
├── line 1 → Bike
├── line 2 → Helmet
└── line 3 → Bottle
```

Trong Fact:

```text
SO1001  1  Bike
SO1001  2  Helmet
SO1001  3  Bottle
```

Do đó:

```text
SalesOrderNumber
```

xuất hiện nhiều dòng không nhất thiết là duplicate sai.

Có thể đơn giản vì:

```text
One Sales Order
    ↓
Many Order Lines
```

---

# 28. Granularity và Medallion Architecture

Một pattern thường gặp:

```text
Bronze
   ↓
Silver
   ↓
Gold
```

---

## Bronze

Raw event-level data:

```text
event_id
timestamp
order_id
product_id
customer_id
event_type
```

Grain có thể rất fine:

```text
one event
```

---

## Silver

Cleaned business data:

```text
FactSales
```

Grain:

```text
one order line
```

---

## Gold

Aggregated data:

```text
DailyProductSales
```

Grain:

```text
one product per day
```

Hoặc:

```text
MonthlyStoreSales
```

Grain:

```text
one store per month
```

Có thể hình dung:

```text
Bronze
Fine / Raw Grain

      ↓

Silver
Atomic Business Grain

      ↓

Gold
Summary / Aggregate Grain
```

---

# 29. Granularity và Storage

## Fine Grain

Ví dụ:

```text
Transaction-level
```

Có thể có:

```text
billions of rows
```

Ưu điểm:

```text
+ Flexible
+ Drill-down tốt
+ Không mất detail
+ Phù hợp ad-hoc analysis
```

Nhược điểm:

```text
- Storage lớn
- Scan nhiều dữ liệu
- Aggregate query có thể tốn tài nguyên hơn
```

---

## Coarse Grain

Ví dụ:

```text
Daily / Monthly aggregate
```

Ưu điểm:

```text
+ Ít rows
+ Query dashboard nhanh
+ Ít data scan
```

Nhược điểm:

```text
- Mất detail
- Không thể trả lời câu hỏi chi tiết hơn grain
```

---

# 30. Có phải luôn chọn Grain nhỏ nhất?

Không phải lúc nào cũng đơn giản là:

```text
Càng chi tiết càng tốt
```

Thực tế thường dùng:

```text
Atomic Fact
+
Aggregate Fact Tables
```

Ví dụ:

```text
Raw
 │
 ▼
Atomic Fact
 │
 ├───────────┐
 ▼           ▼
Daily Agg   Monthly Agg
 │           │
 └─────┬─────┘
       ▼
    Dashboard
```

Atomic Fact giữ khả năng drill-down.

Aggregate Fact giúp performance.

---

# 31. Cách đọc một bảng bất kỳ

Khi gặp một table, nên hỏi theo thứ tự:

```text
1. Business process là gì?

2. Một row đại diện cho cái gì?

3. Grain là gì?

4. Cái gì làm row unique?

5. Dimensions nào phù hợp với Grain?

6. Measures nào có ý nghĩa tại Grain?

7. Cardinality của các key/columns là gì?

8. Join với table khác có làm thay đổi Grain không?

9. Aggregate có gây Double Counting không?
```

---

# 32. Tóm tắt Granularity

```text
                    GRANULARITY
                         │
               Mức độ chi tiết data
                         │
           ┌─────────────┴─────────────┐
           ▼                           ▼
       Fine Grain                 Coarse Grain
           │                           │
       detailed                    aggregated
           │                           │
       nhiều rows                   ít rows
           │                           │
      flexible query               fast summary
           │                           │
      drill-down tốt              mất detail
```

Câu quan trọng nhất:

> **One row represents WHAT?**

---

# 33. Tóm tắt Cardinality

```text
CARDINALITY
     │
     ├── Số distinct values của column
     │
     └── Số lượng quan hệ giữa entities/tables
```

Ví dụ:

```text
CustomerID
→ High Cardinality

Gender
→ Low Cardinality
```

Relationship:

```text
1 : 1
1 : N
N : 1
N : N
```

---

# 34. Granularity vs Cardinality vs Primary Key

| Khái niệm | Câu hỏi chính |
|---|---|
| Granularity / Grain | Một row đại diện cho cái gì? |
| Cardinality | Có bao nhiêu giá trị khác nhau / quan hệ bao nhiêu-nhiêu? |
| Primary Key | Column nào xác định duy nhất một row? |

Ví dụ:

```text
FactDailyProductStore
```

Grain:

```text
One Product
per Store
per Day
```

Có thể có unique key:

```text
DateKey + StoreKey + ProductKey
```

Cardinality:

```text
DateKey      → ~365/year
StoreKey     → 1,000 stores
ProductKey   → 100,000 products
```

---

# 35. Mindset cần nhớ

Khi nhìn một bảng Data Warehouse, đừng hỏi đầu tiên:

```text
Table này có những column gì?
```

Hãy hỏi:

> **Một row của table này đại diện cho cái gì?**

Sau đó mới hỏi:

```text
Dimensions?
Measures?
Primary Key?
Cardinality?
Relationships?
Aggregation?
```

Có thể nhớ toàn bộ tư duy bằng chuỗi:

```text
Business Process
      ↓
Declare Grain
      ↓
Identify Dimensions
      ↓
Identify Facts
      ↓
Understand Cardinality
      ↓
Build Relationships
      ↓
Correct Aggregation
      ↓
Accurate Analytics
```

---

# 36. Checklist khi thiết kế Fact Table

Trước khi tạo Fact Table, kiểm tra:

- [ ] Business process đã rõ chưa?
- [ ] Một row đại diện cho cái gì?
- [ ] Grain đã được viết thành một câu rõ ràng chưa?
- [ ] Grain có nhất quán cho toàn bộ rows không?
- [ ] Dimensions có phù hợp với Grain không?
- [ ] Measures có đúng mức Grain không?
- [ ] Có trộn Atomic Grain với Summary Grain không?
- [ ] Join với bảng khác có gây row explosion không?
- [ ] Có nguy cơ double counting không?
- [ ] Cardinality của các foreign keys là bao nhiêu?
- [ ] Relationship là 1:N hay N:N?
- [ ] Có cần Bridge Table không?
- [ ] Có cần Aggregate Fact Table riêng để tối ưu performance không?

---

# 37. Kết luận

Ba ý quan trọng nhất:

### 1. Grain là business meaning của một row

```text
One row represents WHAT?
```

### 2. Granularity càng fine thì dữ liệu càng chi tiết

```text
Fine Grain
→ nhiều detail
→ nhiều rows
→ linh hoạt

Coarse Grain
→ ít detail
→ ít rows
→ query summary nhanh
```

### 3. Cardinality không phải Granularity

```text
Granularity
→ mức chi tiết của row

Cardinality
→ số distinct values hoặc loại quan hệ
```

Hiểu đúng Grain và Cardinality giúp tránh rất nhiều lỗi trong Data Engineering:

```text
Wrong Grain
    ↓
Wrong Join
    ↓
Duplicate Rows
    ↓
Double Counting
    ↓
Wrong Metrics
    ↓
Wrong Dashboard
```

Vì vậy, trước khi thiết kế hoặc query một Fact Table, hãy luôn bắt đầu bằng:

> **Một row trong bảng này đại diện cho cái gì?**
