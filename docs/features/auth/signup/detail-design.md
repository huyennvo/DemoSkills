# Detail Design — Sign up

## API

### POST /signup
- **Request:** `{ "username": string, "password": string(base64) }`
- **Response OK:** không có `errorMessage` (FE coi mọi response không lỗi là thành công) — body cụ thể chưa xác minh
- **Response lỗi (HTTP 200):**

| Điều kiện | Body |
|---|---|
| Username đã tồn tại | `{"errorMessage":"This user already exist."}` |
| Thiếu username / password | `errorMessage` chứa tên field thiếu (theo collection — chưa xác minh) |

## Data entities
* **USER:** `username` (unique), `password` (base64).

## Lưu ý automation
- Sinh username duy nhất: `qa_demo_<epoch>_<random>`.
- Case "already exist": dùng `API_USERNAME` trong `automation_test/.env`.
