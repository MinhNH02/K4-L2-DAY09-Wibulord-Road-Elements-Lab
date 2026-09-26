# Problem statement + downstream contract

Tối đa nửa trang, viết **trước khi mở CVAT**. Đây là bằng chứng của gate G1 (topic lock). Thay mọi placeholder
mới là xong.

## Bài toán

Tại giao lộ có nhiều đầu đèn (đèn tròn, đèn mũi tên rẽ, đèn của giao lộ xa hơn, đèn hướng ngang), xác định **đầu
đèn nào điều khiển làn xe mình đang đi** và **đèn đó đang sáng màu gì**. Khó ở chỗ đèn nhỏ, xa, bị chói, ban đêm, và
chỉ có một ảnh tĩnh nên không biết chắc xe mình định đi thẳng hay rẽ.

## Downstream contract

1. **Downstream task / model / user là ai?** Module ra quyết định dừng/đi (planning) của hệ thống hỗ trợ lái. Nhãn
   dùng để train và đánh giá model phát hiện đèn + phân loại `state` + `relevance`.
2. **Output annotation nào thực sự cần?** Box `traffic_light` cho từng đầu đèn quay về phía xe mình; attribute
   `state` (red / yellow / green / off / unknown), `relevance` (relevant / not_relevant / unknown), `pictogram` (tròn hay
   mũi tên hướng nào, để suy ra relevance) và cờ `escalate`.
3. **Failure nào gây hậu quả lớn nhất?** Đèn **relevant** bị bỏ sót, bị gán `not_relevant`, hoặc sai `state` giữa đỏ
   và xanh: xe có thể vượt đèn đỏ. Đây là các decision `critical` trong gold.
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Annotator bật `escalate = true` trên box, ghi
   câu hỏi vào `07_blind_handoff/clarification_log.csv` (blind) hoặc báo spec owner (nội bộ). Spec owner quyết định,
   thêm rule/edge case và ghi vào `08_revision_log.md`.

## Scope

- **Trong scope (bắt buộc label):** mọi đầu đèn giao thông cho xe, nhìn thấy mặt đèn quay về phía xe mình, vỏ đèn cao
  ≥ 8 px. Gồm cả đèn của giao lộ xa hơn (gán `not_relevant`).
- **Ngoài scope (ignore):** đèn quay lưng/quay ngang (chỉ thấy vỏ, không thấy mặt đèn), đèn cho người đi bộ, đèn chớp
  cảnh báo trên biển báo, đèn đường ray, đèn phản chiếu trên kính/xe, đèn cao < 8 px.
- **Geometry tolerance:** box ôm phần vỏ đèn nhìn thấy, không lấy cột, cần treo hay biển kèm theo. Mỗi cạnh lệch
  ≤ 3 px là đạt; đèn bị che thì chỉ ôm phần nhìn thấy.

## Output chấm được

- **LABEL:** có box `traffic_light` kèm `state` và `relevance`.
- **IGNORE:** không vẽ box (đèn ngoài scope ở trên).
- **UNKNOWN:** `state = unknown` hoặc `relevance = unknown` khi ảnh không đủ bằng chứng (chói, mờ, quá tối).
- **ESCALATE:** `escalate = true` khi nhìn rõ nhưng guideline chưa có rule để quyết định.
- **Geometry:** vị trí và độ ôm của box theo tolerance trên.

Mọi quyết định trên đều nằm trong file export CVAT for images 1.1 (shape + attribute).

## Dữ liệu và giới hạn

- Nguồn: `bdd100k` (các ảnh có đèn, gồm ảnh đêm và chạng vạng) và `lisa` (một clip 30 frame liên tiếp, dayClip5).
  Dự kiến dùng khoảng 15 ảnh: 3–4 example, 6 calibration, 5 blind.
- Blind chỉ lấy ảnh BDD chưa dùng ở example/calibration, vì các frame LISA liên tiếp gần như giống nhau.
- Ảnh BDD có đèn ít, nên chia split phải tiết kiệm; LISA dùng cho example/calibration.
- Ảnh tĩnh: không có mục temporal. Không biết ý định rẽ của xe mình, nên mặc định xe **đi thẳng**, trừ khi làn xe
  mình có mũi tên rẽ bắt buộc nhìn thấy trên mặt đường.
- Toàn bộ là đèn kiểu Mỹ (BDD, LISA), chưa kiểm với đèn kiểu Việt Nam.
