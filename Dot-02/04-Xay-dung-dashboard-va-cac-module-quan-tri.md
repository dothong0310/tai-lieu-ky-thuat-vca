# Dashboard, module quản trị, Media và giao diện Responsive

> Hạng mục: Dashboard; quản lý bài viết, chuyên mục, tags, Media; giao diện công khai Responsive  
> Phiên bản tài liệu: 1.0

## 1. Mục tiêu

Cung cấp một Admin CMS thực dụng cho việc quản trị nội dung hằng ngày và một Public Website responsive để độc giả xem tin tức, văn bản, trang thông tin và thư viện ảnh trên nhiều kích thước màn hình.

## 2. Công nghệ giao diện

| Thành phần | Công nghệ |
| --- | --- |
| Server-side view | Blade anonymous components |
| Styling | Tailwind CSS 4, mobile-first |
| UI state | Alpine.js 3 |
| Rich text editor | TipTap 3 |
| Slider/carousel | Splide 4 |
| Icon | Lucide |
| Date/time input | Flatpickr |
| Build | Vite 8 |

Admin và Public có CSS/JavaScript entry riêng để tránh tải các thư viện không cần thiết giữa hai khu vực.

## 3. Dashboard Admin

Dashboard tại `/admin` có:

- Các card tổng quan Post, Page, Document, Media.
- Khu vực nội dung gần đây.
- Tổng quan trạng thái xuất bản.
- Danh sách module CMS.
- Layout sidebar, topbar, global search và profile menu.

### Trạng thái dữ liệu dashboard

Layout và component dashboard đã được xây dựng. Tuy nhiên, `DashboardController` hiện truyền các giá trị khởi tạo tĩnh (`0` và dữ liệu mẫu), chưa query thống kê thật từ database. Vì vậy dashboard hiện phù hợp để nghiệm thu layout/điều hướng, chưa phù hợp làm báo cáo số liệu vận hành.

## 4. Module quản trị

### 4.1. Bài viết

- Danh sách có tìm kiếm, locale, status, category và thùng rác.
- Tạo, sửa, xóa mềm, khôi phục, xóa vĩnh viễn.
- Tiêu đề phụ, mô tả, nội dung rich text.
- Chuyên mục, ngôn ngữ, trạng thái, lịch xuất bản.
- Bài nổi bật và số lượt xem.
- Thumbnail qua Media Picker hoặc fallback legacy.
- Metadata SEO.
- Tags được lưu trong trường `posts.tags`.

Lưu ý: tags hiện là trường text trên Post, chưa có bảng `tags` và quan hệ nhiều-nhiều.

### 4.2. Chuyên mục

- Phân loại `post`, `document`, `gallery`.
- Cấu trúc cha/con.
- Locale, thứ tự hiển thị, trạng thái active.
- Ảnh bìa qua Media hoặc đường dẫn legacy.
- Kiểm tra type/locale của category cha.

### 4.3. Trang tĩnh

- CRUD Page.
- Nội dung rich text.
- Locale và translation key.
- Trạng thái/lịch xuất bản.
- Metadata SEO.
- Xóa mềm, khôi phục và xóa vĩnh viễn.

### 4.4. Văn bản

- Số/ký hiệu, tiêu đề, người ký.
- Nhóm văn bản: điều hành, quy phạm pháp luật, văn bản Đảng.
- Loại văn bản và cơ quan ban hành.
- Ngày ban hành, ngày hiệu lực, năm.
- File từ Media hoặc legacy path.
- Filter/search/pagination.
- Trạng thái, lịch xuất bản, metadata SEO.

### 4.5. Cơ quan ban hành

- CRUD danh mục cơ quan.
- Trạng thái active/inactive.
- Theo dõi người tạo/người cập nhật.
- Xóa mềm, khôi phục và xóa vĩnh viễn.

### 4.6. Media Library

- Upload file dùng chung.
- Grid thư viện có search và filter image/document/other.
- Lưu tiêu đề, alt text, caption.
- Hiển thị dung lượng, MIME type, extension và kích thước ảnh.
- Media Picker dùng lại trong Post, Document và Gallery.
- Xóa mềm, khôi phục và xóa vĩnh viễn.

### 4.7. Gallery

- Quản lý album và chuyên mục gallery.
- Chọn nhiều ảnh từ Media Picker.
- Chọn cover riêng.
- Giữ đồng thời ảnh Media mới và ảnh legacy.
- Public index, category và detail.

### 4.8. Widget

- Nội dung theo placement và slot.
- Locale, type, sort order.
- Trạng thái/lịch xuất bản.
- Hiển thị động trên các vùng của trang chủ.

### 4.9. User và Profile

- Danh sách, tạo, sửa, xóa mềm và khôi phục tài khoản.
- Thông tin name, email, username, address, phone, mã định danh, ngày sinh.
- Ngăn tài khoản đang đăng nhập tự xóa.
- Trang hồ sơ cá nhân cơ bản.

## 5. Rich text và an toàn nội dung

Component `<x-rich-editor>` bọc TipTap và hỗ trợ:

- Heading, paragraph và text formatting.
- Link, image.
- Table.
- Text alignment.
- Placeholder và toolbar.

Nội dung từ editor được đồng bộ về form field và xử lý qua `RichContentSanitizer` trước khi lưu/hiển thị theo flow service. Sanitizer có unit test riêng.

## 6. Quản lý và tối ưu Media

### Đã triển khai

- File vật lý lưu theo `media/YYYY/MM/{uuid}.{extension}`.
- Không dùng tên gốc làm tên vật lý.
- Metadata và kích thước ảnh được lưu trong database.
- Module khác chỉ tham chiếu `media_id`.
- Public URL được tạo qua Laravel Storage.
- File tối đa 10 MB và giới hạn danh sách extension.
- Admin/Public dùng `alt_text` để hỗ trợ accessibility khi có dữ liệu.

### Giới hạn

Repository chưa có pipeline tạo thumbnail, resize, nén ảnh hoặc chuyển định dạng tự động. Vì vậy “tối ưu hóa hình ảnh” hiện bao gồm quản lý file an toàn, metadata và khả năng tái sử dụng; không nên hiểu là image processing tự động.

## 7. Public Website

### Màn hình

- Homepage với tin nổi bật và các section nội dung.
- Danh sách bài theo chuyên mục.
- Chi tiết bài viết và tin liên quan.
- Trang tĩnh.
- Danh sách văn bản theo ba nhóm.
- Filter văn bản theo category, cơ quan, năm và từ khóa.
- Chi tiết và tải file văn bản.
- Thư viện ảnh theo category/album.
- Gợi ý tìm kiếm.
- Legacy URL redirect.

### Quy tắc hiển thị

- Chỉ nội dung `published`.
- Tôn trọng `published_at`.
- Lọc locale từ hostname.
- Ưu tiên Media mới, fallback legacy path.
- Danh sách dùng pagination.

## 8. Responsive design

Tailwind được triển khai mobile-first:

- Layout tự co giãn trên mobile, tablet và desktop.
- Table Admin có vùng cuộn ngang khi cần.
- Form và card chuyển cột theo breakpoint.
- Public header có menu riêng cho viewport nhỏ.
- Grid bài viết, văn bản và gallery thay đổi số cột theo breakpoint.
- Image sử dụng kích thước container và tỉ lệ phù hợp.
- Viewport meta được khai báo trong cả Admin và Public layout.

## 9. Component tái sử dụng

| Nhóm | Component |
| --- | --- |
| Layout | `layouts/admin`, `layouts/public`, `layouts/admin-auth` |
| Navigation | Admin sidebar/topbar, Public header/footer |
| UI cơ bản | Button, Badge, Card, Icon, Modal, Flash message |
| Form | Admin form component theo module |
| Nội dung | Rich editor |
| Media | Media Picker, Gallery Media Picker |
| Public | Featured posts, news feature section, widget stack, logo slider |

Component giúp đồng nhất style và giảm lặp HTML/Tailwind.

## 10. Kiểm thử

Feature test bao phủ module:

- Dashboard và authentication.
- Post, Page, Category, Document.
- IssuingAgency, Gallery, Widget, Media, User.
- Public Home, Post/Page/Category, Document, Gallery, Search.
- Legacy media/file và URL redirect.

Vite production build ngày 28/07/2026 đã transform 1.800 module thành công.

## 11. Bằng chứng đối chiếu

- Admin layout: `resources/views/components/layouts/admin.blade.php`.
- Public layout: `resources/views/components/layouts/public.blade.php`.
- Các view module nằm trong `resources/views/admin` và `resources/views/public`.
- Frontend module tách trong `resources/js/admin`, `resources/js/public`, `resources/js/shared`.
- 194 automated test đạt.

## 12. Kết luận

Các module CMS chính, Media Library, rich editor và Public Website responsive đã hiện diện và có test. Ba giới hạn cần được hiểu rõ là dashboard chưa dùng thống kê động, tags chưa phải entity riêng và chưa có image processing tự động.
