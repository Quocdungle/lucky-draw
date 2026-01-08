# Vòng Quay May Mắn - Lucky Wheel

## 1. Cấu trúc thư mục

Đảm bảo thư mục của bạn có các file sau:

- index.html (Chứa toàn bộ code giao diện và logic)
- web.jpg (Ảnh nền)
- Vòng quay.png (Ảnh vòng quay tròn)
- Nút bấm quay.png (Ảnh nút quay)
- README.md (File hướng dẫn này)

## 2. Cấu hình Logic

- **Tỉ lệ trúng thưởng:** Đã được cài đặt trong `index.html` biến `prizes`.
  - 20%: Vé 1tr8 (Highlight)
  - 40%: Vé 1tr6
  - 40%: Vé 1tr4
- **Database:** Sử dụng Google Sheets.
  - Sheet `Codes`: Chứa danh sách mã code.
  - Sheet `Winners`: Lưu người trúng.

## 3. Cách Deploy lên GitHub Pages (Miễn phí)

### Bước 1: Đẩy code lên GitHub

1. Tạo một tài khoản GitHub nếu chưa có.
2. Tạo một Repository mới (đặt tên ví dụ: `lucky-wheel`).
3. Upload toàn bộ các file (`index.html`, ảnh...) lên Repository đó.

### Bước 2: Bật GitHub Pages

1. Vào Repository vừa tạo, chọn **Settings**.
2. Tìm mục **Pages** ở menu bên trái.
3. Tại phần **Source**, chọn `Deploy from a branch`.
4. Tại phần **Branch**, chọn `main` (hoặc `master`) và folder `/ (root)`.
5. Nhấn **Save**.
6. Đợi khoảng 1-2 phút, GitHub sẽ cung cấp cho bạn một đường link (ví dụ: `https://username.github.io/lucky-wheel`).

### Bước 3: Admin quản lý

- Để thêm code mới: Vào Google Sheet tab `Codes`, thêm mã vào cột A.
- Để xem người trúng: Vào Google Sheet tab `Winners`.
- Để xuất Excel: Tại Google Sheet > Tệp > Tải xuống > Microsoft Excel (.xlsx).

## 4. Lưu ý quan trọng

- Logic quay và xóa code được thực hiện ngay khi user ấn nút "QUAY NGAY" hợp lệ để tránh trường hợp user quay thấy giải thấp thì F5 lại trang.
- Đảm bảo ảnh vòng quay `Vòng quay.png` là ảnh vuông, nền trong suốt (PNG) và các múi chia đều hoặc khớp với góc độ trong code JS.
