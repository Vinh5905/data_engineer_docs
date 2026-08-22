# Chương 1: Nhập môn Kho dữ liệu, Business Intelligence và Mô hình hóa chiều

> Bản trình bày và giải thích bằng tiếng Việt dựa trên Chương 1, trang 1-35 của *The Data Warehouse Toolkit, Third Edition* - Ralph Kimball và Margy Ross. Trong tệp PDF nguồn, nội dung tương ứng với trang vật lý 37-71.
>
> Đây là bản diễn giải đầy đủ các luận điểm, khái niệm và ví dụ chính của chương, không phải bản sao từng câu. Thuật ngữ tiếng Anh quan trọng được giữ lại để tiện đối chiếu với sách.

---

## Mục lục Chương 1 - bản dịch tiếng Việt

### Nhập môn Kho dữ liệu, Trí tuệ kinh doanh và Mô hình hóa chiều - trang 1

- Hai thế giới khác nhau: thu thập dữ liệu và phân tích dữ liệu - trang 2
- Mục tiêu của Kho dữ liệu và Trí tuệ kinh doanh - trang 3
  - Ẩn dụ xuất bản dành cho các nhà quản lý DW/BI - trang 5
- Giới thiệu về mô hình hóa chiều - trang 7
  - Lược đồ hình sao và khối OLAP - trang 8
  - Bảng fact dùng để lưu các đại lượng đo lường - trang 10
  - Bảng dimension dùng để cung cấp ngữ cảnh mô tả - trang 13
  - Kết nối fact và dimension trong lược đồ hình sao - trang 16
- Kiến trúc DW/BI của Kimball - trang 18
  - Các hệ thống nguồn vận hành - trang 18
  - Hệ thống trích xuất, chuyển đổi và nạp dữ liệu (ETL) - trang 19
  - Khu vực trình bày dữ liệu phục vụ Business Intelligence - trang 21
  - Các ứng dụng Business Intelligence - trang 22
  - Ẩn dụ nhà hàng dành cho kiến trúc Kimball - trang 23
- Các kiến trúc DW/BI thay thế - trang 26
  - Kiến trúc các data mart độc lập - trang 26
  - Kiến trúc Corporate Information Factory dạng trung tâm và nan hoa của Inmon - trang 28
  - Kiến trúc lai giữa mô hình trung tâm-nan hoa và kiến trúc Kimball - trang 29
- Những quan niệm sai về mô hình hóa chiều - trang 30
  - Quan niệm sai 1: Mô hình chiều chỉ dành cho dữ liệu tổng hợp - trang 30
  - Quan niệm sai 2: Mô hình chiều chỉ ở cấp phòng ban, không dùng được cho toàn doanh nghiệp - trang 31
  - Quan niệm sai 3: Mô hình chiều không có khả năng mở rộng - trang 31
  - Quan niệm sai 4: Mô hình chiều chỉ phù hợp với nhu cầu sử dụng có thể dự đoán trước - trang 31
  - Quan niệm sai 5: Không thể tích hợp các mô hình chiều với nhau - trang 32
- Những lý do khác để tư duy theo mô hình chiều - trang 32
- Các vấn đề cần cân nhắc khi áp dụng Agile - trang 34
- Tổng kết - trang 35

---

## 1. Quan điểm nền tảng: bắt đầu từ nhu cầu kinh doanh

Khi xây dựng một hệ thống Data Warehouse/Business Intelligence - viết tắt là **DW/BI** - không nên bắt đầu bằng việc lựa chọn cơ sở dữ liệu, phần mềm ETL hay công cụ báo cáo.

Trình tự hợp lý là:

1. Xác định doanh nghiệp cần đưa ra những quyết định nào.
2. Xác định thông tin cần thiết để hỗ trợ các quyết định đó.
3. Thiết kế mô hình logic cho thông tin.
4. Thiết kế cách lưu trữ vật lý.
5. Cuối cùng mới lựa chọn công nghệ và công cụ.

Người làm DW/BI vì vậy cần có hai nhóm năng lực:

- Một nửa là **DBA - database administrator**: hiểu dữ liệu, cơ sở dữ liệu, hiệu năng và kỹ thuật.
- Một nửa là **MBA/business analyst**: hiểu hoạt động kinh doanh, người dùng và giá trị mà dữ liệu cần tạo ra.

Thông điệp xuyên suốt chương là: **công nghệ chỉ là phương tiện; mục tiêu thực sự là giúp doanh nghiệp đưa ra quyết định tốt hơn.**

---

## 2. Hai thế giới: thu thập dữ liệu và phân tích dữ liệu

Thông tin của một tổ chức thường được sử dụng cho hai mục đích rất khác nhau:

| Hệ thống vận hành | Hệ thống DW/BI |
|---|---|
| Là nơi đưa dữ liệu vào | Là nơi lấy thông tin ra |
| Ghi nhận và xử lý giao dịch | Phân tích hoạt động |
| Thường xử lý từng bản ghi | Đọc và tổng hợp rất nhiều bản ghi |
| Thực hiện những tác vụ lặp lại, có thể dự đoán | Trả lời các câu hỏi thường xuyên thay đổi |
| Chủ yếu lưu trạng thái hiện tại | Cần bảo tồn lịch sử |
| Tối ưu cho tốc độ ghi và cập nhật | Tối ưu cho tốc độ truy vấn |

Hệ thống vận hành thực hiện những công việc như:

- Ghi nhận đơn hàng.
- Đăng ký khách hàng.
- Cập nhật trạng thái vận chuyển.
- Lưu khiếu nại.
- Sửa địa chỉ hoặc trạng thái hiện tại của khách hàng.

Người dùng DW/BI lại đặt những câu hỏi như:

- Tuần này có bao nhiêu đơn hàng so với tuần trước?
- Vì sao số khách hàng mới tăng hoặc giảm?
- Nhóm khách hàng nào khiếu nại nhiều nhất?
- Doanh số theo sản phẩm, khu vực và thời gian thay đổi ra sao?
- Các quy trình vận hành có đang hoạt động hiệu quả không?

Một câu hỏi phân tích có thể phải đọc hàng trăm nghìn hoặc hàng triệu giao dịch để tạo ra một kết quả tổng hợp.

### Vì sao không thể chỉ sao chép hệ thống vận hành?

Một số hệ thống được gọi là data warehouse thực chất chỉ là bản sao của cơ sở dữ liệu vận hành đặt trên máy khác. Việc này có thể ngăn truy vấn phân tích làm chậm hệ thống giao dịch, nhưng chưa giải quyết được các khác biệt cốt lõi:

- Cấu trúc dữ liệu vẫn khó hiểu đối với người kinh doanh.
- Dữ liệu chưa được tổ chức thuận tiện cho phân tích.
- Lịch sử có thể không đầy đủ.
- Định nghĩa giữa các nguồn chưa được thống nhất.
- Truy vấn rộng và không dự đoán trước vẫn có thể rất chậm.

Data warehouse vì vậy không chỉ là một bản sao dữ liệu. Dữ liệu phải được **tổ chức lại cho mục đích phân tích**.

---

## 3. Các mục tiêu của DW/BI

Các nhà quản lý thường có những than phiền quen thuộc:

- Chúng ta thu thập rất nhiều dữ liệu nhưng không truy cập được.
- Chúng ta muốn phân tích dữ liệu theo mọi góc độ.
- Người kinh doanh cần tiếp cận dữ liệu dễ dàng.
- Hãy chỉ cho tôi điều gì thực sự quan trọng.
- Mọi người tranh luận xem số liệu của ai đúng thay vì đưa ra quyết định.
- Chúng ta muốn ra quyết định dựa trên sự kiện và bằng chứng.

Từ đó, Kimball đưa ra bảy yêu cầu nền tảng.

### 3.1. Dễ truy cập, dễ hiểu và nhanh

Dữ liệu phải được trình bày theo cách suy nghĩ và từ vựng của người kinh doanh, không chỉ thuận tiện cho lập trình viên.

Người dùng cần có khả năng:

- Lọc dữ liệu theo nhiều điều kiện.
- Kết hợp nhiều góc nhìn.
- Đọc tên trường và hiểu ngay ý nghĩa.
- Nhận kết quả với thời gian chờ ngắn.
- Sử dụng công cụ mà không cần hiểu cấu trúc kỹ thuật phức tạp.

Tác giả tóm tắt yêu cầu này bằng hai từ: **simple and fast - đơn giản và nhanh**.

### 3.2. Dữ liệu phải nhất quán

Dữ liệu lấy từ nhiều nguồn cần được:

- Làm sạch.
- Kiểm tra chất lượng.
- Chuẩn hóa nhãn và định nghĩa.
- Chỉ công bố khi đủ đáng tin cậy.

Nếu hai chỉ số có cùng tên, chúng phải có cùng ý nghĩa. Nếu ý nghĩa khác nhau, chúng phải được đặt tên khác nhau.

Ví dụ, nếu phòng kinh doanh và phòng tài chính cùng sử dụng chỉ số "doanh thu", họ phải thống nhất doanh thu có bao gồm thuế, chiết khấu và đơn hàng bị hoàn hay không.

### 3.3. Có khả năng thích ứng với thay đổi

Nhu cầu người dùng, điều kiện kinh doanh, nguồn dữ liệu và công nghệ đều thay đổi. Hệ thống phải tiếp nhận thay đổi mà không phá hỏng dữ liệu hoặc báo cáo đang tồn tại.

Ví dụ, khi bổ sung một thuộc tính mới cho sản phẩm, các báo cáo cũ vẫn phải chạy và cho kết quả như trước.

Nếu giá trị mô tả thay đổi theo thời gian, hệ thống phải ghi nhận thay đổi đúng cách và khiến việc đó minh bạch đối với người dùng.

### 3.4. Cung cấp thông tin đúng thời điểm

Tùy loại quyết định, dữ liệu có thể cần được cập nhật:

- Mỗi ngày.
- Mỗi giờ.
- Mỗi vài phút.
- Gần thời gian thực.

Dữ liệu càng phải xuất hiện nhanh thì thời gian làm sạch và kiểm tra càng ít. Nhóm DW/BI và người dùng phải thống nhất kỳ vọng thực tế giữa **độ mới** và **độ tin cậy**.

### 3.5. Bảo vệ tài sản thông tin

Data warehouse có thể chứa những thông tin rất nhạy cảm:

- Bán sản phẩm gì.
- Bán cho ai.
- Giá bán và chiết khấu.
- Lợi nhuận.
- Thông tin khách hàng.
- Dữ liệu nhân viên.

DW/BI phải kiểm soát quyền truy cập và bảo vệ thông tin bí mật.

### 3.6. Là nền tảng đáng tin cậy cho việc ra quyết định

Đầu ra quan trọng nhất của DW/BI không phải một bảng dữ liệu hay dashboard đẹp. Đầu ra thực sự là **những quyết định được đưa ra dựa trên bằng chứng phân tích**.

Tên gọi cũ **decision support system - hệ thống hỗ trợ quyết định** vì vậy vẫn mô tả chính xác mục đích của DW/BI.

### 3.7. Được cộng đồng người dùng chấp nhận

Một hệ thống có kiến trúc đẹp, công nghệ tốt và mô hình kỹ thuật hoàn hảo vẫn là thất bại nếu người kinh doanh không sử dụng nó.

Khác với hệ thống vận hành bắt buộc phải dùng để hoàn thành công việc, việc sử dụng BI thường mang tính tự nguyện. Người dùng sẽ quay lại nếu hệ thống:

- Dễ hiểu.
- Nhanh.
- Đáng tin.
- Cung cấp thông tin có thể hành động.

Tác giả đặc biệt nhấn mạnh hai yêu cầu cuối: hệ thống phải tạo ra quyết định tốt hơn và phải được người dùng thực sự chấp nhận.

---

## 4. Ẩn dụ nhà xuất bản dành cho nhà quản lý DW/BI

Kimball so sánh người quản lý DW/BI với tổng biên tập của một tạp chí.

Một tổng biên tập thành công phải:

- Hiểu độc giả.
- Biết độc giả muốn đọc gì.
- Chọn nội dung có giá trị.
- Trình bày nội dung dễ đọc.
- Duy trì tiêu chuẩn biên tập và tính chính xác.
- Thích ứng khi độc giả hoặc nguồn nội dung thay đổi.
- Xuất bản đều đặn.
- Duy trì lòng tin.
- Bảo đảm hoạt động có thể tồn tại lâu dài.

Người quản lý DW/BI có các trách nhiệm tương ứng:

- Hiểu công việc, mục tiêu và quyết định của người dùng.
- Chọn những dữ liệu có giá trị nhất từ nhiều nguồn.
- Cung cấp giao diện và ứng dụng phù hợp với cách người dùng suy nghĩ.
- Bảo đảm dữ liệu chính xác và được đặt tên nhất quán.
- Theo dõi chất lượng dữ liệu liên tục.
- Thích ứng với yêu cầu, ưu tiên và nguồn dữ liệu mới.
- Cập nhật hệ thống đều đặn.
- Duy trì lòng tin của người dùng, lãnh đạo và bộ phận IT.
- Chứng minh giá trị kinh doanh để tiếp tục nhận ngân sách và nhân lực.

Ẩn dụ này cho thấy công việc DW/BI giống hoạt động **lựa chọn, biên tập, kiểm chứng, đóng gói và xuất bản dữ liệu**. Công nghệ in không phải mục tiêu của tạp chí; tương tự, cơ sở dữ liệu và công cụ ETL không phải mục tiêu cuối cùng của DW/BI.

---

## 5. Giới thiệu về mô hình hóa chiều

Mô hình hóa chiều được sử dụng để đồng thời đạt hai mục tiêu:

1. Dữ liệu dễ hiểu đối với người kinh doanh.
2. Truy vấn có hiệu năng tốt.

Giả sử một lãnh đạo nói: "Doanh nghiệp bán sản phẩm ở nhiều thị trường và đo hiệu quả theo thời gian."

Nhà thiết kế sẽ nhận ra ba góc nhìn chính:

- Sản phẩm.
- Thị trường.
- Thời gian.

Tại giao điểm của ba góc nhìn là các phép đo như:

- Số lượng bán.
- Doanh thu.
- Lợi nhuận.

Đây là nguồn gốc trực giác của khối dữ liệu: người dùng có thể cắt dữ liệu theo sản phẩm, thị trường hoặc thời gian.

### Mô hình chuẩn hóa 3NF và mô hình chiều

Mô hình chuẩn hóa bậc ba - **third normal form, 3NF** - cố gắng loại bỏ dữ liệu dư thừa. Một đơn hàng có thể được chia thành hàng chục hoặc hàng trăm bảng nhỏ.

Cách này phù hợp với hệ thống vận hành vì một lần thêm hoặc sửa dữ liệu chỉ tác động đến một nơi. Nhưng đối với BI:

- Người dùng khó hiểu cấu trúc.
- Cần quá nhiều phép join.
- Trình tối ưu truy vấn khó xử lý những câu hỏi không dự đoán trước.
- Hiệu năng có thể rất kém.

Mô hình chiều không nhất thiết chứa thông tin khác với mô hình chuẩn hóa. Nó **đóng gói cùng thông tin theo cách dễ hiểu, dễ truy vấn và dễ mở rộng hơn**.

---

## 6. Star schema và OLAP cube

Hai cách triển khai vật lý phổ biến của mô hình chiều là:

- **Star schema - lược đồ hình sao:** triển khai trong cơ sở dữ liệu quan hệ.
- **OLAP cube - khối OLAP:** triển khai trong môi trường cơ sở dữ liệu đa chiều.

Cả hai có cùng logic về dimension và fact, nhưng cách lưu trữ vật lý khác nhau.

### Ưu điểm của OLAP cube

Cube thường:

- Tạo sẵn các phép tổng hợp.
- Có cơ chế chỉ mục tối ưu cho phân tích.
- Hỗ trợ drill-down và roll-up nhanh.
- Có các hàm phân tích mạnh hơn SQL.
- Có khả năng phân quyền tinh vi, chẳng hạn cho xem số tổng hợp nhưng không cho xem chi tiết.
- Xử lý tốt hệ phân cấp không đều như sơ đồ tổ chức hoặc cấu trúc nguyên vật liệu.

### Hạn chế và lưu ý khi triển khai OLAP

- Quá trình nạp và xử lý cube có thể tốn thời gian, nhất là với dữ liệu lớn.
- Cấu trúc cube khác nhau nhiều giữa các nhà cung cấp, khiến việc chuyển hệ thống khó hơn.
- Khoảng cách hiệu năng giữa cube và RDBMS đã thu hẹp nhờ phần cứng, cơ sở dữ liệu in-memory và columnar database.
- Một star schema trong cơ sở dữ liệu quan hệ là nền tảng ổn định cho việc xây cube, sao lưu và khôi phục.
- Một số cách cập nhật dimension yêu cầu xử lý lại một phần hoặc toàn bộ cube.
- Cube phù hợp với transaction fact và periodic snapshot fact, nhưng thường khó xử lý accumulating snapshot do loại này cần ghi đè dữ liệu khi quy trình tiến triển.
- Một số sản phẩm hạn chế cách định nghĩa khóa phân cấp hoặc role-playing dimension.
- Khả năng phân tích phong phú hơn SQL có thể là lý do quan trọng nhất để sử dụng OLAP.

Khuyến nghị của tác giả là lưu dữ liệu chi tiết, nguyên tử trong star schema trước; nếu cần, xây OLAP cube từ star schema đó.

---

## 7. Fact table: lưu các sự kiện đo lường

**Fact** là một đại lượng kinh doanh được đo tại một sự kiện cụ thể.

Ví dụ, khi một sản phẩm được quét tại quầy thanh toán, hệ thống có thể ghi:

- Số lượng bán.
- Số tiền bán.
- Ngày.
- Sản phẩm.
- Cửa hàng.
- Chương trình khuyến mãi.
- Khách hàng.
- Nhân viên thu ngân.
- Số giao dịch.

Mỗi dòng fact table tương ứng với một **measurement event - sự kiện đo lường**.

### Grain - mức chi tiết

Grain mô tả chính xác một dòng fact đại diện cho điều gì.

Trong ví dụ bán lẻ, grain là:

> Một dòng cho một sản phẩm được bán trong một giao dịch.

Mọi dòng trong cùng fact table phải có cùng grain. Nếu trộn nhiều mức chi tiết, số liệu rất dễ bị tính hai lần.

Nguyên tắc nền tảng là: **một sự kiện đo lường trong thế giới thực phải tương ứng với một dòng ở đúng grain trong fact table.**

### Tính cộng được của fact

Fact hữu ích nhất thường là số và có thể cộng được, chẳng hạn doanh thu, số lượng hoặc chi phí.

- **Additive fact:** cộng được theo mọi dimension, ví dụ doanh thu.
- **Semi-additive fact:** chỉ cộng được theo một số dimension. Số dư tài khoản có thể cộng giữa các tài khoản nhưng không thể cộng số dư của nhiều ngày thành một tổng có ý nghĩa.
- **Non-additive fact:** không thể cộng trực tiếp, ví dụ đơn giá hoặc tỷ lệ phần trăm. Thường phải dùng trung bình, đếm hoặc công thức khác.

### Dữ liệu chữ thường không nên đặt trong fact table

Nội dung chữ thường là phần mô tả và nên được đưa vào dimension. Không nên lặp lại cùng chuỗi văn bản trong hàng triệu dòng fact.

Chỉ nội dung văn bản thật sự khác nhau ở mỗi dòng, chẳng hạn bình luận tự do, mới có thể được xem là text fact; nhưng loại dữ liệu này rất khó phân tích.

### Fact table thường thưa, sâu và hẹp

Nếu một sản phẩm không được bán, không cần thêm một dòng có doanh số bằng 0. Chỉ ghi lại hoạt động thực sự xảy ra.

Fact table vì vậy:

- **Sparse - thưa:** không lưu mọi tổ hợp có thể có.
- **Deep - sâu:** có rất nhiều dòng.
- **Narrow - hẹp:** có tương đối ít cột.

Dù thưa, fact table thường chiếm từ 90% dung lượng của mô hình chiều trở lên.

### Ba loại fact table chính

1. **Transaction fact table:** một dòng cho một giao dịch hoặc sự kiện.
2. **Periodic snapshot fact table:** một dòng tổng hợp trạng thái tại mỗi chu kỳ, chẳng hạn số dư cuối ngày.
3. **Accumulating snapshot fact table:** một dòng được cập nhật khi một quy trình nhiều bước tiến triển, chẳng hạn từ lúc nhận đơn đến lúc giao hàng.

### Khóa trong fact table

Fact table chứa các foreign key nối đến dimension table. Khi mọi foreign key đều tìm được primary key tương ứng, hệ thống bảo đảm **referential integrity - toàn vẹn tham chiếu**.

Primary key của fact table thường là khóa ghép từ một số foreign key. Fact table cũng biểu diễn quan hệ nhiều-nhiều giữa các dimension.

---

## 8. Dimension table: cung cấp ngữ cảnh mô tả

Nếu fact trả lời câu hỏi "bao nhiêu", dimension trả lời:

- Ai?
- Cái gì?
- Ở đâu?
- Khi nào?
- Bằng cách nào?
- Tại sao?

Ví dụ, Product Dimension có thể chứa:

- Mã sản phẩm.
- Mô tả sản phẩm.
- Thương hiệu.
- Danh mục.
- Phòng hàng.
- Kiểu và kích thước bao bì.
- Trọng lượng.
- Loại bảo quản.
- Thời hạn sử dụng.

Dimension thường có ít dòng hơn fact table nhưng có thể có rất nhiều cột mô tả, đôi khi từ 50 đến 100 thuộc tính. Mỗi dimension có một primary key để fact table tham chiếu.

### Vai trò của dimension attribute

Dimension attribute được dùng làm:

- Điều kiện lọc.
- Tiêu chí phân nhóm.
- Nhãn trên báo cáo.

Trong câu "doanh thu theo thương hiệu", doanh thu là fact, còn thương hiệu là dimension attribute.

Dimension là điểm đi vào dữ liệu. Chất lượng phân tích phụ thuộc trực tiếp vào độ phong phú và chính xác của dimension.

### Tránh bắt người dùng ghi nhớ mã

Thay vì chỉ lưu một mã khó hiểu như `CAT_004`, dimension nên cung cấp tên đầy đủ. Nếu mã vận hành vẫn có ý nghĩa kinh doanh, có thể giữ cả mã gốc lẫn mô tả dễ đọc.

Nếu một mã chứa thông tin ngầm, chẳng hạn hai ký tự đầu đại diện cho khu vực, nên tách ý nghĩa đó thành thuộc tính riêng để người dùng lọc và nhóm thuận tiện.

Phép giải mã mã nghiệp vụ phải được quản lý tập trung trong dữ liệu, không nên bị chôn trong từng báo cáo vì sẽ tạo ra sự không nhất quán.

### Một số là fact hay dimension?

Có thể sử dụng câu hỏi sau:

- Nó là đại lượng liên tục và tham gia tính toán? Thường là **fact**.
- Nó là giá trị rời rạc dùng để mô tả, lọc hoặc tạo nhãn? Thường là **dimension attribute**.

Ví dụ:

- Số tiền bán: fact.
- Mã vùng `01`, `02`, `03`: dimension attribute.
- Chi phí chuẩn của sản phẩm có thể được mô hình hóa theo cả hai cách, tùy tần suất thay đổi và mục đích phân tích.

### Không nên snowflake dimension một cách không cần thiết

Trong Product Dimension, mỗi dòng sản phẩm có thể lặp lại tên thương hiệu và danh mục. Sự lặp lại này là có chủ đích để người dùng dễ sử dụng và truy vấn nhanh.

Nếu tách thương hiệu, danh mục và phòng hàng thành các bảng nhỏ riêng biệt, mô hình trở thành **snowflake schema**.

Kimball thường khuyên giữ dimension ở dạng phẳng, phi chuẩn hóa vì:

- Dimension nhỏ hơn fact table rất nhiều.
- Tiết kiệm một ít dung lượng dimension gần như không ảnh hưởng đến tổng dung lượng.
- Đổi lại, mô hình đơn giản và cần ít phép join hơn.

---

## 9. Ghép fact và dimension thành star schema

Trong star schema:

- Fact table nằm ở trung tâm.
- Các dimension table bao quanh.
- Mỗi dimension cung cấp ngữ cảnh đúng tại thời điểm sự kiện xảy ra.

Ví dụ Retail Sales Fact kết nối với:

- Date Dimension.
- Product Dimension.
- Store Dimension.
- Promotion Dimension.
- Customer Dimension.
- Clerk Dimension.

### Ba lợi ích chính

**Dễ hiểu:** Người kinh doanh nhìn vào mô hình thường nhận ra ngay cấu trúc hoạt động của mình.

**Hiệu năng tốt:** Hệ quản trị có thể lọc dimension trước, lấy tập khóa phù hợp rồi sử dụng những khóa đó để truy cập fact table.

**Dễ mở rộng:** Có thể:

- Thêm dimension mới nếu mỗi dòng fact hiện có xác định được một giá trị của dimension đó.
- Thêm fact mới nếu fact mới có cùng grain.
- Thêm thuộc tính mới vào dimension.

Các báo cáo cũ vẫn có thể chạy mà không thay đổi kết quả.

### Tại sao dữ liệu nguyên tử quan trọng?

Dữ liệu càng chi tiết càng có nhiều khả năng phân tích. Dữ liệu nguyên tử cho phép trả lời những câu hỏi chưa được dự đoán khi thiết kế.

Dữ liệu tổng hợp có thể được bổ sung để tăng hiệu năng, nhưng không nên thay thế dữ liệu chi tiết.

### Cách fact và dimension xuất hiện trong báo cáo

Trong ví dụ báo cáo doanh số:

- Date Dimension cung cấp bộ lọc tháng và năm.
- Store Dimension cung cấp nhóm theo khu vực.
- Product Dimension cung cấp nhóm theo thương hiệu.
- Sales Fact cung cấp tổng doanh thu.

Nói cách khác:

- `WHERE` hoặc filter thường đến từ dimension.
- `GROUP BY` thường đến từ dimension.
- `SUM`, `COUNT` và `AVG` thường áp dụng lên fact.

> **Lưu ý về ví dụ trong sách:** Hình 1-6 ghi báo cáo cho tháng 6/2013, nhưng đoạn SQL kế tiếp lọc tháng 1/2013. Đây có vẻ là lỗi không nhất quán trong ví dụ; nó không ảnh hưởng đến nguyên lý được minh họa.

---

## 10. Kiến trúc DW/BI của Kimball

Kiến trúc Kimball có bốn thành phần:

```text
Hệ thống nguồn -> ETL -> Khu vực trình bày -> Ứng dụng BI
```

### 10.1. Hệ thống nguồn vận hành

Đây là những hệ thống ghi nhận giao dịch thực tế. Chúng được xem là nằm ngoài data warehouse vì nhóm DW/BI thường không kiểm soát:

- Cấu trúc dữ liệu.
- Định dạng.
- Quy tắc hoạt động.
- Tần suất thay đổi.

Các hệ thống này ưu tiên hiệu năng và tính sẵn sàng cho xử lý giao dịch, thường chỉ giữ ít lịch sử và không nhất thiết dùng chung định nghĩa sản phẩm, khách hàng, địa lý hoặc lịch với các hệ thống khác.

### 10.2. Hệ thống ETL

ETL là mọi thứ nằm giữa nguồn vận hành và khu vực trình bày:

- **Extract:** đọc, hiểu và sao chép dữ liệu cần thiết từ nguồn.
- **Transform:** làm sạch, chuẩn hóa, kết hợp, loại trùng và áp dụng quy tắc kinh doanh.
- **Load:** tạo cấu trúc vật lý và nạp fact/dimension vào khu vực trình bày.

Các công việc chuyển đổi có thể bao gồm:

- Sửa lỗi chính tả.
- Xử lý giá trị thiếu.
- Chuẩn hóa định dạng.
- Hợp nhất nhiều nguồn.
- Loại bỏ bản ghi trùng.
- Gán surrogate key.
- Giải mã code thành mô tả.
- Tách hoặc ghép cột.
- Làm phẳng các bảng 3NF thành dimension.

ETL cũng có thể tạo metadata chẩn đoán để tìm nguyên nhân chất lượng dữ liệu kém và cải thiện hệ thống nguồn.

Có thể sử dụng một cơ sở dữ liệu chuẩn hóa trong quá trình ETL, nhưng đó không phải sản phẩm cuối cùng. Cấu trúc chuẩn hóa phải nằm ngoài phạm vi truy vấn của người dùng vì nó không đáp ứng tốt yêu cầu dễ hiểu và hiệu năng.

Kimball cảnh báo việc xây cả kho 3NF lẫn khu vực chiều có thể khiến dữ liệu bị nạp hai lần, làm tăng:

- Thời gian phát triển.
- Thời gian xử lý định kỳ.
- Dung lượng lưu trữ.
- Chi phí vận hành.
- Yêu cầu phần cứng.

### 10.3. Khu vực trình bày

Đây là phần người dùng và ứng dụng BI thực sự nhìn thấy. Nó phải có bốn đặc điểm:

1. **Có cấu trúc chiều:** star schema hoặc OLAP cube.
2. **Chứa dữ liệu nguyên tử:** có thể kèm dữ liệu tổng hợp để tăng tốc.
3. **Tổ chức theo quy trình kinh doanh:** không theo từng phòng ban.
4. **Dùng conformed dimensions:** các dimension được chuẩn hóa và dùng chung toàn doanh nghiệp.

Ví dụ, nên có một fact table doanh số thống nhất để bán hàng, marketing, logistics và tài chính cùng sử dụng; không nên tạo bốn cơ sở dữ liệu doanh số gần giống nhưng khác cách tính.

### Conformed dimension và bus architecture

**Conformed dimension** là dimension có nhãn, giá trị và định nghĩa thống nhất, có thể tái sử dụng giữa nhiều fact table.

Ví dụ, Customer Dimension dùng trong bán hàng, giao hàng và hỗ trợ khách hàng phải cùng hiểu "khách hàng" theo một cách tương thích.

Các conformed dimension tạo thành **enterprise data warehouse bus architecture**. Nhờ đó, doanh nghiệp có thể phát triển từng quy trình một cách phân tán và lặp dần nhưng vẫn tích hợp được về sau.

### 10.4. Ứng dụng BI

Ứng dụng BI truy vấn khu vực trình bày để hỗ trợ quyết định. Nó có thể là:

- Công cụ truy vấn ad hoc.
- Báo cáo chuẩn.
- Dashboard có tham số.
- Ứng dụng phân tích.
- Công cụ dự báo.
- Data mining hoặc mô hình thống kê.

Chỉ một tỷ lệ nhỏ người dùng có thể sử dụng công cụ truy vấn ad hoc hiệu quả. Phần lớn nên được phục vụ bằng các ứng dụng hoặc mẫu báo cáo dựng sẵn, có tham số rõ ràng.

---

## 11. Ẩn dụ nhà hàng cho kiến trúc Kimball

Kimball ví kiến trúc DW/BI như một nhà hàng.

### ETL là nhà bếp phía sau

Nhà bếp:

- Nhận nguyên liệu thô.
- Làm sạch và chế biến.
- Áp dụng công thức nhất quán.
- Kiểm tra chất lượng và an toàn.
- Tối ưu luồng công việc.
- Không cho khách tự do đi vào.

ETL cũng:

- Nhận dữ liệu nguồn.
- Làm sạch và chuyển đổi.
- Áp dụng quy tắc kinh doanh một lần.
- Kiểm tra tính toàn vẹn.
- Tối ưu throughput.
- Không cho người dùng truy vấn dữ liệu đang xử lý dở.

Quy tắc nên được xử lý một lần ở ETL thay vì bắt từng người dùng tự tính lại trong báo cáo. Điều đó làm ETL phức tạp hơn, nhưng giúp toàn hệ thống nhất quán hơn.

### Khu vực trình bày là phòng ăn

Khách đánh giá nhà hàng dựa trên:

- Chất lượng món ăn.
- Không gian.
- Dịch vụ.
- Chi phí.

Tương ứng trong DW/BI:

- **Món ăn:** dữ liệu có chất lượng và phù hợp.
- **Không gian:** cấu trúc và giao diện dễ sử dụng.
- **Dịch vụ:** phản hồi nhanh, đúng yêu cầu.
- **Chi phí:** giá trị tạo ra phải tương xứng với đầu tư.

Nhà hàng không có khách sẽ đóng cửa; hệ thống BI không có người dùng cũng là một khoản đầu tư thất bại. Người quản lý phải chủ động theo dõi mức độ hài lòng, vì người dùng không hài lòng có thể âm thầm bỏ hệ thống mà không khiếu nại.

---

## 12. Các kiến trúc DW/BI thay thế

### 12.1. Independent data mart architecture

Trong kiến trúc này, mỗi phòng ban tự tạo data mart riêng:

- Phòng bán hàng có định nghĩa doanh thu riêng.
- Phòng marketing tạo data mart khác từ cùng nguồn.
- Phòng tài chính lại có cách tính khác.

Cách này hấp dẫn trong ngắn hạn vì:

- Phát triển nhanh.
- Chi phí ban đầu thấp.
- Không cần quản trị dữ liệu liên phòng ban.
- Phù hợp với cách cấp ngân sách theo từng bộ phận.

Nhưng về lâu dài:

- Dữ liệu bị trích xuất nhiều lần.
- Lưu trữ dư thừa.
- Định nghĩa không nhất quán.
- Báo cáo giữa các phòng không khớp.
- Mọi người mất thời gian đối chiếu số liệu.

Kimball phản đối mạnh kiến trúc này. Một data mart độc lập vẫn có thể dùng mô hình chiều, nhưng nó vi phạm các nguyên tắc quan trọng: tổ chức theo quy trình, giữ dữ liệu nguyên tử và sử dụng conformed dimensions.

### 12.2. Corporate Information Factory của Inmon

Kiến trúc hub-and-spoke của Inmon thường hoạt động như sau:

```text
Nguồn -> ETL/data acquisition -> Enterprise Data Warehouse 3NF
                                      |
                                      v
                                 Các data mart
                                      |
                                      v
                                 Ứng dụng BI
```

Khác biệt chính:

- Kiến trúc Inmon bắt buộc có một EDW chuẩn hóa 3NF chứa dữ liệu nguyên tử.
- Kiến trúc Kimball tích hợp dữ liệu thông qua bus architecture và conformed dimensions; kho chuẩn hóa trong ETL chỉ là tùy chọn.

Tác giả lưu ý rằng **chuẩn hóa không đồng nghĩa với tích hợp**. Hai nguồn không tương thích vẫn có thể được chuẩn hóa rất kỹ nhưng vẫn mâu thuẫn về định nghĩa. Tích hợp đòi hỏi giải quyết các mâu thuẫn về nhãn, giá trị và quy tắc kinh doanh.

Trong một CIF thuần túy, dữ liệu nguyên tử có thể bị khóa trong EDW 3NF khó truy vấn, trong khi các data mart phía sau lại mang tính phòng ban và chỉ chứa số liệu tổng hợp. Theo Kimball, đây là dạng cực đoan không hoạt động tốt như một data warehouse phục vụ người dùng.

### 12.3. Kiến trúc lai Inmon-Kimball

Kiến trúc lai sử dụng:

1. Một EDW chuẩn hóa 3NF ở phía sau.
2. EDW này không cho người dùng truy vấn.
3. Dữ liệu từ đó được đưa vào khu vực trình bày Kimball.
4. Khu vực trình bày chứa star schema, dữ liệu nguyên tử và conformed dimensions.

Cách này hợp lý nếu doanh nghiệp đã đầu tư vào EDW 3NF nhưng người dùng đang gặp vấn đề về hiệu năng hoặc tính dễ sử dụng.

Nếu bắt đầu từ con số không, kiến trúc lai thường tốn thêm tiền và thời gian vì:

- Dữ liệu nguyên tử được lưu nhiều lần.
- Có thêm một lần di chuyển dữ liệu.
- Cần phát triển và vận hành cả hai tầng.

---

## 13. Năm quan niệm sai về mô hình chiều

### Quan niệm sai 1: Chỉ dành cho dữ liệu tổng hợp

Ngược lại, star schema nên chứa dữ liệu ở mức chi tiết thấp nhất có thể. Không thể dự đoán trước mọi câu hỏi, nên người dùng cần dữ liệu nguyên tử để tự tổng hợp theo nhu cầu.

Bảng tổng hợp chỉ nên bổ sung nhằm tăng tốc các truy vấn phổ biến, không được thay thế dữ liệu chi tiết.

Mô hình chiều cũng không giới hạn lượng lịch sử. Giữ bao nhiêu lịch sử phải dựa vào nhu cầu kinh doanh.

### Quan niệm sai 2: Chỉ dùng cho phòng ban

Mô hình chiều phải được tổ chức theo các quy trình như:

- Đặt hàng.
- Bán hàng.
- Xuất hóa đơn.
- Giao hàng.
- Cuộc gọi dịch vụ.

Một quy trình thường phục vụ nhiều phòng ban. Không nên tạo nhiều bản sao không nhất quán của cùng dữ liệu chỉ vì các bộ phận sử dụng nó khác nhau.

### Quan niệm sai 3: Không mở rộng được

Fact table có thể chứa hàng tỷ hoặc thậm chí hàng nghìn tỷ dòng.

Mô hình chuẩn hóa và mô hình chiều có thể biểu diễn cùng nội dung logic và trả lời cùng câu hỏi. Khác biệt nằm ở mức độ khó sử dụng và hiệu năng, không phải khả năng biểu diễn dữ liệu.

### Quan niệm sai 4: Chỉ phù hợp với nhu cầu dự đoán trước

Không nên thiết kế mô hình chỉ dựa trên danh sách các báo cáo hiện tại, vì danh sách đó sẽ thay đổi.

Thiết kế phải dựa trên **sự kiện đo lường ổn định của quy trình kinh doanh**. Dữ liệu càng chi tiết thì hệ thống càng linh hoạt trước những câu hỏi mới.

Nếu tổng hợp dữ liệu quá sớm, người dùng sẽ gặp bức tường khi muốn drill-down xuống mức chi tiết chưa được lưu.

### Quan niệm sai 5: Không tích hợp được

Các mô hình chiều hoàn toàn có thể tích hợp nếu dùng conformed dimensions và bus architecture.

Việc đạt được thống nhất toàn tổ chức về nhãn, giá trị và định nghĩa rất khó, nhưng khó khăn này tồn tại ở cả mô hình chuẩn hóa lẫn mô hình chiều. Không thể đổ lỗi cho mô hình chiều nếu tổ chức không thực hiện nguyên tắc conformed dimension.

---

## 14. Tư duy theo chiều vượt ra ngoài việc thiết kế bảng

Kimball khuyên áp dụng tư duy chiều ngay từ đầu dự án.

### Khi thu thập yêu cầu

Không nên chỉ hỏi:

- Cần những báo cáo nào?
- Dashboard phải có những biểu đồ nào?

Nên hỏi:

- Chỉ số này được tạo ra từ quy trình kinh doanh nào?
- Sự kiện đo lường nào tạo ra dữ liệu?
- Fact là gì?
- Những dimension nào mô tả sự kiện?

### Khi xác định phạm vi dự án

Mỗi vòng triển khai nên tập trung vào một quy trình kinh doanh, thay vì cố xây một dashboard bao phủ nhiều quy trình chưa được tích hợp.

### Khi lập lộ trình

Cùng lãnh đạo kinh doanh đánh giá từng quy trình theo:

- Giá trị kinh doanh.
- Tính khả thi.

Nên ưu tiên quy trình vừa có tác động lớn vừa có khả năng triển khai cao.

### Khi quản trị dữ liệu

Data governance nên bắt đầu với những danh từ cốt lõi của doanh nghiệp:

- Ngày.
- Khách hàng.
- Sản phẩm.
- Nhân viên.
- Cơ sở.
- Nhà cung cấp.
- Tài khoản.

Các chuyên gia nghiệp vụ cần chịu trách nhiệm về định nghĩa và chất lượng của những dimension này.

---

## 15. Kimball và Agile

Nhiều nguyên tắc Agile phù hợp với Kimball:

- Tập trung vào giá trị kinh doanh.
- Hợp tác chặt chẽ giữa IT và nghiệp vụ.
- Giao tiếp và nhận phản hồi thường xuyên.
- Thích ứng với yêu cầu thay đổi.
- Phát triển theo các phần nhỏ, lặp dần.
- Cung cấp kết quả trong vài tuần thay vì chờ nhiều tháng hoặc nhiều năm.

Tuy nhiên, Agile có thể gặp vấn đề nếu bị hiểu thành "không cần quy hoạch và kiến trúc". Nếu mỗi nhóm nhanh chóng tạo ra một giải pháp riêng, kết quả sẽ là các data silo không tương thích.

Kimball sử dụng **enterprise data warehouse bus matrix** làm khung kiến trúc tổng thể. Ma trận này xác định:

- Các quy trình kinh doanh.
- Các conformed dimension dùng chung.
- Mối quan hệ giữa quy trình và dimension.
- Thứ tự phát triển từng phần.

Conformed dimensions không cản trở Agile mà có thể làm Agile nhanh hơn. Khi các dimension dùng chung đã được xây dựng, một quy trình mới chủ yếu chỉ cần bổ sung fact table và phần ETL tương ứng.

Thông điệp ở đây là:

> Phát triển từng bước nhỏ, nhưng các bước nhỏ phải nằm trong một kiến trúc có khả năng tích hợp.

---

## 16. Danh mục hình minh họa trong chương

- **Hình 1-1:** So sánh star schema và OLAP cube.
- **Hình 1-2:** Sự kiện đo lường của quy trình kinh doanh được chuyển thành fact table.
- **Hình 1-3:** Dimension table chứa các đặc điểm mô tả của những danh từ nghiệp vụ.
- **Hình 1-4:** Các dòng mẫu trong dimension table với hệ phân cấp được phi chuẩn hóa.
- **Hình 1-5:** Fact table và dimension table trong một mô hình chiều.
- **Hình 1-6:** Dimension attribute và fact kết hợp thành một báo cáo đơn giản.
- **Hình 1-7:** Bốn thành phần cốt lõi của kiến trúc DW/BI Kimball.
- **Hình 1-8:** Kiến trúc independent data mart.
- **Hình 1-9:** Kiến trúc Corporate Information Factory dạng hub-and-spoke.
- **Hình 1-10:** Kiến trúc lai có cấu trúc 3NF và khu vực trình bày chiều theo Kimball.

---

## 17. Tổng kết Chương 1

Toàn bộ chương có thể cô đọng thành tám nguyên tắc:

1. DW/BI được xây dựng để hỗ trợ quyết định, không đơn thuần để lưu dữ liệu.
2. Hệ thống phân tích có nhu cầu khác căn bản với hệ thống giao dịch.
3. Thành công được đo bằng mức độ sử dụng và giá trị kinh doanh, không phải độ tinh vi của công nghệ.
4. Fact lưu các phép đo; dimension cung cấp ngữ cảnh.
5. Mỗi fact table phải có một grain rõ ràng và nhất quán.
6. Khu vực người dùng truy vấn phải đơn giản, có tính chiều và chứa dữ liệu nguyên tử.
7. Các quy trình kinh doanh phải được tích hợp bằng conformed dimensions.
8. Có thể phát triển theo Agile và từng bước, nhưng không được tạo ra các data mart cô lập.

Nếu cần nhớ một luồng duy nhất, hãy nhớ:

```text
Nguồn vận hành
      |
      v
ETL: làm sạch, thống nhất và áp dụng quy tắc
      |
      v
Star schema: fact + conformed dimensions
      |
      v
Báo cáo, dashboard và phân tích
      |
      v
Quyết định kinh doanh
```

Đây là nền móng mà các chương tiếp theo của cuốn sách sẽ tiếp tục sử dụng và mở rộng.

---

## Thuật ngữ cần nhớ

| Thuật ngữ | Nghĩa ngắn gọn |
|---|---|
| DW/BI | Hệ thống kho dữ liệu và trí tuệ kinh doanh |
| Operational system | Hệ thống ghi nhận và xử lý giao dịch vận hành |
| Fact | Đại lượng đo lường của một sự kiện kinh doanh |
| Fact table | Bảng chứa các phép đo và khóa nối đến dimension |
| Dimension | Ngữ cảnh mô tả cho fact: ai, cái gì, ở đâu, khi nào, bằng cách nào, tại sao |
| Grain | Ý nghĩa và mức chi tiết chính xác của một dòng fact |
| Star schema | Fact table ở trung tâm, các dimension table bao quanh |
| OLAP cube | Cách triển khai mô hình chiều trong môi trường đa chiều |
| Atomic data | Dữ liệu ở mức chi tiết thấp nhất được thu thập |
| ETL | Extract, Transform, Load - trích xuất, chuyển đổi và nạp dữ liệu |
| Presentation area | Khu vực dữ liệu đã sẵn sàng cho người dùng và ứng dụng BI truy vấn |
| Conformed dimension | Dimension có định nghĩa thống nhất và được dùng chung giữa nhiều mô hình |
| Bus architecture | Khung tích hợp các quy trình thông qua conformed dimensions |
| Data mart | Kho dữ liệu phân tích có phạm vi hẹp; có thể tích hợp hoặc độc lập |
| 3NF | Mô hình chuẩn hóa bậc ba, phù hợp với xử lý vận hành hơn là truy vấn BI trực tiếp |
| Snowflake | Cách chuẩn hóa dimension thành nhiều bảng phụ |

