# Detail Design — Cá nhân → Gói ưu đãi (ViettelMoney Android)

> Locator lấy từ `uiautomator dump` trên bản store `com.bplus.vtpay` 8.8.62 (Samsung A72, 1080×2400).
> resource-id đầy đủ = `com.bplus.vtpay:id/<name>`. Login: xem `features/auth/login/detail-design.md`.

## Home — tab Cá nhân (`com.bplus.vtpay.activity.MainActivity`)
| Element | resource-id | Class | Ghi chú |
|---|---|---|---|
| Tab Cá nhân (container) | `navItemAccount` | LinearLayout | clickable, **dùng để tap** |
| Nhãn tab | `title5` | TextView | `Cá nhân` (không clickable) |

## Màn Cá nhân (`com.bplus.vtpay.activity.MainFragmentActivity`)
| Element | resource-id | Text / ghi chú |
|---|---|---|
| Back | `imvBack` | |
| Họ tên | `tvUsername` | Hiển thị **họ tên**. *(Cập nhật 2026-10-22 trên bản 8.8.62-DEBUG: id là `tvUsername`; bản khảo sát trước ghi `phoneNumber`. Home header `Chào <Tên>` cũng dùng id `tvUsername`.)* |
| Hạng hội viên | `buttonAction` | vd `Hội viên Tiềm năng` |
| Danh sách menu | `menuList` (RecyclerView) | |
| Mục Gói ưu đãi (container) | `container` | clickable, chứa `tvTitle` = `Gói ưu đãi`. Id `container` lặp ở mọi mục → tap theo XPath: `//*[@resource-id='com.bplus.vtpay:id/container'][.//*[@text='Gói ưu đãi']]` |
| Badge | `badgeNew` | `Mới` |
| Mô tả | `tvDescription` | `Gia tăng ưu đãi, miễn phí chuyển tiền` |

## Màn Gói ưu đãi (fragment trong `MainFragmentActivity`)
> ⚠️ Màn này là fragment chồng lên màn Cá nhân, và **UI dump vẫn chứa node của màn Cá nhân bên dưới** (vd `tvTitle`='Gói ưu đãi' của menu). Để nhận diện đã vào màn Gói ưu đãi, dùng `tvEndow` (tiêu đề trang) hoặc `listSubPackage`, **không dùng** `//*[@text='Gói ưu đãi']`.

| Element | resource-id | Class | Text / ghi chú |
|---|---|---|---|
| Tiêu đề trang | `tvEndow` | TextView | `Gói ưu đãi` |
| Trợ giúp (?) | `btnHelp` | ImageButton | |
| Danh sách gói | `listSubPackage` | RecyclerView | Cuộn dọc |
| Thẻ gói | `package_info` | LinearLayout | clickable, lặp mỗi gói |
| Tên gói | `tvSubName` | TextView | vd `Gói Standard` |
| **Trạng thái** | `tv_status` | TextView | `Đang sử dụng`. **Chỉ có ở thẻ gói đang dùng** |
| Giá | `tvPrice` | TextView | `Miễn phí` / `15.000đ`... |
| Chu kỳ | `txtCycleName` | TextView | `/30 ngày` (không có ở gói miễn phí) |
| Khung trạng thái | `btn_status_active` | | chứa `img_status` (chấm xanh) + `tv_status` — chỉ có ở thẻ gói đang dùng |
| Ảnh gói | `img_sub` | ImageView | chỉ có ở gói chưa dùng |
| Nút Đăng ký | `btn_register_now` | TextView | `Đăng ký`, nằm trong `rlt_register`. Chỉ có ở gói chưa dùng. **Không tap khi chạy test** |
| Xem chi tiết | `btn_detail` | | chứa TextView `Xem chi tiết` (không id). *(Cập nhật 2026-10-22: bản trước ghi "không id".)* ⚠️ Id `btn_detail` **trùng** với nút `Đăng ký gói ưu đãi` trong bottom sheet chi tiết — luôn scope locator theo container, không tap `btn_detail` khi bottom sheet đang mở |
| Dòng quyền lợi | *(không id)* | TextView/View | Text bị tách nhiều node (`✔︎`, ` Ưu đãi `, `2%`...) → không assert theo nguyên câu |

### Locator assert gói đang dùng
```
# Thẻ gói đang dùng (thẻ có tv_status)
//*[@resource-id='com.bplus.vtpay:id/package_info'][.//*[@resource-id='com.bplus.vtpay:id/tv_status']]

# Tên gói của thẻ đó → expect 'Gói Standard'
//*[@resource-id='com.bplus.vtpay:id/package_info'][.//*[@resource-id='com.bplus.vtpay:id/tv_status']]//*[@resource-id='com.bplus.vtpay:id/tvSubName']

# Trạng thái → expect 'Đang sử dụng'
//*[@resource-id='com.bplus.vtpay:id/tv_status']

# Assert gộp 1 locator
//*[@resource-id='com.bplus.vtpay:id/package_info'][.//*[@text='Gói Standard'] and .//*[@resource-id='com.bplus.vtpay:id/tv_status' and @text='Đang sử dụng']]
```

Toạ độ tham khảo (1080×2400, lúc khảo sát): thẻ Standard `[54,417][1026,1113]`, `tv_status` `[700,461][910,500]`.

## Trang chi tiết gói (tap vào `package_info` của Gói Standard)
| Thành phần | Text |
|---|---|
| Back | `←` |
| Tiêu đề | `Gói Standard` |
| Nhãn giá | `Giá gói` → `Miễn phí` |
| Section | `Ưu đãi viễn thông`, `Ghi chú` |
| Link | `Tại đây` → mở trình duyệt ngoài `https://viettelmoney.vn/` |
| Ảnh minh hoạ + câu | Hộp quà + `Đăng ký gói ưu đãi để nhận thêm ưu đãi hấp dẫn từ Viettel Money` (cuối trang, trên nút) |
| Nút | `Đăng ký gói ưu đãi` (**không tap**) |

> ⚠️ **Không lấy được UI dump ở trang này.** `uiautomator dump` báo `ERROR: could not get idle state` (có thể nội dung là WebView, hoặc có animation chạy liên tục). Vì vậy chưa có resource-id; nội dung trên đọc từ ảnh chụp màn hình. Khi automation:
> - Đặt Appium setting `waitForIdleTimeout` = `0` (AppiumLibrary: `Update Settings    waitForIdleTimeout=0`) trước khi tìm element ở trang này.
> - Nếu là WebView thì cần chuyển context (`Get Contexts` / `Switch To Context`) *(chưa verify)*.

## Chi tiết gói chưa dùng — bottom sheet (bấm `Xem chi tiết`)
> Khảo sát 2026-10-22 trên SM-A725F / Android 14 / `8.8.62-DEBUG`, mở từ thẻ Gói Start. **Không phải trang riêng** như trang chi tiết Gói Standard mà là bottom sheet chồng lên màn Gói ưu đãi. `uiautomator dump` **chạy được** ở sheet này.

| Thành phần | resource-id | Text / ghi chú |
|---|---|---|
| Sheet | `design_bottom_sheet` | FrameLayout, có thanh kéo ở đầu |
| Tên gói | `tvSubName` | `Gói Start` |
| Nhãn giá | `tvRates` | `Giá gói` |
| Giá | `tvPrice` + `txtCycleName` | `15.000đ` + `/30 ngày` |
| Thời hạn | `tvExpiredTime` | `Từ 22/10/2026 đến hết 20/11/2026` (ngày đổi theo ngày xem) |
| Nội dung quyền lợi + Ghi chú | *(WebView, không id)* | Section `Ưu đãi chuyển tiền liên ngân hàng`, `Ưu đãi viễn thông`, `Ưu đãi SMS biến động số dư`, `Ghi chú` — đọc được text qua dump |
| Nút | `btn_detail` | `Đăng ký gói ưu đãi` — **KHÔNG tap** (trùng id với `Xem chi tiết` trên thẻ) |

Đóng sheet: phím Back hệ thống → về màn Gói ưu đãi (giữ nguyên vị trí cuộn).

## Trung tâm hỗ trợ (bấm `btnHelp`)
Mở activity `com.bplus.vtpay.screen.cc_portal.main.MainCCPortalActivity`, tiêu đề `Trung tâm hỗ trợ`, ô tìm kiếm `Nhập từ khóa tìm kiếm` (bàn phím tự bật), nhóm FAQ `Gói ưu đãi` (vd `Gói ưu đãi là sản phẩm như thế nào?`, `Chu kỳ gia hạn của gói ưu đãi là bao lâu? Làm sao để hủy gia hạn tự động?`). Back 2 lần (đóng bàn phím, rồi thoát màn) → về màn Gói ưu đãi.

## Popup lỗi mạng trên màn Gói ưu đãi (khảo sát 2026-10-22, tắt Wi-Fi + dữ liệu)
| Thành phần | resource-id | Text |
|---|---|---|
| Tiêu đề | `title` | `Thông báo` |
| Nội dung | `content` | `Bạn vui lòng đợi vài phút trước khi thử lại nhé.` |
| Nút | `name` (trong `recyclerView`) | `Đóng` |

- Popup hiện sau ~4s kể từ khi bấm mục Gói ưu đãi; phía sau là màn Gói ưu đãi với `tvEndow` + `btnHelp`, **không có `listSubPackage`**.
- Bấm `Đóng` → ở lại màn Gói ưu đãi, danh sách trống, không có nút thử lại. Back → Cá nhân; có mạng lại thì mở lại màn tải bình thường (~3s).
- Trạng thái **loading**: lớp phủ xám + icon app có vòng xoay ở giữa màn; trong lúc này `uiautomator dump` không trả về node danh sách.

## Link `Tại đây` (trang chi tiết Gói Standard)
Mở **trình duyệt ngoài** (Samsung Internet, `com.sec.android.app.sbrowser`) với URL `https://viettelmoney.vn/` — rời khỏi app. Khi automate: assert package/activity đang focus đổi sang trình duyệt (hoặc intent URL), sau đó đưa app lại foreground (bấm Back hoặc `activateApp`), **không** dùng `am start` thẳng vào `MainFragmentActivity` (mở ra màn trắng).

## Bấm thân thẻ gói chưa dùng
Bấm vào vùng tên gói (`tvSubName`) trên thẻ gói chưa dùng → mở **cùng bottom sheet** như bấm `Xem chi tiết` (`design_bottom_sheet`, `tvSubName` = tên gói, `tvExpiredTime`).

## Điều hướng quay lại
- Trang chi tiết → Back → danh sách Gói ưu đãi
- Danh sách Gói ưu đãi → Back → Cá nhân → Back → Home
- Bottom sheet chi tiết gói chưa dùng → Back hệ thống → danh sách Gói ưu đãi
- Trung tâm hỗ trợ → Back (đóng bàn phím) → Back → danh sách Gói ưu đãi

## Thời gian đo trên thiết bị thật (2026-10-22, adb + uiautomator dump, chưa có overhead framework)
| Bước | Thời gian |
|---|---|
| Home → Cá nhân | ~3,1s |
| Cá nhân → Gói ưu đãi (thấy `tv_status`) | ~4,2s |
| Gói ưu đãi → trang chi tiết Standard | ~3s (chờ cố định, không dump được) |
| Chi tiết → Back → Gói ưu đãi | ~2,7s |
| Gói ưu đãi → Back → Cá nhân | ~2,6s |
| Cá nhân → Back → Home | ~3,4s |
| **Tổng 1 vòng** | **~23s** |
