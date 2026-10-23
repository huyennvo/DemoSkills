# KBKTCN — VIETTELMONEY_Gói ưu đãi — kiểm tra gói đang sử dụng (V1.0)

- **Mã tài liệu**: KBKTCN_VTMONEY_GoiUuDai_V1.0
- **Người lập**: huyenvt31
- **Ngày lập**: 2026-10-22
- **Loại chức năng**: APP
- **Tổng TC**: 24
- **Nguồn SRS**: (chưa xác định nguồn)

---

# Suite: Giao diện

## Màn hình 1 - Cá nhân

### TC-001 — Kiểm tra hiển thị mục Gói ưu đãi trên màn Cá nhân (anchor)

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test (`APP_USERNAME`) đã đăng nhập app ViettelMoney, đang ở màn Home

**F (Các bước)**:
1. Bấm tab `Cá nhân` ở bottom bar
2. Quan sát mục đầu tiên trong danh sách menu của màn Cá nhân, không cuộn màn hình

**G (Kết quả mong muốn)**:
1. App mở màn Cá nhân: có nút Back, họ tên tài khoản, hạng hội viên (vd `Hội viên Tiềm năng`) và danh sách menu
2. Mục `Gói ưu đãi` hiển thị ngay đầu danh sách menu (không cần cuộn), có badge `Mới` và mô tả `Gia tăng ưu đãi, miễn phí chuyển tiền`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Mục Gói ưu đãi nằm ngay đầu danh sách menu màn Cá nhân, có badge `Mới` và mô tả `Gia tăng ưu đãi, miễn phí chuyển tiền`.
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end", bước 4–5; detail-design.md — "Màn Cá nhân"

**Z (Notes)**: Trên bản 8.8.62-DEBUG (verify 2026-10-22), họ tên trên màn Cá nhân dùng id `tvUsername` (tài liệu cũ ghi `phoneNumber`).

**graph_refs**: `ui_screen:ca_nhan, field:nav_item_account, field:muc_goi_uu_dai, flow_step:4_bam_ca_nhan`

### TC-002 — Kiểm tra bấm mục Gói ưu đãi mở màn Gói ưu đãi

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đã đăng nhập, đang ở màn Cá nhân

**F (Các bước)**:
1. Bấm mục `Gói ưu đãi` trên màn Cá nhân

**G (Kết quả mong muốn)**:
1. App mở màn Gói ưu đãi: tiêu đề trang `Gói ưu đãi` (`tvEndow`) hiển thị và danh sách gói (`listSubPackage`) hiển thị

**H/I**: MEDIUM / MAJOR

**Business Rule**: Bấm mục Gói ưu đãi ở màn Cá nhân mở màn Gói ưu đãi (fragment chồng lên màn Cá nhân, nhận diện bằng `tvEndow`/`listSubPackage`).
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end", bước 5–6; detail-design.md — "Màn Gói ưu đãi" (ghi chú ⚠️ nhận diện màn)

**Z (Notes)**: Không nhận diện màn bằng text `Gói ưu đãi` vì UI dump vẫn chứa node menu của màn Cá nhân bên dưới.

**graph_refs**: `ui_screen:ca_nhan, ui_screen:goi_uu_dai, field:muc_goi_uu_dai, field:tv_endow, flow_step:5_bam_goi_uu_dai`

## Màn hình 2 - Gói ưu đãi

### TC-003 — Kiểm tra hiển thị màn Gói ưu đãi (anchor)

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã đăng nhập, đang ở màn Cá nhân

**F (Các bước)**:
1. Bấm mục `Gói ưu đãi` trên màn Cá nhân
2. Quan sát toàn bộ thành phần hiển thị trên màn Gói ưu đãi trước khi cuộn

**G (Kết quả mong muốn)**:
1. App mở màn Gói ưu đãi
2. Màn hiển thị đủ: nút Back, tiêu đề trang `Gói ưu đãi`, nút Trợ giúp `?` (`btnHelp`), danh sách thẻ gói cuộn dọc; thẻ đầu tiên `Gói Standard` có chỉ báo trạng thái gồm chấm xanh + `Đang sử dụng` + `>`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Màn Gói ưu đãi gồm tiêu đề, nút Trợ giúp, danh sách thẻ gói; gói đang dùng ở đầu danh sách.
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end", bước 6; detail-design.md — "Màn Gói ưu đãi"

**Z (Notes)**: AMB-02 resolved + đã verify 2026-10-22: `btnHelp` mở màn `Trung tâm hỗ trợ` (FAQ nhóm Gói ưu đãi). TC này chỉ assert nút hiển thị.

**graph_refs**: `ui_screen:goi_uu_dai, field:tv_endow, field:btn_help, field:list_sub_package, field:package_info, status:dang_su_dung`

### TC-004 — Kiểm tra gói đang sử dụng nằm ở thẻ đầu tiên của danh sách

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Xác định thẻ gói (`package_info`) đầu tiên trong danh sách gói
2. Xác định thẻ gói đang chứa trạng thái `Đang sử dụng` (`tv_status`)

**G (Kết quả mong muốn)**:
1. Thẻ đầu tiên có tên `Gói Standard`
2. Thẻ chứa `Đang sử dụng` chính là thẻ đầu tiên (`Gói Standard`), không phải thẻ ở vị trí khác

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-P2 — Gói đang dùng hiện ở đầu danh sách.
**Source**: business-scenario.md — "Business rules", BR-P2; "Luồng nghiệp vụ end-to-end", bước 6

**graph_refs**: `ui_screen:goi_uu_dai, field:package_info, field:tv_sub_name, field:tv_status, business_rule:br_p2_goi_dang_dung_dau_danh_sach, status:dang_su_dung`

### TC-005 — Kiểm tra thẻ Gói Standard hiển thị giá Miễn phí và không có chu kỳ

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Đọc giá (`tvPrice`) trên thẻ `Gói Standard`
2. Kiểm tra phần chu kỳ (`txtCycleName`) trên thẻ `Gói Standard`

**G (Kết quả mong muốn)**:
1. Giá hiển thị đúng `Miễn phí`
2. Thẻ `Gói Standard` không có phần chu kỳ `/30 ngày`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Gói Standard là gói miễn phí, giá `Miễn phí`; chu kỳ `/30 ngày` không có ở gói miễn phí.
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end", bước 6 và BR-P5; detail-design.md — "Màn Gói ưu đãi" (`txtCycleName`: không có ở gói miễn phí)

**graph_refs**: `ui_screen:goi_uu_dai, field:tv_price, field:txt_cycle_name, business_rule:br_p5_mac_dinh_goi_standard`

### TC-006 — Kiểm tra thẻ Gói Standard hiển thị 2 dòng quyền lợi tóm tắt

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Đọc phần quyền lợi tóm tắt trên thẻ `Gói Standard`

**G (Kết quả mong muốn)**:
1. Thẻ hiển thị đúng 2 dòng quyền lợi: `Ưu đãi 2% nạp điện thoại/thanh toán cước từ nguồn Tiền di động.` và `Ưu đãi 5% khi mua data từ nguồn Tiền di động.`

**H/I**: LOW / MINOR

**Business Rule**: Quyền lợi tóm tắt của Gói Standard: 2% nạp điện thoại/thanh toán cước, 5% mua data, từ nguồn Tiền di động.
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end", bước 6

**Z (Notes)**: Text quyền lợi bị tách nhiều node (`✔︎`, ` Ưu đãi `, `2%`...) — khi automate assert theo từng mảnh (`2%`, `5%`, `Tiền di động`) thay vì nguyên câu (detail-design.md — "Dòng quyền lợi"). Quyền lợi do backend cấu hình, có thể đổi theo thời kỳ.

**graph_refs**: `ui_screen:goi_uu_dai, field:quyen_loi_tom_tat`

### TC-007 — Kiểm tra thẻ gói đang sử dụng không có nút Đăng ký và Xem chi tiết

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Tìm nút `Đăng ký` (`btn_register_now`) bên trong thẻ `Gói Standard`
2. Tìm nút `Xem chi tiết` bên trong thẻ `Gói Standard`

**G (Kết quả mong muốn)**:
1. Thẻ `Gói Standard` không có nút `Đăng ký`
2. Thẻ `Gói Standard` không có nút `Xem chi tiết`

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-P3 — Thẻ gói đang dùng không có nút `Đăng ký` (và không có `Xem chi tiết`).
**Source**: business-scenario.md — "Business rules", BR-P3; "Luồng nghiệp vụ end-to-end", bước 6; detail-design.md — `btn_register_now` chỉ có ở gói chưa dùng

**graph_refs**: `ui_screen:goi_uu_dai, field:package_info, field:btn_register_now, field:xem_chi_tiet, business_rule:br_p3_goi_dang_dung_khong_co_dang_ky`

### TC-008 — Kiểm tra toàn bộ danh sách chỉ có duy nhất 1 gói ở trạng thái Đang sử dụng

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Cuộn danh sách gói từ đầu đến hết, ghi nhận tên mọi thẻ gói và thẻ nào có trạng thái `Đang sử dụng` (`tv_status`)
2. Đếm tổng số phần tử `Đang sử dụng` đã ghi nhận

**G (Kết quả mong muốn)**:
1. Chỉ thẻ `Gói Standard` có `Đang sử dụng`; các thẻ còn lại (Gói Start, Gói Smart, Gói Super, Gói Plus CBNV Viettel, Gói Premium CBNV Viettel, Gói Plus, Gói Premium) không có trạng thái này
2. Tổng số phần tử `Đang sử dụng` = 1

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-P1 — Mỗi tài khoản có đúng 1 gói ở trạng thái `Đang sử dụng`.
**Source**: business-scenario.md — "Business rules", BR-P1; "Danh sách gói quan sát được"

**Z (Notes)**: Tên các gói khác lấy theo khảo sát 2026-10-22, danh sách do backend cấu hình có thể đổi — khi automate chỉ assert cứng số lượng `tv_status` = 1 và gói có trạng thái là `Gói Standard`.

**graph_refs**: `ui_screen:goi_uu_dai, field:list_sub_package, field:tv_status, business_rule:br_p1_duy_nhat_1_goi_dang_dung, status:dang_su_dung`

### TC-009 — Kiểm tra thẻ gói chưa dùng có giá dạng số tiền, chu kỳ /30 ngày, nút Đăng ký và Xem chi tiết

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi; các gói còn lại là gói chưa dùng

**E (Data test)**:
```
goi_1=Gói Start | gia_tham_khao=15.000đ
goi_2=Gói Smart | gia_tham_khao=25.000đ
goi_3=Gói Super | gia_tham_khao=50.000đ
goi_4=Gói Plus CBNV Viettel | gia_tham_khao=5.500đ
goi_5=Gói Premium CBNV Viettel | gia_tham_khao=16.500đ
goi_6=Gói Plus | gia_tham_khao=5.500đ
goi_7=Gói Premium | gia_tham_khao=16.500đ
```

**F (Các bước)**:
1. Cuộn danh sách gói, với từng thẻ gói chưa dùng trong E đọc giá (`tvPrice`) và chu kỳ (`txtCycleName`)
2. Với từng thẻ gói chưa dùng trong E, kiểm tra sự hiện diện của nút `Đăng ký` và nút `Xem chi tiết` (không bấm)

**G (Kết quả mong muốn)**:
1. Mỗi thẻ có giá dạng `<số>đ` (vd `15.000đ`) và chu kỳ `/30 ngày`
2. Mỗi thẻ đều có nút `Đăng ký` và nút `Xem chi tiết`

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-P4 — Gói chưa dùng có `Đăng ký` + `Xem chi tiết`, giá dạng `<số>đ` + `/30 ngày`.
**Source**: business-scenario.md — "Business rules", BR-P4; "Danh sách gói quan sát được" (7 gói chưa dùng)

**Z (Notes)**: Giá trong E chỉ để tham khảo (backend cấu hình, đổi theo thời kỳ; đã đối chiếu đủ 7 giá trên máy thật 2026-10-22) — automate assert theo định dạng, không assert cứng giá. KHÔNG bấm `Đăng ký` (BR-P6: phát sinh trừ tiền thật). Nút `Xem chi tiết` có id `btn_detail` — id này trùng với nút `Đăng ký gói ưu đãi` trong bottom sheet, luôn scope locator theo thẻ gói.

**graph_refs**: `ui_screen:goi_uu_dai, field:tv_price, field:txt_cycle_name, field:btn_register_now, field:xem_chi_tiet, business_rule:br_p4_goi_chua_dung_co_dang_ky_xem_chi_tiet`

### TC-010 — Kiểm tra bấm thẻ Gói Standard mở trang chi tiết gói

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Bấm vào thẻ `Gói Standard` (`package_info` có `Đang sử dụng`)

**G (Kết quả mong muốn)**:
1. App mở trang chi tiết gói có tiêu đề `Gói Standard`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Tap thẻ gói đang dùng → mở trang chi tiết gói.
**Source**: business-scenario.md — "Danh sách gói quan sát được" (Gói Standard: tap thẻ → trang chi tiết); detail-design.md — "Trang chi tiết gói"

**Z (Notes)**: Trang chi tiết không lấy được UI dump (`could not get idle state`) — khi automate đặt `waitForIdleTimeout=0`, có thể cần chuyển context WebView (detail-design.md).

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:chi_tiet_goi, field:package_info`

### TC-011 — Kiểm tra bấm Xem chi tiết ở thẻ Gói Start mở trang chi tiết Gói Start

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi; Gói Start là gói chưa dùng, thẻ hiển thị giá `15.000đ`

**F (Các bước)**:
1. Ghi nhận giá hiển thị trên thẻ `Gói Start`
2. Bấm nút `Xem chi tiết` trên thẻ `Gói Start`

**G (Kết quả mong muốn)**:
1. Thẻ `Gói Start` hiển thị giá (tham khảo `15.000đ`)
2. App mở bottom sheet chi tiết (khung trượt từ dưới lên, chồng lên danh sách): tiêu đề `Gói Start` (`tvSubName`), nhãn `Giá gói` với giá khớp giá đã ghi nhận ở bước 1 kèm `/30 ngày`, và dòng thời hạn `Từ <ngày chạy test> đến hết <ngày chạy test + 29 ngày>` (`tvExpiredTime`, vd `Từ 22/10/2026 đến hết 20/11/2026`)

**H/I**: MEDIUM / MAJOR

**Business Rule**: Gói chưa dùng có nút `Xem chi tiết` để xem chi tiết gói.
**Source**: business-scenario.md — "Danh sách gói quan sát được", BR-P4; detail-design.md — "Màn Gói ưu đãi" (`Xem chi tiết`)

**Z (Notes)**: AMB-01 resolved + đã verify trên máy thật 2026-10-22 (SM-A725F, 8.8.62-DEBUG): chi tiết gói chưa dùng là bottom sheet `design_bottom_sheet`, có thêm dòng thời hạn. Đóng sheet bằng Back hệ thống. ⚠️ Id `btn_detail` vừa là nút `Xem chi tiết` trên thẻ vừa là nút `Đăng ký gói ưu đãi` trong sheet — locator phải scope theo thẻ `Gói Start`; KHÔNG bấm `btn_detail` khi sheet đang mở (trừ tiền thật).

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:chi_tiet_goi, field:xem_chi_tiet, field:tv_price`

### TC-012 — Kiểm tra bấm Back ở màn Gói ưu đãi quay về màn Cá nhân

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đã mở màn Gói ưu đãi từ màn Cá nhân

**F (Các bước)**:
1. Bấm nút Back trên màn Gói ưu đãi

**G (Kết quả mong muốn)**:
1. App quay về màn Cá nhân: danh sách menu hiển thị với mục `Gói ưu đãi`; tiêu đề trang `tvEndow` và danh sách gói `listSubPackage` không còn hiển thị

**H/I**: MEDIUM / MAJOR

**Business Rule**: Danh sách Gói ưu đãi → Back → Cá nhân.
**Source**: detail-design.md — "Điều hướng quay lại"; business-scenario.md — "Kịch bản kiểm thử gợi ý" TC05

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:ca_nhan, field:imv_back`

### TC-021 — Kiểm tra bấm nút Trợ giúp mở Trung tâm hỗ trợ với nhóm câu hỏi Gói ưu đãi

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đã đăng nhập, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Bấm nút Trợ giúp `?` (`btnHelp`) ở góc trên phải màn Gói ưu đãi
2. Bấm phím Back hệ thống 2 lần

**G (Kết quả mong muốn)**:
1. App mở màn `Trung tâm hỗ trợ` (activity `MainCCPortalActivity`): ô tìm kiếm `Nhập từ khóa tìm kiếm`, nhóm `Gói ưu đãi` có câu `Gói ưu đãi là sản phẩm như thế nào?`
2. App quay về màn Gói ưu đãi, `tvEndow` hiển thị

**H/I**: LOW / MINOR

**Business Rule**: Nút Trợ giúp trên màn Gói ưu đãi mở Trung tâm hỗ trợ với FAQ nhóm Gói ưu đãi.
**Source**: business-scenario.md — "Nút Trợ giúp (?)"; detail-design.md — "Trung tâm hỗ trợ (bấm `btnHelp`)"

**Z (Notes)**: AMB-02 resolved + đã verify 2026-10-22. Bàn phím tự bật khi vào màn → Back lần 1 chỉ đóng bàn phím. Không bấm các nút `Đăng Ký Ngay` / `Gửi yêu cầu hỗ trợ` / đánh giá trong FAQ.

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:trung_tam_ho_tro, field:btn_help`

### TC-022 — Kiểm tra bấm vào thân thẻ Gói Start mở bottom sheet chi tiết Gói Start

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi; Gói Start là gói chưa dùng

**F (Các bước)**:
1. Bấm vào tên gói `Gói Start` (`tvSubName`) trên thẻ Gói Start — không bấm vùng nút `Đăng ký` / `Xem chi tiết`
2. Bấm phím Back hệ thống

**G (Kết quả mong muốn)**:
1. App mở bottom sheet chi tiết (`design_bottom_sheet`) có tiêu đề `Gói Start`, `Giá gói` kèm `/30 ngày` và dòng thời hạn `Từ <ngày chạy test> đến hết <ngày chạy test + 29 ngày>` (`tvExpiredTime`) — giống khi bấm `Xem chi tiết`
2. Bottom sheet đóng, màn Gói ưu đãi hiển thị lại với thẻ `Gói Standard` – `Đang sử dụng` ở đầu

**H/I**: MEDIUM / MAJOR

**Business Rule**: Thẻ gói (`package_info`) clickable; bấm thân thẻ gói chưa dùng mở cùng bottom sheet chi tiết như nút `Xem chi tiết`.
**Source**: detail-design.md — "Bấm thân thẻ gói chưa dùng", "Chi tiết gói chưa dùng — bottom sheet"; business-scenario.md — "Khảo sát bổ sung 2026-10-22 (đợt 2)"

**Z (Notes)**: AMB-07 resolved + đã verify 2026-10-22. ⚠️ Trong bottom sheet, `btn_detail` là nút `Đăng ký gói ưu đãi` — KHÔNG bấm.

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:chi_tiet_goi, field:package_info, field:tv_sub_name`

### TC-023 — Kiểm tra mất mạng khi mở màn Gói ưu đãi hiển thị popup lỗi và danh sách trống

**Category**: Negative

**D (Trường hợp)**: TH: Tài khoản test đã đăng nhập, đang ở màn Cá nhân; thiết bị tắt cả Wi-Fi lẫn dữ liệu di động (không có kết nối mạng)

**F (Các bước)**:
1. Bấm mục `Gói ưu đãi` trên màn Cá nhân
2. Bấm nút `Đóng` trên popup
3. Bật lại mạng, bấm Back, rồi bấm lại mục `Gói ưu đãi`

**G (Kết quả mong muốn)**:
1. Sau khoảng 4s, popup hiển thị tiêu đề `Thông báo`, nội dung `Bạn vui lòng đợi vài phút trước khi thử lại nhé.` và nút `Đóng`
2. Popup đóng, app ở lại màn Gói ưu đãi (`tvEndow` hiển thị), không có danh sách gói (`listSubPackage` không hiển thị), không có nút thử lại
3. Màn Gói ưu đãi tải lại bình thường, thẻ đầu tiên `Gói Standard` – `Đang sử dụng`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Khi không có mạng, màn Gói ưu đãi báo lỗi bằng popup `Thông báo` và không hiển thị danh sách; có mạng lại thì vào lại màn tải bình thường.
**Source**: detail-design.md — "Popup lỗi mạng trên màn Gói ưu đãi"; business-scenario.md — "Khảo sát bổ sung 2026-10-22 (đợt 2)"

**Z (Notes)**: AMB-05 resolved + đã verify 2026-10-22. Tắt/bật mạng qua `adb shell svc wifi disable|enable` và `svc data disable|enable`; teardown **luôn** bật lại mạng kể cả khi test fail. Id popup: `title`, `content`, `name` (nút Đóng).

**graph_refs**: `ui_screen:goi_uu_dai, ui_screen:popup_loi_mang, field:list_sub_package`

## Màn hình 3 - Chi tiết gói

### TC-013 — Kiểm tra hiển thị trang chi tiết Gói Standard (anchor)

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở màn Gói ưu đãi

**F (Các bước)**:
1. Bấm vào thẻ `Gói Standard`
2. Quan sát toàn bộ thành phần hiển thị trên trang chi tiết (cuộn đến cuối trang nếu cần)

**G (Kết quả mong muốn)**:
1. App mở trang chi tiết Gói Standard
2. Trang hiển thị đủ: nút Back `←`, tiêu đề `Gói Standard`, nhãn `Giá gói` với giá trị `Miễn phí`, section `Ưu đãi viễn thông`, section `Ghi chú` có link `Tại đây`, ảnh minh hoạ hộp quà kèm câu `Đăng ký gói ưu đãi để nhận thêm ưu đãi hấp dẫn từ Viettel Money`, nút `Đăng ký gói ưu đãi`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Trang chi tiết Gói Standard gồm tiêu đề, Giá gói, Ưu đãi viễn thông, Ghi chú, link Tại đây, nút Đăng ký gói ưu đãi.
**Source**: business-scenario.md — "Trang chi tiết Gói Standard"; detail-design.md — "Trang chi tiết gói"

**Z (Notes)**: Không bấm nút `Đăng ký gói ưu đãi` (BR-P6). Trang chưa có resource-id (không dump được UI) — locator theo text/ảnh.

**graph_refs**: `ui_screen:chi_tiet_goi, field:uu_dai_vien_thong, field:ghi_chu, field:link_tai_day, field:btn_dang_ky_goi_uu_dai`

### TC-014 — Kiểm tra mục Ưu đãi viễn thông của Gói Standard hiển thị đủ 5 ưu đãi

**Category**: Business Rule

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở trang chi tiết Gói Standard

**E (Data test)**:
```
uu_dai_1=2% khi nạp tiền điện thoại Viettel từ nguồn Tiền di động
uu_dai_2=2% khi thanh toán cước di động Viettel từ nguồn Tiền di động
uu_dai_3=5% khi mua mới data từ nguồn Tiền di động, các nguồn khác ưu đãi 3%
uu_dai_4=Khuyến mãi 2% khi thanh toán cước internet, truyền hình Viettel từ nguồn Tiền di động
uu_dai_5=Khuyến mãi 1% khi mua thẻ cào, thanh toán cước điện thoại cố định, Homephone Viettel từ nguồn Tiền di động
```

**F (Các bước)**:
1. Đọc toàn bộ các dòng trong section `Ưu đãi viễn thông`

**G (Kết quả mong muốn)**:
1. Section hiển thị đủ 5 dòng ưu đãi đúng nội dung trong E, không thiếu, không thừa

**H/I**: MEDIUM / MAJOR

**Business Rule**: Ưu đãi viễn thông của Gói Standard gồm 5 mục (2% nạp ĐT, 2% thanh toán cước di động, 5%/3% mua data, 2% cước internet/truyền hình, 1% thẻ cào/cố định/Homephone).
**Source**: business-scenario.md — "Trang chi tiết Gói Standard", mục Ưu đãi viễn thông

**Z (Notes)**: Nội dung đọc từ ảnh chụp màn hình; quyền lợi do backend cấu hình có thể đổi theo thời kỳ.

**graph_refs**: `ui_screen:chi_tiet_goi, field:uu_dai_vien_thong`

### TC-015 — Kiểm tra mục Ghi chú hiển thị nội dung chính sách chung và link Tại đây

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở trang chi tiết Gói Standard

**F (Các bước)**:
1. Cuộn đến section `Ghi chú` và đọc nội dung
2. Kiểm tra link `Tại đây` trong section `Ghi chú` (không bấm)

**G (Kết quả mong muốn)**:
1. Ghi chú nêu ưu đãi chuyển tiền và chính sách khác áp dụng theo chính sách chung từng thời kỳ
2. Link `Tại đây` hiển thị trong section `Ghi chú`

**H/I**: LOW / MINOR

**Business Rule**: Ghi chú: ưu đãi chuyển tiền và chính sách khác áp dụng theo chính sách chung từng thời kỳ, có link `Tại đây`.
**Source**: business-scenario.md — "Trang chi tiết Gói Standard", mục Ghi chú

**Z (Notes)**: AMB-03 resolved + đã verify 2026-10-22: link mở trình duyệt ngoài tới `https://viettelmoney.vn/` — hành vi bấm link được test riêng ở TC-024; TC này chỉ assert hiển thị.

**graph_refs**: `ui_screen:chi_tiet_goi, field:ghi_chu, field:link_tai_day`

### TC-016 — Kiểm tra nút Đăng ký gói ưu đãi vẫn hiển thị trên trang chi tiết của gói đang sử dụng

**Category**: Edge Case

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard (thẻ trên danh sách không có nút `Đăng ký`), đã mở trang chi tiết Gói Standard

**F (Các bước)**:
1. Cuộn đến cuối trang chi tiết Gói Standard
2. Kiểm tra nút `Đăng ký gói ưu đãi` (không bấm)

**G (Kết quả mong muốn)**:
1. Trang cuộn đến cuối, section `Ghi chú` hiển thị
2. Nút `Đăng ký gói ưu đãi` hiển thị dù Gói Standard đang được sử dụng

**H/I**: MEDIUM / MAJOR

**Business Rule**: Nút `Đăng ký gói ưu đãi` vẫn hiện trên trang chi tiết kể cả khi gói này đang dùng (khác thẻ trên danh sách — BR-P3).
**Source**: business-scenario.md — "Trang chi tiết Gói Standard"; "Câu hỏi mở" Q-P5

**Z (Notes)**: AMB-04 resolved: tạm assert đúng hiện trạng (nút hiển thị), KHÔNG bấm; chờ BA/PO xác nhận đây là thiết kế hay lỗi.

**graph_refs**: `ui_screen:chi_tiet_goi, field:btn_dang_ky_goi_uu_dai, business_rule:br_p3_goi_dang_dung_khong_co_dang_ky, status:dang_su_dung`

### TC-017 — Kiểm tra bấm Back ở trang chi tiết quay về danh sách Gói ưu đãi giữ nguyên trạng thái

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở trang chi tiết Gói Standard từ màn Gói ưu đãi

**F (Các bước)**:
1. Bấm nút Back `←` trên trang chi tiết

**G (Kết quả mong muốn)**:
1. App quay về màn Gói ưu đãi (`tvEndow` hiển thị), thẻ đầu tiên vẫn là `Gói Standard` với trạng thái `Đang sử dụng`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Trang chi tiết → Back → danh sách Gói ưu đãi.
**Source**: detail-design.md — "Điều hướng quay lại"; business-scenario.md — "Kịch bản kiểm thử gợi ý" TC04

**graph_refs**: `ui_screen:chi_tiet_goi, ui_screen:goi_uu_dai, status:dang_su_dung`

### TC-024 — Kiểm tra bấm link Tại đây mở trình duyệt tới trang viettelmoney.vn

**Category**: UI State

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã mở trang chi tiết Gói Standard

**F (Các bước)**:
1. Cuộn trang chi tiết đến section `Ghi chú`
2. Bấm link `Tại đây`
3. Bấm phím Back hệ thống

**G (Kết quả mong muốn)**:
1. Section `Ghi chú` hiển thị link `Tại đây`, nằm tách xa nút `Đăng ký gói ưu đãi`
2. Trình duyệt ngoài mở trang `https://viettelmoney.vn/` (app ViettelMoney chuyển xuống nền)
3. App ViettelMoney trở lại foreground

**H/I**: LOW / MINOR

**Business Rule**: Link `Tại đây` trong Ghi chú dẫn tới trang chính sách chung trên website Viettel Money.
**Source**: detail-design.md — "Link `Tại đây` (trang chi tiết Gói Standard)"; business-scenario.md — "Khảo sát bổ sung 2026-10-22 (đợt 2)"

**Z (Notes)**: AMB-03 resolved + đã verify 2026-10-22 (mở Samsung Internet trên SM-A725F — trình duyệt mặc định có thể khác trên máy khác, assert theo URL thay vì package trình duyệt). Không dùng `am start` thẳng vào `MainFragmentActivity` để quay lại app.

**graph_refs**: `ui_screen:chi_tiet_goi, field:link_tai_day`

# Suite: Chức năng

## Kiểm tra gói đang sử dụng end-to-end

### TC-018 — Kiểm tra luồng Đăng nhập → Cá nhân → Gói ưu đãi hiển thị Gói Standard đang sử dụng

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test (`APP_USERNAME`) mặc định dùng Gói Standard; app ViettelMoney đang ở màn đăng nhập

**E (Data test)**:
```
so_dien_thoai=${APP_USERNAME}
mat_khau=${APP_PASSWORD}   # 6 chữ số
goi_mong_doi=Gói Standard
trang_thai_mong_doi=Đang sử dụng
```

**F (Các bước)**:
1. Mở app ViettelMoney và chờ màn loading kết thúc
2. Nhập số điện thoại `so_dien_thoai`, bấm `Tiếp tục`
3. Nhập đủ 6 chữ số mật khẩu `mat_khau`
4. Bấm tab `Cá nhân` ở bottom bar
5. Bấm mục `Gói ưu đãi`
6. Đọc tên gói và trạng thái của thẻ gói chứa `tv_status`

**G (Kết quả mong muốn)**:
1. App hiển thị màn nhập số điện thoại
2. App chuyển sang bước nhập mật khẩu, hiện `Xin chào, <Họ tên>`
3. App tự đăng nhập (không cần bấm nút), vào Home với header `Chào <Tên>`
4. App mở màn Cá nhân, mục `Gói ưu đãi` hiển thị đầu danh sách
5. App mở màn Gói ưu đãi (`tvEndow` hiển thị)
6. Thẻ gói đó có tên `Gói Standard` và trạng thái `Đang sử dụng`

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-P5 — Tài khoản test mặc định dùng Gói Standard (miễn phí); thẻ Gói Standard có trạng thái `Đang sử dụng`.
**Source**: business-scenario.md — "Luồng nghiệp vụ end-to-end" bước 1–7, BR-P5; features/auth/login/business-scenario.md — "Luồng nghiệp vụ: Đăng nhập"

**Z (Notes)**: Credential lấy từ `automation_test/.env` (không hardcode). Nếu phiên còn hiệu lực app có thể vào thẳng bước mật khẩu/Home — khi automate cần xử lý điều kiện này.

**graph_refs**: `activity_flow:xem_goi_dang_su_dung, ui_screen:home, ui_screen:ca_nhan, ui_screen:goi_uu_dai, field:tv_sub_name, field:tv_status, business_rule:br_p5_mac_dinh_goi_standard, status:dang_su_dung, flow_step:4_bam_ca_nhan, flow_step:5_bam_goi_uu_dai, flow_step:6_hien_danh_sach_goi`

### TC-019 — Kiểm tra trạng thái gói đang sử dụng không đổi khi vào lại màn Gói ưu đãi lần thứ hai

**Category**: Edge Case

**D (Trường hợp)**: TH: Tài khoản test đang dùng Gói Standard, đã đăng nhập, đang ở màn Cá nhân; trong phiên không thực hiện đăng ký/đổi gói nào

**F (Các bước)**:
1. Bấm mục `Gói ưu đãi`
2. Bấm nút Back trên màn Gói ưu đãi
3. Bấm lại mục `Gói ưu đãi`
4. Đếm số phần tử `Đang sử dụng` trong danh sách và đọc tên thẻ đầu tiên

**G (Kết quả mong muốn)**:
1. Màn Gói ưu đãi hiển thị, thẻ đầu tiên `Gói Standard` – `Đang sử dụng`
2. App quay về màn Cá nhân
3. Màn Gói ưu đãi hiển thị lại
4. Vẫn chỉ có 1 phần tử `Đang sử dụng`, thẻ đầu tiên vẫn là `Gói Standard`

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-P1, BR-P2 — Đúng 1 gói `Đang sử dụng`, ở đầu danh sách — phải giữ nguyên qua các lần mở màn.
**Source**: business-scenario.md — "Business rules", BR-P1, BR-P2

**Z (Notes)**: Error Guessing — kiểm tra danh sách render lại không bị nhân đôi/mất trạng thái khi mở màn lần 2.

**graph_refs**: `ui_screen:ca_nhan, ui_screen:goi_uu_dai, field:tv_status, business_rule:br_p1_duy_nhat_1_goi_dang_dung, business_rule:br_p2_goi_dang_dung_dau_danh_sach, status:dang_su_dung`

## Kiểm tra điều hướng quay lại

### TC-020 — Kiểm tra chuỗi Back từ trang chi tiết gói quay về Home

**Category**: Happy Path

**D (Trường hợp)**: TH: Tài khoản test đã đăng nhập, đi Home → Cá nhân → Gói ưu đãi → trang chi tiết Gói Standard

**F (Các bước)**:
1. Bấm nút Back `←` trên trang chi tiết Gói Standard
2. Bấm nút Back trên màn Gói ưu đãi
3. Bấm nút Back trên màn Cá nhân

**G (Kết quả mong muốn)**:
1. App quay về màn Gói ưu đãi
2. App quay về màn Cá nhân
3. App quay về Home, header hiển thị `Chào <Tên>`

**H/I**: MEDIUM / MAJOR

**Business Rule**: Trang chi tiết → Back → danh sách Gói ưu đãi → Back → Cá nhân → Back → Home.
**Source**: detail-design.md — "Điều hướng quay lại"

**graph_refs**: `ui_screen:chi_tiet_goi, ui_screen:goi_uu_dai, ui_screen:ca_nhan, ui_screen:home, field:imv_back`
