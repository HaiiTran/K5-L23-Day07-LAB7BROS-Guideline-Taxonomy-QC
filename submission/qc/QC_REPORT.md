# Báo cáo QC — K5-L23-Day07

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: Thực hành đặc tả và QC nhãn bounding box 2D; tập ảnh tuyển chọnchưa đánh giá tính đại diện cho dữ liệu vận hành.
- Nguồn và phép sử dụng dữ liệu: CC BY-SA 4.0
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json` SHA-256 `71efd6791e61fd16a1b3f359df2153080772636d36f3caf89f7213bbd3738fd2`; `submission/qc/labels_calibrated.json` SHA-256 `2bcf67a82e8fd2e3b32e56ff5efe268306cde2bf6277ce751361fa2667eedae2`.
- Guideline và taxonomy khi gán / khi bàn giao: phiên bản taxonomy v1.0, guideline phiên bản 1.1; dùng `guideline_v1.1_calibrated.md` và `taxonomy_v1.1.json`. 

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 lượt; 3 người gán (137, 151, 170)
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 178 cặp box ghép; IoU trung bình 0.878; đồng thuận lớp 96.1%; mẫu số 178.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 39/147 box chưa ghép (A/B); 1 cặp bỏ qua do ignore; 56 ca bất đồng ignore theo bảng IAA.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: `img_002` được chốt chỉ có `truck` chở xe theo R-08

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 355 ca trong `calibration_candidates.csv` cùng quyết định bổ sung `CP5-M035`; sổ quyết định ghi mã 151. Các quyết định được dẫn theo case trong `calibration_decisions.csv`.
- Tác động v1.1 lên nhãn và ảnh: Làm rõ ranh giới pickup và thân/vỏ trưng bày; phạm vi calibration là toàn bộ 40 ảnh. Export CVAT vòng 2 được kiểm tra bằng `build_final.py`; lớp pickup trong `img_001`, `img_012`, `img_028` hiện là `truck` theo taxonomy.
- Tham chiếu bằng chứng khi Rework: Vòng 1 ghi lỗi cần hiệu chỉnh; sau cập nhật CVAT, vòng 2 rà đủ 40 ảnh và các ảnh đã sạch.

## 4. Kết quả duyệt theo hai vòng

- Vòng 1 — HOLD: duyệt 40/40 ảnh; 6/40 ảnh có lỗi (15.0%), 7/356 annotation có lỗi (2.0%), không ghi vật bỏ sót. Phiếu ghi 1 vấn đề dữ liệu; lỗi gồm 4 sai lớp, 3 sai vị trí/kích thước và 1 box thừa. Đây là kết quả của bản nhãn trước khi sửa CVAT.
- Vòng 2 — RELEASE: duyệt 40/40 ảnh; 0/40 ảnh lỗi, 0/355 annotation lỗi và không ghi vật bỏ sót. Đây là kết quả QC cuối sau lần cập nhật CVAT và export hiệu chỉnh.

### Chi tiết vòng 2

- Vòng QC: 2
- Người review và ngày: 151, 2026-11-17.
- Đã duyệt / cả lô: 40/40 ảnh, chế độ full; 38 ảnh thuộc nhóm ca khó và 2 ảnh còn lại.
- Ảnh lỗi / ảnh đã duyệt: 0/40 = 0.0%.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 0/355 = 0.0%.
- Vật bỏ sót, ảnh và vùng: 0 vật bỏ sót được ghi riêng trong phiếu vòng 2.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: Không còn lỗi được ghi nhận trong vòng 2; các box đã được rà lại sau cập nhật CVAT, gồm `img_001`, `img_003`, `img_010`.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: `build_final.py` chặn export khi tên hash không khớp các đầu vào; reviewer xác nhận các hiệu chỉnh ở `img_001`, `img_003`, `img_010` và ghi các ảnh PASS.

## 5. Quyết định bàn giao

- Quyết định: Release
- Lý do, người nhận và phạm vi sử dụng: Theo phiếu vòng 2, đã duyệt full 40/40, không ghi nhận lỗi hoặc vật bỏ sót; tỷ lệ lỗi 0% đạt ngưỡng QC. 
- Nếu còn việc:
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: `img_001.jpg` #1, `img_003.jpg` #23, `img_010.jpg` #97/#102/#103 → đối chiếu R-02/R-06 với `labels_calibrated.json`; các ảnh được ghi sạch ở `reviews/round-02/qc_review.csv`. Kết quả và checksum vòng ở `reviews/round-02/qc_result.json` và `review_manifest.json`.
