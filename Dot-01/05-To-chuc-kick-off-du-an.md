# Tổ chức Kick-off dự án

> Hạng mục: Quản lý, điều phối và tổ chức họp khởi động dự án  
> Phiên bản tài liệu: 1.0  
> Phạm vi tài liệu: Baseline kỹ thuật dùng trong Kick-off

## 1. Mục đích

Kick-off thống nhất giữa Bên A và Bên B về:

- Mục tiêu sản phẩm.
- Phạm vi chức năng và ngoài phạm vi.
- Kiến trúc và công nghệ.
- Trách nhiệm của các nhóm tham gia.
- Kế hoạch cung cấp dữ liệu, hạ tầng và phản hồi.
- Tiêu chí kiểm thử, bàn giao và nghiệm thu.
- Cách quản lý thay đổi và rủi ro.

Tài liệu này ghi baseline kỹ thuật cần được trao đổi trong Kick-off. Ngày họp, danh sách người tham dự, nội dung phát biểu và chữ ký xác nhận thuộc biên bản họp chính thức bên ngoài repository.

## 2. Thành phần theo vai trò

| Vai trò | Trách nhiệm |
| --- | --- |
| Đại diện Bên A | Chốt yêu cầu, phạm vi, nội dung và tiêu chí nghiệm thu |
| Chủ nhiệm/đầu mối Bên A | Cung cấp dữ liệu, phản hồi và điều phối người dùng kiểm thử |
| Quản lý dự án Bên B | Kế hoạch, tiến độ, rủi ro, báo cáo và phối hợp |
| Technical Lead | Kiến trúc, tiêu chuẩn kỹ thuật và quyết định triển khai |
| Backend Developer | Laravel, database, migration, CMS và API/endpoint |
| Frontend Developer | Blade, responsive UI và frontend asset |
| Data Engineer/Developer | Phân tích dữ liệu CodeIgniter, transform và đối soát |
| QA/Tester | Test plan, regression, UAT support và báo cáo lỗi |
| DevOps/Vận hành | Development, staging, backup, deployment và monitoring |

Một người có thể đảm nhiệm nhiều vai trò tùy cơ cấu dự án.

## 3. Nội dung Kick-off

### 3.1. Bối cảnh

- Hệ thống cũ dùng CodeIgniter và có khối lượng nội dung lịch sử lớn.
- Hệ thống mới dùng Laravel để tăng khả năng bảo trì, kiểm thử và mở rộng.
- Website gồm Admin CMS và Public Website.
- Yêu cầu quan trọng là bảo toàn nội dung, file và URL trong quá trình chuyển đổi.

### 3.2. Mục tiêu sản phẩm

- Quản lý tập trung nội dung chính thức của VCA.
- Cho phép biên tập, lưu nháp, xuất bản, lưu trữ và khôi phục.
- Cung cấp trải nghiệm đọc tin, tra cứu văn bản và xem thư viện ảnh.
- Hỗ trợ tiếng Việt và tiếng Anh.
- Có nền tảng kỹ thuật dễ vận hành và phát triển tiếp.

### 3.3. Phạm vi đã thống nhất về kỹ thuật

| Khu vực | Phạm vi |
| --- | --- |
| Admin CMS | Authentication, dashboard, Post, Page, Category, Document, IssuingAgency, Media, Gallery, Widget, User, search |
| Public Website | Homepage, category, post, page, document, gallery, search, legacy redirect |
| Dữ liệu | Bài viết, văn bản, chuyên mục, cơ quan ban hành, album và đường dẫn file/ảnh cũ |
| Hạ tầng | Development, staging, build, test, migration, backup và deployment |

### 3.4. Ngoài phạm vi

- CRM hoặc quản lý thành viên hợp tác xã.
- Workflow duyệt bài nhiều cấp.
- Marketplace.
- Public API cho hệ thống đối tác.
- Dịch nội dung tự động.

Các nội dung ngoài phạm vi chỉ được triển khai sau khi có yêu cầu và đánh giá thay đổi.

## 4. Quyết định kỹ thuật nền

| Chủ đề | Quyết định |
| --- | --- |
| Kiến trúc | Laravel monolith |
| Rendering | Blade server-side cho Admin và Public |
| Frontend | Tailwind CSS, Alpine.js, Vite |
| Database | MySQL/MariaDB, thay đổi qua migration |
| Data access | Eloquent; Repository cho query lặp lại/phức tạp |
| Business logic | Service cho nghiệp vụ nhiều bước |
| Media | Laravel Storage và Media module dùng chung |
| Đa ngôn ngữ | Record riêng theo `locale`, liên kết bằng `translation_key` |
| Dữ liệu cũ | SQL transform, legacy path fallback và URL redirect |
| Test | PHPUnit Feature/Unit test và Vite build |

## 5. Đầu vào Bên A cần cung cấp

- Database dump CodeIgniter đầy đủ và đúng phiên bản.
- Bộ thư mục file upload/ảnh/tài liệu cũ.
- Danh sách domain/subdomain dự kiến.
- Quy tắc phân loại nội dung và trạng thái xuất bản.
- Danh sách tài khoản hoặc ma trận vai trò nếu cần phân quyền chi tiết.
- Nội dung/asset chính thức: logo, banner, favicon, thông tin liên hệ.
- Thông tin hạ tầng staging theo kênh bảo mật.
- Người dùng đại diện thực hiện UAT.

## 6. Đầu ra Bên B

- Repository Laravel và lịch sử thay đổi.
- Database migration.
- Admin CMS và Public Website.
- Bộ SQL/script chuyển đổi dữ liệu.
- Automated test và kết quả build.
- Hướng dẫn cài đặt, triển khai và rollback.
- Tài liệu kỹ thuật theo hạng mục.
- Hỗ trợ triển khai staging và xử lý lỗi trong phạm vi.

## 7. Phương thức phối hợp

| Hoạt động | Nội dung |
| --- | --- |
| Báo cáo tiến độ | Công việc hoàn thành, đang làm, rủi ro và nội dung cần xác nhận |
| Review chức năng | Demo theo module hoặc mốc nghiệm thu |
| Quản lý yêu cầu | Ghi nhận yêu cầu, phân loại trong/ngoài phạm vi, đánh giá tác động |
| Quản lý lỗi | Có bước tái hiện, mức ưu tiên, người xử lý và kết quả retest |
| Phê duyệt | Thực hiện qua đầu mối được chỉ định và lưu vết |
| Trao đổi bảo mật | Credential và dữ liệu nhạy cảm gửi qua kênh được phê duyệt, không đưa vào Git |

## 8. Nguyên tắc quản lý thay đổi

Một thay đổi cần ghi rõ:

1. Nội dung yêu cầu.
2. Lý do và mức ưu tiên.
3. Module bị ảnh hưởng.
4. Tác động database, dữ liệu, UI và hạ tầng.
5. Tác động tiến độ/nghiệm thu.
6. Người xác nhận.

Thay đổi kiến trúc, thêm dependency, mở API ngoài hoặc sửa schema lớn phải được đánh giá và phê duyệt trước.

## 9. Tiêu chí nghiệm thu chung

- Chức năng đúng phạm vi và hoạt động trên staging.
- Dữ liệu đầu vào được validation.
- Nội dung chưa xuất bản không hiển thị public.
- Dữ liệu chuyển đổi được đối soát số lượng và kiểm tra mẫu.
- File/URL legacy quan trọng truy cập được hoặc có báo cáo ngoại lệ.
- Automated test và frontend build đạt.
- Không có lỗi nghiêm trọng chưa xử lý.
- Có hướng dẫn vận hành và tài liệu kỹ thuật.

## 10. Rủi ro cần thống nhất sớm

| Rủi ro | Bên phối hợp chính | Xử lý |
| --- | --- | --- |
| Database dump không đầy đủ | Bên A + Data team | Xác nhận phiên bản và checksum |
| File legacy thiếu | Bên A + Vận hành | Kiểm kê, bổ sung hoặc lập danh sách ngoại lệ |
| Yêu cầu RBAC chưa có ma trận quyền | Bên A + Technical Lead | Chốt role/permission matrix |
| Khác biệt môi trường | DevOps | Chuẩn hóa version và deployment checklist |
| Thay đổi giao diện muộn | Bên A + Frontend | Review theo mốc và quản lý change request |
| Thời gian import kéo dài | Data team + DevOps | Chạy rehearsal trên bản sao |

## 11. Action checklist sau Kick-off

- [ ] Chốt đầu mối Bên A/Bên B.
- [ ] Chốt phạm vi và ngoài phạm vi.
- [ ] Bàn giao database dump và file legacy.
- [ ] Chốt thông tin staging qua kênh bảo mật.
- [ ] Chốt ma trận vai trò/quyền nếu yêu cầu granular RBAC.
- [ ] Chốt tiêu chí UAT và nghiệm thu.
- [ ] Chốt cách ghi nhận lỗi/yêu cầu thay đổi.
- [ ] Chốt lịch demo, review và báo cáo tiến độ.
- [ ] Lưu biên bản họp và xác nhận của các bên.

## 12. Kết luận

Kick-off tạo baseline chung cho phạm vi, kiến trúc, dữ liệu, trách nhiệm và tiêu chí nghiệm thu. Baseline này giúp các nhóm làm việc trên cùng một định nghĩa và giảm rủi ro phát sinh do khác biệt kỳ vọng trong quá trình triển khai.
