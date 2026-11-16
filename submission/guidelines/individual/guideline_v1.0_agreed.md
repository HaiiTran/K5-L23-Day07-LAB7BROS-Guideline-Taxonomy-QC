# Guideline gán nhãn — v0 (bản khởi đầu)

- Phiên bản: v0
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0
- Người viết / duyệt: 151

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện** trên ảnh đường phố
(ví dụ cho camera giao thông hoặc xe tự lái).

## 2. Lớp và thuộc tính

### Lớp

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con, SUV. | Sedan, hatchback, wagon, SUV/crossover đang chạy hoặc đỗ. | Xe tải có thùng hàng riêng là `truck`; xe thùng kín cỡ nhỏ là `van`. |
| `truck` | Xe tải, xe có thùng hàng riêng. | Xe tải thùng, xe tải sàn, xe tải chở ô tô. | Ô tô con/SUV chở đồ bên trong vẫn là `car`; xe thùng kín cỡ nhỏ dạng van là `van`. |
| `van` | Xe thùng kín cỡ nhỏ/trung cấu trúc liền khối giữa khoang lái và khoang sau, dùng chở người hoặc hàng nhẹ. | Xe giao hàng thân kín cỡ nhỏ, khoang hàng liền khối với cabin. | Xe tải có thùng hàng riêng là `truck`; SUV chở người là `car`. |
| `motorcycle` | Xe gắn máy, mô tô. | Xe máy/scooter hai bánh có hoặc không có người lái. | Xe đạp không động cơ không thuộc lớp này; tuk-tuk/xe ba bánh gán `tuk_tuk`, không ép sang `motorcycle`. |
| `person` | Người đi bộ hoặc người ngồi trên xe. | Người đi bộ, người lái hoặc ngồi sau xe máy, người ngồi trong xe nếu nhìn thấy rõ. | Tượng, mannequin, người trong tranh/ảnh/màn hình hoặc hình phản chiếu không phải người thật trong cảnh. |
| `bus` | Xe khách / xe buýt cỡ trung và lớn (từ 16 chỗ trở lên) thiết kế chuyên chở hành khách công cộng hoặc du lịch với khoang kính dài liền khối. | Xe buýt đô thị và xe khách. | Xe van/minivan thuộc `van`; xe chở hàng thuộc `truck`. |
| `tuk_tuk` | Xe cơ giới ba bánh kiểu tuk-tuk, dùng chở người hoặc hàng. | Tuk-tuk | Không gán tuk-tuk thành `motorcycle`. |
| `ignore_region` | Vùng không gán từng vật. | Vùng nhỏ hoặc dày đặc của đối tượng mục tiêu không thể tách box ổn định. | Không dùng cho nền trống, vật ngoài taxonomy hoặc đối tượng lớn có thể gán riêng. Không gán thuộc tính cho lớp này. |

### Thuộc tính

| Thuộc tính | Giá trị cố định | Quy tắc |
|---|---|---|
| `occluded_level` | `0` | Không bị vật khác che, hoặc phần bị che dưới 10%. |
|  | `1` | Bị che từ 10% đến dưới 30%. |
|  | `2` | Bị che từ 30% đến dưới 60%. |
|  | `3` | Bị che từ 60% trở lên nhưng vẫn nhận ra lớp và còn phần nhìn thấy để vẽ box. |
| `truncated` | `no` | Toàn bộ vật nằm trong khung ảnh, dù có bị vật khác che. |
|  | `yes` | Một phần vủa vật nằm ngoài mép ảnh. |
| `can_review` | bỏ tick | Lớp, phạm vi vật và box được xác định theo quy tắc. |
|  | tick | Có từ hai cách hiểu hợp lý trở lên sau khi áp dụng quy tắc. |

## 3. Quy tắc vẽ box

- **`R-01`:** Gán box cho mọi đối tượng thuộc `car`, `truck`, `van`, `motorcycle`, `person`, `bus`, `tuk_tuk` trong ảnh. Với đối tượng là xe (`car`, `truck`, `van`, `motorcycle`, `bus`, `tuk_tuk).
- **`R-02`:** Box ôm sát vật. Box không ôm bóng đổ, khoảng trống, người/xe kế bên hay vật đang chở. Các phần nhìn thấy rời nhau nhưng thuộc cùng một vật vẫn nằm trong cùng một box. Không ước lượng vẽ trùm phần bị che khuất: Nếu nửa thân dưới của người bị xe che khuất, cạnh dưới của box person dừng ngay tại mép phần cơ thể nhìn thấy được.
- **`R-03`:** Trong trường hợp vật gán nhãn bị che: Chỉ gán nếu đối tượng không bị che quá 80%, vẫn có thể xác định lớp và cao ít nhất 20 px. Box ôm phần nhìn thấy; đặt `occluded_level` theo tỷ lệ ước lượng.
- **`R-04`:** Đối tượng bị cắt mép: gán phần nhìn thấy, box chạm mép ảnh và `truncated=yes`.
- **`R-05`:** Đối tượng mục tiêu cao dưới 20 px: không vẽ box riêng; gộp vùng nhỏ/dày liên quan vào `ignore_region`. Đối tượng nhỏ nhưng tách biệt không cần đưa vào một vùng bao quá rộng.
- **`R-06`:** Khu vực đối tượng đông: vẽ từng đối tượng ở rìa ngoài nếu các tượng có thể phân định; đặt `ignore_region` chỉ trên cụm đằng sau không phân định rõ ràng. Không dùng một box `person` bao nhiều người.
- **`R-07`:** Một phương tiện có nhiều bộ phận liền nhau vẫn là một đối tượng/box (ví dụ cabin và thùng xe tải, thân và rơ-moóc chở xe nếu liền thành tổ hợp đang kéo). Không tách bánh, cabin, hàng hóa thành vật thể phương tiện riêng. Không mở rộng box để bao người hoặc hàng hóa được chở riêng
- **`R-08`:** Đối tượng phản chiếu trong gương, kính, vũng nước; ảnh trên biển quảng cáo, màn hình, tranh hoặc ảnh chụp; tượng, ma-nơ-canh, đồ chơi: không gán.
- **`R-09`:** Xe ego (xe mang camera): nếu một phần xe của camera xuất hiện ở mép ảnh, không gán nhãn cho phần xe ego.
- **`R-10`:** Xe biến dạng/xe trưng bày nhưng là phương tiện thật vẫn phân loại theo cấu tạo. Xe đồ chơi, mô hình hoặc đạo cụ không gán, kể cả có hình dáng giống xe thật.
- **`R-11`:** Không dùng `ignore_region` để che vùng lớn chỉ vì khó hoặc ngoài phạm vi; vùng phải sát cụm đối tượng mục tiêu không thể tách. Nếu không chắc chính vùng/đối tượng có phải mục tiêu thật hay không, đánh dấu `can_review` và ghi chú.

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_002 | Xe tải chở nhiều ô tô | R6 | `truck` cho xe chở và `car` cho mỗi xe nhìn được | Xe chở và hàng xe là các đối tượng riêng. |
| img_003, img_010 | Người hoặc xe máy quá đông và che khuất nhau | R2, R5, R7 | Tách và gán nhãn một vài người và xe nhận diện được; vùng quá dày/nhỏ phía sau dùng `ignore_region`. | Tránh suy diễn phần bị che. |
| img_014, img_031 | Chuyển động nhòe/ánh sáng yếu | R2, R4, R13 | Gán khi vẫn nhận diện được đối tượng và box hợp lý; đặt `can_review` nếu lớp hoặc ranh giới không chắc. | Nhòe không tự động biến vật thành vùng ignore. |
| img_035 | Xe tuk-tuk chở chồng bao tải chất cao gấp đôi thân xe. | R2 | Vẽ box chỉ ôm phần khung thân xe màu vàng (từ bánh xe tới nóc cabin), **không** kéo box bao trùm đống bao tải trên nóc. | Hàng hóa rời chất cao làm biến dạng tỷ lệ hình học của xe; rất dễ gây lệch IoU nếu hai người không thống nhất biên trên của box. |

**Rule IDs:** R1 taxonomy/phân loại; R2 vật bị che/nhỏ; R3 cắt mép; R4 box phần nhìn thấy và chất lượng ảnh; R5 cụm đông; R6 xe chở xe; R7 người và xe; R9 vật thể giả/ảnh phản chiếu; R10 xe chủ; R11 đồ chơi/trưng bày; R12 ngoài taxonomy; R13 `can_review`.

## 5. QC và phiên bản

| Tiêu chí | Yêu cầu |
|---|---|
| Độ phủ lượt gắn | Đủ 100% lượt được phân công. |
| IoU trung bình của IAA | ≥0.85 |
| Đồng thuận lớp trên các cặp box ghép được | ≥95% |
| Ngưỡng so sánh box | Cao ≥50 px: IoU ≥0.85; nhỏ hơn: ≥0.70 |
| Tỷ lệ ảnh có lỗi tối đa | ≤2% |
| Phạm vi review cuối | Toàn bộ lô ảnh |

**Quy trình xử lý ca mơ hồ:** Nếu gặp đối tượng đã vẽ được box nhưng lưỡng lự về lớp hoặc biên box (như `img_011.jpg`, `img_035.jpg`, `img_037.jpg`), giữ box theo quyết định sát quy tắc nhất và tick `can_review = true`.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v1 | Cập nhật QC | - |
| v1 | Cập nhật ca khó | xe tuk-tuk chở hàng hóa quá cao (img_035) |
| v1 | Chỉnh sửa R-1 | Vẽ box cả gương |





