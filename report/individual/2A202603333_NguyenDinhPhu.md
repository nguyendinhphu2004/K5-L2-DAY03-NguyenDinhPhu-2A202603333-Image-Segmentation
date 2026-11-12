# Báo cáo thực hành Day 3

- Mã học viên theo lớp: 2A202603333
- Ngày / CVAT local: 11–12/11/2026 / CVAT v2.74.1 chạy local
- Công cụ đã dùng: CVAT (Polygon, Brush, OpenCV Intelligent Scissors)

## 1. Bài đã nộp

| Task            | File ZIP đúng tên   | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --------------- | ------------------- | -----------------: | ---------------------------: |
| easy_semantic   | easy_semantic.zip   |              3 / 3 |                           20 |
| medium_instance | medium_instance.zip |              3 / 3 |                           32 |
| hard_panoptic   | hard_panoptic.zip   |              2 / 2 |                           30 |
| cp1_holes       | cp1_holes.zip       |              1 / 1 |                            3 |
| cp2_slice       | cp2_slice.zip       |              1 / 1 |                            3 |
| cp5_occlusion   | cp5_occlusion.zip   |              1 / 1 |                            3 |
| cp3_thin        | cp3_thin.zip        |              1 / 1 |                            3 |
| cp4_curb        | cp4_curb.zip        |              1 / 1 |                            3 |
| cp6_coverage    | cp6_coverage.zip    |              1 / 1 |                            3 |
| **Tổng tối đa** |                     |                    |                      **100** |

Cả 9 ZIP được `scripts/inspect_submissions.py` báo `OK`.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh 000000181542.jpg, xe buýt đỏ ở góc trên bên trái.
- Class và quy tắc tôi dùng để chọn biên: class `bus`; chỉ vẽ phần nhìn thấy, dừng mask ở chỗ taxi trắng và người lái xe máy che, không vẽ xuyên qua.
- Nếu dùng gợi ý sau đó: không dùng (không có SAM).
- Nếu không dùng gợi ý: taxi trắng và xe buýt đứng sát nhau nhưng là hai object riêng, mỗi vật một mask.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: hard_panoptic, ảnh 000000350023.jpg, vùng `sky`.
- Lỗi thuộc loại: khác (sai loại shape, thiếu mask `sky`).
- Bằng chứng tôi nhìn thấy: script báo `LỖI` "annotation 1 thiếu polygon/RLE hợp lệ". Trong JSON, `sky` có `segmentation` rỗng và `area` = 640 × 294.72 đúng bằng bbox, tức đã vẽ bằng Rectangle.
- Quy tắc và hành động sửa: panoptic cần mask theo vùng, nên tôi xóa Rectangle và thay bằng polygon. Còn hạn chế: polygon mới chỉ 4 điểm (tới y ≈ 306), chưa bám đường chân trời, còn phủ lên tòa nhà và cây phía dưới.
- Sau sửa đã Save và export lại chưa? Rồi; script báo `OK`.

Kết quả tự kiểm: trước sửa `LỖI`, sau sửa `OK`. Chưa có điểm.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp4_curb: dải cỏ/đất giữa bó vỉa và vỉa hè lát | `sidewalk` hoặc để trống | Bộ nhãn chỉ có `road`, `sidewalk`, không có `terrain` | Để trống. Hỏi coach: dải cỏ và bó vỉa có tính là `sidewalk` không? |
| cp4_curb: bãi đỗ xe phía trên bên phải (có xe SUV trắng) | `sidewalk` hoặc để trống | Chức năng là bãi đỗ xe, không phải lối đi bộ | Để trống |
| hard_panoptic: `road` (ảnh 000000460147.jpg) và `sky` (ảnh 000000350023.jpg) phủ cả vùng có vật khác đè lên | Chấp nhận chồng lấn do layer, hoặc cắt mask stuff ra | COCO không lưu thứ tự layer; đo pixel ảnh 460147 chồng lấn khoảng 24,6% | Chưa kịp cắt lại. Hỏi coach scorer xử lý chồng lấn thế nào |
