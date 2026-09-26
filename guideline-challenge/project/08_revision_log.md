# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản nháp đầu, gộp từ 2 bản nháp (`Traffic_Light_Annotation_Guideline.docx`, `Guideline_Gan_Nhan_Den_Giao_Thong.md`) vào khung 10 mục | Bỏ phần video/track và quy trình sản xuất; thêm `unknown` cho `state`; bỏ `flashing_yellow` (ảnh tĩnh không nhận ra); tách UNKNOWN (ảnh thiếu bằng chứng) với ESCALATE (guideline thiếu rule); đơn vị = 1 đầu đèn; mặc định xe mình đi thẳng; đèn người đi bộ ngoài scope | LISA01, LISA20 (mũi tên trái đỏ, đèn tròn đổi đỏ → xanh), BDD16 (negative) |
| v2 | (1) R3: không suy relevance từ vị trí đầu đèn trên ảnh; mũi tên luôn qua R3. (2) Định nghĩa "thấy mặt đèn"; đèn quay ngang/chấm màu ở mép vỏ → IGNORE, không escalate. (3) Bỏ mâu thuẫn mục 3/mục 5: không thấy vỏ thì vẽ khi vùng sáng có màu ≥ 8 px, box ôm vùng sáng. (4) `escalate` chỉ cho danh sách mục 7, không bật kèm `unknown`. (5) Đèn tắt không đọc được hình → `pictogram = unknown`, không `other`. (6) Thêm 5 ví dụ calibration vào mục 9 | Calibration 5 người (đồng thuận count 28,6%, attribute 0%): 3/5 gán mũi tên trái là relevant (critical); 3/5 vẽ vỏ quay ngang; BDD25 người vẽ 0 người vẽ 3 box; 3/5 bỏ sót đầu đèn tắt ở LISA16; escalate dùng từ 0 đến 8 box/người | `06_calibration_report.csv` 8 dòng; `06_calibration_measure.csv`: LISA08/LISA16 relevance + pictogram, BDD07 count 2–6, BDD25 count 0–3, BDD18, LISA16 đầu đèn xa trái |
