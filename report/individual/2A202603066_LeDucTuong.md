# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Lê Đức Tường 
- MSSV: 2A202603066
- Email: 26ai.tuongld@vinuni.edu.vn 
- Lớp: L2A
- Link repo bài làm: https://github.com/ductuong05it/K5-L2A-Day02-LeDucTuong-2A202603066-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/ductuong05it/K5-L2A-Day02-LeDucTuong-2A202603066-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb#scrollTo=rnrth6ScX_p8

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.
Phần cần giảng viên làm rõ: nhãn gold eval/train gán xe buýt lớn là class 3 và không gán xe đỗ ven đường, khác README/GUIDE. Khi hỏi trong buổi lab, giảng viên hướng dẫn làm theo định nghĩa trong tài liệu .md, nên tôi giữ bus=2 và vẫn gán xe đỗ.
## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: PS D:\VinAI\day2\K5-L2A-Day02-LeDucTuong-2A202603066-ObjectDetection> py tools/check_yolo_labels.py
ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: rà toàn bộ 50 ảnh (drive_001 → drive_050) trong CVAT, ưu tiên ảnh đông xe (002, 012, 026, 050), ảnh ban đêm (004, 010) và ảnh có bus/truck (008, 009, 039, 040).
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:  Thay đổi đo được giữa bản trước và sau khi rà: truck 26 → 27, bus 88 → 90, van 51 → 49. Kiểm tra tự động sau khi rà: không có ảnh rỗng, không có cặp box trùng (IoU > 0.6)
- Commit khóa nhãn ban đầu: f27dbcc — "Day02: khóa nhãn ban đầu 50 ảnh train".

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |469 | |
| FP |576 | |
| FN |184 | |
| Precision |0.4488 | |
| Recall |0.7182 | |
| F1 |0.5524 | |
| mean_iou_of_matches |0.8425 | |
| passed |false | |

https://github.com/ductuong05it/K5-L2A-Day02-LeDucTuong-2A202603066-ObjectDetection/blob/main/outputs/annotation_evaluation.json commit cedaff3. Mean IoU đạt (0.8425 ≥ 0.70), F1 chưa đạt (0.5524 < 0.80).
Phân tích:

Lệch class ID có hệ thống. 123/177 FN là box đúng vị trí (IoU ≥ 0.5) nhưng khác class với gold, theo một hoán vị cố định: gold 3 ↔ nhãn bus(2) (51 box), gold 1 ↔ nhãn van(3) (31 box), gold 2 ↔ nhãn truck(1) (20 box). Nhãn của tôi theo README; gold dường như dùng thứ tự ID khác. Bỏ qua class, F1 = 0.6866, recall = 0.9142.
Khác quy ước xe đỗ. 545/610 FP nằm ở nửa trên ảnh. So sánh trực quan drive_001, drive_028: gold chỉ gán xe trên lòng đường chính, không gán xe đỗ ven đường, trong khi GUIDE §1 quy định "xe đang đỗ vẫn gán lớp theo loại xe". Tôi giữ quyết định theo GUIDE.
Lỗi thật của tôi: 37 xe bỏ sót và 17 box lệch (IoU 0.2–0.5)do tự ước lượng vật thể bị che khuất, tập trung ở drive_005, 008, 009, 015, 024, 026, 027, 041, 048 (ví dụ drive_042 sót một xe lớn ở phía dưới ảnh).
Rework: không có rework, giữ nguyên bộ nhãn đã khóa; cột "Sau sửa" để trống vì không chạy lại evaluator. Lý do: phần lớn FN/FP đến từ khác biệt quy ước giữa gold và README/GUIDE; kể cả bỏ qua class, F1 tối đa ≈ 0.69, nên sửa 54 lỗi thật cũng không vượt ngưỡng 0.80 nếu vẫn giữ đúng schema tài liệu. Cần giảng viên làm rõ quy ước class ID và xe đỗ của gold.
## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:Google Colab, GPU Tesla T4 (14913 MiB), Python 3.13.16, Ultralytics 8.4.208, torch 2.14.1+cu130.
- Các tham số thay đổi so với notebook nếu có và lý do: chỉ điền REPO_URL.
- Đường dẫn best checkpoint trong phiên chạy:/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.4105453698519763 |
| Recall |0.23026854853628678 |
| mAP50 |0.27225886498171903 |
| mAP50–95 |0.20168300309748077 |

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào recall thấp nhất? Ảnh nào minh họa?
2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box? Cần kiểm tra độ chính xác hình học của bounding box. mAP50 chỉ yêu cầu IoU đạt ngưỡng 0.5, trong khi mAP50–95 đánh giá ở nhiều ngưỡng IoU cao hơn. Vì vậy, chênh lệch lớn có thể cho thấy box dự đoán nhận diện đúng đối tượng nhưng chưa bám sát đường viền vật thể, box quá rộng, quá hẹp hoặc lệch vị trí.
3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?Có thể xem xét lỗi phân biệt bus và van. Trong quá trình đối chiếu, tôi nhận thấy có sự khác biệt giữa gold labels và schema trong tài liệu: một số xe buýt lớn được gán class ID 3 trong gold, trong khi schema quy định class ID 2 là bus, class ID 3 là van. Ngoài ra, cách xử lý xe đỗ ven đường cũng có khác biệt giữa gold labels và hướng dẫn đã áp dụng. Đây là yếu tố cần được giảng viên làm rõ trước khi kết luận lỗi hoàn toàn xuất phát từ annotation hay mô hình.
4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?
Không sử dụng 10 ảnh eval để train nhằm giữ nguyên sự độc lập tương đối giữa dữ liệu huấn luyện và dữ liệu đánh giá, tránh rò rỉ dữ liệu. Trong notebook, 10 ảnh eval được sử dụng làm validation trong quá trình train để theo dõi hiệu suất và lựa chọn checkpoint best.pt, sau đó tiếp tục được dùng để đánh giá mô hình. Vì vậy, đây không phải test set độc lập.
## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Kiểm tra nhãn trên Windows | Lệnh `python3` không được Windows nhận diện, xuất hiện thông báo Python was not found. | Thử sử dụng lệnh `py` thay cho `python3`. | Đã chạy được script kiểm tra nhãn bằng Python Launcher. |
| Chạy script đánh giá annotation | Sử dụng dấu `\` để xuống dòng theo cú pháp Linux khiến PowerShell báo lỗi cú pháp. | Chuyển sang viết lệnh trên một dòng hoặc sử dụng dấu backtick của PowerShell. | Đã xác định được nguyên nhân và cách sửa cú pháp. |
| Mở notebook trên Colab | Ban đầu chưa xác định được vị trí kết nối notebook từ GitHub, chỉ thấy tùy chọn upload. | Sử dụng chức năng Open notebook, chọn GitHub hoặc mở notebook qua URL GitHub. | Đã mở và chạy được notebook từ repo cá nhân. |
| Đánh giá annotation | F1 đạt 0.5524, thấp hơn ngưỡng 0.80 mặc dù Mean IoU đạt 0.8425. | Đối chiếu kết quả JSON và ghi nhận sự khác biệt giữa gold labels với schema trong tài liệu. | Chưa đạt ngưỡng F1; cần làm rõ sự khác biệt về class mapping và quy tắc gán nhãn với giảng viên. |
| Huấn luyện YOLO | Kết quả nhận diện trên 10 ảnh eval còn thấp, đặc biệt Recall chỉ đạt khoảng 0.2303. | Kiểm tra metrics, confusion matrix và ảnh dự đoán để phân tích nguyên nhân. | Cần phân tích thêm recall theo từng lớp và các ảnh bị bỏ sót.

## 7. Tự đánh giá theo rubric

| Tiêu chí | Kết quả thực tế | Tự đánh giá |
|---|---|---|
| Hoàn thành và định dạng YOLO (10 điểm) | Có đủ 50 file nhãn train, checker xác nhận 50/50 file hợp lệ. | Đã hoàn thành kiểm tra cấu trúc và định dạng. |
| Đầy đủ vật thể (20 điểm) | Đã rà toàn bộ 50 ảnh, tuy nhiên kết quả gold QC còn 184 FN. | Chưa đảm bảo đầy đủ vật thể theo ground truth. |
| Độ bám sát và hình học box (20 điểm) | Mean IoU đạt 0.8425, cao hơn ngưỡng 0.70. | Đạt ngưỡng Mean IoU của gold QC, cần tiếp tục kiểm tra box sai lệch. |
| Phân lớp nhất quán (15 điểm) | Có sự khác biệt về cách gán class giữa gold labels và schema trong tài liệu, đặc biệt ở lớp bus và van. | Cần giảng viên làm rõ để thống nhất cách phân lớp. |
| Mini guideline và quyết định mơ hồ (10 điểm) | Có định nghĩa bốn lớp và ba trường hợp mơ hồ. | Đã hoàn thành, cần điều chỉnh quy tắc che khuất để thống nhất với GUIDE.md. |
| Gold-set annotation QC (15 điểm) | F1 = 0.5524, chưa đạt 0.80; Mean IoU = 0.8425, đạt 0.70. | Chưa đạt yêu cầu QC, chưa có kết quả rework sau đánh giá gold. |
| Pipeline YOLO và phân tích (10 điểm) | Đã chạy Colab, train YOLO26n, đánh giá trên 10 ảnh eval và thu được metrics. | Đã chạy pipeline nhưng cần bổ sung phân tích per-class recall, ảnh minh họa và kiểm tra đầy đủ assets. |

**Tự đánh giá chung:**

Đã hoàn thành phần lớn quy trình thực hành từ gán nhãn, kiểm tra cấu trúc dữ liệu đến huấn luyện và đánh giá mô hình YOLO. Tuy nhiên, chất lượng annotation chưa đạt ngưỡng F1 theo yêu cầu. Kết quả mô hình cũng còn thấp, cho thấy cần tiếp tục xem xét chất lượng nhãn, độ mất cân bằng giữa các lớp và độ phủ của dữ liệu huấn luyện.

Tôi chưa tự đánh giá các hạng mục chưa có đủ dữ liệu kiểm chứng là đã hoàn thành.
## 8. Công cụ AI và tài liệu tham khảo

### Công cụ AI đã sử dụng

- **ChatGPT:** Hỗ trợ giải thích quy trình gán nhãn Object Detection, phân biệt các lớp car/truck/bus/van, xây dựng mini guideline và xử lý các trường hợp mơ hồ.
- Hỗ trợ khắc phục lỗi chạy Python trên Windows PowerShell và hướng dẫn mở, chạy notebook trên Google Colab.
- Hỗ trợ giải thích các chỉ số Precision, Recall, F1, IoU, mAP50 và mAP50–95; hỗ trợ xây dựng nội dung phân tích kết quả mô hình.

Các kết quả thực nghiệm, số liệu annotation, metrics YOLO và quyết định gán nhãn được lấy từ quá trình thực hiện bài tập cá nhân. Nội dung hỗ trợ từ AI được đối chiếu với tài liệu bài lab và kết quả thực tế trước khi sử dụng.

### Tài liệu tham khảo

1. `README.md` – Mục tiêu và quy trình thực hành.
2. `GUIDE.md` – Hướng dẫn gán nhãn và đánh giá annotation.
3. `GUIDELINE_MINI.md` – Quy tắc gán nhãn cá nhân.
4. `RUBRIC.md` – Tiêu chí đánh giá bài làm.
5. `SUBMISSION.md` – Quy định cấu trúc và hình thức nộp bài.
6. `notebooks/day2_annotation_to_yolo.ipynb` – Notebook huấn luyện và đánh giá YOLO26.
