# Detail Design — Login / Logout

## API

### POST /login
- **Request:** `{ "username": string, "password": string(base64) }`
- **Response OK:** JSON string `"Auth_token: <token>"` → token = `body.replace("Auth_token: ", "")`
- **Response lỗi (HTTP 200):**

| Điều kiện | Body |
|---|---|
| Username chưa đăng ký | `{"errorMessage":"User does not exist."}` (đã xác minh) |
| Sai password | `{"errorMessage":"Wrong password."}` |
| Thiếu username / body rỗng | `{"errorMessage":"Bad parameter, missing username"}` (đã xác minh) |

### POST /check
- **Request:** `{ "token": string }`
- **Response OK:** `{ "Item": { "username": string, ... } }`
- **Lỗi:** `Token has expired` / `Bad parameter, token malformed.` (đã xác minh với token rác) / `Bad parameter, flag is incorrect.`

## Client state
- Cookie `tokenp_=<token>` — trạng thái đăng nhập.
- Logout: `document.cookie = 'tokenp_=;expires=Thu, 01 Jan 1970 00:00:00 GMT'`.

## Data entities
* **USER:** `username`, `password` (base64), token (`Auth_token`).

## Lưu ý automation (RF / API)
- Tạo keyword `Encode Password` (base64) trước khi gọi `/login`.
- Không dùng `Object.keys(response)[0]` như collection — parse chuỗi.
- Assert `status == 200` **và** kiểm tra `errorMessage` không tồn tại cho case thành công.
