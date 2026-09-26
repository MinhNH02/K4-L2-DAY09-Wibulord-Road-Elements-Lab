# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

Các card dưới đây chỉ dùng ảnh split `example`/`calibration` và đều đã có rule tương ứng trong guideline v2. Card về
ảnh blind sẽ thêm cùng lúc viết `gold_decisions.csv`. Bằng chứng calibration lấy từ `06_calibration_report.csv`.

---

CASE ID: EC01
Sample: LISA08 (lặp lại ở LISA01, LISA16, LISA20)
Scene: Giao lộ lớn, chạng vạng; cần treo có đầu mũi tên trái và đầu đèn tròn
Observation: Đầu mũi tên trái đỏ (x≈733–762, y≈130–197) nằm gần giữa khung hình hơn đầu đèn tròn; không thấy mũi tên rẽ sơn trên làn xe mình
Decision: LABEL
Expected: traffic_light; state=red; pictogram=arrow_left; relevance=not_relevant; escalate=false
Rationale: Downstream dùng relevance để chọn đèn nghe theo. Mặc định xe mình đi thẳng (mục 4.3), mũi tên trái đi qua R3. Gán relevant thì xe có thể dừng/đi theo đèn rẽ
Common mistake: Suy relevance từ vị trí đầu đèn trên ảnh. Calibration: 3/5 người gán relevant
Diversity: critical · conflict

---

CASE ID: EC02
Sample: LISA01 (giống hệt ở LISA08)
Scene: Đầu đèn tròn đỏ trên cần treo, chạng vạng, bóng đỏ trông cam
Observation: Bóng sáng ở vị trí trên cùng của đầu đèn dọc 3 bóng, màu cam đỏ
Decision: LABEL
Expected: state=red; pictogram=circle; relevance=relevant
Rationale: Quy tắc vị trí mục 4.1. Gán yellow cho đèn đỏ đang điều khiển xe mình là lỗi critical (xe không dừng)
Common mistake: Chọn yellow theo màu nhìn thấy
Diversity: critical · low_visibility

---

CASE ID: EC03
Sample: LISA08 (lặp lại ở LISA16)
Scene: Góc bên kia giao lộ, cạnh đầu đèn xa bên phải
Observation: Vỏ tối nhỏ, chỉ thấy một chấm đỏ ở mép vỏ (x≈826–847, y≈400–429), không thấy mặt bóng đèn
Decision: IGNORE
Expected: Không có box
Rationale: Định nghĩa "thấy mặt đèn" (v2): chấm màu ở mép vỏ = đèn quay ngang, không điều khiển hướng xe mình
Common mistake: Vẽ box với unknown và bật escalate. Calibration: 3/5 người vẽ, mỗi người 2 box
Diversity: ambiguity · small_far

---

CASE ID: EC04
Sample: BDD07
Scene: Phố dân cư ban ngày, giao lộ có đèn vỏ vàng kiểu New York
Observation: Mỗi bên đường một cặp vỏ vàng: vỏ quay về xe mình sáng xanh; vỏ ngay cạnh quay ngang, chỉ thấy cạnh vỏ
Decision: LABEL 2 đầu đèn quay về xe mình; IGNORE 2 vỏ quay ngang và 2 đầu đèn rất xa (x≈490, x≈600)
Expected: 2 box (x≈230–241, y≈123–159) và (x≈679–689, y≈146–171): green; circle; relevant
Rationale: 1 vỏ = 1 box, chỉ vẽ đèn thấy mặt. Vỏ quay ngang điều khiển đường cắt ngang, không phải xe mình
Common mistake: Vẽ cả vỏ quay ngang (unknown/not_relevant). Calibration: số box từ 2 đến 6
Diversity: conflict · small_far

---

CASE ID: EC05
Sample: BDD25
Scene: Đại lộ đô thị lúc chạng vạng, mặt đường ướt
Observation: 3 bóng xanh ở giao lộ phía trước, không thấy vỏ; vùng sáng có màu rộng khoảng 10 px
Decision: LABEL (v1: ESCALATE vì mục 3 và mục 5 mâu thuẫn; v2 đã có rule)
Expected: 3 box ôm vùng sáng có màu: green; circle; relevant; lamp_only=true; escalate=false
Rationale: Rule v2: không thấy vỏ thì vẽ khi vùng sáng có màu ≥ 8 px. Đèn xanh relevant bị bỏ sót làm model yếu ở cảnh chạng vạng
Common mistake: Bỏ qua vì không thấy vỏ, hoặc ôm cả quầng sáng. Calibration: người vẽ 0, người vẽ 1, người vẽ 3 box
Diversity: low_visibility · escalation (đường escalate đã dẫn tới rule mới)

---

CASE ID: EC06
Sample: BDD18
Scene: Phố ban đêm
Observation: Cuối đường có 2 chấm xanh rất xa, vùng sáng khoảng 7 px; sát phải là đèn người đi bộ đỏ/trắng
Decision: IGNORE
Expected: Không có box
Rationale: Vùng sáng < 8 px không đủ để khẳng định là đèn và không đo được geometry; đèn người đi bộ ngoài scope
Common mistake: Vẽ 2 chấm xanh với escalate. Calibration: 1/5 người vẽ
Diversity: small_far · low_visibility · negative

---

CASE ID: EC07
Sample: LISA16
Scene: Frame vừa chuyển pha đỏ → xanh
Observation: Đầu đèn xa bên trái (x≈678–689, y≈359–386) tắt hẳn, chỉ thấy vỏ tối trên nền tối
Decision: LABEL
Expected: state=off; pictogram=unknown; relevance=relevant
Rationale: Thấy mặt đèn nhưng không bóng nào sáng → off. Không đọc được hình → unknown, không dùng other. Vẫn là đèn điều khiển xe mình
Common mistake: Bỏ sót vì không có bóng sáng. Calibration: 3/5 người bỏ sót, 1 người gán pictogram=other
Diversity: ambiguity · temporal (frame chuyển pha, gán độc lập)

---

CASE ID: EC08
Sample: LISA08 (lặp lại ở LISA16)
Scene: Đầu đèn tròn trên cần treo bên phải, nền là tán cây tối
Observation: Không thấy vỏ, chỉ thấy bóng đỏ (LISA08, x≈1147–1166, y≈189–213) hoặc bóng xanh (LISA16, x≈1146–1163, y≈229–253)
Decision: LABEL
Expected: Box ôm vùng sáng có màu; state theo màu (không dùng quy tắc vị trí); circle; relevant; lamp_only=true
Rationale: Rule v2 mục 3: không ước lượng vỏ, không ôm quầng. Box to nhỏ khác nhau làm geometry không chấm được
Common mistake: Bỏ sót đèn trong vùng tối, hoặc kéo box ước lượng cả vỏ. Calibration: 2 người bỏ sót, 3 box lệch nhau tới 40 px
Diversity: occlusion · low_visibility

---

CASE ID: EC09
Sample: LISA01 (lặp lại ở LISA08, LISA20)
Scene: Góc bên kia giao lộ
Observation: 2 đầu đèn tròn nhỏ (vỏ cao khoảng 20–30 px) quay về xe mình, cùng pha với đèn trên cần treo
Decision: LABEL
Expected: Mỗi đầu 1 box: state theo màu; circle; relevant
Rationale: Đèn nhắc lại cho cùng hướng (R4). Downstream cần mọi đầu đèn relevant để đối chiếu khi đèn chính bị che
Common mistake: Gán not_relevant hoặc unknown vì ở xa. Calibration: theanh gán not_relevant/unknown
Diversity: small_far · critical

---

CASE ID: EC10
Sample: BDD12
Scene: Ngã tư ban ngày cạnh cây xăng
Observation: Chỉ có đèn người đi bộ hình bàn tay đỏ trên cột bên phải; không có đèn cho xe
Decision: IGNORE
Expected: Không có box
Rationale: Đèn người đi bộ ngoài scope (mục 5). Vẽ nhầm thành đèn đỏ relevant sẽ làm xe dừng vô cớ
Common mistake: Vẽ bàn tay đỏ thành state=red
Diversity: negative · ambiguity

---

CASE ID: EC11
Sample: BDD16
Scene: Xe đi dưới cầu vượt
Observation: Đèn trần tròn sáng, đèn hậu đỏ của xe trước, vệt đỏ phản chiếu trên nắp capo, biển tròn màu cam
Decision: IGNORE
Expected: Không có box
Rationale: Nguồn sáng tròn không phải đèn giao thông, ảnh phản chiếu không phải vật thật
Common mistake: Vẽ đèn hậu hoặc phản chiếu thành đèn đỏ
Diversity: negative

---

CASE ID: EC12
Sample: Không có trong 84 ảnh của lab (ca giả định, giữ để annotator biết cách escalate)
Scene: Đầu đèn 5 bóng ở giao lộ có pha rẽ bảo vệ
Observation: Hai bóng cùng sáng trên một đầu đèn: đỏ tròn và mũi tên xanh
Decision: ESCALATE
Expected: 1 box; state theo bóng tròn (red); pictogram=circle; relevance theo R0–R5; escalate=true
Rationale: Guideline chưa quyết đầu đèn hai pha cùng lúc điều khiển xe mình thế nào; downstream cần biết đi hay dừng nên phải có người quyết (mục 7)
Common mistake: Tự chọn mũi tên xanh và không escalate
Diversity: escalation
