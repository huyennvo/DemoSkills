# Business Scenario — Catalog (Home, Category, Pagination, Product detail, About, Contact)

> Nguồn: Postman folder `HomePage`, `About Page`, `Contact Page` + JS frontend `index.js`, `prod.js`.

## Luồng nghiệp vụ: Trang chủ & danh mục
1. Mở trang chủ (hoặc click logo) → `GET /entries` → hiển thị 9 sản phẩm đầu (id 1–9), nhận `LastEvaluatedKey.id = "9"`.
2. Click "Next" → `POST /pagination { id: "9" }` → các sản phẩm id > 9.
3. Click "Previous" → `POST /pagination { id: <prev id> }`.
4. Chọn danh mục Phones / Laptops / Monitors → `POST /bycat { cat: phone | notebook | monitor }`.

## Luồng nghiệp vụ: Chi tiết sản phẩm
1. Click sản phẩm → `prod.html?idp_=<id>` → `POST /view { id }` → title, price, desc, ảnh.
2. Nút "Add to cart" → xem `features/cart`.

## About us / Contact
- **About us:** modal phát video HLS (`HLS_URL/index.m3u8`) — không có API nghiệp vụ.
- **Contact:** nhập Email, Name, Message → "Send message" → alert "Thanks for the message!!", reload. **Không gọi API**, không validate field.

## Business rules
- BR-C1: Danh mục hợp lệ: `phone`, `notebook`, `monitor` (UI: Phones, Laptops, Monitors).
- BR-C2: Mỗi sản phẩm có `cat, desc, id(number), img, price(number), title`.
- BR-C3: Phân trang theo id toàn catalog (không theo danh mục).
- BR-C4: `/view` với id không tồn tại → `errorMessage: "Not found."` (HTTP 200).

## Test scenarios (từ Postman collection)

| # | Postman request | Input | Expected |
|---|---|---|---|
| 1 | PhoneCategory | `{cat:"phone"}` | 200; `Items` array không rỗng; có "Samsung galaxy s6" giá 360.0 |
| 2 | Laptops Category | `{cat:"notebook"}` | 200; `Items` > 0; có "Sony vaio i5" |
| 3 | Monitors Category | `{cat:"monitor"}` | 200; `Items` > 0; có "Apple monitor 24" |
| 4 | Phone CategoryPoductDetails | `{id: phoneProductId}` | 200; body là object (thiếu assertion chi tiết) |
| 5 | Laptops CategoryPoductDetails | `{id: LaptopProductId}` | như #4 |
| 6 | Monitors CategoryPoductDetails | `{id: MonitorProductId}` | như #4 |
| 7 | Next button at phone category | `{id: phoneProductId}` | 200; object |
| 8 | Next button at Laptops category | `{id: phoneProductId2}` | 200; object |
| 9 | previous button at phone category | `{id: phoneProductId}` | 200; object |
| 10 | Navigate to Homepage via Logo | `GET /entries` | 200; `Items` > 0; item đầu có `cat, desc, id(number), img, price(number), title`; có `LastEvaluatedKey` object |
| 11 | About us link | `GET {{baseUrl}}/imgs/front.jpg` | 404; có header Content-Type |
| 12 | Contact - Send Message | `GET /entries?...` | 200; body không rỗng; value đầu tiên là array |

## Scenario bổ sung đề xuất
- `/view` id hợp lệ → assert đúng title/price/cat (vd id 1 → Samsung galaxy s6, 360.0, phone).
- `/view` id không tồn tại (9999) → "Not found.".
- `/bycat` cat không hợp lệ (`tablet`, rỗng) → `Items: []`.
- `/pagination {id:"9"}` → 6 item id 10–15, `LastEvaluatedKey.id = "15"`, `ScannedCount = 6`.
- Tổng `/bycat` 3 danh mục = tổng sản phẩm toàn catalog; mọi item của `/bycat` có `cat` đúng danh mục.
