# Resource endpoint và JSON API

> Hạng mục trong biên bản: Xây dựng API theo chuẩn RESTful  
> Phiên bản tài liệu: 1.0

## 1. Mục tiêu

Chuẩn hóa endpoint theo tài nguyên, sử dụng HTTP method đúng ý nghĩa và cung cấp JSON cho các tương tác bất đồng bộ cần thiết của Admin CMS/Public Website.

## 2. Phạm vi implementation

Project có 91 route được Laravel đăng ký. Kiến trúc là Blade-first, vì vậy:

- CRUD quản trị sử dụng resource-oriented route và đúng HTTP verb nhưng phần lớn trả Blade/redirect.
- Một số endpoint trả JSON cho search, Media Picker và upload bất đồng bộ.
- Public Website chủ yếu trả HTML.
- Chưa có `routes/api.php`, public API `/api/v1`, API Resource hoặc token authentication cho đối tác.

Nội dung này cần được hiểu là **RESTful resource endpoint cho ứng dụng web và JSON API nội bộ**, không phải một public integration API hoàn chỉnh.

## 3. Quy ước resource route

Ví dụ với `posts`:

| Method | URI | Route name | Chức năng |
| --- | --- | --- | --- |
| GET | `/admin/posts` | `admin.posts.index` | Danh sách |
| GET | `/admin/posts/create` | `admin.posts.create` | Form tạo |
| POST | `/admin/posts` | `admin.posts.store` | Tạo record |
| GET | `/admin/posts/{post}/edit` | `admin.posts.edit` | Form sửa |
| PUT | `/admin/posts/{post}` | `admin.posts.update` | Cập nhật |
| DELETE | `/admin/posts/{post}` | `admin.posts.destroy` | Xóa mềm |
| PATCH | `/admin/posts/{post}/restore` | `admin.posts.restore` | Khôi phục |
| DELETE | `/admin/posts/{post}/force-delete` | `admin.posts.force-delete` | Xóa vĩnh viễn |

Pattern tương tự được áp dụng cho Page, Document, Gallery, IssuingAgency, Widget, Media và User.

## 4. JSON endpoint

### 4.1. Tìm kiếm nhanh Admin

```http
GET /admin/search/suggestions?q=hop%20tac%20xa
Accept: application/json
```

Yêu cầu:

- Có session đăng nhập.
- `q` là chuỗi tùy chọn, tối đa 120 ký tự.

Response:

```json
{
  "data": {
    "query": "hop tac xa",
    "groups": [
      {
        "key": "posts",
        "label": "Bài viết",
        "icon": "newspaper",
        "items": []
      }
    ],
    "total": 0
  }
}
```

Kết quả được chuẩn hóa theo nhóm Post, Page và Document.

### 4.2. Tìm kiếm nhanh Public

```http
GET /tim-kiem?q=hop%20tac%20xa
Accept: application/json
```

Đặc điểm:

- Không yêu cầu đăng nhập.
- Chỉ tìm nội dung đã publish.
- Lọc locale theo hostname.
- Query ngắn hơn 2 ký tự trả danh sách rỗng.
- Mỗi nhóm Post/Document tối đa 5 kết quả.

### 4.3. Media Picker

```http
GET /admin/media/picker?q=logo&type=image
Accept: application/json
```

Response:

```json
{
  "media": [
    {
      "id": 1,
      "title": "Logo",
      "url": "https://example.test/storage/media/2026/07/uuid.png",
      "kind": "image",
      "isImage": true,
      "mimeType": "image/png",
      "humanSize": "120 KB",
      "dimensions": "1200x800"
    }
  ]
}
```

Endpoint trả tối đa 24 item, hỗ trợ loại `image` hoặc `document`.

### 4.4. Upload Media bất đồng bộ

```http
POST /admin/media
Accept: application/json
Content-Type: multipart/form-data
```

Field:

| Field | Yêu cầu |
| --- | --- |
| `file` | Bắt buộc, tối đa 10 MB |
| `title` | Tùy chọn, tối đa 255 ký tự |
| `alt_text` | Tùy chọn, tối đa 255 ký tự |
| `caption` | Tùy chọn, tối đa 1.000 ký tự |

Định dạng cho phép: JPG, JPEG, PNG, WEBP, GIF, PDF, DOC, DOCX, XLS, XLSX, TXT.

JSON upload thành công trả HTTP `201`:

```json
{
  "media": {
    "id": 1,
    "filename": "uuid.png",
    "url": "https://example.test/storage/media/2026/07/uuid.png",
    "kind": "image"
  }
}
```

Nếu request không yêu cầu JSON, cùng endpoint trả redirect và flash message cho flow Blade.

## 5. Public content route

| Method | URI | Chức năng |
| --- | --- | --- |
| GET | `/` | Trang chủ |
| GET | `/danh-muc/{category:slug}` | Danh sách bài theo chuyên mục |
| GET | `/bai-viet/{post:slug}` | Chi tiết bài |
| GET | `/trang/{page:slug}` | Trang tĩnh |
| GET | `/van-ban-dieu-hanh` | Danh sách văn bản điều hành |
| GET | `/van-ban-quy-pham-phap-luat` | Văn bản quy phạm pháp luật |
| GET | `/van-ban-dang` | Văn bản Đảng |
| GET | `/van-ban/{document:slug}` | Chi tiết văn bản |
| GET | `/thu-vien-anh/...` | Danh mục/album ảnh |
| GET | `/{slug}.html` | Chuyển hướng URL legacy |

Route model binding theo slug giúp định danh resource dễ đọc và hỗ trợ SEO.

## 6. Validation và status code

| Trường hợp | Hành vi |
| --- | --- |
| Thành công GET JSON | `200 OK` |
| Tạo Media JSON thành công | `201 Created` |
| Validation lỗi | `422 Unprocessable Entity` theo chuẩn Laravel |
| Chưa đăng nhập vào Admin | Redirect tới login đối với browser flow |
| Resource không tồn tại/không public | `404 Not Found` |
| Xóa/cập nhật Blade thành công | Redirect + flash message |

## 7. Authentication, session và CSRF

Các JSON endpoint Admin nằm trong middleware `web` và `auth`, sử dụng session cookie. POST/PATCH/DELETE chịu CSRF protection của Laravel.

Đây không phải API stateless. Client JavaScript chạy cùng ứng dụng và sử dụng session hiện tại của Admin.

## 8. Client integration

Alpine.js module gọi endpoint:

- `resources/js/admin/global-search.js`.
- `resources/js/admin/media-picker.js`.
- `resources/js/admin/gallery-media-picker.js`.
- `resources/js/admin/media-library.js`.
- `resources/js/public/search.js`.

JavaScript tách theo feature; entry `admin.js`/`public.js` chỉ đăng ký và khởi tạo module.

## 9. Kiểm thử

Feature test kiểm tra:

- JSON shape và kết quả search.
- Guest không truy cập Admin endpoint.
- Chỉ nội dung published xuất hiện ở public search.
- Upload Media tạo record/file và trả response đúng.
- Filter Media Picker.
- Resource route CRUD, validation, restore và force delete.
- Public route, route model binding và legacy redirect.

## 10. Hướng mở rộng public API

Nếu cần tích hợp bên thứ ba, cần thiết kế riêng:

- Prefix `/api/v1`.
- Stateless/token authentication.
- API Resource/ResourceCollection.
- Rate limiting.
- Pagination contract.
- Permission scope.
- Versioning và deprecation policy.
- OpenAPI specification.
- Integration test.

Việc mở public API là thay đổi phạm vi và bảo mật, cần được Bên A phê duyệt.

## 11. Kết luận

Hệ thống đã áp dụng resource-oriented route, HTTP verb đúng nghĩa và JSON response cho các tương tác nội bộ cần thiết. Public integration API chưa được triển khai vì kiến trúc hiện tại ưu tiên Blade; tài liệu ghi rõ ranh giới này để hạng mục nghiệm thu được hiểu đúng theo implementation.
