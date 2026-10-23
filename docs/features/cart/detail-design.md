# Detail Design — Cart & Place Order

## API

### POST /addtocart
- **Request:** `{ "id": string(guid), "cookie": string, "prod_id": number|string, "flag": boolean }`
- **Lỗi:** `Token has expired` / `Bad parameter, token malformed.` (đã xác minh) / `Bad parameter, flag is incorrect.`

### POST /viewcart
- **Request:** `{ "cookie": string, "flag": boolean }` (collection gửi dư `id`, `prod_id` — không cần)
- **Response:** `{ "Items": [ { "cookie": string, "id": string, "prod_id": number } ] }`; giỏ trống → `{"Items":[]}` (đã xác minh)

### POST /deleteitem
- **Request:** `{ "id": string }` — id của cart item (guid lúc add), **không phải** prod_id
- **Response:** id không tồn tại → `{"errorMessage":"Not found."}` (đã xác minh)

### POST /deletecart
- **Request:** `{ "cookie": string }` — login: **username** (lấy từ `/check` → `Item.username`); guest: chuỗi `document.cookie`
- Gọi khi Place Order.

## Data entities
* **CART_ITEM:** `id` (guid client sinh), `cookie` (token hoặc `user=<guid>`), `prod_id` (→ PRODUCT.id).
* **ORDER:** không lưu server — popup client: `Id` (random 1..10,000,000), `Amount`, `Card Number`, `Name`, `Date`.

## Lưu ý automation
- Sinh guid mới cho mỗi add (`uuid4`), không dùng id hard-code của collection.
- Cookie lấy từ token login (`automation_test/.env` → `API_USERNAME`/`API_PASSWORD`), không dùng cookie hard-code `dHJpcGxlX2ExNzQ1MTE1`.
- Teardown: xoá các cart item đã tạo (`/deleteitem`) để không làm bẩn giỏ dùng chung.
