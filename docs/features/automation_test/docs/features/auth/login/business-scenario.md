# Business Scenario — Đăng nhập (ViettelMoney Android)

> Nguồn: khảo sát app bản store `com.bplus.vtpay` 8.8.62 ngày 2026-10-22, đã đi hết luồng Đăng xuất → Nhập SĐT → Nhập mật khẩu → Home. Phần đánh dấu *(chưa verify)* là suy luận, cần xác nhận với BA/PO.

## Luồng nghiệp vụ: Đăng nhập (máy đã từng đăng nhập)
1. Mở app → Splash → `CheckNetworkActivity` ("Đang tải dữ liệu, vui lòng đợi...").
2. App mở `AuthenticationActivity`, gồm 2 bước trong cùng activity:
   1. **Nhập số điện thoại.** Ô nhập tự format dạng `xxxx-xxx-xxx`, đã điền sẵn SĐT của tài khoản gần nhất. Bấm **Tiếp tục**.
   2. **Nhập mật khẩu.** Hiện avatar chữ cái đầu và `Xin chào, <Họ tên>`. Người dùng nhập **mật khẩu 6 chữ số**.
3. **Nhập đủ 6 số là app tự đăng nhập**, không cần bấm nút **Đăng nhập**.
4. Đăng nhập thành công → `MainActivity` (Home), header hiện `Chào <Tên>`.

> Khi mở lại app mà phiên còn hiệu lực thì không phải nhập SĐT. App vào thẳng bước mật khẩu, hoặc vào thẳng Home nếu chưa hết phiên *(chưa verify thời gian hết phiên)*.

## Luồng: Đăng xuất (để đưa app về màn login)
Home → bottom bar **Cá nhân** → cuộn xuống cuối → **Đăng xuất** → dialog `Thông báo` / `Bạn có chắc chắn muốn đăng xuất tài khoản?` → **Xác nhận** → màn Nhập số điện thoại.

## Luồng phụ (thấy trên UI, chưa đi sâu)
- **Quên mật khẩu:** link `Quên mật khẩu` dưới ô mật khẩu.
- **Back (←)** ở bước mật khẩu → quay về bước nhập SĐT *(chưa verify)*.
- **Đăng nhập lần đầu trên máy mới / SĐT khác:** có thể có bước OTP *(chưa khảo sát)*.

## Business rules / điểm cần kiểm thử
| # | Rule | Nguồn |
|---|---|---|
| BR-L1 | Mật khẩu gồm 6 ký tự số | Quan sát |
| BR-L2 | Nhập đủ 6 số mật khẩu thì tự submit đăng nhập | Quan sát |
| BR-L3 | SĐT tự format `xxxx-xxx-xxx`, mặc định điền SĐT gần nhất | Quan sát |
| BR-L4 | Đăng xuất phải xác nhận qua dialog (Bỏ qua / Xác nhận) | Quan sát |
| BR-L5 | Sai mật khẩu → báo lỗi; sai nhiều lần → khoá tạm *(chưa verify số lần)* | Cần BA xác nhận |
| BR-L6 | Bản staging cần mạng nội bộ/VPN mới qua được màn loading | Quan sát |

## Kịch bản kiểm thử gợi ý
- Đăng nhập thành công (SĐT + mật khẩu đúng) → vào Home.
- Mật khẩu sai → hiện thông báo lỗi, vẫn ở màn đăng nhập. **Cẩn thận:** tài khoản test là tài khoản thật, sai nhiều lần có thể bị khoá.
- Đăng xuất → bấm Bỏ qua thì ở lại; bấm Xác nhận thì về màn nhập SĐT.
- Bấm Quên mật khẩu → chuyển sang luồng khôi phục.
