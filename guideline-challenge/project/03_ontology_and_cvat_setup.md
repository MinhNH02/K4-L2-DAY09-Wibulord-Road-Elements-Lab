# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây. Thay mọi
placeholder mới là xong (gate G2).

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_light` | rectangle | class | — | — | — | Một box cho **một đầu đèn** (1 vỏ đèn). Downstream cần vị trí từng đầu đèn; rectangle đủ cho object nhỏ và dễ chấm tolerance 3 px |
| `state` | — | attribute của `traffic_light` (select) | `red`, `yellow`, `green`, `off`, `unknown` | `__undefined__` | Không (ảnh tĩnh) | Màu bóng đang sáng, là thứ planning dùng để dừng/đi. `unknown` cho ảnh loá/mờ/tối; không có `flashing_yellow` vì ảnh tĩnh không nhận ra được |
| `pictogram` | — | attribute của `traffic_light` (select) | `circle`, `arrow_left`, `arrow_right`, `arrow_straight`, `other`, `unknown` | `__undefined__` | Không (ảnh tĩnh) | Hình bóng đang sáng. Cần để suy ra `relevance` (mũi tên rẽ khác hướng xe mình → `not_relevant`, rule R3) |
| `relevance` | — | attribute của `traffic_light` (select) | `relevant`, `not_relevant`, `unknown` | `__undefined__` | Không (ảnh tĩnh) | Đèn có điều khiển xe mình không, theo R0–R5 của guideline. Là decision `critical` chính |
| `escalate` | — | attribute của `traffic_light` (checkbox) | `true`, `false` | `false` | Không (ảnh tĩnh) | Thể hiện quyết định ESCALATE (guideline thiếu rule) trong file export, tách khỏi UNKNOWN (ảnh thiếu bằng chứng) |

## Class hay attribute

- **Chỉ một class `traffic_light`.** Mọi đầu đèn có cùng geometry và cùng rule vẽ box; khác nhau chỉ ở màu, hình và
  việc có điều khiển xe mình hay không. Tách class theo màu hay theo mũi tên sẽ nổ tổ hợp (5 state × 6 pictogram × 3
  relevance) và buộc vẽ lại box khi đổi một thuộc tính.
- **`state`, `pictogram`, `relevance` là attribute** vì là thuộc tính của cùng một object, và downstream đọc chúng
  riêng rẽ (màu cho dừng/đi, relevance để chọn đèn nào nghe theo).
- **`escalate` là attribute trên box**, không phải tag cho cả ảnh: ca mơ hồ thường chỉ ở một đầu đèn, còn các đầu
  đèn khác trong cùng ảnh vẫn quyết được.
- **Default gây bias:** nếu default là một màu hay `relevant`, người vẽ quên đổi sẽ tạo nhãn sai "im lặng". Vì vậy
  ba select đều default `__undefined__`: còn giá trị này trong export nghĩa là chưa gán, reviewer bắt được ngay.
  Riêng `escalate` default `false` là có rủi ro: người vẽ quên tích thì ca mơ hồ bị giấu. Checklist cuối guideline
  (mục 10) có câu kiểm cho việc này.
- Đèn người đi bộ, xe đạp **không** có class riêng vì nằm ngoài scope (guideline mục 5). Không vẽ gì cho chúng.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.75.1 tại http://localhost:8080
- **Tên task calibration** (có version guideline, ví dụ `team07-calib-v1`): `wibulord-calib-v1-<tên bạn>` (mỗi người
  một task, ví dụ `wibulord-calib-v1-minh`)
- **Guide của task đã dán `02_guideline.md`?** chưa
- **Nhóm dùng Track hay Shape, vì sao:** Shape. Task ảnh tĩnh, mỗi ảnh gán nhãn độc lập (guideline mục 8), kể cả các
  frame LISA liên tiếp; Track sẽ kéo attribute từ frame trước sang frame sau, trái với rule "không suy ra từ frame
  khác".

## Setup test

Một thành viên **chưa tham gia setup** mở task và trả lời: label gì, dùng tool nào, gán attribute nào, khi nào
escalate. Ghi lại ai test và chỗ họ vấp:

TODO — chưa test. Sau khi CVAT owner tạo task, một bạn chưa setup mở task, trả lời 4 câu trên, ghi tên người test và
chỗ vấp vào đây.
