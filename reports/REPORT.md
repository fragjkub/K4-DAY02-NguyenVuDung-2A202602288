# Báo cáo — Ngày 2: phát hiện vật thể

Họ và tên: Nguyễn Vũ Dũng
MSSV: 2A202602288
Hình thức: cá nhân
Mã cặp: SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: 58
- Mã SHA-256 của gói YOLO của bạn: cef1f8e255ea86c1e72fcb8bb08dc100acbc8803c429ef7eb2233a4c5dd05ed0
- Mã SHA-256 của gói CVAT gốc của bạn: bdac48fc8b940a3751c455af060560077a477e016643c016678a79135ef9ded6
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Đợt thực hành Ngày 2, nhận sau khi đã tự gán nhãn xong 4 ảnh và xuất đủ 2 gói dữ liệu độc lập.

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Việc gán nhãn toàn bộ 58 vật thể trên cả 4 ảnh trong CVAT và xuất hai gói Ultralytics YOLO và CVAT trước khi nhận gói đối chiếu là do tôi thực hiện, không có sự can thiệp hay sao chép từ nguồn bên ngoài.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_008 / box [236.05, 77.18, 340.45, 206.54] | truck | Cabin riêng biệt phía trước và thùng hàng khối lớn phía sau | Thùng/ben hoặc sàn hàng rõ ràng; gán vào truck, không gộp với car hay van |
| drive_008 / box [34.06, 42.40, 151.32, 126.36] | bus | Thân xe khách dài, có dải cửa sổ liên tiếp dọc thân | Xe khách thân dài, nhiều cửa sổ; gán vào bus |
| drive_022 / box [298.01, 315.48, 358.53, 360.11] | van | Thân hộp nhỏ, kín một khối từ trước ra sau, dùng chở người/hàng | Thân hộp nhỏ, kín; gán vào van, không có thùng hàng tách biệt |
| drive_038 / box [343.47, 516.70, 533.35, 639.26] | car | Phần đuôi xe và kính chiếu hậu dạng xe con sedan/SUV | Phương tiện cá nhân tiêu chuẩn, không có thùng hàng hay thân dài; gán vào car |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau: Tại ảnh drive_008, vật thể tại tọa độ [34.06, 42.40, 151.32, 126.36] có lớp là `bus` và thuộc tính `visibility` là `occluded`. Lớp (class) xác định bản chất chủng loại của vật thể là xe buýt nhằm phục vụ bài toán phân loại, trong khi thuộc tính (attribute) mô tả điều kiện quan sát vật lý thực tế trong ảnh (bị che khuất một phần bởi phương tiện khác). Hai thông tin này độc lập: một chiếc xe thuộc lớp `bus` có thể mang thuộc tính `clear`, `occluded` hoặc `unclear` tùy góc nhìn.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| drive_008 box ở mép phải (xbr=640) bị dư phần nền | hình học | Phóng to 100% kiểm tra biên các vật thể chạm mép | Thu hẹp tọa độ hộp ôm khít phần vỏ xe nhìn thấy, đặt boundary='truncated' |
| drive_038 box [283.71, 342.60, 466.02, 511.41] phân vân giữa truck và van | lớp | Rà soát cấu trúc thân xe: khoang sau liền khối cabin | Gán chính xác lớp `van`, đặt review_state='confident' theo quy tắc thân hộp kín |

- Số hộp `needs_review` trước và sau khi kiểm: Trước khi kiểm tra có 3 hộp `needs_review` (ở drive_008 và drive_033); sau khi kiểm tra và rà soát toàn bộ đã chuyển về 0 hộp `needs_review`.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Tại drive_033, có vật thể rất nhỏ ở xa [306.59, 29.80, 313.82, 39.28] bị mờ và khó phân biệt chi tiết đèn/kính. Ban đầu để `needs_review`, sau khi đối chiếu quy tắc "chỉ gán khi đủ bằng chứng và thấy được mui xe con", tôi giữ lớp `car` và chuyển sang `confident`.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `3 0.512922 0.527805 0.094562 0.069734`
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp `van` (class_id 3), tọa độ pixel [298.01, 315.48, 358.53, 360.11] trên ảnh 640x640
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Một dòng nhãn YOLO chỉ yêu cầu 5 giá trị số hợp lệ về mặt cú pháp và nằm trong khoảng [0, 1]. Trình phân tích cú pháp không thể đánh giá ngữ nghĩa: nếu người gán nhãn nhầm một chiếc xe van thành xe con (sai class_id), vẽ hộp rộng bao trùm cả lề đường (sai hình học), hoặc vô tình bao gồm hai xe vào một khung hình (sai phạm vi), dòng nhãn vẫn hoàn toàn hợp lệ về mặt kỹ thuật nhưng dữ liệu gán nhãn đã bị sai bản chất.
a
## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình phát hiện được xe tải chính giữa ảnh drive_008 nhưng bounding box dự đoán có xu hướng hơi lệch về phía dưới và bỏ sót một số xe con ở xa phía sau.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần kiểm tra lại các trường hợp xe ở hậu cảnh bị che khuất một phần (occluded) xem nhãn vẽ đã đủ nhất quán hay chưa, và liệu các góc khuất có làm mô hình nhầm với nền đường hay không.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Mô hình chỉ được huấn luyện thử 8 epochs trên vỏn vẹn 3 ảnh với độ sâu backbone bị đóng băng (freeze=10). Mức độ dự đoán sai lệch chủ yếu do mô hình bị thiếu dữ liệu trầm trọng (underfitting), không đủ cơ sở để quy kết là do nhãn gán sai.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Tập dữ liệu chỉ gồm 3 ảnh huấn luyện và 1 ảnh kiểm tra không có ý nghĩa thống kê. Các chỉ số như mAP trên một ảnh không phản ánh khả năng khái quát hóa của mô hình trên môi trường thực tế với đa dạng góc quay, thời tiết, mật độ giao thông và độ phân giải khác nhau.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 32
- IoU trung bình và trung vị: IoU trung bình: 0.841354, Trung vị: 0.872384
- Mức đồng thuận lớp: 0.71875 (71.88%)
- Số hộp phía bạn không ghép được: 26
- Số hộp phía đối chiếu không ghép được: 18
- Một điểm khác biệt cụ thể: Ở các ảnh có mật độ phương tiện dày đặc (như drive_008), phía đối chiếu và phía bài làm có sự chênh lệch lớn về ngưỡng gán nhãn đối với các xe ở hậu cảnh xa hoặc bị che khuất nhiều (occluded/unclear), dẫn đến 26 hộp của tôi và 18 hộp của đối chiếu không ghép được cặp hình học thỏa mãn. Trong số 32 hộp ghép được, tỷ lệ đồng thuận lớp đạt 71.88% do có sự khác biệt quan điểm phân định giữa car với van và truck ở các góc nhìn khuất.
- Quy tắc hoặc hành động sửa phát sinh: Rà soát lại tiêu chuẩn phân định giữa xe van thân hộp kín và xe con hatchback/SUV; đồng thời thảo luận thêm với Lab Coach để đưa ra ngưỡng kích thước pixel tối thiểu cụ thể cho các xe ở rất xa nhằm chuẩn hóa việc gán nhãn hoặc bỏ qua.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Mức đồng thuận hay chỉ số IoU cao chỉ phản ánh tính nhất quán và khả năng tái lập quy tắc giữa hai bên. Nếu cả hai người cùng hiểu sai một tiêu chuẩn trong cẩm nang (ví dụ cùng gán nhầm xe bán tải thành xe tải, hoặc cùng bỏ sót vật thể bị cắt mép), kết quả đối chiếu vẫn có thể cho ra độ trùng khớp cao dù nhãn thực tế đã vi phạm ground truth của bài toán

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là tính toàn vẹn và đồng bộ 100% hình học giữa hai bản xuất (YOLO và CVAT XML với 58 boxes), cùng việc xử lý triệt để các hộp `needs_review` về trạng thái `confident` sau rà soát
Câu hỏi cho Lab Coach: Trong thực tế triển khai, đối với các phương tiện bị mép ảnh cắt (truncated) chỉ còn nhìn thấy dưới 10% diện tích (ví dụ chỉ thấy một góc cản trước hoặc gương chiếu hậu), có quy định ngưỡng diện tích hoặc tỷ lệ phần trăm tối thiểu cụ thể nào để quyết định nên gán nhãn hay bỏ qua để tránh gây nhiễu cho bộ phát hiện hay không?