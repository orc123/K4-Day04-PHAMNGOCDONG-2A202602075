# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 27 skeleton, trung bình 12.78 khớp có v > 0 mỗi người
- Tổng: v=2 313 | v=1 32 | v=0 114

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 19 | 2 | 6 | 7% |
| 1 | left_eye | 16 | 2 | 9 | 7% |
| 2 | right_eye | 18 | 2 | 7 | 7% |
| 3 | left_ear | 10 | 2 | 15 | 7% |
| 4 | right_ear | 14 | 3 | 10 | 11% |
| 5 | left_shoulder | 25 | 1 | 1 | 4% |
| 6 | right_shoulder | 27 | 0 | 0 | 0% |
| 7 | left_elbow | 22 | 2 | 3 | 7% |
| 8 | right_elbow | 24 | 1 | 2 | 4% |
| 9 | left_wrist | 20 | 3 | 4 | 11% |
| 10 | right_wrist | 18 | 6 | 3 | 22% |
| 11 | left_hip | 19 | 3 | 5 | 11% |
| 12 | right_hip | 22 | 0 | 5 | 0% |
| 13 | left_knee | 17 | 0 | 10 | 0% |
| 14 | right_knee | 14 | 3 | 10 | 11% |
| 15 | left_ankle | 14 | 1 | 12 | 4% |
| 16 | right_ankle | 14 | 1 | 12 | 4% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
