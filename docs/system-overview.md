# System Overview — Demoblaze

## 1. Kiến trúc

```
Browser (www.demoblaze.com — HTML + jQuery: index.js / prod.js / cart.js)
   │  $.ajax POST JSON (API_URL đọc từ config.json, mặc định https://api.demoblaze.com)
   ▼
API Gateway (api.demoblaze.com)  ──►  Backend serverless  ──►  DynamoDB-style store
                                                               (response dùng Items / Item /
                                                                LastEvaluatedKey / ScannedCount)
Video "About us": HLS stream (HLS_URL/index.m3u8) qua video.js
Ảnh sản phẩm: static www.demoblaze.com/imgs/*
```

- Response mang dấu hiệu DynamoDB (`Items`, `Item`, `LastEvaluatedKey`, `ScannedCount`) → backend dạng AWS Lambda + DynamoDB (suy luận từ format, không có tài liệu chính thức).
- Không có session server-side theo cookie HTTP: trạng thái đăng nhập lưu ở cookie trình duyệt `tokenp_=<token>`; giỏ hàng guest gắn với cookie `user=<guid>`.

## 2. Đối tượng người dùng
* **Guest:** xem catalog, giỏ hàng theo `user=<guid>` (`flag: false`).
* **Registered user:** giỏ hàng theo token (`flag: true`).

## 3. Luồng tổng quan

1. Mở trang chủ → nếu có cookie `tokenp_` → `POST /check {token}` → hiển thị "Welcome <username>"; nếu không → tạo cookie `user=<guid>`.
2. `GET /entries` → render 9 sản phẩm đầu + `LastEvaluatedKey` để phân trang.
3. Next/Previous → `POST /pagination {id}`; chọn danh mục → `POST /bycat {cat}`.
4. Click sản phẩm → `prod.html?idp_=<id>` → `POST /view {id}`.
5. "Add to cart" → `POST /addtocart {id: guid(), cookie, prod_id, flag}` → alert "Product added".
6. Trang Cart → `POST /viewcart {cookie, flag}` → với mỗi item gọi `POST /view {id: prod_id}` để lấy title/price, cộng Total.
7. Delete → `POST /deleteitem {id}`; Place Order (nhập Name + Credit card) → `POST /deletecart {cookie: username | document.cookie}` → popup "Thank you for your purchase!" (Id ngẫu nhiên, Amount, Card, Name, Date).

## 4. Quy ước lỗi chung

| errorMessage | Endpoint | Ý nghĩa |
|---|---|---|
| `Wrong password.` | /login | Sai mật khẩu |
| `User does not exist.` | /login | Username chưa đăng ký |
| `Bad parameter, missing username` | /login | Body thiếu username |
| `This user already exist.` | /signup | Trùng username |
| `Token has expired` | /check, /viewcart, /addtocart | Token hết hạn |
| `Bad parameter, token malformed.` | /check, /viewcart, /addtocart | Token sai định dạng |
| `Bad parameter, flag is incorrect.` | /check, /viewcart, /addtocart | `flag` không khớp loại cookie |
| `Not found.` | /view, /deleteitem | Không tìm thấy id |

Tất cả trả **HTTP 200**.
