# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Khánh Chi
- MSSV: 2a202603338
- Email: trankhanhchii20@gmail.com
- Lớp: K5 – L2
- Repo bài làm: https://github.com/trankhanhchii-20/K5-L2-Day02-Trankhanhchi-2a202603338-ObjectDetection
- Link Colab: chưa có (chưa train YOLO, xem mục 5)

## 2. Schema và mini guideline

Schema 4 lớp: `0 car`, `1 truck`, `2 bus`, `3 van`. Định nghĩa lớp và các ca mơ hồ ghi trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

## 3. Annotation và self-QC

- Gán nhãn bằng CVAT (task "Day 2", job 3), export sang định dạng YOLO.
- Số file nhãn train: 50/50 (`drive_001`–`drive_050`), tổng 715 box: car 569, truck 21, bus 69, van 56.
- Checker `tools/check_yolo_labels.py`: đạt, 50/50 file hợp lệ.
- Nhãn chưa được sửa trước khi chấm với gold.

## 4. Gold QC và rework

Lệnh chạy trên nhãn export từ CVAT, chưa sửa:

```bash
python tools/evaluate_annotations.py --student-labels dataset/labels/train --gold-labels gold_train_labels --output outputs/annotation_evaluation.json
```

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 379 | chưa rework |
| FP | 336 | chưa rework |
| FN | 274 | chưa rework |
| Precision | 0.5301 | chưa rework |
| Recall | 0.5804 | chưa rework |
| F1 | 0.5541 | chưa rework |
| mean_iou_of_matches | 0.7691 | chưa rework |
| passed | false (cần F1 ≥ 0.80, IoU ≥ 0.70) | chưa rework |

Kết quả theo từng ảnh: `outputs/annotation_evaluation.json`.

**Phân tích lỗi** (tính riêng, không thay kết quả chính thức ở trên):

- mean IoU của các box đã ghép là 0.769 (đạt), nên điểm yếu nằm ở F1 chứ không phải độ ôm sát.
- Nếu chỉ xét vị trí (IoU ≥ 0.5, bỏ qua lớp) thì F1 ≈ 0.72, với 494 box khớp; khoảng 115 box đúng vị trí nhưng khác lớp.
- Nhầm lớp trên các box khớp vị trí (gold → nhãn của tôi): 47 `van`→`bus`, 33 `truck`→`van`, 20 `bus`→`truck`, 7 `truck`→`car`, 6 `car`→`van`, 2 `van`→`car`.
- Diện tích box trung bình trong gold: lớp 1 ≈ 0.017, lớp 2 ≈ 0.044, lớp 3 ≈ 0.061; trong nhãn của tôi: truck 0.042, bus 0.050, van 0.017. Mẫu nhầm lặp theo vòng và kích thước box cho thấy thứ tự ID lớp của gold có thể khác `data.yaml` (gold lớp 1 giống `van`, lớp 2 giống `truck`, lớp 3 giống `bus`). Đây là giả thuyết, chưa được xác nhận.
- Nếu hoán gold theo giả thuyết đó: precision 0.670, recall 0.734, F1 0.700, mean IoU 0.781, vẫn dưới 0.80, tức vẫn còn lỗi bỏ sót và box thừa.
- Các ảnh lệch nhiều nhất: `drive_024`, `drive_050`, `drive_003`, `drive_045`, `drive_012`, `drive_049`, `drive_027`, `drive_026`.
- Nhiều box thừa ở ảnh có xe nhỏ ở xa (`drive_001`: 15 box so với 5 của gold; `drive_018`: 10 so với 2; `drive_028`: 11 so với 3; `drive_049`: 19 so với 6). Chưa đối chiếu từng ảnh nên chưa kết luận nguyên nhân.

**Rework:** chưa thực hiện.

## 5. Kết quả YOLO

Chưa thực hiện. Nhãn chưa đạt ngưỡng F1 ≥ 0.80 theo GUIDE.md nên chưa chạy notebook train. Chưa có số liệu precision, recall, mAP50, mAP50–95 và chưa trả lời bốn câu hỏi cuối notebook.

## 6. Khó khăn và cách xử lý

| Bước | Lỗi quan sát | Cách đã thử | Kết quả |
|---|---|---|---|
| Đánh giá với gold | F1 = 0.554, thấp hơn ngưỡng 0.80 | Tách lỗi nhầm lớp, lệch vị trí, thừa/thiếu box | Nhầm lớp có quy luật, nghi khác thứ tự ID lớp so với schema; chưa xác nhận |
| Đánh giá với gold | Nhiều box thừa ở ảnh có xe nhỏ/xa | So số box theo ảnh với gold | Chưa rà lại bằng mắt, chưa sửa |

## 7. Tự đánh giá theo rubric

- Đã có: 50 file nhãn đúng cấu trúc, kết quả evaluate với gold, phân tích lỗi bằng số liệu.
- Chưa đạt: ngưỡng annotation (F1 0.554 < 0.80; mean IoU 0.769 đạt).
- Chưa làm: rework nhãn, train và evaluate YOLO.

## 8. Công cụ AI và tài liệu tham khảo

- Claude (Anthropic): dùng ở các thao tác kỹ thuật sau khi tôi gán nhãn trên CVAT: export nhãn sang YOLO, chạy `check_yolo_labels.py` và `evaluate_annotations.py`, phân tích lỗi so với gold và soạn báo cáo từ kết quả chạy thật. Việc gán nhãn 50 ảnh do tôi thực hiện.
- Tài liệu: GUIDE.md, SUBMISSION.md, RUBRIC.md của repo đề bài. Không đính kèm gold train, token hay mật khẩu.
