# Batch Processing & Stream Processing cho Data Engineer

## 1. Bài toán gốc: dữ liệu được xử lý khi nào?

Khi dữ liệu liên tục được sinh ra, Data Engineer phải quyết định:

> **Xử lý dữ liệu theo từng đợt hay xử lý liên tục khi dữ liệu vừa xuất hiện?**

Đó chính là khác biệt cốt lõi giữa:

```text
Batch Processing
vs
Stream Processing
```

---

# 2. Batch Processing là gì?

## 2.1. Ý tưởng

**Batch Processing = gom dữ liệu lại thành từng đợt rồi xử lý.**

Ví dụ:

```text
10:00:01  Customer A mua hàng
10:00:03  Customer B mua hàng
10:00:05  Customer C mua hàng
...
```

Thay vì xử lý ngay từng transaction, hệ thống có thể chờ:

```text
10:00 ───────────── 11:00
       collect data
                     ↓
                  process
                     ↓
                   result
```

Ví dụ business:

> Mỗi 1 giờ tính tổng doanh thu một lần.

---

## 2.2. Ví dụ Batch Pipeline

```text
OLTP Database
      ↓
Extract
      ↓
Transform
      ↓
Data Warehouse
      ↓
BI Dashboard
```

Pipeline chạy mỗi ngày:

```text
01:00
│
├── extract_orders
├── extract_payments
├── transform
├── load_dim_customer
├── load_fact_sales
└── refresh_dashboard
```

Đây là một **batch pipeline**.

---

# 3. Vì sao Batch Processing tồn tại?

Không phải bài toán nào cũng cần realtime.

Ví dụ doanh nghiệp chỉ cần biết:

```text
Doanh thu hôm qua?
Doanh thu tuần trước?
Top sản phẩm tháng này?
Báo cáo tài chính cuối ngày?
```

Nếu business nói:

```text
Data trễ 1 giờ cũng chấp nhận được
```

thì xây một hệ thống streaming phức tạp có thể không đáng.

Nguyên tắc quan trọng:

> **Không phải dữ liệu càng realtime thì kiến trúc càng tốt.**

Kiến trúc tốt là kiến trúc đáp ứng đúng **latency requirement** của business với chi phí và độ phức tạp hợp lý.

---

# 4. Batch Processing làm việc với Bounded Data

Batch thường xử lý một tập dữ liệu hữu hạn.

Ví dụ:

```text
orders_2026_08_17.csv
```

có:

```text
1,000,000 rows
```

Ta biết:

```text
START
  ↓
row 1
...
row 1,000,000
  ↓
END
```

Do đó các phép tính như:

```sql
SELECT COUNT(*)
FROM orders;
```

hay:

```sql
SELECT SUM(amount)
FROM orders;
```

có một kết quả cuối cùng vì dataset có điểm kết thúc.

---

# 5. Batch không đồng nghĩa với Full Load

Đây là chỗ rất dễ nhầm.

Có thể có:

```text
Batch + Full Load
```

hoặc:

```text
Batch + Incremental Load
```

---

## 5.1. Full Batch Load

Giả sử bảng source có:

```text
100 triệu rows
```

Mỗi ngày vẫn đọc lại toàn bộ:

```sql
SELECT *
FROM orders;
```

---

## 5.2. Incremental Batch Load

Mỗi ngày chỉ có thêm:

```text
200,000 rows
```

Ta có thể chỉ lấy phần thay đổi:

```sql
SELECT *
FROM orders
WHERE updated_at > last_loaded_time;
```

Ví dụ:

```text
Ngày 1

1 → 1,000,000
        ↓
      load


Ngày 2

1,000,001 → 1,200,000
               ↓
             load
```

Vẫn là:

```text
Batch Processing
```

chỉ khác ở cách load:

```text
Incremental / Delta Load
```

---

# 6. Batch Pipeline phải giải quyết những gì?

Một Data Engineer không chỉ cần biết:

```text
Airflow
Spark
dbt
```

mà phải hiểu các vấn đề:

```text
Batch Processing
│
├── Full Load
├── Incremental Load
├── Partitioning
├── Scheduling
├── Dependency
├── Retry
├── Idempotency
├── Backfill
├── Data Quality
└── SLA
```

Ví dụ pipeline:

```text
extract_orders
      ↓
transform_orders
      ↓
load_fact_sales
      ↓
refresh_dashboard
```

Nếu `transform_orders` fail, phải trả lời được:

```text
Có retry không?
Retry bao nhiêu lần?
Có rerun toàn pipeline không?
Có cần rerun riêng ngày bị lỗi không?
Có duplicate dữ liệu không?
Có vượt SLA không?
```

---

# 7. Backfill là gì?

Giả sử pipeline chạy theo ngày:

```text
2026-08-15 ✅
2026-08-16 ❌
2026-08-17 ✅
2026-08-18 ✅
```

Ngày 16 bị lỗi.

Ta chạy lại riêng:

```text
2026-08-16
```

để bổ sung dữ liệu thiếu.

Đó là:

```text
Backfill
```

Backfill rất quan trọng với batch pipeline.

---

# 8. Idempotency trong Batch

Pipeline có thể cần rerun.

Nếu chạy lại:

```text
load_sales_2026_08_17
```

mà tạo thêm duplicate rows thì pipeline không an toàn.

Ví dụ không idempotent:

```sql
INSERT INTO fact_sales
SELECT *
FROM staging_sales;
```

Rerun có thể insert lần nữa.

Một cách an toàn hơn có thể là:

```sql
MERGE INTO fact_sales t
USING staging_sales s
ON t.order_id = s.order_id

WHEN MATCHED THEN UPDATE ...
WHEN NOT MATCHED THEN INSERT ...;
```

Mục tiêu:

```text
run 1 lần
=
run nhiều lần
```

về mặt kết quả cuối cùng.

---

# 9. Stream Processing là gì?

## 9.1. Ý tưởng

**Stream Processing = dữ liệu đến đâu xử lý đến đó.**

Ví dụ:

```text
event
  ↓
Processor
  ↓
result

event
  ↓
Processor
  ↓
result
```

Thay vì chờ 1 giờ rồi mới xử lý:

```text
Transaction xảy ra
        ↓
Process ngay
        ↓
Update result
```

---

# 10. Stream thường làm việc với Unbounded Data

Kafka topic:

```text
orders
```

có thể liên tục có event:

```text
event1
event2
event3
event4
...
...
...
```

và không có:

```text
END
```

Đây là:

```text
Unbounded Data
```

Vì không có điểm kết thúc, ta không thể đơn giản hỏi:

```text
COUNT(all events)
```

và chờ một kết quả cuối cùng.

Đó là lý do **Window** xuất hiện.

---

# 11. Streaming khác Stream Processing

Hai khái niệm này không hoàn toàn giống nhau.

## Streaming

Là việc dữ liệu di chuyển liên tục:

```text
Application
    ↓
Kafka
    ↓
Consumer
```

## Stream Processing

Là xử lý dữ liệu đang stream:

```text
Stream
  ↓
Filter
Transform
Aggregate
Join
Detect Pattern
Enrich
  ↓
Result
```

Ví dụ:

```text
Kafka
  ↓
Flink
  ↓
SUM(amount)
GROUP BY customer
WINDOW 5 minutes
```

---

# 12. Kiến trúc Stream cơ bản

Mental model:

```text
Producer
   ↓
Event Broker
   ↓
Stream Processor
   ↓
Sink
```

Ví dụ:

```text
Website
   ↓
Kafka
   ↓
Flink
   ↓
ClickHouse
```

---

## 12.1. Producer

Sinh ra event:

```text
Web App
Mobile App
Database CDC
IoT Sensor
Payment System
```

---

## 12.2. Event Broker

Giữ và phân phối event:

```text
Kafka
Pulsar
Kinesis
```

---

## 12.3. Stream Processor

Xử lý event:

```text
Flink
Spark Structured Streaming
Kafka Streams
```

---

## 12.4. Sink

Nơi nhận kết quả:

```text
Data Warehouse
Lakehouse
Database
Search Engine
Kafka topic khác
Dashboard backend
```

---

# 13. Event là gì?

Ví dụ:

```json
{
  "event_id": "abc123",
  "event_type": "ORDER_CREATED",
  "order_id": 1001,
  "customer_id": 12,
  "amount": 500000,
  "event_time": "2026-08-18T20:00:13Z"
}
```

Một Data Engineer nên chú ý:

```text
event_id
event_type
business key
payload
event_time
schema/version
```

Đặc biệt:

```text
event_time
```

là cực kỳ quan trọng.

---

# 14. Event Time vs Processing Time

Giả sử customer mua hàng lúc:

```text
10:01:00
```

nhưng do network lag:

```text
Kafka nhận lúc       10:01:07
Flink xử lý lúc      10:01:09
```

Ta có:

```text
Event Time
10:01:00
    ↓

Ingestion Time
10:01:07
    ↓

Processing Time
10:01:09
```

---

# 15. Vì sao Event Time quan trọng?

Business hỏi:

> Doanh thu từ 10:00 → 10:05 là bao nhiêu?

Có event:

```text
Event A
event_time = 10:04:50
arrival    = 10:04:51

Event B
event_time = 10:04:30
arrival    = 10:05:10
```

Event B xảy ra trước nhưng đến hệ thống sau.

Nếu dùng:

```text
processing_time
```

B có thể bị tính vào:

```text
10:05 → 10:10
```

Sai business meaning.

Nếu dùng:

```text
event_time
```

B vẫn thuộc:

```text
10:00 → 10:05
```

Đúng.

---

# 16. Out-of-order Event

Dữ liệu thực tế không nhất thiết đến theo thứ tự:

```text
1
2
3
4
5
```

mà có thể:

```text
1
3
2
5
4
```

Nguyên nhân:

```text
network delay
retry
multiple producers
different partitions
mobile offline
queueing
```

Do đó:

> **Arrival order không nhất thiết bằng Event order.**

Đây là lý do stream processing cần:

```text
Event Time
+
Watermark
```

---

# 17. Window

Vì stream không có điểm kết thúc nên khi aggregate phải chia stream thành các khoảng hữu hạn.

Ví dụ:

```text
SUM sales every 5 minutes
```

---

## 17.1. Tumbling Window

Không overlap:

```text
10:00────10:05
10:05────10:10
10:10────10:15
```

Ví dụ:

```text
Doanh thu mỗi 5 phút
```

---

## 17.2. Sliding Window

Ví dụ:

```text
window size = 10 phút
slide       = 5 phút
```

Ta có:

```text
09:55────────10:05
      10:00────────10:10
            10:05────────10:15
```

Các window overlap.

Use case:

> Số transaction trong 10 phút gần nhất, cập nhật mỗi 5 phút.

---

## 17.3. Session Window

Dựa trên inactivity.

Ví dụ:

```text
click click click
       │
       │ 30 phút không hoạt động
       ▼
     session end
```

Dùng nhiều trong:

```text
clickstream
user behavior
web analytics
```

---

# 18. Watermark

Giả sử window:

```text
10:00 → 10:05
```

Đồng hồ hiện tại:

```text
10:05
```

Không thể đóng window ngay vì có thể còn event:

```text
event_time = 10:04:55
```

nhưng đến lúc:

```text
10:05:03
```

Nếu đóng quá sớm:

```text
result bị thiếu
```

Nhưng cũng không thể chờ mãi.

Watermark trả lời câu hỏi:

> **Hệ thống tin rằng event-time đã tiến tới đâu và các event cũ hơn mốc đó phần lớn đã tới rồi.**

Ví dụ:

```text
Max event time seen = 10:05:20
Allowed lateness    ≈ 10 sec

Watermark ≈ 10:05:10
```

Có thể hiểu:

```text
"Tôi tin rằng phần lớn event <= 10:05:10
đã đến rồi."
```

---

# 19. Late Event

Giả sử:

```text
watermark = 10:05:10
```

sau đó lại xuất hiện:

```text
event_time = 10:04:40
```

Đó là:

```text
Late Event
```

Ta cần policy:

```text
late <= 5 phút
    ↓
update / recalculate window

late > 5 phút
    ↓
side output / dead letter / drop
```

Tùy business.

---

# 20. Trade-off giữa Accuracy và Latency

Nếu chờ lâu hơn:

```text
late events ↓
accuracy ↑
latency ↑
```

Nếu đóng window sớm hơn:

```text
latency ↓
late events ↑
risk missing data ↑
```

Nên stream processing luôn có trade-off:

```text
Correctness
Latency
Cost
```

Không thể mặc định yêu cầu:

```text
realtime tuyệt đối
+
chính xác tuyệt đối
+
rẻ
```

---

# 21. Stateless Processing

Mỗi event được xử lý độc lập.

Ví dụ:

```text
event
 ↓
amount * exchange_rate
 ↓
output
```

Hoặc:

```sql
WHERE amount > 1000
```

Không cần nhớ event trước.

---

# 22. Stateful Processing

Ví dụ:

> Tổng tiền Customer A đã chi trong 10 phút gần nhất.

Event:

```text
A $10
A $30
A $50
```

Processor phải nhớ:

```text
state[A] = 90
```

Khi event mới:

```text
A $20
```

thì:

```text
state[A] = 110
```

Đó là:

```text
State
```

---

# 23. Những operation cần State

Rất nhiều:

```text
GROUP BY
SUM
COUNT
AVG
Window
Stream-stream Join
Deduplication
Fraud Detection
Sessionization
```

Ví dụ deduplication:

```text
event_id = abc123
```

Processor có thể cần nhớ:

```text
seen = {
  abc123,
  def456,
  ...
}
```

Nếu thấy:

```text
abc123
```

lần nữa:

```text
DROP
```

---

# 24. State tạo ra vấn đề Failure

Giả sử state:

```text
Customer A = $1000
Customer B = $500
Customer C = $300
```

Server chết:

```text
💥
```

Nếu state chỉ ở RAM:

```text
STATE LOST
```

Do đó stream processing cần:

```text
Checkpoint
Snapshot
Replay
```

---

# 25. Checkpoint

Ví dụ stream:

```text
E1 E2 E3 E4 E5 E6 E7
            ↑
       checkpoint
```

Checkpoint có thể lưu:

```text
source position / offset
+
operator state
```

Nếu chết khi đang xử lý:

```text
E7
```

hệ thống có thể:

```text
restart
   ↓
load checkpoint
   ↓
restore state
   ↓
replay event sau checkpoint
```

---

# 26. Offset

Trong Kafka:

```text
Topic: orders
```

Một partition:

```text
offset 0 → event A
offset 1 → event B
offset 2 → event C
offset 3 → event D
offset 4 → event E
```

Offset cho biết vị trí event trong partition log.

Consumer có thể biết mình đã đọc tới đâu:

```text
offset = 3
```

Nhờ đó khi restart có thể:

```text
resume / replay
```

---

# 27. Partition

Kafka topic thường được chia:

```text
orders
│
├── Partition 0
├── Partition 1
├── Partition 2
└── Partition 3
```

Mục đích:

```text
parallelism
scalability
throughput
```

Một điểm quan trọng:

> **Ordering được đảm bảo trong từng partition, không phải toàn topic.**

---

# 28. Partition Key

Ví dụ:

```text
key = customer_id
```

Ta có thể muốn:

```text
Customer A → Partition 1
Customer A → Partition 1
Customer A → Partition 1
```

Nhờ đó các event của cùng customer đi cùng logical partition.

Các use case:

```text
GROUP BY customer_id
Fraud Detection by account
Session by user
```

phụ thuộc mạnh vào partition key.

Chọn key sai có thể tạo:

```text
hot partition
data skew
poor parallelism
```

---

# 29. Delivery / Processing Semantics

Ba khái niệm bắt buộc:

```text
At-most-once
At-least-once
Exactly-once
```

---

## 29.1. At-most-once

Event được xử lý:

```text
0 hoặc 1 lần
```

Thường không retry.

Flow:

```text
Receive E1
   ↓
commit
   ↓
process
```

Nếu crash sau commit nhưng trước khi xử lý xong:

```text
E1 có thể bị mất
```

Đặc điểm:

```text
Không duplicate
Có thể mất data
```

---

## 29.2. At-least-once

Event được xử lý:

```text
>= 1 lần
```

Có retry.

Nếu crash:

```text
process E1
   ↓
💥
   ↓
retry E1
```

Đặc điểm:

```text
Không muốn mất event
Nhưng có thể duplicate
```

---

## 29.3. Exactly-once

Mục tiêu là:

```text
event → business effect đúng 1 lần
```

Ngay cả khi hệ thống phải:

```text
retry
replay
restore
```

nhiều lần ở bên trong.

Ví dụ:

```text
E1 = cộng doanh thu 100.000
```

Có thể physical processing:

```text
Process E1
   ↓
💥
   ↓
Replay E1
```

nhưng kết quả cuối phải tương đương:

```text
Revenue += 100.000
```

chỉ một lần.

Điểm cần nhớ:

> **Exactly-once không nhất thiết nghĩa là event chỉ được execute vật lý đúng một lần.**

Mà là:

> **Kết quả cuối cùng phải giống như event chỉ được xử lý một lần.**

---

# 30. So sánh 3 semantics

| Semantic | Retry | Có thể mất event? | Có thể duplicate effect? |
|---|---:|---:|---:|
| At-most-once | Thường không | Có | Không |
| At-least-once | Có | Không | Có |
| Exactly-once | Có thể có | Không | Không về mặt logical/business effect |

Mental model:

```text
At-most-once
= không muốn duplicate
= chấp nhận mất


At-least-once
= không muốn mất
= chấp nhận duplicate


Exactly-once
= không muốn mất
= không muốn duplicate effect
```

---

# 31. Idempotency

Operation idempotent nghĩa là:

```text
run 1 lần
=
run nhiều lần
```

về mặt kết quả cuối.

Ví dụ:

```sql
MERGE INTO sales t
USING new_data s
ON t.order_id = s.order_id

WHEN MATCHED THEN UPDATE ...
WHEN NOT MATCHED THEN INSERT ...;
```

Idempotency rất quan trọng cho:

```text
retry
replay
recovery
exactly-once effect
```

---

# 32. Stream Join khó hơn Batch Join

Batch join:

```text
orders
   ↓
 JOIN
   ↑
customers
```

Hai dataset hữu hạn.

Stream-stream join:

```text
orders stream
      ↓
      JOIN
      ↑
payments stream
```

Ví dụ:

```text
Order    10:00
Payment  10:02
```

Processor phải giữ order trong state để đợi payment.

Nhưng phải trả lời:

```text
Chờ bao lâu?
1 phút?
1 giờ?
1 ngày?
```

Vì vậy stream join liên quan tới:

```text
state
time
window
watermark
TTL
```

---

# 33. Backpressure

Giả sử:

```text
Producer
100k events/sec
      ↓

Flink
100k events/sec
      ↓

Database
20k writes/sec
```

Sink không theo kịp.

Data bắt đầu tích tụ:

```text
Source
 ↓↓↓↓↓↓↓
Processor
 ↓↓↓↓↓↓↓
Sink
   ↓
  slow
```

Đây là:

```text
Backpressure
```

Backpressure có thể lan ngược từ downstream về upstream.

---

# 34. Metrics cần monitor trong Streaming

```text
Throughput
Latency
Consumer Lag
Backpressure
CPU
Memory
State Size
Checkpoint Duration
Checkpoint Failure
Late Event Rate
Error Rate
```

---

# 35. Throughput vs Latency

## Throughput

Số lượng event xử lý được mỗi đơn vị thời gian:

```text
100,000 events/sec
```

## Latency

Thời gian từ lúc event xảy ra đến lúc result available:

```text
event
  ↓
250 ms
  ↓
result
```

Không thể hỏi đơn giản:

> Hệ thống nào nhanh hơn?

Phải hỏi:

```text
Business cần latency bao nhiêu?
Volume bao nhiêu?
Có burst không?
SLA là gì?
```

---

# 36. Micro-batch

Batch và Stream không phải lúc nào cũng là hai cực hoàn toàn tách biệt.

Có thể hình dung:

```text
Batch
──────────────────────────── Stream

1 day
1 hour
5 min
1 min
10 sec
1 sec
100 ms
continuous
```

Micro-batch:

```text
events
events
events
   ↓
[small batch]
   ↓
process
   ↓
[small batch]
```

Business có thể nhìn thấy kết quả gần realtime nhưng execution vẫn theo các batch rất nhỏ.

---

# 37. Spark Structured Streaming

Spark Structured Streaming hỗ trợ xử lý stream thông qua DataFrame / Dataset abstraction.

Mental model thường gặp:

```text
Kafka
 ↓
small batch
 ↓
Spark
 ↓
result
```

Sau đó:

```text
Kafka
 ↓
next small batch
 ↓
Spark
```

Spark Structured Streaming mặc định thường được hiểu theo mô hình micro-batch.

---

# 38. Flink

Flink được thiết kế xoay quanh stream processing.

Mental model:

```text
event
 ↓
operator
 ↓
operator
 ↓
sink

event
 ↓
operator
 ↓
...
```

Khi học Flink, các khái niệm cốt lõi cần hiểu:

```text
Event Time
Watermark
Window
State
Checkpoint
Replay
```

---

# 39. CDC liên quan gì đến Streaming?

CDC = Change Data Capture.

Database có:

```text
INSERT
UPDATE
DELETE
```

CDC biến các thay đổi này thành event:

```text
Database Change
      ↓
     CDC
      ↓
   Event Stream
```

Ví dụ:

```text
PostgreSQL
    ↓
Debezium / CDC
    ↓
Kafka
    ↓
Flink
    ↓
Warehouse
```

CDC giúp chuyển từ:

```text
Hourly Batch Sync
```

sang:

```text
Continuous Incremental Sync
```

---

# 40. Batch vs Stream

| Tiêu chí | Batch | Stream |
|---|---|---|
| Data | Bounded | Unbounded |
| Processing | Theo đợt | Liên tục |
| Latency | Phút → giờ | ms → giây/phút |
| State | Thường đơn giản hơn | Rất quan trọng |
| Window | Không luôn bắt buộc | Rất quan trọng |
| Event Time | Ít phức tạp hơn | Cực kỳ quan trọng |
| Late Data | Dễ xử lý hơn | Vấn đề cốt lõi |
| Ordering | Dễ kiểm soát hơn | Khó hơn |
| Failure Recovery | Rerun / Backfill | Checkpoint + Replay |
| Complexity | Thấp hơn | Cao hơn |
| Cost | Thường thấp hơn | Thường cao hơn |
| Use Case | ETL, BI, reports | Fraud, monitoring, realtime |

---

# 41. Batch và Stream có thể cùng tồn tại

Kiến trúc thực tế:

```text
                 ┌── Stream ──► Realtime Dashboard
                 │
Database → CDC → Kafka
                 │
                 └── Stream ──► Data Lake
                                    ↓
                                  Batch
                                    ↓
                               dbt / Spark
                                    ↓
                              Data Warehouse
```

Một hệ thống có thể dùng stream cho:

```text
Fraud Detection
Realtime Monitoring
Realtime Dashboard
```

nhưng batch cho:

```text
Financial Report
Historical Aggregation
ML Training
Daily BI
```

---

# 42. Khi nào chọn Batch?

Ví dụ:

```text
Daily Sales Report
Monthly Finance Report
Historical Analysis
ML Training
Warehouse Transformations
Customer Segmentation
```

Nếu business nói:

```text
Data trễ 1 giờ không sao
```

thì batch thường là lựa chọn hợp lý.

Câu hỏi quan trọng:

> **Business có thực sự chịu thiệt hại nếu data trễ một khoảng thời gian không?**

Nếu không, chưa chắc cần streaming.

---

# 43. Khi nào chọn Stream?

Ví dụ:

```text
Fraud Detection
Realtime Recommendation
IoT Monitoring
Payment Monitoring
Realtime Operational Dashboard
Log Monitoring
Alerting
Clickstream
```

Ở đây:

```text
event
  ↓
wait 1 hour
```

có thể làm mất giá trị của dữ liệu.

Khi đó stream hợp lý hơn.

---

# 44. Chọn kiến trúc từ Business Requirement

Không nên bắt đầu bằng:

```text
Kafka?
Spark?
Flink?
```

Mà nên bắt đầu:

```text
Business Requirement
        ↓
How fresh must data be?
        ↓
Latency Requirement
        ↓
Architecture
        ↓
Technology
```

Ví dụ:

```text
24h
 ↓
Daily Batch

1h
 ↓
Hourly Batch

5m
 ↓
Micro-batch

seconds
 ↓
Stream

milliseconds
 ↓
Low-latency Stream
```

---

# 45. Case hoàn chỉnh: Fraud Detection

Business requirement:

> Một tài khoản có tổng giao dịch > 100 triệu trong vòng 5 phút thì phát cảnh báo.

Data:

```text
Account A → 30m
Account A → 40m
Account B → 10m
Account A → 50m
```

Pipeline:

```text
Kafka
  ↓
keyBy(account_id)
  ↓
5-minute window
  ↓
SUM(amount)
  ↓
sum > 100m
  ↓
Fraud Alert
```

Case này cần:

```text
Partition
State
Window
Event Time
Watermark
Late Event Policy
Checkpoint
Replay
Exactly-once / Idempotency
Backpressure Handling
```

---

# 46. Mental Model quan trọng nhất

```text
                 DATA

             ┌────┴────┐
             │         │
          Bounded   Unbounded
             │         │
           Batch     Stream
             │         │
         Dataset      Event
             │         │
       Run → Finish   Continuous
                       │
                ┌──────┼──────┐
                │      │      │
              Time   State  Failure
                │      │      │
             Window  State  Checkpoint
                │             │
            Watermark        Replay
```

---

# 47. Chuỗi nguyên nhân của Stream Processing

Đây là phần nên nhớ nhất.

```text
Data liên tục sinh ra
        ↓
Unbounded Stream
        ↓
Không thể chờ toàn bộ dataset
        ↓
Phải xử lý continuously
        ↓
Muốn aggregate
        ↓
Cần WINDOW
        ↓
Events có thể đến sai thứ tự
        ↓
Cần EVENT TIME
        ↓
Không thể chờ event trễ mãi
        ↓
Cần WATERMARK
        ↓
Một số event vẫn đến quá trễ
        ↓
Cần LATE EVENT POLICY
        ↓
Aggregation phải nhớ dữ liệu trước
        ↓
Cần STATE
        ↓
Máy có thể chết
        ↓
Cần CHECKPOINT
        ↓
Sau failure phải đọc lại
        ↓
Cần REPLAY / OFFSET
        ↓
Replay có thể xử lý event lần nữa
        ↓
Cần DELIVERY SEMANTICS
        ↓
Có nguy cơ duplicate side effect
        ↓
Cần IDEMPOTENCY / TRANSACTION
```

Nếu hiểu chuỗi này thì bạn đã hiểu phần lõi của stream processing ở góc nhìn Data Engineer.

---

# 48. Chuỗi nguyên nhân của Batch Processing

```text
Data hữu hạn hoặc chia được theo khoảng
        ↓
Collect
        ↓
Schedule
        ↓
Process Batch
        ↓
Full hoặc Incremental Load
        ↓
Failure
        ↓
Retry / Backfill
        ↓
Pipeline có thể chạy lại
        ↓
Cần Idempotency
        ↓
Partition + Orchestration
        ↓
Reliable Batch Pipeline
```

---

# 49. Tóm tắt những điều bắt buộc phải hiểu

## Batch

```text
Bounded Data
Full Load
Incremental / Delta Load
Partitioning
Scheduling
Dependency
Retry
Backfill
Idempotency
Data Quality
SLA
```

## Stream

```text
Unbounded Data
Event
Producer / Consumer
Topic
Partition
Offset
Event Time
Processing Time
Window
Watermark
Late Event
Out-of-order Event
State
Stateful / Stateless
Checkpoint
Replay
At-most-once
At-least-once
Exactly-once
Idempotency
Deduplication
Ordering
Backpressure
Throughput
Latency
```

---

# 50. Câu chốt nên nhớ

> **Batch khó ở orchestration, incremental processing, retry, backfill và data correctness.**

> **Stream có tất cả các vấn đề correctness đó, cộng thêm time, ordering, state và continuous failure recovery.**

Và quan trọng nhất:

> **Không chọn Batch hay Stream vì công nghệ nào “xịn hơn”. Hãy chọn dựa trên latency requirement và giá trị business của dữ liệu.**
