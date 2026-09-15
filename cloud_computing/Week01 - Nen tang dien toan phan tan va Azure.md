# Week 01 - Nền tảng điện toán phân tán và làm quen Azure

> 🟦 **Mục tiêu tuần:** Hiểu vì sao Cloud ra đời từ nhu cầu tính toán phân tán; phân biệt cluster, grid và cloud; tạo được tài nguyên Azure đầu tiên mà không mất kiểm soát chi phí.

## 1. Học gì và học khi nào?

| Mốc | Thời lượng | Nội dung | Kết quả cần có |
|---|---:|---|---|
| Thứ Hai, 14/09/2026 | 19:30-21:00 | Điện toán phân tán, cluster, parallel computing | Vẽ được sơ đồ chia một bài toán cho nhiều máy |
| Thứ Tư, 16/09/2026 | 19:30-21:00 | Grid, cloud, utility computing | So sánh được cluster - grid - cloud |
| Thứ Bảy, 19/09/2026 | 08:30-10:30 | Azure account, resource group, VM | Tạo, kiểm tra và xóa một VM thử nghiệm |

## 2. Bức tranh lớn

```mermaid
flowchart LR
    A["Một máy tính"] --> B["Nhiều lõi / xử lý song song"]
    B --> C["Cluster: nhiều máy như một hệ thống"]
    C --> D["Grid: chia sẻ tài nguyên giữa nhiều tổ chức"]
    D --> E["Cloud: tài nguyên theo nhu cầu, đo lường và tự phục vụ"]
```

🟨 **Ý chính:** Cloud không phải một công nghệ đơn lẻ. Nó là **cách tổ chức và cung cấp tài nguyên CNTT như một dịch vụ**, được xây trên mạng, hệ phân tán, ảo hóa và tự động hóa.

## 3. Các khái niệm phải nắm

| Khái niệm | Hiểu đơn giản | Ví dụ thực tế |
|---|---|---|
| **Computing** | Biến dữ liệu đầu vào thành kết quả bằng thuật toán và tài nguyên máy tính. | Ứng dụng bản đồ nhận vị trí hiện tại rồi tính tuyến đường ngắn nhất. |
| **Distributed computing** | Nhiều máy kết nối và chia sẻ các phần việc của một bài toán. | Hệ thống thương mại điện tử tách dịch vụ giỏ hàng, thanh toán và kho lên các máy khác nhau. |
| **Parallel computing** | Nhiều phép tính chạy đồng thời để rút ngắn thời gian. | Chia 10 triệu ảnh thành 100 phần để 100 worker nhận diện ảnh cùng lúc. |
| **Cluster** | Một nhóm máy thường cùng mạng/quản trị, phối hợp như một tài nguyên thống nhất. | Cụm render phim: mỗi node dựng một nhóm khung hình. |
| **Node** | Một máy thành viên của cluster, có CPU, RAM, I/O và hệ điều hành. | Một server 16 CPU/64 GB RAM trong cụm Hadoop là một node. |
| **Shared-memory** | Nhiều luồng/bộ xử lý cùng truy cập một vùng nhớ. | Chương trình xử lý ảnh dùng 8 thread trên cùng máy và cùng đọc mảng pixel trong RAM. |
| **Distributed-memory** | Mỗi máy/tiến trình có vùng nhớ riêng; muốn lấy dữ liệu từ nơi khác phải gửi thông điệp. | Hai máy huấn luyện mô hình trao đổi gradient qua mạng. |
| **Flynn taxonomy** | Phân loại kiến trúc theo số luồng lệnh và luồng dữ liệu: SISD, SIMD, MISD, MIMD. | GPU áp dụng cùng phép toán cho nhiều pixel gần với **SIMD**; cluster chạy nhiều tác vụ khác nhau là **MIMD**. |
| **MPI / message passing** | Chuẩn để các tiến trình trao đổi dữ liệu và đồng bộ trong mô hình bộ nhớ phân tán. | Bài toán dự báo thời tiết chia bản đồ cho nhiều node, sau mỗi bước các node gửi dữ liệu vùng biên cho nhau. |
| **SPMD** | Nhiều tiến trình chạy cùng một chương trình nhưng trên dữ liệu khác nhau. | Mọi worker đều chạy hàm `count_words`, mỗi worker nhận một tệp log khác nhau. |
| **Grid computing** | Chia sẻ tài nguyên giữa nhiều đơn vị theo quy tắc của một tổ chức ảo. | Nhiều trường đại học góp máy để xử lý dữ liệu thiên văn chung. |
| **Virtual Organization (VO)** | Nhóm cá nhân/tổ chức thống nhất ai được dùng tài nguyên nào và trong điều kiện nào. | Ba bệnh viện chia sẻ GPU cho nghiên cứu nhưng chỉ thành viên dự án được gửi job. |
| **Utility computing** | Dùng tài nguyên tính toán giống điện/nước: dùng bao nhiêu, trả bấy nhiêu. | Website bán vé tăng máy chủ trong giờ mở bán, sau đó giảm xuống. |
| **Cloud computing** | Cấp phát tài nguyên qua mạng theo nhu cầu, có đo lường, co giãn và tự động hóa. | Startup tạo VM trong vài phút thay vì mua server và chờ lắp đặt. |
| **Resource group** | Nhóm logic chứa các tài nguyên Azure cần quản lý cùng vòng đời. | VM, disk, IP của bài thực hành cùng nằm trong `rg-is402-w01` để xóa một lần. |

### Cluster, Grid và Cloud khác nhau thế nào?

| Tiêu chí | Cluster | Grid | Cloud |
|---|---|---|---|
| Phạm vi quản trị | Thường một tổ chức | Nhiều tổ chức | Nhà cung cấp vận hành, khách hàng thuê |
| Mức đồng nhất | Thường khá đồng nhất | Có thể rất khác nhau | Bị ẩn sau lớp dịch vụ |
| Cấp phát | Theo quản trị cụm/job queue | Theo thỏa thuận VO | Tự phục vụ, API/portal, theo nhu cầu |
| Ví dụ thực tế | Cụm render nội bộ | Lưới nghiên cứu liên trường | Azure/AWS/GCP |

> 🟥 **Dễ nhầm:** “Nhiều máy” chưa đủ để gọi là cloud. Cloud còn cần **tự phục vụ, co giãn, đo lường mức dùng và lớp dịch vụ chuẩn hóa**.

## 4. Liên hệ bài toán thực tế

**Tình huống:** Một trang bán vé có lượng truy cập bình thường 200 người/phút nhưng tăng lên 20.000 người/phút khi mở bán.

- Dùng một máy mạnh hơn là **scale up**; ví dụ nâng từ 4 lên 32 vCPU.
- Dùng nhiều máy sau load balancer là **scale out**; ví dụ tăng từ 2 lên 50 web server.
- Cloud phù hợp vì có thể cấp thêm máy trong thời gian cao điểm rồi thu hồi.
- Nếu giữ 50 máy chạy cả tháng chỉ để phục vụ 2 giờ cao điểm, hệ thống đúng kỹ thuật nhưng **sai về kinh tế**.

## 5. Trick tối ưu và kiểm soát chi phí

> 🟩 **Quy tắc tuần 1: “Tạo theo nhóm - gắn nhãn - kiểm tra - xóa theo nhóm.”**

1. Dùng một resource group riêng cho mỗi lab: `rg-is402-w01`.
2. Gắn tag tối thiểu: `course=IS402`, `week=01`, `owner=<MSSV>`, `expires=2026-09-19`.
3. Chọn region gần người dùng và có dịch vụ cần dùng; **không nhân bản nhiều region chỉ để thử nghiệm**.
4. Với VM học tập, chọn cấu hình nhỏ/burstable khi phù hợp; cấu hình auto-shutdown ngay lúc tạo.
5. **Stop trong hệ điều hành chưa chắc ngừng tính phí compute**; kiểm tra VM ở trạng thái **Stopped (deallocated)**.
6. Disk, public IP, snapshot và dữ liệu outbound có thể vẫn phát sinh phí sau khi VM dừng. Xóa cả resource group sau lab.
7. Tạo budget/cảnh báo chi phí ngay từ đầu và xem Cost Analysis hằng tuần.

## 6. Thực hành sau buổi học

### Lab chính - Tạo và dọn một VM Azure

**Mục tiêu:** Biết vòng đời `create → observe → stop/deallocate → delete`.

1. Đăng nhập Azure Portal/Cloud Shell bằng tài khoản sinh viên.
2. Tạo resource group `rg-is402-w01` và gắn các tag ở trên.
3. Tạo Linux VM cấu hình nhỏ, bật **auto-shutdown**. Không mở cổng nào ngoài cổng thực sự cần.
4. Ghi lại: region, size, OS image, loại disk, private IP, public IP và ước tính chi phí hiển thị.
5. Mở **Metrics**, quan sát CPU và network trong ít nhất 10 phút.
6. Deallocate VM, kiểm tra trạng thái, sau đó xóa toàn bộ `rg-is402-w01`.
7. Vào Cost Management kiểm tra không còn tài nguyên đang chạy.

**Bằng chứng cần nộp:**

- Ảnh trang Overview của VM và biểu đồ CPU.
- Bảng 6 thông số ở bước 4.
- Ảnh hoặc kết quả xác nhận resource group đã bị xóa.
- Đoạn 5-7 câu giải thích vì sao deallocate khác shutdown bên trong VM.

### Bài luyện tư duy - Chia việc

Với 1.000 tệp log, hãy viết giả mã chia cho 10 worker theo SPMD. Nêu cách xử lý khi worker số 4 hỏng giữa chừng.

**Đáp án định hướng:** Master giữ danh sách partition chưa hoàn thành; mỗi worker chạy cùng hàm xử lý; partition của worker hỏng được đưa lại vào hàng đợi và cấp cho worker còn sống.

## 7. Tự kiểm tra cuối tuần

- [ ] Giải thích được cloud khác cluster ở ít nhất 3 điểm.
- [ ] Phân biệt shared-memory và distributed-memory bằng ví dụ.
- [ ] Giải thích được scale up và scale out.
- [ ] Tạo và xóa được resource group.
- [ ] Không còn VM/disk/public IP của lab.

## 8. Nguồn học tuần này

- Slide: [Buổi 01 - Giới thiệu](../Buoi01_GioiThieu_02.pdf), trang 1-7 và 34.
- Slide: [Lecture 1 - Overview of Distributed Computing](../Lecture%201%20-%20Overview%20of%20Distributed%20Computing.pdf), trang 9-40 và 49-70.
- Giáo trình Marinescu: Chương 1-3 và Phụ lục B.1-B.4.
- Giáo trình Buyya: Chương 1, mục 1.1-1.5.

---

**Một câu cần nhớ:** <span style="color:#d73a49"><strong>Cloud chỉ tạo ra lợi ích khi khả năng co giãn kỹ thuật đi cùng kỷ luật kiểm soát chi phí.</strong></span>
