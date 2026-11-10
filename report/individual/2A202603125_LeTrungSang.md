# Báo cáo cá nhân — Ngày 2 Object Detection

Đổi tên file theo `MSSV_HoTen.md`, ví dụ `20241234_NguyenVanAn.md`. Điền kết quả thật, xóa dòng hướng dẫn khi nộp. Xem [quy định nộp](../../SUBMISSION.md).

## 1. Thông tin học viên

- Họ tên: Lê Trung Sang
- MSSV: 2A202603125
- Email: trungsangle123@gmail.com
- Lớp: C401
- Link repo bài làm: https://github.com/trungsangle123-glitch/K5-L2-DAY02-LeTrungSang-2A202603125-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1Gs5Gg1B60dgf-J0Fqa3aR7aWdVFKSLzC?usp=sharing

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt ba ca mơ hồ đã quyết định, cách áp dụng schema và phần cần giảng viên làm rõ.

## 3. Annotation và self-QC

- Số file nhãn train: 50 file nhãn (tương ứng 50 ảnh train đã kiểm tra khớp thành công ở bước chuẩn bị).

- Kết quả checker và lỗi đã xử lý: Quá trình kiểm tra dữ liệu đầu vào hoàn toàn thành công (OK: 50 train + 10 eval; eval không đi vào train.). Không phát hiện lỗi lệch ảnh/nhãn nào.

- Danh sách ít nhất 10 ảnh đã rà bằng mắt: drive_051.jpg, drive_052.jpg, drive_053.jpg, drive_054.jpg, drive_055.jpg, drive_056.jpg, drive_057.jpg, drive_058.jpg, drive_059.jpg, drive_060.jpg.

- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
Từ kết quả dự đoán của model YOLO26n (mAP50 đạt 0.272 và mAP50-95 đạt 0.201):
Lớp bus có mAP50 cực kỳ thấp (~0.0078), có thể do số lượng mẫu quá ít (chỉ có 2 thực thể) hoặc nhãn bị gán nhầm lẫn với car/truck.
Lớp van có mAP50 thấp (~0.103) thường xuyên bị nhận diện nhầm thành car hoặc bỏ sót khi các xe xếp san sát nhau.
Cách sửa: Cần bổ sung thêm ảnh chứa lớp bus và van hoặc hiệu chỉnh lại nhãn để model phân biệt rõ kích thước và hình dáng đặc trưng giữa chúng.

- Commit khóa nhãn ban đầu: 81d12db 

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP |566 | |
| FP | 65| |
| FN |103	 | |
| Precision | 0.8970	| |
| Recall |0.8460	 | |
| F1 |0.8708	 | |
| mean_iou_of_matches |0.9782	 | |
| passed |True | |

Dẫn tới `outputs/annotation_evaluation.json` trong repo. Nếu rework, ghi ảnh, lỗi đã kiểm, quyết định sửa/giữ và lý do. Không suy nguyên nhân chỉ từ FP/FN. Không có rework thì ghi rõ.

## 5. Kết quả YOLO
Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
<li> Môi trường: Google Colab
<li>GPU: Tesla T4 (15GB VRAM)
<li>Phiên bản Ultralytics: 8.4.209
<li>Phiên bản Torch: 2.14.1+cu130
<li>Các tham số thay đổi so với notebook: Giữ nguyên mặc định theo thiết lập chuẩn (epochs=50, imgsz=640, batch=-1).
<li>Đường dẫn best checkpoint: <b>/content/runs/detect/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt<b>

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision |0.4101 |
| Recall | 0.2298|
| mAP50 | 0.2719|
| mAP50–95 | 0.2016|

Đặt cả 2 folder `assets/eval_predictions/` và `assets/yolo26_runs/` (giải nén từ `day2_colab_outputs.zip`) cạnh báo cáo và dẫn link tương đối. Không chỉ ghi số; chỉ ra ít nhất một ảnh và vật thể cụ thể.

Trả lời bốn câu hỏi cuối notebook:

1. Lớp nào có recall thấp nhất? Chỉ ra một ảnh eval minh họa.
Trả lời: Hai lớp truck và van đều có recall bằng 0 (thấp nhất tuyệt đối).
Minh họa:
Nhìn vào kết quả dự đoán của ảnh drive_054.jpg (ở bước trước, mô hình chỉ dự đoán được 9 cars, 1 bus). Trong khi đó, các ảnh như drive_055.jpg đến drive_060.jpg có rất nhiều xe dạng bán tải, xe tải nhỏ hoặc xe khách/van nhưng mô hình thường nhận diện nhầm thành car hoặc bỏ sót (recall = 0), dẫn đến việc không phát hiện chính xác các lớp này.
2. Nếu mAP50 cao nhưng mAP50-95 thấp, box đang gặp vấn đề gì?
mAP50 đo lường độ chính xác khi chỉ cần độ giao nhau giữa box dự đoán và ground truth (IoU) đạt từ 0.5 trở lên.
mAP50-95 đo trung bình mAP ở nhiều ngưỡng IoU khắt khe hơn (từ 0.5 đến 0.95 với bước nhảy 0.05).
Vấn đề của box: Khi mAP50 cao nhưng mAP50-95 thấp, điều này có nghĩa là mô hình đã xác định đúng vị trí tổng thể của đối tượng (phân loại đúng, khoanh vùng hụt nhẹ vẫn tính là trúng ở ngưỡng IoU 0.5), nhưng khung bounding box vẽ ra không khớp khít, bị lệch hoặc quá rộng/quá hẹp so với ground truth thực tế (không đạt yêu cầu ở các ngưỡng IoU cao hơn như 0.75, 0.85, 0.95).
3. Có lỗi truck/bus/van/car nào liên quan tới guideline không?
Có sự nhầm lẫn rất lớn giữa các lớp xe cỡ lớn và xe con.
Lỗi phổ biến: Nhiều loại xe bán tải lớn (Pick-up), xe tải nhỏ (Mini-truck) hoặc xe 16 chỗ thường bị gán nhãn tùy tiện lúc train hoặc bị mô hình đoán thành car thay vì phân loại đúng vào truck hay van theo đúng guideline kỹ thuật. Do tập train chỉ có 50 ảnh và chất lượng nhãn chưa được chuẩn hóa đồng đều theo quy chuẩn phân biệt cụ thể (ví dụ: thế nào là van - xe tải van/16 chỗ, thế nào là truck - xe tải nâng), mô hình bị bối rối và quy hết về lớp chiếm đa số là car (lớp car có recall cao nhất: 0.755).
4. Vì sao không dùng 10 ảnh eval để train dù chúng có nhãn?
Tránh hiện tượng Overfitting (Quá khớp): Nếu đưa cả 10 ảnh eval vào quá trình train, mô hình sẽ học thuộc lòng các đặc trưng của 10 ảnh này.
Đánh giá khách quan: Ta cần một tập dữ liệu độc lập (những ảnh mô hình chưa từng được tối ưu hóa trọng số dựa trên chúng) để đánh giá khả năng tổng quát hóa (generalization) của mô hình trên dữ liệu thực tế ngoài đời thực. Nếu dùng eval để train, các chỉ số đo lường như mAP sẽ cực kỳ cao nhưng hoàn toàn là giả tạo (không phản ánh đúng khả năng thực tế).


## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gán nhãn & Kiểm tra (Day 1)|Sai lệch số lượng nhãn hoặc thiếu bounding box so với tập chuẩn (Gold Set). |Chạy checker nội bộ để lấy danh sách file lỗi, rà soát thủ công các lỗi lệch nhãn.|Đã khắc phục hoàn toàn: Số lượng nhãn đạt 50/50, chỉ số Gold QC đạt F1 = 0.8708, passed = True. |
|Huấn luyện Model (Day 2)|Model YOLO26n gặp khó khăn khi nhận diện lớp `bus` (mAP50 cực thấp ~0.0078) và lớp `van` (mAP50 ~0.103).|Kiểm tra dữ liệu huấn luyện và dự đoán trực quan trên tập eval.| Nhận thấy tập dữ liệu gốc quá ít mẫu cho `bus` và `van`, các lớp này dễ bị model dự đoán nhầm thành `car`. Cần cải thiện bằng cách thu thập thêm ảnh đa dạng hoặc tối ưu hóa nhãn.|

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md): phần nào đã có kết quả, phần nào còn thiếu hoặc chưa kiểm được. Không tự nhận hoàn thành bước chưa chạy.

## 8. Công cụ AI và tài liệu tham khảo

Dùng opencode về tech và gemini về nghiên cứu để setup Yolo v26 cho CVAT và giải thích thuật ngữ vì bản thân em / mình là người mới nên cần research về AI và cả 4 câu hỏi trên cũng nhờ 1 phần AI giải đáp

