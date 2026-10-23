# Chiến lược Test: profile/subscription-package (Gói ưu đãi)

> Ngày tạo: 2026-10-22
> Nguồn TC: `automation_test/docs/features/profile/subscription-package/test-cases.md`
> Platform: android
> Tool: Playwright TypeScript (nguồn: user chọn) — dùng `/autotest-pw-android` (Playwright `_android` điều khiển màn hình native qua ADB — cầu nối dòng lệnh tới điện thoại; hỗ trợ Android của Playwright đang ở mức **thử nghiệm**)
> Tổng số TC đã phân tích: 24 (20 TC ban đầu + TC-021→024 bổ sung sau khi verify gap)
> Cập nhật lần cuối: 2026-10-22 (đợt 2 — thêm TC-021→024 sau khi verify gap trên máy thật)

> **Cách trả lời 6 câu hỏi đầu vào:** user chọn tool (câu 3) và yêu cầu khám phá thiết bị đang cắm vào máy để trả lời phần còn lại. Khám phá thực hiện ngày 2026-10-22 trên Samsung SM-A725F / Android 14, app `com.bplus.vtpay` **8.8.62-DEBUG** (bản debug — bản build cho nhà phát triển, không phải bản tải từ store), chỉ thao tác đọc/xem. Những câu thiết bị không trả lời được (team, deadline, kỳ vọng của khách hàng) để `[TBD]`.

---

## Tổng quan dự án & Kiến thức nghiệp vụ cần nắm
> Đọc phần này trước khi viết automation — đây là kiến thức tối thiểu để hiểu đúng TC và không viết sai luồng.

### Dự án / Sản phẩm
ViettelMoney (package `com.bplus.vtpay`) là ví điện tử của Viettel cho khách hàng cá nhân: chuyển tiền, nạp tiền điện thoại, mua data, thanh toán cước. Nguồn tiền chính là **Tiền di động**. Phạm vi automation hiện tại chỉ gồm app Android.

### Chức năng đang test: Gói ưu đãi
"Gói ưu đãi" là gói thành viên mà mỗi tài khoản dùng đúng 1 gói tại một thời điểm: **Gói Standard** miễn phí (mặc định) hoặc các gói trả phí chu kỳ 30 ngày (Start, Smart, Super, Plus, Premium, và 2 gói riêng cho CBNV — cán bộ nhân viên — Viettel). Mỗi gói cho mức % ưu đãi khác nhau khi nạp ĐT, mua data, thanh toán cước, chuyển tiền.

Phạm vi test là **xem**: gói nào đang dùng, trạng thái của nó, danh sách gói, chi tiết gói. Điều kiện trước: tài khoản test (`APP_USERNAME`) đã đăng nhập và đang dùng Gói Standard. Sau khi chạy test, dữ liệu tài khoản **không thay đổi** vì không bấm đăng ký gói nào.

### Luồng nghiệp vụ tổng quan
1. **Khách hàng** mở app. **Hệ thống** hiện màn loading, sau đó vào màn đăng nhập. Khi phiên đăng nhập còn hiệu lực, app có thể vào thẳng bước mật khẩu hoặc vào thẳng Home.
2. **Khách hàng** nhập số điện thoại, bấm `Tiếp tục`, rồi nhập mật khẩu 6 số. Khi nhập đủ 6 số, **hệ thống** tự đăng nhập mà không cần bấm nút, và đưa khách hàng vào Home (`Chào <Tên>`). Bước này không có OTP với máy đã từng đăng nhập.
3. **Khách hàng** bấm tab `Cá nhân` ở thanh dưới. **Hệ thống** mở màn Cá nhân, có mục `Gói ưu đãi` (badge `Mới`) ở ngay đầu danh sách.
4. **Khách hàng** bấm `Gói ưu đãi`. **Hệ thống** tải danh sách gói từ backend (máy chủ) và hiển thị trong khoảng 4 giây đo được. Không có SLA cố định trong tài liệu, và không có thông báo nào gửi cho khách hàng. Gói đang dùng nằm ở thẻ đầu tiên, có chấm xanh + `Đang sử dụng`.
5. **Khách hàng** có thể xem chi tiết theo 2 cách, và 2 cách mở ra 2 kiểu màn khác nhau:
   - Bấm vào thẻ gói đang dùng → **trang chi tiết riêng**, có nút `←`.
   - Bấm `Xem chi tiết` ở thẻ gói chưa dùng → **bottom sheet** (khung trượt từ dưới lên, chồng lên danh sách).
6. **Khách hàng** bấm `?` → **hệ thống** mở `Trung tâm hỗ trợ` với các câu hỏi thường gặp về Gói ưu đãi.
7. **Khách hàng** bấm Back để quay lại: chi tiết → danh sách → Cá nhân → Home.

Các nút `Đăng ký` / `Đăng ký gói ưu đãi` sẽ **trừ tiền thật** và tự động gia hạn, nên **không bao giờ bấm** khi chạy test.

### Kiến thức nghiệp vụ quan trọng (automation cần nắm)
- **BR-P1:** chỉ có **đúng 1** phần tử `tv_status` (= "Đang sử dụng") trong cả danh sách. Phải cuộn hết danh sách mới đếm đủ. Đã xác nhận trên máy: 8 gói, chỉ 1 `tv_status`.
- **BR-P2 / BR-P5:** gói đang dùng nằm ở thẻ đầu tiên, và với tài khoản test đó là `Gói Standard`, giá `Miễn phí`, không có chu kỳ.
- **BR-P3 / BR-P4:** thẻ gói đang dùng không có nút `Đăng ký` / `Xem chi tiết`. Mỗi thẻ gói chưa dùng có giá dạng `<số>đ` + `/30 ngày` và có 2 nút đó.
- **Giá và quyền lợi do backend cấu hình**, có thể đổi theo thời kỳ. Chỉ assert cứng (kiểm tra khớp chính xác) **gói đang dùng + trạng thái**. Giá các gói khác chỉ assert theo định dạng.
- **Bẫy locator** (locator = cách test tìm phần tử trên màn hình):
  - Màn Gói ưu đãi là fragment (màn con) chồng lên màn Cá nhân, nên cây UI vẫn còn text `Gói ưu đãi` của menu bên dưới. Nhận diện màn bằng `tvEndow`/`listSubPackage`, **không** bằng text.
  - ⚠️ resource-id (mã định danh phần tử) `btn_detail` vừa là nút `Xem chi tiết` trên thẻ, **vừa là nút `Đăng ký gói ưu đãi`** trong bottom sheet. Locator phải giới hạn theo thẻ chứa nó, nếu không có nguy cơ bấm nhầm nút đăng ký (trừ tiền thật).
  - Text quyền lợi bị tách thành nhiều node (phần tử con), nên phải assert theo từng mảnh như `2%`, `5%`.
- **Trang chi tiết Gói Standard không đọc được cây UI** (`uiautomator dump` báo `could not get idle state` — màn hình không bao giờ "đứng yên"). Bottom sheet của gói trả phí thì đọc được: `tvSubName`, `tvPrice`, `txtCycleName`, `tvExpiredTime`, nội dung nằm trong WebView (trang web nhúng trong app).
- **Gói trả phí tự động gia hạn**. Nguồn gia hạn gồm ViettelPay, Tiền di động, ngân hàng liên kết. Huỷ gia hạn tại mục `Quản lý gói`. Thông tin này đọc từ Ghi chú trong app.

### Phụ thuộc & tài liệu liên quan
- `automation_test/docs/product-overview.md`: mô tả sản phẩm và ràng buộc "chỉ test luồng đọc/xem trên tài khoản thật".
- `automation_test/docs/features/profile/subscription-package/business-scenario.md` và `detail-design.md`: luồng, business rule, locator. Đã cập nhật ngày 2026-10-22 theo kết quả khám phá thiết bị.
- `automation_test/docs/features/profile/subscription-package/doc-gaps.md`: 7 điểm tài liệu thiếu (AMB-01→07) và bảng "Kết quả verify trên thiết bị thật".
- `automation_test/docs/features/auth/login/business-scenario.md`: feature **chạy trước**. Mọi TC đều cần đăng nhập. TC-018 đi trọn luồng đăng nhập.
- `automation_test/docs/global-data.md` đang **trống** (chưa có URL Appium, appPackage...). Cần điền trước khi scaffold project.

---

## ⚠️ ĐIỂM CHẶN (BLOCKERS)
> Phải giải quyết từng điểm chặn trước khi viết bất kỳ script test nào.

- [x] **Tình trạng test thủ công:** đã khám phá thủ công trên thiết bị thật ngày 2026-10-22. Luồng xem gói ổn định và khớp tài liệu. Có 3 điểm lệch tài liệu, đã sửa vào domain docs: id họ tên `tvUsername`, `Xem chi tiết` có id `btn_detail`, chi tiết gói chưa dùng là bottom sheet. Bản trên máy là **8.8.62-DEBUG**, không phải bản store.
- [ ] **Trang chi tiết Gói Standard không đọc được cây UI** — 8 TC: TC-010, TC-013, TC-014, TC-015, TC-016, TC-017, TC-020, TC-024. Chưa xác nhận Playwright `_android` có tìm được phần tử ở trang này không, vì nó cũng dùng UiAutomator. Cần làm một spike (thử nghiệm kỹ thuật ngắn) ~0,5 ngày: thử `waitForIdle` (chờ màn hình đứng yên) = 0, chuyển sang context (ngữ cảnh) WebView, hoặc dùng phương án dự phòng là assert bằng ảnh chụp.
- [ ] **Hỗ trợ Android của Playwright đang ở mức thử nghiệm** — cả 24 TC. Rủi ro gặp lỗi framework chưa có cách xử lý. Đề xuất làm spike trên TC-018 trước khi viết hàng loạt.
- [ ] **Nguy cơ bấm nhầm nút trừ tiền** — liên quan TC-009, TC-011 và mọi TC đi qua bottom sheet. Do id `btn_detail` bị trùng (xem mục Kiến thức nghiệp vụ quan trọng), cần thống nhất một quy tắc code: cấm dùng locator `btn_detail`, `btn_register_now` mà không giới hạn theo thẻ. Nên có review checklist riêng cho điểm này.
- [ ] **Môi trường backend chưa xác định**: bản app là DEBUG nhưng đang hiển thị dữ liệu tài khoản thật. Chưa rõ app trỏ tới production hay staging. Ảnh hưởng tới quyết định có được mở rộng sang luồng đăng ký gói hay không (BR-P6, Q-P6).
- [ ] **`global-data.md` trống**: chưa có thông tin thiết bị/appPackage/Appium/ADB cho framework.
- [ ] **Ước tính implementation:** ~13–19 ngày công, tính theo công thức `24 TC ÷ (1,7–2,4) script/ngày × 1.3 (buffer)`. Khoảng này phụ thuộc trình độ team, đang `[TBD]` (xem mục Ước tính Implementation).

---

## Câu hỏi cần làm rõ
> Mang danh sách này vào buổi họp với khách hàng/BA/dev.

### Dữ liệu test & cơ chế reset
- [ ] Ai chuẩn bị và giữ tài khoản test? Tài khoản hiện tại là tài khoản thật đang dùng Gói Standard.
- [ ] Các TC **không tạo bản ghi nào**: không đăng ký gói, không phát sinh giao dịch, nên **không cần cơ chế reset dữ liệu**. Điều kiện duy nhất là tài khoản phải luôn ở Gói Standard. Nếu ai đó lỡ đăng ký gói trả phí trên tài khoản này, toàn bộ TC assert "Gói Standard đang sử dụng" sẽ fail cho tới khi gói hết hạn hoặc bị huỷ gia hạn. Ai chịu trách nhiệm giữ nguyên trạng thái này?
- [ ] App trên máy test (8.8.62-DEBUG) đang trỏ backend production hay staging?

### Auth & Token
- [ ] Mỗi lần chạy có đăng nhập lại từ đầu không, hay dùng phiên còn hiệu lực? Phiên hết hạn sau bao lâu? Tài liệu login ghi "chưa verify".
- [ ] Có được đăng nhập cùng tài khoản trên nhiều máy cùng lúc không? Câu trả lời quyết định có chạy song song được không.

### Cấu trúc TC
- [ ] Bộ TC chỉ có **1 TC negative** (TC kiểm tra trường hợp lỗi): TC-023 mất mạng, làm được bằng cách tắt/bật mạng trên máy qua adb. Trường hợp backend trả về danh sách rỗng hoặc lỗi máy chủ thì không giả lập được trên tài khoản thật. Có môi trường hoặc tài khoản nào khác để test các trường hợp này không?
- [ ] Câu hỏi mở còn lại sau khi đọc FAQ trong app: Q-P4 (điều kiện xem/đăng ký gói CBNV), Q-P5 (bấm `Đăng ký gói ưu đãi` trên gói đang dùng — FAQ nói đăng ký nhầm không được hoàn tiền nên không thử trên tài khoản thật), Q-P6 (môi trường UAT), hành vi khi không đủ số dư lúc đăng ký.
- [ ] Các câu hỏi mở còn lại (Q-P3..Q-P6 trong business-scenario.md: đổi gói giữa chu kỳ, điều kiện gói CBNV, hành vi nút `Đăng ký gói ưu đãi`, môi trường UAT) có cần trả lời để mở rộng phạm vi không?

### Môi trường & thiết bị
- [ ] Chỉ Android hay cả iOS? Lưu ý Playwright **không hỗ trợ iOS**.
- [ ] Chạy trên 1 máy thật cắm vào máy tính (hiện tại là SM-A725F), hay có device farm (kho thiết bị chạy test từ xa)?
- [ ] Khách hàng yêu cầu bao nhiêu môi trường? `[TBD]`
- [ ] Bản DEBUG có cờ test nào khác bản store không?

### Timeline & báo cáo
- [ ] Deadline? `[TBD]`
- [ ] Team mấy người, level nào? `[TBD]`
- [ ] Kỳ vọng thời gian chạy full suite? `[TBD]`
- [ ] Mục tiêu % coverage automation? Format báo cáo, ai nhận?

---

## Khả năng tự động hóa
> Nhóm theo Category.

| Category | Số TC | Khả năng | Lý do |
|---|---|---|---|
| Happy Path | 9 | ✅ 6 Auto · ⚠️ 3 Cần làm rõ | TC-002, TC-011, TC-012, TC-018, TC-021, TC-022 tự động hoá được: màn native có resource-id, đã đo trên máy. TC-010, TC-017, TC-020 phải đi qua trang chi tiết Gói Standard, nơi không đọc được cây UI. |
| Business Rule | 6 | ✅ 5 Auto · ⚠️ 1 Cần làm rõ | TC-004, TC-005, TC-007, TC-008, TC-009 dùng id ổn định. TC-014 nằm trên trang chi tiết Gói Standard. |
| UI State | 6 | ✅ 3 Auto · ⚠️ 3 Cần làm rõ | TC-001, TC-003, TC-006 tự động hoá được. TC-013, TC-015, TC-024 nằm trên trang chi tiết Gói Standard. |
| Negative | 1 | ✅ 1 Auto | TC-023: tắt/bật mạng qua `adb shell svc wifi/data`, popup lỗi có resource-id (`title`, `content`, `name`). Teardown (bước dọn dẹp sau test) phải luôn bật lại mạng. |
| Edge Case | 2 | ✅ 1 Auto · ⚠️ 1 Cần làm rõ | TC-019 tự động hoá được. TC-016 nằm trên trang chi tiết Gói Standard. |

---

## Quan sát cấu trúc

### Nhóm chạy nối tiếp (Sequential Flow)
Không phát hiện. Mỗi TC có điều kiện **D (Trường hợp)** tự đủ và không dùng kết quả của TC khác.

Một số TC có các bước đầu giống nhau (đăng nhập → Cá nhân → Gói ưu đãi). Phần này nên đưa vào một fixture dùng chung (bước chuẩn bị tự động chạy trước mỗi test), không gộp TC.

### Cơ hội gộp data-driven
Không có nhóm ≥5 luồng cùng cấu trúc. TC-009 (7 gói chưa dùng) và TC-014 (5 ưu đãi) **đã ở dạng data-driven** (chạy cùng một kịch bản với nhiều bộ dữ liệu, dữ liệu nằm trong bảng **E (Data test)**). Khi automate chỉ cần lặp qua bảng đó, không phải quyết định gộp thêm.

**Tổng kết:** 24 TC → **24 test** (Playwright `test()`), không gộp.

---

## Phạm vi tự động hóa

| | Số lượng | % |
|---|---|---|
| ✅ Auto | 16 | 67% |
| ⚠️ Cần làm rõ | 8 | 33% |
| 🔴 Skip | 0 | 0% |
| **Tổng** | **24** | **100%** |

**Chi tiết ⚠️:** cả 8 TC (TC-010, TC-013, TC-014, TC-015, TC-016, TC-017, TC-020, TC-024) bị chặn bởi cùng một nguyên nhân: trang chi tiết Gói Standard không đọc được cây UI. Nếu spike thành công, cả 8 TC chuyển sang ✅. Nếu không, phương án dự phòng là assert bằng ảnh chụp màn hình so với ảnh mẫu, chấp nhận rủi ro bảo trì cao hơn.

**Phần vẫn cần test thủ công sau khi automation hoàn tất:**
- Luồng đăng ký, đổi gói, huỷ gia hạn: ngoài phạm vi theo BR-P6, vì trừ tiền thật.
- Bấm nút `Đăng ký gói ưu đãi` (AMB-04) — không thử trên tài khoản thật vì đăng ký nhầm không được hoàn tiền.
- Trạng thái loading và danh sách rỗng do backend trả 0 gói (lỗi mất mạng đã có TC-023 tự động).
- Kiểm tra giao diện (màu chấm xanh, layout) và exploratory testing (thử tự do ngoài kịch bản).

---

## Giai đoạn ưu tiên

### P1 — Happy Path + rule rủi ro cao
> Chạy mỗi PR (yêu cầu gộp code) hoặc khi merge vào main.
- TC-018: Luồng Đăng nhập → Cá nhân → Gói ưu đãi hiển thị Gói Standard đang sử dụng
- TC-002: Bấm mục Gói ưu đãi mở màn Gói ưu đãi
- TC-004: Gói đang sử dụng nằm ở thẻ đầu tiên *(HIGH/CRITICAL)*
- TC-007: Thẻ gói đang sử dụng không có nút Đăng ký và Xem chi tiết *(HIGH/CRITICAL)*
- TC-008: Toàn bộ danh sách chỉ có duy nhất 1 gói Đang sử dụng *(HIGH/CRITICAL)*
- TC-010: Bấm thẻ Gói Standard mở trang chi tiết gói
- TC-011: Bấm Xem chi tiết ở thẻ Gói Start mở chi tiết Gói Start
- TC-012: Bấm Back ở màn Gói ưu đãi quay về màn Cá nhân
- TC-017: Bấm Back ở trang chi tiết quay về danh sách, giữ nguyên trạng thái
- TC-020: Chuỗi Back từ trang chi tiết gói quay về Home
- TC-022: Bấm vào thân thẻ Gói Start mở bottom sheet chi tiết Gói Start

TC-004, TC-007 và TC-008 không thuộc category Happy Path, nhưng được xếp vào P1 vì rủi ro HIGH/CRITICAL: sai gói đang dùng là lỗi nghiêm trọng nhất của feature này.

### P2 — Edge Case / Tiêu cực
> Chạy nightly (chạy tự động hằng đêm).
- TC-016: Nút Đăng ký gói ưu đãi vẫn hiển thị trên trang chi tiết của gói đang sử dụng
- TC-019: Trạng thái gói đang sử dụng không đổi khi vào lại màn Gói ưu đãi lần thứ hai
- TC-023: Mất mạng khi mở màn Gói ưu đãi hiển thị popup lỗi và danh sách trống

### P3 — Còn lại
> Chạy weekly, theo sprint hoặc trước release.
- TC-001: Hiển thị mục Gói ưu đãi trên màn Cá nhân (anchor — TC đầu tiên của màn, kiểm tra bố cục tổng quan)
- TC-003: Hiển thị màn Gói ưu đãi (anchor)
- TC-005: Thẻ Gói Standard hiển thị giá Miễn phí và không có chu kỳ
- TC-006: Thẻ Gói Standard hiển thị 2 dòng quyền lợi tóm tắt
- TC-009: Thẻ gói chưa dùng có giá dạng số tiền, chu kỳ /30 ngày, nút Đăng ký và Xem chi tiết
- TC-013: Hiển thị trang chi tiết Gói Standard (anchor)
- TC-014: Mục Ưu đãi viễn thông của Gói Standard hiển thị đủ 5 ưu đãi
- TC-015: Mục Ghi chú hiển thị nội dung chính sách chung và link Tại đây
- TC-021: Bấm nút Trợ giúp mở Trung tâm hỗ trợ với nhóm câu hỏi Gói ưu đãi
- TC-024: Bấm link Tại đây mở trình duyệt tới trang viettelmoney.vn

---

## Ước tính Implementation

**Số TC Auto cần implement:** 24 (16 ✅ + 8 ⚠️ phụ thuộc spike)
**Sau khi gộp data-driven:** 24 test (không gộp)

**Velocity gốc:** 4 script/ngày/người (giữa khoảng 3–5)
**Velocity điều chỉnh:**
- Framework: làm mới → ×0,6. Repo chưa có `playwright.config.ts` hay code automation nào.
- Team level: `[TBD]`. Tính theo khoảng senior ×1,0 đến junior ×0,7.
- Environment: ×1. Hiện mới thấy 1 môi trường: 1 máy thật với bản DEBUG.

→ 4 × 0,6 × (0,7–1,0) = **1,7–2,4 script/ngày**

**Cơ bản:** 24 ÷ (1,7–2,4) = ~10,0–14,1 ngày

**Buffer +30%:**
- Playwright Android còn thử nghiệm, dễ gặp lỗi framework chưa có cách xử lý.
- Trang chi tiết Gói Standard không đọc được cây UI, cần spike.
- Phụ thuộc 1 máy thật và mạng; tài khoản thật có thể bị ai đó đổi gói.
- Thay đổi yêu cầu giữa chừng (7 điểm AMB, 3 điểm còn mở).

**Tổng ước tính:** ~13,0–18,4 ngày → **~13–19 ngày công** (chia theo số người khi biết quy mô team, đang `[TBD]`). Con số này đã tính scaffold framework (dựng khung project) nhờ hệ số ×0,6. Cộng thêm ~0,5 ngày spike cho trang chi tiết.

**Deadline:** `[TBD — chờ họp với khách hàng]`

---

## Ước tính thời gian chạy

**Platform:** android
**Thời gian trung bình/TC:** ~45–90s. Ước tính từ số đo thật: một vòng điều hướng read-only đầy đủ mất ~23s qua adb (Home → Cá nhân 3,1s; → Gói ưu đãi 4,2s; → chi tiết ~3s; Back 2,7s + 2,6s + 3,4s). Cộng thêm thời gian khởi động hoặc đăng nhập lại app (~15–30s) và overhead (chi phí phụ) của Playwright. TC-008, TC-009 dài hơn vì phải cuộn hết 8 gói, ~10–15s thêm.

| Suite | Số TC | Chạy nối tiếp (1 máy) | Chạy parallel (4 máy / 4 worker — luồng chạy song song, mỗi máy 1 worker) |
|---|---|---|---|
| P1 | 11 | ~9–17 phút | ~3–5 phút |
| P1 + P2 | 14 | ~11–21 phút | ~3–6 phút |
| Full suite | 24 | ~18–36 phút | ~5–9 phút |

**Kỳ vọng của khách hàng:** `[TBD — chờ họp với khách hàng]`
> Hiện chỉ có **1 máy**, nên thực tế chỉ chạy nối tiếp được. Chạy song song cần thêm máy, và còn phụ thuộc câu hỏi "có đăng nhập cùng tài khoản trên nhiều máy được không" (xem mục Câu hỏi cần làm rõ > Auth & Token). Chạy lại `/autotest-strategy subscription-package` sau buổi họp để hoàn thiện mục này.

---

## Lịch CI/CD

| Suite | Trigger |
|---|---|
| P1 (Happy Path + rule rủi ro cao) | Mỗi PR / merge vào main |
| P2 (Edge Case / Tiêu cực) | Build nightly |
| P3 (Còn lại) | Weekly / theo sprint / trước release / trước deploy |

Vì máy test cắm trực tiếp vào máy tính, CI runner (máy chạy CI) phải là chính máy có cắm điện thoại, hoặc phải dùng device farm.

---

## Dữ liệu test & Môi trường

- **Platform:** android — Samsung SM-A725F, Android 14, `com.bplus.vtpay 8.8.62-DEBUG`
- **Số môi trường:** 1 quan sát được (máy thật + bản DEBUG, backend chưa xác định). Yêu cầu của khách hàng `[TBD]`.
- **Số tài khoản test cần:** 1. Lý do: không có TC nào thay đổi dữ liệu (xem mục Quan sát cấu trúc: 0 nhóm nối tiếp, 0 TC tạo bản ghi), nên 24 TC dùng chung được 1 tài khoản đang ở Gói Standard. Nếu sau này chạy song song trên nhiều máy mà hệ thống không cho đăng nhập đồng thời, cần thêm 1 tài khoản Gói Standard cho mỗi máy.
- **Credential:** `APP_USERNAME`, `APP_PASSWORD` trong `automation_test/.env` (user tự điền, không commit).
- **Cơ chế reset dữ liệu:** không cần, vì không TC nào tạo bản ghi.
- **Nhóm config batch:** N/A
- **Độ ổn định staging:** `[TBD — chờ họp với khách hàng]`

---

## Rủi ro Maintenance

| Nhóm / TC | Rủi ro | Lý do |
|---|---|---|
| TC-010, TC-013, TC-014, TC-015, TC-016, TC-017, TC-020, TC-024 (trang chi tiết Gói Standard) | 🔴 Cao |
| TC-023 (mất mạng) | 🟠 Trung bình | Tắt/bật mạng trên máy thật; nếu teardown không bật lại mạng thì các TC chạy sau đều fail | Không có resource-id, không đọc được cây UI, có thể phải assert bằng ảnh hoặc WebView |
| TC-018 (đăng nhập end-to-end) | 🔴 Cao | Phụ thuộc trạng thái phiên (app có thể vào thẳng bước mật khẩu hoặc Home), màn loading, mạng |
| TC-006, TC-009, TC-011, TC-014 (giá, quyền lợi, thời hạn) | 🟠 Trung bình | Nội dung do backend cấu hình và đổi theo thời kỳ. Ngày thời hạn trong TC-011 đổi theo ngày chạy |
| TC-001, TC-002, TC-003, TC-004, TC-005, TC-007, TC-008, TC-012, TC-019 | 🟡 Thấp | Màn native với resource-id ổn định (`tvEndow`, `package_info`, `tv_status`...) |

---

## Đề xuất Demo

TC đề xuất cho demo khách hàng (< 2 phút mỗi TC):
1. TC-018: Luồng Đăng nhập → Cá nhân → Gói ưu đãi hiển thị Gói Standard đang sử dụng — đi trọn hành trình người dùng, thấy ngay giá trị nghiệp vụ.
2. TC-008: Chỉ có duy nhất 1 gói Đang sử dụng — test tự cuộn hết 8 gói và đếm, việc mà làm tay rất dễ bỏ sót.
3. TC-011: Bấm Xem chi tiết ở Gói Start — cho thấy test mở thông tin gói trả phí an toàn mà không bấm nút đăng ký.

> Tip: highlight đúng element đang tương tác ở mỗi step để khách hàng dễ theo dõi.

---

## Template báo cáo tiến độ

```
📊 Cập nhật Sprint — Gói ưu đãi — <date>

✅ Done:        X / 24 TC  (<category đã hoàn thành>)
🔄 Đang làm:    X TC      (<category hiện tại>)
⏭️ Đã Skip:     0 TC
❓ Đang chờ:    X TC      (chặn bởi: trang chi tiết Gói Standard không đọc được cây UI)

📅 ETA full suite: <date>
Milestone kế tiếp: <việc hoàn thành ở sprint sau>
```
