# Detail Design — Đăng nhập (ViettelMoney Android)

> Locator lấy từ `uiautomator dump` trên bản store `com.bplus.vtpay` 8.8.62 (Samsung A72, 1080×2400). Build UAT có cùng id (đã đối chiếu màn mật khẩu).
> resource-id đầy đủ = `<package>:id/<name>` (vd `com.bplus.vtpay:id/inputField`).

## Màn: Loading — `com.bplus.vtpay.activity.CheckNetworkActivity`
| Element | resource-id | Text |
|---|---|---|
| Thông báo loading | `titleLoading` | `Đang tải dữ liệu, vui lòng đợi...` |
| Copyright | `copyRight` | `© Viettel Digital Financial Services` |

→ Chờ `titleLoading` biến mất (timeout gợi ý ≥ 30s) trước khi thao tác.

## Màn: Đăng nhập — `vn.viettelpay.authentication.ui.AuthenticationActivity`
Bước nhập SĐT và bước nhập mật khẩu dùng **chung activity và chung id `inputField`**. Phân biệt 2 bước bằng `hint`, hoặc bằng nút `buttonContinue` / `buttonSignIn`.

### Bước 1 — Nhập số điện thoại
| Element | resource-id | Class | Text / ghi chú |
|---|---|---|---|
| Nút Back | `buttonLeft` | ImageView | |
| Nhãn | `hint` | TextView | `Nhập số điện thoại` |
| Ô SĐT | `inputField` | EditText | Tự format `xxxx-xxx-xxx`. Có thể đã điền sẵn SĐT cũ → **Clear Text trước khi nhập** |
| Nút xoá/clear trong ô | `buttonAction` | ImageView | clickable. **Trùng id** với nhãn nút Tiếp tục bên dưới |
| Nút Tiếp tục (container) | `buttonContinue` | FrameLayout | **Dùng để tap** |
| Nhãn nút | `buttonAction` | TextView | `Tiếp tục` |

### Bước 2 — Nhập mật khẩu
| Element | resource-id | Class | Text / ghi chú |
|---|---|---|---|
| Nút Back | `buttonLeft` | ImageView | |
| Avatar chữ cái | `shortName` | TextView | vd `H` |
| Lời chào | `welcomeTitle` | TextView | `Xin chào, <Họ tên>` |
| Nhãn | `hint` | TextView | `Nhập mật khẩu` |
| Ô mật khẩu | `inputField` | EditText | 6 số. Có `passwordCover` (View) phủ cùng vùng: tap để focus, rồi `Input Text` / `adb shell input text` |
| Lớp phủ | `passwordCover` | View | clickable |
| Quên mật khẩu | `forgotPassword` | TextView | `Quên mật khẩu` |
| Nút Đăng nhập (container) | `buttonSignIn` | FrameLayout | Thường **không cần bấm** vì nhập đủ 6 số là tự submit |
| Nhãn nút | `buttonAction` | TextView | `Đăng nhập` |

> ⚠️ **Không tap tiếp sau khi nhập mật khẩu.** App chuyển sang Home ngay, nên cú tap vào toạ độ nút Đăng nhập (≈ 540,1247) sẽ trúng ô **Gửi tiết kiệm** trên Home (đã gặp lúc khảo sát). Sau khi nhập mật khẩu, chỉ cần `Wait Until Page Contains Element` `tvUsername`.

## Đăng xuất — tab Cá nhân (`com.bplus.vtpay.activity.MainFragmentActivity`)
| Element | resource-id | Text / ghi chú |
|---|---|---|
| Mục Đăng xuất | `container` > `tvTitle` | `Đăng xuất`, nằm cuối danh sách, phải cuộn. Locator: `//*[@text='Đăng xuất']` |
| Dialog tiêu đề | `title` | `Thông báo` |
| Dialog nội dung | `content` | `Bạn có chắc chắn muốn đăng xuất tài khoản?` |
| Bỏ qua | `buttonNegative` | `Bỏ qua` |
| Xác nhận | `buttonPositive` | `Xác nhận` |
| Phiên bản | `tv_title` | `Phiên bản: <version> (<build>)` (cuối trang Cá nhân) |

## Điều kiện kết thúc (assert đăng nhập thành công)
- Activity = `com.bplus.vtpay.activity.MainActivity`
- `tvUsername` hiển thị, text bắt đầu bằng `Chào `

## Data
- `APP_USERNAME` (SĐT, nhập dạng 10 số liền, app tự format), `APP_PASSWORD` (6 số) trong `automation_test/.env`.

## Lưu ý automation
- Ngay sau khi nhập mật khẩu, `uiautomator dump` có thể không trả kết quả vì màn đang chuyển. Nên chờ element ở Home thay vì dump màn login.
