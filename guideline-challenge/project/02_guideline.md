# Annotation guideline — Trạng thái đèn giao thông + đèn nào điều khiển xe mình

**Version:** v2

Nhóm Wibulord · công cụ CVAT · label `traffic_light` (rectangle) · export **CVAT for images 1.1**.

**Nguyên tắc cốt lõi:** chỉ ghi điều nhìn thấy trong ảnh. Không đoán màu, không đoán hướng rẽ. Ảnh không đủ bằng
chứng thì chọn `unknown`. Nhìn rõ nhưng guideline không có rule để quyết thì bật `escalate`. Rule không có trong file
này thì không tồn tại.

Từ ngữ dùng trong file:

- **Xe mình (ego):** xe gắn camera.
- **Đầu đèn:** một vỏ đèn (housing) có 1–5 bóng. Hai vỏ trên cùng một cần treo là hai đầu đèn.
- **Giao lộ gần nhất:** giao lộ đầu tiên xe mình sẽ đi vào, tức vạch dừng hoặc vạch người đi bộ đầu tiên phía trước.
  Nếu xe mình đã qua vạch dừng thì vẫn là giao lộ đang đi qua.
- **Thấy mặt đèn:** thấy ít nhất một bóng tròn hoặc mũi tên nằm **trọn trong mặt vỏ**, nhìn gần như thẳng (sáng hay
  tắt đều được). Chỉ thấy cạnh/lưng vỏ, hoặc chỉ thấy một chấm màu ở **mép** hay **cạnh** vỏ, là đèn quay ngang:
  không thấy mặt đèn.
- **Vùng sáng có màu:** phần bóng đèn đang sáng có màu rõ (đỏ/vàng/xanh), **không** tính quầng mờ lan ra xung quanh.

## 1. Objective + scope

**Mục đích:** dữ liệu cho module quyết định dừng/đi của hệ thống hỗ trợ lái. Mỗi đầu đèn cần có vị trí (box), màu đang
sáng (`state`) và câu trả lời "đèn này có điều khiển xe mình không" (`relevance`).

**Trong scope, bắt buộc vẽ:** mọi đầu đèn giao thông cho **xe cơ giới** thoả cả ba điều kiện:

1. **thấy mặt đèn** quay về phía xe mình (định nghĩa ở trên);
2. đủ lớn: vỏ đèn cao **≥ 8 px** trong ảnh gốc; nếu không thấy vỏ (ban đêm, nền tối) thì **vùng sáng có màu rộng
   ≥ 8 px**;
3. là vật thật, không phải ảnh phản chiếu.

Đầu đèn của giao lộ xa hơn hoặc của đường khác vẫn vẽ, rồi gán `relevance = not_relevant`.

**Ngoài scope, không vẽ:** xem mục 5.

## 2. Annotation unit

- Đơn vị là **một ảnh tĩnh**. Mỗi ảnh gán nhãn độc lập với mọi ảnh khác.
- Một object là **một đầu đèn**: 1 vỏ đèn = 1 box.
  - Hai đầu đèn trên cùng một cần treo (ví dụ đầu mũi tên trái và đầu đèn tròn) là **2 box**.
  - Một đầu đèn 3 bóng hay 5 bóng đều là **1 box**. Không vẽ box riêng cho từng bóng.
  - Không dùng một box bao nhiều đầu đèn.
- Không vẽ cột, cần treo, biển báo gắn cạnh đèn.

## 3. Geometry rule

- Hình: **rectangle**.
- **Box ôm sát phần vỏ đèn nhìn thấy** (visible, không amodal):
  - gồm vỏ đèn và mái che bóng (visor);
  - **không** gồm cột, cần treo, dây, biển báo cạnh đèn;
  - không gồm tấm viền đen phía sau (backplate) nếu phân biệt được với vỏ. Không phân biệt được thì ôm phần tối liền
    khối.
- **Bị che một phần:** chỉ ôm phần vỏ còn nhìn thấy. Không ước lượng phần bị che.
- **Bị cắt ở mép ảnh:** box dừng đúng mép ảnh. Box không vượt ra ngoài ảnh.
- **Không ôm theo quầng sáng:** ban đêm bóng đèn loá rộng hơn vỏ đèn. Thấy vỏ thì box theo vỏ đèn.
- **Không thấy vỏ** (ban đêm, chạng vạng, đèn chìm trong tán cây tối): box ôm **vùng sáng có màu** của bóng đang sáng
  và bật **`lamp_only = true`**. Không ước lượng vỏ, không kéo box xuống chỗ các bóng tắt mà mắt không thấy, không ôm
  quầng. Vùng sáng có màu rộng < 8 px thì không vẽ (mục 1). Cách xác định và gán đủ attribute: mục 6.1.
- **Tolerance:** mỗi cạnh box lệch ≤ **3 px** so với mép vỏ đèn là đạt.
- Mẹo trên CVAT: zoom tới khi đầu đèn cao ít nhất khoảng 1/4 màn hình rồi mới kéo box.

## 4. Taxonomy

Một class: `traffic_light`. Mọi thông tin khác là attribute trên box.

| Attribute | Kiểu CVAT | Giá trị | Mặc định |
|---|---|---|---|
| `state` | select | `red` / `yellow` / `green` / `off` / `unknown` | `__undefined__` (bắt buộc chọn) |
| `pictogram` | select | `circle` / `arrow_left` / `arrow_right` / `arrow_straight` / `other` / `unknown` | `__undefined__` (bắt buộc chọn) |
| `relevance` | select | `relevant` / `not_relevant` / `unknown` | `__undefined__` (bắt buộc chọn) |
| `escalate` | checkbox | `true` / `false` | `false` |
| `lamp_only` | checkbox | `true` / `false` | `false` |

Không được nộp box nào còn `__undefined__`.

`lamp_only = true` khi **không thấy vỏ** và box chỉ ôm vùng sáng của bóng đèn (mục 3, mục 6.1). Thấy vỏ thì để
`false`. Box ôm bóng nhỏ hơn box ôm vỏ khoảng 3 lần; cờ này cho downstream tách hai loại box khi train và khi chấm
geometry.

### 4.1. `state`: màu bóng đang sáng

| Giá trị | Khi nào |
|---|---|
| `red` | Bóng đang sáng là đỏ. Đèn đỏ trên camera hay ra **cam đỏ**: xem quy tắc vị trí bên dưới trước khi chọn `yellow`. |
| `yellow` | Bóng đang sáng là vàng hổ phách, ở vị trí giữa của đèn dọc 3 bóng. |
| `green` | Bóng đang sáng là xanh. Đèn xanh trên camera hay ra **xanh ngọc / cyan**. |
| `off` | Thấy rõ mặt đèn nhưng không bóng nào sáng. |
| `unknown` | Không đọc được màu, **và** không dùng được quy tắc vị trí: loá, cháy sáng, mờ, quá tối, quá nhỏ. |

**Quy tắc vị trí:** với đầu đèn dọc 3 bóng, bóng sáng ở **trên = `red`**, **giữa = `yellow`**, **dưới = `green`**.
Màu bị cháy trắng hoặc lệch tông nhưng thấy rõ bóng sáng nằm ở vị trí nào thì gán theo vị trí. Không thấy vị trí thì
chọn `unknown`.

Quy tắc vị trí **chỉ dùng khi thấy vỏ**. Không thấy vỏ (`lamp_only = true`) thì không biết bóng nằm ở vị trí nào: gán
theo màu nhìn thấy; đỏ cam không phân biệt được với vàng thì chọn `unknown`. Không suy "bóng nằm cao nên là đỏ".

Hai bóng cùng sáng trên một đầu đèn (ví dụ đỏ tròn + mũi tên xanh): gán theo **bóng tròn** và bật `escalate`.

### 4.2. `pictogram`: hình của bóng đang sáng

- `circle`: bóng tròn trơn.
- `arrow_left` / `arrow_right` / `arrow_straight`: bóng mũi tên theo hướng đó.
- `other`: hình khác của đèn cho xe (mũi tên quay đầu, chữ X…).
- `unknown`: thấy là mũi tên nhưng không đọc được hướng, hoặc đèn `off` mà không đọc được hình. Không đọc được hình
  thì chọn `unknown`, **không** chọn `other`.

Đầu đèn tắt (`off`) nhưng vẫn đọc được hình trên mặt kính thì gán theo hình đó.

### 4.3. `relevance`: đèn có điều khiển xe mình không

Trả lời theo thứ tự, dừng ở bước đầu tiên khớp:

| Bước | Điều kiện | Gán |
|---|---|---|
| R0 | Không xác định được xe mình đang ở làn nào so với giao lộ (không thấy mặt đường, vạch, hướng đường) | `unknown` |
| R1 | Đầu đèn thuộc giao lộ **xa hơn** giao lộ gần nhất | `not_relevant` |
| R2 | Đầu đèn điều khiển **đường khác**: đường song song bên kia dải phân cách, đường nhánh, hướng ngược chiều | `not_relevant` |
| R3 | Đầu đèn **mũi tên** chỉ hướng xe mình không đi (xem giả định hướng đi bên dưới) | `not_relevant` |
| R4 | Đầu đèn tròn, hoặc mũi tên đúng hướng xe mình đi, của giao lộ gần nhất, quay mặt về xe mình | `relevant` |
| R5 | Đầu đèn ở giao lộ gần nhất nhưng không chắc nó thuộc R1–R4 | `unknown` |

**Giả định hướng đi:** ảnh tĩnh không cho biết xe mình định đi đâu, nên **mặc định xe mình đi thẳng**. Chỉ khác khi
làn xe mình có **mũi tên rẽ bắt buộc nhìn thấy trên mặt đường**, hoặc biển "ONLY" treo trên làn xe mình. Khi đó xe
mình đi theo hướng mũi tên. Không dùng hành vi xe khác để đoán hướng đi.

**Không suy `relevance` từ vị trí đầu đèn trên ảnh.** Đầu đèn nằm gần giữa khung hình, hay ngay phía trên xe mình,
**không** có nghĩa là nó điều khiển xe mình. Đầu đèn mũi tên luôn đi qua R3: mũi tên trái chỉ `relevant` khi thấy
mũi tên rẽ trái sơn trên làn xe mình; không thấy thì `not_relevant`. Gán nhầm mũi tên rẽ là `relevant` là lỗi
**critical**.

**Đèn nhắc lại:** một giao lộ thường có nhiều đầu đèn cho cùng hướng, trên cần treo và ở góc bên kia giao lộ. Mọi đầu
đèn đó đều theo R4, tức là `relevant`. Có nhiều đầu đèn `relevant` trong cùng một ảnh là bình thường.

`relevance` không phụ thuộc `state`: đèn `off` hoặc `state = unknown` vẫn phải gán `relevance`.

## 5. Inclusion / exclusion

**Vẽ (LABEL):**

- Đầu đèn cho xe cơ giới thoả điều kiện mục 1, kể cả đèn tắt, đèn nhỏ, đèn bị che một phần, đèn của giao lộ xa.
- Đầu đèn mũi tên (rẽ trái, rẽ phải, đi thẳng).

**Không vẽ (IGNORE):**

| Object | Vì sao dễ nhầm |
|---|---|
| Đèn quay lưng hoặc quay ngang: chỉ thấy cạnh/lưng vỏ, hoặc chỉ thấy chấm màu ở mép/cạnh vỏ (không thấy mặt đèn). Không vẽ và **không** escalate | Vẫn là đèn giao thông nhưng không điều khiển hướng này. Hay gặp: vỏ đèn vàng đứng cạnh đầu đèn quay về xe mình |
| Đèn cho người đi bộ (hình người, bàn tay, đếm ngược) | Cùng màu đỏ/xanh |
| Đèn cho xe đạp, đèn đường sắt, đèn chớp cảnh báo trên biển báo | Không thuộc scope |
| Đầu đèn có vỏ cao < 8 px; hoặc không thấy vỏ và vùng sáng có màu rộng < 8 px | Không đủ để khẳng định là đèn, không đo được geometry |
| Ảnh phản chiếu của đèn trên kính chắn gió, nắp capo, mặt đường ướt, cửa kính | Không phải vật thật |
| Đèn hậu, đèn phanh, xi-nhan của xe | Đỏ/vàng giống đèn giao thông |
| Đèn đường, đèn trần hầm, đèn biển hiệu, đèn trang trí | Là nguồn sáng tròn |
| Biển báo màu cam/đỏ | Cùng tông màu |

## 6. Visibility / occlusion

| Tình huống | Làm gì |
|---|---|
| Bị che một phần, vẫn thấy mặt đèn | Vẽ box phần nhìn thấy. `state` theo bóng thấy được; bóng sáng bị che thì `unknown` |
| Bị che gần hết, không còn thấy mặt đèn | Không vẽ |
| Bị cắt ở mép ảnh, vẫn thấy mặt đèn và cao ≥ 8 px | Vẽ box tới mép ảnh |
| Nhỏ/xa, cao ≥ 8 px, đọc được màu | Vẽ bình thường |
| Nhỏ/xa, cao ≥ 8 px, không đọc được màu | Vẽ, `state = unknown` |
| Vỏ cao < 8 px | Không vẽ |
| Loá nắng, cháy sáng | Dùng quy tắc vị trí (mục 4.1). Không thấy vị trí thì `state = unknown` |
| Ban đêm/chạng vạng/tán cây tối, không thấy vỏ, vùng sáng có màu ≥ 8 px | Vẽ theo mục 6.1: box ôm vùng sáng có màu, `lamp_only = true`. **Không** escalate |
| Không thấy vỏ, vùng sáng có màu < 8 px | Không vẽ |
| Đầu đèn tắt hẳn (thấy mặt đèn, không bóng nào sáng) | Vẽ, `state = off`. Vỏ tối trên nền tối dễ bỏ sót: zoom quét kỹ |
| Ảnh mờ do chuyển động / mưa / kính bẩn | Như dòng "nhỏ/xa": đọc được thì gán, không thì `unknown` |
| Ảnh phản chiếu | Không vẽ |

### 6.1. Chỉ thấy bóng đèn, không thấy vỏ

**Vì sao vẫn vẽ:** ban đêm gần như không bao giờ thấy vỏ. Bỏ hết những đèn này thì dataset không có đèn giao thông ban
đêm, và model không học được đúng cảnh hay gặp nhất ngoài đường. Downstream cần màu đèn và relevance, bóng đang sáng
chính là thông tin đó. Bỏ sót đèn `relevant` là lỗi critical.

**Bước 1 · chắc là đèn giao thông cho xe.** Cần **ít nhất 2** dấu hiệu dưới đây, thiếu thì không vẽ:

- nằm trên cột hoặc cần treo, cao hơn mặt đường, ở vị trí đèn thường đặt (góc giao lộ, trên làn xe);
- màu thuần đỏ, vàng hoặc xanh (đèn đường thường trắng hoặc vàng cam nhạt; đèn hậu xe thấp, đi theo cặp, gắn trên xe);
- cùng pha hoặc cùng hàng với một đầu đèn khác đã thấy rõ trong ảnh (ví dụ cùng xanh với đèn trên cần treo).

**Bước 2 · đo.** Kéo box ôm **vùng sáng có màu**, không ôm quầng. CVAT hiện kích thước box khi kéo (ví dụ
`11.0x12.0px`): chiều nhỏ hơn < 8 px thì xoá box, không vẽ.

**Bước 3 · gán attribute:**

| Attribute | Gán thế nào |
|---|---|
| `lamp_only` | `true` |
| `state` | Theo màu nhìn thấy. **Không dùng quy tắc vị trí** (không thấy vỏ). Đỏ cam không phân biệt được với vàng → `unknown` |
| `pictogram` | Bóng tròn rõ → `circle`; đọc được mũi tên → hướng đó; còn lại → `unknown` |
| `relevance` | R0–R5 như mọi đầu đèn. Cùng pha với đèn trên cần treo ở giao lộ gần nhất thường là đèn nhắc lại → `relevant` |
| `escalate` | `false` (ca này đã có rule) |

## 7. Ambiguity / escalation

Mỗi quyết định phải **nhìn thấy được trong file export**:

| Quyết định | Khi nào | Thể hiện trong CVAT |
|---|---|---|
| **LABEL** | Đủ bằng chứng cho mọi attribute | Box `traffic_light`, đủ `state`, `pictogram`, `relevance`; `escalate = false`; `lamp_only = true` nếu không thấy vỏ |
| **IGNORE** | Object thuộc danh sách "không vẽ" (mục 5) | Không có box |
| **UNKNOWN** | **Ảnh** không đủ bằng chứng: loá, mờ, tối, không thấy vạch làn | Box có `state`, `pictogram` hoặc `relevance` = `unknown`; `escalate = false` |
| **ESCALATE** | Nhìn **rõ** nhưng **guideline** không có rule, hoặc hai rule cho hai kết quả khác nhau | Box có `escalate = true`; attribute nào quyết được thì điền, còn lại `unknown` |

Phân biệt: **UNKNOWN là lỗi của ảnh, ESCALATE là lỗi của guideline.** Không bật `escalate` chỉ vì ảnh mờ, và không
bật `escalate` kèm theo mỗi giá trị `unknown`: chọn `unknown` là đã đủ.

**Chỉ** bật `escalate` cho các ca dưới đây. Ca nào đã có rule trong file này (đèn quay ngang, đèn nhỏ, đèn ban đêm
không thấy vỏ, đèn tắt…) thì làm theo rule, không escalate.

Các ca phải ESCALATE:

- Hai bóng cùng sáng trên một đầu đèn.
- Đèn tạm thời (công trường, cảnh sát) hoặc đèn LED hiển thị hình lạ.
- Vật cao ≥ 8 px nghi là đèn giao thông cho xe nhưng không chắc có thuộc scope: vẫn vẽ box, `escalate = true`,
  `relevance = unknown`.

Ai xử lý: **spec owner** của nhóm đọc các box `escalate = true` sau mỗi vòng, quyết định, ghi thành rule mới và tăng
version guideline.

**Thứ tự quyết định nhanh cho mỗi nguồn sáng/vật trong ảnh:**

1. Có phải đầu đèn giao thông cho xe, **thấy mặt đèn**, đủ lớn (vỏ ≥ 8 px, hoặc vùng sáng có màu ≥ 8 px khi không
   thấy vỏ), không phải phản chiếu? Không → IGNORE.
2. Vẽ box theo mục 3. Không thấy vỏ → làm theo mục 6.1, bật `lamp_only`.
3. Chọn `state` (mục 4.1), rồi `pictogram` (mục 4.2).
4. Chọn `relevance` theo R0–R5 (mục 4.3).
5. Có ca nào ở danh sách ESCALATE không? Có → `escalate = true`.

## 8. Temporal rule

Không áp dụng. Đây là task **ảnh tĩnh**. Kể cả khi ảnh là các frame liên tiếp của cùng một clip (LISA), mỗi ảnh gán
nhãn độc lập: **không** dùng frame trước/sau hay nhịp đèn để suy ra `state`, và không dùng track.

## 9. Examples

Ảnh ví dụ thuộc split `example` hoặc `calibration`. Kích thước ảnh LISA 1280×960, BDD 1280×720; toạ độ ghi dạng xấp
xỉ.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| LISA01 | Cần treo phía trên giao lộ có 2 đầu đèn: bên trái là đầu **mũi tên trái đỏ** (x≈732–761, y≈130–197), ở giữa là **đầu đèn tròn đỏ** (x≈926–955, y≈140–212). Bóng đỏ trông hơi cam | Mũi tên: `red`, `arrow_left`, `not_relevant`. Đèn tròn: `red`, `circle`, `relevant` | 2: 2 vỏ = 2 box. 4.1: bóng trên cùng → `red` dù trông cam. R3: xe mình mặc định đi thẳng nên mũi tên trái không điều khiển xe mình. R4 |
| LISA01 | Đầu đèn tròn đỏ thứ hai trên cần treo bên phải, chìm trong tán cây tối, chỉ thấy bóng đỏ (x≈1147–1166, y≈189–213) | `red`, `circle`, `relevant`, `lamp_only = true` | R4, đèn nhắc lại. 6.1: màu đỏ thuần + cùng pha với đèn giữa nên gán được `red` dù không dùng quy tắc vị trí |
| LISA01 | Ở góc bên kia giao lộ có 2 đầu đèn tròn đỏ nhỏ (vỏ cao khoảng 20 px), quay mặt về xe mình | Mỗi đầu 1 box: `red`, `circle`, `relevant` | Mục 1: cao ≥ 8 px thì vẽ. R4, đèn nhắc lại ở góc bên kia giao lộ |
| LISA01 | Sát phải đầu đèn xa thứ hai có một vỏ tối, chỉ thấy một chấm đỏ nhỏ ở mép, không thấy mặt bóng đèn | Không vẽ | Mục 5: không thấy mặt đèn quay về xe mình |
| LISA01 | Biển báo cạnh đầu mũi tên, đèn pha các xe ngược chiều | Không vẽ | Mục 2, mục 5 |
| LISA20 | Cùng giao lộ. Đầu mũi tên trái vẫn **đỏ**; đầu đèn tròn giữa, đầu đèn tròn bên phải và 2 đầu đèn xa đã **xanh** (trông xanh ngọc) | Mũi tên: `red`, `arrow_left`, `not_relevant`. 4 đầu đèn tròn: `green`, `circle`, `relevant` (đầu đèn bên phải thêm `lamp_only = true`) | R3, R4. 4.1: xanh ngọc = `green`. Trong cùng ảnh có thể có đèn đỏ `not_relevant` và đèn xanh `relevant` |
| BDD16 | Xe đi dưới cầu vượt: đèn trần tròn sáng ở góc trên phải, đèn hậu đỏ của xe phía trước, vệt đỏ phản chiếu trên nắp capo, biển tròn màu cam phía xa | Không có box nào | Mục 5: đèn trần, đèn hậu, ảnh phản chiếu, biển báo đều IGNORE. Ảnh không có đèn giao thông là hợp lệ |
| LISA16 | Frame vừa chuyển pha: đầu đèn xa bên trái (x≈678–689, y≈359–386) **tắt hẳn**, chỉ thấy vỏ tối; mũi tên trái vẫn đỏ; các đầu đèn tròn khác đã xanh | Đầu đèn xa bên trái: `off`, `unknown`, `relevant`. Mũi tên: `red`, `arrow_left`, `not_relevant` | 4.1: thấy mặt đèn, không bóng nào sáng → `off`. 4.2: không đọc được hình → `unknown`, không phải `other`. R3 |
| LISA16 | Đầu đèn tròn bên phải chìm trong tán cây tối, chỉ thấy bóng xanh sáng (x≈1146–1163, y≈229–253) | `green`, `circle`, `relevant`, `lamp_only = true`; box ôm vùng sáng có màu | Mục 3, 6.1: không thấy vỏ → box ôm vùng sáng, không ước lượng vỏ. Dấu hiệu: trên cần treo + cùng pha với đèn giữa |
| BDD07 | Mỗi bên đường có một cặp vỏ vàng: vỏ quay về xe mình sáng **xanh** (x≈230–241 và x≈679–689), vỏ ngay cạnh quay ngang, chỉ thấy cạnh vỏ | 2 box: `green`, `circle`, `relevant`. Hai vỏ quay ngang: không vẽ. Hai đầu đèn rất xa (x≈490 và x≈600) không thấy mặt đèn: không vẽ | Định nghĩa "thấy mặt đèn". Mục 5: đèn quay ngang IGNORE, không escalate. R4 đèn nhắc lại |
| BDD25 | Chạng vạng, không thấy vỏ; 3 bóng xanh ở giao lộ phía trước, vùng sáng có màu rộng khoảng 10 px | 3 box ôm vùng sáng: `green`, `circle`, `relevant`, `lamp_only = true`; không escalate | Mục 1, 3, 6.1: không thấy vỏ nhưng vùng sáng ≥ 8 px → vẽ. Dấu hiệu: trên cột ở giao lộ + xanh thuần + cùng pha |
| BDD18 | Ban đêm; cuối đường có 2 chấm xanh rất xa (vùng sáng khoảng 7 px); sát phải là đèn đi bộ đỏ/trắng | Không có box nào | Mục 5: vùng sáng < 8 px → không vẽ; đèn người đi bộ → không vẽ |

## 10. Common mistakes

| Lỗi | Hậu quả | Cách tránh |
|---|---|---|
| Gán `yellow` cho đèn đỏ vì trên ảnh trông cam | **Critical**: xe có thể không dừng | Dùng quy tắc vị trí: bóng trên cùng là `red` |
| Gán `not_relevant` hoặc bỏ sót đầu đèn điều khiển xe mình | **Critical**: xe có thể vượt đèn đỏ | Chạy R0–R5 cho từng box. Nhớ các đèn nhắc lại ở góc bên kia giao lộ |
| Gán mũi tên rẽ là `relevant` vì đầu đèn nằm gần giữa ảnh / ngay trên xe mình | **Critical**: xe đi/dừng theo đèn rẽ | Không suy relevance từ vị trí trên ảnh. Mặc định xe mình đi thẳng (mục 4.3) |
| Một box bao cả cần treo có 2 đầu đèn | Sai số object, sai relevance | 1 vỏ = 1 box |
| Box theo quầng sáng ban đêm, hoặc lấy cả cột/biển | Sai geometry | Box theo vỏ đèn, tolerance 3 px |
| Vẽ đèn quay ngang/quay lưng (vỏ vàng cạnh đầu đèn, chấm màu ở mép vỏ), đèn người đi bộ, ảnh phản chiếu | Nhiễu dữ liệu | Kiểm tra "thấy mặt đèn" và danh sách IGNORE mục 5 |
| Bỏ sót đèn nhỏ ở xa, đèn chìm trong tán cây tối, đầu đèn đang tắt | Thiếu object | Zoom quét cả ảnh, nhất là quanh giao lộ, phía xa và vùng tối |
| Đoán màu khi ảnh loá, hoặc dùng frame LISA trước/sau để suy ra màu | Nhãn sai mà trông như đúng | Không thấy màu và vị trí thì `unknown`; mỗi ảnh độc lập |
| Bật `escalate` cho ảnh mờ, bật kèm mỗi `unknown`, hoặc bật cho ca đã có rule | Trộn hai loại quyết định, escalate mất tác dụng | UNKNOWN = ảnh thiếu bằng chứng; ESCALATE chỉ cho danh sách ở mục 7 |
| Để attribute `__undefined__` (hay gặp nhất: quên `relevance` ở box cuối cùng vẽ) | Không chấm được | Mở lại từng box trong sidebar trước khi export |
| Đèn không thấy vỏ: quên bật `lamp_only`, dùng quy tắc vị trí để đoán đỏ, hoặc vẽ đèn đường/đèn hậu | Box ôm bóng lẫn với box ôm vỏ; sai màu; nhiễu | Làm đủ 3 bước mục 6.1 |

**Tự kiểm trước khi export:** đã quét hết ảnh ở mức zoom lớn, cả vùng tối? Mỗi đầu đèn đúng 1 box? Không box nào còn
`__undefined__`? Mọi đèn đỏ đã kiểm theo vị trí bóng? Mọi box đã qua R0–R5, và mọi mũi tên đã qua R3? `escalate`
chỉ bật cho ca trong danh sách mục 7? Mọi box không thấy vỏ đã bật `lamp_only`?
