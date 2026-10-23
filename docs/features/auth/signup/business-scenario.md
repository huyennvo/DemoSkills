# Business Scenario — Sign up

> Nguồn: Postman folder `Signup Page` (+ request `Contact` trong `Contact Page` thực chất gọi `/signup`) + JS frontend `register()`.

## Luồng nghiệp vụ
1. User mở modal "Sign up", nhập Username + Password.
2. Thiếu 1 trong 2 → FE alert "Please fill out Username and Password." (không gọi API).
3. FE gửi `POST /signup { username, password: base64(password) }`.
4. Thành công → alert "Sign up successful."; trùng username → alert "This user already exist.".

## Business rules
- BR-S1: Username là duy nhất.
- BR-S2: Password gửi base64 (FE).
- BR-S3: Lỗi trả HTTP 200 + `errorMessage`.
- BR-S4: Không có API xoá user → mỗi lần test đăng ký mới để lại dữ liệu vĩnh viễn trên môi trường public.

## Test scenarios (từ Postman collection)

| # | Postman request | Input | Expected (theo collection) |
|---|---|---|---|
| 1 | signup valid u&p | `signupUsername` đã tồn tại + password | 200; `errorMessage` = "This user already exist." |
| 2 | Signup - Valid Username, Empty Password | chỉ `username` | 400 hoặc 200; `errorMessage` chứa "password" |
| 3 | Signup - Valid password, Empty Username | chỉ `password` | 400 hoặc 200; body chứa "username" |
| 4 | Signup -Empty Username and password | `{}` | 400 hoặc 200; body chứa "username" và "password" |
| 5 | Contact (POST /signup) | giống #1 | giống #1 |

> Kỳ vọng ở #2–#4 lấy từ collection, **chưa xác minh** với API thật (không chạy để tránh tạo user rác). Cần chạy 1 lần để chốt message thực tế trước khi automate.

## Scenario bổ sung đề xuất
- Đăng ký user mới (username sinh ngẫu nhiên) → thành công → login được bằng cùng password (base64).
- Username có ký tự đặc biệt / rất dài.
