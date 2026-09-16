# Mini guideline - nhóm: **\_\_** | người gán: PHẠM NGỌC ĐÔNG | ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

Nguyên tắc chung nhóm dùng để suy ra mọi luật dưới đây: **câu hỏi quyết định cờ là
"tôi có nhìn thấy bề mặt khớp đó không", không phải "tôi có đoán được nó ở đâu không".**
Đoán được vị trí là điều kiện để _đặt chấm_; nhìn thấy mới là điều kiện để dùng `v = 2`.

| Tình huống                                                             | Luật nhóm bạn chọn                                                                                                                                                                                                      | Vì sao                                                                                                                                                                                                                        |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hông của người mặc quần áo dài                                         | Luôn `v = 1` + đặt chấm ước lượng. Vị trí: điểm giữa bề ngang thân ở mức nếp gấp bẹn, xấp xỉ ngang cổ tay khi tay buông thẳng. Chỉ dùng `v = 2` khi thấy được đường viền xương chậu qua đồ bó sát                       | Không người mặc quần áo nào để lộ bề mặt khớp hông. Để `v = 2` là nói dối về độ tin cậy; để `v = 0` và bỏ chấm thì mất hai cạnh vai-hông và hông-gối, model không học được tỉ lệ thân và không nối được nửa trên với nửa dưới |
| Tai bị tóc hoặc mũ bảo hiểm che một phần                               | Còn thấy vành tai hoặc ống tai -> `v = 2`. Che hoàn toàn (tóc dày, mũ fullface, khăn trùm) nhưng đầu còn trong khung -> `v = 1` + chấm ước lượng đặt ngang tầm đuôi mắt, lùi về phía gáy khoảng một chiều rộng mắt      | Tai là mốc định hướng đầu. Xoá nó đi thì model mất tín hiệu phân biệt người quay mặt với người quay lưng - đúng loại lỗi khó sửa nhất sau này                                                                                 |
| Tai/mắt ở **phía bên kia** của đầu (người quay nghiêng hoặc quay lưng) | `v = 1` + chấm ước lượng, **không** dùng `v = 0`                                                                                                                                                                        | Bị che bởi chính cái đầu vẫn là "bị che", không phải "ra ngoài khung". Đây là chỗ sai cờ phổ biến nhất của cả lớp                                                                                                             |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên)                      | Khớp rơi ra ngoài mép -> `v = 0`, **không** đặt chấm, không suy đoán toạ độ âm hay vượt biên. Khớp còn trong khung dù bị che -> `v = 1`                                                                                 | Đây đúng là định nghĩa `outside`. Ngoài ra toạ độ ngoài biên khi chuẩn hoá YOLO sẽ nằm ngoài `[0, 1]` và `check_pose_labels.py` sẽ báo lỗi                                                                                    |
| Cổ tay nằm sau tay lái / sau thân mình                                 | `v = 1` + chấm ước lượng. Nếu thấy găng tay: cổ tay là chỗ nối giữa mép găng và cẳng tay. Nếu không thấy bàn tay: kéo dài trục cẳng tay thêm một đoạn bằng chiều dài bàn tay                                            | Vị trí suy ra được từ cẳng tay với sai số nhỏ hơn nhiều so với thiệt hại của việc bỏ trống khớp cuối chuỗi tay                                                                                                                |
| Hai người chồng lên nhau                                               | Gán **xong hẳn** người phía trước rồi mới sang người phía sau. Khớp của người bị che vẫn `v = 1` + chấm ước lượng đặt **trên cơ thể người đó**, tuyệt đối không mượn khớp của người đứng trước                          | Đây là cách duy nhất chặn lỗi `nham_nguoi`, lỗi bị trừ nặng thứ nhì trong rubric. Làm xen kẽ hai người là lúc sinh ra lỗi này                                                                                                 |
| Người nhỏ đến mức nào thì không gán nữa                                | Gán mọi người mà bạn **phân biệt được đầu với thân** và đoán được hướng mặt (thực tế cao hơn ~40 px). Dưới ngưỡng đó, người chỉ còn là một vệt màu không đọc được tư thế -> không tạo skeleton, và ghi ảnh đó vào mục 3 | Bộ 20 ảnh này đã được chọn sao cho người cần gán đều đủ lớn. Ngưỡng ở đây dùng cho **người ở hậu cảnh** (thuyền ở xa trong `train_14`, xe cộ ở `train_05`), không phải cho chủ thể chính                                      |

> Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
> Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu.
>
> **TODO của bạn:** chụp ít nhất 3 screenshot cho ba luật đầu (hông, tai, mép ảnh) và
> chèn vào bảng trên.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

> Số "người thứ mấy" phụ thuộc thứ tự bạn vẽ skeleton trong CVAT - điền lại theo bài
> của bạn sau khi gán xong.

### Ca 1 - ảnh `train_02`, người thứ `___`, khớp `nose`, `left_eye`, `right_eye`

- **Mơ hồ ở chỗ nào:** người đạp xe quay lưng hoàn toàn về phía máy ảnh và đội mũ bảo
  hiểm. Mũi và cả hai mắt không hề xuất hiện trên ảnh, nhưng đầu thì nằm gọn giữa khung.
  Phản xạ đầu tiên là gắn `v = 0` vì "không thấy gì cả".
- **Bạn quyết thế nào:** `v = 1`, đặt chấm ước lượng ở mặt khuất của đầu - mũi nằm trên
  trục dọc giữa đầu, hai mắt đối xứng qua trục đó.
- **Vì sao:** `v = 0` dành riêng cho khớp **ra ngoài khung hình**. Ở đây khớp vẫn trong
  khung, chỉ bị chính cái đầu che - theo luật lớp đó là `v = 1`.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** nếu cả lớp gắn `v = 0` cho
  mọi người quay lưng, model sẽ chỉ học được khái niệm "mặt" ở người quay về phía camera
  và sẽ bỏ trống toàn bộ vùng đầu khi gặp người quay lưng - tức là mất luôn khả năng
  ước lượng hướng nhìn, thứ mà ứng dụng pose thật sự cần.

### Ca 2 - ảnh `train_04`, người thứ `___` (người bên phải, đội mũ fullface), khớp `left_ear`, `right_ear`

- **Mơ hồ ở chỗ nào:** mũ bảo hiểm fullface che kín hai tai, nhưng qua kính chắn lại
  **nhìn thấy rõ hai mắt và sống mũi**. Trong cùng một cái đầu, có khớp thấy rõ và có
  khớp không thấy gì - dễ bị kéo theo, gắn cả cụm đầu cùng một cờ.
- **Bạn quyết thế nào:** `left_eye`, `right_eye`, `nose` -> `v = 2`. `left_ear`,
  `right_ear` -> `v = 1`, đặt chấm ở vị trí giải phẫu bên ngoài vỏ mũ, ngang tầm mắt.
- **Vì sao:** cờ là thuộc tính **của từng khớp**, không phải của bộ phận cơ thể. Di chuột
  lên đúng một điểm rồi bấm `q`, đừng bấm khi con trỏ đang ở trên box bao - làm vậy sẽ
  đổi cờ cả 17 điểm.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** gắn tai `v = 2` là dạy
  model rằng vỏ mũ bảo hiểm chính là cái tai; gắn `v = 0` là dạy nó rằng người đội mũ
  thì không có tai. Cái thứ nhất làm model đặt tai sai vị trí trên mọi người đội mũ,
  cái thứ hai làm nó mất mốc định hướng đầu.

### Ca 3 - ảnh `train_07`, người thứ `___`, khớp `left_ankle`, `right_ankle`

- **Mơ hồ ở chỗ nào:** người cầm ô bị **mép dưới của ảnh cắt ngang ở khoảng cẳng chân**.
  Hai cổ chân nằm hẳn bên dưới biên ảnh. Vì vẫn nhìn thấy đùi và gối rất rõ, có xu hướng
  muốn "kéo dài chân xuống" và đặt chấm cho đủ bộ.
- **Bạn quyết thế nào:** `v = 0`, **không** đặt chấm. Gối vẫn `v = 2` vì còn trong khung.
- **Vì sao:** đây đúng là trường hợp `outside` duy nhất mà luật lớp cho phép. Đặt chấm
  ra ngoài biên sẽ tạo toạ độ chuẩn hoá vượt `[0, 1]` và `check_pose_labels.py` báo lỗi
  định dạng - mất điểm ở cổng bắt buộc chứ không chỉ mất điểm vị trí.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** nếu ai cũng bịa toạ độ
  cho khớp ngoài khung, model sẽ học cách dự đoán cổ chân ở sát mép ảnh cho **mọi**
  người bị crop - một loại ảo giác có hệ thống, và tệ hơn là nó trông rất hợp lý nên
  khó phát hiện khi review.

### Ca 4 - ảnh `train_03`, người thứ `___` (người mặc áo khoác rằn ri, đứng sau), khớp `left_hip`, `left_knee`

- **Mơ hồ ở chỗ nào:** hai người đứng chồng lên nhau, và **bánh xe đạp cùng khung xe**
  che ngang qua chân cả hai. Nửa dưới của người đứng sau vừa bị người đứng trước che,
  vừa bị nan hoa xe đạp cắt vụn. Rất dễ đặt nhầm chấm sang chân của người đứng trước.
- **Bạn quyết thế nào:** `v = 1` cho cả hai khớp, chấm đặt trên đường thẳng nối vai và
  cổ chân **của chính người đó**, suy ra từ phần thân còn thấy được. Gán xong hẳn người
  đứng trước rồi mới bắt đầu người đứng sau.
- **Vì sao:** khớp bị che bởi vật thể hay bởi người khác đều vẫn ở trong khung. Quan
  trọng hơn: xử lý tuần tự từng người là biện pháp chống lỗi `nham_nguoi`.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** nếu đặt chấm sang cơ thể
  người bên cạnh, model học được một "người" có thân trên ở chỗ này và chân ở chỗ khác.
  Loại lỗi này làm hỏng cả phần gom nhóm keypoint theo người, không chỉ hỏng một khớp.

### Ca 5 - ảnh `train_10`, người thứ `___`, khớp `left_hip`, `right_hip`, `left_knee`, `right_knee`

- **Mơ hồ ở chỗ nào:** ảnh cận cảnh, người chồm về phía trước qua một chiếc xe máy. Nửa
  dưới cơ thể vừa bị xe che, vừa nằm ở vùng mép dưới ảnh - không chắc hông còn trong
  khung hay đã ra ngoài.
- **Bạn quyết thế nào:** kiểm bằng cách kéo dài trục thân trong CVAT. Khớp nào điểm ước
  lượng vẫn **rơi bên trong canvas** -> `v = 1` + đặt chấm. Khớp nào rơi ra ngoài -> `v = 0`.
  Ranh giới quyết định là mép ảnh, không phải mép vật che.
- **Vì sao:** "bị che" và "ra ngoài khung" là hai câu hỏi tách rời nhau; vật che không
  bao giờ biến một khớp thành `v = 0`.
- **Nếu người khác quyết ngược lại thì model học sai cái gì:** nếu coi mọi thứ bị vật
  thể che là `v = 0`, thì toàn bộ ảnh người ngồi trên xe, sau bàn, sau quầy sẽ mất nửa
  dưới cơ thể trong nhãn - và model sẽ không bao giờ học được tư thế ngồi.

## 4. Sau khi so visibility report với bạn cùng nhóm

> Chạy lệnh này rồi mới điền được mục này:
>
> ```powershell
> python tools\visibility_report.py --labels dataset\labels\train `
>     --compare ..\ban_cung_nhom\dataset\labels\train --markdown reports\visibility_compare.md
> ```

- Khớp lệch `%v=1` nhiều nhất: `______` (bạn `___%` / họ `___%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**:
- Luật mới bổ sung vào mục 2 sau khi thống nhất:

**Dự đoán trước khi chạy** (ghi lại để đối chiếu - đoán sai cũng là dữ liệu tốt): ba khớp
nhiều khả năng lệch nhất là `left_hip`/`right_hip` (ai coi hông là `v = 2`, ai coi là
`v = 1`), `left_ear`/`right_ear` (tóc và mũ), và nhóm `nose`/`eye` ở những người quay lưng
như `train_02`. Cả ba đều là bất đồng về **guideline**, không phải về bức ảnh.

> Lưu ý khi đọc kết quả: `%v = 1` của bạn sẽ cao hơn của gold, và đó là đúng luật lớp.
> Gold lấy từ COCO, mà COCO dùng `v = 0` cho cả "ra ngoài khung" lẫn "không gán nhãn".
> Xem mục cuối [README.md](README.md).
