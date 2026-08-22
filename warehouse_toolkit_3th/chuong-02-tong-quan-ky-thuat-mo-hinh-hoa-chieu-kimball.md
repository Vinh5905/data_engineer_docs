# Chương 2: Tổng quan các kỹ thuật mô hình hóa chiều của Kimball

> Bản trình bày và giải thích bằng tiếng Việt dựa trên Chương 2, trang 37-68 của *The Data Warehouse Toolkit, Third Edition* - Ralph Kimball và Margy Ross. Trong tệp PDF nguồn, nội dung tương ứng với trang vật lý 73-104.
>
> Đây là bản diễn giải đầy đủ các luận điểm, khái niệm và mẫu thiết kế của chương, không phải bản sao từng câu. Thuật ngữ tiếng Anh quan trọng được giữ lại để tiện đối chiếu với sách.

---

## Mục lục Chương 2 - bản dịch tiếng Việt

### Tổng quan các kỹ thuật mô hình hóa chiều của Kimball - trang 37

#### Các khái niệm nền tảng - trang 37

- Thu thập yêu cầu kinh doanh và tìm hiểu thực tế dữ liệu - trang 37
- Các workshop mô hình hóa chiều có sự cộng tác - trang 38
- Quy trình thiết kế mô hình chiều gồm bốn bước - trang 38
- Quy trình kinh doanh - trang 39
- Grain - mức chi tiết - trang 39
- Dimension cung cấp ngữ cảnh mô tả - trang 40
- Fact dùng cho các phép đo - trang 40
- Star schema và OLAP cube - trang 40
- Mở rộng mô hình chiều mà không làm gián đoạn hệ thống - trang 41

#### Các kỹ thuật fact table cơ bản - trang 41

- Cấu trúc fact table - trang 41
- Fact cộng được, cộng được một phần và không cộng được - trang 42
- Giá trị null trong fact table - trang 42
- Conformed facts - các fact có định nghĩa thống nhất - trang 42
- Transaction fact table - trang 43
- Periodic snapshot fact table - trang 43
- Accumulating snapshot fact table - trang 44
- Factless fact table - fact table không có phép đo số - trang 44
- Aggregate fact table hoặc OLAP cube - trang 45
- Consolidated fact table - fact table hợp nhất - trang 45

#### Các kỹ thuật dimension table cơ bản - trang 46

- Cấu trúc dimension table - trang 46
- Surrogate key của dimension - trang 46
- Natural key, durable key và supernatural key - trang 46
- Drill-down - phân tích xuống chi tiết hơn - trang 47
- Degenerate dimension - dimension suy biến - trang 47
- Dimension phẳng, phi chuẩn hóa - trang 47
- Nhiều hệ phân cấp trong một dimension - trang 48
- Biểu diễn cờ và chỉ báo bằng thuộc tính văn bản - trang 48
- Thuộc tính null trong dimension - trang 48
- Calendar Date Dimension - dimension ngày theo lịch - trang 48
- Role-playing dimension - dimension đóng nhiều vai trò - trang 49
- Junk dimension - trang 49
- Snowflaked dimension - dimension dạng bông tuyết - trang 50
- Outrigger dimension - trang 50

#### Tích hợp thông qua conformed dimensions - trang 50

- Conformed dimensions - trang 51
- Shrunken dimensions - dimension thu gọn - trang 51
- Drill-across - phân tích xuyên qua nhiều fact table - trang 51
- Value chain - chuỗi giá trị - trang 52
- Kiến trúc Enterprise Data Warehouse Bus - trang 52
- Ma trận Enterprise Data Warehouse Bus - trang 52
- Ma trận bus triển khai chi tiết - trang 53
- Ma trận cơ hội và bên liên quan - trang 53

#### Xử lý các thuộc tính dimension thay đổi chậm - trang 53

- Type 0: Giữ nguyên giá trị ban đầu - trang 54
- Type 1: Ghi đè - trang 54
- Type 2: Thêm dòng mới - trang 54
- Type 3: Thêm thuộc tính mới - trang 55
- Type 4: Thêm mini-dimension - trang 55
- Type 5: Thêm mini-dimension và Type 1 outrigger - trang 55
- Type 6: Thêm thuộc tính Type 1 vào Type 2 dimension - trang 56
- Type 7: Dimension hỗ trợ đồng thời Type 1 và Type 2 - trang 56

#### Xử lý hệ phân cấp trong dimension - trang 56

- Hệ phân cấp có độ sâu cố định và vị trí xác định - trang 56
- Hệ phân cấp hơi không đều hoặc có độ sâu biến đổi nhỏ - trang 57
- Hệ phân cấp không đều với hierarchy bridge table - trang 57
- Hệ phân cấp không đều với thuộc tính pathstring - trang 57

#### Các kỹ thuật fact table nâng cao - trang 58

- Surrogate key của fact table - trang 58
- Centipede fact table - fact table "con rết" - trang 58
- Giá trị số nên là thuộc tính hay fact - trang 59
- Fact biểu diễn độ trễ hoặc khoảng thời gian - trang 59
- Header/line fact table - trang 59
- Allocated facts - phân bổ fact - trang 60
- Fact table lãi lỗ sử dụng phân bổ - trang 60
- Fact sử dụng nhiều loại tiền tệ - trang 60
- Fact sử dụng nhiều đơn vị đo - trang 61
- Fact lũy kế từ đầu năm - trang 61
- SQL nhiều lượt để tránh nối trực tiếp các fact table - trang 61
- Theo dõi khoảng thời gian hiệu lực trong fact table - trang 62
- Late-arriving facts - fact đến trễ - trang 62

#### Các kỹ thuật dimension nâng cao - trang 62

- Join giữa các dimension table - trang 62
- Dimension đa trị và bridge table - trang 63
- Bridge table đa trị thay đổi theo thời gian - trang 63
- Chuỗi thời gian của các nhãn hành vi - trang 63
- Nhóm nghiên cứu hành vi - trang 64
- Đưa fact tổng hợp vào làm thuộc tính dimension - trang 64
- Phân dải giá trị động - trang 64
- Dimension lưu bình luận văn bản - trang 65
- Xử lý nhiều múi giờ - trang 65
- Measure type dimension - dimension loại phép đo - trang 65
- Step dimension - dimension bước trong quy trình - trang 65
- Hot-swappable dimensions - dimension có thể thay thế động - trang 66
- Abstract generic dimensions - dimension chung mang tính trừu tượng - trang 66
- Audit dimension - dimension kiểm toán - trang 66
- Late-arriving dimensions - dimension đến trễ - trang 67

#### Các schema dùng cho mục đích đặc biệt - trang 67

- Schema supertype và subtype dành cho các sản phẩm không đồng nhất - trang 67
- Real-time fact table - fact table thời gian thực - trang 68
- Error event schema - schema ghi nhận sự kiện lỗi - trang 68

---

## 1. Chương 2 thực chất là gì?

Chương 2 không phải một bài học tuần tự như Chương 1. Nó là danh mục chính thức các mẫu thiết kế mô hình chiều của Kimball.

Mỗi kỹ thuật được:

- Định nghĩa ngắn gọn.
- Giải thích vấn đề nó giải quyết.
- Kèm tham chiếu đến các chương có ví dụ thực tế.

Tác giả không kỳ vọng người đọc mới sẽ đọc liên tục từ đầu đến cuối ngay lần đầu. Chương này được thiết kế giống một cuốn từ điển mẫu thiết kế để quay lại tra cứu.

Nó cho thấy mô hình hóa chiều không chỉ gồm fact table, dimension table và star schema. Hệ thống kỹ thuật Kimball còn xử lý:

- Dữ liệu thay đổi theo thời gian.
- Nhiều grain khác nhau.
- Nhiều loại fact table.
- Dimension đa trị.
- Hệ phân cấp không đều.
- Dữ liệu đến trễ.
- Đa tiền tệ và đa đơn vị.
- Tích hợp nhiều quy trình kinh doanh.
- Dữ liệu thời gian thực.
- Quản trị chất lượng dữ liệu.

---

# Phần I: Các khái niệm nền tảng

## 2. Yêu cầu kinh doanh và thực tế dữ liệu

Một mô hình tốt phải nằm ở giao điểm của hai phía:

```text
Doanh nghiệp cần gì?  <->  Dữ liệu thực tế cho phép làm gì?
```

### Tìm hiểu yêu cầu kinh doanh

Nhóm thiết kế cần làm việc với đại diện nghiệp vụ để hiểu:

- Mục tiêu kinh doanh.
- KPI quan trọng.
- Vấn đề cấp bách.
- Quy trình ra quyết định.
- Các nhu cầu phân tích hỗ trợ quyết định.

### Tìm hiểu thực tế dữ liệu

Đồng thời, nhóm phải làm việc với chuyên gia hệ thống nguồn và thực hiện data profiling ở mức tổng quan để xác định:

- Dữ liệu có tồn tại không?
- Có đủ mức chi tiết cần thiết không?
- Chất lượng ra sao?
- Khóa có ổn định không?
- Lịch sử có được lưu không?
- Có thể kết nối những nguồn nào?
- Việc cung cấp dữ liệu có khả thi không?

Nếu chỉ nghe yêu cầu kinh doanh mà không kiểm tra dữ liệu, nhóm có thể hứa một hệ thống không thể xây dựng. Nếu chỉ nhìn dữ liệu hiện có, nhóm có thể xây một hệ thống đúng về kỹ thuật nhưng không tạo ra giá trị.

## 3. Workshop mô hình hóa có sự cộng tác

Mô hình chiều không nên do một data modeler ngồi một mình thiết kế.

Những người cần tham gia gồm:

- Data modeler.
- Chuyên gia nghiệp vụ.
- Đại diện quản trị dữ liệu.
- Chuyên gia hệ thống nguồn.
- Khi cần, các nhà phát triển ETL và BI.

Data modeler điều phối và chịu trách nhiệm về phương pháp thiết kế, nhưng các định nghĩa nghiệp vụ phải được hình thành trong các workshop tương tác cao.

Một mô hình có thể hợp lệ về kỹ thuật nhưng vẫn sai về nghiệp vụ. Chẳng hạn, "khách hàng", "đơn hàng hoàn tất" hoặc "doanh thu thuần" có thể được các bộ phận hiểu khác nhau. Workshop cũng giúp doanh nghiệp chấp nhận mô hình vì họ trực tiếp tham gia xây dựng nó.

## 4. Quy trình thiết kế bốn bước

Mọi mô hình chiều bắt đầu bằng bốn quyết định theo đúng thứ tự:

1. Chọn quy trình kinh doanh.
2. Tuyên bố grain.
3. Xác định các dimension.
4. Xác định các fact.

Thứ tự rất quan trọng. Đặc biệt, không nên lựa chọn fact và dimension trước khi tuyên bố grain.

Sau bốn quyết định này, nhóm mới tiếp tục xác định:

- Tên bảng.
- Tên cột.
- Các giá trị mẫu.
- Định nghĩa nghiệp vụ.
- Quy tắc chất lượng.
- Quy tắc ETL.

## 5. Chọn quy trình kinh doanh

Quy trình kinh doanh là hoạt động vận hành tạo ra sự kiện hoặc trạng thái có thể đo lường, chẳng hạn:

- Nhận đơn hàng.
- Bán một sản phẩm.
- Xử lý yêu cầu bảo hiểm.
- Đăng ký sinh viên vào lớp.
- Chụp số dư tài khoản cuối tháng.
- Giao một kiện hàng.

Mỗi quy trình tạo ra các phép đo riêng và thường dẫn đến ít nhất một fact table riêng.

Không nên bắt đầu bằng phòng ban. "Phòng marketing" không phải quy trình kinh doanh; "chiến dịch marketing", "tương tác khách hàng" hoặc "bán hàng" mới là các quy trình có thể tạo fact.

Mỗi quy trình kinh doanh tương ứng với một hàng trong bus matrix của doanh nghiệp.

## 6. Grain - hợp đồng của fact table

Grain xác định chính xác một dòng fact table đại diện cho điều gì.

Ví dụ:

- Một dòng cho mỗi sản phẩm trên mỗi hóa đơn.
- Một dòng cho mỗi tài khoản vào cuối mỗi ngày.
- Một dòng cho mỗi yêu cầu bảo hiểm từ khi mở đến khi đóng.
- Một dòng cho mỗi lần học sinh tham dự một buổi học.

Grain được xem như một **hợp đồng ràng buộc**. Mọi dimension và fact sau đó phải phù hợp với grain.

Ví dụ, nếu grain là "một sản phẩm trên một hóa đơn":

- Số lượng sản phẩm bán phù hợp.
- Doanh thu của dòng hàng phù hợp.
- Sản phẩm phù hợp.
- Khách hàng của hóa đơn phù hợp.
- Tổng lương tháng của quản lý cửa hàng không phù hợp.

Kimball khuyên bắt đầu với **atomic grain**, tức mức chi tiết thấp nhất mà quy trình nguồn ghi nhận được. Dữ liệu nguyên tử linh hoạt nhất trước những câu hỏi chưa dự đoán.

Nếu cần một grain tổng hợp khác để tăng hiệu năng, phải tạo fact table vật lý riêng. Không trộn hai grain trong cùng bảng.

## 7. Dimension và fact

### Dimension

Dimension cung cấp ngữ cảnh "ai, cái gì, ở đâu, khi nào, tại sao và bằng cách nào" cho sự kiện.

Dimension chứa thuộc tính dùng để:

- Lọc.
- Phân nhóm.
- Drill-down.
- Đặt nhãn cho báo cáo.

Trong một quan hệ fact-dimension thông thường, mỗi dimension nên có đúng một giá trị tương ứng với một dòng fact.

Tác giả gọi dimension là "linh hồn" của data warehouse vì trải nghiệm phân tích phụ thuộc rất lớn vào độ phong phú, chính xác và dễ hiểu của các thuộc tính dimension.

### Fact

Fact là phép đo tạo ra từ sự kiện kinh doanh và gần như luôn là số.

Một dòng fact phải tương ứng một-một với sự kiện được grain mô tả. Fact table được thiết kế theo sự kiện thực tế, không được thiết kế chỉ để đáp ứng một báo cáo cụ thể.

## 8. Star schema, OLAP cube và khả năng mở rộng

Star schema là mô hình chiều triển khai trong RDBMS. OLAP cube là mô hình chiều triển khai trong cơ sở dữ liệu đa chiều và thường được xây từ star schema.

Cube có thể cung cấp các ngôn ngữ phân tích mạnh hơn SQL, chẳng hạn MDX, và thường là lớp cuối hoặc lớp tổng hợp của hệ thống.

Một mô hình chiều được thiết kế tốt có thể mở rộng mà không làm thay đổi kết quả của báo cáo hiện tại:

- Thêm fact mới nếu cùng grain.
- Thêm dimension mới nếu không làm thay đổi grain.
- Thêm thuộc tính mới vào dimension.
- Hạ grain xuống chi tiết hơn nếu giữ được ý nghĩa và tên của các cột hiện có.

Đây là **graceful extensibility**: hệ thống tiếp nhận thêm dữ liệu mà không phá vỡ ứng dụng cũ.

---

# Phần II: Các kỹ thuật fact table cơ bản

## 9. Cấu trúc fact table

Ở mức nguyên tử, một dòng fact table tương ứng với một sự kiện đo lường trong thế giới thực.

Fact table thường chứa:

- Các phép đo số.
- Foreign key tới dimension.
- Degenerate dimension key nếu có.
- Timestamp khi cần độ chính xác cao.
- Có thể có surrogate key riêng cho fact row.

Fact table là mục tiêu chính của các phép `SUM`, `COUNT`, `MIN`, `MAX`, `AVG` và những tính toán động trong truy vấn.

## 10. Additive, semi-additive và non-additive facts

### Additive fact

Cộng được theo mọi dimension, ví dụ doanh thu, số lượng bán hoặc chi phí.

### Semi-additive fact

Cộng được theo một số dimension nhưng không phải tất cả.

Ví dụ số dư tài khoản:

- Có thể cộng số dư của nhiều tài khoản trong cùng ngày.
- Không thể cộng số dư của cùng tài khoản qua nhiều ngày.

### Non-additive fact

Không thể cộng trực tiếp, chẳng hạn tỷ lệ, phần trăm hoặc đơn giá.

Nếu có thể, nên lưu các thành phần cộng được rồi tính tỷ lệ sau khi tổng hợp. Thay vì chỉ lưu tỷ lệ chuyển đổi 20%, nên lưu số người mua và số người truy cập. Sau đó tính:

```text
SUM(số người mua) / SUM(số người truy cập)
```

Không nên lấy trung bình trực tiếp của các tỷ lệ nếu mẫu số của từng nhóm khác nhau.

## 11. Null trong fact table

Phép đo null thường không gây vấn đề lớn vì các hàm tổng hợp SQL biết cách xử lý null.

Tuy nhiên, foreign key trong fact table không nên là null vì sẽ phá vỡ toàn vẹn tham chiếu.

Thay vào đó, dimension phải có các dòng mặc định như:

- Unknown - chưa biết.
- Not Applicable - không áp dụng.
- Not Yet Available - chưa có thông tin.
- Error - dữ liệu lỗi.

Fact table sẽ tham chiếu surrogate key của dòng mặc định tương ứng.

## 12. Conformed facts

Nếu cùng một phép đo xuất hiện trong nhiều fact table, định nghĩa kỹ thuật và nghiệp vụ của nó phải được kiểm tra.

Nếu chúng thực sự tương thích:

- Đặt cùng tên.
- Cho phép so sánh hoặc kết hợp.

Nếu chúng khác nhau:

- Đặt tên khác nhau.
- Không để người dùng hiểu lầm rằng hai chỉ số có thể so sánh trực tiếp.

Ví dụ, `Gross Sales Amount` và `Net Sales Amount` không nên cùng được gọi là `Sales Amount`.

## 13. Ba loại fact table chính

| Loại | Một dòng đại diện cho | Cách cập nhật | Độ thưa |
|---|---|---|---|
| Transaction | Một sự kiện tại một thời điểm | Thường chỉ insert | Chỉ có dòng khi sự kiện xảy ra |
| Periodic snapshot | Trạng thái hoặc tổng hợp trong một chu kỳ | Thêm dòng mỗi chu kỳ | Thường dày, kể cả không có hoạt động |
| Accumulating snapshot | Một quy trình từ đầu đến cuối | Insert rồi update cùng dòng | Một dòng cho mỗi đối tượng quy trình |

### 13.1. Transaction fact table

Một dòng tương ứng với một giao dịch hoặc sự kiện tại một điểm trong không gian và thời gian.

Ví dụ:

- Một dòng sản phẩm được bán.
- Một cuộc gọi.
- Một lần thanh toán.
- Một lượt khám.

Transaction fact table ở grain nguyên tử thường có nhiều dimension nhất, do đó linh hoạt nhất cho slicing và dicing. Không có giao dịch thì không có dòng.

### 13.2. Periodic snapshot fact table

Một dòng tóm tắt trạng thái hoặc hoạt động trong một khoảng tiêu chuẩn như ngày, tuần hoặc tháng.

Ví dụ grain:

> Một dòng cho mỗi tài khoản vào cuối mỗi ngày.

Ngay cả khi không có giao dịch trong ngày, vẫn có thể thêm một dòng với các fact bằng 0 hoặc null. Vì vậy periodic snapshot thường dày hơn transaction fact.

### 13.3. Accumulating snapshot fact table

Dùng cho quy trình có điểm bắt đầu rõ ràng, các mốc trung gian có thể dự đoán và điểm kết thúc rõ ràng.

Ví dụ:

```text
Đặt hàng -> Phê duyệt -> Đóng gói -> Gửi đi -> Giao hàng
```

Fact table có foreign key ngày cho từng mốc. Dòng được tạo khi quy trình bắt đầu, sau đó được cập nhật mỗi khi đạt mốc mới.

Đây là loại fact table đặc biệt vì cùng một dòng bị cập nhật nhiều lần. Nó thường chứa ngày của từng mốc, khoảng thời gian giữa các mốc, bộ đếm mốc đã hoàn thành và các dimension mô tả quy trình.

## 14. Factless fact table

Factless fact table không có phép đo số nhưng vẫn ghi nhận một sự kiện có ý nghĩa.

Ví dụ một sinh viên tham dự lớp học:

```text
Ngày + Sinh viên + Giáo viên + Phòng + Môn học
```

Bản thân sự kết hợp này đã là sự kiện. Có thể dùng `COUNT(*)` để đếm số lần tham dự.

### Phân tích điều không xảy ra

Có thể sử dụng hai factless fact table:

1. Coverage table chứa tất cả sự kiện có thể xảy ra.
2. Activity table chứa sự kiện thực tế đã xảy ra.

Lấy coverage trừ activity sẽ tìm được các sự kiện không xảy ra, chẳng hạn sản phẩm được lên kế hoạch khuyến mãi nhưng không bán được hoặc học sinh đăng ký nhưng không tham dự.

## 15. Aggregate fact table và OLAP cube

Aggregate fact table chứa số liệu tổng hợp từ fact table nguyên tử chỉ để tăng hiệu năng.

Ví dụ:

- Fact nguyên tử: một dòng sản phẩm trên một giao dịch.
- Aggregate fact: doanh số theo ngày, cửa hàng và sản phẩm.

BI tool nên tự chọn bảng phù hợp thông qua **aggregate navigation**. Người dùng không cần biết truy vấn đang chạy trên bảng nguyên tử hay bảng tổng hợp.

Aggregate nên hoạt động giống chỉ mục: giúp truy vấn nhanh hơn, không làm thay đổi kết quả và không trở thành nguồn dữ liệu độc lập mà người dùng phải tự chọn.

Aggregate fact table phải dùng các **shrunken conformed dimensions** tương thích với dimension nguyên tử.

## 16. Consolidated fact table

Nếu fact từ nhiều quy trình có thể biểu diễn ở cùng grain, có thể hợp nhất chúng vào một bảng.

Ví dụ doanh số thực tế và doanh số dự báo có thể ở cùng grain `tháng + sản phẩm + khu vực`. Khi đó chúng có thể nằm trong consolidated fact table để phân tích actual-versus-forecast nhanh hơn.

Đánh đổi:

- ETL phức tạp hơn.
- BI đơn giản và nhanh hơn.

Chỉ nên làm khi các phép đo từ nhiều quy trình thường xuyên được phân tích cùng nhau.

---

# Phần III: Các kỹ thuật dimension cơ bản

## 17. Cấu trúc dimension table

Dimension table thường:

- Có một primary key.
- Rộng, phẳng và phi chuẩn hóa.
- Có nhiều thuộc tính văn bản.
- Chứa mô tả dễ hiểu thay vì mã bí ẩn.
- Được dùng chủ yếu trong điều kiện lọc, `GROUP BY` và nhãn báo cáo.

Các mã vận hành có thể được giữ lại, nhưng nên đi kèm mô tả đầy đủ.

## 18. Surrogate, natural và durable key

### Natural key

Khóa được tạo bởi hệ thống vận hành, chẳng hạn mã nhân viên, mã sản phẩm hoặc số tài khoản.

Natural key nằm ngoài quyền kiểm soát của DW/BI. Nó có thể thay đổi, bị tái sử dụng, trùng giữa nhiều hệ thống hoặc được quản lý không nhất quán.

### Surrogate key

Khóa số nguyên vô nghĩa do data warehouse tự cấp, thường tăng tuần tự:

```text
1, 2, 3, 4, ...
```

Dimension cần surrogate key vì một natural key có thể có nhiều dòng lịch sử Type 2.

| CustomerKey | CustomerID | City |
|---:|---|---|
| 101 | C001 | Hà Nội |
| 247 | C001 | Đà Nẵng |

Cùng khách hàng `C001`, nhưng hai phiên bản lịch sử có surrogate key khác nhau.

Date Dimension là ngoại lệ phổ biến: có thể dùng khóa thông minh dạng `YYYYMMDD`.

### Durable hoặc supernatural key

Đây là khóa bền vững đại diện cho cùng một thực thể xuyên suốt mọi thay đổi.

Một nhân viên nghỉ việc rồi quay lại có thể được hệ thống nguồn cấp mã nhân viên mới. Data warehouse có thể gán một durable key duy nhất để nhận ra đây vẫn là cùng một người.

Một thực thể có thể có nhiều surrogate key cho các phiên bản Type 2 nhưng chỉ có một durable key không đổi suốt đời.

## 19. Drill-down và drill-across

### Drill-down

Drill-down là thêm một dimension attribute vào mức phân nhóm của truy vấn:

```text
Doanh thu theo năm
-> Doanh thu theo năm và tháng
-> Doanh thu theo năm, tháng và sản phẩm
```

Về SQL, đây chủ yếu là thêm cột vào `SELECT` và `GROUP BY`. Drill-down không nhất thiết phải đi theo hierarchy dựng sẵn; có thể thêm thuộc tính từ bất kỳ dimension nào nối với fact table.

### Drill-across

Drill-across truy vấn riêng từng fact table bằng các conformed attributes giống nhau, sau đó ghép các tập kết quả đã tổng hợp.

Ví dụ:

```text
Doanh số theo tháng và sản phẩm
Dự báo theo tháng và sản phẩm
```

Hai kết quả có thể được ghép theo `tháng + sản phẩm`. Không được join trực tiếp hai fact table trên foreign key của chúng.

## 20. Degenerate dimension

Degenerate dimension là dimension chỉ còn khóa nghiệp vụ nhưng không có bảng dimension riêng.

Ví dụ số hóa đơn:

- Một hóa đơn có nhiều dòng.
- Các thuộc tính khách hàng, ngày và cửa hàng đã được đưa vào dimension khác.
- Số hóa đơn vẫn cần để nhóm các dòng thuộc cùng giao dịch.
- Nhưng không còn thuộc tính mô tả riêng đáng để tạo Invoice Dimension.

Khi đó `InvoiceNumber` nằm trực tiếp trong fact table và được gọi là degenerate dimension. Nó phổ biến trong transaction và accumulating snapshot fact table.

## 21. Dimension phẳng và snowflake

Kimball ưu tiên dimension phẳng, phi chuẩn hóa.

Product Dimension nên trực tiếp chứa:

```text
Product -> Brand -> Category -> Department
```

dưới dạng các cột trên cùng một dòng.

### Snowflake dimension

Snowflake tách các cấp thành bảng riêng:

```text
Product -> Brand table -> Category table -> Department table
```

Mặc dù mô tả chính xác quan hệ phân cấp, snowflake khó hiểu hơn, cần nhiều join hơn, có thể làm truy vấn chậm và không chứa thêm thông tin so với dimension phẳng. Vì vậy nên tránh trong phần lớn trường hợp.

## 22. Nhiều hierarchy trong một dimension

Một dimension có thể chứa đồng thời nhiều hệ phân cấp.

Date Dimension có thể hỗ trợ:

```text
Ngày -> Tháng -> Quý -> Năm dương lịch
```

và:

```text
Ngày -> Tuần -> Kỳ tài chính -> Năm tài chính
```

Các hierarchy này có thể cùng tồn tại dưới dạng các nhóm thuộc tính trong một dimension.

## 23. Flags, indicators và null attributes

Các giá trị như `Y/N`, `0/1`, `A/I` hoặc `P/C` nên được bổ sung bằng mô tả văn bản như Có/Không, Active/Inactive hoặc Pending/Completed.

Nếu mã chứa nhiều phần có ý nghĩa, nên tách từng ý nghĩa thành thuộc tính riêng.

Đối với thuộc tính dimension bị thiếu, nên dùng chuỗi mô tả như `Unknown`, `Not Applicable` hoặc `Not Yet Available`. Không nên để null vì các hệ quản trị có thể xử lý việc lọc và nhóm null khác nhau.

## 24. Date Dimension

Hầu hết fact table đều cần Date Dimension.

Nó chứa sẵn các thuộc tính như:

- Ngày.
- Thứ.
- Số tuần.
- Tên tháng.
- Quý.
- Năm.
- Kỳ tài chính.
- Ngày lễ.
- Ngày làm việc hoặc cuối tuần.
- Mùa hoặc chiến dịch kinh doanh.

Không nên tính những thông tin như ngày lễ bằng SQL trong từng báo cáo. Hãy tra cứu chúng trong Date Dimension.

Date key có thể là số nguyên `YYYYMMDD`, nhưng vẫn phải có dòng đặc biệt cho ngày chưa biết hoặc chưa xác định.

Nếu cần giờ phút giây chính xác, lưu timestamp trực tiếp trong fact table. Nếu người dùng cần nhóm theo ca, khung giờ hoặc phần trong ngày, thêm Time-of-Day Dimension.

## 25. Role-playing dimension

Một dimension vật lý có thể đóng nhiều vai trò trong cùng fact table.

Ví dụ Order Fact có:

- Order Date.
- Ship Date.
- Delivery Date.
- Payment Date.

Tất cả tham chiếu cùng Date Dimension, nhưng mỗi vai trò phải được trình bày qua view riêng với tên thuộc tính rõ ràng như `Order Month`, `Ship Month` và `Delivery Month`.

## 26. Junk dimension

Một giao dịch thường có nhiều cờ và chỉ báo nhỏ:

- Đơn hàng ưu tiên?
- Có quà tặng?
- Có dùng coupon?
- Khách lần đầu?
- Kênh đặt hàng?
- Trạng thái xác minh?

Không nên tạo một dimension riêng cho mỗi cờ. Có thể gom chúng vào một **junk dimension**, đôi khi gọi là Transaction Profile Dimension.

Junk dimension chỉ cần lưu những tổ hợp thực sự xuất hiện, không bắt buộc tạo tích Descartes của mọi giá trị có thể có.

## 27. Outrigger dimension

Outrigger xuất hiện khi một dimension tham chiếu đến dimension khác. Ví dụ Account Dimension tham chiếu Date Dimension để mô tả ngày mở tài khoản.

Outrigger được phép nhưng nên dùng ít. Trong nhiều trường hợp, quan hệ giữa hai dimension nên được đưa xuống fact table để cả hai trở thành foreign key độc lập.

Lý do là outrigger, đặc biệt khi kết hợp với Type 2, có thể làm dimension chính tăng số dòng rất nhanh.

---

# Phần IV: Tích hợp dữ liệu bằng conformed dimensions

## 28. Conformed dimension

Hai dimension conform khi các thuộc tính dùng chung có:

- Cùng tên cột.
- Cùng định nghĩa.
- Cùng tập giá trị.
- Cùng ý nghĩa nghiệp vụ.

Conformed dimensions được định nghĩa một lần với sự tham gia của data governance rồi tái sử dụng trong nhiều fact table.

Lợi ích:

- Kết quả nhất quán.
- Có thể phân tích nhiều quy trình cùng nhau.
- Không phải xây lại cùng dimension.
- Giảm chi phí phát triển về sau.

Đây là nền tảng tích hợp trong kiến trúc Kimball.

## 29. Shrunken dimension

Shrunken dimension là tập con của một dimension cơ sở: ít dòng hơn, ít cột hơn hoặc cả hai.

Nó được dùng khi fact table có grain cao hơn.

Ví dụ:

- Sales Fact: `ngày + SKU`.
- Forecast Fact: `tháng + thương hiệu`.

Forecast không thể dùng trực tiếp Date Dimension ở grain ngày và Product Dimension ở grain SKU. Nó cần Month Dimension thu gọn từ Date Dimension và Brand Dimension thu gọn từ Product Dimension.

Các dimension thu gọn vẫn phải conform với dimension gốc.

## 30. Value chain và bus architecture

Value chain mô tả luồng tự nhiên của các quy trình chính.

Ví dụ bán lẻ:

```text
Mua hàng -> Nhập kho -> Tồn kho -> Bán hàng
```

Mỗi bước có sự kiện, grain, fact, tần suất và dimension riêng hoặc dùng chung. Do đó, mỗi bước thường cần ít nhất một fact table nguyên tử.

### Enterprise Data Warehouse Bus Architecture

Bus architecture cho phép xây data warehouse theo từng phần:

- Mỗi phần tập trung vào một quy trình.
- Các phần tích hợp nhờ conformed dimensions.
- Có thể phát triển Agile và tăng dần.
- Không phụ thuộc nền tảng RDBMS hay OLAP.

### Bus matrix

Bus matrix có hàng là quy trình kinh doanh, cột là dimension và ô đánh dấu dimension có liên quan đến quy trình hay không.

| Quy trình | Ngày | Sản phẩm | Khách hàng | Cửa hàng |
|---|---:|---:|---:|---:|
| Bán hàng | ✓ | ✓ | ✓ | ✓ |
| Tồn kho | ✓ | ✓ |  | ✓ |
| Mua hàng | ✓ | ✓ |  |  |

Đọc theo hàng giúp thiết kế một quy trình. Đọc theo cột giúp tìm các dimension phải được chuẩn hóa giữa nhiều quy trình. Mỗi vòng triển khai nên tập trung vào một hàng của ma trận.

### Detailed implementation bus matrix

Phiên bản chi tiết mở rộng mỗi quy trình thành các fact table cụ thể, grain của từng bảng, danh sách fact và các dimension tham gia.

### Opportunity/stakeholder matrix

Thay các cột dimension bằng các bộ phận hoặc nhóm liên quan như marketing, kinh doanh, tài chính và vận hành. Ma trận này giúp xác định ai cần tham gia workshop của từng quy trình.

---

# Phần V: Slowly Changing Dimensions

Slowly Changing Dimension - SCD - giải quyết câu hỏi:

> Khi thuộc tính của một thực thể thay đổi, dữ liệu lịch sử phải được nhìn theo giá trị cũ hay giá trị mới?

Một dimension có thể sử dụng nhiều kỹ thuật SCD cho những thuộc tính khác nhau.

## 31. Tổng quan Type 0 đến Type 7

| Type | Cách xử lý | Có giữ lịch sử? | Góc nhìn chính |
|---|---|---:|---|
| 0 | Không thay đổi | Giữ giá trị ban đầu | Original |
| 1 | Ghi đè giá trị cũ | Không | As-is |
| 2 | Thêm dòng mới | Có đầy đủ | As-was |
| 3 | Thêm cột lưu giá trị thay thế | Có giới hạn | Hiện tại và một thực tại khác |
| 4 | Tách nhóm thuộc tính ra mini-dimension | Có qua fact | Thuộc tính thay đổi nhanh |
| 5 | Type 4 cộng tham chiếu current Type 1 | Có | Lịch sử và hiện tại |
| 6 | Type 2 cộng các cột current Type 1 | Có | Lịch sử và hiện tại |
| 7 | Hai cách join Type 1 và Type 2 | Có | As-is và as-was linh hoạt |

## 32. Type 0: giữ nguyên

Giá trị không bao giờ được thay đổi.

Phù hợp với:

- Thuộc tính có chữ "original".
- Điểm tín dụng ban đầu.
- Ngày đăng ký ban đầu.
- Durable identifier.
- Phần lớn thuộc tính của Date Dimension.

Mọi fact luôn được nhóm theo giá trị đầu tiên.

## 33. Type 1: ghi đè

Giá trị cũ bị thay thế bởi giá trị mới.

```text
Tên cũ: Công ty ABCD
Tên mới: Công ty ABC
```

Sau khi cập nhật, lịch sử cũng hiển thị tên mới. Giá trị cũ biến mất.

Ưu điểm:

- Dễ triển khai.
- Không tạo thêm dòng.

Nhược điểm:

- Phá hủy lịch sử.
- Aggregate và OLAP cube liên quan có thể phải tính lại.

Type 1 phù hợp với sửa lỗi hoặc thuộc tính không cần phân tích lịch sử.

## 34. Type 2: thêm dòng mới

Khi thuộc tính thay đổi:

1. Kết thúc hiệu lực dòng cũ.
2. Tạo dòng dimension mới.
3. Gán surrogate key mới.
4. Fact mới tham chiếu dòng mới.

| CustomerKey | CustomerID | City | Effective | Expiration | Current |
|---:|---|---|---|---|---|
| 101 | C001 | Hà Nội | 2022-01-01 | 2024-05-31 | N |
| 247 | C001 | Đà Nẵng | 2024-06-01 | 9999-12-31 | Y |

Fact cũ tiếp tục nối với `101`; fact mới nối với `247`.

Type 2 cần ít nhất:

- Effective timestamp.
- Expiration timestamp.
- Current row indicator.

Đây là phương pháp chuẩn để giữ đúng bối cảnh lịch sử tại thời điểm sự kiện xảy ra.

## 35. Type 3: thêm thuộc tính mới

Dimension lưu đồng thời giá trị hiện tại và một giá trị thay thế.

| CustomerKey | CurrentRegion | PreviousRegion |
|---:|---|---|
| 101 | Miền Trung | Miền Bắc |

Người dùng có thể phân tích theo cả cách phân vùng hiện tại và phân vùng trước đó.

Type 3 chỉ giữ một số lượng lịch sử giới hạn, thường là một thực tại thay thế, nên ít được dùng hơn Type 2.

## 36. Type 4: mini-dimension

Nếu một nhóm thuộc tính thay đổi quá nhanh trong dimension rất lớn, tách chúng thành mini-dimension.

Ví dụ Customer Dimension có các thuộc tính hành vi thay đổi thường xuyên:

- Nhóm thu nhập.
- Nhóm điểm tín dụng.
- Nhóm mức chi tiêu.
- Nhóm rủi ro.

Fact table lưu cả Customer Key và Customer Profile Mini-Dimension Key. Nhờ vậy, không phải tạo hàng loạt phiên bản Type 2 của Customer Dimension khổng lồ.

Mini-dimension cũng hữu ích cho nhóm thuộc tính được truy vấn nhiều trong một dimension hàng triệu dòng, kể cả khi chúng không thay đổi quá nhanh.

## 37. Type 5

Type 5 xây trên Type 4:

- Fact table vẫn tham chiếu mini-dimension để giữ lịch sử.
- Base dimension đồng thời chứa tham chiếu Type 1 tới mini-dimension hiện tại.

Nhờ đó có thể:

- Xem thuộc tính lịch sử đúng tại thời điểm fact xảy ra.
- Xem toàn bộ fact theo hồ sơ hiện tại.
- Truy cập hồ sơ hiện tại trực tiếp từ base dimension mà không cần đi qua fact table.

Tham chiếu hiện tại phải được ETL ghi đè khi hồ sơ thay đổi.

## 38. Type 6

Type 6 xây trên Type 2 nhưng bổ sung các cột Type 1 biểu diễn giá trị hiện tại.

Mỗi dòng Type 2 có:

- Giá trị lịch sử đúng tại thời điểm dòng có hiệu lực.
- Giá trị hiện tại được ghi đè trên tất cả phiên bản cùng durable key.

Người dùng có thể phân tích fact theo bối cảnh lúc sự kiện xảy ra hoặc phân loại lại toàn bộ lịch sử theo giá trị hiện tại.

## 39. Type 7

Type 7 đặt cả hai khóa trong fact table:

- Surrogate key của phiên bản Type 2.
- Durable key của thực thể.

Hai view được cung cấp:

- **As-was:** join bằng surrogate key để xem đúng bối cảnh lịch sử.
- **As-is:** join bằng durable key với dòng `Current = Y` để xem toàn bộ lịch sử theo thuộc tính hiện tại.

Đây là cách rõ ràng và linh hoạt để hỗ trợ đồng thời hai góc nhìn.

---

# Phần VI: Xử lý hierarchy

## 40. Fixed-depth positional hierarchy

Nếu hierarchy có số cấp cố định, quan hệ nhiều-một rõ ràng và mỗi cấp có tên ổn định, hãy đưa từng cấp thành một thuộc tính trong dimension.

```text
Product -> Brand -> Category -> Department
```

Đây là cách dễ hiểu và có hiệu năng tốt nhất.

## 41. Slightly ragged hierarchy

Nếu số cấp chỉ biến đổi nhẹ, chẳng hạn hierarchy địa lý có từ ba đến sáu cấp, có thể ép vào số cột bằng mức sâu tối đa.

Các cấp thiếu được điền theo quy tắc nghiệp vụ, chẳng hạn lặp lại cấp cha, dùng `Not Applicable` hoặc đẩy giá trị lên vị trí chuẩn.

Cách này đơn giản hơn việc sử dụng bridge table.

## 42. Hierarchy bridge table

Với hierarchy có độ sâu không xác định, bridge table lưu tất cả đường đi có thể có giữa tổ tiên và hậu duệ.

Nó hỗ trợ:

- Duyệt lên và xuống hierarchy.
- Thay đổi hierarchy theo thời gian.
- Nhiều hierarchy thay thế.
- Một đối tượng có nhiều chủ sở hữu.
- Truy vấn bằng SQL tiêu chuẩn.

Đổi lại, thiết kế và ETL phức tạp hơn.

## 43. Pathstring

Mỗi dòng dimension lưu một chuỗi mã hóa toàn bộ đường đi từ nút gốc đến nút hiện tại.

```text
/ROOT/REGION01/BRANCH07/TEAM03/
```

Có thể dùng SQL để tìm tổ tiên hoặc hậu duệ.

Hạn chế:

- Khó thay nhanh hierarchy thay thế.
- Không hỗ trợ tốt sở hữu chung.
- Khi cấu trúc thay đổi, có thể phải gán lại path cho nhiều dòng.

---

# Phần VII: Các kỹ thuật fact nâng cao

## 44. Surrogate key cho fact table

Fact table không bắt buộc phải có surrogate key riêng, nhưng một khóa số nguyên duy nhất có thể giúp:

- Làm primary key một cột.
- Xác định nhanh fact row trong ETL.
- Tiếp tục hoặc hoàn tác một lần nạp bị gián đoạn.
- Chuyển update phức tạp thành insert cộng delete an toàn hơn.

Khóa này không đại diện cho dimension nào.

## 45. Centipede fact table

Centipede fact table xuất hiện khi fact table có quá nhiều foreign key do tách từng cấp hierarchy thành dimension riêng hoặc tạo dimension riêng cho từng cờ nhỏ.

Ví dụ không nên đưa đồng thời:

```text
DateKey + MonthKey + QuarterKey + YearKey
```

Chỉ cần DateKey; tháng, quý và năm phải là thuộc tính của Date Dimension. Các cờ nhỏ nên được gom vào junk dimension.

## 46. Giá trị số là fact hay dimension attribute?

Nếu giá trị chủ yếu dùng để tính toán, nó nên là fact.

Nếu giá trị tương đối ổn định và chủ yếu dùng để lọc, nhóm hoặc đặt nhãn, nó nên là dimension attribute.

Ví dụ giá niêm yết chuẩn của sản phẩm có thể là thuộc tính. Có thể bổ sung dải:

```text
0-50
50-100
100-500
```

Trong một số trường hợp, cùng giá trị có thể được mô hình hóa hai lần: dạng số làm fact và dạng phân loại văn bản làm dimension attribute.

## 47. Lag và duration facts

Accumulating snapshot có nhiều mốc thời gian. Người dùng thường cần tính khoảng thời gian giữa các mốc.

Nếu có nhiều mốc, số cặp khoảng thời gian có thể rất lớn. Thay vì lưu mọi cặp, lưu độ trễ từ điểm bắt đầu đến từng mốc:

```text
LagToApproval
LagToPack
LagToShip
LagToDelivery
```

Khoảng thời gian giữa đóng gói và giao hàng được tính:

```text
LagToDelivery - LagToPack
```

Cách này giảm số cột cần lưu và vẫn tính được mọi khoảng.

## 48. Header/line fact table

Hệ thống giao dịch thường có một header và nhiều dòng chi tiết.

```text
Order Header
  -> Order Line 1
  -> Order Line 2
  -> Order Line 3
```

Các dimension key và degenerate dimension ở cấp header nên được sao xuống fact table ở cấp line. Điều này cho phép truy vấn một fact table duy nhất ở grain chi tiết.

## 49. Allocated facts

Một số fact chỉ tồn tại ở cấp header, chẳng hạn phí vận chuyển của cả đơn hàng. Nếu fact table có grain dòng hàng, nên phân bổ phí xuống từng dòng theo quy tắc nghiệp vụ:

- Theo giá trị dòng hàng.
- Theo khối lượng.
- Theo số lượng.
- Theo thể tích.

Sau phân bổ, chi phí có thể được phân tích theo sản phẩm, thương hiệu hoặc dimension khác. Nếu không phân bổ, phí chỉ có thể phân tích ở cấp đơn hàng.

## 50. Fact table lãi lỗ

Phương trình cơ bản:

```text
Doanh thu - Chi phí = Lợi nhuận
```

Fact table lợi nhuận mạnh nhất khi được xây ở grain giao dịch doanh thu nguyên tử. Khi đó có thể tính lợi nhuận theo khách hàng, sản phẩm, khuyến mãi, kênh hoặc khu vực.

Khó khăn lớn là phân bổ các loại chi phí về grain giao dịch. Đây thường là một hệ thống ETL lớn và có tính chính trị vì các bộ phận có thể không thống nhất quy tắc phân bổ.

Vì vậy, fact table lãi lỗ thường không phải phần triển khai đầu tiên.

## 51. Nhiều loại tiền tệ

Mỗi fact tài chính nên có hai giá trị:

- Giá trị theo tiền tệ thực của giao dịch.
- Giá trị theo một tiền tệ chuẩn chung.

```text
TransactionAmount = 1.000 EUR
StandardAmount = 1.080 USD
```

Fact table cũng phải có Currency Dimension để xác định loại tiền gốc. Giá trị chuẩn được ETL tính theo quy tắc chuyển đổi đã được doanh nghiệp phê duyệt.

## 52. Nhiều đơn vị đo

Chuỗi cung ứng có thể cần cùng phép đo dưới dạng pallet, thùng vận chuyển, thùng bán lẻ hoặc đơn vị sản phẩm.

Thay vì lưu lặp mọi fact theo mọi đơn vị, có thể:

1. Lưu fact theo đơn vị chuẩn.
2. Lưu hệ số chuyển đổi trên fact row.
3. Cung cấp các view chuyển đổi cho từng nhóm người dùng.

Hệ số phải nằm đúng trên fact row vì nó có thể thay đổi theo sản phẩm và thời gian.

## 53. Year-to-date facts

Không nên vội lưu YTD trong fact table vì yêu cầu dễ phát triển thành calendar YTD, fiscal YTD, period-to-date, quarter-to-date hoặc YTD tại ngày đóng kỳ.

Cách linh hoạt hơn là tính YTD trong BI layer hoặc OLAP cube từ dữ liệu nguyên tử.

## 54. Không join trực tiếp hai fact table

Không được viết SQL join trực tiếp hai fact table qua các foreign key chung vì quan hệ nhiều-nhiều có thể làm nhân số dòng và trả về kết quả sai.

Ví dụ không được join trực tiếp:

```text
Shipments Fact <-> Returns Fact
```

qua CustomerKey và ProductKey.

Cách đúng:

1. Tổng hợp Shipments theo các conformed attributes.
2. Tổng hợp Returns theo cùng attributes.
3. Ghép hai tập kết quả đã tổng hợp.

Đây là multipass SQL hoặc drilling across.

## 55. Timespan tracking trong fact table

Trong một số trường hợp hiếm, fact row có thể có effective date, expiration date và current indicator. Mẫu này giống SCD Type 2 nhưng áp dụng cho fact table.

Nó hữu ích khi một trạng thái thay đổi chậm và periodic snapshot thường xuyên sẽ tạo rất nhiều dòng giống hệt nhau, chẳng hạn một số dạng số dư tồn kho.

## 56. Late-arriving facts

Fact đến trễ là sự kiện xảy ra trong quá khứ nhưng dữ liệu chỉ đến ETL sau đó.

Không được nối fact này với phiên bản dimension hiện tại nếu bối cảnh đã thay đổi. ETL phải tìm surrogate key của dimension có hiệu lực tại thời điểm sự kiện thực sự xảy ra.

---

# Phần VIII: Các kỹ thuật dimension nâng cao

## 57. Join giữa các dimension

Dimension có thể tham chiếu dimension khác qua outrigger, nhưng nếu outrigger thay đổi Type 2, nó có thể buộc dimension chính cũng tạo phiên bản mới và gây tăng trưởng bùng nổ.

Một giải pháp là đưa cả hai dimension xuống fact table làm hai foreign key độc lập.

Khi đó quan hệ giữa chúng chỉ được phát hiện thông qua fact table, nhưng tránh nhân bản không cần thiết trong dimension.

## 58. Dimension đa trị và bridge table

Thông thường một fact row có một giá trị cho mỗi dimension. Nhưng một số trường hợp thực sự đa trị.

Ví dụ một lần điều trị có thể liên quan đồng thời nhiều chẩn đoán:

```text
Treatment Fact
    |
Diagnosis Group Key
    |
Bridge Table
    |
Nhiều Diagnosis Dimension rows
```

Bridge table chứa một dòng cho mỗi thành viên trong nhóm.

Cần chú ý nguy cơ đếm lặp nếu một fact được nối đến nhiều thành viên. Một số thiết kế cần trọng số phân bổ.

## 59. Bridge table đa trị thay đổi theo thời gian

Quan hệ nhiều-nhiều cũng có thể thay đổi. Ví dụ một tài khoản ngân hàng có nhiều chủ và chủ sở hữu thay đổi theo thời gian.

Bridge table phải có effective timestamp và expiration timestamp. Truy vấn phải lọc bridge tại một thời điểm cụ thể để tạo snapshot nhất quán.

## 60. Behavior tag time series

Phân tích data mining có thể gán cho khách hàng những nhãn hành vi định kỳ:

- Khách trung thành.
- Có nguy cơ rời bỏ.
- Nhạy cảm với giá.
- Người mua theo mùa.

Chuỗi nhãn theo thời gian có thể được lưu thành các thuộc tính vị trí trong Customer Dimension, cùng một chuỗi văn bản tổng hợp nếu cần.

Nhãn hành vi là dữ liệu mô tả dùng cho lọc và phân nhóm, không phải phép đo số để tính toán.

## 61. Behavior study groups

Một hành vi phức tạp có thể cần phân tích dài để xác định, chẳng hạn khách hàng đã mua ít nhất ba nhóm sản phẩm, ngừng mua sáu tháng rồi quay lại sau một chiến dịch.

Không nên lặp lại thuật toán đó trong mọi dashboard. Hãy chạy phân tích một lần và lưu danh sách durable customer key vào một bảng study group.

Study group có thể được dùng làm bộ lọc trong các schema khác. Có thể kết hợp các nhóm bằng giao, hợp hoặc hiệu tập hợp.

## 62. Fact tổng hợp làm dimension attribute

Người dùng thường muốn lọc khách hàng theo kết quả tổng hợp:

- Tổng chi tiêu năm trước trên một ngưỡng.
- Giá trị vòng đời trên một ngưỡng.
- Số lần mua trong 90 ngày.

Có thể đưa một số kết quả tổng hợp vào Customer Dimension, thường dưới dạng dải:

```text
0-10 triệu
10-50 triệu
50-100 triệu
Trên 100 triệu
```

Điều này làm ETL nặng hơn nhưng giúp truy vấn BI đơn giản hơn.

## 63. Dynamic value bands

Dynamic banding chia một fact số thành các khoảng được xác định tại thời điểm truy vấn.

Ví dụ:

- 0-10.
- 10,01-25.
- 25,01-100.
- Trên 100.

Có thể triển khai bằng một value band dimension nhỏ và điều kiện lớn hơn/nhỏ hơn, hoặc biểu thức SQL `CASE`.

Dimension riêng có thể có hiệu năng tốt hơn, đặc biệt trên columnar database.

## 64. Text comments dimension

Không nên đặt bình luận tự do trực tiếp trong fact table như text fact.

Có thể tạo Comments Dimension riêng hoặc đặt comment trong dimension có grain một dòng cho mỗi giao dịch. Fact table chỉ lưu foreign key tới comment tương ứng.

## 65. Nhiều múi giờ

Để giữ cả thời gian chuẩn và thời gian địa phương, fact table có thể chứa hai bộ khóa:

- UTC Date/Time.
- Local Date/Time.

Các khóa tham chiếu đến các role-playing Date Dimension và có thể cả Time-of-Day Dimension.

Việc chỉ lưu UTC có thể gây khó khăn khi phân tích theo ngày kinh doanh địa phương.

## 66. Measure type dimension

Khi fact table có hàng trăm cột fact nhưng mỗi dòng chỉ có vài cột được sử dụng, có thể bị cám dỗ chuyển từ cấu trúc rộng:

```text
FactA | FactB | FactC | FactD
```

sang cấu trúc dọc:

```text
MeasureType | MeasureValue
```

Kimball thường không khuyến nghị vì:

- Số dòng tăng theo số phép đo có giá trị.
- Các tính toán giữa phép đo trở nên khó.
- Ý nghĩa kiểu dữ liệu và tính cộng được bị trộn lẫn.

Chỉ nên cân nhắc khi có hàng trăm loại phép đo nhưng mỗi dòng chỉ sử dụng một số rất ít.

## 67. Step dimension

Dùng cho quy trình tuần tự mà mỗi bước là một transaction fact row riêng.

Step Dimension mô tả:

- Đây là bước số mấy.
- Tổng cộng có bao nhiêu bước.
- Còn bao nhiêu bước để hoàn tất.
- Loại bước.

Ví dụ hành trình web:

```text
Trang chủ -> Tìm kiếm -> Giỏ hàng -> Thanh toán -> Xác nhận
```

## 68. Hot-swappable dimensions

Cùng một fact table có thể được ghép với nhiều phiên bản khác nhau của một dimension.

Ví dụ một fact table chứa giá cổ phiếu được cung cấp cho nhiều nhà đầu tư. Mỗi nhà đầu tư có bộ thuộc tính phân loại cổ phiếu riêng và mang tính độc quyền.

Fact không đổi, nhưng dimension được thay thế tùy đối tượng sử dụng.

## 69. Tránh abstract generic dimension

Không nên gom mọi loại thực thể vào một dimension chung chỉ vì chúng có điểm trừu tượng giống nhau.

Ví dụ không nên tạo:

- Generic Person chứa nhân viên, khách hàng và liên hệ nhà cung cấp.
- Generic Location chứa cửa hàng, nhà kho và địa chỉ khách hàng.

Các thực thể này thường có bộ thuộc tính khác nhau. Việc gom chung tạo nhiều cột không áp dụng, dimension lớn hơn, tên thuộc tính mơ hồ và truy vấn khó hiểu hơn.

Sự trừu tượng có thể hữu ích trong hệ thống nguồn hoặc ETL, nhưng thường gây hại trong presentation layer.

## 70. Audit dimension

Khi ETL tạo fact row, có thể gắn Audit Dimension chứa:

- Trạng thái chất lượng dữ liệu.
- Quy tắc kiểm tra đã áp dụng.
- Phiên bản mã ETL.
- Thời điểm thực thi.
- Batch ID.
- Nguồn dữ liệu.
- Các biến môi trường.

Audit Dimension giúp truy ngược dòng nào được tạo bởi phiên bản ETL nào, dòng nào có cảnh báo chất lượng và batch nào gây ra kết quả bất thường.

Nó đặc biệt hữu ích cho kiểm toán và tuân thủ.

## 71. Late-arriving dimensions

Trường hợp này ngược với late-arriving fact: fact đã đến nhưng ngữ cảnh dimension chưa có.

Ví dụ giao dịch tồn kho đến cùng `CustomerID`, nhưng thông tin khách hàng chưa được đồng bộ.

ETL phải:

1. Tạo dimension placeholder với natural key đã biết.
2. Điền các thuộc tính khác bằng `Unknown`.
3. Cho fact tham chiếu placeholder.
4. Khi thông tin đầy đủ đến, cập nhật Type 1 vào dòng placeholder.

Nếu một thay đổi Type 2 được khai báo hồi tố, cần chèn một phiên bản dimension mới đúng khoảng hiệu lực và xác định lại những fact row phải tham chiếu phiên bản đó.

---

# Phần IX: Schema dùng cho mục đích đặc biệt

## 72. Supertype và subtype schema

Một doanh nghiệp có thể có nhiều sản phẩm rất khác nhau nhưng vẫn chia sẻ một lõi chung.

Ví dụ ngân hàng có:

- Tài khoản thanh toán.
- Tiết kiệm.
- Thẻ tín dụng.
- Vay mua nhà.
- Vay doanh nghiệp.

Nếu cố tạo một fact table chung chứa hợp của mọi fact và một dimension chung chứa mọi thuộc tính, kết quả sẽ có hàng trăm cột không tương thích và phần lớn để trống.

Giải pháp:

- **Supertype/core fact table:** chứa giao của các fact chung cho mọi sản phẩm.
- **Supertype dimension:** chứa thuộc tính chung.
- **Subtype/custom fact table:** chứa fact riêng của từng loại sản phẩm.
- **Subtype dimension:** chứa thuộc tính riêng.

Cách này vừa hỗ trợ báo cáo toàn doanh nghiệp vừa giữ được chi tiết chuyên biệt.

## 73. Real-time fact table

Fact table thời gian thực được cập nhật thường xuyên hơn batch ban đêm.

Các kỹ thuật tùy thuộc DBMS hoặc OLAP engine. Ví dụ:

- Dùng một hot partition nằm trong bộ nhớ.
- Không xây aggregate và index nặng trên partition đang nhận dữ liệu.
- Định kỳ hợp nhất partition nóng vào phần lịch sử.
- Dùng deferred update để truy vấn hiện tại hoàn tất trước khi cập nhật.

Mục tiêu là cân bằng giữa độ mới dữ liệu, hiệu năng truy vấn, chi phí cập nhật index/aggregate và tính nhất quán.

## 74. Error event schema

Chất lượng dữ liệu cần được kiểm tra bằng các rule hoặc filter trong toàn bộ luồng ETL.

Khi phát hiện lỗi, ETL ghi một sự kiện vào schema chuyên dụng ở back room.

### Error Event Fact

Grain:

> Một dòng cho mỗi sự kiện lỗi.

Nó mô tả:

- Rule nào thất bại.
- Batch nào.
- Nguồn nào.
- Thời điểm nào.
- Mức độ nghiêm trọng.

### Error Event Detail Fact

Grain:

> Một dòng cho mỗi cột của mỗi bảng tham gia vào sự kiện lỗi.

Nó cung cấp chi tiết chính xác về dữ liệu gây lỗi. Schema này chủ yếu phục vụ nhóm ETL và quản trị chất lượng, không phải người dùng BI thông thường.

---

## 75. Tổng kết Chương 2

Chương 2 đưa ra bản đồ hoàn chỉnh của phương pháp Kimball. Có thể gom toàn bộ kỹ thuật thành sáu câu hỏi:

1. Đang đo quy trình nào?
2. Một dòng fact đại diện chính xác cho điều gì?
3. Những dimension nào mô tả sự kiện đó?
4. Những fact nào hợp lệ ở grain này?
5. Dữ liệu thay đổi và đến không đúng thứ tự sẽ được xử lý thế nào?
6. Mô hình tích hợp với các quy trình khác bằng conformed dimensions ra sao?

Luồng thiết kế trung tâm:

```text
Yêu cầu kinh doanh + thực tế dữ liệu
                    |
                    v
          Chọn quy trình kinh doanh
                    |
                    v
              Tuyên bố grain
                    |
              +-----+-----+
              |           |
              v           v
         Dimension       Fact
              |           |
              +-----+-----+
                    |
                    v
        Star schema / OLAP cube
                    |
                    v
  Tích hợp bằng conformed dimensions và bus matrix
```

Điểm quan trọng nhất là mỗi kỹ thuật chỉ giải quyết một dạng vấn đề nhất định. Không nên dùng kỹ thuật phức tạp chỉ vì nó tồn tại.

Luôn bắt đầu với thiết kế đơn giản nhất: quy trình rõ ràng, grain nguyên tử, fact table gọn và dimension phẳng. Chỉ sử dụng các mẫu nâng cao khi dữ liệu thực sự yêu cầu.

---

## Thuật ngữ cần nhớ

| Thuật ngữ | Nghĩa ngắn gọn |
|---|---|
| Business process | Hoạt động vận hành tạo ra sự kiện hoặc trạng thái có thể đo lường |
| Grain | Ý nghĩa và mức chi tiết chính xác của một dòng fact |
| Atomic grain | Mức chi tiết thấp nhất mà quy trình nguồn ghi nhận được |
| Transaction fact | Fact table có một dòng cho mỗi sự kiện riêng lẻ |
| Periodic snapshot | Fact table ghi trạng thái hoặc tổng hợp theo chu kỳ |
| Accumulating snapshot | Fact table theo dõi một quy trình từ đầu đến cuối bằng cách cập nhật cùng dòng |
| Factless fact table | Fact table ghi nhận sự kiện bằng các foreign key mà không cần phép đo số |
| Conformed fact | Fact có định nghĩa thống nhất giữa nhiều fact table |
| Conformed dimension | Dimension có định nghĩa và giá trị thống nhất, dùng chung giữa các quy trình |
| Shrunken dimension | Tập con về dòng hoặc cột của một conformed dimension cơ sở |
| Bus matrix | Ma trận có hàng là quy trình kinh doanh và cột là dimension |
| Natural key | Khóa nghiệp vụ do hệ thống nguồn tạo |
| Surrogate key | Khóa thay thế do data warehouse tự cấp |
| Durable key | Khóa bền vững nhận diện cùng một thực thể qua mọi thay đổi |
| Degenerate dimension | Khóa dimension nằm trực tiếp trong fact table mà không có bảng dimension riêng |
| Junk dimension | Dimension gom nhiều cờ và thuộc tính rời rạc có số giá trị thấp |
| Role-playing dimension | Một dimension vật lý được sử dụng với nhiều vai trò logic |
| Outrigger | Dimension phụ được tham chiếu từ một dimension khác |
| Bridge table | Bảng trung gian xử lý quan hệ đa trị hoặc hierarchy phức tạp |
| SCD | Nhóm kỹ thuật xử lý thuộc tính dimension thay đổi theo thời gian |
| As-was | Xem dữ liệu theo bối cảnh đúng tại thời điểm lịch sử |
| As-is | Xem toàn bộ dữ liệu theo phân loại hoặc thuộc tính hiện tại |
| Drill-down | Thêm dimension attribute để phân tích chi tiết hơn |
| Drill-across | Tổng hợp riêng nhiều fact table rồi ghép kết quả qua conformed attributes |
| Late-arriving fact | Fact đến sau thời điểm sự kiện thực tế xảy ra |
| Late-arriving dimension | Fact đã đến nhưng ngữ cảnh dimension tương ứng chưa có |
| Audit dimension | Dimension lưu metadata về quá trình ETL và chất lượng dữ liệu |

