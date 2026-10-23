# Detail Design — Category (POST /bycat)

> Nguồn: Postman request `Monitors Category` ("Add Cat Information") + xác minh API thật 2026-10-21.

## API

### POST /bycat
- **URL:** `{{baseUrl}}/bycat` = `https://api.demoblaze.com/bycat` (Postman `Laptops Category` có `?=` thừa — bỏ đi)
- **Auth:** không cần
- **Header:** `Content-Type: application/json` (bắt buộc)
- **Request body:**

| Field | Kiểu | Bắt buộc | Giá trị | Ghi chú |
|---|---|---|---|---|
| `cat` | string | Có | `phone` \| `notebook` \| `monitor` | Mã danh mục sản phẩm (Postman ghi nhầm "The category of the cat") — phân biệt hoa thường |

- **Response thành công:** HTTP 200, `{ "Items": [Product] }` — không có `LastEvaluatedKey` (không phân trang)

Mỗi phần tử `Items` (Product):

| Field | Kiểu | Mô tả | Ví dụ |
|---|---|---|---|
| `cat` | string | Mã danh mục của sản phẩm | `"monitor"` |
| `desc` | string | Mô tả sản phẩm (có thể chứa `\n`) | `"LED Cinema Display features..."` |
| `id` | number | Định danh duy nhất của sản phẩm | `10` |
| `img` | string | Đường dẫn ảnh **tương đối**, host `https://www.demoblaze.com/` | `"imgs/apple_cinema.jpg"` |
| `price` | number | Giá (float) | `400.0` |
| `title` | string | Tên sản phẩm (có thể có `\n` cuối — vd id 9) | `"Apple monitor 24"` |

### JSON schema Product (dùng validate)
```json
{
  "type": "object",
  "required": ["cat", "desc", "id", "img", "price", "title"],
  "properties": {
    "cat": {"type": "string", "enum": ["phone", "notebook", "monitor"]},
    "desc": {"type": "string"},
    "id": {"type": "number"},
    "img": {"type": "string"},
    "price": {"type": "number"},
    "title": {"type": "string"}
  }
}
```

## Dữ liệu tham chiếu theo danh mục (snapshot 2026-10-21)

| cat | Số SP | id | Sản phẩm "neo" để assert |
|---|---|---|---|
| `phone` | 7 | 1, 2, 3, 4, 5, 6, 7 | id 1 — Samsung galaxy s6 — 360.0 |
| `notebook` | 6 | 8, 9, 11, 12, 13, 15 | id 8 — Sony vaio i5 — 790.0 |
| `monitor` | 2 | 10, 14 | id 10 — Apple monitor 24 — 400.0 |

Danh sách đầy đủ title/price: `automation_test/docs/global-data.md` §5. Dữ liệu public có thể thay đổi — ưu tiên assert theo cấu trúc + sản phẩm neo.

## Hành vi biên & lỗi (xác minh bằng request thật 2026-10-21)

| Request | HTTP | Response |
|---|---|---|
| `{cat:"tablet"}` (ngoài enum) | 200 | `{"Items":[]}` |
| `{cat:""}` | 200 | `{"Items":[]}` |
| `{cat:"Phone"}` / `{cat:"PHONE"}` | 200 | `{"Items":[]}` — phân biệt hoa thường |
| `{}` (thiếu cat) | 200 | `{"errorMessage":"Bad parameter, missing cat."}` |
| `GET /bycat` | 405 | HTML "Method Not Allowed" |
| POST không có `Content-Type: application/json` | 415 | HTML "Unsupported Media Type" |
| Body JSON sai cú pháp (`{cat:`) | 400 | HTML "Bad Request" |

## Sai khác giữa tài liệu Postman và thực tế
1. Tên/mô tả "Add Cat Information — add cat information" → thực tế là **lọc sản phẩm theo danh mục** (chỉ đọc, không thêm dữ liệu).
2. "cat: The category of the cat" → `cat` là **mã danh mục sản phẩm**, chỉ nhận 3 giá trị `phone`/`notebook`/`monitor`.
3. Postman chỉ mô tả response 200 thành công — không nêu hành vi khi `cat` sai/rỗng/thiếu (bổ sung ở bảng "Hành vi biên & lỗi").

## Lưu ý automation
- So sánh `title` nên trim (id 9 có `\n` cuối).
- Lỗi nghiệp vụ trả HTTP 200 + `errorMessage` → assert theo body, không chỉ status.
- TC tương ứng hiện nằm trong `features/catalog/test-cases.md`: TC-004 → TC-014.
