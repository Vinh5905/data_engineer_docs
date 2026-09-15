# Lộ trình tổng - Cloud Computing (IS402) trong 10 tuần

> 🟦 **Thời gian đề xuất:** 14/09/2026 - 21/11/2026, theo múi giờ Việt Nam (Asia/Ho_Chi_Minh). Tổng thời lượng khoảng **50 giờ**.
>
> Nếu lịch thực tế thay đổi, hãy giữ **đúng thứ tự tuần** vì kiến thức được xếp theo quan hệ phụ thuộc.

## 1. Cách dùng bộ tài liệu

Mỗi tuần học theo nhịp sau:

1. **Thứ Hai - Hiểu:** đọc mục tiêu, sơ đồ và bảng khái niệm; tự kể lại ví dụ mà không nhìn tài liệu.
2. **Thứ Tư - Nối:** đối chiếu slide gốc, ghi câu hỏi và làm bài luyện tư duy.
3. **Thứ Bảy - Làm:** hoàn thành lab, thu bằng chứng, cleanup tài nguyên cloud rồi đánh dấu checklist.
4. **Chủ Nhật - Ôn 20 phút:** trả lời lại câu hỏi tự kiểm tra và cập nhật một tờ ghi chú A4.

### Quy ước màu

- 🟦 **Mục tiêu / kết quả phải đạt**
- 🟨 **Ý cốt lõi cần hiểu**
- 🟩 **Trick tối ưu chi phí, hiệu năng hoặc vận hành**
- 🟧 **Cập nhật hiện hành so với slide cũ**
- 🟥 **Điểm dễ nhầm hoặc rủi ro quan trọng**

> 🟥 **Kỷ luật lab cloud:** Mỗi lab có resource group riêng, tags, budget/alert nếu có thể và bước cleanup. Không ghi access key, SAS, token hoặc secret vào ảnh báo cáo/Git.

## 2. Lộ trình và mốc thời gian

| Tuần | Khoảng thời gian | Chủ đề | Mốc hoàn thành |
|---:|---|---|---|
| 1 | 14-19/09/2026 | Distributed computing, cluster, grid, cloud, Azure VM | Tạo/quan sát/deallocate/xóa VM; phân biệt cluster-grid-cloud |
| 2 | 21-26/09/2026 | Cloud characteristics, IaaS/PaaS/SaaS, deployment models, Storage/Cosmos DB | Blob private + SAS ngắn hạn; thiết kế partition key |
| 3 | 28/09-03/10/2026 | IaaS, network virtualization, VNet peering, Data Lake/Synapse | Hai VNet không chồng CIDR, peering Connected, bài tính dữ liệu quét |
| 4 | 05-10/10/2026 | Server/storage virtualization, Key Vault, encryption, ARM/Bicep, File Sync | Bicep lint/what-if; thiết kế storage có RPO/RTO |
| 5 | 12-17/10/2026 | PaaS, container/ACI, Databricks, AI Language | Chạy và xóa container; vẽ pipeline sentiment |
| 6 | 19-24/10/2026 | GFS/HDFS, Data Factory, ACR/Container Apps, bot architecture | Pipeline idempotent + block placement qua 2 rack |
| 7 | 26-31/10/2026 | MapReduce, Pregel/BSP, App Service, Azure Monitor | Map/Shuffle/Reduce đúng; dashboard và ba alert |
| 8 | 02-07/11/2026 | NoSQL/CAP, Bigtable/HBase, streaming, load balancing | Row key không hotspot; window/watermark; chọn L4/L7 |
| 9 | 09-14/11/2026 | SaaS, REST/SOAP, SSO/OIDC, Functions, Defender | API contract + SLO; Function idempotent; security rules |
| 10 | 16-21/11/2026 | AI Search, Backup/Restore, RBAC, FinOps, đồ án | Architecture review, cost sheet, restore/demo checklist |

## 3. File học của từng tuần và ý cần nắm trước

### [Week 01 - Nền tảng điện toán phân tán và Azure](<Week01 - Nen tang dien toan phan tan va Azure.md>)

**Nắm trước khi học:**

- Bài toán lớn có thể tăng tốc bằng phần cứng nhanh hơn, thuật toán tốt hơn hoặc nhiều máy phối hợp.
- Cluster thường cùng quản trị; grid chia sẻ giữa nhiều tổ chức; cloud cung cấp tài nguyên theo dịch vụ.
- Shared-memory trao đổi qua vùng nhớ chung; distributed-memory phải gửi message.
- Cloud chỉ có ích kinh tế khi tài nguyên được thu hồi sau lúc cao điểm.

**Từ khóa:** distributed computing, parallel, cluster, grid, VO, Flynn, MPI, SPMD, utility computing.

### [Week 02 - Cloud service và lưu trữ Azure](<Week02 - Cloud service va luu tru Azure.md>)

**Nắm trước khi học:**

- Scalability khác elasticity; availability khác reliability và resilience.
- IaaS/PaaS/SaaS khác nhau chủ yếu ở **ranh giới trách nhiệm quản lý**.
- Blob/File/Queue/Table/Cosmos DB giải các access pattern khác nhau.
- Partition key và consistency ảnh hưởng đồng thời tới hiệu năng, độ đúng và chi phí.

**Từ khóa:** SLA, QoS, SOA, multi-tenancy, public/private/hybrid, SAS, RU, consistency.

### [Week 03 - IaaS, mạng ảo và Data Lake](<Week03 - IaaS mang ao va Data Lake.md>)

**Nắm trước khi học:**

- IaaS ảo hóa compute, storage, network và cung cấp interface quản lý/monitoring.
- VNet/subnet là phân đoạn logic; NSG kiểm soát traffic; peering kết nối các VNet.
- CIDR chồng lấn là lỗi thiết kế khó sửa sau khi triển khai.
- Parquet + partition/column pruning giảm số byte query và chi phí serverless analytics.

**Từ khóa:** abstraction, VM lifecycle, VLAN, VPN, MPLS, GRE, TUN/TAP, peering, Data Lake.

### [Week 04 - Ảo hóa server/storage và hạ tầng như mã](<Week04 - Ao hoa server storage va ha tang nhu ma.md>)

**Nắm trước khi học:**

- Hypervisor can thiệp CPU, memory và I/O; mỗi lần trap/context switch có thể tạo overhead.
- Hardware assistance như VT-x/EPT/VT-d/SR-IOV giảm một phần overhead.
- Storage virtualization có thể ở file/block level và host/network/storage location.
- IaC tạo hạ tầng lặp lại được; `what-if` giúp phát hiện thay đổi nguy hiểm trước deploy.

**Từ khóa:** Type 1/2, full/para virtualization, EPT, live migration, NAS/SAN, LVM, tiering, Bicep.

### [Week 05 - PaaS, container, Databricks và AI Language](<Week05 - PaaS container Databricks va AI Language.md>)

**Nắm trước khi học:**

- PaaS gồm resource pool, core platform và enabling services.
- Container chia sẻ kernel nên nhẹ hơn VM; dữ liệu bền vững không nên nằm trong filesystem tạm của container.
- Databricks/Spark biến đổi dữ liệu phân tán; action mới kích hoạt thực thi trong mô hình lazy phổ biến.
- Kết quả sentiment có xác suất và giới hạn, không nên tự động hóa quyết định nhạy cảm mà không kiểm tra.

**Từ khóa:** runtime, dynamic provisioning, image, registry, DataFrame, transformation/action, sentiment.

### [Week 06 - GFS/HDFS, Data Factory và Container Apps](<Week06 - GFS HDFS Data Factory va Container Apps.md>)

**Nắm trước khi học:**

- Coordinator giữ metadata; client trao đổi data trực tiếp với data server.
- Replication/placement/checksum giúp chịu lỗi nhưng không thay thế backup.
- HDFS tối ưu file lớn/đọc tuần tự; file nhỏ gây nhiều metadata và scheduling overhead.
- Pipeline cần retry, idempotency, quarantine và metric; container/Container Apps cần revision/scale guardrail.

**Từ khóa:** chunk, block, NameNode, DataNode, lease, checksum, locality, ETL, trigger, revision.

### [Week 07 - MapReduce, Pregel, App Service và Monitoring](<Week07 - MapReduce Pregel App Service va Monitoring.md>)

**Nắm trước khi học:**

- Map phát key-value; shuffle gom theo key; reduce tổng hợp.
- Combiner chỉ an toàn với phép toán phù hợp; data skew làm một reducer trở thành nút thắt.
- Pregel xử lý graph theo vertex và superstep có barrier.
- Quan sát hệ thống cần nối metric, log, trace với latency, traffic, errors và saturation.

**Từ khóa:** input split, partitioner, combiner, speculative execution, BSP, aggregator, SLO, alert.

### [Week 08 - NoSQL/CAP, Bigtable, streaming và load balancing](<Week08 - NoSQL CAP Bigtable streaming va load balancing.md>)

**Nắm trước khi học:**

- Chọn relational/NoSQL theo transaction và access pattern, không theo trào lưu.
- CAP buộc đánh đổi consistency/availability **khi network partition xảy ra**.
- Row key quyết định phân bố và locality; key tăng tuần tự dễ tạo hotspot.
- Streaming cần phân biệt event time/processing time; L4/L7 load balancer phục vụ nhu cầu khác nhau.

**Từ khóa:** ACID, BASE, sharding, SSTable, tablet/region, compaction, watermark, health probe, WAF.

### [Week 09 - SaaS, REST/SSO, Functions và Defender](<Week09 - SaaS REST SSO Functions va Defender.md>)

**Nắm trước khi học:**

- SaaS thành công nhờ multi-tenancy, cấu hình hóa và vận hành tập trung.
- Authentication xác minh danh tính; authorization quyết định quyền.
- Function/event handler phải idempotent vì message có thể được giao lại.
- Dùng PaaS/SaaS không xóa trách nhiệm của khách hàng đối với dữ liệu, identity, cấu hình và code.

**Từ khóa:** tenant isolation, SOAP, WSDL, REST, idempotency key, OIDC, SSO, trigger/binding, Defender.

### [Week 10 - Search, Backup, RBAC, FinOps và đồ án](<Week10 - Search Backup RBAC FinOps va do an.md>)

**Nắm trước khi học:**

- Search index là cấu trúc phục vụ tìm kiếm, không phải nguồn dữ liệu gốc duy nhất.
- Backup chỉ đáng tin khi restore test thành công; RPO nói về mất dữ liệu, RTO nói về thời gian phục hồi.
- Role assignment luôn gồm principal + role + scope; quyền control plane khác data plane.
- FinOps là vòng lặp plan → measure → optimize → verify, không phải cắt chi phí một lần.

**Từ khóa:** indexer, analyzer, vector/hybrid search, recovery point, RPO/RTO, RBAC, right-sizing, unit economics.

## 4. Mốc đồ án song song với việc học

| Hạn | Việc cần chốt | Sản phẩm |
|---|---|---|
| 26/09/2026 | Chọn bài toán, user, loại và nguồn dữ liệu | Problem statement + dataset note |
| 10/10/2026 | Chọn compute/storage/network và mô hình dữ liệu sơ bộ | Architecture v1 + data model v1 |
| 24/10/2026 | Hoàn thành ingest/ETL mẫu, retry và logging | Pipeline chạy với sample nhỏ |
| 07/11/2026 | Hoàn thành xử lý/query/API hoặc dashboard | Demo chức năng chính |
| 14/11/2026 | Bổ sung identity, security, monitoring và SLO | RBAC matrix + dashboard/alerts |
| 18/11/2026 | Bổ sung backup/restore, cost và tối ưu | RPO/RTO + cost sheet + benchmark |
| 21/11/2026 | Tự chấm rubric, quay demo và hoàn thiện báo cáo | Bản nộp hoàn chỉnh |

## 5. Mười lăm ý chính cho tờ A4 cuối kỳ

1. **Cloud = mô hình dịch vụ**, dựa trên distributed computing, virtualization, network và automation.
2. **Scale up** tăng sức mạnh một node; **scale out** tăng số node; elasticity là co giãn theo tải.
3. **IaaS/PaaS/SaaS** khác ở ranh giới trách nhiệm giữa khách hàng và nhà cung cấp.
4. Availability, reliability, fault tolerance và resilience liên quan nhưng không đồng nghĩa.
5. Virtualization là abstraction; overhead có thể đến từ CPU trap, address translation, I/O và network.
6. Storage phải chọn theo file/object/block, access pattern, latency, throughput, RPO và retention.
7. Replication giúp availability; backup + restore test mới chứng minh khả năng phục hồi dữ liệu.
8. VNet/subnet/NSG/route/peering là các lớp khác nhau; kết nối có không đồng nghĩa được phép truy cập.
9. PaaS giảm phần hạ tầng phải quản nhưng application/data/identity/SLO vẫn là trách nhiệm của đội phát triển.
10. GFS/HDFS tách metadata path và data path; data locality giảm truyền mạng.
11. MapReduce = Map → Shuffle/Sort → Reduce; combiner không được giả định chạy đúng một lần.
12. CAP mô tả lựa chọn khi partition; partition key quyết định phân bố tải và dữ liệu.
13. Multi-tenant phải cô lập ở backend, database, object path, cache, logs và background jobs.
14. Observability nối metrics/logs/traces với SLO; alert phải có ngưỡng, thời gian và hành động.
15. Tối ưu cloud phải đo **cost per unit** và kiểm tra lại SLO, không chỉ nhìn hóa đơn tổng.

## 6. Checklist tiến độ tổng

- [ ] Hoàn thành 10 file tuần và toàn bộ câu hỏi tự kiểm tra.
- [ ] Mỗi khái niệm có thể tự giải thích bằng một ví dụ khác ví dụ trong tài liệu.
- [ ] Có một resource naming/tagging convention nhất quán.
- [ ] Mọi lab cloud đều có ảnh/bằng chứng cleanup.
- [ ] Có một tờ A4 công thức/so sánh do chính bạn tự viết.
- [ ] Đồ án có data size, data format, algorithm, service role và data model.
- [ ] Đồ án có ETL, benchmark đọc/ghi, monitoring, retry và idempotency.
- [ ] Đồ án có RBAC/secret management, RPO/RTO và restore scenario.
- [ ] Đồ án có so sánh phương án, cost estimate, cost per unit và 5 tối ưu.
- [ ] Demo kể được câu chuyện: **bài toán → kiến trúc → dữ liệu → chạy thật → lỗi/khôi phục → chi phí**.

## 7. Bản đồ tài liệu gốc

| Tài liệu | Được dùng ở |
|---|---|
| `Buoi01_GioiThieu_02.pdf` | Toàn bộ lịch lab tuần 1-10, rubric quá trình/đồ án |
| `Buoi02_CloudComputingPlatform_01.pdf` | Week 02, bản đồ dịch vụ Azure/AWS/GCP |
| `Lecture 1 - Overview of Distributed Computing.pdf` | Week 01 |
| `Lecture 2 - Introduction to Cloud Computing.pdf` | Week 02 |
| `Lecture 3 - Introduction to IaaS.pdf` | Week 03 |
| `Lecture 3.1 - Server Virtualization.pdf` | Week 04 |
| `Lecture 3.2 - Storage Virtualization.pdf` | Week 04 |
| `Lecture 3.3 - Network virtualization.pdf` | Week 03 |
| `Lecture 4 - Introduction to PaaS.pdf` | Week 05 |
| `Lecture 4.1 - PaaS Techniques (File System).pdf` | Week 06 |
| `Lecture 4.2 - PaaS Techniques (Programming Model).pdf` | Week 07 |
| `Lecture 4.3 - PaaS Techniques (Database).pdf` | Week 08 |
| `Lecture 5 - Introduction to SaaS & Its Techniques.pdf` | Week 09 |
| Giáo trình Marinescu (2022/2023 edition trong thư mục) | Đọc sâu xuyên suốt Week 01-10 |
| Giáo trình Buyya (2011) | Nền tảng cloud, provisioning, SLA, storage và application |
| `ThamKhao/ChuDeBaoCao_2021.pdf`, `2022.pdf` | Gợi ý đề tài và yêu cầu demo ở Week 10 |
| Hai sách blockchain trong `ThamKhao` | Tài liệu miền ứng dụng tùy chọn, không thay thế phần cloud cốt lõi |

## 8. Nguyên tắc cập nhật so với slide cũ

- Giữ khái niệm nền tảng như abstraction, elasticity, MapReduce, CAP, multi-tenancy, SLA.
- Với tên dịch vụ/giới hạn/SKU, ưu tiên tài liệu nhà cung cấp hiện hành trước khi làm lab.
- Không học thuộc thông số cũ trong slide như giới hạn Blob/SQL hoặc cấu hình role đời đầu.
- `Azure Cognitive Search` hiện được gọi là **Azure AI Search**.
- Bài Azure Disk Encryption cần lưu ý mốc retirement 15/09/2028 và đánh giá hướng mã hóa mới cho workload mới.

## 9. Tài liệu trực tuyến dùng để kiểm tra tính hiện hành

- [Azure Cost Management best practices](https://learn.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices)
- [Azure Resource Manager overview](https://learn.microsoft.com/azure/azure-resource-manager/management/overview)
- [Bicep overview](https://learn.microsoft.com/azure/azure-resource-manager/bicep/overview)
- [Azure Storage SAS](https://learn.microsoft.com/azure/storage/common/storage-sas-overview)
- [Cosmos DB partitioning](https://learn.microsoft.com/azure/cosmos-db/partitioning)
- [VNet peering](https://learn.microsoft.com/azure/virtual-network/virtual-network-peering-overview)
- [Synapse serverless SQL best practices](https://learn.microsoft.com/azure/synapse-analytics/sql/best-practices-serverless-sql-pool)
- [Azure File Sync cloud tiering](https://learn.microsoft.com/azure/storage/file-sync/file-sync-cloud-tiering-overview)
- [Azure AI Search FAQ](https://learn.microsoft.com/azure/search/search-faq-frequently-asked-questions)

---

<span style="color:#0969da"><strong>Bắt đầu từ Week 01, nhưng luôn mở lại file này vào Chủ Nhật để nhìn tiến độ toàn khóa và mốc đồ án tiếp theo.</strong></span>
