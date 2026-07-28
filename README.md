# Tài liệu kỹ thuật VCA Portal CMS

> Phiên bản tài liệu: 1.0  
> Phạm vi: Các hạng mục nghiệm thu đợt 1 và đợt 2

## Mục đích

Thư mục này tập hợp tài liệu kỹ thuật bàn giao cho hệ thống VCA Portal CMS. Mỗi tài liệu tương ứng với một hạng mục trong file `Nghiem-thu-dot-1-2.docx`, giúp Bên A:

- Hiểu mục tiêu, phạm vi và cách triển khai của từng hạng mục.
- Đối chiếu hạng mục nghiệm thu với cấu trúc mã nguồn hiện có.
- Có cơ sở vận hành, bảo trì và mở rộng hệ thống.
- Phân biệt nội dung đã hiện diện trong repository với thông tin hạ tầng hoặc thủ tục được quản lý bên ngoài repository.

## Danh mục tài liệu

### Đợt 1

| STT | Hạng mục | Tài liệu |
| --- | --- | --- |
| 1 | Phân tích hệ thống và thiết kế kiến trúc Laravel | [Phân tích và thiết kế kiến trúc](Dot-01/01-Phan-tich-va-thiet-ke-kien-truc.md) |
| 2 | Xây dựng Database Schema và Migration Scripts | [Thiết kế database và migration](Dot-01/02-Thiet-ke-database-va-migration.md) |
| 3 | Lập kế hoạch triển khai dự án chi tiết | [Kế hoạch triển khai dự án](Dot-01/03-Ke-hoach-trien-khai-du-an.md) |
| 4 | Thiết lập môi trường Development và Staging | [Thiết lập môi trường Development và Staging](Dot-01/04-Thiet-lap-moi-truong-development-staging.md) |
| 5 | Quản lý, điều phối và tổ chức Kick-off Meeting | [Tổ chức Kick-off dự án](Dot-01/05-To-chuc-kick-off-du-an.md) |

### Đợt 2

| STT | Hạng mục | Tài liệu |
| --- | --- | --- |
| 1 | Xây dựng Models, Controllers, Middleware | [Models, Controllers và Middleware](Dot-02/01-Xay-dung-model-controller-middleware.md) |
| 2 | Tích hợp hệ thống phân quyền RBAC | [Xác thực và phân quyền truy cập](Dot-02/02-Tich-hop-phan-quyen-RBAC.md) |
| 3 | Xây dựng API theo chuẩn RESTful | [Resource endpoint và JSON API](Dot-02/03-Xay-dung-RESTful-API.md) |
| 4 | Dashboard, module nội dung, Media và giao diện Responsive | [Dashboard và các module quản trị](Dot-02/04-Xay-dung-dashboard-va-cac-module-quan-tri.md) |
| 5 | Chuyển đổi dữ liệu CodeIgniter sang Laravel | [Chuyển đổi dữ liệu CodeIgniter sang Laravel](Dot-02/05-Chuyen-doi-du-lieu-CodeIgniter-sang-Laravel.md) |

## Thông tin kỹ thuật tại thời điểm đối chiếu

| Nội dung | Kết quả |
| --- | --- |
| Kiến trúc | Laravel monolith, server-side rendering bằng Blade |
| Backend | PHP `^8.3`, Laravel `^13.8` |
| Frontend | Blade, Tailwind CSS 4, Alpine.js 3, Vite 8 |
| Database local mặc định | MySQL theo `.env.example` |
| Model nghiệp vụ | 9 model: `User`, `Category`, `Post`, `Page`, `Document`, `IssuingAgency`, `Media`, `Gallery`, `Widget` |
| Route đã đăng ký | 91 route, gồm Admin CMS, Public Website, endpoint JSON nội bộ và route hệ thống |
| Automated test | 194 test, 973 assertion; toàn bộ đạt ngày 28/07/2026 |
| Frontend build | Vite production build thành công ngày 28/07/2026 |
| Dữ liệu chuyển đổi chính | 20.924 bài viết, 595 văn bản, 1.243 bản ghi ảnh nguồn cùng dữ liệu danh mục liên quan |

## Lưu ý về phạm vi xác nhận

- Tài liệu được viết dựa trên mã nguồn, migration, test và dữ liệu hiện diện trong repository tại ngày đối chiếu.
- Thông tin bí mật như IP staging, SSH key, mật khẩu, token CI/CD và thông tin truy cập database không được ghi trong repository.
- Biên bản phê duyệt kế hoạch, danh sách người tham dự và biên bản Kick-off có chữ ký là hồ sơ hành chính riêng; tài liệu tại đây mô tả cơ sở kỹ thuật của các hoạt động đó.
- Phân quyền hiện tại bảo vệ toàn bộ khu vực `/admin` bằng xác thực phiên đăng nhập. Repository chưa có bảng `roles`, `permissions`, Laravel Policy hoặc Gate để phân quyền chi tiết theo vai trò.
- Hệ thống dùng resource-oriented route và đúng HTTP verb cho CRUD; JSON được dùng cho tìm kiếm, Media Picker và upload bất đồng bộ. Repository chưa công bố public API phiên bản hóa dạng `/api/v1`.
- Dashboard quản trị đã có layout và các vùng hiển thị, nhưng số liệu tổng hợp trong `DashboardController` hiện là dữ liệu khởi tạo tĩnh, chưa phải thống kê động từ database.
- Các file vật lý legacy phải được đối soát riêng trên staging; repository chủ yếu chứa SQL chuyển đổi và đường dẫn tham chiếu đến file cũ.

## Quy ước cập nhật

Khi hệ thống thay đổi, tài liệu liên quan cần được cập nhật cùng phiên bản mã nguồn. Mỗi lần cập nhật nên ghi lại:

1. Ngày đối chiếu.
2. Phạm vi thay đổi.
3. Migration hoặc route mới.
4. Kết quả test và frontend build.
5. Các giới hạn hoặc công việc còn lại.
