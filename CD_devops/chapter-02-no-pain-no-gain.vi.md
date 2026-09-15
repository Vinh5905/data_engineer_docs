# Chương 2: Không gian nan, không thành quả

*Nguyên tác: Chapter 2: No Pain, No Gain - trang 19-32*

> **Ghi chú về tiêu đề:** *No pain, no gain* là một thành ngữ, hàm ý rằng muốn đạt được kết quả đáng kể thì phải sẵn sàng đối mặt với khó khăn và nỗ lực. Trong chương này, "pain" còn chỉ những vấn đề nhức nhối đang tồn tại trong quy trình chuyển giao phần mềm.

Trong Chương 1, *Sự phát triển của một công ty phần mềm*, bạn đã được giới thiệu về ACME Systems và biết cách họ nhận ra rằng quy trình chuyển giao phần mềm của mình có vấn đề - điều này ảnh hưởng nghiêm trọng đến năng lực chuyển giao sản phẩm nói chung. Bạn cũng đã thấy cách họ xử lý những vấn đề ấy, tiến hóa và, sau nhiều nỗ lực cùng một khoảng thời gian đáng kể, áp dụng phương thức làm việc theo Continuous Delivery và DevOps.

Bây giờ, chúng ta sẽ đi sâu vào một số cách giúp nhận diện và hiểu những vấn đề mà bạn đang có - hoặc có thể là không có.

Cũng có thể bạn không gặp vấn đề nào cả: mọi thứ đều vận hành tốt, và tất cả những người tham gia quy trình chuyển giao phần mềm đều làm việc rất hiệu quả, tận tâm và có động lực. Nếu điều đó thực sự đúng, thì có thể bạn thuộc một trong ba trường hợp sau:

- Bạn đã đạt đến trạng thái lý tưởng tuyệt đối trong chuyển giao phần mềm.
- Bạn đang phủ nhận thực tế.
- Bạn chưa hoàn toàn hiểu một quy trình chuyển giao phần mềm thực sự hiệu quả và tinh gọn có thể đạt đến mức nào.

Khả năng cao hơn là bạn đang có một quy trình chuyển giao phần mềm dùng được, nhưng một số khu vực trong quy trình - hoặc một số nhóm hay cá nhân - đang làm mọi thứ chậm lại. Điều này rất có thể không phải do cố ý. Có thể tổ chức phải tuân thủ một số quy tắc và quy định; có thể một số cổng kiểm soát chất lượng là cần thiết; có thể chưa từng có ai đặt câu hỏi vì sao một số việc phải được thực hiện theo cách hiện tại nên mọi người cứ thế tiếp tục; hoặc có thể chưa ai chỉ ra rằng việc phát hành phần mềm thực sự quan trọng đến mức nào.

Vậy hãy giả định rằng bạn thực sự gặp khó khăn trong việc phát hành phần mềm một cách thuận lợi và muốn hiểu nguyên nhân gốc rễ - nhiều khả năng là nhiều nguyên nhân - để làm cho toàn bộ quy trình hiệu quả và tinh gọn hơn. Cách duy nhất để hiểu đầy đủ những vấn đề đang tồn tại là **kiểm tra và thích ứng khi cần**, đúng theo tiền đề căn bản của phần lớn các phương pháp Agile.

> **Chú thích:** *Inspect and adapt* không có nghĩa là chỉ kiểm tra một lần rồi sửa. Đây là vòng lặp liên tục: quan sát thực tế, thu thập dữ liệu, điều chỉnh cách làm, sau đó kiểm tra lại kết quả.
>
> **Ví dụ:** Một nhóm cho rằng khâu kiểm thử làm chậm phát hành. Sau khi đo, họ phát hiện phần lớn thời gian thực ra bị mất vì bản dựng phải chờ môi trường kiểm thử. Giải pháp phù hợp không phải là giảm kiểm thử, mà là tự động hóa việc tạo môi trường.

## Con voi trong phòng

*Nguyên tác: Elephant in the room*

Trước khi bắt đầu cuộc kiểm tra này, tôi muốn quay lại điều căn bản và giới thiệu với bạn về "con voi trong phòng".

Một số người trong chúng ta mắc một chứng bệnh rất thật và đáng lo, gây ảnh hưởng xấu đến đời sống công việc: chứng "mù con voi trong phòng" - hay gọi theo một cái tên mang vẻ y khoa là *Pachyderm in situ vision impairedness*. Chúng ta biết có một vấn đề lớn đang chắn đường, cản trở tiến độ và hiệu suất, nhưng lại chọn cách chấp nhận nó hoặc, tệ hơn, phớt lờ nó. Sau đó, chúng ta nghĩ ra đủ cách khéo léo để đi vòng qua vấn đề và tự thuyết phục rằng đó là một điều tốt. Thậm chí, chúng ta có thể đầu tư rất nhiều công sức, thời gian và tiền bạc để xây dựng những giải pháp chỉ nhằm né tránh nó.

> **Chú thích:** *Pachyderm in situ vision impairedness* là một cụm từ giả-y-khoa mang tính hài hước do tác giả dùng. *Pachyderm* chỉ động vật da dày, ở đây ám chỉ con voi; toàn cụm có thể hiểu vui là "chứng suy giảm thị lực trước con voi đang ở ngay tại chỗ".

Để kéo dài phép ẩn dụ này thêm một chút - xin hãy kiên nhẫn, tôi thực sự có dụng ý - tôi muốn chuyển sang thế giới nghệ thuật. Trong triển lãm *Barely Legal* năm 2006 tại Los Angeles, nghệ sĩ Banksy đã trưng bày một tác phẩm nghệ thuật sống: một con voi Ấn Độ trưởng thành đứng trong một phòng khách dựng tạm có tường in hoa. Toàn thân con voi cũng được sơn cùng kiểu hoa văn đó. Tác phẩm có tên - bạn đoán được chứ? - *Elephant in the Room*.

Thoạt nhìn, cảnh này thật lố bịch: người ta hoàn toàn có thể thấy một con voi khổng lồ nặng khoảng 12.000 pound đang đứng ở đó. Dù nó được sơn để hòa vào khung cảnh xung quanh, nó vẫn hiện diện ngay trước mắt. Và đây chính là điều tác giả muốn nói: những vấn đề trong quy trình chuyển giao phần mềm cũng giống như con voi ấy; việc chúng ta đơn giản phớt lờ sự tồn tại của chúng cũng vô lý chẳng kém.

> **Điểm cần nhớ:** Nhìn chung, nếu thực sự quan sát thì "con voi trong phòng" không khó phát hiện. Nó thường nằm hoặc lẩn khuất ngay nơi mọi người đều có thể nhìn thấy. Bạn chỉ cần biết cách nhìn và biết mình cần tìm điều gì.

Trong phần còn lại của chương, chúng ta sẽ xem xét một số cách làm nổi bật sự tồn tại của con voi trong phòng và, quan trọng hơn, bảo đảm rằng càng nhiều người càng tốt cũng nhìn thấy nó và nhận ra rằng đây không phải là thứ nên né tránh, đi vòng qua hay làm ngơ.

Vậy là mắt bạn đã mở và bạn đã sẵn sàng gột lớp hoa văn khỏi con voi. Nhưng chưa hẳn. Vẫn còn một số điều khác cần cân nhắc trước khi tiếp tục. Bạn cần xác định cách mình sẽ phơi bày con voi, tức là phải đặt ra một số nguyên tắc nền tảng.

> **Ví dụ:** Cả công ty đều biết việc triển khai cuối tuần thường thất bại, nhưng thay vì sửa pipeline, tổ chức lập thêm một nhóm trực đêm để xử lý sự cố. Nhóm trực đêm là một giải pháp "đi vòng qua con voi"; con voi thực sự là quy trình triển khai thiếu tin cậy.

## Các nguyên tắc nền tảng

*Nguyên tác: Ground rules*

Trong bất kỳ hoạt động xem xét, phơi bày, điều tra hay kiểm tra nào, ở một mức độ nhất định, bạn cũng sẽ phải đào lên những chuyện không mấy dễ chịu. Điều này là không thể tránh khỏi và không nên bị xem nhẹ. Những vấn đề có thể xuất hiện dưới dạng các câu hỏi như:

- Vì sao mọi việc lại được thực hiện theo cách này?
- Ai đã quyết định chọn cách này thay vì cách khác?
- Chính xác thì những quyết định ấy được đưa ra khi nào?
- Ai chịu trách nhiệm đối với toàn bộ quy trình chuyển giao sản phẩm?
- Ai chịu trách nhiệm đối với từng bước trong quy trình?
- Động lực hoặc yếu tố nào đã dẫn đến một số quyết định?
- Trước đây đã có ai chất vấn quy trình chưa, và chuyện gì đã xảy ra?
- Vì sao ban quản lý không lắng nghe chúng ta?
- Các bên liên quan là những ai?

Việc "đào bới" này có thể khiến một số người không thoải mái và có thể làm lộ ra những điều gây phản ứng cảm xúc, đặc biệt ở những người từng tham gia thiết kế hoặc triển khai chính quy trình chuyển giao phần mềm đang được xem xét. Không chỉ vậy, bạn còn có thể cần chính những người ấy tham gia sâu vào việc thay thế hoặc cải tiến quy trình. Ngay cả khi họ nhìn thấy và hiểu rằng quy trình mình từng nuôi dưỡng đã hỏng và cần được tái thiết kế, họ vẫn có thể gắn bó về mặt cảm xúc với nó, nhất là khi đã tham gia trong thời gian dài.

### Cuộc điều tra này không phải là gì?

- Một cuộc săn phù thủy nhắm vào cá nhân.
- Một buổi "khám nghiệm tử thi" để truy cứu người gây ra thất bại.
- Một cách đổ lỗi hoặc khiến người khác cảm thấy tội lỗi.
- Một diễn đàn chính trị để trục lợi cá nhân hoặc đề cao cái tôi.
- Một chiến dịch bôi nhọ.
- Việc công khai chuyện nội bộ xấu xí trước thiên hạ.
- Một hoạt động mang tính tiêu cực.

> **Chú thích:** Tác giả dùng từ *post mortem* theo nghĩa một cuộc phân tích sau sự cố chỉ chăm chăm mổ xẻ cái đã chết. Trong thực hành hiện đại, một buổi *blameless postmortem* - phân tích sự cố không đổ lỗi - vẫn là kỹ thuật rất hữu ích. Điều bị phản đối ở đây là tinh thần truy cứu cá nhân, không phải việc học từ sự cố.
>
> **Ví dụ:** Câu hỏi "Ai viết đoạn mã làm hệ thống sập?" dễ biến cuộc họp thành săn lỗi cá nhân. Câu hỏi tốt hơn là "Vì sao thay đổi này có thể vượt qua review, kiểm thử và cơ chế bảo vệ sản xuất?".

Hồi tưởng có thể là một công cụ mạnh. Nếu dùng sai, nó có thể gây hại nhiều hơn lợi - bạn có thể tự bắn vào chân mình hết lần này đến lần khác. Trước khi bắt tay vào loại hoạt động này, bạn phải chắc chắn rằng mình hiểu điều gì đang chờ đợi.

Không chỉ cách tiến hành điều tra mới quan trọng; việc thiết lập đúng môi trường và duy trì hành vi cởi mở, trung thực xuyên suốt quá trình cũng có ý nghĩa sống còn.

## Cởi mở và trung thực là chìa khóa

*Nguyên tác: Openness and honesty is the key*

Muốn đi đến tận gốc một vấn đề, bạn phải thu thập nhiều thông tin và dữ liệu nhất có thể để mọi người cùng phân tích và thống nhất cách vượt qua hoặc xử lý nó. Nói theo phép ẩn dụ của chương này, bạn phải biết con voi thực sự lớn đến đâu trước khi di chuyển nó ra khỏi đường đi.

Bạn có thể đang cân nhắc tổ chức một cuộc điều tra kín do ban quản lý hoặc một nhóm tư vấn kinh doanh bên ngoài thực hiện. Cách này có thể cung cấp một lượng thông tin và dữ liệu nhất định, nhưng rất có khả năng vẫn bỏ sót điều gì đó. Có người cảm thấy bị đe dọa nên không muốn tiết lộ một thông tin quan trọng, thậm chí không muốn tham gia. Có người quên chi tiết và không có đồng đội bên cạnh để bổ sung phần còn thiếu. Bạn cũng có thể hiểu sai thông tin được cung cấp hoặc tách nó khỏi bối cảnh.

Điều tra kín có thể hiệu quả trong một số tình huống và đôi khi, đặc biệt trong mắt lãnh đạo cấp cao, được xem là cách tốt nhất để kiểm soát tình hình, nhanh chóng thu thập thông tin và ngăn tin đồn lan rộng. Tuy nhiên, điều tra kín cũng là môi trường thuận lợi cho sự mất lòng tin, việc che giấu thông tin, sự thiếu gắn kết và những góc nhìn phiến diện. Vì vậy, tác giả không khuyến nghị kỹ thuật này.

Để thực sự có được lượng thông tin và mức độ tham gia cần thiết, bạn phải tạo ra một môi trường cởi mở, minh bạch, nơi hành vi tích cực và đối thoại trung thực, mang tính xây dựng có thể phát triển. Điều đó không có nghĩa bạn phải làm việc trong một ngôi nhà kính, công khai mọi cuộc trò chuyện và đưa mọi quyết định ra biểu quyết tập thể. Điều cần thiết là **hạn chế tối đa bí mật**.

Nghe có vẻ đơn giản đến thiếu thực tế, thậm chí hơi cực đoan, nhưng nếu thiếu cởi mở, trung thực và minh bạch, mọi người sẽ tiếp tục dè chừng và bạn sẽ không thu được tất cả sự thật cần thiết. Bạn cần một môi trường nơi bất kỳ ai cũng cảm thấy có thể nói ra suy nghĩ của mình và, quan trọng hơn, có thể đóng góp. Bề ngoài, điều này tưởng như dễ đạt được; trên thực tế, nó có thể rất khó, nhất là khi liên quan đến cấp quản lý.

Những việc bạn cần làm bao gồm:

- Thuyết phục cấp quản lý rằng đây là việc đáng làm.
- Để họ đồng ý cho nhiều người tạm dừng công việc thường ngày trong vài giờ để tham gia. Số lượng có thể rất lớn; chương này sẽ đề cập thêm ở phần sau.
- Để họ đồng ý không cố điều khiển chương trình nghị sự.
- Đề nghị họ cởi mở và trung thực trong một nhóm đồng nghiệp rộng hơn.
- Bảo đảm họ cho phép cấp dưới nói chuyện cởi mở và trung thực mà không sợ bị trả đũa.
- Đề nghị họ đứng ngoài trong lúc bạn làm việc với những người thực sự biết chuyện gì đang xảy ra và vấn đề nằm ở đâu.
- Khiến họ tin tưởng bạn.

Như bạn có thể hình dung, bạn sẽ phải thực hiện nhiều cuộc trao đổi tế nhị với nhiều người ở khắp doanh nghiệp.

Ngoài thách thức thuyết phục ban quản lý, nếu không có điều kiện để các nhóm làm việc cùng một địa điểm, bạn còn phải đưa những người ở xa vào một hoạt động mang tính tương tác và quan hệ giữa người với người rất cao. Có thể thử một số cách sau:

- Đưa thành viên của các nhóm ở xa đến chỗ bạn, nếu ngân sách cho phép.
- Đưa nhóm tại chỗ đến chỗ họ, cũng nếu ngân sách cho phép.
- Dùng hội nghị truyền hình; chỉ hội nghị thoại là chưa đủ đối với loại hoạt động này.
- Nếu có chênh lệch múi giờ, chọn thời điểm mà phần lớn mọi người có thể tham gia hoặc tổ chức các phiên riêng biệt, dù đây không phải phương án lý tưởng.

Như vậy, trước khi bắt đầu thử thách phơi bày con voi trong phòng, bạn phải làm khá nhiều công việc chuẩn bị. Giả sử bạn đã vượt qua mọi khó khăn để tập hợp mọi người tại cùng một nơi, được ban quản lý bật đèn xanh, thống nhất thời gian tạm dừng công việc và thiết lập một môi trường an toàn. Bạn gần như đã sẵn sàng - nhưng vẫn còn một câu hỏi: nên mời bao nhiêu người tham gia cuộc kiểm tra, và ở một mức độ nào đó, những ai không nên tham gia?

> **Chú thích:** "Môi trường an toàn" ở đây chủ yếu là an toàn tâm lý: người tham gia có thể nêu sự thật bất tiện hoặc thừa nhận sai lầm mà không sợ bị trừng phạt, làm nhục hay gây bất lợi nghề nghiệp.
>
> **Ví dụ:** Nếu trưởng bộ phận ngồi trong phòng và phản bác ngay mọi nhận xét của nhân viên, lời tuyên bố "hãy cứ nói thật" gần như vô nghĩa. Người điều phối có thể thống nhất quy tắc không ngắt lời, ẩn danh hóa phiếu góp ý và hoãn phần phản hồi của quản lý đến sau khi đã thu thập xong dữ liệu.

## Bao gồm (gần như) tất cả mọi người

*Nguyên tác: Include (almost) everyone*

Dù có ý định tốt và muốn mọi người liên quan đến quy trình chuyển giao phần mềm đều tham gia kiểm tra, điều đó vừa không thực tế vừa khó khả thi - đơn giản là có thể không đủ chỗ. Điều bạn thực sự cần là những cá nhân có thể đóng góp tích cực, có sự gắn kết và muốn thay đổi mọi thứ, hoặc ít nhất muốn chứng kiến mọi thứ trở nên tốt hơn.

Những người đóng góp này nên đến từ mọi bộ phận trong doanh nghiệp. Nếu họ tham gia vòng đời tạo ra và chuyển giao sản phẩm thì họ nên được đại diện. Bạn cần một tập hợp thông tin và dữ liệu đủ rộng để tiến về phía trước. Hai từ khóa ở đây là **gắn kết** và **đóng góp**.

Có thể có những người mang bất mãn cá nhân hoặc chỉ cần một diễn đàn để phát biểu ý kiến riêng. Điều đó không có gì sai, nhưng nếu không được quản lý tốt thì sẽ thiếu tính xây dựng và có thể làm chệch hướng quá trình điều tra. Không nên gạt họ đi ngay lập tức, vì họ có thể đưa ra những quan sát có giá trị; việc bác bỏ họ còn có thể nuôi dưỡng thêm thái độ tiêu cực. Tuy nhiên, bạn phải bảo đảm họ thực sự tham gia và hiểu các nguyên tắc nền tảng. Có thể bạn cần để mắt đến họ, giống như để ý những đứa trẻ nghịch ngợm trong lớp; dù vậy, bạn có thể bất ngờ trước giá trị mà họ mang lại.

Một số người có thể quá bận hoặc đơn giản là chưa nhận ra hoạt động này quan trọng đến mức nào. Nhiệm vụ của bạn là khuyến khích những nhân vật then chốt trong nhóm này tham gia. Nếu họ thực sự quan trọng, đôi khi nói rõ điều đó cũng hữu ích; một chút khích lệ cái tôi có thể giúp thuyết phục họ.

Bạn cũng có thể gặp một "vấn đề tích cực": có quá nhiều người muốn tham gia điều tra. Xét ở vài khía cạnh, đây là một điều tốt; quá nhiều người đăng ký vẫn là một vấn đề dễ chịu. Khi đó, bạn có thể cân nhắc tổ chức nhiều cuộc điều tra. Không nên cố điều hành một phiên tương tác với quá đông người, vì phiên họp sẽ khó kiểm soát và quá nhiều tiếng nói có thể làm tăng nhiễu thay vì tạo thêm thông tin có giá trị.

Nhìn chung, bạn cần sự đại diện tốt từ khắp doanh nghiệp. Thông thường, danh sách sẽ gồm một phần hoặc toàn bộ các vai trò sau; đây chỉ là danh sách khái quát và có thể không phản ánh đúng cơ cấu tổ chức của bạn:

- Chủ sở hữu hoặc quản lý sản phẩm.
- Quản lý chương trình.
- Lập trình viên.
- Trưởng nhóm.
- Quản trị viên hệ thống.
- Quản trị viên cơ sở dữ liệu.
- Kiểm thử viên, nhân sự QA và QC.
- Chuyên viên phân tích nghiệp vụ.
- Scrum Master.
- Nhân sự kiểm soát thay đổi.
- Quản lý phát hành.
- Quản trị viên SCM.

Nói đơn giản, bạn cần thu hút bất kỳ ai đang trực tiếp tham gia việc xác định, xây dựng, kiểm thử, đưa đi và hỗ trợ phần mềm trong tổ chức. Tấm lưới được giăng càng rộng thì, nói theo nghĩa bóng, lượng thông tin và dữ liệu liên quan bạn thu được càng nhiều.

> **Chú thích:**
>
> - **QA (Quality Assurance):** bảo đảm chất lượng bằng cách cải thiện và kiểm soát quy trình.
> - **QC (Quality Control):** kiểm soát chất lượng của sản phẩm đầu ra, thường thông qua kiểm tra và phát hiện lỗi.
> - **SCM (Software Configuration Management):** quản lý cấu hình phần mềm, bao gồm phiên bản, bản dựng, thay đổi và các thành phần cấu thành sản phẩm.

Trong chương này, bạn đã gặp những cụm từ như "điều tra", "phiên tương tác giữa người với người", "phơi bày con voi" và "hồi tưởng". Về cơ bản, tất cả đều chỉ cùng một việc: thu thập thông tin và dữ liệu về cách quy trình chuyển giao sản phẩm đầu-cuối thực sự vận hành, từ đó làm nổi bật những vấn đề cần sửa chữa.

Bây giờ, chúng ta sẽ chuyển sang một số cách thu thập thông tin và dữ liệu ấy.

## Một số kỹ thuật đã được thử nghiệm và kiểm chứng

*Nguyên tác: Some tried and tested techniques*

Danh sách sau hoàn toàn không đầy đủ; nó chỉ tập hợp một số công cụ và kỹ thuật có thể hữu ích. ACME Systems phiên bản 2.0 đã sử dụng một số kỹ thuật này rất hiệu quả để lột mặt nạ "con voi" của họ.

Như đã nói, thu thập thông tin và dữ liệu bằng một phương pháp cởi mở, tương tác tốt hơn nhiều so với một phiên họp kín. Các phiên mở có tính gắn kết và cộng tác cao hơn, đồng thời cũng thú vị hơn. Trên thực tế, để tổ chức một phiên như vậy, bạn chỉ cần bộ công cụ quen thuộc của người thực hành Agile: một bức tường trống lớn được phủ giấy hoặc một bảng trắng lớn, giấy ghi chú, bút, các miếng dán nhiều màu, một khoảng không gian và một chút kiên nhẫn.

Phần còn lại của chương sẽ dùng một số thuật ngữ Agile. Dưới đây là danh sách đơn giản dành cho những người chưa thật quen thuộc:

| Thuật ngữ | Ý nghĩa |
|---|---|
| **Product Owner** | Đại diện kinh doanh chính, người diễn giải các yêu cầu và truyền đạt chúng cho nhóm. |
| **Scrum Master** | Người chịu trách nhiệm duy trì quy trình hoặc các quy trình Agile và sức khỏe tổng thể của nhóm. |
| **Feature** | Một yêu cầu nghiệp vụ cấp cao. Ví dụ: một trang web mới cho phép người dùng đăng nhập. |
| **Story** | Một yêu cầu hoặc feature đã được chia nhỏ đến mức có thể ước lượng thực tế và xác định tiêu chí chấp nhận. Ví dụ: một ô đăng nhập mới để người dùng nhập thông tin. |
| **Backlog** | Tập hợp các story sẽ được thực hiện vào một thời điểm nào đó trong tương lai. |
| **Task** | Thường là một phần nhỏ của story, gồm từng công việc cần thiết để đáp ứng yêu cầu của story. Ví dụ: viết một hàm JavaScript kiểm tra khoảng trắng trong tên người dùng. |
| **Time-boxed** | Một khoảng thời gian có độ dài cố định được dành cho một mục tiêu cụ thể. Cách làm này được dùng trong phát triển Agile và cũng có thể áp dụng cho hoạt động khác, chẳng hạn retrospective. |

> **Chú thích:** Cách dùng thuật ngữ có thể khác giữa các tổ chức. Chẳng hạn, có nơi xem *feature* lớn hơn *story*, còn nơi khác dùng hai từ gần như đồng nghĩa. Điểm quan trọng là cả nhóm thống nhất một cách hiểu.

Các công cụ và kỹ thuật tiếp theo sẽ hỗ trợ cuộc điều tra theo những cách khác nhau. Chúng không được xếp theo thứ tự ưu tiên và cũng không tạo thành một danh sách đầy đủ. Chúng ta bắt đầu với lập bản đồ dòng giá trị.

### Lập bản đồ dòng giá trị

*Nguyên tác: Value stream mapping*

Kỹ thuật Lean này bắt nguồn từ sản xuất - cũng như khá nhiều phương pháp và công cụ Agile khác - và đã tồn tại dưới nhiều hình thức trong nhiều năm. Có thể bạn từng nghe đến nó trong bối cảnh Kanban, một phương pháp chuyển giao phần mềm Agile dựa trên luồng, tương tự Scrum; cuốn sách sẽ đề cập đến Kanban ở phần sau.

Giống mọi phương pháp, công cụ hoặc kỹ thuật Lean, lập bản đồ dòng giá trị xoay quanh mô hình **giá trị so với lãng phí**. Về bản chất, bản đồ dòng giá trị là cách chia quy trình chuyển giao sản phẩm thành một chuỗi bước và điểm bàn giao. Nó cũng có thể được dùng để tính mức hiệu suất. Toàn bộ bản đồ được trình bày và phân tích để xác định nơi xuất hiện nút thắt hoặc chậm trễ trong luồng - nói cách khác, xác định những bước không tạo thêm giá trị.

Chỉ số then chốt trong lập bản đồ dòng giá trị là **lead time**, chẳng hạn khoảng thời gian từ lúc ý tưởng ban đầu xuất hiện đến khi nó bắt đầu tạo ra tiền thật cho doanh nghiệp.

> **Chú thích:**
>
> - **Value (giá trị):** hoạt động trực tiếp biến sản phẩm theo hướng khách hàng cần và sẵn sàng trả tiền.
> - **Waste (lãng phí):** thời gian hoặc công sức không tạo ra giá trị, chẳng hạn chờ phê duyệt, bàn giao lặp lại, làm lại do lỗi.
> - **Lead time:** tổng thời gian từ lúc có yêu cầu đến khi giá trị đến tay khách hàng. Nó bao gồm cả thời gian làm việc lẫn thời gian chờ.
>
> **Ví dụ:** Sửa một lỗi chỉ cần hai giờ lập trình nhưng phải chờ tám ngày để được xếp lịch, phê duyệt và triển khai. Thời gian xử lý là hai giờ, nhưng lead time là hơn tám ngày. Tối ưu riêng tốc độ viết mã gần như không cải thiện trải nghiệm của khách hàng.

Có rất nhiều tài nguyên trình bày chi tiết cách xây dựng bản đồ dòng giá trị, đồng thời cũng có nhiều chuyên gia trong lĩnh vực này nếu bạn cần hỗ trợ.

Để xây dựng bản đồ hiệu quả, bạn cần những người đến từ nhiều khu vực của doanh nghiệp, có hiểu biết rất rõ và tốt nhất là kinh nghiệm thực tế đối với từng giai đoạn của quy trình chuyển giao sản phẩm - đôi khi còn gọi là vòng đời sản phẩm.

Lý tưởng nhất, bản đồ dòng giá trị nên đại diện cho một quy trình nghiệp vụ. Tuy nhiên, lúc đầu việc đó có thể quá đáng ngại và rối rắm. Để giữ mọi thứ đơn giản, sẽ hữu ích hơn nếu chọn một dự án hoặc đợt phát hành gần đây làm ví dụ rồi phân rã nó.

Ví dụ, hãy xem luồng của một yêu cầu tính năng đơn lẻ được ACME Systems phiên bản 2.0 chuyển giao trước khi họ "nhìn thấy ánh sáng":

| # | Bước trong quy trình | Thời gian làm việc | Thời gian chờ đến bước sau |
|---:|---|---:|---:|
| 1 | Nhận yêu cầu tính năng mới từ khách hàng | - | 10 ngày |
| 2 | Product Owner thêm yêu cầu vào backlog | 1 giờ | 5 ngày |
| 3 | Product Owner sắp xếp mức ưu tiên | 2 giờ | 5 ngày |
| 4 | Ước lượng tính năng và chia nhỏ thành story | 2 giờ | 3 ngày |
| 5 | Thiết kế hệ thống và tinh chỉnh story | 2 ngày | 2 ngày |
| 6 | Lập lịch: quản lý dự án cập nhật kế hoạch phát hành; quản lý QA và phát triển phân bổ nguồn lực | 1 ngày | 5 ngày |
| 7 | Chuẩn bị phát triển: dựng môi trường phát triển và QA; cập nhật kế hoạch kiểm thử | 2 ngày | 10 ngày |
| 8 | Phát triển: lập trình, viết tài liệu và kiểm thử | 10 ngày | 5 ngày |
| 9 | Chuẩn bị phát hành: tạo ghi chú phát hành, kết quả kiểm thử và hồ sơ mẫu | 2 ngày | 5 ngày |
| 10 | Xin phê duyệt | 5 ngày | 2 ngày |
| 11 | Tạo bản phát hành | 1 ngày | 5 ngày |
| 12 | Nghiệm thu người dùng đối với bản phát hành thử | 5 ngày | 2 ngày |
| 13 | Quyết định phát hành hay không phát hành (GO/NO GO) | 2 ngày | 5 ngày |
| 14 | Phần mềm được phát hành | 1 ngày | 1 ngày |
| 15 | Khách hàng bắt đầu sử dụng tính năng | - | - |

Mỗi ô trong hình gốc đại diện cho một bước của toàn bộ quy trình. Thời lượng trong ô là **thời gian làm việc**, tức thời gian cần để thực hiện bước đó. Thời lượng trên mũi tên là **thời gian chờ** giữa hai bước.

Đây là một cái nhìn tổng quan rất đơn giản, nhưng nó cho thấy ngay cả một yêu cầu hết sức đơn giản cũng có thể mất bao lâu mới được đưa đến khách hàng. Nó cũng làm lộ rõ lượng lãng phí trong quy trình. Mỗi bước đều có chi phí, mỗi lần trì hoãn đều có chi phí và mỗi sai sót đều có chi phí. Giá trị thực sự chỉ xuất hiện khi khách hàng sử dụng phần mềm. Nếu phải chờ một tính năng quá lâu, khách hàng có thể chán nản và tìm đến nơi khác.

Thoạt nhìn, tạo loại bản đồ này có vẻ đơn giản, nhưng quá trình thực hiện có thể rất khó. Sơ đồ đơn giản ấy được dựng theo thời gian thực với dữ liệu từ nhiều khu vực khác nhau của doanh nghiệp. Sẽ có rất nhiều cuộc đối thoại và tranh luận cởi mở, trung thực trong lúc mọi người xác minh sự kiện, gợi lại ký ức, đối chiếu ngày giờ, làm rõ ví dụ và đi đến thống nhất về điều thực sự đang xảy ra.

Nếu muốn dùng thuật ngữ và ký hiệu tiêu chuẩn của bản đồ dòng giá trị, bạn có thể chuyển phiên bản làm bằng giấy ghi chú thành một sơ đồ như sau. Sơ đồ vẫn biểu diễn luồng yêu cầu tính năng đi qua doanh nghiệp:

**Doanh nghiệp -> Lập kế hoạch -> Phát triển -> Kiểm thử -> Tạo bản phát hành -> Phát hành -> Khách hàng**

| Công đoạn | Thời gian tạo giá trị | Thời gian chết/chờ sau công đoạn |
|---|---:|---:|
| Lập kế hoạch | 0,5 ngày | 0 ngày |
| Phát triển | 5 ngày | 2 ngày |
| Kiểm thử | 2 ngày | 10 ngày |
| Tạo bản phát hành | 2 ngày | 30 ngày |
| Phát hành | 1 ngày | - |
| **Tổng** | **10,5 ngày** | **42 ngày** |

Hiệu suất trong hình được tính bằng thời gian tạo giá trị chia cho tổng thời gian của luồng:

`10,5 ngày / 52,5 ngày = 20%`

> **Chú thích:** Hiệu suất 20% không có nghĩa nhân viên chỉ làm việc 20% thời gian. Nó có nghĩa rằng, xét trên hành trình của một yêu cầu, chỉ 20% lead time được dành cho hoạt động tạo giá trị; 80% còn lại là thời gian chết hoặc chờ.

Khi đã có bản đồ dòng giá trị, thách thức tiếp theo là tìm ra và lập kế hoạch xử lý những vấn đề vừa được làm nổi bật. Các chương sau sẽ đề cập chi tiết hơn. Kỹ thuật tiếp theo chúng ta xem xét là retrospective.

### Sử dụng retrospective

*Nguyên tác: Using retrospectives*

Retrospective thường là phần **kiểm tra** trong vòng lặp "kiểm tra và thích ứng" của Agile. Nếu bạn biết hoặc đang dùng Scrum, tổ chức retrospective hẳn không còn mới. Nếu chưa từng thực hiện, bạn sẽ có một số hoạt động khá thú vị để học.

Nhiệm vụ của retrospective là nhìn lại một khoảng thời gian, một dự án, một đợt phát hành hoặc đơn giản là một thay đổi trong doanh nghiệp, rồi làm nổi bật điều gì đã diễn ra tốt và điều gì chưa tốt. Việc này có thể hơi khô khan, nên retrospective thường dựa trên các trò chơi - một số người gọi chúng là bài tập, nhưng tác giả thích từ "trò chơi" hơn. Cách làm này khuyến khích hợp tác, tăng sự tham gia và bổ sung một chút vui vẻ.

Như mọi trò chơi, luôn có các quy tắc phải tuân theo, đặc biệt là quy tắc time-box: phải rất nghiêm ngặt về lượng thời gian dành cho từng phần. Giống kỹ thuật lập bản đồ dòng giá trị, những công cụ duy nhất bạn cần là bút, giấy, bảng trắng hoặc một bức tường và giấy ghi chú.

Có rất nhiều trò chơi retrospective và cũng có rất nhiều nguồn thông tin, tài liệu tham khảo để bạn tìm ý tưởng. Tốt nhất là thử một vài trò và xem trò nào phù hợp với bạn.

Mục tiêu cuối cùng của mọi retrospective là tạo ra các điểm hành động có thể chuyển thành kế hoạch cải tiến. Chương sau sẽ nói rõ hơn về việc này.

Tác giả giới thiệu hai trò chơi yêu thích: **Dòng thời gian** và **StoStaKee**. Trước hết là trò Dòng thời gian.

#### Trò chơi Dòng thời gian

*Nguyên tác: The timeline game*

Đúng như tên gọi, trò chơi xoay quanh việc dựng một dòng thời gian rồi mời những người tham gia xem xét và bình luận về những gì đã xảy ra trong khoảng thời gian được chọn.

Trò chơi có nhiều biến thể, nhưng về cơ bản, toàn nhóm viết các sự kiện đáng chú ý trong giai đoạn đó lên giấy ghi chú và dùng chấm màu để biểu thị cảm xúc mà sự kiện tạo ra:

- Xanh lá = vui.
- Xanh dương = buồn.
- Đỏ = tức giận.

Từ đó, mọi người thảo luận cởi mở và trung thực về những sự kiện tạo ra nhiều cảm xúc nhất, rồi thống nhất các hành động tiếp theo - chẳng hạn điều nên dừng làm, bắt đầu làm và tiếp tục làm.

Retrospective cần được time-box để mọi người giữ tập trung và cuộc thảo luận không đi lạc sang những vấn đề ngoài lề.

Hình trong sách minh họa một bức tường dòng thời gian điển hình. Nội dung các ghi chú được chuyển sang tiếng Việt như sau:

| Mốc trên dòng thời gian | Ví dụ ghi chú của người tham gia |
|---|---|
| 02/01/2012 - Thống nhất ngân sách | "Công việc mới"; "Yêu cầu mơ hồ" |
| 13/01/2012 - Bắt đầu lập kế hoạch | "Chúng ta có công việc mới" |
| 24/01/2012 - Bắt đầu phát triển | "Dự án mới có nhiều thách thức"; "Không phải toàn bộ nhóm phát triển đều sẵn sàng"; "Đặc tả chưa được thống nhất trước khi bắt đầu phát triển" |
| 12/02/2012 - Hoàn thành phát triển | "Thay đổi phạm vi"; "Nhiều đêm làm muộn vì không đủ lập trình viên"; "Máy chủ phát triển liên tục gặp sự cố"; "Thực ra chúng ta chưa hoàn thành"; "Nhóm đã bàn giao" |
| 24/01/2012 - Hoàn thành UAT | "Một cuối tuần nghỉ ngơi sau nhiều tháng"; "Nỗ lực rất lớn để sửa lỗi"; "Vẫn còn vô số lỗi"; "Rất nhiều lỗi"; "Thêm nhiều ngày/đêm làm việc kéo dài" |
| 03/03/2012 - Thực sự hoàn thành UAT lần này | "Chúng ta làm được rồi!" |
| 13/03/2012 - Lần phát hành số 1 thất bại | "Bản phát hành làm nền tảng production ngừng hoạt động trong 3 giờ"; "Mọi người đều xắn tay vào giúp"; "Đau đớn!!!"; "Ai cần ngủ chứ" |
| 22/03/2012 - Lần phát hành số 2, lần này đã chạy thật | "Nó chạy rồi!"; "Lại thêm một ngày và một đêm dài"; "Cuối cùng cũng xong" |
| 29/03/2012 - Uống bia | "Bia"; "Tại sao lại ăn mừng một thất bại?" |

> **Chú thích của người dịch:** Hình gốc ghi mốc "UAT Complete" là **24/01/2012**, dù nó được đặt sau mốc hoàn thành phát triển ngày 12/02/2012. Đây có vẻ là lỗi ngày tháng trong bản in. Bản dịch giữ nguyên dữ liệu của sách để tránh tự ý sửa nguồn.
>
> **Ví dụ áp dụng:** Với một sự cố production kéo dài ba giờ, nhóm có thể ghi cả sự kiện khách quan ("production ngừng hoạt động ba giờ") lẫn cảm xúc (buồn, tức giận). Khi thảo luận, nhóm không dừng ở việc kể lại sự cố mà phải tạo hành động cụ thể, chẳng hạn bổ sung kiểm thử smoke tự động và cơ chế rollback.

Tiếp theo là trò chơi StoStaKee.

#### StoStaKee

StoStaKee là viết tắt của **Stop, Start, Keep** - **Dừng làm, Bắt đầu làm, Tiếp tục làm**. Đây cũng là một bài tập tương tác có giới hạn thời gian, tập trung vào các sự kiện trong quá khứ.

Lần này, bạn đề nghị mọi người viết giấy ghi chú về những điều họ muốn dừng làm, bắt đầu làm hoặc tiếp tục làm, rồi đặt chúng vào một trong ba cột tương ứng. Sau đó, mọi người bỏ phiếu - cũng bằng các chấm dán - cho những ý kiến họ quan tâm nhất.

Bạn cần khuyến khích thật nhiều cuộc thảo luận cởi mở và mang tính xây dựng để bảo đảm mọi người hiểu ý nghĩa của từng ghi chú. Mục tiêu cuối cùng là tạo ra một tập hợp hành động để tiếp tục thực hiện.

Hình trong sách minh họa một bảng StoStaKee điển hình cho buổi retrospective của Dự án A:

| Dừng làm - Stop | Bắt đầu làm - Start | Tiếp tục làm - Keep |
|---|---|---|
| Phát hành phần mềm còn lỗi | Chia nhỏ công việc | Tổ chức retrospective |
| Để phạm vi phình ra ngoài kiểm soát | Sử dụng Agile | Tiếp tục tiến lên |
| Làm việc mọi giờ | Xác định trước phạm vi | Không làm việc cuối tuần |
| Hứa những điều không thể bàn giao | Thử TDD | Duy trì quan hệ làm việc tốt |
| Ăn mừng thất bại | Để Dev và Ops phối hợp chặt chẽ hơn | Tiếp tục sử dụng Agile |
| Cắt giảm nhân lực | Uống thêm bia |  |
| Cắt giảm phạm vi | Tự hào về công việc |  |
| Làm theo Waterfall | Làm việc thông minh hơn |  |
| Kiểm thử quá muộn |  |  |

Từ các phiếu và lượt bình chọn, bảng ví dụ tạo ra những hành động sau:

- Tập trung vào chất lượng phần mềm và các lỗi lọt ra ngoài.
- Tiếp tục tổ chức retrospective thường xuyên.
- Xem xét làm việc theo mô hình DevOps.
- Ngừng làm việc kéo dài và làm việc cuối tuần. Ý này xuất hiện ba lần trên bảng hành động, phản ánh mức độ quan tâm cao.
- Tìm hiểu TDD và chạy thử nghiệm.
- Tập trung áp dụng Agile nhiều hơn.

> **Chú thích:**
>
> - **Scope creep:** phạm vi dự án tăng dần mà không được kiểm soát tương ứng về thời gian, chi phí hoặc nguồn lực.
> - **TDD (Test-Driven Development):** phát triển hướng kiểm thử; trước tiên viết một kiểm thử thất bại, sau đó viết lượng mã tối thiểu để kiểm thử chạy qua, rồi cải tiến mã.
> - **Escaped defect:** lỗi không bị phát hiện trong các bước kiểm soát nội bộ mà "lọt" đến khách hàng hoặc production.
>
> **Ví dụ:** Ghi chú "kiểm thử quá muộn" nhận nhiều phiếu. Hành động tốt không phải là câu mơ hồ "cải thiện kiểm thử", mà là "trong sprint tới, thử TDD với module thanh toán; theo dõi số lỗi phát hiện trước và sau khi tích hợp".

Nhìn chung, các kỹ thuật và công cụ vừa nêu chỉ là một phần nhỏ trong số những lựa chọn hiện có. Chúng đã nhiều lần chứng minh hiệu quả trong việc điều tra và, quan trọng hơn, giúp mọi người hiểu các vấn đề trong quy trình chuyển giao sản phẩm.

Bạn có thể cảm thấy một số kỹ thuật không phù hợp với văn hóa hoặc môi trường của mình. Điều đó hoàn toàn bình thường; vẫn còn rất nhiều lựa chọn khác. Việc mời một chuyên gia trong lĩnh vực hỗ trợ lựa chọn và điều chỉnh kỹ thuật, công cụ cho phù hợp cũng có thể hữu ích.

Vậy đến đây, chúng ta đã học được những gì?

## Tổng kết

*Nguyên tác: Summary*

Trong chương này, bạn đã có được cái nhìn sâu hơn về các khía cạnh sau:

- Hiểu rằng quy trình chuyển giao sản phẩm có những vấn đề mà mọi người đang phớt lờ, hoặc nhiều khả năng là không thể nhìn thấy - điều chương này gọi là "con voi trong phòng".
- Các cách điều tra vấn đề bằng việc thu hút cả ban quản lý lẫn lực lượng lao động cùng tham gia nhận diện.
- Một số công cụ và kỹ thuật hiệu quả giúp chia nhỏ vấn đề thành các phần việc dễ nhận biết.

Hình tổng kết trong sách đối chiếu cách làm xấu và cách làm tốt:

| Không tốt | Tốt |
|---|---|
| Phớt lờ vấn đề | Thu hút càng nhiều người tham gia càng tốt |
| Không cho càng nhiều người càng tốt tham gia | Làm cho cuộc điều tra trở nên thú vị |
| Giữ bí mật và làm việc sau những cánh cửa đóng kín | Biến kết quả điều tra thành hành động loại bỏ vấn đề |
|  | Đối thoại cởi mở và trung thực |
|  | Loại bỏ lãng phí |

Bây giờ, bạn đã biết cách thu thập thông tin và dữ liệu có giá trị về vấn đề của mình, đồng thời đã có những hành động rất cần thiết để tiếp tục. Nếu các vấn đề xoay quanh lãng phí do chu kỳ phát hành kéo dài và một tổ chức bị chia cắt thành các silo, bạn đã có một mục tiêu rõ ràng, gần như chắc chắn sẽ xử lý được vấn đề và mang lại điều toàn doanh nghiệp cần: triển khai phương thức làm việc Continuous Delivery và DevOps.

Việc còn lại là tập hợp một kế hoạch hành động để triển khai - và thật đúng lúc, đó chính là nội dung của chương tiếp theo.

> **Chú thích:** *Siloed organization* là tổ chức trong đó các bộ phận hoạt động như những "ống silo" tách biệt: tối ưu mục tiêu riêng, ít chia sẻ thông tin và thường bàn giao công việc qua ranh giới cứng. DevOps tìm cách giảm các rào cản này bằng mục tiêu chung, cộng tác và trách nhiệm xuyên suốt.
>
> **Ví dụ:** Dev được đánh giá theo số tính năng hoàn thành, còn Ops được đánh giá theo độ ổn định và số thay đổi càng ít càng tốt. Hai mục tiêu cục bộ xung đột khiến phát hành trở thành cuộc giằng co. Một mục tiêu chung như lead time ngắn đi kèm tỷ lệ thay đổi thất bại thấp sẽ khuyến khích hai bên cùng cải thiện toàn bộ hệ thống.

