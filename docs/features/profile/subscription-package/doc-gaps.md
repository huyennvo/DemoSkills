# Doc Gaps — profile/subscription-package

> Các quyết định dưới đây do AI tự quyết **tạm thời** theo yêu cầu của user (2026-10-22: "tạm thời bạn tự quyết 6 câu hỏi đó, sau này khi chạy test trên máy thật bạn hãy cập nhật lại tài liệu"). Khi chạy test trên thiết bị thật, cần quan sát hành vi thật rồi cập nhật lại `business-scenario.md` / `detail-design.md` và TC liên quan.

### AMB-01 — 2026-10-22 — Completeness Gap
**Phát hiện tại**: Step A1.5
**Mô tả**: Thẻ gói chưa dùng có nút `Xem chi tiết` (business-scenario.md — bảng "Danh sách gói quan sát được"; detail-design.md — "Màn Gói ưu đãi", `Xem chi tiết` không id) nhưng tài liệu không mô tả bấm vào thì mở màn nào, nội dung gì.
**Quyết định của user**: AI tự quyết tạm — bấm `Xem chi tiết` mở trang chi tiết của đúng gói đó: tiêu đề = tên gói, `Giá gói` = giá hiển thị trên thẻ. Không assert nội dung quyền lợi. Cần verify trên máy thật.
**Ảnh hưởng TC**: TC-011

### AMB-02 — 2026-10-22 — Orphan
**Phát hiện tại**: Step A1.5
**Mô tả**: Nút Trợ giúp `btnHelp` (?) chỉ có trong detail-design.md, business-scenario.md không mô tả hành vi khi bấm.
**Quyết định của user**: AI tự quyết tạm — chỉ assert nút hiển thị trên màn Gói ưu đãi, không viết TC bấm nút.
**Ảnh hưởng TC**: TC-003 (assert hiển thị); TC bấm nút — chưa viết, bỏ qua

### AMB-03 — 2026-10-22 — Completeness Gap
**Phát hiện tại**: Step A1.5
**Mô tả**: Link `Tại đây` trong mục Ghi chú của trang chi tiết gói — tài liệu không nêu link mở đi đâu.
**Quyết định của user**: AI tự quyết tạm — chỉ assert link hiển thị, không viết TC bấm link.
**Ảnh hưởng TC**: TC-015 (assert hiển thị); TC bấm link — chưa viết, bỏ qua

### AMB-04 — 2026-10-22 — Logic Issue
**Phát hiện tại**: Step A1.5
**Mô tả**: Thẻ gói đang dùng trên danh sách **không có** nút `Đăng ký` (BR-P3), nhưng trang chi tiết của chính gói đó **vẫn hiện** nút `Đăng ký gói ưu đãi` (business-scenario.md — "Trang chi tiết Gói Standard"; Q-P5). Chưa rõ là thiết kế hay lỗi, chưa rõ hành vi khi bấm.
**Quyết định của user**: AI tự quyết tạm — viết TC assert nút hiển thị đúng hiện trạng, KHÔNG bấm; chờ BA/PO xác nhận.
**Ảnh hưởng TC**: TC-016

### AMB-05 — 2026-10-22 — Completeness Gap
**Phát hiện tại**: Step A1.5
**Mô tả**: Tài liệu không mô tả trạng thái loading, danh sách rỗng, lỗi mạng/lỗi backend của màn Gói ưu đãi và trang chi tiết gói.
**Quyết định của user**: AI tự quyết tạm — bỏ qua, không viết TC.
**Ảnh hưởng TC**: chưa viết — bỏ qua

### AMB-06 — 2026-10-22 — Completeness Gap
**Phát hiện tại**: Step A1.5
**Mô tả**: Câu hỏi mở Q-P1 → Q-P6 trong business-scenario.md (nguồn trừ tiền khi đăng ký, tự gia hạn/huỷ gia hạn, đổi gói giữa chu kỳ, điều kiện gói CBNV Viettel, hành vi nút `Đăng ký gói ưu đãi`, môi trường UAT/tài khoản test cho luồng trả phí) chưa có thông tin; kèm ràng buộc BR-P6 (không phát sinh giao dịch trên production).
**Quyết định của user**: AI tự quyết tạm — bỏ qua, không viết TC cho các luồng đăng ký/gia hạn/đổi gói/điều kiện CBNV.
**Ảnh hưởng TC**: chưa viết — bỏ qua

### AMB-07 — 2026-10-22 — Completeness Gap
**Phát hiện tại**: Step A2.6 Audit (DOC)
**Mô tả**: detail-design.md ghi `package_info` "clickable, lặp mỗi gói" (mọi thẻ đều bấm được), nhưng business-scenario.md chỉ mô tả "tap thẻ → trang chi tiết" cho Gói Standard; không nói bấm thân thẻ của gói chưa dùng thì đi đâu.
**Quyết định của user**: AI tự quyết tạm (theo uỷ quyền của user cho các điểm tài liệu thiếu) — bỏ qua, chưa viết TC; vùng chạm gần nút `Đăng ký` (BR-P6). Verify trên máy thật.
**Ảnh hưởng TC**: chưa viết — bỏ qua

---

## Kết quả verify trên thiết bị thật — 2026-10-22
> SM-A725F / Android 14 / `com.bplus.vtpay 8.8.62-DEBUG`, tài khoản test đang dùng Gói Standard. Chỉ thao tác đọc/xem, không bấm nút Đăng ký nào.

| AMB | Trạng thái | Quan sát thực tế |
|---|---|---|
| AMB-01 | ✅ Đã verify — **khác một phần** tạm quyết | `Xem chi tiết` (id `btn_detail`) mở **bottom sheet** (không phải trang riêng): tiêu đề = tên gói, `Giá gói` = giá + `/30 ngày` khớp thẻ, **thêm dòng thời hạn** `Từ <hôm nay> đến hết <+29 ngày>`. Đóng bằng Back hệ thống. TC-011 cần cập nhật (đề xuất ở Step 5 của /autotest-strategy). |
| AMB-02 | ✅ Đã verify | `btnHelp` mở `Trung tâm hỗ trợ` (MainCCPortalActivity) với FAQ nhóm `Gói ưu đãi`, bàn phím tự bật; Back 2 lần về danh sách. Có thể viết thêm TC bấm nút (chỉ đọc). |
| AMB-03 | ⏳ Chưa verify | Link `Tại đây` hiển thị đúng (ảnh chụp), chưa bấm. |
| AMB-04 | ◐ Verify một phần | Nút `Đăng ký gói ưu đãi` hiển thị trên trang chi tiết Gói Standard (đúng hiện trạng). Hành vi khi bấm chưa verify — **không bấm** trên tài khoản thật. |
| AMB-05 | ⏳ Chưa verify | Không giả lập được lỗi mạng/rỗng trên tài khoản thật. |
| AMB-06 | ◐ Verify một phần | Ghi chú trong sheet chi tiết gói trả phí trả lời một phần Q-P1/Q-P2: nguồn gia hạn = ViettelPay, Tiền di động, ngân hàng liên kết; gói **tự động gia hạn**; huỷ gia hạn tại `Quản lý gói`. Q-P3..Q-P6 vẫn mở. |
| AMB-07 | ⏳ Chưa verify | Chưa bấm thân thẻ gói chưa dùng. |

**Phát hiện mới (không thuộc AMB nào):**
- Bản cài trên máy test là `8.8.62-DEBUG`, tài liệu ghi bản store.
- Màn Cá nhân: họ tên dùng id `tvUsername` (tài liệu cũ ghi `phoneNumber`) — đã sửa detail-design.md.
- ⚠️ Id `btn_detail` dùng cho **cả** `Xem chi tiết` (trên thẻ) **và** `Đăng ký gói ưu đãi` (trong bottom sheet) — locator phải scope theo container, nếu không có thể bấm nhầm nút đăng ký (trừ tiền thật).
- `uiautomator dump` chạy được ở bottom sheet chi tiết gói trả phí, nhưng vẫn **không** chạy được ở trang chi tiết Gói Standard.

## Kết quả verify đợt 2 — 2026-10-22 (theo yêu cầu "xử lý nốt gap")
| AMB | Trạng thái | Quan sát thực tế |
|---|---|---|
| AMB-03 | ✅ Đã verify | Link `Tại đây` mở trình duyệt ngoài (Samsung Internet) tới `https://viettelmoney.vn/`. |
| AMB-04 | ⛔ Không verify — cố ý | Không bấm `Đăng ký gói ưu đãi` trên tài khoản thật: FAQ xác nhận đăng ký nhầm **không được huỷ/hoàn tiền**. Cần BA/PO trả lời hoặc tài khoản test riêng. |
| AMB-05 | ✅ Đã verify | Loading = lớp phủ xám + icon xoay. Mất mạng → popup `Thông báo` / `Bạn vui lòng đợi vài phút trước khi thử lại nhé.` / `Đóng` (id `title`/`content`/`name`); đóng xong danh sách trống, không có nút thử lại; có mạng lại → vào lại màn tải bình thường. Trạng thái danh sách rỗng (backend trả 0 gói) không giả lập được. |
| AMB-06 | ◐ Phần lớn đã trả lời | Q-P2, Q-P3 trả lời đầy đủ từ FAQ; Q-P1 trả lời phần nguồn tiền; Q-P4, Q-P6 vẫn mở; Q-P5 = AMB-04. Chi tiết ở business-scenario.md mục "Câu trả lời từ FAQ". |
| AMB-07 | ✅ Đã verify | Bấm thân thẻ gói chưa dùng → mở cùng bottom sheet như `Xem chi tiết`. |

**Ghi chú môi trường:** lần mở màn đầu sau khi đăng nhập lại mất ~25s và hiện lại màn `Đang tải dữ liệu` — user xác nhận do lỗi mạng lúc đó, không phải lỗi app.

**Còn mở:** AMB-04 (Q-P5), Q-P4 (điều kiện gói CBNV), Q-P6 (môi trường UAT), hành vi khi không đủ số dư lúc đăng ký (Q-P1), trạng thái danh sách rỗng.

**TC bổ sung sau đợt 2 (user duyệt "tất cả", 2026-10-22):** TC-021 (AMB-02), TC-022 (AMB-07), TC-023 (AMB-05 — TC negative đầu tiên), TC-024 (AMB-03); sửa TC-013 (thêm ảnh hộp quà + câu mời đăng ký), TC-015 (Z: AMB-03 đã verify).
