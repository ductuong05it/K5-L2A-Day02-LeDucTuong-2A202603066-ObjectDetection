# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Phương tiện giao thông đường bộ có 4 bánh, thường dùng để chở người, có kích thước nhỏ hoặc trung bình | chở người, có kích thước nhỏ hoặc trung bình.Ô tô con, sedan, SUV|
| `truck` |Phương tiện vận tải có kích thước lớn, được thiết kế chủ yếu để vận chuyển hàng hóa|Xe tải thùng, xe ben, xe tải đông lạnh, xe đầu kéo |
| `bus` |Phương tiện giao thông công cộng hoặc chuyên dụng, có sức chứa nhiều hành khách |Xe buýt thành phố, xe khách, xe buýt trường học |
| `van` |Phương tiện có khoang thân xe kín, kích thước trung bình, thường dùng để chở người hoặc hàng hóa |Minivan, xe van giao hàng, xe chở khách cỡ nhỏ |
Nguyên tắc phân biệt: Dựa trên hình dáng tổng thể, cấu trúc thân xe, kích thước tương đối và mục đích sử dụng có thể nhận biết từ hình ảnh. Không phân loại chỉ dựa trên màu sắc hoặc góc chụp. đặc biệt lớp xe bus thường có hình khối hình chữ nhật và cao hơn hẳn dễ nhận thấy, lớp van thường thấp hơn lớp bus nhưng vẫn cao hơn lớp car, mũi xe ngắn, lớp truck dễ phân biệt nhờ container đằng sau.

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Bounding box phải bao quanh toàn bộ phần phương tiện nhìn thấy được, sát với đường viền ngoài của vật thể. Không lấy quá nhiều phần nền xung quanh và không cắt mất những bộ phận đang hiển thị.
- Vật thể bị che khuất: Nếu phương tiện vẫn có thể nhận diện được, tiến hành gán nhãn theo phần vật thể nhìn thấy. Bounding box có thể ước lượng phần bị che khuất dựa trên hình dáng, kích thước và cấu trúc của phần vật thể còn nhìn thấy, nhằm bao quát toàn bộ phương tiện. Tuy nhiên, không suy đoán quá mức khi không đủ căn cứ xác định vị trí hoặc kích thước thực tế
- Vật thể bị cắt bởi rìa ảnh: Nếu phương tiện bị cắt một phần bởi cạnh ảnh nhưng vẫn đủ đặc điểm để xác định lớp, tiến hành gán nhãn phần nằm trong khung hình
- Vật thể rất nhỏ/mơ hồ: Nếu phương tiện quá nhỏ hoặc hình ảnh không đủ rõ để xác định chính xác lớp, không cố gán nhãn theo suy đoán. Chỉ gán nhãn khi có đủ căn cứ nhận diện

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` |Một chiếc xe van có hình dáng gần giống car vì có hình dáng nhỏ |Gán nhãn van |Xe có khoang thân kín, mũi xe ngắn |
| `drive_004` |Ba chiếc xe cạnh nhau nhỏ sáng đèn và ảnh nhiễu do trời tối |Gán nhãn cả |Ba xe có cùng kích thước thấp ngang nhau và phía trước có một chiếc van cao hơn hẳn để so sánh |
| `drive_014` |Một chiếc xe nhìn đc đầu xe khá cao nhưng ảnh nhiễu mờ khuất đi phần sau thân xe, phân vân giữa đầu xe truck và xe van |Không gán nhãn |Hình ảnh không đủ rõ để xác định chính xác lớp |
