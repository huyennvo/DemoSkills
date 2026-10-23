# Business Scenario — Cart & Place Order

> Nguồn: Postman folder `Cart Page` + JS frontend `prod.js` (`addToCart`), `cart.js` (`viewcart`, `deleteItem`, `deleteCart`, `purchaseOrder`).

## Luồng nghiệp vụ: Thêm vào giỏ
1. Ở trang chi tiết, click "Add to cart".
2. Đã login → `POST /addtocart { id: guid(), cookie: <token>, prod_id, flag: true }` → alert "Product added.".
3. Guest → `POST /addtocart { id: guid(), cookie: "user=<guid>", prod_id, flag: false }` → alert "Product added".
4. Token lỗi → alert theo errorMessage (expired / malformed / flag incorrect).

## Luồng nghiệp vụ: Xem giỏ
1. Mở trang Cart → `POST /viewcart { cookie, flag }`.
2. Với mỗi item `{cookie, id, prod_id}` → `POST /view { id: prod_id }` → hiển thị ảnh, title, price, link Delete.
3. Total = tổng `parseInt(price)`.

## Luồng nghiệp vụ: Xoá item
1. Click "Delete" → `POST /deleteitem { id: <cart item id> }` → reload.

## Luồng nghiệp vụ: Place Order
1. Click "Place Order" → modal nhập Name, Country, City, Credit card, Month, Year.
2. Thiếu Name hoặc Credit card → alert "Please fill out Name and Creditcard.".
3. Hợp lệ → `POST /deletecart { cookie: <username nếu login | document.cookie nếu guest> }` → popup "Thank you for your purchase!" (Id ngẫu nhiên, Amount = Total, Card, Name, Date) → OK về `index.html`.
4. Không có API tạo đơn hàng — "đặt hàng" = xoá giỏ.

## Business rules
- BR-CA1: Mỗi lần add tạo cart item mới (id = guid client) — add trùng sản phẩm tạo nhiều dòng.
- BR-CA2: `flag` phải khớp loại cookie (true = token, false = guest cookie), sai → "Bad parameter, flag is incorrect.".
- BR-CA3: Total chỉ cộng phần nguyên của price.
- BR-CA4: Chỉ bắt buộc Name + Credit card khi Place Order (không validate định dạng thẻ).

## Test scenarios (từ Postman collection)

| # | Postman request | Input | Expected |
|---|---|---|---|
| 1 | Add to Cart | id + cookie hard-code, `prod_id: phoneProductId`, `flag: true` | 200 |
| 2 | Go to Cart Page | cookie + id hard-code, `flag: true` | 200; object; nếu `Items` không rỗng mỗi item có `cookie`, `id`, `prod_id` |
| 3 | Delete from Cart | `id` hard-code | 200; `errorMessage` ∈ {undefined, "Not found."} |

## Scenario bổ sung đề xuất (E2E API)
1. Login → lấy token → add `prod_id=1` → `/viewcart` chứa item `prod_id=1` với id vừa sinh → `/deleteitem` → `/viewcart` không còn item.
2. Guest flow với `cookie: "user=<guid>"`, `flag: false`.
3. `/addtocart` token rác → "Bad parameter, token malformed." (đã xác minh).
4. `/addtocart` token hợp lệ + `flag: false` → "Bad parameter, flag is incorrect.".
5. `/viewcart` cookie guest mới → `Items: []` (đã xác minh).
6. Add 2 sản phẩm → tổng price khớp; `/deletecart` → giỏ trống.
