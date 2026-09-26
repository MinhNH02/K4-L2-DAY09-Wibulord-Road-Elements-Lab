# QA plan + quality gates

Không được viết "reviewer kiểm tra lại". Phải có sampling, metric, threshold và action khi fail. Thay mọi placeholder
mới là xong (gate G6).

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate. Ghi cụ thể cho project của nhóm:

- **Ai review, review bao nhiêu:** QA owner (Bùi Thanh Minh Hoàng) review mọi batch. Một batch = một job CVAT của một
  annotator. Reviewer không review bài của chính mình; bài của QA owner do spec owner (Hồ Minh Hậu) review.
  - **100%** các box có `relevance = relevant`, mọi box `state = red`, và mọi box `escalate = true`. Đây là các box
    quyết định dừng/đi.
  - **100%** ảnh có tag `critical`, `conflict` hoặc `low_visibility` trong `sample_pack.csv`.
  - **20%** số ảnh còn lại, chọn ngẫu nhiên, tối thiểu 2 ảnh mỗi batch, để bắt lỗi bỏ sót và vẽ thừa.
  - **100%** batch đầu tiên (20 ảnh đầu) của annotator mới.
- **Chọn sample theo rule nào:** theo rủi ro trước, ngẫu nhiên sau (như trên). Nếu một batch bị REWORK thì batch kế
  tiếp của annotator đó được review 100%.
- **Issue được ghi ở đâu, đóng thế nào:** reviewer mở **Issue** trong CVAT ngay trên object, câu mở đầu ghi mức độ
  `[C]` / `[M]` / `[m]` / `[Q]` và số mục guideline bị vi phạm (ví dụ `[C] 4.3 R3: mũi tên trái phải not_relevant`).
  Annotator sửa rồi trả lời issue; reviewer kiểm lại và bấm **Resolve**. Batch chỉ qua gate khi không còn issue `[C]`
  hoặc `[M]` đang mở.
- **Khi phát hiện guideline gap thì update và version ra sao:** issue `[Q]` và box `escalate = true` chuyển cho spec
  owner. Spec owner quyết trong cùng buổi, viết rule hoặc ví dụ mới vào `02_guideline.md`, tăng `Version`, ghi một
  dòng vào `08_revision_log.md` kèm bằng chứng (sample_id, issue). CVAT owner dán lại Guide vào mọi task đang mở. Ảnh
  đã gán nhãn theo rule cũ mà bị rule mới ảnh hưởng thì annotator sửa lại trong batch kế tiếp.

## Defect severity

Mapping theo downstream contract: module dừng/đi chỉ nghe các đầu đèn `relevant`, nên lỗi trên đèn `relevant` nặng
hơn lỗi trên đèn `not_relevant`.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Lỗi làm sai quyết định dừng/đi: đèn điều khiển xe mình bị bỏ sót, bị gán `not_relevant`, hoặc sai `state` giữa red/yellow/green; đèn không điều khiển xe mình bị gán `relevant` | Mũi tên trái đỏ gán `relevant` (EC01); đèn đỏ trông cam gán `yellow` (EC02); bỏ sót đèn nhắc lại (EC09) | Sửa ngay; review lại 100% ảnh của annotator trong batch; ghi vào critical escape nếu lọt qua gate |
| Major | Sai object hoặc attribute không làm đổi quyết định dừng/đi: vẽ đèn thuộc danh sách IGNORE; sai `state` của đèn `not_relevant`; box lệch > 3 px một cạnh; còn `__undefined__`; bỏ sót đèn `not_relevant` | Vẽ vỏ quay ngang (EC04), đèn người đi bộ (EC10); `relevance = __undefined__` | Sửa trong batch trước khi qua gate |
| Minor | Sai không ảnh hưởng quyết định và không làm lệch thống kê đáng kể | `pictogram = unknown` thay vì `circle` trên đèn `not_relevant`; bật `escalate` thừa cho ca đã có rule | Sửa khi rework; lặp ≥ 3 lần trong batch thì coaching |
| Question | Ca guideline chưa có rule, hoặc hai rule mâu thuẫn | Box `escalate = true` (EC12); issue annotator hỏi | Chuyển spec owner; quyết thành rule/ví dụ mới, tăng version |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Critical defect rate | Số lỗi Critical / số đầu đèn `relevant` được review × 100 | Chỉ đèn `relevant` quyết định dừng/đi; đo đúng rủi ro downstream |
| Relevant-light recall | Số đầu đèn `relevant` annotator vẽ đúng / số đầu đèn `relevant` reviewer xác nhận | Bỏ sót đèn điều khiển xe mình là lỗi nguy hiểm nhất |
| Extra-box rate | Số box vẽ thừa (IGNORE, phản chiếu, đèn quay ngang) / tổng số box | Calibration cho thấy 3/5 người vẽ đèn quay ngang |
| State accuracy (relevant) | Số box `relevant` đúng `state` / số box `relevant` | Màu của đèn relevant là input trực tiếp của planning |
| Geometry pass rate | Số box có mọi cạnh lệch ≤ 3 px / số box được review | Tolerance đã chốt ở mục 3 guideline |
| Escalate rate | Số box `escalate = true` / tổng số box | Cao (> 10%) nghĩa là guideline thiếu rule; calibration v1 dao động 0–8 box/người |
| Inter-annotator agreement | Tỉ lệ `agree` trong `make calib` (count và attribute) | Đo độ rõ của guideline; baseline v1: count 28,6%, attribute 0% |

Metric high-risk tách riêng: **critical defect escape rate** = số lỗi Critical phát hiện **sau** khi batch đã PASS
(ở vòng QA sau, ở blind test hoặc do downstream báo) / tổng số lỗi Critical tìm được. Mục tiêu 0%; bất kỳ escape nào
cũng mở review lại toàn bộ batch đó.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Giải thích trade-off cost/risk.

```text
PASS if:
  0 lỗi Critical trong phần được review
  AND relevant-light recall = 100%
  AND số lỗi Major ≤ 2 trên 100 box
  AND geometry pass rate ≥ 95%
  AND không còn box nào __undefined__, không còn issue [C]/[M] đang mở
REWORK if: 1–2 lỗi Critical, hoặc Major > 2/100 box, hoặc geometry pass rate < 95%
  → annotator sửa, reviewer review lại 100% batch, batch sau của annotator đó review 100%
REJECT / ESCALATE if: ≥ 3 lỗi Critical trong một batch, hoặc cùng một lỗi xuất hiện ở ≥ 2 annotator
  → dừng batch, spec owner coi là guideline gap: sửa guideline, tăng version, gán nhãn lại các ảnh bị ảnh hưởng
```

Trade-off: review 100% các đèn `relevant`, đèn đỏ và ảnh rủi ro tốn công, nhưng mỗi ảnh chỉ có 1–5 đầu đèn `relevant`
nên chi phí nhỏ so với rủi ro xe vượt đèn đỏ. Phần còn lại chỉ lấy mẫu 20% vì lỗi ở đèn `not_relevant` không đổi quyết
định dừng/đi. Ngưỡng Critical = 0 là cố ý khắt khe; Major ≤ 2/100 và geometry ≥ 95% nới hơn vì đèn nhỏ ở xa khó ôm
chính xác từng pixel. Luật "cùng lỗi ở ≥ 2 annotator thì REJECT" dựa trên calibration: khi 3/5 người cùng sai một
chỗ, nguyên nhân nằm ở guideline chứ không ở người vẽ, nên sửa rule rẻ hơn coaching từng người.
