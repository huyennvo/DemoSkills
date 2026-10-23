# Product Overview — ViettelMoney (Android)

ViettelMoney (package `com.bplus.vtpay`) là ví điện tử / ứng dụng tài chính của Viettel (Viettel Digital Financial Services) cho khách hàng cá nhân. App dùng để chuyển tiền, nạp tiền điện thoại, mua data, thanh toán cước viễn thông và các dịch vụ khác. Nguồn tiền chính là **Tiền di động**.

**Gói ưu đãi** là gói thành viên mà người dùng đăng ký trên app:
- Gói miễn phí (vd **Gói Standard**, mặc định).
- Gói trả phí theo chu kỳ **30 ngày** (Start, Smart, Super, Plus, Premium).
- Gói dành riêng cho CBNV Viettel.

Mỗi gói có mức ưu đãi % khác nhau khi nạp tiền điện thoại, mua data, thanh toán cước, và ưu đãi miễn phí chuyển tiền. Mỗi tài khoản dùng đúng 1 gói tại một thời điểm.

## Đối tượng người dùng
- Khách hàng cá nhân có tài khoản ViettelMoney.
- CBNV Viettel (có gói riêng giá thấp hơn).

## Tính năng chính (phạm vi automation hiện tại)
| Module | Feature | Docs |
|---|---|---|
| Xác thực | Đăng nhập / Đăng xuất | `features/auth/login/` |
| Cá nhân | Gói ưu đãi: xem danh sách gói, gói đang dùng, chi tiết gói | `features/profile/subscription-package/` |

## Ràng buộc kiểm thử
- Test chạy trên **app production** với **tài khoản thật**, nên chỉ automation các luồng **đọc/xem**. Các luồng phát sinh giao dịch (đăng ký gói trả phí, chuyển tiền, thanh toán) nằm ngoài phạm vi.
- Danh sách gói, giá và quyền lợi do backend cấu hình, có thể đổi theo thời kỳ. Test chỉ assert cứng gói đang dùng và trạng thái của nó.
