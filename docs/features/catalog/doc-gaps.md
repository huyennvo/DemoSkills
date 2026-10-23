# Doc Gaps — catalog

### AMB-01 — 2026-10-21 — Completeness Gap
**Phát hiện tại**: Step A2 Never Assume
**Mô tả**: Tài liệu không nêu kết quả mong muốn khi `/view` nhận id không phải số dương (`"abc"`, `"0"`) hoặc `/pagination` nhận id không phải số (`"abc"`). Server thực tế trả HTTP 500 + trang HTML.
**Quyết định của user**: 1b — TC kỳ vọng đúng hành vi hiện tại (HTTP 500), characterization test.
**Ảnh hưởng TC**: TC-024, TC-032, TC-033

### AMB-02 — 2026-10-21 — Completeness Gap
**Phát hiện tại**: Step A2 Never Assume
**Mô tả**: Tài liệu thiếu hành vi biên/lỗi của /bycat (cat ngoài enum, rỗng, sai hoa thường, thiếu cat), /pagination (trang cuối, id 0, thiếu id), /view (thiếu id → "Product not found." khác "Not found."), method sai (405), thiếu Content-Type (415), JSON sai (400).
**Quyết định của user**: 2a — bổ sung kết quả gọi API thật vào `features/catalog/detail-design.md` mục "Hành vi biên & lỗi".
**Ảnh hưởng TC**: TC-003, TC-008 → TC-014, TC-018 → TC-023, TC-028 → TC-031

### AMB-03 — 2026-10-21 — Missing relationship
**Phát hiện tại**: Step A2 Never Assume
**Mô tả**: Nút "Previous" không có API riêng, tài liệu không nêu FE tính id trang trước thế nào (Postman gửi cùng id với Next). Contact không gọi API. About us chỉ có ảnh/video tĩnh.
**Quyết định của user**: Đồng ý phạm vi đề xuất — không viết TC cho Previous và Contact; giữ TC About us (ảnh front.jpg trên host www, host api trả 404).
**Ảnh hưởng TC**: Previous/Contact — chưa viết, bỏ qua; About us → TC-037, TC-038
