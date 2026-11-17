# Guideline gán nhãn — v0.1-draft — 137

> Bản đặc tả cá nhân khảo sát độc lập trên toàn bộ 40 ảnh (`data/images/img_001.jpg` – `img_040.jpg`) ở Pha 1, viết lại từ `guideline_v0_starter.md` theo khung 5 phần chuẩn.

- Phiên bản: `v0.1-draft` (Bản cá nhân Pha 1)
- Ngày: `2026-11-16`
- Người viết / duyệt: Thành viên `137`

---

## 1. Mục tiêu

- **Mục đích sử dụng nhãn:** Huấn luyện và đánh giá mô hình thị giác máy tính 2D phát hiện **người thật và phương tiện cơ giới đường bộ** (`Object Detection`) cho bài toán hỗ trợ lái xe nâng cao (ADAS / xe tự hành) và giám sát giao thông đô thị.
- **Tác động của sai số:**
  - **Bỏ sót (False Negative / Omission):** Cực kỳ nguy hiểm nếu bỏ sót người đi bộ sang đường (`img_014.jpg`, `img_034.jpg`), người đi xe đạp/xích lô (`img_006.jpg`, `img_023.jpg`), hoặc phương tiện đang lưu thông ở tiền cảnh/trung cảnh vì gây nguy cơ va chạm trực tiếp.
  - **Báo giả (False Positive / Commission):** Gán nhầm tượng điêu khắc (`img_017.jpg`, `img_027.jpg`), ma-nơ-canh (`img_033.jpg`), đồ chơi mô hình (`img_038.jpg`), chân dung trên biển quảng cáo (`img_018.jpg`), hoặc ảnh phản chiếu trong gương chiếu hậu (`img_020.jpg`) sẽ làm mô hình học sai đặc trưng hình học và phanh gấp vô cớ.
  - **Box không ôm sát (Loose / Cropped BBox):** Kéo giãn box theo vật mang vác quá khổ (`img_029.jpg`, `img_035.jpg`) hoặc gộp chung người với xe máy làm sai lệch ước lượng khoảng cách 2D–3D của hệ thống cảm biến.

---

## 2. Lớp và thuộc tính

### 2.1. Bảng định nghĩa lớp (Classes)

> **Lưu ý đề xuất cập nhật Taxonomy cho buổi họp Pha 2:** Qua khảo sát 40 ảnh, bộ dữ liệu xuất hiện nhiều **xe buýt (`bus`)** (`img_009.jpg`, `img_031.jpg`, `img_037.jpg`, `img_040.jpg`) và **xe ba bánh có động cơ (`tuk_tuk`)** (`img_004.jpg`, `img_016.jpg`, `img_025.jpg`, `img_035.jpg`, `img_040.jpg`) mà bản v0 chưa có lớp riêng. Trong bảng dưới đây, tôi định nghĩa đầy đủ 6 lớp gốc của v0 và **đề xuất bổ sung 2 lớp `bus`, `tuk_tuk`** khi nhóm chốt `taxonomy.json` v1.0.

| Lớp | Định nghĩa (một câu) | Ví dụ trong bộ 40 ảnh | Phản ví dụ / Dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở người từ 4–9 chỗ (sedan, hatchback, SUV, coupe, station wagon, taxi con) có 4 bánh và khoang hành khách khép kín hoặc mui trần. | Sedan/hatchback đỗ sau hàng rào (`img_018.jpg`), SUV/sedan trên đường mưa (`img_015.jpg`), xe AMC Pacer wagon (`img_024.jpg`), 8 ô tô con trên sàn xe lồng (`img_002.jpg`), xe diễu hành biển số `YR04 NUM` (`img_019.jpg`). | **Không gán:** ô tô mô hình đồ chơi trong tủ kính (`img_038.jpg`), khung vỏ xe vàng rỗng gắn trên nóc xe (`img_024.jpg`), ảnh xe phản chiếu trong gương (`img_020.jpg`), xe bán tải có thùng hàng rời (`truck`). |
| `truck` | Xe cơ giới vận tải có **thùng hàng tách biệt** với cabin lái (xe tải thùng hở/mui bạt/thùng kín, xe bán tải pickup, xe đầu kéo rơ-moóc chở xe). | Xe bán tải Suzuki trắng (`img_001.jpg`), xe đầu kéo chở ô tô (`img_002.jpg`), xe tải thùng kín trong kho (`img_007.jpg`), bán tải chở hàng (`img_012.jpg`), xe tải nhẹ CMC Veryca (`img_036.jpg`), bán tải nắp thùng cao (`img_037.jpg`). | **Không gán:** bia đá điêu khắc hình xe bán tải ở nghĩa trang (`img_027.jpg`). Dễ nhầm với `van` khi xe bán tải lắp nắp thùng cao (`img_037.jpg`) — phân biệt bằng cấu trúc đầu cabin rời và khe nối giữa cabin với thùng sau. |
| `van` | Xe thùng kín cỡ nhỏ/trung cấu trúc **liền khối (unibody/minivan/microvan)** giữa khoang lái và khoang sau, dùng chở người hoặc hàng nhẹ. | Xe minivan/passenger van màu bạc chạy ngang phía sau (`img_005.jpg`, `img_016.jpg`), xe microvan trắng đỗ bên trái (`img_005.jpg`). | Dễ nhầm với `truck` thùng kín (`img_007.jpg`, `img_036.jpg` có cabin rời gắn thùng hàng phía sau) hoặc `bus` cỡ lớn chở khách công cộng (`img_009.jpg`, `img_037.jpg`). |
| `bus` *(đề xuất thêm ở v1.0)* | Xe khách / xe buýt cỡ trung và lớn (từ 16 chỗ trở lên) thiết kế chuyên chở hành khách công cộng hoặc du lịch với khoang kính dài liền khối. | Xe khách/buýt màu kem–nâu đi qua ngã tư (`img_009.jpg`), xe buýt tuyến 62 ban đêm (`img_031.jpg`), 2 xe buýt GO Transit xanh–trắng (`img_037.jpg`), xe buýt vàng cam phía xa (`img_040.jpg`). | Nếu nhóm chưa thêm lớp `bus` vào taxonomy thì phải thống nhất gộp tạm vào `van` kèm `can_review = true`, tuyệt đối không để người gán `van`, người gán `truck`. |
| `tuk_tuk` *(đề xuất thêm ở v1.0)* | Phương tiện cơ giới **3 bánh** có mái che và khung cabin chở khách/hàng đặc trưng châu Á (tuk-tuk Thái Lan, auto-rickshaw Ấn Độ). | Hai xe tuk-tuk xanh đỗ vỉa hè (`img_004.jpg`), tuk-tuk chạy trên phố (`img_016.jpg`, `img_040.jpg`), auto-rickshaw màu vàng chở bao tải (`img_035.jpg`). | **Không gán:** xích lô đạp chân 3 bánh không động cơ (`img_006.jpg`, `img_023.jpg`, `img_034.jpg`, `img_039.jpg`) và mô tô 2 bánh gắn thuyền sidecar (`img_022.jpg` thuộc `motorcycle`). |
| `motorcycle` | Xe cơ giới 2 bánh (xe máy số, xe tay ga, mô tô phân khối lớn) hoặc mô tô gắn thuyền bên (sidecar) chạy bằng động cơ. | Xe máy đang lưu thông (`img_005.jpg`, `img_006.jpg`, `img_021.jpg`, `img_026.jpg`), xe máy đỗ thành dãy (`img_008.jpg`, `img_030.jpg`), mô tô cổ có sidecar (`img_022.jpg`). | **Không gán:** xe đạp không động cơ (`img_003.jpg`, `img_006.jpg`, `img_010.jpg`, `img_029.jpg`, `img_039.jpg`). Không gộp người ngồi vào box `motorcycle`. |
| `person` | Con người thật (người đi bộ, người đứng/ngồi quán xá, tài xế và hành khách đang ngồi trên xe máy, xe đạp, xích lô, tuk-tuk, ô tô). | Người đứng cạnh bán tải và khách ngồi quán (`img_001.jpg`), người bán hàng rong (`img_003.jpg`, `img_010.jpg`), người lái/ngồi xe máy (`img_005.jpg`, `img_026.jpg`), người đạp xích lô (`img_023.jpg`). | **Không gán:** tượng đồng/đá/nhựa (`img_011.jpg`, `img_017.jpg`), nhân vật hoạt hình giả (`img_019.jpg`), ảnh người trên biển quảng cáo/poster/decal kính (`img_018.jpg`, `img_030.jpg`, `img_040.jpg`), ma-nơ-canh và búp bê (`img_033.jpg`, `img_038.jpg`). |
| `ignore_region` | Vùng hình chữ nhật đánh dấu khu vực có đối tượng thật thuộc phạm vi nhưng **quá nhỏ (`< 20 px`), che khuất chồng lấn dày đặc (`> 70%`) hoặc nhòe không thể tách từng vật**. | Đám đông khán giả đứng dày đặc nhiều lớp sau rào (`img_019.jpg`), đám đông lễ hội ở xa (`img_011.jpg`), dãy người chờ xe buýt chồng lấn (`img_032.jpg`), luồng xe máy xa mờ ở ngã tư (`img_021.jpg`), lớp xe máy bị lấp sâu trong bãi (`img_030.jpg`). | Không dùng `ignore_region` để bọc các vật thể vốn không thuộc taxonomy (như tượng, đồ chơi, cây cối) hoặc lười gán các đối tượng tiền cảnh nhìn rõ. |

#### Xử lý vật không thuộc lớp nào (Out-of-taxonomy):
- **Phương tiện thô sơ không động cơ** (xe đạp, xích lô đạp chân, xe đẩy hàng tay ở `img_003.jpg`, `img_006.jpg`, `img_010.jpg`, `img_023.jpg`, `img_029.jpg`, `img_034.jpg`, `img_039.jpg`): **Không vẽ box** cho phương tiện thô sơ, nhưng **bắt buộc gán `person`** cho người điều khiển và hành khách ngồi trên đó.
- **Động vật thật hoặc tượng động vật** (chó ở `img_009.jpg`, chim hải âu ở `img_017.jpg`, tượng ngựa ở `img_017.jpg`, `img_028.jpg`): **Không gán nhãn**.

---

### 2.2. Thuộc tính (Attributes) và ngưỡng định lượng đo được

> **Lưu ý kỹ thuật CVAT:** Sử dụng đúng trường thuộc tính `occluded_level` trong bảng Objects của CVAT, không dùng phím tắt `Q` (cờ `occluded` mặc định của CVAT vì script kiểm tra không đọc cờ này).

| Thuộc tính | Kiểu | Giá trị cố định | Ý nghĩa & Ngưỡng định lượng cụ thể |
|---|---|---|---|
| `occluded_level` | `select` | `0` (mặc định) | **Không bị che (0%):** Quan sát thấy trọn vẹn 100% bề mặt đối tượng theo góc nhìn camera (hoặc bị che khuất không đáng kể `< 1%`). |
| `occluded_level` | `select` | `1` | **Che khuất nhẹ (1% – 30%):** Đối tượng bị vật khác che từ `1%` đến `30%` diện tích thực tế (ví dụ: người ngồi trước xe máy bị ghi-đông/gương che một phần chân ở `img_006.jpg`, xe con tầng trên bị thanh giằng cam che nhẹ ở `img_002.jpg`). |
| `occluded_level` | `select` | `2` | **Che khuất trung bình (31% – 70%):** Đối tượng bị che từ `31%` đến `70%` diện tích nhưng vẫn xác định rõ lớp và biên phần nhìn thấy (ví dụ: người ngồi giữa trên xe máy bị che khuất thân dưới ở `img_005.jpg`, ô tô sau hàng rào và biển hiệu ở `img_018.jpg`, tài xế ngồi trong cabin xe ở `img_004.jpg`, `img_019.jpg`). |
| `occluded_level` | `select` | `3` | **Che khuất nặng (> 70%):** Đối tượng bị che trên `70%` diện tích. **Quy tắc:** Nếu đối tượng đơn lẻ ở tiền cảnh/trung cảnh vẫn nhận diện chắc chắn 100% lớp (như người khách ngồi quán chỉ lộ phần đầu/vai sau lưng người khác ở `img_001.jpg`) thì gán `occluded_level = 3`; nếu nằm trong cụm dày đặc không thể phân định biên từng cá thể thì gộp vào `ignore_region`. |
| `truncated` | `select` | `no` (mặc định) | Toàn bộ phần nhìn thấy của đối tượng nằm hoàn toàn bên trong 4 cạnh khung ảnh (cách mép ảnh `>= 2 px`). |
| `truncated` | `select` | `yes` | Bất kỳ phần thực tế nào của đối tượng bị **cắt ngang bởi 1 trong 4 cạnh biên của bức ảnh** (ví dụ: đầu xe đỏ và đuôi xe bạc ở 2 mép `img_015.jpg`, thùng xe bán tải chạm mép phải `img_001.jpg`, `img_012.jpg`, người đi xe máy chạm mép dưới `img_005.jpg`, `img_040.jpg`). *Lưu ý: Vật bị che bởi vật khác ở giữa ảnh nhưng không chạm mép ảnh thì `truncated = no`.* |
| `can_review` | `checkbox` | `false` / `true` | Mặc định `false`. Tick `true` khi gặp tình huống biên lưỡng lự về lớp, ranh giới box hoặc mức che khuất để đưa ra thảo luận tại Pha 4–5. |

---

## 3. Quy tắc vẽ Bounding Box (kèm mã quy tắc `R-xx`)

- **`R-01` — Nguyên tắc ôm sát phần nhìn thấy (Visible Tight Box):**
  - Bounding box phải ôm sát cực trị (trên, dưới, trái, phải) của **phần thực tế nhìn thấy được (visible pixels)** của đối tượng, khe hở hoặc lẹm biên không vượt quá `2 px`.
  - **Không ước lượng vẽ trùm phần bị che khuất (No amodal completion):** Nếu nửa thân dưới của người bị xe che khuất, cạnh dưới của box `person` dừng ngay tại mép phần cơ thể nhìn thấy được.
  - **Trường hợp vật bị chia cắt thành nhiều mảnh rời (Split occlusion):** Nếu một vật chắn mảnh (như cột đèn, chấn song hàng rào ở `img_018.jpg`, thanh khung sắt xe lồng ở `img_002.jpg`, hoặc người đứng chắn trước đầu xe bán tải ở `img_001.jpg`) cắt đối tượng thành 2 phần nhưng ta nhìn rõ chúng thuộc cùng 1 đối tượng liền mạch, vẽ **1 bounding box duy nhất** bao quanh toàn bộ các phần nhìn thấy của đối tượng đó và gán `occluded_level` tương ứng (`1` hoặc `2`).

- **`R-02` — Quy tắc tách cặp Người và Phương tiện (Rider vs. Vehicle Separation):**
  - Người điều khiển/hành khách và phương tiện (`motorcycle`, `tuk_tuk`, `car`, xe đạp, xích lô) **luôn gán tách biệt thành các box riêng**.
  - Box `person`: Ôm trọn từ đỉnh đầu (bao gồm mũ bảo hiểm, nón lá đội trên đầu ở `img_003.jpg`, `img_005.jpg`, `img_040.jpg`) xuống tới mũi bàn chân/dép nhìn thấy và hai khuỷu tay/bàn tay nắm ghi-đông.
  - Box `motorcycle` / `tuk_tuk`: Ôm trọn từ mép ngoài bánh trước tới bánh sau/biển số, từ điểm thấp nhất của lốp xe lên tới đỉnh gương chiếu hậu/kính chắn gió/mái che của xe; **không** kéo đỉnh box lên theo đầu người ngồi.
  - Khi có nhiều người ngồi trên cùng một xe (`img_005.jpg`, `img_006.jpg`, `img_010.jpg`, `img_026.jpg`): Vẽ mỗi người 1 box `person` riêng biệt.

- **`R-03` — Quy tắc Phụ kiện gắn liền vs. Hàng hóa / Vật mang vác rời:**
  - **Người (`person`):** Bao gồm quần áo, mũ bảo hiểm, nón lá, ba lô/túi xách đeo sát người (`img_006.jpg`, `img_014.jpg`). **Không bao gồm** hàng hóa cồng kềnh tách rời cơ thể như đôi quang gánh mít (`img_003.jpg`), thúng xe đẩy (`img_010.jpg`), hoặc thanh nhôm dài hàng mét vác trên vai (`img_029.jpg`).
  - **Phương tiện (`truck`, `motorcycle`, `tuk_tuk`, `car`):** Bao gồm phụ kiện cố định theo xe (gương chiếu hậu, giá nóc cố định, nắp thùng `Master 75` trên `img_037.jpg`, thùng hàng sau xe máy ở `img_008.jpg`, thuyền sidecar gắn liền ở `img_022.jpg`). **Không bao gồm** hàng hóa rời chất vượt khỏi khung thân xe như kiện bao tải chất cao gấp đôi nóc tuk-tuk (`img_035.jpg`), tượng ngựa trắng đặt trên thùng bán tải (`img_028.jpg`), hay thanh nhôm dài trên xe máy (`img_029.jpg`).

- **`R-04` — Quy tắc Xe chở xe (Car Carrier / Multi-vehicle Transport):**
  - Với xe tải chuyên dụng chở các phương tiện khác trên sàn (`img_002.jpg`):
    1. Vẽ 1 box `truck` ôm trọn phần xe tải đầu kéo + khung rơ-moóc.
    2. Vẽ riêng từng box `car` cho mỗi chiếc ô tô thật đang nằm trên cả 2 tầng của rơ-moóc (gán `occluded_level = 1` hoặc `2` tùy mức bị thanh sắt cam che khuất).
  - *Lý do:* Các ô tô trên sàn đều là phương tiện thật có hình thái hoàn chỉnh; nếu không gán `car`, mô hình học sâu sẽ bị phạt (false positive penalty) khi phát hiện ra ô tô thật ở vị trí trên cao.

- **`R-05` — Quy tắc Loại trừ vật thể giả, tượng, hình in 2D, phản chiếu và xe chủ (Negative Cases):**
  - **Không gán nhãn** (để 0 box cho đối tượng đó) đối với:
    1. **Tượng điêu khắc, bia đá, mô hình hóa trang tĩnh:** Tượng đồng cưỡi ngựa (`img_017.jpg`), mô hình Predator (`img_011.jpg`), tượng đài xe bán tải bằng đá (`img_027.jpg`), tượng ngựa (`img_028.jpg`).
    2. **Ma-nơ-canh, thú nhồi bông, đồ chơi mô hình:** Ma-nơ-canh trong kính (`img_033.jpg`), chuột hoạt hình trắng gắn trên xe (`img_019.jpg`), ô tô mô hình và búp bê trong tủ kính (`img_038.jpg`), khung vỏ xe vàng rỗng trưng bày trên nóc ô tô (`img_024.jpg`).
    3. **Hình ảnh 2D trên biển quảng cáo, poster, băng-rôn, decal dán kính:** Chân dung trên billboard (`img_018.jpg`), poster ca sĩ (`img_030.jpg`), ảnh cô gái dán trên kính chắn gió taxi (`img_040.jpg`), hình cô gái in trên vách kính (`img_033.jpg`).
    4. **Phản chiếu qua gương / kính và Xe chủ (Ego-vehicle):** Ảnh ô tô phản chiếu trong gương chiếu hậu và phần thân xe chủ nơi đặt máy ảnh (`img_020.jpg`), hình ảnh xe tải phản chiếu/nhìn xuyên mờ qua lớp kính tiệm (`img_033.jpg`).

- **`R-06` — Quy tắc Kích thước tối thiểu (`20 px`) và vùng `ignore_region`:**
  - Mọi đối tượng đơn lẻ có **chiều cao phần nhìn thấy `< 20 px`** (`min_box_height_px = 20`) thì **không vẽ box cá nhân**.
  - Sử dụng `ignore_region` để khoanh gọn theo cụm đối với:
    1. Đám đông người hoặc cụm xe ở xa có chiều cao `< 20 px` (`img_011.jpg`, `img_013.jpg`, `img_025.jpg`, `img_031.jpg`, `img_034.jpg`).
    2. Đám đông người hoặc bãi đỗ xe đứng ken đặc nhiều lớp chồng lấn (`occluded > 70%`) không thể phân tách chính xác biên từng cá thể (`img_019.jpg`, `img_021.jpg`, `img_030.jpg`, `img_032.jpg`).
  - **Lưu ý COCO:** Các bức ảnh sau khi áp dụng quy tắc loại trừ mà không có bất kỳ box nào (`img_017.jpg`, `img_020.jpg`, `img_027.jpg`, `img_033.jpg`, `img_038.jpg`) vẫn phải giữ nguyên trong Task CVAT và xuất đầy đủ trong tệp JSON với `0` annotation.

---

## 4. Ca khó (Edge Cases khảo sát thực tế từ 40 ảnh)

> Bảng dưới đây chọn lọc **5 ca khó tiêu biểu nhất** đại diện cho các nhóm bất đồng chính (xe chở xe, người đi xe máy bị che/cắt mép, tượng/phản chiếu loại theo quy tắc, phương tiện chở hàng quá khổ và ca biên cần hỏi reviewer).

| Ảnh | Tình huống | Rule ID | Phân loại | Quyết định | Lý do |
|---|---|---|---|---|---|
| `img_002.jpg` | Xe đầu kéo Renault chở 8 ô tô con trên sàn 2 tầng; 1 người đứng cạnh bánh sau bên phải. | `R-04`, `R-01` | **Cần hỏi Reviewer / Họp Pha 2** | Vẽ 1 box `truck` ôm cả đầu kéo + rơ-moóc; vẽ thêm **8 box `car`** riêng cho 8 xe con trên 2 tầng (`occluded_level=1` hoặc `2`); 1 box `person` ở đuôi xe. | 8 xe trên sàn là ô tô thật có hình thái hoàn chỉnh; cần chốt chung ở Pha 2 để tránh lệch giữa người gán 1 box `truck` và người gán đủ 9 box xe. |
| `img_005.jpg` | 4 người ngồi trên các xe máy cắt ngang cận cảnh dưới mép ảnh; phía sau có xe minivan bạc chạy ngang. | `R-01`, `R-02` | Xử lý theo quy tắc | Tách riêng 4 box `person` (ôm cả mũ bảo hiểm) và các box `motorcycle` ở mép dưới (`truncated=yes`); người bị che thân dưới gán `occluded_level=2`; xe bạc phía sau gán `van`. | Giữ nguyên tắc tách rời người và xe máy; phần xe máy ở mép dưới vẫn lộ rõ đầu xe, tay lái và cao `>= 20 px`. |
| `img_017.jpg` | Tượng đồng Công tước Wellington cưỡi ngựa (tương tự phản chiếu gương `img_020.jpg`, ma-nơ-canh `img_033.jpg`). | `R-05` | **Loại theo quy tắc** | **Không gán bất kỳ box nào** (xuất COCO với `0` annotation). | Tượng đồng, ma-nơ-canh và ảnh phản chiếu trong gương không phải người/xe thật trên đường; nếu gán `person` sẽ tạo báo giả (False Positive). |
| `img_035.jpg` | Xe auto-rickshaw (tuk-tuk) 3 bánh màu vàng chở chồng bao tải chất cao gấp đôi thân xe. | `R-03`, `R-02` | **Cần hỏi Reviewer / Họp Pha 2** | Đề xuất thêm lớp `tuk_tuk`; vẽ box chỉ ôm phần khung thân xe màu vàng (từ bánh xe tới nóc cabin), **không** kéo box bao trùm đống bao tải trên nóc. | Hàng hóa rời chất cao làm biến dạng tỷ lệ hình học của xe; rất dễ gây lệch IoU nếu hai người không thống nhất biên trên của box. |
| `img_037.jpg` | Xe bán tải GO Transit màu trắng lắp nắp thùng kín cao (`Master 75 Truck Cap`) đỗ cạnh 2 xe buýt. | `R-03` | **Cần hỏi Reviewer / Họp Pha 2** | Gán `truck` và tick `can_review=true` cho xe trắng ở giữa; đề xuất thêm lớp `bus` cho 2 xe buýt xanh–trắng và gán `person` cho người đi bộ. | Xe có gầm và cabin rời của bán tải (`truck`) nhưng lắp thùng phụ kiện kín cao giống hệt `van`; cần họp nhóm chốt lớp duy nhất. |

---

## 5. QC và phiên bản

### 5.1. Ngưỡng kiểm soát chất lượng (QC Thresholds)
Giữ nguyên các ngưỡng định lượng chuẩn từ `submission/taxonomy.json`:
- Chiều cao box tối thiểu (`min_box_height_px`): **`20 px`**.
- Ngưỡng ghép cặp đối tượng giữa các người gán (`match_iou`): **`0.50`**.
- Ngưỡng IoU đạt chuẩn cho từng cặp box: **`>= 0.85`** với vật lớn (cạnh ngắn `>= 50 px`) và **`>= 0.70`** với vật nhỏ (`< 50 px`).
- Ngưỡng đồng thuận toàn lô (IAA): IoU trung bình trên các cặp ghép **`>= 0.85`**, tỷ lệ đồng thuận lớp **`>= 95%`**.
- Tỷ lệ ảnh có lỗi tối đa cho phép khi duyệt QC để Release (`error_rate_img_max`): **`<= 2%`** (tương đương `0/40` ảnh lỗi trên lô 40 ảnh).

### 5.2. Quy trình xử lý ca mơ hồ
1. **Trong khi gán độc lập (Pha 3):** Nếu gặp đối tượng đã vẽ được box nhưng lưỡng lự về lớp hoặc biên box (như `img_011.jpg`, `img_035.jpg`, `img_037.jpg`), giữ box theo quyết định sát quy tắc nhất và tick `can_review = true`. Nếu gặp vùng nghi ngờ chưa thể vẽ box, ghi nhận vào `submission/qc/image_issues.csv` sau khi khóa nhãn.
2. **Khi họp Calibration (Pha 5):** Người điều phối và cả nhóm đối chiếu từng `case_id` trong `calibration_candidates.csv`, chốt quyết định kèm mã quy tắc `R-01`..`R-06` vào `calibration_decisions.csv` và nâng cấp đặc tả lên `v1.1`.

### 5.3. Lịch sử phiên bản

| Phiên bản | Thay đổi chính | Lý do (dẫn chiếu ca cụ thể) |
|---|---|---|
| `v0` | Bản khởi đầu của giảng viên (6 lớp sơ sài, thuộc tính chưa có ngưỡng %, chưa có quy tắc ca khó). | Khởi tạo bài lab. |
| `v0.1-draft` | Bản khảo sát độc lập của thành viên `137`: Định lượng hóa ngưỡng `%` cho `occluded_level` (`0%`, `1–30%`, `31–70%`, `>70%`) và `truncated`; thiết lập 6 quy tắc `R-01`..`R-06`; đề xuất bổ sung 2 lớp `bus` và `tuk_tuk`; lập bảng phân tích 5 ca khó điển hình. | Xử lý các ca gây mơ hồ trên bộ 40 ảnh: xe chở xe (`img_002.jpg`), người đi xe máy bị che/cắt mép (`img_005.jpg`), tượng/phản chiếu (`img_017.jpg`, `img_020.jpg`), tuk-tuk chở hàng (`img_035.jpg`), bán tải nắp thùng cao (`img_037.jpg`). |
