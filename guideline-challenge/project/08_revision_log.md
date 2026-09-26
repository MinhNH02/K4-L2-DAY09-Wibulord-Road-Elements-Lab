# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản nháp đầu, gộp từ 2 bản nháp (`Traffic_Light_Annotation_Guideline.docx`, `Guideline_Gan_Nhan_Den_Giao_Thong.md`) vào khung 10 mục | Bỏ phần video/track và quy trình sản xuất; thêm `unknown` cho `state`; bỏ `flashing_yellow` (ảnh tĩnh không nhận ra); tách UNKNOWN (ảnh thiếu bằng chứng) với ESCALATE (guideline thiếu rule); đơn vị = 1 đầu đèn; mặc định xe mình đi thẳng; đèn người đi bộ ngoài scope | LISA01, LISA20 (mũi tên trái đỏ, đèn tròn đổi đỏ → xanh), BDD16 (negative) |
