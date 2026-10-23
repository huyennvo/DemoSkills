# Global Data — Demoblaze

> Nguồn: Postman public workspace [asmaa-2393668/demoblaze](https://www.postman.com/asmaa-2393668/demoblaze) (workspace `DEMOBLAZE`, collection `Demoblaze API Tests`) + đối chiếu API thật và JS frontend `www.demoblaze.com` (ngày 2026-10-21).

## 1. Environment

| Thành phần | URL | Ghi chú |
|---|---|---|
| API (backend) | `https://api.demoblaze.com` | Giá trị của `{{baseUrl}}` trong collection (collection/environment trên Postman **không định nghĩa** biến này — phải tự set) |
| Web (frontend) | `https://www.demoblaze.com` | SPA jQuery; `index.html`, `prod.html?idp_=<id>`, `cart.html` |
| Ảnh sản phẩm | `https://www.demoblaze.com/imgs/<file>` | Field `img` trong response là đường dẫn tương đối (`imgs/galaxy_s6.jpg`) — host là **www**, không phải api (`api.demoblaze.com/imgs/...` → 404) |

- **Platform:** api (chính) — có thể mở rộng web
- **Môi trường:** chỉ có 1 môi trường public (production demo), không có staging riêng. Dữ liệu dùng chung với mọi người dùng internet.

## 2. Authentication
- **Login endpoint:** `POST /login`
- **Username field:** `username`
- **Password field:** `password` — frontend gửi **base64(password)** (`b64EncodeUnicode`), không phải plaintext
- **Token field:** response là **JSON string** dạng `"Auth_token: <token>"` (không phải object) → token = phần sau `Auth_token: `
- **Cách dùng token:** truyền trong **body** (`cookie` của `/addtocart`, `/viewcart`; `token` của `/check`) — API **không** dùng header `Authorization: Bearer` (collection có set bearer rỗng nhưng không có tác dụng)
- **Guest (chưa login):** frontend sinh cookie `user=<guid>` và gửi `cookie: "user=<guid>"`, `flag: false`

## 3. Test Credentials
- Giá trị ở `automation_test/.env` (gitignore) — key: `API_USERNAME`, `API_PASSWORD` (password dạng plaintext; keyword test tự base64 trước khi gửi)
- Nếu test thêm web: `WEB_USERNAME`, `WEB_PASSWORD`

## 4. Biến của Postman collection (map sang test data)

Các biến dưới đây được collection tham chiếu nhưng **không có giá trị** trong workspace public (environment `New Environment` rỗng, globals rỗng):

| Biến Postman | Ý nghĩa | Giá trị gợi ý / nguồn |
|---|---|---|
| `baseUrl` | API host | `https://api.demoblaze.com` |
| `validUsername`, `loginUsername` / `validPassword`, `loginPassword` | Tài khoản hợp lệ | `automation_test/.env` → `API_USERNAME` / `API_PASSWORD` |
| `incorrectPassword` | Sai mật khẩu | sinh ngẫu nhiên |
| `unregisteredUsername` / `registeredPassword` | User chưa đăng ký | sinh ngẫu nhiên (vd `zz_<timestamp>`) |
| `signupUsername` / `signupPassword` | Đăng ký | user đã tồn tại (test case "already exist") hoặc sinh mới |
| `authToken` | Token sau login | set động từ response `/login` |
| `phoneProductId` / `phoneProductId2` | id sản phẩm phone / id phân trang | `1` (Samsung galaxy s6) / `9` |
| `LaptopProductId` | id laptop | `8` (Sony vaio i5) |
| `MonitorProductId` | id monitor | `10` (Apple monitor 24) |
| `contactEmail`, `contactName`, `contactMessage` | Form Contact | dữ liệu giả — form Contact không gọi API |

## 5. Dữ liệu catalog tham chiếu (snapshot 2026-10-21)

| id | cat | title | price |
|---|---|---|---|
| 1 | phone | Samsung galaxy s6 | 360.0 |
| 2 | phone | Nokia lumia 1520 | 820.0 |
| 3 | phone | Nexus 6 | 650.0 |
| 4 | phone | Samsung galaxy s7 | 800.0 |
| 5 | phone | Iphone 6 32gb | 790.0 |
| 6 | phone | Sony xperia z5 | 320.0 |
| 7 | phone | HTC One M9 | 700.0 |
| 8 | notebook | Sony vaio i5 | 790.0 |
| 9 | notebook | Sony vaio i7`\n` (title có ký tự xuống dòng cuối) | 790.0 |
| 10 | monitor | Apple monitor 24 | 400.0 |
| 11 | notebook | MacBook air | 700.0 |
| 12 | notebook | Dell i7 8gb | 700.0 |
| 13 | notebook | 2017 Dell 15.6 Inch | 700.0 |
| 14 | monitor | ASUS Full HD | 230.0 |
| 15 | notebook | MacBook Pro | 1100.0 |

Tổng: 15 sản phẩm — phone 7, notebook 6, monitor 2. Dữ liệu public có thể thay đổi; test nên assert theo cấu trúc + sản phẩm "neo" (id 1, 8, 10) thay vì đếm cứng.
