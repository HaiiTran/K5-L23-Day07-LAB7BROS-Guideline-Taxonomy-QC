# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 3
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 178 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 178 | 0.878 | 96.1% | 56.7% | 93.8% | 39/147 | 1 | ĐẠT |
| 137|151 | 13 | 56 | 0.915 | 92.9% | 51.8% | 89.3% | 6/64 | 1 | CHƯA ĐẠT |
| 137|170 | 13 | 65 | 0.856 | 100.0% | 47.7% | 95.4% | 12/58 | 0 | ĐẠT |
| 151|170 | 14 | 57 | 0.867 | 94.7% | 71.9% | 96.5% | 21/25 | 0 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 355 ({'bo_sot_thua': 159, 'iou_thap': 48, 'sai_lop': 7, 'sai_thuoc_tinh': 83, 'bat_dong_ignore': 56, 'co_can_review': 2}), trên 38 ảnh cần calibration.
