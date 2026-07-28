# Kế hoạch triển khai dự án

> Hạng mục: Lập kế hoạch triển khai dự án chi tiết  
> Phiên bản tài liệu: 1.0  
> Ngày cập nhật: 28/07/2026

## 1. Mục tiêu kế hoạch

Kế hoạch tổ chức việc chuyển đổi cổng thông tin VCA từ CodeIgniter sang Laravel theo từng nhóm có thể kiểm tra độc lập, ưu tiên:

- Hoàn thiện nền tảng CMS trước khi mở rộng tính năng.
- Duy trì khả năng truy cập nội dung cũ.
- Tách rõ công việc kỹ thuật, dữ liệu, hạ tầng và nghiệm thu.
- Có tiêu chí hoàn thành cho từng hạng mục.
- Giảm rủi ro khi chuyển đổi lượng dữ liệu lớn.

Tài liệu này mô tả kế hoạch kỹ thuật. Mốc ngày, nhân sự chỉ định và chữ ký phê duyệt được quản lý trong kế hoạch/biên bản hành chính của dự án.

## 2. Phạm vi sản phẩm

### Trong phạm vi

- Admin authentication và giao diện quản trị.
- Quản lý Page, Post, Category, Document, IssuingAgency.
- Quản lý Media, Gallery và Widget.
- Quản lý User và hồ sơ cơ bản.
- Public homepage, bài viết, chuyên mục, trang tĩnh, văn bản, thư viện ảnh.
- Tìm kiếm nhanh Admin và Public.
- Nội dung `vi`/`en`.
- Chuyển hướng URL cũ và dữ liệu legacy.
- Automated test và frontend build.

### Ngoài phạm vi hiện tại

- CRM quản lý hợp tác xã/thành viên.
- Workflow duyệt nhiều cấp.
- Public API cho đối tác bên ngoài.
- Dịch máy hoặc AI tự động.
- Marketplace hoặc thương mại điện tử.

## 3. Các giai đoạn thực hiện

| Giai đoạn | Công việc chính | Đầu ra |
| --- | --- | --- |
| 1. Khảo sát | Phân tích CodeIgniter, dữ liệu nguồn, URL và nghiệp vụ | Product context, domain model, phạm vi chuyển đổi |
| 2. Kiến trúc | Chọn Laravel monolith, phân tầng và tổ chức route/view | Architecture document, ADR |
| 3. Nền tảng dữ liệu | Thiết kế schema, migration, model, factory | Migration và Eloquent model |
| 4. Admin CMS | Authentication, layout, CRUD, Media Picker, rich editor | Khu vực `/admin` |
| 5. Public Website | Homepage, nội dung, văn bản, gallery, search | Các route và Blade public |
| 6. Chuyển đổi | Tạo SQL transform, import, legacy fallback/redirect | Bộ `data_files` và kiểm tra đối soát |
| 7. Kiểm thử | Feature test, unit test, build frontend | Báo cáo test/build |
| 8. Staging/nghiệm thu | Deploy, smoke test, UAT, xử lý phản hồi | Phiên bản staging và hồ sơ nghiệm thu |

## 4. Phân chia theo đợt nghiệm thu

### Đợt 1: Nền tảng và tổ chức dự án

| Hạng mục | Deliverable kỹ thuật | Tiêu chí hoàn thành |
| --- | --- | --- |
| Phân tích và kiến trúc | Product context, domain model, architecture, ADR | Phạm vi và luồng hệ thống được thống nhất |
| Database và migration | Schema, migration, index, model relation | Có thể dựng database mới bằng Artisan |
| Project plan | Kế hoạch giai đoạn, deliverable, rủi ro | Có đầu ra và tiêu chí kiểm tra cho từng giai đoạn |
| Development/Staging | Hướng dẫn cấu hình, build, test, deploy | Có thể cài đặt và chạy ứng dụng theo quy trình |
| Kick-off | Baseline phạm vi, vai trò và phương thức phối hợp | Các bên hiểu đầu ra và cơ chế thay đổi |

### Đợt 2: Chức năng và dữ liệu

| Hạng mục | Deliverable kỹ thuật | Tiêu chí hoàn thành |
| --- | --- | --- |
| Models, Controllers, Middleware | Domain model, controller, request, service, repository, route protection | Workflow quản trị/public chạy qua đúng layer |
| Xác thực/phân quyền | Login, logout, session, bảo vệ `/admin` | Guest không truy cập được Admin |
| RESTful endpoint | Resource route đúng HTTP verb, JSON endpoint nội bộ | Route được đăng ký, validate và trả response phù hợp |
| Dashboard và module CMS | Admin/Public UI, CRUD, Media, Responsive | Workflow nội dung chính có test |
| Chuyển đổi dữ liệu | SQL nguồn/đích, mapping, legacy fallback | Đối soát record và kiểm tra mẫu dữ liệu |

## 5. Definition of Done

Một hạng mục kỹ thuật được coi là hoàn thành khi:

1. Mã nguồn nằm đúng cấu trúc dự án và tuân thủ Laravel convention.
2. Không thay đổi `.env` thật hoặc đưa credential vào Git.
3. Thay đổi schema có migration mới.
4. Validation và access control phù hợp phạm vi.
5. Có test cho workflow hoặc rủi ro chính.
6. `php artisan test` thành công.
7. `npm run build` thành công nếu liên quan frontend.
8. Tài liệu kỹ thuật và hướng dẫn vận hành được cập nhật.
9. Có thể kiểm tra lại bằng route, UI hoặc truy vấn đối soát.

## 6. Chiến lược kiểm thử

| Lớp kiểm thử | Nội dung |
| --- | --- |
| Feature test | Authentication, CRUD Admin, Public route, search, legacy redirect |
| Unit test | Logic xử lý rich content |
| Database test | Migration, validation, soft delete, restore, filter |
| Frontend build | Compile Tailwind, JavaScript module và vendor library |
| Smoke test staging | Login, tạo/sửa nội dung, upload, public rendering, download |
| Data validation | Record count, foreign reference, status, URL/file sample |

Tại ngày 28/07/2026, bộ kiểm thử tự động có 194 test và 973 assertion, toàn bộ đạt.

## 7. Kế hoạch release

```text
Chốt phiên bản
  -> Backup database và file
  -> Cài dependency production
  -> Build asset
  -> Bật maintenance mode
  -> Chạy migration
  -> Chạy import nếu có
  -> Clear/cache cấu hình
  -> Smoke test
  -> Tắt maintenance mode
  -> Theo dõi log
```

Mỗi release cần có:

- Commit/tag hoặc mã phiên bản xác định.
- Danh sách migration mới.
- Kết quả test/build.
- Hướng dẫn rollback.
- Người thực hiện và người xác nhận.

## 8. Quản lý thay đổi

Yêu cầu mới được phân loại:

| Loại | Cách xử lý |
| --- | --- |
| Sửa lỗi trong phạm vi | Ghi nhận, tái hiện, thêm test và sửa trong phiên bản gần nhất |
| Thay đổi UI nhỏ | Đánh giá tác động view/component và cập nhật build |
| Thay đổi schema | Thiết kế migration mới, backup và kế hoạch rollback |
| Thay đổi kiến trúc | Lập ADR, đánh giá chi phí/rủi ro và xin phê duyệt |
| Thêm package/integration | Đánh giá bảo mật, vận hành, license và xin phê duyệt |
| Mở rộng ngoài phạm vi | Ước lượng và bổ sung kế hoạch/đợt nghiệm thu |

## 9. Rủi ro và biện pháp

| Rủi ro | Mức ảnh hưởng | Biện pháp |
| --- | --- | --- |
| Dữ liệu CodeIgniter không đồng nhất | Cao | Chuẩn hóa trường, giữ legacy path, đối soát record và sample |
| File cũ thiếu hoặc đường dẫn lỗi | Cao | Kiểm kê file trên staging, fallback path, báo cáo file thiếu |
| URL cũ mất truy cập | Cao | Legacy redirect và kiểm tra mẫu URL |
| Migration trên dữ liệu lớn kéo dài | Cao | Chạy thử bản sao, chia batch, backup và maintenance window |
| Khác biệt local/staging | Trung bình | Chuẩn hóa version runtime, `.env` key và deployment checklist |
| Public query lộ nội dung draft | Cao | Dùng published scope, locale filter và feature test |
| Phạm vi RBAC chưa đủ chi tiết | Trung bình | Chốt ma trận vai trò/quyền trước khi triển khai granular RBAC |
| Dashboard dùng số liệu tĩnh | Thấp | Bổ sung query tổng hợp trước khi dùng dashboard làm báo cáo vận hành |

## 10. Trạng thái đối chiếu hiện tại

| Nhóm | Trạng thái kỹ thuật |
| --- | --- |
| Kiến trúc và domain | Đã có trong repository |
| Migration và model | Đã có |
| Admin CMS và Public Website | Đã có các module chính |
| Dữ liệu chuyển đổi SQL | Đã có |
| Automated test | 194/194 đạt |
| Frontend production build | Đạt |
| Granular RBAC | Chưa có role/permission matrix trong source |
| Public `/api/v1` | Chưa mở, không thuộc Blade-first scope hiện tại |
| Dynamic dashboard metrics | Chưa nối database |
| Chứng từ hạ tầng và phê duyệt | Quản lý ngoài repository |

## 11. Kết luận

Kế hoạch chia dự án theo nền tảng, module, dữ liệu và nghiệm thu giúp mỗi phần có thể kiểm tra độc lập. Các giới hạn được ghi rõ để Bên A có căn cứ phân biệt phần đã hiện diện trong mã nguồn và phần cần hồ sơ hạ tầng hoặc quyết định phạm vi bổ sung.
