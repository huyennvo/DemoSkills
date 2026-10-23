# Business Scenario — Category (lọc sản phẩm theo danh mục)

> Nguồn: Postman collection `Demoblaze API Tests`, request `Monitors Category` (mô tả "Add Cat Information") + `PhoneCategory`, `Laptops Category`; đối chiếu API thật và JS frontend `index.js` (2026-10-21).
> Liên quan: `features/catalog/` (trang chủ, phân trang, chi tiết sản phẩm) — category là một phần của luồng xem catalog.

## Mô tả chung
Trên trang chủ Demoblaze, người dùng (guest hoặc đã đăng nhập) chọn một danh mục ở menu **CATEGORIES** để chỉ xem sản phẩm của danh mục đó. Frontend gọi `POST /bycat` và hiển thị danh sách trả về. Không cần đăng nhập, không thay đổi dữ liệu (chỉ đọc).

> ⚠️ Mô tả trong Postman ("Add Cat Information … add cat information", "cat: The category of the cat") **không đúng nghiệp vụ**: endpoint không thêm dữ liệu gì, `cat` là mã danh mục sản phẩm, không phải "con mèo". Thực tế là API **lọc/đọc** sản phẩm theo danh mục.

## Luồng nghiệp vụ: Chọn danh mục
1. Người dùng mở trang chủ → thấy menu CATEGORIES: Phones, Laptops, Monitors.
2. Người dùng click một danh mục → frontend gửi `POST /bycat { "cat": "<mã danh mục>" }`.
3. Hệ thống trả danh sách sản phẩm thuộc danh mục → frontend hiển thị (ảnh, title, price).
4. Người dùng click một sản phẩm → sang luồng chi tiết sản phẩm (`features/catalog`).

## Business rules
- **BR-CAT1:** Chỉ có 3 mã danh mục hợp lệ — UI → mã API: Phones → `phone`, Laptops → `notebook`, Monitors → `monitor`.
- **BR-CAT2:** Mọi sản phẩm trả về có `cat` đúng bằng mã danh mục đã gửi; mỗi sản phẩm thuộc đúng 1 danh mục.
- **BR-CAT3:** Mỗi sản phẩm có đủ 6 field `cat, desc, id (number), img, price (number), title` (schema Product — xem `detail-design.md`).
- **BR-CAT4:** So khớp `cat` chính xác, **phân biệt hoa thường**; mã không hợp lệ/rỗng → danh sách rỗng `{"Items": []}` (không báo lỗi).
- **BR-CAT5:** Thiếu field `cat` → lỗi nghiệp vụ HTTP 200 + `{"errorMessage": "Bad parameter, missing cat."}`.
- **BR-CAT6:** Kết quả lọc không phân trang — trả toàn bộ sản phẩm của danh mục trong 1 response.

## Test scenarios (từ Postman collection)

| # | Postman request | Input | Expected (Postman) |
|---|---|---|---|
| 1 | PhoneCategory | `{cat:"phone"}` | 200; `Items` array không rỗng; có "Samsung galaxy s6" giá 360.0 |
| 2 | Laptops Category | `{cat:"notebook"}` (URL lỗi `/bycat?=`) | 200; `Items` > 0; có "Sony vaio i5" |
| 3 | Monitors Category | `{cat:"monitor"}` | 200; `Items` > 0; có "Apple monitor 24" |

## Scenario bổ sung đề xuất (đã xác minh bằng request thật)
- Số lượng theo danh mục (snapshot 2026-10-21): phone 7, notebook 6, monitor 2 — tổng 15 = toàn catalog, không trùng id.
- `cat` ngoài enum (`tablet`), rỗng (`""`), sai hoa thường (`Phone`, `PHONE`) → `{"Items": []}`.
- Thiếu `cat` → `errorMessage: "Bad parameter, missing cat."`.
- Sai method (`GET`) → 405; không có `Content-Type: application/json` → 415; JSON sai cú pháp → 400.
