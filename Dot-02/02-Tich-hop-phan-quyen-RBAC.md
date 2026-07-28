# Xác thực và phân quyền truy cập

> Hạng mục trong biên bản: Tích hợp hệ thống phân quyền RBAC  
> Phiên bản tài liệu: 1.0

## 1. Mục tiêu

Hệ thống cần tách quyền truy cập giữa người dùng công khai và người quản trị, ngăn người chưa đăng nhập truy cập chức năng quản trị và tạo nền tảng để mở rộng phân quyền theo vai trò.

## 2. Kết luận kỹ thuật tại thời điểm đối chiếu

Implementation hiện có là **authentication và area-based access control**, gồm:

- Người dùng công khai/guest được truy cập Public Website.
- Tài khoản đã đăng nhập được truy cập toàn bộ khu vực Admin CMS.

Repository **chưa có granular RBAC đầy đủ** vì chưa có:

- Bảng `roles`, `permissions`.
- Bảng liên kết user-role hoặc role-permission.
- Thuộc tính role trên `users`.
- Laravel Gate/Policy kiểm tra quyền theo action/resource.
- Middleware `role:*` hoặc `permission:*`.
- Ma trận quyền theo `admin`, `editor`, `publisher` hoặc vai trò tương đương.

Vì vậy, nếu tiêu chí hợp đồng yêu cầu nhiều vai trò với quyền khác nhau, hạng mục cần được bổ sung ma trận quyền và implementation tương ứng. Tài liệu không gọi cơ chế hiện tại là RBAC chi tiết để tránh mô tả sai mã nguồn.

## 3. Nhóm truy cập hiện tại

| Nhóm | Public Website | Login | Admin CMS | Quản lý User |
| --- | --- | --- | --- | --- |
| Guest/độc giả | Có | Có | Không | Không |
| Authenticated Admin | Có | Bị chuyển về dashboard | Có | Có |

Mọi tài khoản trong `users` hiện có cùng khả năng truy cập các route Admin sau khi đăng nhập.

## 4. Luồng xác thực

### Login

```text
GET /admin/login
  -> hiển thị form

POST /admin/login
  -> validate email + password
  -> Auth::attempt(...)
  -> regenerate session
  -> redirect /admin
```

Nếu credential không hợp lệ, Laravel trả lỗi validation cho trường email và giữ user ở trạng thái guest.

### Logout

```text
POST /admin/logout
  -> Auth::logout()
  -> invalidate session
  -> regenerate CSRF token
  -> redirect /admin/login
```

## 5. Bảo vệ route

Route Admin được cấu hình:

```text
/admin/login                  -> guest
/admin/logout                 -> auth
/admin                        -> auth
/admin/pages/*                -> auth
/admin/posts/*                -> auth
/admin/documents/*            -> auth
/admin/galleries/*            -> auth
/admin/media/*                -> auth
/admin/categories/*           -> auth
/admin/issuing-agencies/*     -> auth
/admin/widgets/*              -> auth
/admin/users/*                -> auth
/admin/search/suggestions     -> auth
```

Public route chỉ dùng middleware `web` và không yêu cầu đăng nhập.

## 6. Các kiểm soát bổ sung

### Session và CSRF

- Khu vực browser dùng middleware group `web`.
- Form thay đổi dữ liệu được bảo vệ bằng CSRF token.
- Session được regenerate sau login.
- Session bị invalidate khi logout.

### Password

- `User` cast password bằng `hashed`, sử dụng hasher của Laravel.
- Password không xuất hiện trong fillable response hoặc Blade output.
- `password` và `remember_token` nằm trong hidden attributes.

### Validation

Form Request `authorize()` hiện trả `true`; quyền truy cập dựa vào middleware `auth`. Validation vẫn kiểm soát input và relationship của từng resource.

### Bảo vệ thao tác tài khoản

`UserController` ngăn tài khoản đang đăng nhập tự xóa chính mình. Đây là business guard, không thay thế RBAC.

## 7. Kiểm thử hiện có

`AdminLoginTest` kiểm tra:

- Trang login truy cập được với guest.
- Login route tồn tại.
- Credential đúng đăng nhập thành công.
- Credential sai bị từ chối.
- User đã đăng nhập không quay lại trang login.
- Logout hủy trạng thái xác thực.

Các Feature Test của module sử dụng:

- Guest bị chuyển đến trang login khi truy cập Admin.
- Authenticated user thực hiện workflow quản trị.

## 8. Mô hình RBAC đề xuất nếu mở rộng

Nếu Bên A yêu cầu phân quyền chi tiết, cần chốt ma trận trước khi triển khai. Ví dụ:

| Quyền | Administrator | Editor | Publisher |
| --- | ---: | ---: | ---: |
| Quản lý user/role | Có | Không | Không |
| Tạo/sửa nội dung | Có | Có | Có |
| Xuất bản nội dung | Có | Không | Có |
| Xóa/khôi phục | Có | Hạn chế | Có |
| Quản lý Media | Có | Có | Có |
| Cấu hình Widget | Có | Không | Có |

Các thành phần cần bổ sung:

```text
roles
permissions
role_user
permission_role
Policies hoặc Gates
role/permission middleware
Admin UI gán vai trò
Seed data
Authorization tests
```

Quyết định dùng package hoặc tự xây phải được phê duyệt trước vì project không cho phép tự thêm dependency.

## 9. Tiêu chí nghiệm thu granular RBAC

1. Có ma trận role-permission được Bên A phê duyệt.
2. User được gán một hoặc nhiều role theo thiết kế.
3. Backend kiểm tra quyền, không chỉ ẩn nút ở UI.
4. Từng action create/update/publish/delete/restore được authorize.
5. API/JSON endpoint áp dụng cùng chính sách.
6. Có test cho allow và deny của từng nhóm quyền quan trọng.
7. Thay đổi role/permission có audit nếu nghiệp vụ yêu cầu.

## 10. Kết luận

Hệ thống hiện bảo vệ đúng ranh giới Public/Admin bằng Laravel authentication, session, CSRF và middleware. Tuy nhiên, đây chưa phải RBAC nhiều vai trò. Việc ghi rõ trạng thái này giúp Bên A có cơ sở chốt xem access control hiện tại đã đủ phạm vi hay cần triển khai thêm ma trận quyền chi tiết.
