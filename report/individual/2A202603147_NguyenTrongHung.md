# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: 2A202603147
- Ngày / CVAT local: 11/11/2026 - 12/11/2026 / CVAT local v2.74.1
- Công cụ đã dùng: CVAT local, SegFormer (b2-cityscapes), YOLOv8x-seg

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh LMS đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | **14 / 14** | **100** |

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, đối tượng người đi bộ (`person`) ở vùng phía trên bên phải của cụm người, tọa độ hộp giới hạn khoảng `x: [427, 560], y: [122, 316]`.
- Class và quy tắc tôi dùng để chọn biên: Lớp `person`. Vẽ sát đường biên cơ thể nhìn thấy thực tế (bao gồm quần áo và mũ bảo hiểm), loại trừ phần bóng đổ kéo dài trên mặt đường và không để polygon lấn sang thân xe máy bên cạnh.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Mô hình phân tách tốt các đối tượng người và xe độc lập. Tuy nhiên ở các cụm người và xe đỗ san sát nhau, tôi đã kiểm tra lại từng polygon để chắc chắn không có 2 cá thể nào bị gộp chung thành một mask duy nhất theo đúng quy tắc instance segmentation, đồng thời loại bỏ các nhãn không thuộc schema của bài thi (như biển báo, ba lô).
- Nếu không dùng gợi ý: (Đã dùng mô hình hỗ trợ và tự kiểm tra lại theo quy tắc trên).

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `cp1_holes`, ảnh `000000144300.jpg`, vùng chiếc xe mô tô lớn trung tâm khung hình (`box=[43, 67, 588, 392]`).
- Lỗi thuộc loại: biên / khoét lỗ nhầm chi tiết: Model ban đầu có xu hướng khoét các khe hở ở kính chắn gió và khung sườn xe thành các lỗ nền (holes).
- Bằng chứng tôi nhìn thấy: Phần kính chắn gió trong suốt nhìn xuyên qua hậu cảnh và khe sườn xe bị tách rời khỏi mask của xe mô tô.
- Quy tắc và hành động sửa: Theo quy tắc đặc thù của `cp1_holes` (*"Holes: windows/gaps stay inside the mask — do NOT cut them out"*), kính và các chi tiết bên trong vật thể phải được giữ nguyên vẹn trong mask. Tôi đã dùng công cụ lấp kín các khe hở và bao trọn toàn bộ viền ngoài của xe mô tô.
- Sau sửa đã Save và export lại chưa? Đã Save trên CVAT và export lại thành `cp1_holes.zip`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa: Chạy `scripts/inspect_submissions.py` báo `[OK] cp1_holes: cp1_holes.zip` với 7 annotations polygon hợp lệ, cấu trúc chuẩn COCO 1.0.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `easy_semantic` (`7ee6d192-89e2408b.jpg`), mép đường giao với thảm cỏ | Lòng đường (`road`) hay Lớp khác (như terrain/vegetation) khi có cỏ phủ mỏng | Mặt đường có lớp cỏ phủ mỏng ở mép ngoài, nhưng chức năng xe không lưu thông | Quyết định chỉ gán `road` phần nhựa đường rõ rệt; mép cỏ và đất bên ngoài không gộp vào `road` để tránh làm sai lệch ranh giới chức năng. |
| 2. `cp4_curb` (`7d83710e-4697c3b2.jpg`), ranh giới vỉa hè lát đá và lòng đường | Vỉa hè (`sidewalk`) hay Lòng đường (`road`) khi cùng dải màu xám | Ranh giới phân định dựa trên độ cao gờ bó vỉa (curb) và chức năng dành cho người đi bộ chứ không dựa trên màu sắc | Quyết định vẽ đường ranh giới bám sát đường gờ bó vỉa, phân định rạch ròi `sidewalk` và `road`. |
| 3. `cp5_occlusion` (`000000336232.jpg`), xe ô tô bị người đi bộ che cắt đôi thân xe | Tách thành 2 instance xe riêng hay Giữ nguyên 1 instance xe | Quy tắc instance occlusion: một vật thể bị vật khác che cắt đôi vẫn là MỘT thực thể duy nhất | Quyết định gán chung cùng một ID nhóm (`group`) cho cả 2 phần nhìn thấy của chiếc xe, không đếm tăng thêm xe. |
