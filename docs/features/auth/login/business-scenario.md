# Business Scenario — Login / Logout

> Nguồn: Postman folder `Login Page`, `HomePage/Login`, `Logout Page` + JS frontend (`logIn()`, `logOut()`, `/check`).

## Luồng nghiệp vụ: Đăng nhập
1. User mở modal "Log in", nhập Username + Password.
2. Nếu thiếu 1 trong 2 → FE alert "Please fill out Username and Password." (không gọi API).
3. FE gửi `POST /login { username, password: base64(password) }`.
4. Thành công → response `"Auth_token: <token>"` → FE lưu cookie `tokenp_=<token>`, reload trang.
5. Mỗi lần load trang → `POST /check { token }` → hiển thị "Welcome <username>", ẩn nút Sign up / Log in, hiện Log out.
6. Lỗi → alert theo `errorMessage`: "Wrong password." / "User does not exist.".

## Luồng nghiệp vụ: Đăng xuất
1. User click "Log out" → FE xoá cookie `tokenp_` (expires 1970) → về trang chủ.
2. **Không có API logout** — token cũ không bị thu hồi phía server.

## Business rules
- BR-L1: Password được gửi dưới dạng base64 (từ FE).
- BR-L2: Lỗi trả HTTP 200 + `errorMessage`.
- BR-L3: Response thất bại không chứa token.
- BR-L4: Token hết hạn → `/check` trả "Token has expired" → FE yêu cầu login lại.

## Test scenarios (từ Postman collection)

| # | Postman request | Input | Expected |
|---|---|---|---|
| 1 | Login (HomePage) / Login valid u&p | username + password hợp lệ | 200; body không rỗng; chứa `Auth_token: ` → lưu `authToken` |
| 2 | Login - Unregistered Username, Registered Password | username chưa đăng ký | 200; `errorMessage` chứa "user does not exist"; không có `token`/`Item` |
| 3 | Login - Valid Username, Incorrect Password | sai password | 200; `errorMessage` chứa "wrong password"; không có token |
| 4 | Login - Empty Username, Password | body `{}` | 200; `errorMessage` = "Bad parameter, missing username" (không nhắc password) |
| 5 | Logout link | `GET /entries` | 200; `Items` là array (thực chất chỉ kiểm tra trang chủ load lại) |

## Scenario bổ sung đề xuất (chưa có trong collection)
- Login với password plaintext vs base64 cho user tạo từ UI.
- `POST /check` với token hợp lệ → `Item.username` đúng user; token rác → "Bad parameter, token malformed.".
- Username có khoảng trắng / phân biệt hoa thường.
- Chỉ có username, thiếu password.
