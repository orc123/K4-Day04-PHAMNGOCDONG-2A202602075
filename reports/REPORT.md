# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: PHẠM NGỌC ĐÔNG   Nhóm: `______`   Ngày: 16/09/2026

> **Trạng thái bản này:** đã chấm với gold lần 1 (`outputs/eval_vs_gold.json`). Nhãn
> **chưa rework** — cột "sau rework" ở mục 2 còn trống. Mục 3 chờ bạn cùng nhóm. Các chỗ
> còn thiếu đánh dấu `[CHƯA CÓ]`.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 (gold có 29) |
| v=2 / v=1 / v=0 | 313 / 32 / 114 |
| Thời gian trung bình mỗi ảnh | `[CHƯA CÓ]` phút |

Trung bình 12.78 khớp có `v > 0` mỗi người — tức **4.22 khớp bỏ trống mỗi skeleton**.

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `right_wrist` — 22% (6/27)
2. `right_ear`, `left_wrist`, `left_hip`, `right_knee` — 11% (3/27), đồng hạng
3. `nose`, `left_eye`, `right_eye`, `left_ear`, `left_elbow` — 7% (2/27)

**Chúng có đúng là những khớp tôi thấy khó gán nhất không?**

Không, và chỗ lệch chính là bằng chứng của một lỗi thao tác. `right_wrist` đứng đầu thì hợp
lý — bộ ảnh có nhiều người ngồi xe máy, cổ tay nằm sau tay lái. Nhưng bảng cho `left_knee`
= **0% v=1** với 10 khớp `v=0`, và `right_hip` = **0% v=1** với 5 khớp `v=0`. Gối và hông
là hai khớp bị che nhiều nhất trong thực tế; không thể có chuyện chúng không bị che lần nào
trong 27 người.

Đối chiếu với nhãn phát sẵn ở `dataset/labels/test` cho thấy mức độ: tôi có **1.19 khớp
`v=1` mỗi skeleton**, nhãn phát sẵn có **2.23**. Luật lớp chặt hơn COCO nên con số của tôi
lẽ ra phải *cao hơn*, không phải bằng một nửa.

Nguyên nhân: trong CVAT tôi bấm `o` (Outside) ở chỗ đáng lẽ phải bấm `q` (Occluded). Một
lỗi lặp lại, không phải nhiều lỗi rời rạc.

**Rà thủ công trên `outputs/vis_train/` trước khi nhận gold**, tôi phân loại 114 khớp `v=0`
thành 82 khớp sai và 26 khớp đúng, theo một ranh giới duy nhất: *mép ảnh, không phải mép
vật che*. Gold sau đó xác nhận cách phân loại này — không một khớp nào trong nhóm "giữ
`v=0`" bị báo lỗi, kể cả gối và cổ chân của `train_01`, `train_04`, `train_13`, và
`train_07` không xuất hiện trong danh sách lỗi.

| Nhóm khớp | Số `v=0` | Chẩn đoán | Gold xác nhận |
| --- | ---: | --- | --- |
| Đầu (mũi, mắt, tai) | 47 | Đầu luôn trong khung → sai hết | 14 khớp bị tính lỗi |
| Gối + cổ chân | 44 | 28 đúng (mép dưới cắt thật), 16 sai | 10 khớp bị tính lỗi |
| Hông | 10 | Mâu thuẫn với chính luật hông của tôi | 7 khớp bị tính lỗi |
| Vai + tay | 13 | Bị người/vật che, vẫn trong khung → sai | 5 khớp bị tính lỗi |

**Một lưu ý về công cụ.** `check_pose_labels.py` báo 15 cảnh báo, nhưng danh sách đó không
trùng danh sách lỗi thật. Box trong file nhãn bằng đúng hull của các khớp **đã đặt chấm**
(kiểm chứng: `box_bottom == kp_y_max` ở cả 27 skeleton), nên khi bỏ cổ chân thì box co lên
và rời mép ảnh, khiến `touches_image_edge()` tưởng người đó nằm gọn giữa ảnh. Hệ quả hai
chiều: cảnh báo nổ nhầm ở `train_01`, `train_04`, `train_13` (người bị cắt chân thật), và
bỏ sót 6 skeleton có `v=0` dưới ngưỡng 4 khớp — trong đó `train_08`, `train_12`, `train_18`
sau này đều bị gold tính lỗi. Đi theo danh sách cảnh báo là sẽ sửa nhầm chỗ.

## 2. Chấm với gold

Nguồn: `outputs/eval_vs_gold.json`, 29 người trong gold, ghép được 27.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.8095 | `[CHƯA CÓ]` |
| OKS@0.50 | 0.8966 | `[CHƯA CÓ]` |
| OKS@0.75 | 0.7241 | `[CHƯA CÓ]` |
| Lỗi `dao_trai_phai` | **1** | `[CHƯA CÓ]` |
| Lỗi `nham_nguoi` | **0** | `[CHƯA CÓ]` |
| Lỗi `xoa_khop_bi_che` | 22 | `[CHƯA CÓ]` |

Các loại lỗi còn lại: `thieu_khop` 14, `lech_nhe` 15, `thieu_nguoi` 2, `thua_nguoi` 0.
Hai mục `co_khac_gold` (39) và `gold_khong_gan_nhan` (12) là chẩn đoán, không trừ điểm.

**Đọc kết quả này thế nào.** Hai ngưỡng số đều qua: OKS trung bình 0.8095 ≥ 0.75 và
OKS@0.75 là 0.7241 ≥ 0.70. Nhưng theo `RUBRIC.md`, **một lỗi `dao_trai_phai` còn sót giữ
bài ở mức "Cần rework" bất kể điểm số**. Vậy bài này hiện **chưa qua cổng**, và thứ chặn nó
là đúng một khớp.

Ba nguồn mất điểm, theo thứ tự thiệt hại:

1. **`train_13` — bỏ sót 2 trong 3 người.** Tôi chỉ gán 1 skeleton, gold có 3. Hai người
   kia nhận OKS 0.000 và kéo thẳng `oks50`/`oks75` xuống vì mẫu số là 29 chứ không phải 27.
   Đây là lỗi tốn nhất: hai skeleton bằng 0 làm mất nhiều hơn toàn bộ lỗi lệch vị trí cộng
   lại. Ảnh này rộng 500×280, hai người còn lại đứng ở hậu cảnh bên trái — tôi đã bỏ qua họ
   vì áp ngưỡng "người quá nhỏ" trong `GUIDELINE_MINI.md` quá tay.
2. **36 khớp mất trắng vì cờ.** 22 `xoa_khop_bi_che` + 14 `thieu_khop`, mỗi khớp tính 0
   điểm theo `evaluate_pose_annotations.py:69-76`. Quan hệ này bất đối xứng: gold để `v=0`
   thì miễn phí cho tôi, còn tôi để `v=0` nơi gold có chấm thì không.
3. **1 lỗi đảo trái/phải** ở `train_15` người 2 — xem phần dưới.

Đáng chú ý: trong 82 khớp tôi tự đánh giá là sai cờ, chỉ **36 khớp thực sự bị trừ điểm**;
46 khớp còn lại rơi vào chỗ gold cũng để `v=0` nên không mất gì. Điều đó khớp với mục lưu ý
trong `README.md`: `%v=1` cao hơn gold không bị phạt, nhưng `v=0` sai thì bị.

**Kế hoạch rework** `[CHƯA CHẠY LẠI]` — mỗi dòng là ảnh / người / khớp / thao tác:

- `train_15` người 2 — **đổi lại cặp trái/phải cho toàn skeleton** (lỗi `dao_trai_phai`).
  Ưu tiên 1 vì đây là thứ duy nhất chặn cổng.
- `train_13` — **gán bổ sung 2 người** ở hậu cảnh, đủ 17 điểm mỗi người.
- `train_15` người 1 — `right_knee`, `left_ankle`, `right_ankle` → `v=1` + chấm ước lượng;
  `right_eye`, `right_ear`, `left_hip` → gán lại (gold có, tôi để `v=0`).
- `train_15` người 2 — `left_eye`, `left_ear` → gán lại.
- `train_10` người 1 và `train_11` người 1 — `left_hip`, `right_hip` → `v=1` + chấm.
- `train_08` người 1 và `train_12` người 1 — `left_knee`, `left_ankle` → `v=1` + chấm
  (chân ở phía xa của xe, vẫn trong khung).
- `train_06` người 1 — `left_ear`, `right_ear` gán lại; `right_hip` → `v=1`.
- `train_09` người 1 — `left_eye`, `right_hip` → `v=1` + chấm.
- `train_20` người 1 — `left_elbow`, `left_hip` → `v=1` + chấm.
- `train_03` người 1 — `left_shoulder`, `right_wrist` → `v=1` + chấm.
- `train_16` người 2 — `nose` → `v=1`; `right_eye`, `right_ear` gán lại.
- `train_19` người 2 — `left_ear` → `v=1`; `right_ear` gán lại.
- `train_01` người 1 — `left_wrist` → `v=1` (bị khay pizza che).
- `train_02` người 1 — `left_ear` gán lại. `train_04` người 1 — `left_ear`, `right_ear`
  gán lại. `train_14` người 1 — `right_ear` gán lại. `train_18` người 1 — `right_ankle`
  → `v=1`.

15 lỗi `lech_nhe` để cuối cùng, sửa nếu còn thời gian — đây là loại ít hại nhất.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào? Ảnh đó dễ hay khó?**

`train_15`, người thứ 2 — người phụ nữ đội mũ bảo hiểm xanh đứng cạnh xe máy ở cây xăng.
Đây là **ảnh dễ**: cô ấy đứng thẳng, toàn thân nhìn thấy rõ từ mũ đến giày cao gót, không
bị ai che, không vặn mình. Người thứ nhất trong cùng bức ảnh đó — người khó hơn hẳn, đứng
khuất sau xe mô tô — thì lại không có lỗi đảo.

Vì sao vẫn sai: cô ấy đứng **quay lưng chếch** về phía máy ảnh. Khi người ta quay lưng,
tay trái của họ xuất hiện ở bên trái ảnh chứ không phải bên phải, tức là ngược với phản xạ
đã hình thành sau hàng chục người quay mặt trước đó. Tôi gán bức này ở đoạn giữa buổi, lúc
đang chạy nhanh, và không dừng lại để làm phép thử "tự đứng vào chỗ người đó". Đúng như
`GUIDE.md` cảnh báo: lỗi đảo trái/phải không xảy ra ở ảnh khó, nó xảy ra ở ảnh dễ lúc bạn
đang làm nhanh.

Điều này cũng giải thích vì sao bộ kiểm mắt-vai-hông trong `check_pose_labels.py` không bắt
được: nó cần cả hai mắt và mũi có `v > 0` mới so hướng được, mà ở người quay lưng tôi đã để
`left_eye` và `left_ear` là `v=0`. Lỗi cờ đã che mất lỗi trái/phải.

## 3. Kiểm chéo

Bạn cùng nhóm: `[CHƯA CÓ]`

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| | | | | |
| | | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- `[CHƯA CÓ]`

**Dự đoán:** ba khớp lệch nhiều nhất sẽ là `left_ear`/`right_ear`, nhóm
`nose`/`left_eye`/`right_eye` ở người quay lưng, và `left_hip`/`right_hip` — cả ba đều là
bất đồng về **guideline**, không phải về bức ảnh.

## 4. Model

Nguồn: `outputs/eval_model.json`. Tập test 10 ảnh / 13 người, Ultralytics 8.4.153,
Tesla T4, `seed=20260915`.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | **0.0000** |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | **0.0000** |
| box_mAP50-95 | 0.8119 | 0.8041 | −0.0078 |

**Điều quan trọng nhất không nằm trong bảng:** lần train này **đã phân kỳ**. Metric đứng
yên ở mức baseline suốt epoch 1-12, rồi sụp ở epoch 13 (`pose_mAP50` 0.845 → 0.017),
`pose_loss` bốc từ 2.74 lên 8.09 và `rle_loss` từ 4.33 lên 14.7. Model bò lên lại trong 25
epoch tiếp theo nhưng chỉ đạt 0.730 khi early stopping dừng ở epoch 39.

`EarlyStopping` chốt **best model ở epoch 9** — nghĩa là `best.pt`, cái dùng để tính cột
"sau fine-tune", là model gần như **chưa học gì từ 20 ảnh của tôi**. Đó là lý do
`pose_mAP50` và `pose_recall` trùng baseline đến bốn chữ số thập phân.

Nguyên nhân: `optimizer='auto'` chọn `AdamW(lr=0.002)` theo giả định dataset cỡ thường. Với
20 ảnh và `batch=8` thì mỗi epoch chỉ có **3 bước cập nhật**, nên đến epoch 13 mới khoảng
36 bước — `warmup_epochs=3` không đủ ổn định trong khi lr đặt như đang train tập lớn. Góp
phần thứ hai là 114 khớp `v=0`: Ultralytics loại khớp `v=0` khỏi hàm mất mát, nên nhiều
skeleton chỉ còn rất ít điểm được giám sát (`train_06` còn 7/17), làm tín hiệu pose thưa
và nhiễu hơn.

### Trả lời năm câu hỏi ở cuối notebook

**1. `pose_mAP50-95` thay đổi bao nhiêu?**

+0.0055 (0.6853 → 0.6908). Với 13 instance thì đây **nằm sâu trong nhiễu** — một người
được chấm khác đi đủ tạo chênh lệch lớn hơn nhiều. Kết luận trung thực: 20 ảnh của tôi
**không dạy được model điều gì đo được**, và cột "sau fine-tune" thực chất là model ở
epoch 9.

Hai lý do khiến không thể khác. Thứ nhất, `yolo26n-pose` đã train trên COCO, mà 10 ảnh test
cũng là ảnh COCO — rất có thể model đã nhìn thấy chính những ảnh này lúc train, nên baseline
0.845 gần như là trần chứ không phải thành tích của một model tổng quát. Thứ hai, 20 ảnh so
với hơn 150.000 người trong COCO là quá nhỏ để dịch chuyển trọng số theo hướng tốt; thứ nó
làm được là làm mất ổn định quá trình train, đúng như đã xảy ra ở epoch 13.

Thứ duy nhất 20 ảnh của tôi dạy được mà COCO chưa dạy là **luật cờ của lớp** — gán `v=1`
cho khớp bị che thay vì bỏ trống. Nhưng với 114 khớp `v=0` trong nhãn, tôi đã dạy ngược
lại chính luật đó.

**2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu?**

`box_mAP50-95` 0.8041 so với `pose_mAP50-95` 0.6908 — chênh **0.113**. Tìm *người* dễ hơn
tìm *khớp*, và khoảng cách này là tất yếu: box chỉ cần đúng bốn cạnh, còn pose phải đúng 17
vị trí, mỗi vị trí bị chấm theo bán kính dung sai riêng của khớp đó (sigma COCO).

Đáng chú ý hơn là khoảng cách trong chính pose: `pose_mAP50` 0.845 nhưng `pose_mAP50-95`
chỉ 0.691. Ở ngưỡng OKS lỏng thì gần như đúng hết, ở ngưỡng chặt tụt 15 điểm — model đặt
khớp **đúng chỗ về đại thể nhưng không sát tới từng pixel**. Đây chính là lý do `GUIDE.md`
khuyên không chỉnh nhãn tới từng pixel: độ chính xác của model dừng ở khoảng 4 pixel.

`pose_precision` 0.979 với `pose_recall` 0.846 cho biết model thà bỏ sót còn hơn đoán bừa:
nó sót 2 trong 13 người, và sót đúng 2 người đó ở cả hai lần đo.

**3. Một ảnh test model đoán sai, gọi tên lỗi theo bốn loại:**

`[CHƯA CÓ]` — cần ảnh visualize và bảng so ở cell cuối notebook.

**4. Ảnh nào có OKS thấp nhất giữa nhãn của tôi và model? Ai đúng?**

`[CHƯA CÓ]` — cần chạy model trên 20 ảnh train rồi chấm ngược. Kết quả với gold cho biết
*tôi* tệ nhất ở đâu, nhưng chưa cho biết *model* tệ nhất ở đâu.

**5. Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không?**

Nửa câu trả lời đã có. Ảnh tôi gán tệ nhất là **`train_13`** (OKS 0.000 cho 2 trong 3
người, vì tôi bỏ sót họ hoàn toàn), rồi đến **`train_15`** (người 1: OKS 0.432; người 2:
0.569 kèm lỗi đảo trái/phải). Nửa còn lại `[CHƯA CÓ]` — chờ câu 4.

Nếu `train_15` hoá ra cũng là ảnh model đoán tệ nhất thì điều đó nói bức ảnh ấy khó một
cách khách quan: hai người, một người khuất sau mô tô, một người quay lưng chếch ở cây
xăng nhiều vật che. Còn nếu model làm tốt `train_13` trong khi tôi bỏ sót người, thì lỗi
nằm ở ngưỡng "người quá nhỏ" trong guideline của tôi, không nằm ở bức ảnh.

**Ghi chú về quy trình.** Lần chạy notebook diễn ra trước khi rework, chỉ đo model gốc trên
nhãn test phát sẵn và không dùng để sửa nhãn train — thứ tự tự-gán-trước theo `RUBRIC.md`
được giữ. Sau khi rework và commit, tôi sẽ chạy lại với `lr0=0.0002` và `freeze=10` để có
một lần fine-tune không phân kỳ, giữ log lần hỏng này để so sánh.

## 5. Một rule evidence tôi đã dùng

**Ảnh `train_07`, người thứ 1, khớp `left_ankle` và `right_ankle` — chọn `v = 0`.**

Người đàn ông cầm ô đứng quay mặt về phía máy ảnh, nhìn rõ thân, hông và ống quần kaki.
Căn cứ thị giác để quyết: hông anh ta ở khoảng `y ≈ 465` và gối ở `y ≈ 575` trên ảnh cao
640, tức đoạn hông-gối dài khoảng 110 px; chiếu tiếp cùng độ dài đó xuống thì cổ chân rơi
vào khoảng `y ≈ 685`, **nằm dưới mép dưới của ảnh**. Ngoài ra nền đá lát nhìn thấy được ở
hai bên người nhưng bàn chân không xuất hiện ở đâu trong khung.

Vì khớp đã ra khỏi khung chứ không phải bị vật gì che, đây đúng là `Outside`: `v = 0` và
**không** đặt chấm. Đặt chấm ước lượng ở `y ≈ 685` sẽ tạo toạ độ chuẩn hoá lớn hơn 1 và
`check_pose_labels.py` báo lỗi định dạng ngay.

**Gold xác nhận quyết định này:** `train_07` không xuất hiện một lần nào trong danh sách
lỗi của `outputs/eval_vs_gold.json` — không `thieu_khop`, không `xoa_khop_bi_che`.

**Đối chiếu ngược để thấy ranh giới.** Ở `train_15`, người thứ 1 cũng không nhìn thấy được
cẳng chân — nhưng nguyên nhân là chiếc mô tô chắn trước mặt, và đôi giày của anh ta vẫn ló
ra dưới gầm xe, tức khớp **vẫn nằm trong khung**. Tôi đã gán `v = 0` cho `right_knee`,
`left_ankle`, `right_ankle` ở người này, và gold tính cả ba là `xoa_khop_bi_che`. Hai ảnh
trông giống nhau ở chỗ "không thấy chân", nhưng ranh giới quyết định là **mép ảnh, không
phải mép vật che** — nên `train_07` là `v = 0` còn `train_15` phải là `v = 1`.
