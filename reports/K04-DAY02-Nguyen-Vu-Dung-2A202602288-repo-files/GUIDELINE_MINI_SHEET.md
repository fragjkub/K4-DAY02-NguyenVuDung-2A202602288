# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Nguyễn Vũ Dũng
**MSSV:** 2A202602288
**Hình thức:** cá nhân
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới[cite: 7].
- Mỗi phương tiện là một hộp; không gộp nhiều xe[cite: 7].
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu[cite: 7].
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định[cite: 7].

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con[cite: 7] | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp[cite: 7] |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng[cite: 7] | ô tô con; thân xe buýt; xe van kín một khối[cite: 7] |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế[cite: 7] | xe van nhỏ; xe tải; ô tô con[cite: 7] |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng[cite: 7] | thân xe buýt; khoang hàng tách biệt như xe tải[cite: 7] |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`[cite: 7].

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy[cite: 7].
- Không ước lượng phần bị xe khác che[cite: 7].
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp[cite: 7].
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện[cite: 7].

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy)[cite: 7] | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ)[cite: 7] | mức bằng chứng nhìn thấy[cite: 7] |
| `boundary` (quan hệ mép ảnh)[cite: 7] | `inside` (trong ảnh), `truncated` (bị cắt)[cite: 7] | vật thể có bị mép ảnh cắt hay không[cite: 7] |
| `review_state` (trạng thái xem lại)[cite: 7] | `confident` (tự tin), `needs_review` (cần xem lại)[cite: 7] | đánh dấu quyết định cần quay lại[cite: 7] |

YOLO không lưu ba thuộc tính này[cite: 7]. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc[cite: 7].

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu[cite: 7].

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_038.jpg` / vật thể tại tọa độ `[283.71, 342.60, 466.02, 511.41]`[cite: 3]
- Dấu hiệu nhìn thấy: Phương tiện có kích thước lớn hơn ô tô con, dáng thân hộp một khối kín, phần mui thẳng kéo dài nhưng tổng chiều dài cơ sở không quá lớn như xe khách liên tỉnh, không có dải nhiều hàng cửa sổ dài.
- Quy tắc áp dụng: Thân hộp nhỏ/vừa, kín một khối, dùng chở người hoặc hàng thì xếp vào `van`[cite: 7]; xe buýt (`bus`) yêu cầu thân xe khách dài với nhiều cửa sổ hoặc nhiều hàng ghế[cite: 7].
- Quyết định: Gán lớp `van`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đặt tạm `review_state` là `needs_review`[cite: 7], phóng to 100% để đếm số khung cửa kính dọc thân xe; nếu tỷ lệ chiều dài/chiều cao vẫn nằm ở vùng tranh chấp và không đủ bằng chứng xe khách dài thì ưu tiên gán `van` và đối chiếu quy tắc với Lab Coach.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_033.jpg` / vật thể tại tọa độ `[256.50, 281.44, 312.45, 459.97]`[cite: 3]
- Dấu hiệu nhìn thấy: Phương tiện di chuyển giữa đường có cabin điều khiển tách biệt ở phía trước và phần sàn chở hàng/thùng xe phẳng kéo dài ở phía sau, gầm xe cao.
- Quy tắc áp dụng: Có thùng hàng, sàn chở hàng hoặc kết cấu chuyên dụng tách rời khỏi buồng lái thì gán vào `truck`[cite: 7]; không gán vào `van` (vì van phải là thân hộp kín một khối) hay `car`[cite: 7].
- Quyết định: Gán lớp `truck`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Giữ bounding box ôm sát phần nhìn thấy, đánh dấu `needs_review`[cite: 7] và kiểm tra kỹ phần khe hở giữa cabin và khoang sau; nếu không nhìn rõ kết cấu thùng chở hàng do góc chụp quá hẹp thì tham khảo ảnh liền trước/sau trong chuỗi hoặc nhờ Lab Coach phân xử.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_008.jpg` / vật thể tại tọa độ `[624.13, 220.16, 640.00, 274.63]`[cite: 3]
- Dấu hiệu nhìn thấy khi phóng 100%: Phần đuôi, cụm đèn hậu và một phần mui xe nhìn thấy sắc nét, nhưng toàn bộ nửa thân trước bị cắt cụt bởi mép phải của khung hình (tọa độ x chạm 640.00)[cite: 3].
- Giá trị `visibility`: `clear` (phần nằm trong ảnh có đường nét rõ ràng, không bị xe khác che đè lên)[cite: 7].
- Giá trị `boundary`: `truncated` (vật thể bị mép ảnh cắt ngang thân)[cite: 7].
- Trạng thái `review_state`: `confident` (đã xác định đủ căn cứ nhận dạng xe con dạng sedan/hatchback)[cite: 7].
- Lý do: Vật thể chạm sát mép viền ảnh nhưng phần hiển thị còn lại có đặc trưng đuôi đèn xe con rất rõ, thỏa mãn quy tắc "vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp" và "vẽ sát phần vật thể nhìn thấy, không ước lượng phần ngoài mép ảnh"[cite: 7].

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh[cite: 7].
- [x] Đã kiểm vật thể thiếu và trùng[cite: 7].
- [x] Đã kiểm lớp và hình học từng hộp[cite: 7].
- [x] Mỗi hộp có đủ ba thuộc tính[cite: 7].
- [x] Đã xử lý mọi hộp `needs_review`[cite: 7].
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu[cite: 7].
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi[cite: 7].
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu[cite: 7].
- [x] Số vật thể thực tế: 58 — 40–60 là mục tiêu khối lượng, không phải điểm cắt[cite: 1, 3, 7].