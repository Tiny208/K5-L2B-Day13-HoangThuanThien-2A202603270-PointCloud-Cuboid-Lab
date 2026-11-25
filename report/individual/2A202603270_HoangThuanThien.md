# Báo cáo Day 13 — Hoàng Thuận Thiên, 2A202603270

## 1. Kết luận trước
- **MSSV:** 2A202603270 · **Task:** `D13-2A202603270` · **10 frame:** `s01f19`, `s01f45`, `s02f30`, `s02f45`, `s03f02`, `s03f46`, `s04f10`, `s04f46`, `s05f02`, `s05f43`.
- **Môi trường:** macOS · **Trình duyệt:** Chrome · **Phiên bản CVAT:** 2.74.1 · **MSSV bạn ngồi cạnh:** Chưa đổi kiểm trực tiếp do hoàn thiện ngoài giờ trên lớp (đã tự đối chiếu với `bangA.csv` và `tukiem.csv`).
- **Tổng kết cuboid:** Tổng **99 cuboid** (`75 Car`, `12 Cyclist`, `9 Truck`, `3 Bus`, 100% là `CUBOID SHAPE`). Frame ít nhất là `s02f45` (**5 cuboid**) do đoạn đường cong bị tán cây dày che hai bên và mật độ xe trong bán kính 50 m ít hơn các ngã tư (`s05f43` có 19 cuboid, `s04f10` có 18 cuboid). `selfcheck` cờ **25/99 cuboid**. Phần tin nhất là nhận diện class trên xe gần, hướng `yaw_deg` (0 cuboid bị `length < width`) và cao độ đáy xe gần; phần còn yếu là căn đáy `z_bottom` và chiều dài đủ ở xe xa (> 35 m) hoặc bị che khuất, cùng việc chưa bật cờ `occluded` trên một số vật bị che.

- **Cách dùng CVAT (3 ý chính):**
  1. Đổi góc nhìn: kéo chuột trái xoay khung 3D thẳng từ trên xuống, kéo chuột phải để dời tâm và lăn con lăn (hoặc phím `I`/`K`) để phóng to/thu nhỏ; nháy đúp khoảng trống để về góc nhìn mặc định.
  2. Vẽ và chỉnh cuboid: chọn nút vẽ cuboid → chọn class → bấm **Shape** (không dùng Track), nháy đúp lên cụm điểm để đặt; chỉnh tâm, dài, rộng, hướng yaw ở khung **Top**, chỉnh đáy chạm mặt đường và chiều cao ở **Side** và **Front** (không xoay ở Side/Front).
  3. Bật mũi tên hướng và xem chiếu camera: tích **Cuboid orientation** trong mục **Appearance** để thấy vạch đỏ `+X` hướng về đầu xe; bấm **Save** (`Cmd + S`) và chạy `python scripts/lab.py sync` để kiểm tra cuboid chiếu lên 7 ảnh camera trong `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f19-3d`, `t1-s01f19-top`, `t1-s01f45-3d`, `t1-s01f45-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước `-y`, trục `+x`, `+y`).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f19` | Phía trước (`-y`) ở **cạnh trái** ảnh nhìn từ trên xuống (đối chiếu `cam01` thấy xe con trắng và xe buýt phía trước nằm bên trái gốc). Trục `+x` (đỏ/cam) chỉ **xuống dưới** (bên trái xe); `+y` (xanh lá) chỉ **sang phải** (ra sau). | Mặt đường **phẳng** (các vòng quét đồng tâm nằm trên mặt ngang); thấp hơn tâm LiDAR khoảng **1,7 m**. | Xe/giá long môn/mặt tiền nhà đọc được tới khoảng **60–80 m** (xa hơn chỉ còn các dải điểm rất thưa). | Xe con, xe buýt, người đi bộ, xe đạp dựng vỉa hè, giá long môn biển báo, cây cối, dải phân cách, nhà cao tầng. | **Phía sau (`+y`):** xe con trắng ở làn sau-phải (`cam08`/`cam09`), xe con xám bạc phía sau cùng làn và xe con xanh đậm ở làn sau-trái (`cam09`/`cam05`). **Hai bên:** bên trái (`+x`) có xe con trắng sát hông trước-trái (`cam01`/`cam06`), xe SUV bạc đi ngược chiều qua dải phân cách (`cam06`); bên phải (`-x`) sau hàng cây có 1 người đi bộ và 1 xe đạp dựng (`cam07`). | Cụm tán cây/bụi rậm dọc vỉa hè bên phải (`-x`), vùng khuất sau xe buýt, và các vệt điểm rất thưa ở khoảng cách > 60 m không rõ hình dạng vật nếu không xem camera. |
| `s01f45` | Phía trước (`-y`) ở **cạnh trái** ảnh nhìn từ trên xuống (đối chiếu `cam01` thấy 3 xe con phía trước nằm bên trái gốc). Trục `+x` (đỏ/cam) chỉ **xuống dưới** (bên trái xe); `+y` (xanh lá) chỉ **sang phải** (ra sau). | Mặt đường **phẳng**; thấp hơn tâm LiDAR khoảng **1,7–1,8 m**. | Xe cộ nhận diện được tới khoảng **50–65 m**, giá long môn và mặt đứng tòa nhà tới khoảng **70–90 m**. | Xe con, xe minivan/MPV, xe buýt ở xa, người đi xe đạp, người đi bộ, giá long môn, hàng cây, bụi rào, nhà cao tầng. | **Phía sau (`+y`):** xe con trắng bám ngay sau cùng làn (`cam09`) và xe minivan màu xanh rêu/xám đang tiến lên ở làn sau-phải (`cam08`/`cam09`). **Hai bên:** bên trái (`+x`) có xe con trắng và hatchback xanh đậm ở làn bên cạnh (`cam01`/`cam06`); bên phải (`-x`) sau hàng rào cây có nhóm người đi xe đạp và người đi bộ trên đường gom/vỉa hè (`cam07`/`cam08`/`cam09`). | Cụm điểm hỗn hợp sau rào cây thấp bên phải (`-x`, nơi người đi xe đạp bị tán cây và hàng rào lẫn vào nhau), cùng các cụm điểm thưa trên vỉa hè/mặt nhà ở > 60 m. |

- **Góc mù camera (câu 3):** Qua 7 camera (`cam01`/`cam03` trước, `cam06` trước-trái, `cam07` trước-phải, `cam05` sau-trái, `cam08` sau-phải, `cam09` sau), vẫn còn góc mù ở **sát hai bên hông xe theo phương ngang** (vùng giữa `cam06`–`cam05` bên trái và `cam07`–`cam08` bên phải quanh trụ B), **vùng thấp ngay sát cản trước/sau**, và **phía trên cao** ngay trên nóc xe — trong khi LiDAR vẫn quét đủ 360°.
- **3 điều chỉ camera cho biết** (từ `s01f19`, `s01f45`):
  1. **Màu sắc, đèn xe và chi tiết bề mặt:** phân biệt màu thân xe (xe con trắng, xe xanh đậm, xe buýt trắng-đỏ-xanh, minivan xanh rêu), trạng thái đèn phanh/đèn pha, loại biển số và dải hoa/hàng cây.
  2. **Phân biệt đầu xe với đuôi xe, vạch kẻ đường và biển báo:** từ ảnh `cam01`/`cam03`/`cam06`–`cam09` nhận biết ngay kính lái, đèn trước hay đèn sau để xoay mũi tên `+X` đúng đầu xe, đồng thời đọc được vạch mũi tên phân làn và biển chỉ dẫn giao thông.
  3. **Nhận diện vật nhỏ/mảnh bị lẫn vào nền thực vật:** phát hiện rõ người đi bộ và xe đạp dựng sau lùm cây bên phải (`s01f19` `cam07`) hay nhóm người đạp xe vàng/xanh sau hàng rào cây (`s01f45` `cam07`/`cam08`), vốn rất khó tách khỏi bụi cây nếu chỉ nhìn điểm LiDAR.
- **3 điều chỉ LiDAR cho biết** (từ `s01f19`, `s01f45`):
  1. **Khoảng cách mét 3D và độ cao thực:** đo trực tiếp khoảng cách ngang đến xe trước/sau, kích thước dài–rộng–cao (m) và cao độ mặt đường (`z ≈ -1,7 m`) mà ảnh camera 2D bị méo phối cảnh không đo thẳng được.
  2. **Bản đồ vị trí 360° thống nhất (cả trong góc mù hẹp):** trên góc nhìn từ trên xuống thấy ngay vị trí tương đối giữa xe thu dữ liệu và các xe hai bên, làn ngược chiều hay phía sau trên cùng một hệ trục, không bị tách rời thành 7 ảnh và không phụ thuộc chói nắng hay bóng phản chiếu kính lái.
  3. **Cấu trúc hình học 3D và vùng bóng che (occlusion):** xoay khung 3D thấy độ phẳng mặt đường, chiều cao thực của thành xe buýt/hàng cây và các vệt bóng che hình nón đen phía sau xe gần (`s01f19`, `s01f45`) do tia laser bị vật cản chắn.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f30` | Cạnh **trái** (`-y` sang trái, `+x` xuống dưới, `+y` sang phải) | Đoạn đường hơi cong nhẹ; dải lan can kim loại bên trái có khoảng hở quay đầu; bóng phản chiếu bảng táp-lô nhẹ ở mép dưới `cam01`; xe gần phía trước-trái tạo vùng bóng che khá rõ trên point cloud. |
| `s02f45` | Cạnh **trái** | Hai bên đường có hàng cây lớn tán rộng tạo bóng râm loang lổ trên mặt đường (`cam01`) và gây nhiều điểm nhiễu/che khuất ở hai rìa point cloud; có xe đạp xếp thành hàng sau luống hoa bên phải (`cam01`). |
| `s03f02` | Cạnh **trái** | Đoạn đường bị thu hẹp bởi hàng rào tôn công trường màu xanh cao ở **cả hai bên** (trên point cloud hiện hai đường tường phẳng đứng sắc nét kẹp sát lòng đường); mật độ xe đông nối đuôi gây nhiều vệt bóng che chồng chéo phía sau các xe. |
| `s03f46` | Cạnh **trái** | Ra khỏi đoạn rào tôn công trường (chỉ còn lan can trắng bên phải); phía xa chính diện có xe buýt chạy ngang qua giao lộ (`cam01`); xe bám sát phía trước gây vùng bóng che kéo dài dọc làn giữa. |
| `s04f10` | Cạnh **trái** | Có hàng rào tôn công trường bên phải (lấp ló cần máy xúc phía sau rào trên `cam01`) và lan can kim loại màu vàng dọc làn trái và dải phân cách; xe bám sau gần và xe ở rìa phải tạo bóng che lệch góc. |
| `s04f46` | Cạnh **trái** | Đường hơi cong sang phải; ngay góc trước-phải có xe con đỏ và SUV trắng áp rất gần đầu xe (`cam01`), chắn phần lớn tia LiDAR hướng trước-phải; hàng rào công trường chạy dọc cả hai bên. |
| `s05f02` | Cạnh **trái** | Xuất hiện **thanh giàn ngang gắn nhiều camera giám sát** bắc ngang phía trên lòng đường (`cam01`), trên point cloud tạo vệt điểm mảnh vắt ngang trên cao; vỉa hè phải có hàng xe đạp dựng sát lùm cây. |
| `s05f43` | Cạnh **trái** | **Ngã tư rất rộng và thoáng**, mất hoàn toàn hàng rào kẹp sát hai bên nên các vòng quét mặt đường trải rộng 360°; xuất hiện nhiều xe cỡ lớn (3 xe tải màu xanh lá và 1 xe minivan xanh dương ở phía trước), người đi xe hai bánh rẽ ở rìa phải và công trình cao tầng có cần trục tháp bên trái (`cam01`). |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f19`, idx `2` | `Car` | Đúng (`yaw_deg = 267,7°`, vạch đỏ `+X` hướng về đầu xe `-y`) | Chạm đường (`z_bottom = -1,828 m`, lệch 0,13 m quanh `-1,7 m`) | Có (Top/Side/Front + `cam01`/`cam06`/`work/xem`) | Trong khoảng (`4,403 × 1,891 × 1,572 m`, chuẩn sedan 3,8–5,0 × 1,70–1,90 × 1,40–1,60 m) |
| (b) xe buýt hoặc xe tải | `s01f19`, idx `1` | `Bus` | Đúng (`yaw_deg = 269,4°`, chỉ về đầu xe buýt hướng `-y`) | Chìm nhẹ (`z_bottom = -2,066 m`, lệch 0,37 m dưới `-1,7 m`) | Có (Top/Side/Front + `cam01`/`cam03`/`work/xem`) | Gần khớp (`12,800 × 2,856 × 3,099 m`, chuẩn bus 10,0–12,5 × 2,50–2,60 × 3,00–3,50 m) |
| (c) người đi bộ hoặc xe hai bánh | `s01f45`, idx `7` | `Cyclist` | Đúng (`yaw_deg = 262,9°`, theo hướng xe đạp chạy về `-y`) | Cao nhẹ do hàng cây che bánh (`z_bottom = -1,223 m`) | Có (Top/Side/Front + `cam08`/`cam09`/`work/xem`) | Hơi hẹp dọc/cao (`1,294 × 0,816 × 1,271 m` so với chuẩn 1,6–1,9 × 0,50–0,70 × 1,60–1,90 m) do hàng cây che phần đầu/bánh dưới |

- **Vòng hiệu chuẩn và các vật bỏ qua:** Làm rõ quy ước bắt buộc dùng **Shape** (không dùng Track), vạch đỏ `+X` luôn chỉ thẳng đầu xe (`yaw_deg ≈ 270°` cùng chiều phía trước, `≈ 90°` ngược chiều), kiểm tra đáy chạm đường tại `Side`/`Front` (`z ≈ -1,7 m`). Các vật bỏ qua vì không đủ cơ sở: người đi bộ và xe đạp dựng sau lùm cây bên phải `s01f19` (`cam07`, `LOW_VISIBILITY` vì bị hàng cây che gần hết điểm, xe đạp không người không gán theo guideline) và các xe ở cuối tầm nhìn `> 50 m` còn quá ít điểm (`UNCERTAIN_SCOPE` / `LOW_VISIBILITY`).

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s01f19` · `t4-1-bong-che` · bài cá nhân | **Giả thuyết:** thân xe buýt `#1` (cao `3,099 m`, cách `23,65 m`) và xe con `#2` (`4,82 m`) chắn hoàn toàn tia LiDAR đi qua phía sau chúng. | Sau xe buýt `#1` có dải hình nón đen trống trơn không có điểm mặt đường hay xe phía sau; phía sau `#5` ở làn giữa chỉ còn `#8` (`13` điểm ở `40,02 m`) và `#9` (`31` điểm ở `48,00 m`). | Vùng trống sau xe lớn là vùng chưa quan sát được, không phải đường trống; phải bật `occluded` và bao đủ kích thước suy luận hợp lý theo loại xe. |
| 2. Thưa ở xa | `s01f19` (`idx 9`) · `t4-2-thua-o-xa` · bài cá nhân | **Giả thuyết:** ở khoảng cách gần ngưỡng 50 m, góc giãn giữa các tia laser tăng lên khiến số điểm trúng bề mặt xe giảm mạnh. | Xe con `#9` ở `dist_m = 48,00 m` chỉ còn **31 điểm** (tương tự `#8` ở `40,02 m` còn **13 điểm**), chủ yếu thấy một vệt ngang mờ phần đuôi/cản. | Áp quy tắc 15–20% thể tích: nếu đối chiếu `cam09` nhận ra rõ xe và vị trí thì vẽ hộp theo kích thước tham chiếu bảng 4.3; nếu không đủ cơ sở thì bỏ qua và ghi `LOW_VISIBILITY`. |
| 3. Phản xạ thấp / ít điểm bất thường | `s05f02` (`idx 4`, `idx 6`) · `t4-3-phan-xa-thap` · bài cá nhân | **Giả thuyết:** kính chắn gió sau tối màu của xe `#4` (`13,12 m`) cho tia laser xuyên qua hoặc phản xạ gương ra ngoài, còn xe tối/bị chệch góc `#6` (`14,58 m`) hấp thụ tia hồng ngoại mạnh nên cường độ phản xạ yếu. | Trên `#4` (`679` điểm, ảnh `Front`/`Side`), phần cản dưới phản xạ sáng rõ trong khi vùng kính sau và thân trên gần như trống điểm; còn xe `#6` ở làn đối diện chỉ có `355` điểm với cường độ trung bình rất thấp (`0,1`). | Không thu nhỏ hộp theo phần điểm dày ở cản dưới; đối chiếu `cam01`/`cam03` để căn đủ biên xe thực tế và giữ đúng kích thước chuẩn. |
| 4. Điểm lạ | `s02f35` (`D13-HC485087`) · `t4-4-diem-la` · nguồn: task chung `D13-HC485087` | **Giả thuyết:** tia LiDAR phản xạ đa đường (multipath) qua mặt kính xe hoặc bề mặt phản chiếu làm cảm biến tính sai quãng đường bay thành điểm ảo nằm sâu dưới mặt đường. | Khi xoay nghiêng khung 3D nhìn ngang thấy rõ cụm điểm rời nằm sâu **dưới mặt đường** (`z ≈ -13,2 đến -13,5 m`, cách tâm khoảng `28–29 m` phía sau bên trái, thấp hơn mặt đất `-1,7 m` gần `11,8 m`), không tương ứng vật nào trên camera. | Tuyệt đối không gán cuboid thành vật thể thật; cần xoay nghiêng khung 3D kiểm tra độ cao để loại bỏ điểm nhiễu hình học. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603270-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202603270-tukiem.csv`.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r = √(x²+y²+z²) (m) | yaw_deg (°) | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f19`, idx `2`, `Car` | -1,042 | 1,572 | -1,828 | 4,82 | 4,93 | 267,7 | Chạm đường (`ground_gap_m = +0,01 m`, rất sát mặt đường `-1,7 m`) |
| `s02f45`, idx `5`, `Car` | -0,056 | 1,678 | -0,895 | 39,62 | 39,62 | 270,0 | Lơ lửng (`ground_gap_m = +0,59 m`, đáy cao hơn mặt đường `0,59 m`) |
| `s05f02`, idx `9`, `Car` | -1,396 | 1,531 | -2,161 | 29,36 | 29,39 | 90,8 | Chìm (`ground_gap_m = -0,82 m`, đáy thấp hơn mặt đường `0,82 m`) |

- **(a) Cuboid gần nhất và xa nhất:**
  - Gần nhất: `s03f02`, idx `1` (`Car`), `dist_m = 3,61 m`, `r = 3,74 m`.
  - Xa nhất: `s03f46`, idx `6` (`Bus`), `dist_m = 60,94 m`, `r = 60,95 m` *(lưu ý nằm ngoài ranh 50 m nên không được tính điểm)*.
- **(b) So sánh kích thước với bảng 4.3:**
  - `Cyclist` (`s01f45`, idx `7`): `l = 1,294 m`, `w = 0,816 m`, `h = 1,271 m` (Bảng 4.3 xe đạp có người: `l = 1,6–1,9 m`, `w = 0,50–0,70 m`, `h = 1,60–1,90 m`). Chiều dài (`1,294 m`) và chiều cao (`1,271 m`) nhỏ hơn khoảng chuẩn do hàng rào cây ven đường che mất bánh trước và phần dưới, còn chiều rộng (`0,816 m`) hơi dư nhẹ.
  - `Car` (`s01f19`, idx `2`): `l = 4,403 m`, `w = 1,891 m`, `h = 1,572 m` — nằm gọn trong khoảng Sedan/Hatchback của bảng 4.3 (`l = 3,8–5,0 m`, `w = 1,70–1,90 m`, `h = 1,40–1,60 m`).
- **(c) Góc `yaw_deg` cùng chiều và ngược chiều phía trước:**
  - Xe cùng chiều phía trước: `s01f19`, idx `2` (`Car`) có `yaw_deg = 267,7°` (gần `270°`).
  - Xe ngược chiều phía trước: `s01f19`, idx `7` (`Car`) có `yaw_deg = 93,9°` (và `s02f45`, idx `1` có `89,9°`, rất gần `90°`).

Mật độ theo khoảng cách (4 cuboid `Car` ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (`points_in`) |
|---|---|---|
| `s03f02`, idx `1`, `Car` | 3,61 | 2160 |
| `s04f10`, idx `7`, `Car` | 15,11 | 854 |
| `s03f02`, idx `9`, `Car` | 25,22 | 272 |
| `s04f10`, idx `17`, `Car` | 38,79 | 128 |

- **Quy luật mật độ:** Khi khoảng cách tăng gấp ~2 lần (ví dụ từ `15,11 m` lên `25,22–38,79 m`), số điểm LiDAR trong cuboid giảm khoảng **3–7 lần** (xấp xỉ tỉ lệ nghịch bình phương khoảng cách), phù hợp xu hướng đường cong bài giảng (xe con trung vị: `0–10 m ~ 1968`, `10–20 m ~ 718`, `20–30 m ~ 214`, `30–40 m ~ 72` điểm).
- **Sửa cuboid & cờ giữ nguyên:** Trước lần `sync` cuối, đã bổ sung và chỉnh sửa trực tiếp trên CVAT: thêm `s01f19` idx `8–9` (`Car`), `s01f45` idx `7` (`Cyclist`), và `s02f45` idx `5` (`Car`), kiểm lại trên `work/xem/` và `tukiem.csv`. Với **25/99 cuboid** còn cờ trên `tukiem.csv`, giữ nguyên các cờ `26–30 điểm sát ngay ngoài mép` ở `s04f10` idx `13, 14` (`Cyclist`) vì các điểm ngoài mép thuộc xe đạp đi sát bên cạnh chứ hộp đã khớp thân xe, và giữ nguyên các cờ `length` hơi ngắn (2,6–2,9 m) ở các xe bị che một phần đuôi tại `s02f45 #2, #3`.
- **Đổi kiểm:** Chưa đổi kiểm trực tiếp với bạn ngồi cạnh do hoàn thiện ngoài giờ trên lớp; đã tự đối chiếu trực tiếp toàn bộ các con số mục 5 với `submission/D13-2A202603270-bangA.csv` và `submission/D13-2A202603270-tukiem.csv`, mọi số liệu đều khớp chính xác.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Từ phía model học trên nhãn 3D:** Nhãn 3D dùng đơn vị **mét** trong hệ tọa độ thực quanh xe để mô hình học trực tiếp vị trí, kích thước hình học và khoảng cách vật cản phục vụ dự đoán quỹ đạo/phanh (ảnh 2D theo pixel bị biến dạng phối cảnh, không suy trực tiếp khoảng cách thật). Vật thưa điểm cần quy tắc riêng (ngưỡng 15–20% thể tích / `LOW_VISIBILITY`) để tránh việc người gán đoán mò làm mô hình học sai kích thước hoặc sinh dự đoán giả (false positive) ở vùng dữ liệu quá yếu.
- **(b) Dùng AI:**
  - Công cụ: Trợ lý AI Antigravity.
  - Câu hỏi chính: Hướng dẫn kiểm tra hệ tọa độ VF, hỗ trợ thao tác chụp ảnh Bước 2, rà soát frame thiếu cuboid, đối chiếu số liệu bảng A / phiếu tự kiểm và tổng hợp báo cáo.
  - Cách dùng kết quả: Đối chiếu lại toàn bộ số liệu, tọa độ và ảnh chiếu camera trong `work/xem/` với `bangA.csv` và `tukiem.csv` do `python scripts/lab.py sync` tạo ra.
- **(c) Điều còn chưa chắc:** Một số xe ở xa (`> 40 m` như `s01f19 #8, #9`) có rất ít điểm nên chiều dài và cao độ đáy mang tính ước lượng theo kích thước chuẩn; còn một số cuboid bị cờ lơ lửng/chìm (`s02f45 #5`, `s05f02 #9`) và `3` cuboid ở rìa trên `50 m` (`s02f30 #3` 51,83 m; `s03f46 #6` 60,94 m; `s05f43 #8` 51,99 m) chưa kịp kéo gọn vào sát ngưỡng hoặc chỉnh lại cao độ do sát giờ nộp bài.
