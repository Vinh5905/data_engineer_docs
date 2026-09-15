# Chương 3: Kế hoạch hành động

*Nguyên tác: Chapter 3: Plan of Attack - trang 33-48*

> **Ghi chú về tiêu đề:** *Plan of attack* vốn là cách nói mang sắc thái quân sự, nhưng trong ngữ cảnh này có nghĩa là một kế hoạch hành động có mục tiêu, thứ tự ưu tiên, nguồn lực và cách triển khai rõ ràng.

Trong Chương 2, *Không gian nan, không thành quả*, bạn đã được giới thiệu các công cụ và kỹ thuật giúp nhận diện những vấn đề có thể tồn tại trong toàn bộ quy trình chuyển giao sản phẩm. Chúng ta gọi vấn đề đó là "con voi trong phòng": không khó nhìn thấy, nhưng rất dễ bị phớt lờ.

Ở đây, tác giả giả định rằng cuộc điều tra đã phát hiện những vấn đề phổ biến trong nhiều quy trình chuyển giao phần mềm:

- Lãng phí do quy trình có quá nhiều điểm bàn giao và điểm ra quyết định.
- Lãng phí do thời gian chờ không cần thiết giữa các bước.
- Nhiều thay đổi phần mềm bị đóng gói thành những đợt phát hành "big bang" lớn và phức tạp.
- Các đợt phát hành lớn, không thường xuyên tạo điều kiện cho lỗi lọt ra ngoài và bug phát sinh.
- Phát hành bị xem là chuyện đáng sợ thay vì một cơ hội thay đổi tích cực.
- Mọi người thiếu gắn kết, tinh thần làm việc thấp, hoặc cả hai.
- Các thay đổi phần mềm không được tin tưởng cho đến khi đã bị kiểm thử đi kiểm thử lại rất nhiều lần.
- Thiết kế phần mềm có các mối phụ thuộc quá phức tạp.
- Nhiều công việc bị lặp lại trong suốt quy trình.

Bây giờ, chúng ta sẽ lấy thông tin và dữ liệu đã thu thập, rồi tìm cách biến chúng thành thứ có thể triển khai để khắc phục vấn đề - nói cách khác, xây dựng một kế hoạch hành động để áp dụng Continuous Delivery (CD) và DevOps.

Không nên xem nhẹ kế hoạch này. Cũng như ở giai đoạn điều tra, bạn phải làm khá nhiều công việc nền tảng để bảo đảm phạm vi triển khai được hiểu đúng, chấp thuận và truyền đạt rõ ràng. Giống mọi kế hoạch hoặc dự án khác, nó cần một mục tiêu cuối cùng và một tầm nhìn về cách đi đến mục tiêu ấy.

> **Chú thích:** *Big bang release* là cách gom rất nhiều thay đổi vào một đợt phát hành lớn. Vì phạm vi thay đổi rộng, chu kỳ dài và khó cô lập lỗi, rủi ro thường cao hơn so với những thay đổi nhỏ, thường xuyên.
>
> **Ví dụ:** Thay vì phát hành 40 tính năng mỗi quý trong một đêm cuối tuần, nhóm dần chuyển sang phát hành từng thay đổi nhỏ mỗi ngày. Khi một thay đổi gây lỗi, phạm vi cần điều tra và rollback nhỏ hơn nhiều.

## Thiết lập và truyền đạt mục tiêu cùng tầm nhìn

*Nguyên tác: Setting and communicating goals and vision*

Mục tiêu và tầm nhìn rất quan trọng đối với mọi dự án. Chúng giúp tất cả những người liên quan biết điều gì được kỳ vọng, đồng thời giúp những người thực hiện dự án hiểu dự án - và chính họ - đang đi về đâu. Nghe có vẻ đơn giản, nhưng điều này không phải lúc nào cũng hiển nhiên.

Bên cạnh việc xác lập mục tiêu và tầm nhìn, nội dung bạn truyền đạt và cách bạn truyền đạt cũng quan trọng không kém. Nếu làm sai một trong hai, bạn có nguy cơ đánh mất sự ủng hộ của doanh nghiệp, đặc biệt là từ lãnh đạo cấp cao.

Chẳng hạn, họ có thể tin rằng chỉ cần xử lý một hoặc hai vấn đề được phát hiện trong cuộc điều tra là đủ giải quyết toàn bộ khó khăn. Bạn phải nói thật rõ mình định đạt được điều gì, đồng thời xác định thật rõ mình đang truyền đạt cho ai.

Đối với CD và DevOps, đây có thể là một thử thách vì người chưa quen không phải lúc nào cũng dễ hiểu hoặc hình dung sản phẩm bàn giao và lợi ích của chúng. Việc định lượng đầy đủ cũng có thể khó, bởi một số lợi ích của CD và DevOps không hoàn toàn hữu hình - chẳng hạn rất khó đo chính xác mức tăng về cộng tác và hạnh phúc của nhóm.

Lời khuyên tốt nhất là **KISS: Keep It Simple, Stupid** - hãy giữ mọi thứ đơn giản. Bạn có một danh sách vấn đề do toàn doanh nghiệp cung cấp, còn điều họ muốn là một thứ gì đó giúp cuộc sống dễ dàng hơn và cho phép họ làm tốt công việc. Nói thật, danh sách đó có lẽ dài hơn khả năng thực hiện hiệu quả của bạn. Hãy xem đây là điều tốt, vì bạn còn không gian để sắp xếp ưu tiên.

> **Chú thích:** KISS không khuyên đơn giản hóa vấn đề một cách ngây thơ. Ý của nó là diễn đạt mục tiêu bằng ngôn ngữ rõ ràng, loại bỏ sự phức tạp không cần thiết và tránh biến kế hoạch thành một tập hợp khẩu hiệu kỹ thuật khó hiểu.

Thử thách của bạn là xây dựng một mục tiêu và tầm nhìn có sức cộng hưởng với mọi bên liên quan, đồng thời bảo đảm chúng có thể được thực hiện. Công việc này có thể tốn khá nhiều công sức, nhưng hoàn toàn khả thi.

Hãy xem lại ví dụ ACME Systems. Khi lập kế hoạch triển khai CD và DevOps, họ đặt mục tiêu cho dự án là:

> **KHÁCH HÀNG CỦA BẠN CẦN BẠN!**
>
> **Mục tiêu của chúng ta: chuyển giao phần mềm hoạt động được cho khách hàng 10 lần mỗi ngày.**

Đây là một khẩu hiệu rất đơn giản mà gần như bất kỳ ai cũng có thể hiểu, và nó trở thành nền tảng cho chiến lược truyền thông. Họ còn làm poster và dán chúng lên các bức tường quanh văn phòng.

Mục tiêu này đầy tham vọng, nhưng họ biết rằng với nỗ lực, lòng can đảm, quyết tâm và đúng những người cần thiết, nó có thể trở thành hiện thực.

Việc đặt mục tiêu cho bạn có thể cũng đơn giản như vậy. Bạn đã hiểu rõ các vấn đề kinh doanh cần khắc phục, biết những nhóm nào có liên quan và có ý niệm tốt về điều sẽ tạo được sự đồng cảm từ các bên liên quan. Với một bảng trắng trống và một cây bút, bạn có thể nhanh chóng lấp đầy bảng bằng các mục tiêu khả dĩ.

Hãy tham khảo ý kiến những người có năng lực phán đoán mà bạn tin tưởng. Nếu họ cho rằng mục tiêu đề xuất lệch quá xa, rất có thể đúng là như vậy. Nếu có nhân sự PR hoặc marketing, hãy hỏi ý kiến họ; đây vốn là lĩnh vực họ giỏi. Một kế hoạch truyền thông cấp cao cũng có thể giúp thông điệp tập trung hơn cho từng nhóm đối tượng.

Quay lại nhóm dự án CD của ACME. Họ đã có mục tiêu "triển khai 10 lần mỗi ngày" và giờ phải xác lập tầm nhìn. Tầm nhìn gồm nhiều nội dung, có phần kỹ thuật và có phần phi kỹ thuật, tất cả đều có thể được truyền đạt rõ ràng. Sau đó, tầm nhìn được phân rã để cho thấy nội dung nào sẽ được xử lý trước và nội dung nào xử lý sau.

Người quen với Agile có thể nhận ra đây là một **feature backlog đã được sắp xếp ưu tiên**, trong đó mục tiêu đóng vai trò như một **epic**.

> **Chú thích:**
>
> - **Goal - mục tiêu:** đích đến rõ ràng, tốt nhất là có thể kiểm chứng. Ví dụ: có khả năng triển khai an toàn 10 lần mỗi ngày.
> - **Vision - tầm nhìn:** bức tranh rộng hơn về trạng thái tương lai và hướng đi để đạt mục tiêu, gồm cả thay đổi kỹ thuật, quy trình và con người.
> - **Epic:** một nhu cầu hoặc kết quả lớn cần được chia thành nhiều feature hoặc story nhỏ hơn để thực hiện.
>
> **Ví dụ:** "Rút ngắn thời gian đưa giá trị đến khách hàng" là định hướng rộng. "Đến cuối quý IV, 90% thay đổi thông thường có lead time dưới 24 giờ" là mục tiêu có thể đo. Tầm nhìn có thể bao gồm tự động hóa pipeline, cải thiện kiểm thử và trao quyền cho nhóm tự phát hành.

Bước tiếp theo của ACME là trình bày mục tiêu và tầm nhìn với doanh nghiệp cùng các bên liên quan, rồi đạt được sự thống nhất rằng đề xuất đó sẽ xử lý các vấn đề đã được ghi nhận trong cuộc điều tra. Phần trình bày hướng tới đối tượng rộng nhất có thể, không chỉ ban quản lý. Nhiều phiên được tổ chức trong nhiều ngày để càng nhiều người càng tốt có thể tham gia.

Khi tầm nhìn đã được chấp thuận, họ chia các hạng mục ưu tiên cao nhất của tầm nhìn - tức các feature ưu tiên cao - thành yêu cầu, tức story, sao cho dễ hiểu và, quan trọng hơn, có thể chuyển giao.

Để bảo đảm tính minh bạch và khả năng truy cập thuận tiện, nhóm ACME CD phải làm cho mọi dữ liệu, thông tin và kế hoạch đều được công khai trong nội bộ doanh nghiệp, chứ không phải công khai ra Internet. Họ tận dụng toàn bộ công cụ truyền thông nội bộ và kho dự án đang có: wiki nội bộ, blog, website, intranet và diễn đàn.

Nếu tổ chức chưa có những công cụ như vậy, việc thiết lập một giải pháp mã nguồn mở không nên tốn quá nhiều công sức. Cũng có các giải pháp trực tuyến đủ an toàn để bảo vệ bí mật doanh nghiệp. Mức độ minh bạch và cởi mở này sẽ hỗ trợ bạn trong quá trình thực thi kế hoạch, đặc biệt với những giải pháp mang tính xã hội như blog và diễn đàn, nơi mọi người có thể phản hồi và thảo luận trực tuyến.

Khi được cô đọng trong vài đoạn, mọi chuyện nghe khá đơn giản - và với môi trường phù hợp cùng đúng người tham gia, nó thực sự có thể đơn giản. Điều cốt lõi là hiểu doanh nghiệp và các bên liên quan muốn gì, biết tóm lược nó thành một mục tiêu dễ hiểu, rồi căn chỉnh tầm nhìn để dẫn dắt mọi thứ đúng hướng.

Từ khóa là **dễ hiểu**. Đây có thể là một thách thức khi phải giao tiếp giữa nhiều khu vực kinh doanh, thậm chí nhiều múi giờ và nền văn hóa, trong khi mỗi nơi có cách hiểu riêng về thuật ngữ và từ vựng. Vì vậy, chúng ta cần xem xét cách giao tiếp sao cho tất cả người liên quan đều hiểu chuyện gì đang diễn ra.

## Chuẩn hóa từ vựng và ngôn ngữ

*Nguyên tác: Standardizing vocabulary and language*

Một điều nhỏ nhưng hoàn toàn có thể tránh được vẫn có thể đánh đắm cả dự án: hiểu sai sản phẩm cần bàn giao. Nghe có vẻ đáng báo động, nhưng dự án có thể thất bại đơn giản vì một người kỳ vọng một thứ, trong khi người khác hiểu sai hoặc diễn giải khác rồi bàn giao một thứ khác.

Thông thường, nguyên nhân không phải sự thiếu hiểu biết mà là hai bên diễn giải cùng một khái niệm theo hai cách khác nhau.

Hãy xét một từ tưởng như đơn giản: **release**. Đối với Project Manager hoặc Release Manager, nó có thể là một gói thay đổi phần mềm cần được kiểm thử và đưa vào hoạt động theo một lịch hoặc chương trình công việc. Đối với một lập trình viên làm việc theo Agile, release có thể chỉ là một dòng mã thay đổi, được đưa vào hoạt động ngay sau khi hoàn tất lập trình và chạy kiểm thử.

Vấn đề còn lớn hơn khi xem xét vô số từ ngữ, thuật ngữ và **TLA - three-letter acronym**, tức các chữ viết tắt gồm ba ký tự, được sử dụng trong ngành CNTT.

Vì vậy, phải chú ý đến đối tượng mình đang giao tiếp. KISS tiếp tục hữu ích. Bạn không nhất thiết phải hạ mọi nội dung xuống "mẫu số chung thấp nhất"; việc đó rất khó, có thể buộc bạn viết cả một cuốn sách và thậm chí làm tình hình tệ hơn. Hãy tìm điểm cân bằng. Nếu có người chưa hiểu, hãy nhờ một người đã hiểu trao đổi và giải thích cho họ. Cách này vừa thu hẹp khoảng cách vừa hình thành quan hệ làm việc tốt.

Một cách khác là xây dựng bảng thuật ngữ để mọi người cùng tham chiếu. Sách đưa ra ví dụ đơn giản sau:

| Thuật ngữ | Nó là gì | Nó không phải là gì |
|---|---|---|
| **Continuous Delivery** | Phương pháp chuyển giao phần mềm đã được kiểm thử và hoạt động đầy đủ thành những phần tăng trưởng nhỏ lên môi trường production. | Phương pháp chuyển giao những gói mã khổng lồ sau mỗi vài tuần hoặc vài tháng. |
| **DevOps** | Cách làm việc khuyến khích nhóm Development và Operations cộng tác chặt chẽ để hướng đến cùng một mục tiêu. | Cách bắt lập trình viên gánh công việc vận hành hoặc bắt Operations gánh công việc phát triển. |
| **CD** | Xem *Continuous Delivery*. | - |
| **Continuous Integration** | Phương pháp phát hiện vấn đề phần mềm càng sớm càng tốt trong chu kỳ phát triển và bảo đảm mọi phần của nền tảng tổng thể giao tiếp chính xác với nhau. | Thứ có thể bị phớt lờ hoặc đi vòng qua chỉ vì nó đòi hỏi công sức. |
| **CI** | Xem *Continuous Integration*. | - |
| **Definition of Done** | Một thay đổi đối với nền tảng - phần mềm, phần cứng, hạ tầng... - đã chạy thật và được khách hàng sử dụng. | Một thứ chỉ mới được ký xác nhận trên danh nghĩa là "sẽ hoạt động khi đưa lên live". |
| **DOD** | Xem *Definition of Done*. | - |
| **Release** | Một lần đưa mã đơn lẻ vào một môi trường cụ thể như test, staging hoặc production. | Một gói thay đổi khổng lồ được bàn giao cho người khác tự tìm cách xử lý. |
| **Deploy** | Hành động đẩy một release vào một môi trường cụ thể. | Việc mặc nhiên chỉ thuộc trách nhiệm của nhóm Operations. |

Nếu có wiki, intranet, blog hoặc diễn đàn, đó là nơi phù hợp để chia sẻ bảng này. Những người khác có thể cập nhật nó theo thời gian khi xuất hiện thêm buzzword và TLA.

Nguyên tắc thực hành là: dù chuẩn hóa theo từ vựng, ngôn ngữ hoặc thuật ngữ nào, bạn cũng phải tuân thủ và dùng nhất quán. Chẳng hạn, nếu chọn cụm "CD và DevOps", hãy dùng đúng cụm đó trong mọi hình thức giao tiếp, cả viết lẫn nói. Dần dần, nó trở thành thói quen và được dùng hàng ngày, giúp các cuộc trao đổi nhất quán hơn và giảm đáng kể nguy cơ hiểu nhầm hoặc nhầm lẫn.

> **Chú thích:** Định nghĩa *Definition of Done* trong sách rất nghiêm ngặt: thay đổi phải được khách hàng sử dụng. Trong nhiều nhóm Scrum hiện nay, DoD có thể chỉ yêu cầu increment ở trạng thái có thể phát hành, chưa nhất thiết đã lên production. Không có một định nghĩa duy nhất cho mọi tổ chức; điều quan trọng là định nghĩa phải minh bạch, phù hợp mục tiêu và được cả nhóm thống nhất.
>
> **Ví dụ:** Nếu "deploy" đối với Dev nghĩa là đẩy lên staging, nhưng đối với Ops lại nghĩa là đưa lên production, câu "đã deploy xong" có thể gây ra quyết định sai. Bảng thuật ngữ nên ghi rõ cả động từ lẫn môi trường đích, chẳng hạn "deploy to staging" và "deploy to production".

Bây giờ bạn đã có mục tiêu, tầm nhìn, backlog cấp cao và một cách giao tiếp chuẩn hóa. Bạn gần như sẵn sàng khởi động. Tuy nhiên, không được xem nhẹ việc thực thi kế hoạch.

Dù là một công ty phần mềm nhỏ hay một tập đoàn lớn, bạn nên dành cho việc áp dụng CD và DevOps mức độ nghiêm túc tương đương mọi dự án khác có ảnh hưởng đến nhiều bộ phận. Chẳng hạn, không ai triển khai một hệ thống email hoàn toàn mới cho cả doanh nghiệp như thể đó là một dự án thử nghiệm nhỏ, hoạt động âm thầm. Việc đó đòi hỏi sự cộng tác và điều phối của nhiều người. CD và DevOps cũng vậy.

## Một dự án thay đổi doanh nghiệp đúng nghĩa

*Nguyên tác: A business change project in its own right*

Gọi việc triển khai CD và DevOps là một dự án thay đổi doanh nghiệp có thể nghe hơi khô khan, nhưng đó chính xác là bản chất của nó: bạn đang thay đổi cách toàn bộ doanh nghiệp vận hành theo hướng tốt hơn. Đây tuyệt đối không phải việc có thể xem nhẹ. Nếu từng tham gia dự án thay đổi doanh nghiệp, bạn sẽ hiểu phạm vi ảnh hưởng của nó có thể rộng đến mức nào.

Khả năng cao là phần còn lại của doanh nghiệp chưa hiểu điều này sâu như bạn. Họ đã tham gia điều tra, xác nhận các phát hiện và thấy kế hoạch xử lý vấn đề. Nhưng có thể họ chưa hiểu hết hệ quả của việc triển khai CD và DevOps. Ở cấp độ doanh nghiệp, đây có thể là một biến cố làm thay đổi hoàn toàn cách làm việc.

Phần sau của sách sẽ trình bày một số rào cản trong quá trình triển khai. Nếu được cảnh báo ngay từ đầu, bạn sẽ ở vị trí tốt hơn nhiều để vượt qua chúng.

Bạn cần làm cho doanh nghiệp nhận ra rằng dự án sẽ tác động đến khá nhiều người, dù theo hướng tích cực. Quy trình và cách làm việc đều thay đổi. Đây không chỉ là thay đổi quy trình chuyển giao phần mềm; CD và DevOps sẽ thay đổi cách doanh nghiệp suy nghĩ, lập kế hoạch và vận hành.

Giả sử các nhóm marketing và quản lý chương trình đang làm việc theo chu kỳ từ ba đến sáu tháng để đưa tính năng ra thị trường. Nếu việc triển khai thành công, họ phải hiểu rằng một tính năng có thể chạy thật chỉ sau vài ngày. Vì thế, họ cần làm việc theo một nhịp độ khác, tăng tốc và tinh gọn quy trình của chính mình.

Theo kinh nghiệm của tác giả, loại thay đổi này còn mang lại một lợi ích ngoài dự kiến: lòng tin trong toàn doanh nghiệp được khôi phục. Khi R&D và Operations nói sẽ chuyển giao một thứ, họ thực sự chuyển giao nó. Kế hoạch B truyền thống không còn cần thiết - các kế hoạch C, D hay E cũng vậy. Cách chuyển giao tính năng sẽ thay đổi mạnh mẽ; phần còn lại của doanh nghiệp phải chấp nhận và sẵn sàng cho điều đó.

Ở giai đoạn đầu, doanh nghiệp có thể cho rằng tác động của CD và DevOps, đúng như tên gọi, chỉ giới hạn ở hai nhóm Development và Operations:

> **Cách doanh nghiệp nhìn nhận ở giai đoạn đầu**
>
> DevOps = Development + Operations

Ban đầu, cách nhìn này có thể không quá xa sự thật. Bạn có thể khởi đầu ở quy mô nhỏ để nắm các điểm tinh tế và làm quen. Điều đó hoàn toàn ổn. Nhưng khi đã có đà - thường không mất nhiều thời gian - mọi thứ sẽ thay đổi rất nhanh. Nếu mọi người chưa sẵn sàng, hoặc ít nhất chưa nhận thức được, bạn có thể gặp những rào cản làm chậm, thậm chí chặn đứng quá trình triển khai.

Doanh nghiệp phải chấp nhận rằng tác động thực tế sẽ rộng hơn nhiều:

> **Cách doanh nghiệp nên nhìn nhận về các khu vực chịu tác động và tham gia**
>
> DevOps = Marketing + Planning + Development + Operations + Sales + HR

Giờ hãy giả định doanh nghiệp đã đồng thuận về phạm vi rộng của quá trình triển khai và gần như mọi người đều ủng hộ dự án. Thử thách tiếp theo là tập hợp một nhóm chuyên trách để thực sự biến mục tiêu và tầm nhìn thành hiện thực.

> **Chú thích:** Hai sơ đồ không nói rằng mọi nhân viên Marketing, Sales hay HR đều phải trở thành kỹ sư DevOps. Ý của chúng là dòng giá trị phần mềm tác động đến nhiều bộ phận: Marketing phải thay đổi lịch ra mắt; Sales cần biết tính năng có thể xuất hiện nhanh hơn; HR phải hỗ trợ vai trò và cách đánh giá mới.
>
> **Ví dụ:** Khi chu kỳ phát hành giảm từ ba tháng xuống một tuần, Marketing không thể tiếp tục chuẩn bị chiến dịch theo lịch quý cố định, còn bộ phận hỗ trợ khách hàng phải nhận thông tin thay đổi thường xuyên hơn. Chỉ tối ưu Dev và Ops sẽ không đủ.

## Lợi ích của một nhóm chuyên trách

*Nguyên tác: The benefits of a dedicated team*

Giống mọi dự án có mức độ chú ý cao, việc phân bổ nguồn lực chuyên trách để tập trung thực thi dự án CD và DevOps luôn đáng làm.

Người ta thường bị cám dỗ vận hành một số dự án theo kiểu **skunk works**: dự án âm thầm chạy ở hậu trường, thường do những người cùng chí hướng và có hứng thú tham gia, nhưng không có sự hậu thuẫn cần thiết để tạo ra thay đổi lớn, cũng không có thời gian rảnh để toàn tâm cho dự án. Đôi khi những dự án này đơn giản là mờ dần rồi biến mất, bởi các dự án được xem là quan trọng hơn - có tài trợ và sự công nhận chính thức rộng rãi - giành lấy sự chú ý và, quan trọng hơn, nguồn lực.

Muốn việc triển khai CD và DevOps thực sự hiệu quả, bạn cần một nhóm chuyên trách. Lý tưởng nhất, nhóm gồm những người cùng quyết tâm đến từ nhiều khu vực trong doanh nghiệp, chứ không chỉ lập trình viên và nhân sự Operations. Họ phải nhiệt huyết với chủ đề và quyết tâm làm cho nó vận hành.

Làm việc cùng địa điểm là tốt nhất, nhưng không phải lúc nào cũng khả thi. Nếu các nhóm phân bố ở nhiều khu vực địa lý, có thể bố trí một số thành viên làm đại diện tại chỗ. Công việc toàn thời gian của họ là làm mọi điều cần thiết để triển khai thành công mục tiêu đã được xác lập và chấp thuận.

Chỉ tuyển một nhóm hoàn toàn mới từ bên ngoài doanh nghiệp có thể không phải ý hay, vì họ thiếu kiến thức miền nghiệp vụ và chưa có quan hệ với phần còn lại của tổ chức. Nói đơn giản, bạn cần tạm điều chuyển một số nhân sự then chốt ra khỏi công việc thường ngày trong vài tháng để chỉ tập trung triển khai CD và DevOps.

Ngay khi nêu yêu cầu này, một số khu vực trong doanh nghiệp chắc chắn sẽ giảm mức độ ủng hộ, đặc biệt là những nơi đang quản lý chính những người bạn muốn biệt phái sang dự án. Khi đó, bạn phải thuyết phục, nài nỉ, thương lượng và trao đổi để có được người mình cần.

Thành thật mà nói, việc này không nên quá khó, bởi bạn có một lượng lớn "đạn dược": chính thông tin và dữ liệu đã dày công thu thập, được doanh nghiệp đồng ý là nguyên nhân gây đau đớn. Nếu đã thực hiện lập bản đồ dòng giá trị, bạn còn có thể chỉ ra chính xác khu vực gây đau.

Tác giả minh họa bằng cuộc trao đổi điển hình giữa bạn và trưởng bộ phận kiểm thử và QA, gọi là Chucky:

> **Bạn:** Chucky, tôi thực sự cần Steve làm cùng tôi trong dự án CD. Anh ấy hiểu tường tận bộ phận của anh và qua những lần trao đổi, tôi thấy anh ấy rất nhiệt tình, thực sự muốn giải quyết vấn đề.
>
> **Chucky:** Tôi không phản đối. Có lẽ một hoặc hai ngày trong mỗi sprint là được.
>
> **Bạn:** Tôi đang nghĩ đến việc tham gia toàn thời gian trong suốt dự án.
>
> **Chucky:** Tôi không chắc. Anh ấy là nguồn lực thiết yếu và là người duy nhất xử lý được tình huống khi lập trình viên giao mã có rất nhiều lỗi, mà thường lại còn trễ. Chúng ta đang nói đến bao nhiêu sprint?
>
> **Bạn:** Có vẻ khoảng chín đến mười tháng.
>
> **Chucky:** Không thể nào. Anh đã thấy chúng tôi phải chịu đựng thế nào rồi. Không có Steve, chúng tôi sẽ tê liệt.
>
> **Bạn:** Nhưng nếu Steve có thể sửa quy trình phát triển và giúp họ triển khai kỹ thuật phát triển hướng kiểm thử, cùng mọi nội dung khác mà chúng ta đang xem xét, chất lượng mã sẽ cao hơn bất kỳ lúc nào trước đây. Mã cũng sẽ đến thành từng phần nhỏ, dễ kiểm thử. Thêm nữa, chúng ta đã có một số người bên Operations tham gia, nên mọi vấn đề môi trường mà chính nhóm của anh nêu ra sẽ được xử lý. Nhìn chung, để Steve tham gia dự án sẽ giúp anh và nhóm nhiều hơn là giữ anh ấy mắc kẹt ở một vị trí không thể thay đổi hay tác động đến điều gì.
>
> **Chucky:** Nếu nói như vậy thì lựa chọn đã quá rõ. Khi nào anh muốn anh ấy bắt đầu?

Tác giả thừa nhận cuộc trò chuyện thực tế có thể không diễn ra chính xác như vậy, nhưng luận điểm rất rõ: bạn đã có cái nhìn cụ thể về những điều đang làm doanh nghiệp đau đớn và được giao nhiệm vụ loại bỏ chúng. Doanh nghiệp phải hiểu rằng việc đó có chi phí và phải cung cấp cho bạn những gì cần thiết để hoàn thành công việc.

Thành phần nhóm phụ thuộc vào cơ cấu doanh nghiệp. Một doanh nghiệp vừa và nhỏ điển hình thường có các nhóm Development, QA, Operations và Change Management tham gia quy trình phát hành phần mềm; vì vậy, nhóm chuyên trách nên có đại diện từ mỗi khu vực.

Thêm một Scrum Master hoặc Project Manager - trong ví dụ là bạn - một Product Owner, và một Senior Manager làm nhà tài trợ dự án, đại diện cho nhóm ở cấp cao hơn, ta có "đội hình trong mơ" như sau:

| Tầng vai trò | Thành phần |
|---|---|
| Nhà tài trợ và đại diện cấp cao | Senior Manager |
| Điều phối và định hướng giá trị | Scrum Master; Product Owner |
| Chuyên môn thực thi | Lập trình viên; Kiểm thử viên; Nhân sự kiểm soát thay đổi; Operations |

Tất nhiên, mọi thứ còn tùy cách tổ chức doanh nghiệp. Dù vậy, để có uy tín, một nhóm chuyên trách phải gồm nhiều thành phần hơn là chỉ các lập trình viên.

> **Chú thích:** *Skunk works* là một nhóm nhỏ, tương đối tự chủ, làm thử nghiệm hoặc đổi mới bên ngoài quy trình chính thức. Mô hình này có thể rất hiệu quả để chứng minh ý tưởng, nhưng dễ thất bại khi cần thay đổi trên toàn doanh nghiệp mà không có quyền hạn, ngân sách hoặc nguồn lực chính thức.
>
> **Ví dụ:** Hai kỹ sư tự làm pipeline vào buổi tối có thể tạo bản mẫu tốt. Nhưng để thay đổi quy trình phê duyệt, môi trường production, kiểm soát rủi ro và cách đánh giá nhân sự, họ cần một nhóm được chính thức trao quyền với đại diện từ các bộ phận liên quan.

Giờ đây, khi đã nắm nghệ thuật thuyết phục và tạo ảnh hưởng tích cực, bạn cùng nhóm mới thành lập cần học nghệ thuật truyền bá.

## Tầm quan trọng của việc truyền bá

*Nguyên tác: The importance of evangelism*

Liên tục truyền bá một ý tưởng trên toàn doanh nghiệp đòi hỏi công sức và quyết tâm. Nó cũng tốn năng lượng - thực ra là **rất nhiều** năng lượng. Đối tượng của bạn rộng khắp, từ lãnh đạo cấp cao đến nhân viên trực tiếp làm việc, nên bạn và nhóm sẽ phải dành khá nhiều thời gian để truyền tải thông điệp.

Trước khi bàn cụ thể phải nói gì, với ai, khi nào và bằng cách nào, hãy thống nhất các nguyên tắc nền tảng:

- Muốn thuyết phục người khác về giá trị của CD và DevOps, bạn và nhóm mới phải tin tưởng 100%. Nếu chính bạn không tin, làm sao có thể mong người khác tin?
- Bạn và nhóm, hoặc bất kỳ ai tham gia dự án, phải làm đúng điều mình cổ vũ và nêu gương. Nếu xây công cụ trong dự án, hãy xây dựng và triển khai chúng bằng chính kỹ thuật và công cụ mà bạn đang khuyến khích người khác sử dụng.
- Ban đầu, nhiều người sẽ không hiểu. Nhóm phải cực kỳ kiên nhẫn; có thể phải giải thích cùng một điều cho cùng một người nhiều lần. Hãy xem những người này như thước đo: nếu cuối cùng họ hiểu CD và DevOps là gì, khả năng cao thông điệp của bạn đã đúng.
- Hãy nhớ đối tượng và điều chỉnh thông điệp cho phù hợp. Lập trình viên muốn nghe nội dung kỹ thuật mới mẻ; người vận hành hệ thống quan tâm đến ổn định và khả năng dự đoán; cấp quản lý muốn nghe về hiệu suất, tối ưu quy trình và giảm rủi ro. Đây chỉ là khái quát, nhưng có một quy tắc thực tế: nếu thấy mắt người nghe bắt đầu mất tập trung, thông điệp chưa chạm đúng chỗ - hãy thay đổi nó.
- Có người đơn giản không muốn biết hoặc không muốn lắng nghe. Có thể không đáng dồn quá nhiều công sức để ép họ; phần sau của sách sẽ nói thêm. Nếu thuyết phục được họ thì rất đáng ghi nhận, nhưng đừng nản lòng chỉ vì một hai người chậm thay đổi.
- Giữ thông điệp liên quan và nhất quán. Bạn đã có ngôn ngữ chuẩn hóa, mục tiêu và tầm nhìn; hãy dùng chúng.
- Đừng tự bịa thêm. Chỉ cam kết đúng những gì có thể chuyển giao trong phạm vi mục tiêu và tầm nhìn, không hơn, không kém. Ý tưởng và đề xuất mới phải được đưa vào backlog để sắp xếp ưu tiên.
- Trong bất kỳ hoàn cảnh nào, đừng bỏ cuộc.

Nói gọn lại, bạn và nhóm phải **nói được, làm được**. Sẽ có rất nhiều hoạt động kết nối, vì vậy hãy sẵn sàng cho rất nhiều cuộc thảo luận. Mạng lưới càng lớn, cơ hội truyền bá càng nhiều. Đừng né tránh những cơ hội này. Hãy dùng chúng để xây dựng quan hệ làm việc tốt trong toàn doanh nghiệp, bởi bạn sẽ cần những quan hệ ấy về sau.

Việc truyền bá có thể đem lại cảm giác xứng đáng. Nếu thực sự tin CD và DevOps là một bước tiến tuyệt vời, bạn sẽ thấy cơ hội trò chuyện về chúng với người khác cũng là một niềm vui nghề nghiệp.

Về cơ bản, truyền bá cũng là PR. Nếu có nhân sự PR - tốt hơn nữa nếu họ là thành viên nhóm - bạn có thể tạo những thứ đơn giản như logo hoặc quà tặng nhỏ: huy hiệu, cốc, tấm lót chuột... Nghe có vẻ hơi thừa, nhưng cũng như mọi hoạt động PR, mục tiêu là truyền tải thông điệp và khiến nó thấm vào môi trường cùng nhận thức của mọi người.

Cho đến đây, có thể tác giả đã vẽ nên một bức tranh hơi quá tươi sáng. Áp dụng CD không phải một chuyến dã ngoại dễ chịu. Mọi người đều phải leo một ngọn đồi khá lớn. Miễn là người tham gia nhận thức được điều đó và có lòng can đảm cùng quyết tâm thành công, mọi việc sẽ tiến triển tốt.

> **Chú thích:** *Evangelism* ở đây không mang nghĩa tôn giáo. Nó chỉ hoạt động chủ động giải thích, tạo hứng thú, xây dựng đồng thuận và duy trì sự ủng hộ đối với một thay đổi.
>
> **Ví dụ:** Với kỹ sư, có thể demo cách pipeline rút thời gian chờ từ hai giờ xuống mười phút. Với quản lý, cùng kết quả đó nên được trình bày dưới dạng lead time ngắn hơn, ít rủi ro phát hành hơn và khả năng phản hồi khách hàng nhanh hơn.

## Lòng can đảm và quyết tâm cần có trong toàn tổ chức

*Nguyên tác: The courage and determination required throughout the organization*

"Can đảm" và "quyết tâm" có thể nghe như những từ quá mạnh, nhưng chúng hoàn toàn chính xác. Sẽ có nhiều thử thách - một số bạn biết trước, một số thì không - tìm cách cản trở tiến độ. Cần có quyết tâm để bảo đảm dự án tiếp tục đi đúng hướng.

Can đảm là cần thiết vì một số thử thách buộc bạn, nhóm và toàn doanh nghiệp đưa ra những quyết định khó khăn, có thể dẫn đến hành động không thể quay lại. ACME Systems phiên bản 2.0 là một ví dụ phù hợp.

Trong giai đoạn đầu áp dụng CD và DevOps, họ chọn một phần nhỏ của nền tảng làm ứng viên phát hành bằng bộ công cụ triển khai và cách làm việc mới. Không may, cùng lúc đó, một đợt phát hành khác theo cách cũ - đóng gói mọi thứ rồi đẩy đi như một lần triển khai khổng lồ - đang gặp rất nhiều khó khăn.

Doanh nghiệp yêu cầu mọi người tập trung bằng mọi giá để hoàn tất đợt phát hành cũ, kể cả dừng các thử nghiệm CD. Nhóm CD không đón nhận yêu cầu này một cách tích cực. Sau một cuộc trao đổi đầy can đảm giữa trưởng nhóm ACME CD và các đồng cấp, họ thống nhất rằng có thể phân bổ nguồn lực cho đợt phát hành đang gặp sự cố, với điều kiện toàn tổ chức đồng ý đây sẽ là lần phát hành big bang cuối cùng và mọi đợt phát hành tương lai sẽ dùng quy trình pipeline CD mới.

Mọi người đã chấp thuận. Kỷ nguyên phát hành big bang kết thúc và kỷ nguyên CD bắt đầu.

Sau khi đợt big bang cuối cùng hoàn tất, toàn bộ nhóm Development và Operations quyết tâm đưa CD vào hoạt động càng sớm càng tốt. Họ đã chịu đủ đau đớn và cần một cách khác - chính xác hơn là một cách tốt hơn.

Họ kiên trì trong vài tháng cho đến khi đợt phát hành đầu tiên bằng công cụ và cách làm việc mới đi vào production, rồi đợt tiếp theo, và cứ thế tiếp diễn. Đến thời điểm này, không còn đường quay lại vì quá nhiều thứ đã thay đổi.

Quyết định ấy đòi hỏi lòng can đảm từ mọi bộ phận. Không có kế hoạch B; nếu cách mới thất bại, họ sẽ không còn cách phát hành phần mềm. Nhận thức rõ điều đó, doanh nghiệp quyết tâm làm cho CD và DevOps bén rễ và trở thành cách làm việc ổn định.

Đây có thể được xem là một trường hợp cực đoan, nhưng nó cho thấy đôi khi lòng can đảm và quyết tâm thực sự rất cần thiết: có ý chí thì sẽ tìm ra cách.

> **Chú thích:** Ví dụ ACME mô tả một quyết định "không đường lui" có rủi ro cao. Không phải tổ chức nào cũng nên sao chép nguyên xi. Trong thực tế, có thể thể hiện quyết tâm bằng cách đặt thời hạn ngừng quy trình cũ, nhưng vẫn duy trì phương án khôi phục an toàn và tiêu chí kiểm chứng rõ ràng.
>
> **Ví dụ:** Một tổ chức tuyên bố từ ngày 1/7, mọi dịch vụ đủ điều kiện phải phát hành qua pipeline mới; quy trình cũ chỉ dùng cho rollback khẩn cấp trong 60 ngày. Cách này tạo động lực chuyển đổi mà không loại bỏ hoàn toàn khả năng phục hồi.

Trước khi rời giai đoạn lập kế hoạch, vẫn còn hai điều cần biết khi chuẩn bị cho hành trình mới: tìm trợ giúp ở đâu và bảo đảm bạn cùng toàn doanh nghiệp hiểu các chi phí liên quan đến triển khai CD và DevOps. Trước hết là chi phí.

### Hiểu rõ chi phí

*Nguyên tác: Understanding the cost*

Cuối cùng, triển khai CD và DevOps sẽ tiết kiệm cho doanh nghiệp khá nhiều tiền. Theo tác giả, đây là một sự thật đơn giản và hiển nhiên. Công sức phát hành phần mềm sẽ giảm mạnh; nguồn lực cần thiết trở nên rất nhỏ so với những đợt big bang lớn; time to market giảm đáng kể; chất lượng tăng mạnh; còn chi phí vận hành doanh nghiệp - số bản sửa lỗi cần thực hiện, hỗ trợ khi hệ thống ngừng hoạt động, tiền phạt do không đạt SLA... - trở nên không đáng kể.

Dù vậy, triển khai CD và DevOps không miễn phí. Có những chi phí doanh nghiệp phải nhận thức rõ:

- Một nhóm chuyên trách được phân công cho dự án CD và DevOps.
- Thay đổi tài liệu quy trình nghiệp vụ hoặc bản đồ quy trình nghiệp vụ.
- Thay đổi các quy trình vận hành chuẩn.
- Thay đổi hệ thống hosting, nếu chuyển sang hạ tầng ảo hóa.
- Điều chỉnh hệ thống quản lý thay đổi để cho phép hoạt động nhanh và gọn nhẹ hơn.
- Tài liệu PR và marketing nội bộ.
- Thuê chuyên gia bên ngoài hỗ trợ.
- Tốc độ ban đầu có thể chậm lại trong lúc cách làm việc mới dần ổn định.

Những chi phí này không nên quá lớn, nhưng vẫn phải được tính đến và lập kế hoạch. Giống mọi dự án, đặc biệt một dự án có tầm ảnh hưởng rộng như CD và DevOps, luôn tồn tại một số chi phí. Nếu doanh nghiệp biết ngay từ đầu, nguy cơ chi phí làm dự án thất bại về sau sẽ được giảm thiểu.

Cũng có những chi phí gián tiếp. Một số người có thể không chấp nhận thay đổi và quyết định rời đi; tổ chức có thể phải chịu chi phí thay thế họ - hoặc có thể quyết định không thay thế.

Như đã nói, ở đầu quá trình chuyển đổi khỏi các đợt phát hành big bang, bạn có thể phải **chậm lại để sau này nhanh hơn**. Nếu đang có thời hạn hợp đồng trong giai đoạn này, việc thương lượng lại có thể là lựa chọn thận trọng.

Sau cuộc điều tra về cách doanh nghiệp vận hành, bạn hiểu tổ chức của mình hơn bất kỳ ai và có thể nhận ra những khoản chi phí khác. Điều quan trọng là đừng phớt lờ chúng.

> **Chú thích:**
>
> - **Time to market:** thời gian từ khi hình thành ý tưởng hoặc nhu cầu đến khi sản phẩm/tính năng có mặt trên thị trường.
> - **SLA (Service Level Agreement):** cam kết mức dịch vụ, chẳng hạn độ sẵn sàng, thời gian phản hồi hoặc thời gian khắc phục.
> - **Chậm lại để nhanh hơn:** dành năng lực trước mắt cho nền tảng, tự động hóa và học tập có thể làm sản lượng tính năng tạm giảm, nhưng giúp tốc độ bền vững tăng về sau.
>
> **Ví dụ:** Trong hai sprint đầu, nhóm dành 30% năng lực để tự động hóa kiểm thử và triển khai nên số feature hoàn thành giảm. Sau đó, thời gian phát hành giảm từ hai ngày xuống 20 phút và số lần làm lại giảm rõ rệt. Chi phí chuyển đổi ban đầu cần được đưa vào kế hoạch thay vì bị xem là "sụt giảm năng suất" bất ngờ.

Bây giờ, hãy xem bạn có thể tìm sự trợ giúp và lời khuyên ở đâu khi cần.

### Tìm lời khuyên từ người khác

*Nguyên tác: Seeking advice from others*

Trước khi lao vào thay đổi toàn bộ cách doanh nghiệp vận hành, bạn nên nghiên cứu hoặc kết nối với những người:

- Đã trải qua quá trình chuyển đổi này nhiều lần.
- Đang ở cùng hoàn cảnh với bạn.

Trên khắp thế giới, số người có kinh nghiệm triển khai - thậm chí định hình - CD và DevOps ngày càng tăng. Một số là chuyên gia toàn thời gian trong lĩnh vực; số khác đơn giản là thành viên của cộng đồng đang lớn mạnh, đã nhìn thấy lợi ích và sẵn lòng giúp người khác nhận ra những điều họ từng chứng kiến và trải nghiệm.

Xin nhắc lại, triển khai CD và DevOps không hề dễ dàng, và đôi khi đứng ở tuyến đầu có thể rất cô độc. Đừng cho rằng mình phải vật lộn một mình. Có những tài liệu tham khảo giá trị - tác giả hy vọng cuốn sách này là một trong số đó - và quan trọng hơn, có nhiều cộng đồng trực tuyến cùng các buổi gặp mặt trực tiếp mà bạn có thể tham gia để nhận trợ giúp.

Câu chuyện và đóng góp của chính bạn cũng có thể truyền cảm hứng cho người khác. Vì vậy, theo đúng tinh thần DevOps, hãy phá bỏ rào cản và tận hưởng những cuộc đối thoại cởi mở, trung thực. Tác giả cung cấp một danh sách tài liệu tham khảo và địa chỉ liên hệ trong phụ lục *Some Useful Info*.

> **Ví dụ:** Một nhóm chuẩn bị áp dụng trunk-based development có thể trao đổi với tổ chức đã chuyển đổi thành công, tham dự cộng đồng kỹ thuật địa phương hoặc mời chuyên gia đánh giá thử pipeline. Mục tiêu là học từ trải nghiệm thực tế, không sao chép máy móc một mô hình bên ngoài.

## Tổng kết

*Nguyên tác: Summary*

Chương này đã trình bày những nội dung sau:

- Việc xác định mục tiêu và tầm nhìn cho dự án CD và DevOps rất quan trọng; chúng phải dễ hiểu và được truyền đạt tốt.
- Bảo đảm mọi người hiểu dự án thực sự nói về điều gì và quen với ngôn ngữ cùng thuật ngữ được sử dụng.
- Dùng các giải pháp cộng tác trực tuyến như blog, diễn đàn và wiki để chia sẻ thông tin, khuyến khích giao tiếp ở mức độ cao hơn.
- Bảo đảm doanh nghiệp hiểu đầy đủ phạm vi của việc triển khai CD và DevOps, đồng thời nhận ra rằng nó sẽ thay đổi doanh nghiệp theo hướng tốt hơn.
- Hiểu vì sao nguồn lực chuyên trách quan trọng và không thể bị xem nhẹ.
- Hiểu vì sao PR hiệu quả, hoạt động truyền bá, lòng can đảm và quyết tâm có ý nghĩa lớn đối với thành công của dự án.
- Có những chi phí rõ ràng và những chi phí khó nhìn thấy; tất cả phải được tính đến trước khi bắt đầu hành trình.
- Có rất nhiều người cùng chí hướng mà bạn có thể tìm đến khi cần.

Sơ đồ cuối chương tóm lược kế hoạch thành những bước nhỏ:

1. Xác định mục tiêu và tầm nhìn.
2. Giao tiếp, giao tiếp và giao tiếp.
3. Dự án sẽ thay đổi doanh nghiệp, vì vậy phải bảo đảm nó được công nhận đúng mức.
4. Thành lập một nhóm chuyên trách.
5. Truyền bá là một hình thức PR tốt.
6. Không phải mọi thứ đều miễn phí.
7. Bạn không đơn độc - theo một nghĩa tích cực.

Bây giờ, bạn đã có một nhóm, một kế hoạch, sự hậu thuẫn, các cách giao tiếp, sự công nhận, một phần ngân sách và một số hỗ trợ. Bạn đã sẵn sàng chuyển sang giai đoạn tiếp theo: triển khai mục tiêu và tầm nhìn.

