# Chương 3: Bán lẻ - Retail Sales

> Bản trình bày và giải thích bằng tiếng Việt dựa trên Chương 3 của *The Data Warehouse Toolkit, Third Edition* - Ralph Kimball và Margy Ross. Mục Tổng kết bắt đầu ở trang 109 và kết thúc ở trang 110, nên phạm vi đầy đủ của chương là trang sách 69-110, tương ứng với trang vật lý 105-146 trong tệp PDF nguồn.
>
> Đây là bản diễn giải đầy đủ các luận điểm, khái niệm và ví dụ chính, không phải bản sao từng câu. Các thuật ngữ tiếng Anh quan trọng được giữ lại để tiện đối chiếu với sách.

---

## Mục lục Chương 3 - bản dịch tiếng Việt

### Bán lẻ - trang 69

#### Quy trình thiết kế mô hình chiều gồm bốn bước - trang 70

- Bước 1: Chọn quy trình kinh doanh - trang 70
- Bước 2: Tuyên bố grain - trang 71
- Bước 3: Xác định các dimension - trang 72
- Bước 4: Xác định các fact - trang 72

#### Nghiên cứu tình huống bán lẻ - trang 72

- Bước 1: Chọn quy trình kinh doanh - trang 74
- Bước 2: Tuyên bố grain - trang 74
- Bước 3: Xác định các dimension - trang 76
- Bước 4: Xác định các fact - trang 76

#### Chi tiết các dimension table - trang 79

- Date Dimension - trang 79
- Product Dimension - trang 83
- Store Dimension - trang 87
- Promotion Dimension - trang 89
- Các dimension bán lẻ khác - trang 92
- Degenerate dimension dành cho số giao dịch - trang 93

#### Vận dụng retail schema trong truy vấn - trang 94

#### Khả năng mở rộng của retail schema - trang 95

#### Factless fact table - trang 97

#### Khóa của dimension table và fact table - trang 98

- Surrogate key của dimension table - trang 98
- Natural key và durable supernatural key của dimension - trang 100
- Surrogate key cho degenerate dimension - trang 101
- Smart key của Date Dimension - trang 101
- Surrogate key của fact table - trang 102

#### Chống lại xu hướng chuẩn hóa - trang 104

- Snowflake schema với các dimension được chuẩn hóa - trang 104
- Outrigger - trang 106
- Centipede fact table có quá nhiều dimension - trang 108

#### Tổng kết - trang 109-110

---

# Phần I: Quy trình thiết kế bốn bước

## 1. Mục tiêu của chương

Chương 3 là lần đầu cuốn sách xây dựng một mô hình chiều hoàn chỉnh từ đầu đến cuối.

Tác giả chọn bán lẻ vì quy trình thanh toán tại quầy khá quen thuộc, nhưng các nguyên tắc trong chương không chỉ dùng cho siêu thị. Chúng cũng áp dụng cho ngân hàng, bảo hiểm, hàng không, viễn thông, giáo dục và nhiều lĩnh vực khác.

Các chủ đề chính gồm:

- Quy trình thiết kế bốn bước.
- Grain của fact table.
- Transaction fact table.
- Fact cộng được, không cộng được và fact dẫn xuất.
- Thuộc tính dimension và nhiều hierarchy.
- Date Dimension và Time-of-Day Dimension.
- Causal dimension như chương trình khuyến mãi.
- Degenerate dimension như số hóa đơn.
- Null trong mô hình chiều.
- Khả năng mở rộng mô hình.
- Factless fact table.
- Surrogate, natural và durable key.
- Snowflake dimension.
- Outrigger.
- Centipede fact table.

## 2. Bước 1: Chọn quy trình kinh doanh

**Business process** là một hoạt động cụ thể mà tổ chức thực hiện, ví dụ:

- Nhận đơn hàng.
- Xuất hóa đơn.
- Nhận thanh toán.
- Xử lý cuộc gọi dịch vụ.
- Đăng ký sinh viên.
- Thực hiện thủ thuật y tế.
- Xử lý yêu cầu bồi thường.

Quy trình kinh doanh thường có bốn đặc điểm:

1. Nó thường được diễn đạt bằng động từ vì đó là một hoạt động.
2. Nó thường được một hệ thống vận hành hỗ trợ.
3. Nó tạo ra hoặc ghi nhận các chỉ số hiệu suất.
4. Đầu ra của quy trình này có thể trở thành đầu vào của quy trình tiếp theo.

Chuỗi quy trình như vậy thường tạo ra một chuỗi fact table:

```text
Đặt hàng -> Giao hàng -> Lập hóa đơn -> Thu tiền
```

Mỗi bước đo một sự kiện khác nhau, ở grain và thời điểm khác nhau, nên thường cần fact table riêng.

### Quy trình kinh doanh không phải phòng ban

Marketing, tài chính hoặc bán hàng là các bộ phận, không phải quy trình.

Một quy trình như bán hàng có thể được nhiều bộ phận cùng phân tích. Nếu thiết kế data mart riêng cho từng phòng, dữ liệu sẽ bị sao chép và có nguy cơ mang tên gọi, định nghĩa hoặc giá trị khác nhau.

Cách tốt hơn là công bố dữ liệu của một quy trình một lần để nhiều bộ phận cùng sử dụng.

### Sáng kiến chiến lược cũng chưa phải quy trình

Người dùng có thể nói họ muốn phân tích một sáng kiến lớn như "nâng cao trải nghiệm khách hàng". Nhóm DW/BI phải phân rã sáng kiến đó thành những quy trình tạo dữ liệu cụ thể, chẳng hạn:

- Mua hàng.
- Giao hàng.
- Khiếu nại.
- Trả hàng.
- Tương tác với trung tâm hỗ trợ.

Mỗi quy trình mới là một đơn vị công việc đủ cụ thể để mô hình hóa.

## 3. Bước 2: Tuyên bố grain

Grain trả lời câu hỏi:

> Một dòng trong fact table đại diện chính xác cho điều gì?

Ví dụ về grain:

- Một dòng cho mỗi sản phẩm được quét trong một giao dịch bán hàng.
- Một dòng cho mỗi hạng mục trên hóa đơn bác sĩ.
- Một dòng cho mỗi thẻ lên máy bay được quét tại cửa ra máy bay.
- Một dòng cho mỗi sản phẩm tại mỗi kho trong ảnh chụp tồn kho hằng ngày.
- Một dòng cho mỗi tài khoản ngân hàng trong mỗi tháng.

Grain nên được viết bằng ngôn ngữ nghiệp vụ, không chỉ liệt kê một tập khóa.

Ví dụ tốt:

> Một dòng cho mỗi sản phẩm trên mỗi giao dịch tại quầy POS.

Ví dụ chưa tốt:

> Khóa là DateKey, ProductKey, StoreKey và TransactionNumber.

Danh sách khóa có thể tương đương về kỹ thuật, nhưng nó không truyền đạt ý nghĩa nghiệp vụ rõ ràng.

### Vì sao grain là hợp đồng của mô hình?

Sau khi grain được xác định:

- Mỗi dimension phải có một giá trị duy nhất tại grain đó.
- Mỗi fact phải là phép đo hợp lệ tại grain đó.
- Không được trộn dữ liệu từ grain khác vào cùng bảng.

Đây là lỗi phổ biến nhất trong thiết kế mô hình chiều. Khi grain không rõ:

- Nhóm thiết kế tranh luận liên tục về dimension.
- Các fact không phù hợp âm thầm lọt vào bảng.
- Truy vấn dễ đếm trùng.
- Người dùng nhận được kết quả không nhất quán.

Nếu ở bước 3 hoặc 4 phát hiện grain sai, phải quay lại bước 2, viết lại grain rồi đánh giá lại toàn bộ dimension và fact.

## 4. Bước 3: Xác định dimension

Dimension được xác định bằng câu hỏi:

> Người kinh doanh mô tả sự kiện đo lường này bằng cách nào?

Dimension thường trả lời:

- Ai?
- Cái gì?
- Ở đâu?
- Khi nào?
- Tại sao?
- Bằng cách nào?

Các dimension phổ biến gồm Date, Product, Customer, Employee, Facility, Promotion và Payment Method.

Mỗi dimension cần được bổ sung các thuộc tính văn bản phong phú để người dùng lọc, phân nhóm, drill-down và đặt nhãn báo cáo.

Điều kiện quan trọng là dimension phải có một giá trị duy nhất đối với một fact row. Nếu một dimension làm phát sinh thêm nhiều fact row, dimension đó không phù hợp với grain hiện tại hoặc grain cần được viết lại.

## 5. Bước 4: Xác định fact

Fact trả lời câu hỏi:

> Quy trình đang đo điều gì?

Fact thường là các đại lượng số như:

- Số lượng đặt hàng.
- Số lượng bán.
- Doanh thu.
- Chiết khấu.
- Chi phí.
- Lợi nhuận.

Tất cả fact phải phù hợp với grain đã tuyên bố. Một phép đo ở grain khác phải được phân bổ hợp lệ xuống grain hiện tại hoặc được đặt trong một fact table riêng.

### Hai nguồn đầu vào của thiết kế

Hình 3-1 minh họa rằng mô hình được xây từ cả yêu cầu kinh doanh và thực tế dữ liệu nguồn:

```text
Yêu cầu kinh doanh + Thực tế dữ liệu nguồn
                         |
                         v
        Process + Grain + Dimensions + Facts
```

Không nên chỉ nhìn schema của hệ thống nguồn rồi tự thiết kế. Dữ liệu nguồn cho biết cái gì tồn tại, nhưng không thay thế được hiểu biết về cách doanh nghiệp ra quyết định.

---

# Phần II: Case study chuỗi siêu thị

## 6. Bối cảnh nghiệp vụ

Doanh nghiệp trong ví dụ có:

- 100 cửa hàng.
- Hoạt động tại 5 bang.
- Nhiều bộ phận như thực phẩm, đông lạnh, sữa, thịt, rau quả, bánh, hoa và chăm sóc sức khỏe.
- Khoảng 60.000 SKU trong mỗi cửa hàng.

Dữ liệu được thu thập ở nhiều điểm:

- Tại quầy thanh toán khi khách mua hàng.
- Tại cửa sau khi nhà cung cấp giao hàng.
- Trong các hệ thống đặt hàng, tồn kho và tiếp nhận hàng.

Chương này tập trung vào dữ liệu **point-of-sale - POS**, tức dữ liệu được tạo khi mã vạch sản phẩm được quét tại quầy.

Hình 3-2 là hóa đơn mẫu, chứa cửa hàng, thu ngân, mã và mô tả sản phẩm, giá, chiết khấu hoặc coupon, tổng tiền, số mặt hàng, số giao dịch và ngày giờ.

Mục tiêu kinh doanh là vừa tăng doanh số và số khách hàng, vừa kiểm soát giá vốn và chi phí. Hai lĩnh vực được quan tâm đặc biệt là định giá và khuyến mãi.

Một đợt giảm giá lớn có thể làm số lượng bán tăng gấp nhiều lần, nhưng doanh nghiệp vẫn có thể bị lỗ. Vì vậy, chỉ nhìn doanh số tăng là chưa đủ; cần phân tích cả doanh thu, chi phí, lợi nhuận và tác động lan sang các sản phẩm khác.

## 7. Áp dụng bước 1: chọn POS retail sales

Quy trình được chọn là:

> Các giao dịch bán lẻ tại quầy POS.

Dữ liệu này giúp trả lời:

- Sản phẩm nào được bán?
- Bán tại cửa hàng nào?
- Vào ngày nào?
- Trong giao dịch nào?
- Với chương trình khuyến mãi nào?
- Thu ngân nào xử lý?
- Sử dụng phương thức thanh toán gì?

Dự án DW/BI đầu tiên nên chọn quy trình vừa có giá trị cao đối với doanh nghiệp, vừa có tính khả thi cao.

Tính khả thi bao gồm:

- Dữ liệu có sẵn không?
- Chất lượng có đủ tốt không?
- Tổ chức đã sẵn sàng chưa?
- Các bên liên quan có hợp tác không?

## 8. Áp dụng bước 2: grain nguyên tử

Grain được chọn là:

> Một dòng cho mỗi sản phẩm trên một giao dịch POS.

Nếu cùng một sản phẩm được quét nhiều lần nhưng POS gộp lại thành một dòng trên hóa đơn, fact table cũng có một dòng với số lượng tương ứng.

### Vì sao phải giữ mức chi tiết này?

Dữ liệu nguyên tử cung cấp nhiều dimension nhất và cho phép phân tích chính xác nhất.

Người dùng có thể hỏi:

- Doanh số thứ Hai khác Chủ nhật thế nào?
- Có nên giữ nhiều kích cỡ của cùng một thương hiệu không?
- Bao nhiêu người đã dùng khuyến mãi giảm giá dầu gội?
- Khuyến mãi nước ngọt của đối thủ ảnh hưởng đến doanh số của ta ra sao?

Không câu hỏi nào yêu cầu xem một giao dịch riêng lẻ, nhưng tất cả đều cần dữ liệu chi tiết để lọc theo đúng tổ hợp điều kiện.

Dữ liệu tổng hợp có thể được tạo từ dữ liệu chi tiết; chiều ngược lại là bất khả thi.

> DW/BI cần grain thấp nhất không phải vì người dùng muốn đọc từng dòng, mà vì họ cần cắt dữ liệu chi tiết theo những cách rất chính xác.

## 9. Áp dụng bước 3: các dimension

Từ grain trên, các dimension được xác định gồm:

- Date.
- Product.
- Store.
- Promotion.
- Cashier.
- Payment Method.
- POS Transaction Number.

POS Transaction Number là một trường hợp đặc biệt: degenerate dimension.

Quy tắc lựa chọn là:

> Nếu một thuộc tính mô tả có đúng một giá trị đối với mỗi dòng ở grain đã chọn, nó có thể trở thành dimension của fact table.

## 10. Áp dụng bước 4: các fact

POS có thể cung cấp:

- `Sales Quantity`: số lượng bán.
- `Regular Unit Price`: đơn giá thông thường.
- `Discount Unit Price`: mức giảm giá trên một đơn vị.
- `Net Unit Price`: đơn giá khách thực trả.
- `Extended Discount Dollar Amount`: tổng chiết khấu của dòng hàng.
- `Extended Sales Dollar Amount`: doanh thu dòng hàng.
- `Extended Cost Dollar Amount`: giá vốn dòng hàng.
- `Extended Gross Profit Dollar Amount`: lợi nhuận gộp dòng hàng.

Các phép tính:

```text
Extended Sales
= Sales Quantity x Net Unit Price
```

```text
Extended Discount
= Sales Quantity x Discount Unit Price
```

```text
Extended Gross Profit
= Extended Sales - Extended Cost
```

Hình 3-3 trình bày star schema ban đầu với Retail Sales Fact ở giữa và sáu dimension xung quanh.

### Fact cộng được

Bốn fact cộng được theo mọi dimension là:

- Sales Quantity.
- Extended Discount Amount.
- Extended Sales Amount.
- Extended Cost Amount.

Extended Gross Profit cũng cộng được mặc dù là fact dẫn xuất.

## 11. Derived fact - fact dẫn xuất

Gross profit được tính từ doanh thu trừ giá vốn. Tác giả thường khuyến nghị lưu vật lý kết quả này trong fact table vì:

- ETL tính công thức một lần theo cùng quy tắc.
- Mọi người dùng nhận cùng kết quả.
- Giảm nguy cơ người dùng tính sai.
- Chi phí lưu thêm một cột nhỏ hơn nhiều so với hậu quả báo cáo sai lợi nhuận.

Có thể không lưu vật lý nếu dùng một database view bắt buộc cho mọi truy cập. Nhưng không được để một số người dùng đi qua view còn người khác truy cập bảng gốc, vì khi đó quy tắc tính toán không còn được bảo đảm.

Tính trong BI tool cũng chỉ an toàn nếu mọi người sử dụng cùng công cụ và cùng semantic layer.

## 12. Non-additive facts

### Gross margin

Gross margin được tính:

```text
Gross Margin
= Gross Profit / Sales Revenue
```

Đây là fact không cộng được. Muốn tính margin cho một nhóm sản phẩm, cửa hàng hoặc khoảng thời gian, phải:

1. Cộng tổng lợi nhuận.
2. Cộng tổng doanh thu.
3. Chia hai tổng.

Công thức đúng:

```text
SUM(Gross Profit) / SUM(Sales Revenue)
```

Không được dùng `SUM(Gross Margin)` hoặc lấy trung bình đơn giản của margin từng dòng.

### Unit price

Đơn giá cũng không cộng được.

Ví dụ:

- Bán 1 sản phẩm với giá 1 USD.
- Bán 4 sản phẩm với giá 0,50 USD.

Kết quả đúng:

```text
Tổng số lượng = 5
Tổng doanh thu = 3 USD
Đơn giá trung bình có trọng số = 3 / 5 = 0,60 USD
```

Không thể cộng hai đơn giá thành 1,50 USD hoặc lấy trung bình đơn giản thành 0,75 USD.

Quy tắc tổng quát:

> Đối với tỷ lệ và giá bình quân, hãy lưu tử số và mẫu số cộng được, tổng hợp chúng trước rồi mới thực hiện phép chia.

Một số fact vốn không cộng được, như nhiệt độ, vẫn có thể được lưu nếu hệ thống nguồn cung cấp. Việc dùng trung bình phải dựa trên quy tắc được chuyên gia nghiệp vụ chấp thuận.

---

# Phần III: Transaction fact table

## 13. Đặc điểm

Transaction fact table có các đặc điểm:

- Grain thường là một giao dịch hoặc một dòng giao dịch.
- Chỉ có dòng khi sự kiện xảy ra nên thường khá thưa.
- Tuy thưa, nó có thể chứa hàng tỷ hoặc hàng nghìn tỷ dòng.
- Nó thường có nhiều dimension.
- Các phép đo thường cộng được nếu đã nhân với số lượng, thay vì chỉ giữ giá trị trên một đơn vị.

### Ước lượng kích thước

Tác giả minh họa một phép ước lượng nhanh:

```text
Doanh thu năm: 4 tỷ USD
Giá trung bình mỗi mặt hàng: 2 USD
Số dòng giao dịch ước tính: 2 tỷ dòng/năm
```

Đây không thay thế việc khảo sát hệ thống nguồn, nhưng là phép kiểm tra chéo hữu ích. Khi thiết kế, nên sử dụng nhiều cách ước lượng để xem kết quả có hợp lý hay không.

---

# Phần IV: Chi tiết các dimension

## 14. Date Dimension

Date Dimension xuất hiện trong gần như mọi mô hình vì phần lớn quy trình tạo ra chuỗi số liệu theo thời gian.

Date Dimension có grain:

> Một dòng cho mỗi ngày.

Có thể tạo sẵn 10-20 năm dữ liệu. Hai mươi năm chỉ khoảng 7.300 dòng nên đây là một dimension nhỏ.

Hình 3-4 đưa ra các thuộc tính tiêu biểu:

- Date Key.
- Ngày thực.
- Mô tả ngày đầy đủ.
- Thứ trong tuần.
- Số thứ tự ngày trong tháng.
- Số thứ tự ngày trong năm.
- Số ngày trong tháng hoặc kỳ tài chính.
- Ngày cuối tháng?
- Ngày kết thúc tuần.
- Số tuần trong năm.
- Tên và số tháng.
- `YYYY-MM`.
- Quý và `Year-Quarter`.
- Năm dương lịch.
- Tuần, tháng, quý, nửa năm và năm tài chính.
- Ngày lễ?
- Ngày làm việc hay cuối tuần?
- SQL date stamp.

Hình 3-5 minh họa các dòng từ ngày 1 đến 8 tháng 1 năm 2013, mỗi ngày có đầy đủ nhãn lịch, quý, năm tài chính, ngày lễ và loại ngày.

### Vì sao không chỉ dùng cột DATE trong fact table?

Các hàm ngày SQL không thể thuận tiện biểu diễn mọi logic kinh doanh, chẳng hạn:

- Tuần tài chính.
- Kỳ kế toán.
- Mùa bán hàng.
- Ngày lễ.
- Ngày làm việc hoặc cuối tuần.
- Ngày đóng kỳ.
- Lịch 4-4-5.
- Những ngày đặc biệt của doanh nghiệp.

Logic lịch nên nằm tập trung trong Date Dimension, không nên được viết lại trong từng báo cáo.

### Cờ phải có mô tả rõ ràng

Hình 3-6 so sánh báo cáo dùng `Y/N` với báo cáo dùng `Holiday/Non-Holiday`.

Dimension attribute xuất hiện trong menu lọc và nhãn báo cáo, nên giá trị đầy đủ giúp báo cáo tự giải thích. Việc giải mã nên được thực hiện trong data warehouse, không phải lặp lại ở mỗi BI tool.

### Thuộc tính ngày tương đối

Có thể thêm:

- `IsCurrentDay`.
- `IsCurrentMonth`.
- `IsPrior60Days`.
- `IsFiscalMonthEnd`.
- `DayLag`.
- `MonthLag`.
- `QuarterLag`.

Những thuộc tính này phải được cập nhật định kỳ.

Ngày hiện tại trong warehouse thường không phải hôm nay theo đồng hồ, mà là ngày dữ liệu mới nhất đã được nạp, có thể là hôm qua.

Các BI tool hiện đại có thể tự xử lý phép so sánh kỳ trước, nên không phải lúc nào cũng cần lưu mọi cột lag.

## 15. Time-of-Day: dimension hay fact?

Không nên gộp ngày và giờ thành một dimension duy nhất:

- 20 năm theo ngày: khoảng 7.300 dòng.
- 20 năm theo phút: hơn 10 triệu dòng.
- Theo giây: hơn 31 triệu dòng mỗi năm.

Điều này làm dimension được truy vấn thường xuyên nhất trở nên quá lớn.

### Dùng Time-of-Day Dimension khi cần

Nếu người dùng cần lọc hoặc nhóm theo khoảng 15 phút, giờ, ca làm việc, buổi sáng/chiều/tối, giờ ăn trưa hoặc khung giờ cao điểm, có thể tạo Time-of-Day Dimension với một dòng cho mỗi phút trong ngày, tổng cộng 1.440 dòng.

### Chỉ lưu timestamp khi không cần hierarchy thời gian

Nếu không cần nhóm theo các thuộc tính trên, lưu timestamp trực tiếp trong fact table.

Timestamp còn giúp tính thời lượng giao dịch, khoảng cách thời gian giữa hai sự kiện và độ trễ vượt qua ranh giới ngày, tháng hoặc năm.

## 16. Product Dimension

Product Dimension mô tả từng SKU.

Mỗi cửa hàng có thể có khoảng 60.000 SKU, nhưng dimension toàn doanh nghiệp có thể có 300.000 dòng trở lên vì có nhiều phương án phân loại, các sản phẩm lịch sử không còn bán và sản phẩm thay đổi theo thời gian.

Dữ liệu thường lấy từ product master được quản lý ở trụ sở.

### Làm phẳng hierarchy nhiều-một

Hierarchy hàng hóa có thể là:

```text
SKU -> Brand -> Subcategory -> Category -> Department
```

Hình 3-7 trình bày các dòng Product Dimension mẫu với sản phẩm, thương hiệu, phân nhóm, nhóm hàng, bộ phận và hàm lượng chất béo.

Mỗi cấp nên được lưu trực tiếp trên dòng SKU.

Việc một department được lặp lại hàng nghìn lần là hoàn toàn chấp nhận được. Dimension nhỏ hơn fact table rất nhiều; không nên hy sinh sự đơn giản và hiệu năng để tiết kiệm một lượng dung lượng không đáng kể.

Hình 3-8 mở rộng Product Dimension với:

- SKU Number.
- Product Description.
- Brand.
- Subcategory.
- Category.
- Department.
- Package Type và Package Size.
- Fat Content.
- Diet Type.
- Weight và đơn vị.
- Storage Type.
- Shelf Life Type.
- Kích thước trên kệ.

Không phải mọi thuộc tính đều thuộc hierarchy. Người dùng vẫn có thể kết hợp tự do, ví dụ tìm tất cả SKU thuộc nhóm ngũ cốc được đóng gói dạng túi.

### Mã có ý nghĩa nhúng

Nếu một phần mã sản phẩm biểu diễn nhà sản xuất hoặc ngành hàng:

- Vẫn giữ toàn bộ mã gốc làm natural key.
- Đồng thời tách từng phần có ý nghĩa thành thuộc tính riêng.
- Cung cấp tên mô tả đầy đủ cho người dùng.

Không nên bắt người dùng sử dụng phép cắt chuỗi để lọc dữ liệu.

## 17. Giá trị số: fact hay dimension attribute?

Ví dụ điển hình là giá niêm yết chuẩn.

Nếu giá trị chủ yếu phục vụ tính toán, nó nên nằm trong fact table. Có thể nhân giá chuẩn với số lượng để tạo extended standard amount có tính cộng được.

Nếu giá trị chủ yếu dùng để lọc, phân nhóm, tạo dải giá hoặc làm nhãn thì nên đặt trong dimension.

Nếu nó phục vụ cả hai mục đích, hãy lưu ở cả hai nơi:

- Fact table giữ giá trị đúng tại thời điểm giao dịch.
- Product Dimension có thể giữ giá chuẩn hiện tại.

Việc lặp dữ liệu này là có chủ đích nhằm giữ mô hình dễ sử dụng:

> Dữ liệu dùng trong tính toán đặt ở fact; dữ liệu dùng trong lọc, nhóm và nhãn đặt ở dimension.

## 18. Drill-down bằng dimension attribute

Drill-down chỉ là thêm một dimension attribute vào phần nhóm của báo cáo.

Hình 3-9 bắt đầu với doanh số theo department, sau đó drill-down theo Brand hoặc Fat Content.

Fat Content không nằm trong hierarchy hàng hóa nhưng vẫn dùng để drill-down được.

```text
Bakery
  -> Baked Well
  -> Fluffy
  -> Light
```

hoặc:

```text
Bakery
  -> Nonfat
  -> Reduced Fat
  -> Regular Fat
```

Drill-up là thao tác ngược lại: loại bỏ một thuộc tính khỏi tiêu đề hàng hoặc `GROUP BY`.

Một Product Dimension có 50 thuộc tính tốt sẽ cung cấp nhiều khả năng phân tích hơn một dimension chỉ có vài mã kỹ thuật.

## 19. Store Dimension

Store Dimension mô tả từng cửa hàng.

Không phải doanh nghiệp nào cũng có sẵn store master hoàn chỉnh. Nhóm dự án có thể phải ghép thông tin từ POS, hệ thống bất động sản, nhân sự, vận hành cửa hàng và hệ thống tài chính.

### Nhiều hierarchy trong cùng dimension

Store Dimension có thể hỗ trợ hierarchy địa lý:

```text
Store -> ZIP Code -> County -> State
```

và hierarchy tổ chức:

```text
Store -> District -> Region
```

Hai hierarchy cùng tồn tại trong một dimension.

Tác giả lưu ý thành phố và bang tại Hoa Kỳ không tạo thành một hierarchy hoàn toàn an toàn vì nhiều bang có thành phố trùng tên. Nên cung cấp thuộc tính kết hợp `City-State`.

Hình 3-10 đề xuất các thuộc tính:

- Store Number và Store Name.
- Địa chỉ.
- City, County, City-State, State, ZIP.
- Store Manager.
- District và Region.
- Floor Plan Type.
- Photo Processing Type.
- Financial Service Type.
- Selling Square Footage.
- Total Square Footage.
- First Open Date.
- Last Remodel Date.

Các loại cửa hàng nên được biểu diễn bằng mô tả dễ đọc thay vì mã một ký tự.

### Selling square footage là dimension attribute

Diện tích bán hàng là số và về lý thuyết có thể cộng giữa các cửa hàng. Tuy nhiên, nó khá ổn định, mô tả cửa hàng và chủ yếu dùng để lọc hoặc làm nhãn.

Vì vậy nó phù hợp hơn với Store Dimension.

## 20. Ngày nằm bên trong một dimension

`First Open Date` và `Last Remodel Date` có thể chỉ là cột kiểu ngày.

Nếu người dùng muốn phân tích theo các thuộc tính đặc biệt như kỳ tài chính hoặc ngày lễ, chúng có thể trở thành khóa tới các view đóng vai trò khác nhau của Date Dimension.

```text
Date Dimension
      ^
First Open Date view
      ^
Store Dimension
```

View phải đổi tên thuộc tính rõ ràng:

- First Open Fiscal Month.
- First Open Calendar Year.
- First Open Holiday Indicator.

Đây đồng thời là:

- **Role-playing dimension:** cùng Date Dimension đóng một vai trò khác.
- **Outrigger:** Store Dimension tham chiếu một dimension phụ.

Nếu người dùng không cần phân tích ngày theo các thuộc tính lịch đặc biệt, một cột DATE thông thường là đủ.

## 21. Promotion Dimension

Promotion Dimension mô tả các điều kiện có thể tác động đến doanh số:

- Temporary price reduction.
- Quảng cáo.
- Trưng bày trong cửa hàng.
- Coupon.

Nó được gọi là **causal dimension** vì mô tả những yếu tố được cho là nguyên nhân làm doanh số thay đổi.

Hình 3-11 gồm các thuộc tính như:

- Promotion Code và Promotion Name.
- Price Reduction Type.
- Promotion Media Type.
- Ad Type.
- Display Type.
- Coupon Type.
- Ad Media Name.
- Display Provider.
- Promotion Cost.
- Ngày bắt đầu và kết thúc.

### Đánh giá khuyến mãi

Một chương trình không nên chỉ được đánh giá bằng doanh số trong thời gian chạy. Cần xem xét các yếu tố sau.

#### Lift

Doanh số tăng bao nhiêu so với mức cơ sở giả định nếu không khuyến mãi?

```text
Lift = Actual Sales - Baseline Sales
```

Baseline phải được ước lượng từ lịch sử hoặc mô hình phân tích.

#### Time shifting

Khách hàng có chỉ chuyển thời điểm mua không?

Ví dụ, doanh số tăng trong tuần khuyến mãi nhưng giảm trước và sau đó. Khi ấy chương trình không thật sự tạo thêm nhu cầu.

#### Cannibalization

Sản phẩm được khuyến mãi tăng doanh số nhưng làm giảm doanh số của sản phẩm khác trên cùng kệ.

#### Market growth

Toàn bộ category có tăng ròng sau khi xét giai đoạn trước, trong và sau chương trình không?

#### Profitability

Lợi nhuận tăng thêm phải được tính sau khi xem xét:

- Lift.
- Time shifting.
- Cannibalization.
- Chi phí triển khai chương trình.

### Gộp hay tách các cơ chế khuyến mãi?

Có thể gộp giá giảm, quảng cáo, trưng bày và coupon trong một Promotion Dimension; hoặc tách thành bốn dimension.

Gộp hợp lý khi các cơ chế tương quan cao và thường xuất hiện cùng nhau. Khi đó chỉ lưu những tổ hợp thực sự xảy ra.

Tách hợp lý khi:

- Người dùng suy nghĩ về chúng như các khái niệm độc lập.
- Việc quản trị từng dimension riêng dễ hơn.

Hai thiết kế có thể chứa cùng lượng thông tin; lựa chọn phụ thuộc yêu cầu và cách tư duy của doanh nghiệp.

### Promotion Cost không thuộc Sales Fact

Chi phí toàn bộ chương trình không đúng grain "một sản phẩm trên một giao dịch POS". Nếu chép chi phí đó vào từng dòng bán hàng, chi phí sẽ bị nhân lên.

Promotion Cost có thể là thuộc tính để lọc hoặc phân nhóm trong Promotion Dimension, nhưng phép đo chi phí chính thức phải nằm trong fact table có grain "một chương trình khuyến mãi".

## 22. Null trong mô hình chiều

### Null foreign key

Không được để null trong foreign key của fact table.

Ví dụ nhiều giao dịch không có khuyến mãi. Promotion Dimension phải có dòng:

```text
PromotionKey = 0
PromotionName = No Promotion
```

Fact table tham chiếu dòng này.

Nếu để null:

- Toàn vẹn tham chiếu bị phá vỡ.
- Không thể join bằng phép bằng thông thường.
- Người dùng dễ bỏ sót dữ liệu.

### Null dimension attribute

Nên thay bằng nhãn rõ ràng:

- Unknown.
- Not Applicable.
- Not Yet Available.

Không nên dùng chuỗi rỗng, dấu chấm hoặc null. Null có thể biến mất khỏi menu lọc và tạo ra kết quả nhóm khó hiểu.

### Null fact

Đối với phép đo, thường nên giữ null thay vì thay bằng 0.

Null có nghĩa "không biết" hoặc "không có phép đo"; 0 là một giá trị đo thực sự. Thay null bằng 0 có thể làm sai `AVG`, `COUNT`, `MIN` và các phép thống kê khác.

## 23. Cashier và Payment Method Dimension

### Cashier Dimension

Mỗi giao dịch thường có một thu ngân, vì vậy Cashier Dimension phù hợp.

Nó chỉ nên chứa những thuộc tính nhân viên không nhạy cảm cần cho phân tích. Cần có dòng `No Cashier` cho quầy tự thanh toán.

### Payment Method

Nếu mỗi giao dịch chỉ dùng một phương thức thanh toán, có thể gắn Payment Method Dimension trực tiếp với Sales Fact.

Nếu một giao dịch có nhiều phương thức thanh toán, Payment Method không còn có một giá trị duy nhất tại grain dòng sản phẩm.

Không nên đổi grain thành:

> Một dòng cho mỗi phương thức thanh toán, mỗi sản phẩm và mỗi giao dịch.

Grain đó không tự nhiên và có thể nhân các dòng bán hàng.

Giải pháp phù hợp hơn là tạo Payment Fact riêng với một trong hai grain:

- Một dòng cho mỗi giao dịch, các loại thanh toán là các fact riêng; hoặc
- Một dòng cho mỗi phương thức thanh toán trong mỗi giao dịch.

## 24. Degenerate Dimension

POS Transaction Number xuất hiện trên mọi dòng hàng.

Trong hệ thống vận hành, nó thường là khóa của transaction header. Nhưng các thuộc tính header như ngày và cửa hàng đã trở thành những dimension độc lập trong star schema.

Số giao dịch vẫn hữu ích để:

- Gom tất cả sản phẩm trong cùng giỏ hàng.
- Phân tích market basket.
- Truy ngược về hệ thống vận hành.
- Tham gia primary key của fact table.

Vì không còn thuộc tính nào để tạo bảng Transaction Dimension, số giao dịch nằm trực tiếp trong fact table. Đây là **degenerate dimension**.

Các ví dụ phổ biến khác:

- Order Number.
- Invoice Number.
- Bill-of-Lading Number.

Trong case study, nếu các lần quét cùng sản phẩm được gộp thành một dòng, khóa logic của Sales Fact có thể là:

```text
POS Transaction Number + Product Key
```

Nếu thực thể header vẫn còn thuộc tính mô tả đáng kể, hãy tạo dimension bình thường; khi đó nó không còn là degenerate dimension.

---

# Phần V: Sử dụng và mở rộng schema

## 25. Retail schema trong truy vấn

Hình 3-12 minh họa câu hỏi:

> Doanh số hằng tuần theo chương trình khuyến mãi của nhóm đồ ăn nhẹ trong tháng 1/2013 tại các cửa hàng thuộc Boston District là bao nhiêu?

Các điều kiện đến từ dimension:

- Date Dimension: tháng 1 và năm 2013.
- Store Dimension: Boston District.
- Product Dimension: Snacks Category.

Nhóm kết quả theo:

- Calendar Week Ending Date.
- Promotion Name.

Phép đo đến từ fact:

- `SUM(Extended Sales Dollar Amount)`.

Hình 3-13 trình bày cả kết quả dạng dòng và dạng bảng chéo. Nó cho thấy vai trò rõ ràng:

```text
Dimension -> lọc, phân nhóm và nhãn
Fact      -> số liệu được tổng hợp
```

Chất lượng dimension attribute quyết định rất lớn đến chất lượng báo cáo.

## 26. Khả năng mở rộng

Vài năm sau, doanh nghiệp triển khai chương trình khách hàng thân thiết. Bây giờ có thể xác định người mua thay vì chỉ biết một giỏ hàng vô danh.

Cách mở rộng:

1. Tạo Frequent Shopper Dimension.
2. Thêm Shopper Key vào Sales Fact.
3. Các fact lịch sử nhận khóa đại diện cho `Prior to Frequent Shopper Program`.
4. Giao dịch mới không xác định được khách nhận khóa `Frequent Shopper Not Identified`.

Không được để Shopper Key bằng null.

### Vì sao mở rộng được?

Sales Fact ban đầu đã lưu ở grain dòng giao dịch nguyên tử. Shopper có đúng một giá trị cho mỗi giao dịch nên dimension mới được thêm mà không thay đổi dimension cũ, fact cũ hoặc kết quả của báo cáo cũ.

Nếu dữ liệu ban đầu đã được tổng hợp theo ngày, cửa hàng, sản phẩm và promotion thì không thể bổ sung khách hàng, vì nhiều người mua đã bị gộp vào cùng dòng.

Đây là một lợi ích quan trọng của atomic grain.

### Những kiểu mở rộng an toàn

#### Thuộc tính dimension mới

Thêm cột vào dimension. Nếu dữ liệu lịch sử không tồn tại, điền `Not Available`.

Nếu muốn theo dõi lịch sử thay đổi của thuộc tính mới, thiết kế sẽ phức tạp hơn và cần kỹ thuật SCD.

#### Dimension mới

Thêm foreign key vào fact table nếu dimension có một giá trị duy nhất tại grain hiện tại.

#### Fact mới

Thêm cột nếu fact mới được tạo tại cùng sự kiện và cùng grain.

Nếu fact chỉ có từ một thời điểm trở đi, các dòng cũ nên là null.

Nếu fact mới có grain khác và không thể phân bổ hợp lệ, phải tạo fact table riêng.

## 27. Factless fact table cho promotion coverage

Sales Fact chỉ ghi sản phẩm thực sự bán được. Nó không trả lời trực tiếp:

> Sản phẩm nào được khuyến mãi nhưng không bán được?

Không nên tạo các dòng doanh số 0 cho tất cả tổ hợp sản phẩm-cửa hàng-ngày vì bảng sẽ phình rất lớn.

Giải pháp là Promotion Coverage Factless Fact Table trong Hình 3-14.

Grain:

> Một dòng cho mỗi sản phẩm được khuyến mãi tại một cửa hàng trong một ngày.

Các khóa:

- Date Key.
- Product Key.
- Store Key.
- Promotion Key.

Không cần fact số. Có thể thêm `Promotion Count = 1` chỉ để BI tool đếm thuận tiện.

Để tìm sản phẩm được khuyến mãi nhưng không bán:

1. Lấy tập sản phẩm trong Promotion Coverage.
2. Lấy tập sản phẩm trong Sales Fact.
3. Tính hiệu hai tập.

```text
Promoted Products - Sold Products
= Promoted but Not Sold
```

Factless table mô tả **điều có thể hoặc đáng lẽ xảy ra**; transaction fact mô tả **điều thực sự xảy ra**.

---

# Phần VI: Khóa trong mô hình chiều

## 28. Surrogate key của dimension

Primary key của dimension nên là surrogate key do DW/BI tự tạo, không phải natural key của hệ thống nguồn.

Surrogate key thường là số nguyên tăng tuần tự:

```text
1, 2, 3, ...
```

Bản thân giá trị không có ý nghĩa nghiệp vụ. Nó chỉ dùng để nối dimension với fact.

Tác giả yêu cầu gần như mọi phép join giữa fact và dimension phải dựa trên surrogate key số nguyên vô nghĩa.

### Lợi ích

#### Cách ly warehouse khỏi thay đổi vận hành

Hệ thống nguồn có thể:

- Thay đổi quy tắc mã.
- Xóa mã cũ.
- Tái sử dụng mã sau một thời gian.
- Gộp hệ thống sau mua bán doanh nghiệp.

Warehouse giữ lịch sử nhiều năm nên không thể phụ thuộc vào vòng đời khóa ngắn của hệ thống nguồn.

#### Tích hợp nhiều nguồn

Hai hệ thống có thể dùng natural key khác nhau cho cùng một thực thể. ETL sử dụng bảng đối chiếu để ánh xạ các mã này vào cùng surrogate key.

#### Tăng hiệu năng và giảm dung lượng

Surrogate key là số nguyên nhỏ hơn mã chuỗi hoặc khóa ghép.

Nếu fact table có một tỷ dòng, mỗi byte bổ sung trên mỗi dòng tương đương khoảng một gigabyte lưu trữ. Do đó kích thước khóa rất quan trọng.

Số nguyên 4 byte đáp ứng khoảng hai tỷ giá trị dương, đủ cho phần lớn dimension.

#### Biểu diễn trạng thái đặc biệt

Có thể dành key cho:

- Unknown.
- No Promotion.
- Anonymous Customer.
- Not Applicable.
- Date To Be Determined.

Những trạng thái này không có natural key tương ứng.

#### Theo dõi thay đổi dimension

SCD Type 2 cần nhiều dòng cho cùng natural key. Mỗi phiên bản nhận một surrogate key riêng.

Không nên giả lập surrogate key bằng cách ghép natural key với timestamp, vì làm join phức tạp và ảnh hưởng hiệu năng.

## 29. Natural key và durable supernatural key

Natural key còn được gọi là business key, production key hoặc operational key. Nó vẫn nên được lưu như một thuộc tính trong dimension.

Nếu khóa đến từ nhiều nguồn, có thể thêm mã nguồn:

```text
SAP|43251
CRM|6539152
```

Nếu cùng một thực thể tồn tại trong cả hai nguồn, dimension có thể giữ hai natural key riêng.

Nếu natural key chứa các phần có ý nghĩa như quốc gia hoặc ngành hàng, nên tách các phần này thành thuộc tính mô tả.

### Durable supernatural key

Natural key có thể thay đổi do:

- Sáp nhập tổ chức.
- Sửa bản ghi trùng.
- Tích hợp nhiều nguồn.
- Nhân viên nghỉ rồi được tuyển lại với mã mới.

Khi đó warehouse cần một **durable key** không bao giờ thay đổi trong suốt vòng đời thực thể.

Durable key:

- Do DW/BI quản lý.
- Là thuộc tính trong dimension.
- Không thay thế surrogate primary key.
- Có thể liên kết nhiều phiên bản Type 2 của cùng thực thể.

## 30. Surrogate key cho degenerate dimension

Degenerate dimension thường giữ natural transaction number trực tiếp. Tuy nhiên, có thể cần surrogate key nếu:

- Số giao dịch không duy nhất giữa các cửa hàng.
- Số bị tái sử dụng sau khi đạt giới hạn.
- Mã là chuỗi dài, chẳng hạn 24 byte.
- BI tool cần một khóa thống nhất để drill-across.

Nếu tạo surrogate key và bảng dimension tương ứng, đối tượng đó không còn là degenerate dimension đúng nghĩa.

## 31. Smart key của Date Dimension

Date Dimension là ngoại lệ vì các ngày cố định, có thể dự đoán và không bị xóa hoặc tái sử dụng.

Có hai lựa chọn phổ biến.

### Số nguyên tuần tự theo thời gian

```text
Ngày đầu tiên  -> 1
Ngày tiếp theo -> 2
```

### Số nguyên dạng YYYYMMDD

```text
20130101
20130102
20130103
```

`YYYYMMDD` thuận tiện cho partitioning fact table, nhưng không nhằm mục đích cho phép người dùng bỏ qua Date Dimension.

Người dùng vẫn phải lọc bằng các thuộc tính trong dimension.

Partition theo ngày giúp:

- Nạp dữ liệu mới mà ít ảnh hưởng phần cũ.
- Xóa hoặc lưu trữ dữ liệu cũ.
- Tăng tốc backup.
- Giảm phạm vi quét khi truy vấn.

Một số optimizer hiểu kiểu `DATE` tốt hơn số nguyên. Chẳng hạn, nó biết giữa ngày 1/3 và 1/4 có 31 ngày, trong khi hai số `20130301` và `20130401` có khoảng cách số học khác. Nếu DBMS khai thác được hiểu biết này, cột kiểu DATE có thể phù hợp hơn.

Dù dùng loại khóa nào, vẫn cần giá trị đặc biệt cho ngày chưa biết.

## 32. Surrogate key của fact table

Surrogate key của fact table không bắt buộc và chủ yếu phục vụ ETL, không phải truy vấn nghiệp vụ.

Lợi ích:

- Nhận diện ngay một fact row bằng một cột.
- Xác định điểm dừng của một lần bulk load bị lỗi.
- Hoàn tác một dải key đã nạp.
- Tiếp tục nạp từ đúng điểm.
- Thay update bằng insert rồi delete.
- Làm khóa cha trong một số parent/child schema.

Nếu fact table con sử dụng surrogate key của fact cha, nó vẫn phải chứa các dimension key của cha. Không được buộc truy vấn phân tích phải đi qua fact cha và không nên join trực tiếp fact với fact.

---

# Phần VII: Chống lại xu hướng chuẩn hóa

## 33. Snowflake schema

Nhà thiết kế quen với hệ thống vận hành thường không thích việc lặp tên department trong hàng trăm nghìn dòng Product Dimension. Họ muốn tách thành:

```text
Product
  -> Brand
      -> Category
          -> Department
```

Đây là snowflaking. Hình 3-15 cho thấy Product Dimension bị tách thành nhiều bảng Brand, Category, Department, Package Type, Storage Type và Shelf Life Type.

Snowflake là một thiết kế hợp lệ về mặt quan hệ, nhưng Kimball khuyên tránh trong presentation layer.

### Lý do

#### Khó hiểu

Người dùng phải hiểu và nối nhiều bảng chỉ để lấy mô tả sản phẩm.

#### Hiệu năng kém hơn

Nhiều bảng và phép join khiến optimizer dễ chọn kế hoạch không tốt.

#### Tiết kiệm dung lượng không đáng kể

Ví dụ của sách:

- 300.000 sản phẩm.
- Thay mô tả department 20 byte bằng mã 2 byte.
- Tiết kiệm khoảng 5,4 MB.
- Trong khi fact table có thể là 10 GB hoặc lớn hơn rất nhiều.

Khoản tiết kiệm ở dimension gần như không đáng kể so với chi phí sử dụng và hiệu năng.

#### Làm browsing phức tạp

Muốn xem brand trong một category phải đi qua nhiều bảng. Nếu thêm package type, truy vấn còn phức tạp hơn, dù chưa chạm đến fact table.

#### Hạn chế bitmap index

Các cột có cardinality thấp như category hoặc department thích hợp với bitmap index khi nằm trong dimension phẳng. Snowflake làm mất hoặc giảm lợi ích này.

### Khi nào snowflake có thể chấp nhận?

- DBMS xử lý schema chuẩn hóa rất tốt, nhưng vẫn nên cung cấp logical view phẳng cho người dùng.
- Một BI tool cụ thể yêu cầu snowflake.
- Snowflake chỉ dùng để nạp OLAP cube và không bao giờ lộ ra cho người dùng.
- Có lý do kỹ thuật đặc biệt được kiểm chứng.

Cần cân nhắc khả năng đổi BI tool trong tương lai; không nên để presentation model bị khóa vào yêu cầu riêng của một công cụ.

## 34. Outrigger

Outrigger là dimension phụ nối từ một dimension chính thay vì nối trực tiếp với fact.

Hình 3-16 minh họa Product Dimension tham chiếu Product Introduction Date Dimension.

Outrigger ngày chỉ thực sự cần nếu người dùng muốn lọc ngày giới thiệu sản phẩm theo kỳ tài chính, ngày làm việc, dịp lễ hoặc các thuộc tính lịch không tiêu chuẩn.

Các cột phải được đặt tên theo vai trò, chẳng hạn:

- Product Introduction Calendar Month.
- Product Introduction Fiscal Year.
- Product Introduction Holiday Indicator.

Nếu không cần các thuộc tính lịch phức tạp, chỉ cần cột DATE trong Product Dimension.

Outrigger có thể tiết kiệm dung lượng và bảo đảm dùng chung định nghĩa, nhưng cũng tạo thêm join, làm mô hình khó đọc và cản trở browsing giữa các thuộc tính dimension.

Vì vậy, outrigger phải là ngoại lệ, không phải quy tắc.

## 35. Centipede fact table

Centipede fact table là fact table có quá nhiều chân - quá nhiều foreign key tới các dimension nhỏ.

Ví dụ sai:

```text
DateKey
WeekKey
MonthKey
QuarterKey
YearKey
FiscalMonthKey
FiscalYearKey

ProductKey
BrandKey
CategoryKey
DepartmentKey
PackageTypeKey

StoreKey
CountyKey
StateKey
DistrictKey
RegionKey
```

Hình 3-17 minh họa một POS Sales Fact bị bao quanh bởi hàng chục dimension.

Thiết kế đúng chỉ cần:

- Một DateKey; tuần, tháng, quý và năm là thuộc tính của Date Dimension.
- Một ProductKey; brand, category và department là thuộc tính của Product Dimension.
- Một StoreKey; county, state, district và region là thuộc tính của Store Dimension.

### Tác hại

- Fact table rộng hơn và tốn nhiều dung lượng.
- Khóa ghép trở nên khổng lồ.
- Khó xây index hiệu quả.
- Nhiều join.
- Mô hình khó sử dụng.
- Browsing trong cùng hierarchy trở nên bất tiện.

Phần lớn quy trình có thể được mô hình hóa với dưới 20 dimension. Nếu fact table có từ 25 dimension trở lên, cần kiểm tra xem:

- Có hierarchy nào bị tách sai không?
- Có nhiều flag nhỏ nên gom vào junk dimension không?
- Có các dimension tương quan cao nên kết hợp không?

Có thể gộp khi dimension kết quả nhỏ hơn đáng kể so với tích Descartes của các dimension riêng.

Columnar database có thể giảm chi phí lưu trữ và truy vấn của fact table rộng vì chỉ đọc các cột cần thiết. Tuy nhiên, nó không giải quyết hoàn toàn vấn đề dễ hiểu và khả năng browsing. Công nghệ chịu đựng được thiết kế xấu không có nghĩa thiết kế đó trở thành tốt.

---

## 36. Danh mục hình minh họa

- **Hình 3-1:** Hai đầu vào của quy trình thiết kế bốn bước: yêu cầu kinh doanh và thực tế dữ liệu.
- **Hình 3-2:** Hóa đơn POS mẫu.
- **Hình 3-3:** Các fact đo lường trong retail sales schema.
- **Hình 3-4:** Cấu trúc Date Dimension.
- **Hình 3-5:** Các dòng mẫu của Date Dimension.
- **Hình 3-6:** So sánh chỉ báo bí ẩn với chỉ báo văn bản dễ hiểu.
- **Hình 3-7:** Các dòng mẫu của Product Dimension.
- **Hình 3-8:** Cấu trúc Product Dimension.
- **Hình 3-9:** Drill-down bằng dimension attributes.
- **Hình 3-10:** Cấu trúc Store Dimension.
- **Hình 3-11:** Cấu trúc Promotion Dimension.
- **Hình 3-12:** Truy vấn retail sales schema.
- **Hình 3-13:** Kết quả truy vấn dạng dòng và bảng chéo.
- **Hình 3-14:** Promotion Coverage Factless Fact Table.
- **Hình 3-15:** Product Dimension bị snowflake.
- **Hình 3-16:** Một outrigger được chấp nhận.
- **Hình 3-17:** Centipede fact table có quá nhiều dimension.

## 37. Tổng kết Chương 3

Những nguyên tắc quan trọng nhất cần nhớ:

1. Luôn thiết kế theo bốn bước: process, grain, dimensions, facts.
2. Grain phải được tuyên bố bằng ngôn ngữ nghiệp vụ trước khi chọn dimension và fact.
3. Nên lưu dữ liệu ở grain nguyên tử để duy trì khả năng phân tích và mở rộng.
4. Fact phải đúng grain; fact khác grain phải được phân bổ hợp lệ hoặc đưa sang bảng riêng.
5. Tử số và mẫu số của tỷ lệ nên được lưu riêng; tính tỷ lệ của các tổng, không cộng hay lấy trung bình tỷ lệ.
6. Dimension phải chứa nhiều thuộc tính mô tả dễ hiểu.
7. Date Dimension là bắt buộc đối với gần như mọi schema.
8. Foreign key trong fact table không được null; sử dụng các dimension row đặc biệt.
9. Số hóa đơn hoặc số giao dịch thường là degenerate dimension.
10. Factless fact table giúp ghi nhận sự kiện không có phép đo và phân tích điều không xảy ra.
11. Dimension dùng surrogate key để bảo vệ warehouse khỏi thay đổi của hệ thống nguồn và hỗ trợ lịch sử.
12. Giữ hierarchy cố định trong dimension phẳng; tránh snowflake, outrigger tràn lan và centipede fact table.

Mô hình trung tâm của chương có thể ghi nhớ như sau:

```text
Quy trình:
Bán hàng tại quầy POS

Grain:
Một sản phẩm trên một giao dịch

Dimensions:
Date, Product, Store, Promotion,
Cashier, Payment Method, Transaction Number

Facts:
Quantity, Discount, Sales, Cost, Gross Profit
```

Đây là ví dụ đầu tiên cho thấy phương pháp Kimball biến một sự kiện kinh doanh thực tế thành một star schema vừa dễ hiểu, vừa có khả năng mở rộng và trả lời những câu hỏi chưa được biết trước.

---

## Thuật ngữ cần nhớ

| Thuật ngữ | Nghĩa ngắn gọn |
|---|---|
| POS | Point of Sale - hệ thống ghi nhận giao dịch tại quầy bán hàng |
| SKU | Stock Keeping Unit - đơn vị sản phẩm được quản lý riêng |
| Business process | Hoạt động vận hành tạo ra sự kiện hoặc trạng thái có thể đo lường |
| Grain | Ý nghĩa và mức chi tiết chính xác của một dòng fact |
| Atomic grain | Mức chi tiết thấp nhất mà quy trình nguồn ghi nhận được |
| Transaction fact table | Fact table có một dòng cho mỗi giao dịch hoặc dòng giao dịch |
| Additive fact | Fact có thể cộng theo mọi dimension |
| Non-additive fact | Fact không thể cộng trực tiếp, như tỷ lệ hoặc đơn giá |
| Derived fact | Fact được tính từ các phép đo khác |
| Date Dimension | Dimension một dòng mỗi ngày, chứa lịch dương, tài chính và các thuộc tính đặc biệt |
| Time-of-Day Dimension | Dimension mô tả phút, giờ, ca hoặc phần trong ngày |
| Causal dimension | Dimension mô tả các yếu tố được cho là gây ra thay đổi trong kết quả |
| Lift | Phần doanh số tăng thêm so với baseline |
| Time shifting | Doanh số chỉ bị chuyển từ thời điểm khác sang kỳ khuyến mãi |
| Cannibalization | Một sản phẩm tăng doanh số bằng cách lấy doanh số của sản phẩm khác |
| Degenerate dimension | Khóa nghiệp vụ nằm trong fact table mà không có dimension table riêng |
| Factless fact table | Fact table ghi nhận quan hệ hoặc sự kiện mà không cần phép đo số |
| Natural key | Khóa nghiệp vụ do hệ thống nguồn tạo |
| Surrogate key | Khóa thay thế do DW/BI tự cấp, không mang ý nghĩa nghiệp vụ |
| Durable key | Khóa bền vững nhận diện cùng một thực thể qua mọi thay đổi |
| Smart date key | Khóa Date Dimension có cấu trúc như `YYYYMMDD` hoặc kiểu DATE |
| Role-playing dimension | Một dimension vật lý được dùng với nhiều vai trò logic |
| Outrigger | Dimension phụ được tham chiếu từ một dimension khác |
| Snowflake | Cách chuẩn hóa một dimension thành nhiều bảng liên quan |
| Centipede fact table | Fact table có quá nhiều foreign key do tách hierarchy hoặc thuộc tính quá mức |

