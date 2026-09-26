# Team

Điền trước phút 15. Thay mọi placeholder; còn sót thì `make status` báo ở gate G1.

- **Team:** Wibulord
- **Nhóm peer test bài của mình:** TODO (cặp A ↔ B; số nhóm lẻ thì ring 3 nhóm A → B → C → A — Lab Coach công bố)
- **Nhóm mình test bài của:** TODO
- **Problem family:** Traffic light: trạng thái đèn (`state`) + đèn có điều khiển xe mình không (`relevance`) tại giao lộ nhiều đầu đèn
- **Nguồn ảnh:** `bdd100k` (ảnh có đèn, gồm đêm và chạng vạng), `lisa` (clip dayClip5, 30 frame)

| Thành viên | Vai trò chính | File phụ trách |
|---|---|---|
| Hồ Minh Hậu (2A202602058) | TODO | spec owner | `01_problem_statement.md`, `02_guideline.md` |
| Trần Tuấn Anh (2A202602110) | TODO | CVAT owner | `03_ontology_and_cvat_setup.md`, `03_cvat_labels.json`, `sample_pack.csv`, `09_cvat_export_or_task_reference.txt` |
| Nguyễn Thế Anh (2A202602138) | TODO | gold owner (người duy nhất chạy `freeze`) | `04_edge_cases/` |
| Bùi Thanh Minh Hoàng (2A202602054) | TODO | QA owner | `05_qa_plan.md`, `06_calibration_report.csv` |
| Nguyễn Hải Minh (2A202602074) | TODO | handoff owner | `07_blind_handoff/`, `08_revision_log.md` |

Gợi ý chia vai (nhóm 2–3 người thì gộp): **spec owner** (`01`, `02`), **CVAT owner** (`03_*`, `sample_pack.csv`,
`09`), **gold owner** (`04_edge_cases/`), **QA owner** (`05`, `06`, `07_blind_handoff/`). Mỗi file một người sửa
chính để tránh xung đột git. Calibration thì mọi người cùng label.
