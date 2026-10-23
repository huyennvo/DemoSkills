# Product Overview — Demoblaze

> Nguồn: Postman workspace [asmaa-2393668/demoblaze](https://www.postman.com/asmaa-2393668/demoblaze) — collection `Demoblaze API Tests` ("The whole project endpints.") + quan sát website `www.demoblaze.com`.

## Mô tả sản phẩm
**Demoblaze (PRODUCT STORE)** là website thương mại điện tử demo (của BlazeMeter) bán thiết bị điện tử, dùng phổ biến làm sandbox luyện kiểm thử UI/API. Người dùng có thể xem sản phẩm theo danh mục, xem chi tiết, thêm vào giỏ hàng, xoá khỏi giỏ và đặt hàng (giả lập). Không có thanh toán thật.

## Đối tượng người dùng
- **Guest (khách):** xem catalog, thêm giỏ hàng theo cookie `user=<guid>`, đặt hàng.
- **Registered user:** đăng ký, đăng nhập, giỏ hàng gắn với token tài khoản, hiển thị "Welcome <username>".

## Tính năng chính (theo folder trong Postman collection)

| Folder Postman | Feature doc | Nội dung |
|---|---|---|
| HomePage | `features/catalog` | Danh sách sản phẩm (`/entries`), lọc theo danh mục Phones/Laptops/Monitors (`/bycat`), phân trang Next/Previous (`/pagination`), chi tiết sản phẩm (`/view`) |
| About Page | `features/catalog` | Modal "About us" phát video HLS (không có API riêng) |
| Contact Page | `features/catalog` | Modal Contact — chỉ xử lý phía client (alert "Thanks for the message!!"), **không gọi API** |
| Cart Page | `features/cart` | Thêm vào giỏ (`/addtocart`), xem giỏ (`/viewcart`), xoá item (`/deleteitem`), Place Order → xoá giỏ (`/deletecart`) |
| Signup Page | `features/auth/signup` | Đăng ký (`/signup`) |
| Login Page + HomePage/Login | `features/auth/login` | Đăng nhập (`/login`), kiểm tra token (`/check`) |
| Logout Page | `features/auth/login` | Logout — chỉ xoá cookie `tokenp_` phía client, không có API |

## Đặc điểm cần lưu ý cho kiểm thử
- Mọi endpoint dùng `POST` + JSON (trừ `GET /entries`).
- **Lỗi nghiệp vụ trả HTTP 200** kèm `{"errorMessage": "..."}` — không dùng 4xx; assert phải dựa vào body.
- Môi trường public, dữ liệu dùng chung → tránh test tạo rác lớn; user đăng ký không xoá được qua API.
