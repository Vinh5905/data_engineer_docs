# Chương 4: Công cụ và các cách tiếp cận kỹ thuật

*Nguyên tác: Chapter 4: Tools and Technical Approaches - trang 49-70*

Chương trước tập trung vào việc xác lập mục tiêu, tầm nhìn và tập hợp một nhóm chuyên trách để hỗ trợ triển khai Continuous Delivery (CD) và DevOps. Trong một vài chương tiếp theo, chúng ta sẽ đi qua các bước thực thi kế hoạch. Trước hết là khía cạnh kỹ thuật: những công cụ và quy trình mà bạn cùng nhóm nên triển khai hoặc cải tiến.

Có khá nhiều nội dung cần tiếp nhận. Một số thứ bạn sẽ cần ngay, một số có thể đã tồn tại, còn một số nên được cân nhắc cho giai đoạn sau. Dù vậy, tác giả khuyên bạn vẫn nên đọc hết, vì ít nhất có thể tìm thấy một vài thông tin hoặc ý tưởng hữu ích để điều chỉnh cho phù hợp nhu cầu.

Phần lớn chương tập trung vào kỹ thuật phần mềm, tức phía Dev trong quan hệ DevOps. Tuy nhiên, nhiều luận điểm cũng phù hợp với vận hành hệ thống.

Các công cụ và quy trình được đề cập không loại trừ lẫn nhau; đây không phải lựa chọn "tất cả hoặc không gì cả". Bạn chỉ cần chọn thứ phù hợp. Một số nội dung có thứ tự và quan hệ phụ thuộc hợp lý, nhưng chính bạn phải quyết định điều gì khả thi trong hoàn cảnh của mình.

Chúng ta bắt đầu với các thực hành kỹ thuật tốt.

## Thực hành kỹ thuật tốt nhất

*Nguyên tác: Engineering best practice*

Nếu không phải kỹ sư phần mềm hoặc không có nền tảng kỹ thuật phần mềm, có thể bạn hầu như không quan tâm phần mềm được phát triển như thế nào. Bạn có thể hỏi: "Tại sao tôi phải biết lập trình viên làm công việc của họ ra sao? Chẳng phải họ hiểu việc đó hơn tôi sao? Có lẽ tôi còn không hiểu nổi 10% nội dung."

Ở một mức độ nào đó, điều này đúng. Lập trình viên biết - hoặc phải biết - chuyên môn của họ, và có thể không hoan nghênh việc bạn xen vào. Tuy nhiên, hiểu hoặc ít nhất trân trọng cách phần mềm được tạo ra sẽ giúp bạn nhận biết nơi vấn đề có thể ẩn náu.

Tác giả so sánh với động cơ đốt trong: ông hiểu các bộ phận và nguyên lý vận hành nhưng hoàn toàn không phải thợ máy. Khi đưa xe đi bảo dưỡng định kỳ, nếu thợ máy thay toàn bộ hệ thống ống xả chỉ vì phát hiện vấn đề ở kim phun nhiên liệu, ông sẽ chất vấn rất quyết liệt.

Phát triển phần mềm và quy trình bao quanh nó cũng vậy. Ngay cả khi hoàn toàn không có chuyên môn kỹ thuật, bạn vẫn nên hiểu khái quát cách mọi việc được thực hiện. Khi cần hỏi vì sao một việc phải làm theo một cách nhất định, bạn có thể nhận ra những người cố dùng một màn thuật ngữ kỹ thuật để làm người khác sợ và ngừng chất vấn.

CD và DevOps dựa trên tiền đề rằng phần mềm chất lượng có thể được phát triển, build, kiểm thử và chuyển giao rất nhanh, nhiều lần liên tiếp - lý tưởng là trong vài giờ hoặc tối đa vài ngày. Trong phần lớn dự án Agile, ba việc đầu thường đã được coi là đương nhiên; chính bước chuyển giao mới tốn thời gian, như bạn có thể đã phát hiện trong cuộc điều tra. Với dự án phát triển theo kiểu Waterfall, có lẽ ngay cả chu trình `phát triển -> build -> kiểm thử` cũng chứa lãng phí.

Hãy quay lại một số nguyên tắc kỹ thuật phần mềm cơ bản:

- Luôn sử dụng hệ thống quản lý mã nguồn.
- Commit những thay đổi mã nhỏ và thực hiện thường xuyên.
- Không làm mã phức tạp quá mức và luôn duy trì tài liệu.
- Nếu có kiểm thử tự động, hãy chạy chúng thật thường xuyên.
- Nếu có giải pháp Continuous Integration (CI) điều khiển quy trình build và bộ kiểm thử, hãy chạy nó thật thường xuyên.
- Thực hiện code review đều đặn.
- Đừng sợ kiểm thử thất bại hoặc người khác phát hiện lỗi trong mã của bạn.

Đây là những quy tắc khá đơn giản. Phần lớn kỹ sư phần mềm làm việc trong các dự án Agile hiện đại sẽ xem chúng là lẽ thường và thực hành thông thường. Tác giả nói "phần lớn" vì vẫn có những lập trình viên theo lối cũ tin rằng mình được miễn trừ do đã làm cùng một cách suốt 20 năm.

Đáng lo hơn, nhiều kỹ sư không có điều kiện làm theo những quy tắc đơn giản này. Họ không có cơ hội, chưa biết đến chúng, hoặc làm việc trong một môi trường không cho phép hay không tin tưởng các thực hành đó.

Điểm mấu chốt là: nếu không phát hiện vấn đề phần mềm từ sớm, chúng sẽ làm bạn chậm lại về sau và ảnh hưởng toàn bộ dự án. Nói cách khác, nếu phần mềm khi được bàn giao gần như không có bug, việc phát hành phải trở nên rất dễ dàng.

> **Chú thích:** "Best practice" không phải một bộ luật bất biến. Đây là điểm khởi đầu đã được chứng minh trong nhiều bối cảnh; nhóm vẫn phải đo lường và điều chỉnh theo hệ thống, rủi ro và quy định cụ thể.

### Quản lý mã nguồn

*Nguyên tác: Source control*

Có rất nhiều loại, phiên bản và giải pháp quản lý mã nguồn - đôi khi gọi là SCM hoặc hệ thống quản lý phiên bản - cả thương mại lẫn mã nguồn mở. Vì vậy, không có lý do gì để không sử dụng.

Khi mã nằm trong hệ thống quản lý mã nguồn:

- Nó được quản lý phiên bản, nghĩa là có lịch sử của mọi thay đổi từ trước đến nay.
- Bất kỳ ai có quyền truy cập hệ thống đều có thể lấy được mã.
- Mã được bảo vệ.
- Thông thường, mã được sao lưu để tránh mất mát.

Có rất nhiều sách và tài liệu chuyên sâu về chủ đề này nên tác giả không trình bày dài. Kết luận rất thẳng thắn: nếu chưa có giải pháp quản lý mã nguồn, hãy triển khai ngay.

Quản lý phiên bản không nên chỉ giới hạn ở mã nguồn phần mềm. Bạn có thể và nên dùng nó cho bất kỳ thành phần nào của hệ thống có thể thay đổi, bao gồm:

- Cấu hình hệ thống.
- Script khởi động.
- Cấu hình máy chủ.
- Cấu hình mạng.
- Các thành phần khác có thể biểu diễn và lưu trữ dưới dạng văn bản.

Nói ngắn gọn, nếu một phần của toàn bộ nền tảng có thể biểu diễn thành tệp văn bản, nó nên được lưu trong hệ thống quản lý phiên bản.

Quản lý mã nguồn là công cụ rất có giá trị khi áp dụng CD và DevOps. Việc duy trì các thay đổi nhỏ, thường xuyên cũng quan trọng tương tự.

> **Chú thích:** Hãy đưa **cấu trúc và mẫu cấu hình** vào version control, nhưng không commit mật khẩu, token hoặc khóa bí mật ở dạng rõ. Các bí mật nên nằm trong secret manager; kho mã chỉ giữ tham chiếu hoặc mẫu cấu hình.
>
> **Ví dụ:** Thay vì đăng nhập từng máy chủ và sửa `nginx.conf` bằng tay, nhóm lưu cấu hình trong Git, yêu cầu review qua pull request và dùng tự động hóa để áp dụng. Mọi người biết ai thay đổi gì, khi nào và có thể quay lại phiên bản trước.

### Các thay đổi nhỏ, thường xuyên và đơn giản

*Nguyên tác: Small, frequent, and simple changes*

Giữ thay đổi nhỏ đồng nghĩa tác động của nó cũng nên nhỏ hơn, rủi ro giảm và cơ hội thay đổi tăng. Nghe có vẻ quá đơn giản, nhưng điều đó thực sự đúng.

Hình trong sách đối chiếu hai cách phát hành trên cùng một dòng thời gian:

- **Các đợt phát hành lớn:** Version 1.0, Version 2.0 và Version 3.0 kéo dài, chồng lấn nhau.
- **Các phần tăng trưởng nhỏ:** Ver 1.0, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 1.8, 2.0, 2.1, 2.2, 2.3, 2.4, 2.5, 3.0, 3.1 và 3.3 được phát hành liên tục.

Bạn có thể chưa có điều kiện chuyển giao mã thật thường xuyên, nhưng đó không phải lý do để bỏ qua thực hành tốt. Khi CD và DevOps vận hành, bạn sẽ phải làm việc theo cách này; vì vậy, hãy bắt đầu làm quen ngay từ bây giờ.

Phát triển bằng những thay đổi nhỏ, thường xuyên còn giúp giảm độ phức tạp và duy trì chất lượng. Việc merge mã dễ quản lý hơn vì lượng mã thay đổi cần kết hợp rất nhỏ. Điều này cũng hỗ trợ bạn tiến đến trạng thái lý tưởng là luôn phát triển từ trunk.

Thực hành này không chỉ dành cho kỹ thuật phần mềm mà cũng phù hợp với vận hành hệ thống. Một điều chỉnh nhỏ, độc lập đối với cấu hình máy chủ - chẳng hạn thay đổi lượng bộ nhớ cấp cho máy chủ ảo - an toàn, dễ kiểm soát và dễ giám sát hơn nhiều so với thay đổi hàng loạt cùng lúc. Với thay đổi nhỏ, bạn có cơ hội tốt hơn để thấy nó tác động tích cực hay tiêu cực đến toàn nền tảng.

Làm việc với các thay đổi nhỏ, tăng dần là một thực hành rất có lợi. Đồng thời, bạn cũng phải hiểu rõ thay đổi phá vỡ tương thích có thể ảnh hưởng nền tảng ra sao.

> **Chú thích:** *Trunk-based development* là cách mọi người tích hợp thường xuyên vào một nhánh chính, tránh các nhánh tồn tại quá lâu. Để làm an toàn, nhóm thường dùng kiểm thử tự động, feature flag và commit nhỏ.
>
> **Ví dụ:** Một thay đổi cơ sở dữ liệu gồm thêm cột mới, cập nhật ứng dụng và xóa cột cũ không nên triển khai cùng lúc. Có thể chia thành ba thay đổi tương thích ngược: thêm cột, chuyển dần dữ liệu và người dùng, rồi chỉ xóa cột cũ khi không còn thành phần phụ thuộc.

### Không bao giờ làm hỏng bên sử dụng

*Nguyên tác: Never break your consumer*

Nền tảng phần mềm của bạn rất có thể phức tạp và có khá nhiều quan hệ phụ thuộc. Điều này bình thường và không có gì đáng xấu hổ. Các quan hệ phụ thuộc có thể được phân loại thành quan hệ giữa **consumer - bên sử dụng** và **provider - bên cung cấp**.

Provider có thể là thư viện dùng chung, module mã lõi hoặc cơ sở dữ liệu. Consumer gọi, thực thi hoặc gửi yêu cầu đến provider theo một cách cụ thể, dựa trên đặc tả giao diện được định nghĩa trước - đôi khi gọi là service contract.

Một ví dụ đơn giản là trang web dùng thư viện PHP để lấy nội dung nhằm hiển thị địa chỉ của người dùng. Trong trường hợp này, trang web là consumer còn thư viện PHP là provider. Nếu thư viện vốn trả về bốn phần dữ liệu nhưng được sửa thành năm phần, consumer có thể không biết xử lý và phát sinh lỗi, thậm chí sập.

Khi nền tảng ngày càng phức tạp, rất khó phát hiện provider nào đã thay đổi và khiến một trong nhiều consumer thất bại. Khó khăn tăng thêm vì một consumer có thể đồng thời là provider của consumer khác ở tầng cao hơn. Ví dụ:

`Cơ sở dữ liệu -> thư viện PHP dùng chung -> trang web -> JavaScript client`

Phân tích tác động có thể giúp lập bản đồ quan hệ này, nhưng nếu không thể mô tả toàn bộ nền tảng bằng một định dạng dễ hiểu và luôn được cập nhật, công việc vẫn rất khó.

Quy tắc "không bao giờ làm hỏng consumer" cũng áp dụng cho vận hành hệ thống. Nền tảng phần mềm có thể được xem là consumer của hệ điều hành máy chủ, tức provider. Khi thay đổi hoặc nâng cấp hệ điều hành, bạn phải bảo đảm không có thay đổi phá vỡ tương thích làm consumer thất bại.

Đôi khi không thể tránh thay đổi phá vỡ tương thích, chẳng hạn đặc tả giao diện giữa các thành phần phải thay đổi để hỗ trợ chức năng mới. Tuy nhiên, đây phải là ngoại lệ thay vì thông lệ, và bạn cần một chiến lược xử lý được lập kế hoạch trước. Một chiến lược là chạy song song nhiều phiên bản của cùng một tài sản phần mềm.

Quan hệ consumer/provider cũng có thể bị phá vỡ vì người hoặc nhóm sửa provider không biết consumer tồn tại. Điều này đặc biệt dễ xảy ra với provider thuộc khu vực vận hành hệ thống. Thực hành làm việc đồng cấp cởi mở và trung thực sẽ giúp khắc phục hoặc ít nhất giảm rủi ro.

> **Chú thích:** Tên hiện đại thường gặp của nguyên tắc này là **backward compatibility**. Các kỹ thuật liên quan gồm versioning API, consumer-driven contract test và mô hình expand-and-contract cho thay đổi dữ liệu.
>
> **Ví dụ:** API đang trả trường `fullName`. Thay vì đổi ngay thành `displayName` và làm ứng dụng di động cũ hỏng, provider có thể tạm trả cả hai trường, theo dõi phiên bản client, sau đó loại bỏ `fullName` khi không còn consumer sử dụng.

### Thực hành làm việc đồng cấp cởi mở và trung thực

*Nguyên tác: Open and honest peer working practices*

Có nhiều phương pháp phát triển Agile, nhưng tất cả đều xoay quanh một hình thức cộng tác, từ pair programming đến code review trong chính vòng đời phát triển.

Không thể nhấn mạnh quá mức tầm quan trọng của việc chia sẻ mã với người khác. Ngay cả kỹ sư phần mềm hoặc quản trị viên hệ thống giỏi nhất vẫn là con người và có thể mắc lỗi. Nếu xem mã là tài sản quá riêng tư và không muốn chia sẻ, bạn sẽ tạo ra bug và mất nhiều thời gian hơn để xử lý những sai sót nhỏ. Chúng có thể khiến bạn đau đầu hàng giờ hoặc, tệ hơn, gây ảnh hưởng xấu đến khách hàng.

Nếu tự tin mã có chất lượng cao nhất và chịu được sự xem xét, đừng giấu nó. Nếu chưa tự tin, chia sẻ với đồng nghiệp càng hữu ích.

Nhóm đồng cấp không nên chỉ gồm kỹ sư phần mềm; Operations cũng cần tham gia. Có thể họ không đọc được mã - dù nhiều quản trị viên hệ thống thực ra đọc mã rất tốt - nhưng họ hiểu nền tảng production vận hành ra sao và có thể đưa ra ý kiến giá trị hoặc câu hỏi đúng trọng tâm.

Phần lớn phần mềm chất lượng cao trên thế giới được xây dựng theo cách cộng tác, vì vậy không có lý do gì để bạn không làm tương tự.

Quy trình peer review cởi mở, trung thực và minh bạch quan trọng với Operations chẳng kém Development. Mọi thay đổi đối với nền tảng đều có rủi ro; thêm một cặp mắt xem xét sẽ giúp giảm rủi ro. Cũng như mã nguồn, không có lý do gì phải giấu thay đổi cấu hình hệ thống.

Một lợi ích thường ít được nhìn thấy là: nếu mã hoặc thay đổi cấu hình không vượt qua peer review, production không phải chịu tác động. Đó là tinh thần thất bại sớm thay vì chờ đưa lên live rồi mới phát hiện thất bại.

> **Ví dụ:** Một kỹ sư định mở cổng mạng cho toàn Internet để khắc phục lỗi kết nối. Trong review, đồng nghiệp phát hiện chỉ cần cho phép dải IP của dịch vụ phụ thuộc. Vấn đề được giải quyết trước khi thay đổi cấu hình production.

### Thất bại nhanh và thường xuyên

*Nguyên tác: Fail fast and often*

"Thất bại nhanh và thường xuyên" có vẻ trái trực giác, nhưng là một tinh thần làm việc tốt. Nếu bug được tạo ra mà chỉ bị phát hiện sau khi lên live, chi phí sửa sẽ cao - có thể cần một đợt phát hành hoàn toàn mới - chưa kể ảnh hưởng đến danh tiếng và doanh thu. Phát hiện bug sớm là điều bắt buộc.

Test-Driven Development (TDD) dựa trên nguyên tắc tìm lỗi của phần mềm thật sớm. Trước khi phát triển mã, các kiểm thử được viết để bao phủ phần lớn use case phần mềm cần đáp ứng. Trong lúc viết mã, các kiểm thử này được chạy hết lần này đến lần khác. Nếu mã thất bại ở thời điểm này, đó là điều tốt: người duy nhất chịu ảnh hưởng là kỹ sư phần mềm, không phải khách hàng.

Điều này có thể nghe kỳ lạ, đặc biệt với cấp quản lý, nhưng khi bug được phát hiện sớm, không nên làm ầm lên hoặc trách phạt người khác. Có thể có một chút trêu đùa trong văn phòng, nhưng chỉ thế thôi. Hãy tìm vấn đề, tìm nguyên nhân, sửa, rút kinh nghiệm rồi tiếp tục.

Một trở ngại đối với TDD là quy mô và độ phức tạp của nền tảng. Việc bổ sung hồi tố cả bộ kiểm thử cho một hệ thống không được thiết kế theo nguyên tắc này có thể rất đáng ngại. Khi đó, nên bắt đầu nhỏ rồi mở rộng dần.

> **Chú thích:** *Fail fast* không có nghĩa cố tình để production thất bại hoặc chấp nhận chất lượng thấp. Nó có nghĩa thiết kế vòng phản hồi sao cho giả định sai và lỗi kỹ thuật bị phát hiện ở nơi sớm nhất, rẻ nhất và an toàn nhất.
>
> **Ví dụ:** Một validation chạy trong IDE và CI, trả lỗi sau 30 giây, tốt hơn nhiều so với phát hiện cùng lỗi sau ba tuần ở UAT. Với hệ thống cũ, nhóm có thể bắt đầu bằng test cho module đang được sửa thay vì cố bao phủ toàn bộ hệ thống ngay lập tức.

### Build và kiểm thử tự động

*Nguyên tác: Automated build and testing*

Cách tiếp cận fail fast và TDD cung cấp phản hồi nhanh cho kỹ sư về việc thay đổi có hoạt động hay không. Một điều hữu ích khác là biết mã có thể build hoặc biên dịch một cách nhất quán hay không.

Có thể dùng quy trình thủ công, nhưng cách đó cồng kềnh, thiếu nhất quán, dễ lỗi, chậm và không phải lúc nào cũng lặp lại hoàn toàn. Tự động hóa giúp tăng tốc, duy trì tính nhất quán và trên hết là tạo sự tự tin. Nếu cùng một chuỗi bước được chạy nhiều lần và cho cùng kết quả, có cơ sở vững chắc để tin tưởng nó. Khi chỉ thay đổi một thứ trong nền tảng và quy trình vốn đang chạy bỗng thất bại, rất có khả năng thay đổi đó đã làm hỏng điều gì.

Có nhiều công cụ build hoặc biên dịch mã. Về cơ bản, chúng làm những việc giống nhau: xác nhận mã được viết đúng, kiểm tra các tham chiếu bên ngoài có sẵn và, nếu mọi thứ ổn, tạo binary có thể chạy.

Tương tự, có nhiều công cụ và công nghệ kiểm thử tự động. Hãy dành thời gian tìm hiểu lựa chọn hiện có hoặc hỏi nhóm kỹ thuật họ đang dùng và muốn dùng gì. Với một chút nghiên cứu và phát triển, bạn có thể thiết lập giải pháp khá nhanh.

Kiểm thử tự động đôi khi khiến người ta nản vì quy mô có vẻ quá lớn: cần bao phủ bao nhiêu phần của nền tảng, mô phỏng người dùng thực tế thế nào và bắt đầu ở đâu? Không có câu trả lời đơn giản ngoài KISS. Hãy lập bản đồ các use case chính và tạo kiểm thử tự động bao phủ chúng; sau đó tinh chỉnh hoặc bổ sung dần.

Đừng làm dữ liệu kiểm thử phức tạp quá mức. Dữ liệu test có thể trở thành vấn đề gai góc và gây nhiều rắc rối hơn giá trị. Một quy tắc hữu ích là để chính test script tạo, và quan trọng hơn là dọn bỏ, dữ liệu nó cần trong quá trình chạy. Nếu không, bạn sẽ tích lũy dữ liệu cũ nhanh chóng trở nên lỗi thời.

> **Chú thích:** Một test tự tạo và dọn dữ liệu sẽ độc lập, dễ lặp lại hơn. Tuy nhiên, việc dọn dữ liệu phải bảo đảm không xóa nhầm dữ liệu của test khác; có thể dùng namespace, transaction hoặc tài nguyên tạm có mã định danh riêng.
>
> **Ví dụ:** Test đăng ký người dùng tạo địa chỉ email duy nhất cho mỗi lần chạy, xác nhận tài khoản, rồi xóa tài khoản ở bước teardown. Nó không dựa vào tài khoản cố định `test@example.com`, vốn có thể đã tồn tại hoặc bị test khác sửa.

Vậy là chúng ta đã có tự động hóa để build và kiểm thử các thành phần phần mềm. Làm thế nào thiết lập để chạy toàn bộ khi cần? Đây là lúc giải pháp Continuous Integration phát huy vai trò.

### Tích hợp liên tục

*Nguyên tác: Continuous integration*

Continuous Integration, thường gọi là CI, là phương pháp đã được kiểm chứng để bảo đảm tài sản phần mềm đang phát triển build đúng và tương tác tốt với phần còn lại của nền tảng. Từ khóa là **continuous - liên tục**, nghĩa là càng thường xuyên càng tốt, lý tưởng là trên mỗi commit vào hệ thống quản lý mã nguồn hoặc thậm chí thường xuyên hơn.

Có rất nhiều giải pháp CI trưởng thành, cả miễn phí lẫn thương mại. Cũng như quản lý mã nguồn, không có lý do gì để không triển khai và sử dụng CI.

Về cơ bản, giải pháp CI cho phép chạy script tự động trong các CI job khi một sự kiện xảy ra, chẳng hạn:

- Có commit vào hệ thống quản lý mã nguồn.
- Cứ mỗi mười phút.
- Chạy qua đêm.

Mỗi job chứa danh sách hoạt động cần chạy liên tiếp. Ví dụ:

1. Lấy phiên bản mã mới nhất từ kho nguồn.
2. Biên dịch thành tệp thực thi.
3. Triển khai binary vào môi trường test.
4. Lấy bộ kiểm thử tự động từ kho nguồn.
5. Chạy các kiểm thử.

Nếu mọi thứ ổn, CI job hoàn tất và báo thành công. Nếu thất bại, nó báo lỗi và cung cấp phản hồi chi tiết về nguyên nhân. Mỗi lần job chạy, một audit trail đầy đủ được ghi lại để bạn tra cứu và so sánh kết quả.

Công cụ CI có thể rất mạnh và cho phép thêm logic đơn giản để điều khiển quy trình. Nếu mọi kiểm thử tự động đều qua, tệp thực thi có thể được đưa vào binary repository. Nếu có bước thất bại, kết quả có thể được gửi email cho nhóm kỹ thuật. Bạn còn có thể xây dashboard hoặc radiator để hiển thị tức thời kết quả theo cách dễ hiểu.

CI là thành phần bắt buộc của CD: nếu build và kiểm thử thay đổi phần mềm thường xuyên, bạn có thể chuyển giao thường xuyên.

Lợi ích của CI đối với thay đổi vận hành hệ thống ít hiển nhiên hơn nhưng vẫn rất lớn, vì nó cho phép thử thay đổi mà không tác động production. Giả sử CI chạy nhiều kiểm thử tự động qua đêm trên một môi trường test cô lập và các test đã xanh trong vài ngày. Sau đó, bạn sửa cấu hình máy chủ, chạy lại bộ CI và nó thất bại. Vì cấu hình máy chủ là thay đổi duy nhất, có thể suy luận nó đã gây tác động xấu.

Triển khai CI không phải thử thách nhỏ, đặc biệt khi bạn chưa có tự động hóa. Tuy nhiên, CI là công cụ rất mạnh, làm giảm đáng kể chi phí và rủi ro của phương pháp build và kiểm thử thủ công.

> **Chú thích:** *Build radiator* là màn hình dễ nhìn đặt ở nơi chung, hiển thị trạng thái build/test. Tên gọi gợi ý rằng thông tin phải "tỏa" ra để ai cũng nhận biết ngay, thay vì bị giấu trong một công cụ riêng.

Bây giờ, chúng ta chuyển sang một số thử thách kỹ thuật khác, bắt đầu với kiến trúc hệ thống.

## Các cách tiếp cận kiến trúc

*Nguyên tác: Architectural approaches*

Rất nhiều nền tảng phần mềm đã tiến hóa trong nhiều năm và trở nên phức tạp, cồng kềnh khi bảo trì hoặc phát triển. Số cách xây dựng và thiết kế nền tảng gần như nhiều bằng số ngôn ngữ lập trình.

Nếu phải duy trì hoặc phát triển thứ nhiều người gọi là sản phẩm legacy - có thể là một khối mã thực thi khổng lồ phải phát hành trong một gói duy nhất - bạn sẽ bị hạn chế nghiêm trọng khi áp dụng CD và DevOps.

Dù vậy, lỗi có thể không nằm ở thiết kế hệ thống hoặc kiến trúc mà ở quy trình phát hành. Ngay cả nền tảng tích hợp và kết nối chặt chẽ nhất vẫn được tạo thành từ nhiều thành phần nhỏ giao tiếp với nhau.

Nếu lùi lại và nhìn toàn cảnh, nhiều khả năng bạn có thể chia nền tảng, hoặc ít nhất phần lớn nền tảng, thành các phần nhỏ dễ quản lý như thư viện dùng chung và các tầng công nghệ. Chúng có thể được build, triển khai độc lập và quan trọng hơn là phát hành thường xuyên.

### Kiến trúc dựa trên thành phần

*Nguyên tác: Component based architecture*

Nếu may mắn có cơ hội tái thiết kế nền tảng như ACME Systems, bạn nên dành thời gian cân nhắc cách tiếp cận phù hợp nhất. Lý tưởng là chọn công nghệ hoặc kiến trúc cho phép chia nền tảng thành các module hoặc thành phần nhỏ, riêng biệt và liên kết lỏng.

Web service hoặc kiến trúc hướng dịch vụ (SOA) là một điểm khởi đầu tốt. Cách này đem lại các thành phần phần mềm nhỏ, độc lập, có thể được phát triển và quan trọng hơn là phát hành riêng. Nó giúp hiện thực hóa nguyên tắc CD và DevOps: phần mềm chất lượng có thể được phát triển, build, kiểm thử và chuyển giao rất nhanh, nhiều lần liên tiếp.

Kiến trúc thành phần cho phép phát hành thay đổi nhỏ, thường xuyên và cũng có thể đơn giản hóa cách triển khai vật lý cùng hạ tầng. Nếu đang có một hoặc hai khối mã khổng lồ, bạn cần một hoặc hai máy chủ rất lớn để chạy chúng. Chia nhỏ có thể tạo thêm tự do trong cách thiết kế và triển khai hạ tầng cho toàn nền tảng.

Có vô số lựa chọn và tài liệu giúp xác định cách tiếp cận tốt nhất cho nhu cầu hiện tại lẫn tương lai. Theo tác giả, nếu có thể tiến đến kiến trúc thành phần, sự đau đớn và chi phí phát hành sẽ dần biến mất.

Một điểm quan trọng: nếu có kiến trúc thành phần nhưng vẫn gom tất cả vào một đợt phát hành lớn, bạn có thể còn đau đớn hơn khi phát hành nền tảng legacy. Kiến trúc tách thành phần chỉ mang lại lợi ích khi quy trình cho phép chúng thay đổi và phát hành độc lập.

> **Chú thích:** Chia nhỏ không tự động tạo ra hệ thống tốt. Quá nhiều dịch vụ nhỏ có thể làm tăng độ phức tạp phân tán, quan sát, dữ liệu và vận hành. Mục tiêu cốt lõi là ranh giới rõ, liên kết lỏng và khả năng triển khai độc lập, không phải số lượng service.
>
> **Ví dụ:** Tách module gửi email khỏi ứng dụng nguyên khối chỉ có ích nếu module có hợp đồng ổn định và được triển khai độc lập. Nếu mỗi thay đổi vẫn buộc build và phát hành toàn hệ thống, chi phí vận hành tăng nhưng lợi ích CD không xuất hiện.

Nếu không thể tái thiết kế nền tảng, hoặc đã có kiến trúc thành phần nhưng các phụ thuộc cứng vẫn gây đau đớn khi triển khai, lớp trừu tượng có thể hỗ trợ.

### Các lớp trừu tượng

*Nguyên tác: Layers of abstraction*

Khi nền tảng có nhiều phụ thuộc phức tạp, việc tách các tài sản phần mềm bằng một dạng trừu tượng có thể hữu ích. Kỹ thuật này giúp loại bỏ hoặc ít nhất giảm phụ thuộc cứng.

Giả sử hai thành phần được nối cứng đến mức phải triển khai cùng nhau; triển khai một thành phần mà thiếu thành phần kia sẽ khiến nền tảng ngừng hoạt động. Khi đó, rất khó theo phương pháp thay đổi nhỏ, tăng dần, và càng khó phát hành không gián đoạn.

Có nhiều design pattern hỗ trợ giải quyết vấn đề, nhưng tối thiểu bạn nên loại bỏ phụ thuộc chặt ở bất cứ đâu có thể, tránh tạo các cụm tài sản buộc phải triển khai cùng nhau.

Ví dụ đơn giản là cơ sở dữ liệu. Nếu mã truy cập trực tiếp một bảng và bảng được thêm cột mới, bạn có thể phải biên dịch và phát hành lại tài sản phần mềm cùng với thay đổi cơ sở dữ liệu, ngay cả khi mã không đổi. Một cách xử lý là dùng **SQL VIEW** làm lớp trừu tượng giữa mã và cơ sở dữ liệu. Nếu cơ sở dữ liệu thay đổi nhưng VIEW không đổi, không cần biên dịch và phát hành lại mã.

Bạn nên dành thời gian tìm các khu vực liên kết chặt trong nền tảng và xem có thể đặt lớp trừu tượng giữa chúng hay không, qua đó giảm nhu cầu phát hành những cụm lớn cùng lúc.

> **Chú thích:** SQL VIEW chỉ là một ví dụ. Lớp trừu tượng có thể là API, adapter, facade, message schema hoặc compatibility layer. Lớp này cũng cần được thiết kế và quản lý phiên bản; thêm trừu tượng không hợp lý chỉ che giấu chứ không loại bỏ độ phức tạp.
>
> **Ví dụ:** Thay vì để năm dịch vụ đọc trực tiếp bảng khách hàng, tổ chức cung cấp API hồ sơ khách hàng ổn định. Cấu trúc lưu trữ phía sau có thể thay đổi mà không bắt cả năm consumer phát hành đồng thời.

Giả sử bạn đã có các thành phần nhỏ, liên kết lỏng, được viết theo thực hành tốt. Bước tiếp theo là cân nhắc cần bao nhiêu môi trường để bảo đảm thay đổi không ảnh hưởng production và người dùng.

## Bao nhiêu môi trường là đủ?

*Nguyên tác: How many environments is enough?*

Số môi trường cần thiết phụ thuộc cách làm việc, thiết lập kỹ thuật và nền tảng. Điều quan trọng là không đi quá xa.

Bạn có thể muốn tạo nhiều môi trường cho từng kịch bản: development, functional testing, user acceptance testing, performance testing... Nếu có thể giữ tất cả luôn cập nhật, bao gồm dữ liệu test, và dễ dàng triển khai vào chúng thì mô hình này có thể khả thi. Thực tế, quá nhiều môi trường thường phản tác dụng, tạo nhiễu và chi phí quản lý lớn.

Theo tác giả, số môi trường lý tưởng là hai: một cho Development và một cho Production. Nghe như đang chờ tai nạn xảy ra, nhưng nhiều doanh nghiệp nhỏ vận hành tốt với thiết lập này. Khi doanh nghiệp phát triển, nhu cầu tránh rủi ro cũng tăng và có thể dẫn đến nhiều môi trường hơn.

Lúc ACME Systems mới bắt đầu, hai môi trường là đủ. Khi họ lớn lên, số môi trường tăng thành:

- Nhiều môi trường development, mỗi nhóm kỹ thuật một môi trường.
- Một môi trường integration.
- Một môi trường performance testing.
- Một môi trường staging trước khi lên live.
- Các môi trường production.

Cuối cùng, họ cần cả một nhóm chỉ để giữ các môi trường hoạt động - thực tế là hai nhóm: một phụ trách môi trường kỹ thuật và kiểm thử, nhóm kia phụ trách production. Đây không phải trạng thái lý tưởng.

Công nghệ ảo hóa đã trưởng thành và phổ biến, nên dựng hàng trăm máy chủ không còn tốn công như trước. Thách thức khổng lồ là giữ mọi thứ đồng bộ: phiên bản phần mềm, mức bản vá hệ điều hành, cấu hình mạng... Production còn có thể nằm ở khu vực khó truy cập hơn, như data center bảo mật, hoặc do bên thứ ba quản lý.

Khi xem xét nhu cầu cho CD và DevOps, ACME chọn bốn loại môi trường:

- **Development:** phiên bản rút gọn của nền tảng, chỉ có một số thành phần liên quan.
- **CI:** nơi phần mềm được build và toàn bộ kiểm thử tự động được chạy thường xuyên.
- **Pre-production:** dành cho kiểm tra nhanh hoặc UAT không thường xuyên; từ khóa là "không thường xuyên".
- **Production:** nơi mọi hoạt động thực tế diễn ra.

Luồng trong hình:

`Development --build--> CI --deploy--> Pre-production --deploy--> Production`

Thiết lập đủ cho ACME có thể không hoàn toàn phù hợp với bạn, nhưng nó tương đối đơn giản.

> **Chú thích:** Khẳng định "hai môi trường là lý tưởng" phản ánh quan điểm của tác giả, không phải quy tắc phổ quát. Hệ thống chịu quy định, có rủi ro cao hoặc cần kiểm thử hiệu năng riêng có thể cần thêm môi trường. Câu hỏi tốt hơn là: mỗi môi trường có mục đích rõ ràng, có thể tái tạo và được duy trì tự động hay không?
>
> **Ví dụ:** Thay vì giữ 20 môi trường dùng chung luôn bị lệch cấu hình, nhóm có thể duy trì production, một pre-production ổn định và tạo môi trường tạm thời theo pull request bằng Infrastructure as Code rồi hủy sau khi test.

Khi đã có các môi trường cần thiết, thử thách tiếp theo là bảo đảm bạn triển khai **cùng một thứ** vào tất cả chúng.

## Sử dụng cùng một binary trên mọi môi trường

*Nguyên tác: Using the same binary across all environments*

Khi một tài sản phần mềm hoàn tất, nó thường được build hoặc biên dịch thành tệp thực thi. Bạn phải bảo đảm binary chỉ được build **một lần** cho mỗi release/deployment và chính binary không thay đổi đó được dùng ở mọi môi trường, kể cả production. Nghe như lẽ thường, nhưng đôi khi nguyên tắc này bị bỏ qua hoặc không thể thực hiện.

Hãy xét thông tin đăng nhập máy chủ cơ sở dữ liệu. Vì đây là bí mật, nó không nên được cung cấp cho mọi người. Có người còn cho rằng thông tin này nên được nhúng vào binary tại thời điểm biên dịch. Khi đó, tất cả môi trường phải dùng cùng credential, kể cả môi trường development hoàn toàn mở. Để né vấn đề, bạn có thể tạo một binary cho mỗi môi trường, nhưng như vậy bạn đang kiểm thử các phiên bản phần mềm khác nhau.

Lý tưởng là cấu hình nằm trong startup script hoặc tệp cấu hình hệ thống, còn phần mềm nạp nó ở runtime. Nhờ đó, bạn có một binary trên mọi môi trường và cấu hình riêng cho từng môi trường.

Nếu đóng gói thành phần thành gói cài đặt như EXE hoặc RPM, cấu hình có thể được phân phối cùng quy trình đóng gói nhưng binary cốt lõi vẫn phải giống nhau.

Giống mã nguồn, binary cần được quản lý phiên bản và lưu trong repository. Có nhiều giải pháp sẵn có, vì vậy không có lý do để bỏ qua.

> **Chú thích:** Nguyên tắc này thường được gọi là **build once, deploy many**. Nó bảo đảm artifact đã được test chính là artifact chạy ở production. Cấu hình không bí mật có thể được version control; credential và khóa bí mật nên được chèn lúc chạy từ secret manager, không đóng gói vào artifact.
>
> **Ví dụ:** CI tạo image `orders:1.4.2`, ký và lưu theo digest. Dev, staging và production đều chạy đúng digest đó; chỉ biến cấu hình như URL cơ sở dữ liệu và khóa truy cập thay đổi theo môi trường.

Tiếp theo là cách kỹ sư phát triển và kiểm thử dựa trên những thành phần đang chạy trong production.

## Phát triển dựa trên một môi trường giống production

*Nguyên tác: Develop against a like live environment*

Có nhiều cách bảo đảm một phiên bản phần mềm hoạt động hoặc tích hợp với các phần khác của nền tảng. Cách dễ nhất là thực sự phát triển dựa trên một môi trường chứa các phiên bản mã đang chạy live.

Điều này xác nhận rằng những dependency phần mềm kỳ vọng có trong production thực sự tồn tại và vận hành như dự kiến. Trạng thái lý tưởng tuyệt đối là phát triển trực tiếp dựa trên production, nhưng cách đó rất rủi ro và khả năng vô tình gây outage khá cao. Lựa chọn tốt tiếp theo là một môi trường khác chứa toàn bộ phiên bản mã hiện đang chạy trong production.

Có thể bạn thấy môi trường "like-live" là quá mức và tự hỏi vì sao không đơn giản phát triển dựa trên các phiên bản trong CI. Câu trả lời là bạn không biết chắc phiên bản nào trong CI sẽ lên live trước thay đổi của mình, nhất là khi ai đó đang thử một breaking change. Lựa chọn an toàn nhất là phát triển dựa trên thứ đang chạy live.

Môi trường like-live chỉ cần giống production về phiên bản phần mềm và hạ tầng. Có dữ liệu live thì hữu ích, nhưng để chứa lượng dữ liệu tương đương production sẽ cần dung lượng lớn và rất tốn kém.

ACME Systems triển khai như sau:

```text
Development --build--> CI --deploy--> Pre-production --deploy--> Production
     ^                                                        |
     |                                                        | deploy sau khi production thành công
     +------ phát triển dựa trên ------ Like-live <-----------+
```

Môi trường like-live được gắn ở cuối deployment pipeline có chủ ý: chỉ triển khai vào đó sau khi triển khai production thành công. Như vậy nó tiếp tục phản ánh đúng trạng thái đang live.

Một lựa chọn khác là ảo hóa trên máy trạm: dựng bản sao ảo của production trên máy của lập trình viên, giả sử máy có đủ sức mạnh và dung lượng.

> **Chú thích:** *Like-live environment* có thể hiểu là môi trường bản sao production về phiên bản và cấu hình quan trọng. Không nên sao chép dữ liệu khách hàng nhạy cảm một cách tùy tiện; hãy che giấu, tổng hợp hoặc tạo dữ liệu đại diện phù hợp quy định bảo mật.
>
> **Ví dụ:** Sau mỗi deployment production thành công, pipeline cập nhật môi trường replica sang đúng image digest và migration version. Lập trình viên dùng replica để kiểm tra thay đổi tương thích với phiên bản hiện hành trước khi merge.

Bạn đã có gần đủ các khối nền tảng. Vẫn còn câu hỏi làm thế nào đưa thành phần đã build và kiểm thử qua các môi trường. Đây là lúc công cụ CD xuất hiện.

## Công cụ CD

*Nguyên tác: CD tooling*

Nhóm công cụ này, theo thời điểm cuốn sách được viết, có thể không sẵn có nhiều như công cụ build, test và CI. Chúng dùng để kiểm soát và điều phối việc triển khai thành phần phần mềm đến các môi trường.

Không phải hoàn toàn không có công cụ, nhưng số lượng ít hơn và sản phẩm hiện có có thể không khớp hoàn toàn nhu cầu hoặc cách làm việc. Bạn có thể thay đổi quy trình để vừa với một công cụ hoặc tập hợp công cụ, nhưng chi phí chuyển đổi có thể rất lớn. Xây giải pháp riêng đôi khi đơn giản hơn và giúp quy trình CD thích nghi tốt hơn trong tương lai.

Công cụ được chọn hoặc xây sẽ được dùng hàng ngày và phụ thuộc rất nhiều, nên nó phải thật tốt, dễ dùng và đơn giản khi vận hành - lý tưởng là triển khai chỉ bằng một cú nhấp.

ACME nhận thấy giải pháp sẵn có không phù hợp nên xây công cụ riêng, đáp ứng nhu cầu lúc đó và có thể mở rộng khi việc áp dụng CD và DevOps trưởng thành. Họ dùng các thực hành kỹ thuật tốt đã nêu và coi giải pháp như một sản phẩm mã nguồn mở nội bộ: bất kỳ ai trong nhóm kỹ thuật ACME, không chỉ nhóm chuyên trách, cũng có thể cải tiến.

Danh sách tiêu chí ACME cân nhắc:

- Có thể triển khai cùng một binary đến nhiều môi trường không?
- Có thể truy cập binary repository và source repository không?
- Có thể gọi từ xa và điều khiển quá trình cài đặt trên máy chủ đích không?
- Có triển khai được thay đổi cơ sở dữ liệu không?
- Có quản lý hoặc ít nhất tham chiếu cấu hình riêng của môi trường/máy chủ và triển khai nó cùng binary không?
- Có hàng đợi release không?
- Có audit ghi lại thứ gì được triển khai, khi nào và bởi ai không?
- Có an toàn không?
- Có tương tác với hạ tầng để hỗ trợ triển khai không gián đoạn không?
- Có thể điều phối cấp phát hạ tầng tự động không?
- Có thể mở rộng để tương tác với email, quản lý thay đổi, quản lý issue và quản lý dự án không?
- Có dashboard đơn giản, dễ hiểu để hiển thị trên màn hình lớn trong văn phòng không?
- Có thể tương tác hoặc điều phối giải pháp CI không?
- Có phát triển theo nhu cầu tương lai không?
- Có đủ đơn giản để mọi người đều sử dụng được không?

Nếu tìm được công cụ đáp ứng tất cả, xin chúc mừng. Nếu không, bạn phải viết thêm mã hoặc ghép nhiều công cụ để bao phủ nhu cầu.

> **Chú thích:** Phần này phản ánh thị trường công cụ vào khoảng thời gian sách xuất bản. Hiện nay hệ sinh thái CI/CD và GitOps phong phú hơn đáng kể. Tuy nhiên, danh sách tiêu chí vẫn hữu ích. Trước khi tự xây, nên tính cả chi phí bảo trì, bảo mật, khả năng audit và phụ thuộc vào một số ít người hiểu công cụ.

Một tiêu chí trong danh sách là cấp phát tự động. Hãy xem nó có nghĩa gì.

### Cấp phát tự động

*Nguyên tác: Automated provisioning*

Nếu nền tảng có thể chạy hoàn toàn trên hạ tầng ảo hóa, hoặc đã được tái thiết kế để làm vậy, bạn có thể đưa cấp phát tự động vào quy trình triển khai.

Provisioning không mới. Từ khi các nhà cung cấp như Amazon cung cấp máy chủ đám mây, người dùng đã có thể tạo tài nguyên cần thiết khi cần, với một mức phí. Đưa provisioning thành một bước trong deployment là việc rất hữu ích và mạnh mẽ, dù nó cũng có thể phức tạp, đau đớn khi triển khai nếu bạn chưa có kinh nghiệm.

Ngành công nghệ có nhiều buzzword làm hoạt động này nghe phức tạp hơn, như Infrastructure as a Service (IaaS) và Platform as a Service (PaaS). Về bản chất, bạn giao tiếp với một hệ thống bằng chương trình, chỉ định đặc tả, hệ điều hành, cấu hình và các yêu cầu khác, rồi hệ thống tạo ra tài nguyên tương ứng.

Bạn có tài sản phần mềm đã được kiểm thử đầy đủ và "công thức" cấu hình môi trường/máy chủ nằm trong công cụ CD. Quá trình deployment về cơ bản là:

1. Cấp phát máy chủ mà tài sản phần mềm cần để chạy.
2. Triển khai tài sản lên máy chủ.
3. Cài đặt nó.
4. Thêm máy chủ vào nền tảng.
5. Bắt đầu sử dụng.

Thực tế còn nhiều chi tiết hơn, nhưng nếu có khả năng này thì không có lý do gì để không cân nhắc. Nếu chọn hướng đó, công cụ CD bắt buộc phải hỗ trợ. Một lợi ích khác của cấp phát tự động lúc triển khai là giúp thực hiện deployment không gián đoạn.

> **Chú thích:** Cách triển khai hiện đại thường mô tả "công thức" bằng Infrastructure as Code và cấu hình khai báo. Tài nguyên cần có tính lặp lại, được version control, review và kiểm thử giống mã ứng dụng.
>
> **Ví dụ:** Pipeline nhận Terraform module và image đã ký, tạo nhóm máy chủ mới, áp dụng cấu hình, chạy health check rồi mới đưa chúng vào load balancer. Nếu kiểm tra thất bại, tài nguyên mới bị hủy mà hệ thống cũ không bị ảnh hưởng.

### Triển khai không gián đoạn

*Nguyên tác: No-downtime deployments*

Một vấn đề đi kèm các đợt phát hành phần mềm lớn, dù legacy hay không, là nhu cầu không thể chấp nhận được phải đưa một phần hoặc toàn bộ nền tảng offline trong lúc phát hành. Tác giả gọi điều này là "không thể tha thứ" vì nó hoàn toàn có thể tránh.

Nếu vận hành dịch vụ trực tuyến thời gian thực, khách hàng chắc chắn không vui khi không thể truy cập hệ thống hoặc dữ liệu trong vài giờ chỉ để bạn nâng cấp một phần nền tảng. Họ còn có thể mất lòng tin vì lo điều gì đó sẽ hỏng sau khi hệ thống hoạt động trở lại. Nếu sự cố thực sự xảy ra, bạn phải chuyển sang hạn chế thiệt hại để giữ khách hàng, và họ có thể tìm đối thủ không có downtime.

Bức tranh này có vẻ tiêu cực, nhưng đó là thực tế, nhất là khi mạng xã hội lan truyền tin xấu rất nhanh. Tin xấu do một đợt phát hành gây ra sẽ làm giảm tự tin, tổn hại danh tiếng và bào mòn lòng tin đã xây dựng trong doanh nghiệp. Sự cố liên quan đến release vẫn có thể xảy ra; chủ động cộng thêm downtime chỉ làm tình hình tệ hơn.

Một số việc đơn giản giúp loại bỏ nhu cầu downtime:

- Tuân thủ nghiêm ngặt quy tắc không làm hỏng consumer.
- Giữ thay đổi nhỏ và riêng biệt.
- Nếu có thể, triển khai provisioning tự động và tích hợp vào công cụ CD.
- Dùng load balancer và để công cụ CD điều phối máy chủ ra/vào pool trong lúc triển khai.
- Nếu buộc phải có breaking change, triển khai dần thay vì big bang, chẳng hạn bắt đầu từ tầng dưới rồi đi lên.

Có nhiều kỹ thuật khác, nhưng theo tác giả, nếu mỗi lần phát hành phần mềm đều phải đưa nền tảng offline thì đang có một vấn đề nền tảng trong thiết kế hoặc quy trình.

Không chỉ production mới cần uptime tối đa. Mọi môi trường mà development, testing và CD phụ thuộc đều phải được đối xử tương tự. Nếu like-live ngừng hoạt động, bạn phát triển thế nào? Nếu CI ngừng, bạn tích hợp và kiểm thử ra sao? Cùng một quy tắc phải áp dụng không ngoại lệ.

> **Chú thích:** Các chiến lược phổ biến gồm rolling deployment, blue-green deployment, canary release, load balancer draining và feature flag. "Không downtime" không loại bỏ nhu cầu rollback, backup và kế hoạch ứng phó sự cố.
>
> **Ví dụ:** Với blue-green deployment, phiên bản mới được triển khai vào cụm Green trong khi Blue vẫn phục vụ người dùng. Sau health check, lưu lượng chuyển dần sang Green. Nếu chỉ số lỗi tăng, lưu lượng được chuyển về Blue mà không cần tắt dịch vụ.

Trước đây, chúng ta đã nói về cách làm việc cởi mở và trung thực trong thực hành kỹ thuật. Các phẩm chất đó cũng quan trọng với CD. Một cách cung cấp minh bạch là giám sát mọi thứ và cho phép mọi người xem.

## Giám sát

*Nguyên tác: Monitoring*

Một trong những cách quan trọng nhất để biết CD và DevOps có hoạt động hay không là **giám sát, giám sát và tiếp tục giám sát**.

Nếu mọi môi trường trong quy trình CD được quan sát liên tục, tác động của bất kỳ thay đổi lớn hay nhỏ nào cũng dễ nhìn thấy; không nên có bất ngờ bị che giấu. Độ bao phủ giám sát tốt tạo minh bạch trên toàn hệ thống.

Monitoring không nên chỉ dành cho Operations. Mọi người trong doanh nghiệp phải có thể nhìn thấy và hiểu một môi trường, đặc biệt là production, đang hoạt động ra sao và làm gì.

Có nhiều công cụ monitoring nhưng rất khó tạo một góc nhìn duy nhất, nhất quán và có ý nghĩa. Chẳng hạn, Nagios khá tốt trong giám sát hạ tầng và máy chủ, còn Graphite khá tốt trong thu thập metric ứng dụng. Nếu không liên kết chúng thành một góc nhìn mạch lạc, thông tin sẽ rời rạc.

Lý tưởng là tổng hợp hoặc tích hợp dữ liệu từ các công cụ, rồi trình bày một góc nhìn thống nhất về cách production - hoặc bất kỳ môi trường nào - đang chịu tải và vận hành. Bạn có thể thu được lượng dữ liệu rất giá trị để định hướng công việc kỹ thuật, bởi kỹ sư nhìn thấy chính xác phần mềm hoặc hạ tầng hoạt động trong thời gian thực với người dùng thật.

Monitoring là điều bắt buộc để CD và DevOps vận hành đúng. Khi production thay đổi, dù phần mềm hay hạ tầng, cả Dev và Ops đều nhìn thấy chuyện gì đang xảy ra và có thể hỗ trợ khi có vấn đề.

Một lợi ích ít hiển nhiên khác là monitoring cung cấp bằng chứng rằng CD không tác động xấu đến production. Nếu dùng biểu đồ theo thời gian, công cụ CD có thể đặt một dấu mốc lên biểu đồ lúc deployment diễn ra. Sau đó, bạn nhìn trực quan được thay đổi có tạo tác động hay không.

> **Chú thích:** Ngày nay, monitoring thường nằm trong khái niệm rộng hơn là **observability**, kết hợp metric, log, trace và sự kiện deployment. Mục tiêu không chỉ là biết hệ thống "đỏ hay xanh", mà còn điều tra được vì sao hành vi thay đổi.
>
> **Ví dụ:** Pipeline ghi marker lúc phiên bản 2.3.1 được phát hành. Dashboard cho thấy ngay sau marker, p95 latency tăng từ 180 ms lên 700 ms và tỷ lệ lỗi tăng. Nhóm có đủ bằng chứng để rollback nhanh và khoanh vùng phiên bản gây vấn đề.

Cho đến đây, phần lớn nội dung tập trung vào giải pháp kỹ thuật. Tuy nhiên, các quy trình thủ công đơn giản vẫn có chỗ đứng để bổ sung cho công cụ.

## Khi một quy trình thủ công đơn giản cũng là công cụ hiệu quả

*Nguyên tác: When a simple manual process is also an effective tool*

Ngay cả khi có rất nhiều công cụ, chắc chắn vẫn tồn tại những thử thách nhỏ dai dẳng mà tooling và tự động hóa không thể tự giải quyết. Trong một số trường hợp, tự động hóa là quá mức và còn có thể tạo rào cản giữa chính những phần tổ chức mà bạn đang cố gắng gắn kết, tức quan hệ Dev và Ops.

Nếu công cụ loại bỏ hoàn toàn nhu cầu tương tác và thảo luận giữa con người, bạn có thể quay lại đúng điểm xuất phát. Đôi khi cũng gần như không thể tự động hóa để thoát khỏi một vấn đề vốn rất đơn giản.

Hãy xét bài toán quản lý dependency. Khi nền tảng trưởng thành, nhiều quan hệ phụ thuộc hình thành. Trong quy trình CD, các quan hệ này trở thành mục tiêu liên tục di chuyển vì thành phần được phát triển và triển khai với tốc độ khác nhau. CI có thể nắm bắt phần lớn, nhưng vẫn có khả năng bỏ sót điều gì đó và vô tình làm sập toàn nền tảng vì component B được triển khai trước component A.

Bạn có thể lập bản đồ và xây quy tắc vào công cụ để hạn chế các mục tiêu di động, nhưng cuối cùng quy tắc có thể còn phức tạp hơn dependency ban đầu. Một lựa chọn khác là thống nhất quy trình chỉ cho phép một thay đổi diễn ra tại mỗi thời điểm. Hỗ trợ nó bằng một hàng đợi đơn giản viết trên bảng trắng, được các nhóm Engineering và Operations cùng xem xét thường xuyên.

Cách tiếp cận này hoạt động rất tốt tại ACME:

- Mọi người thống nhất rằng tại một thời điểm chỉ có một thay đổi đi vào production. Họ gọi đó là **deployment transaction**.
- Họ không sửa công cụ CD để ép buộc quy tắc, mà dựa vào lẽ thường và cách làm việc cộng tác.
- Người đang thay đổi production, dù deployment hay thay đổi vận hành, phải giữ **production environment token**. Token là một con thú nhồi bông có tên **build badger**. Ai giữ build badger thì người đó đang thay đổi production.
- Họ dùng bảng trắng và bút làm hàng đợi ưu tiên. Mỗi sáng, những người muốn deployment tham gia deployment stand-up để thống nhất thứ tự thay đổi trong ngày.
- Màn hình được lắp khắp văn phòng, không chỉ ở khu Dev và Ops, để hiển thị dashboard thời gian thực.

Tất cả đều rất đơn giản, nhưng giúp ACME vượt qua "địa ngục dependency". Khi chỉ thay đổi một thứ mỗi lần, thứ tự logic giữa các thay đổi trở nên rõ. Cách làm còn xây dựng tinh thần cộng tác giữa mọi nhóm liên quan.

Trong hình của sách, deployment transaction bao phủ phần cuối của pipeline:

```text
Development --build--> CI --deploy--> [Pre-production --deploy--> Production]
                                    [          |                     |       ]
                                    [          +--> Like-live <------+       ]
                                    [        DEPLOYMENT TRANSACTION          ]
```

Một số giải pháp thủ công đơn giản khác:

- Dùng công cụ cộng tác để mọi người giao tiếp thời gian thực, như IRC hoặc phòng chat, rồi tích hợp với CD để deployment được thông báo và ai cũng theo dõi được.
- Nếu quản lý chưa yên tâm để lập trình viên triển khai production mà không có Operations, hãy đặt một máy trạm trong khu Operations, gọi là **deployment station**, và chỉ cho phép deployment live từ máy đó.
- Nếu cần rollback tức thì khi deployment thất bại, dùng cách đơn giản như triển khai lại phiên bản trước bằng công cụ CD.
- Liên tục kiểm tra và thích ứng thông qua retrospective định kỳ để biết điều gì hiệu quả và điều gì không.

Như vậy, không phải mọi giải pháp đều mang tính kỹ thuật. Nếu quy trình thủ công đơn giản hoặc một điều chỉnh trong cách làm việc đã đủ, không nhất thiết phải tự động hóa nó.

> **Chú thích:** Giới hạn một deployment tại một thời điểm có thể là biện pháp chuyển tiếp hữu ích cho hệ thống nhiều dependency, nhưng sẽ giới hạn thông lượng khi tổ chức phát triển. Mục tiêu dài hạn nên là giảm coupling, tự động phát hiện xung đột và cho phép các thay đổi độc lập diễn ra an toàn song song.
>
> **Ví dụ:** Ban đầu, nhóm dùng một "deployment token" và stand-up mỗi sáng để tránh hai migration xung đột. Sau khi bổ sung ownership rõ ràng, contract test và cơ chế khóa tự động theo tài nguyên, những deployment không liên quan có thể chạy song song; quy trình thủ công chỉ còn dùng cho thay đổi rủi ro cao.

Và bài học tạm kết thúc tại đây. Hãy tổng kết những gì đã được trình bày.

## Tổng kết

*Nguyên tác: Summary*

Như đã nói ở đầu chương, có rất nhiều nội dung cần tiếp nhận. Một số phù hợp ngay bây giờ, một số sẽ hữu ích trong tương lai. Chương này đã trình bày:

- Sử dụng thực hành kỹ thuật tốt là điều bắt buộc và không quá khó.
- Không có lý do để không triển khai quản lý mã nguồn, tự động hóa hoặc CI.
- Nếu có cách đáng tin cậy và lặp lại để thường xuyên tạo tài sản phần mềm nhỏ, được kiểm thử đầy đủ, việc deployment sẽ bớt đau đớn nhiều.
- Phải dùng cùng một binary trên mọi môi trường; không cần hàng chục hoặc hàng trăm môi trường mới kiểm thử hiệu quả phần mềm và nền tảng.
- Không phải mọi công cụ cần thiết đều mang tính kỹ thuật. Đôi khi một quy trình hoặc hoạt động thủ công nhỏ hữu ích và mạnh hơn công cụ tự động hoàn toàn.
- Nếu nền tảng hỗ trợ ảo hóa, hãy sử dụng nó.
- Nếu không tìm được công cụ CD phù hợp nhu cầu, hãy xây dựng hoặc kết hợp công cụ.
- Không có lý do để đưa production, hoặc bất kỳ môi trường nào mà quy trình CD phụ thuộc, offline chỉ để nâng cấp.
- Giám sát, giám sát và giám sát.

Như bạn thấy, bạn và nhóm có khá nhiều việc phải làm, triển khai và hướng dẫn người khác. Không phải tất cả đều là kỹ thuật; một phần lớn công việc chỉ đơn giản là thuyết phục mọi người chấp nhận và sử dụng các công cụ cùng quy trình trên.

Và đó mới là phần dễ. Chương tiếp theo sẽ bàn về mặt con người của DevOps: văn hóa và hành vi mà bạn cần cải thiện.

