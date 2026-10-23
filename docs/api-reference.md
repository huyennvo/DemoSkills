# API Reference — Demoblaze

- **Base URL:** `https://api.demoblaze.com` (`{{baseUrl}}`)
- **Content-Type:** `application/json` cho mọi request có body
- **Auth:** token/cookie truyền trong body (không dùng header Authorization)
- **Lỗi:** HTTP 200 + `{"errorMessage": "..."}` (xem `system-overview.md` §4)
- Cột "Postman" = tên request trong collection `Demoblaze API Tests`; "(FE)" = endpoint chỉ thấy trong JS frontend, collection chưa có.

## Authentication (No Auth Required)

| Method | Endpoint | Body | Response thành công | Postman |
|---|---|---|---|---|
| POST | /signup | `{ username, password }` (password base64) | Không có `errorMessage` (body cụ thể chưa xác minh) — FE alert "Sign up successful." | signup valid u&p; Signup - Valid Username, Empty Password; Signup - Valid password, Empty Username; Signup -Empty Username and password; Contact (thực chất gọi /signup) |
| POST | /login | `{ username, password }` (password base64) | JSON string `"Auth_token: <token>"` | HomePage/Login; Login valid u&p; Login - Unregistered Username, Registered Password; Login - Valid Username, Incorrect Password; Login - Empty Username, Password |
| POST | /check | `{ token }` | `{ Item: { username, ... } }` | (FE) |

## Catalog

| Method | Endpoint | Body | Response | Postman |
|---|---|---|---|---|
| GET | /entries | — | `{ Items: [Product], LastEvaluatedKey: { id: "9" } }` — 9 sản phẩm đầu | Navigate to Homepage via Logo; Logout link; Contact - Send Message |
| POST | /bycat | `{ cat: "phone" \| "notebook" \| "monitor" }` | `{ Items: [Product] }` | PhoneCategory; Laptops Category; Monitors Category |
| POST | /pagination | `{ id: "<LastEvaluatedKey.id>" }` | `{ Items: [Product], LastEvaluatedKey: { id }, ScannedCount }` — sản phẩm có id > `id` | Next button at phone category; Next button at Laptops category; previous button at phone category |
| POST | /view | `{ id: "<product id>" }` | `Product` (object) — id không tồn tại → `{ errorMessage: "Not found." }` | Phone/Laptops/Monitors CategoryPoductDetails |

**Product:** `{ cat: string, desc: string, id: number, img: string (tương đối, vd "imgs/galaxy_s6.jpg"), price: number, title: string }`

## Cart

| Method | Endpoint | Body | Response | Postman |
|---|---|---|---|---|
| POST | /addtocart | `{ id: <guid mới>, cookie: <token \| "user=<guid>">, prod_id: <number>, flag: <true nếu login \| false nếu guest> }` | Không có `errorMessage` khi OK (body chưa xác minh); lỗi token → errorMessage | Add to Cart |
| POST | /viewcart | `{ cookie: <token \| "user=<guid>">, flag: <bool> }` | `{ Items: [{ cookie, id, prod_id }] }` — cart trống → `{ Items: [] }` | Go to Cart Page |
| POST | /deleteitem | `{ id: <cart item id> }` | Không có `errorMessage` khi OK; id không tồn tại → `{ errorMessage: "Not found." }` | Delete from Cart |
| POST | /deletecart | `{ cookie: <username nếu login \| document.cookie nếu guest> }` | — (FE bỏ qua response) | (FE) — Place Order |

## Static

| Method | URL | Ghi chú | Postman |
|---|---|---|---|
| GET | `{{baseUrl}}/imgs/front.jpg` | Trên host api → **404** (`text/html`); ảnh thật ở `https://www.demoblaze.com/imgs/front.jpg` → 200 `image/jpeg` | About us link (test kỳ vọng 404) |

## Ghi chú về Postman collection (vấn đề phát hiện khi đối chiếu)

1. `{{baseUrl}}` và các biến `*ProductId`, `validUsername`... không có giá trị trong workspace public — cần tự cấu hình.
2. Request "Laptops Category" có URL lỗi `{{baseUrl}}/bycat?=` (query rỗng) — vẫn chạy nhưng nên bỏ `?=`.
3. "Monitors Category" có mô tả sai ("Add Cat Information … category of the cat").
4. Test "Store the authentication token" dùng `Object.keys(responseJson)[0]` — response `/login` là **chuỗi**, không phải object → logic lấy token sai; token đúng = `response.replace("Auth_token: ", "")`.
5. Header bearer token (rỗng) trên `/view`, `/pagination`, cart — API không dùng.
6. Cart request hard-code `id: "2d397d2e-..."` và `cookie: "dHJpcGxlX2ExNzQ1MTE1"` (cookie của 1 user cụ thể) — cần thay bằng guid mới + token từ login. Body `/viewcart` dư `id`, `prod_id`.
7. Test product detail/pagination ("Response body has product details") **không có assertion**; test signup/login negative chấp nhận `[400, 200]` / `[401, 200]` — API thực tế luôn trả 200.
8. "Logout link" và "Contact - Send Message" chỉ gọi `GET /entries` — logout và contact không có API (xử lý thuần client). Request "Contact" (POST) thực chất là `/signup`.
9. "Next button at Laptops category" dùng `phoneProductId2` — phân trang không theo danh mục, `/pagination` duyệt toàn bộ catalog theo id.
10. Login/signup trong collection gửi password plaintext; FE gửi base64 → tài khoản tạo qua UI chỉ login được qua API khi gửi base64.
