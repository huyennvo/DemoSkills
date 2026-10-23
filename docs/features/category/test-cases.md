# KBKTCN — DEMOBLAZE_Category (V1.0)

- **Mã tài liệu**: KBKTCN_DEMOBLAZE_Category_V1.0
- **Người lập**: huyenvt31
- **Ngày lập**: 2026-10-22
- **Loại chức năng**: API
- **Tổng TC**: 17
- **Nguồn SRS**: https://www.postman.com/asmaa-2393668/demoblaze (collection `Demoblaze API Tests`) + `automation_test/docs/features/category/`

---

# Suite: Chức năng

## Lọc sản phẩm theo danh mục hợp lệ (POST /bycat)

### TC-001 — Lọc danh mục Phones trả đúng danh sách điện thoại

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chưa đăng nhập chọn danh mục Phones (mã API `phone`).

**E (Data test)**:
```
cat=phone
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body `{"cat": "phone"}`, không kèm token/cookie.
2. Kiểm tra field `cat` của từng phần tử trong `Items`.
3. Tìm phần tử sản phẩm neo `id = 1` trong `Items`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body có key `Items` là mảng không rỗng.
2. Mọi phần tử có `cat = "phone"`.
3. Có phần tử `id = 1` với `title = "Samsung galaxy s6"`, `price = 360.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-CAT1 — UI "Phones" ↔ mã `phone`; BR-CAT2 — mọi sản phẩm trả về có `cat` bằng mã đã gửi.
**Source**: category/business-scenario.md — BR-CAT1, BR-CAT2, Test scenarios #1 (Postman `PhoneCategory`); category/detail-design.md — Dữ liệu tham chiếu theo danh mục; global-data.md §5

**Z (Notes)**: Assert theo cấu trúc + sản phẩm neo id 1, không đếm cứng số lượng (global-data.md §5: dữ liệu public có thể thay đổi). Số lượng theo snapshot được kiểm riêng ở TC-005.

**graph_refs**: `activity_flow:chon_danh_muc, field:bycat_cat, business_rule:ma_danh_muc_hop_le, business_rule:san_pham_dung_danh_muc`

### TC-002 — Lọc danh mục Laptops trả đúng danh sách laptop

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chưa đăng nhập chọn danh mục Laptops (mã API `notebook`).

**E (Data test)**:
```
cat=notebook
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body `{"cat": "notebook"}`, không kèm token/cookie.
2. Kiểm tra field `cat` của từng phần tử trong `Items`.
3. Tìm phần tử sản phẩm neo `id = 8` trong `Items`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body có key `Items` là mảng không rỗng.
2. Mọi phần tử có `cat = "notebook"`.
3. Có phần tử `id = 8` với `title = "Sony vaio i5"`, `price = 790.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-CAT1 — UI "Laptops" ↔ mã `notebook`; BR-CAT2 — mọi sản phẩm trả về có `cat` bằng mã đã gửi.
**Source**: category/business-scenario.md — BR-CAT1, BR-CAT2, Test scenarios #2 (Postman `Laptops Category`); category/detail-design.md — Dữ liệu tham chiếu theo danh mục; global-data.md §5

**Z (Notes)**: Assert theo cấu trúc + sản phẩm neo id 8, không đếm cứng. URL Postman `Laptops Category` có `?=` thừa — dùng `/bycat` không query string; hành vi với `?=` kiểm riêng ở TC-017.

**graph_refs**: `activity_flow:chon_danh_muc, field:bycat_cat, business_rule:ma_danh_muc_hop_le, business_rule:san_pham_dung_danh_muc`

### TC-003 — Lọc danh mục Monitors trả đúng danh sách màn hình

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chưa đăng nhập chọn danh mục Monitors (mã API `monitor`).

**E (Data test)**:
```
cat=monitor
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body `{"cat": "monitor"}`, không kèm token/cookie.
2. Kiểm tra field `cat` của từng phần tử trong `Items`.
3. Tìm phần tử sản phẩm neo `id = 10` trong `Items`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body có key `Items` là mảng không rỗng.
2. Mọi phần tử có `cat = "monitor"`.
3. Có phần tử `id = 10` với `title = "Apple monitor 24"`, `price = 400.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-CAT1 — UI "Monitors" ↔ mã `monitor`; BR-CAT2 — mọi sản phẩm trả về có `cat` bằng mã đã gửi.
**Source**: category/business-scenario.md — BR-CAT1, BR-CAT2, Test scenarios #3 (Postman `Monitors Category`); category/detail-design.md — Dữ liệu tham chiếu theo danh mục; global-data.md §5

**Z (Notes)**: Assert theo cấu trúc + sản phẩm neo id 10, không đếm cứng.

**graph_refs**: `activity_flow:chon_danh_muc, field:bycat_cat, business_rule:ma_danh_muc_hop_le, business_rule:san_pham_dung_danh_muc`

### TC-004 — Mỗi sản phẩm trả về đúng schema Product

**Category**: Business Rule

**D (Trường hợp)**: TH: Kiểm tra cấu trúc từng phần tử `Items` của cả 3 danh mục hợp lệ.

**E (Data test)**:
```
cat=phone
cat=notebook
cat=monitor
```

**F (Các bước)**:
1. Với mỗi giá trị `cat` ở E, gửi `POST /bycat` body `{"cat": "<cat>"}`.
2. Validate từng phần tử `Items` theo JSON schema Product.

**G (Kết quả mong muốn)**:
1. Cả 3 request trả HTTP 200, `Items` không rỗng.
2. Mọi phần tử có đủ 6 field `cat`, `desc`, `id`, `img`, `price`, `title`; `id` và `price` kiểu number; `cat`, `desc`, `img`, `title` kiểu string; `cat` thuộc `phone` | `notebook` | `monitor`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-CAT3 — mỗi sản phẩm có đủ 6 field theo schema Product.
**Source**: category/business-scenario.md — BR-CAT3; category/detail-design.md — JSON schema Product

**graph_refs**: `validation_rule:schema_product, field:bycat_cat`

### TC-005 — Kết quả lọc trả toàn bộ sản phẩm trong 1 response, không phân trang

**Category**: Business Rule

**D (Trường hợp)**: TH: Danh mục có nhiều sản phẩm — kiểm tra response không có cơ chế phân trang.

**E (Data test)**:
```
cat=phone     | so_san_pham=7
cat=notebook  | so_san_pham=6
cat=monitor   | so_san_pham=2
```

**F (Các bước)**:
1. Với mỗi dòng ở E, gửi `POST /bycat` body `{"cat": "<cat>"}`.
2. Kiểm tra các key cấp gốc của body response và số phần tử `Items`.

**G (Kết quả mong muốn)**:
1. Cả 3 request trả HTTP 200.
2. Body chỉ có key `Items`, không có key `LastEvaluatedKey`; số phần tử `Items` bằng `so_san_pham` tương ứng.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-CAT6 — kết quả lọc không phân trang, trả toàn bộ sản phẩm của danh mục trong 1 response.
**Source**: category/business-scenario.md — BR-CAT6; category/detail-design.md — "Response thành công" (không có `LastEvaluatedKey`), Dữ liệu tham chiếu theo danh mục

**Z (Notes)**: TC duy nhất kiểm số lượng theo snapshot 2026-10-21 (global-data.md §5) — cần đếm để chứng minh trả "toàn bộ" sản phẩm; cập nhật E khi catalog đổi.

**graph_refs**: `business_rule:khong_phan_trang`

### TC-006 — Mỗi sản phẩm thuộc đúng 1 danh mục, 3 danh mục không trùng sản phẩm

**Category**: Business Rule

**D (Trường hợp)**: TH: Đối chiếu id sản phẩm giữa 3 danh mục hợp lệ.

**F (Các bước)**:
1. Gửi lần lượt `POST /bycat` với `cat` = `phone`, `notebook`, `monitor`; gộp `id` của cả 3 response.
2. Đếm số lần xuất hiện của từng `id` trong tập đã gộp.

**G (Kết quả mong muốn)**:
1. Cả 3 request trả HTTP 200, mỗi `Items` không rỗng.
2. Mỗi id xuất hiện đúng 1 lần — số id khác nhau bằng tổng số phần tử của 3 response; 3 sản phẩm neo id 1, 8, 10 lần lượt chỉ nằm trong `phone`, `notebook`, `monitor`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-CAT2 — mỗi sản phẩm thuộc đúng 1 danh mục.
**Source**: category/business-scenario.md — BR-CAT2, "Scenario bổ sung đề xuất" (phone 7, notebook 6, monitor 2 — tổng 15, không trùng id)

**Z (Notes)**: Không đếm cứng tổng 15 (snapshot 2026-10-21) để TC ổn định khi catalog đổi. Đối chiếu với toàn catalog qua `/entries` + `/pagination` thuộc phạm vi feature catalog.

**graph_refs**: `business_rule:san_pham_dung_danh_muc`

### TC-007 — Gọi /bycat nhiều lần không làm thay đổi dữ liệu

**Category**: Business Rule

**D (Trường hợp)**: TH: Xác nhận `/bycat` chỉ đọc — dù Postman mô tả là "Add Cat Information", gọi lặp không thêm/sửa sản phẩm.

**E (Data test)**:
```
cat=monitor
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "monitor"}`, lưu toàn bộ `Items`.
2. Gửi lại đúng request ở bước 1 lần thứ 2.
3. So sánh `Items` của 2 lần gọi.

**G (Kết quả mong muốn)**:
1. HTTP 200, `Items` không rỗng.
2. HTTP 200, `Items` không rỗng.
3. Hai mảng `Items` giống hệt nhau về số phần tử và giá trị 6 field của từng phần tử (không xuất hiện sản phẩm mới, không đổi giá trị).

**H/I**: MEDIUM / MAJOR

**Business Rule**: Endpoint chỉ đọc, không thay đổi dữ liệu — mô tả "Add Cat Information" trong Postman không đúng nghiệp vụ.
**Source**: category/business-scenario.md — "Mô tả chung" (chỉ đọc) và cảnh báo mô tả Postman; category/detail-design.md — "Sai khác giữa tài liệu Postman và thực tế" mục 1

**graph_refs**: `business_rule:chi_doc, activity_flow:chon_danh_muc`

### TC-008 — Đường dẫn ảnh `img` là tương đối và tải được trên host www

**Category**: Business Rule

**D (Trường hợp)**: TH: Frontend hiển thị ảnh sản phẩm từ field `img` của kết quả lọc danh mục Monitors.

**E (Data test)**:
```
cat=monitor
host_anh=https://www.demoblaze.com/
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "monitor"}`, lấy giá trị `img` của phần tử `id = 10`.
2. Gửi `GET https://www.demoblaze.com/<img>`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `img` của id 10 là `"imgs/apple_cinema.jpg"` — không bắt đầu bằng `http` hay `/`.
2. HTTP 200, body không rỗng.

**H/I**: LOW / MINOR

**Business Rule**: `img` là đường dẫn ảnh tương đối, host `https://www.demoblaze.com/`.
**Source**: category/detail-design.md — bảng field Product (`img`, ví dụ `"imgs/apple_cinema.jpg"`)

**graph_refs**: `field:product_img`

### TC-009 — Tiêu đề sản phẩm id 9 có ký tự xuống dòng cuối chuỗi

**Category**: Edge Case

**D (Trường hợp)**: TH: Sản phẩm id 9 trong danh mục Laptops có `title` kết thúc bằng `\n`.

**E (Data test)**:
```
cat=notebook
id=9
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "notebook"}`, lấy `title` của phần tử `id = 9`.
2. Trim khoảng trắng/xuống dòng đầu-cuối của `title` vừa lấy.

**G (Kết quả mong muốn)**:
1. HTTP 200; `title` của id 9 bằng `"Sony vaio i7\n"` (kết thúc bằng ký tự `\n`).
2. Giá trị sau trim bằng `"Sony vaio i7"`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: `title` có thể có `\n` cuối (vd id 9) — so sánh `title` nên trim.
**Source**: category/detail-design.md — bảng field Product (`title`), "Lưu ý automation"; global-data.md §5 (id 9)

**graph_refs**: `field:product_title`

## Mã danh mục không hợp lệ (POST /bycat)

### TC-010 — Lọc với mã danh mục ngoài 3 giá trị hợp lệ trả danh sách rỗng

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi `cat` không thuộc `phone` | `notebook` | `monitor`.

**E (Data test)**:
```
cat=tablet
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "tablet"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body đúng bằng `{"Items": []}` — không có `errorMessage`.

**H/I**: LOW / MINOR

**Business Rule**: BR-CAT4 — mã không hợp lệ → danh sách rỗng, không báo lỗi.
**Source**: category/business-scenario.md — BR-CAT4; category/detail-design.md — Hành vi biên & lỗi (`{cat:"tablet"}`)

**graph_refs**: `field:bycat_cat, business_rule:ma_danh_muc_hop_le`

### TC-011 — Lọc với mã danh mục rỗng trả danh sách rỗng

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi `cat` là chuỗi rỗng.

**E (Data test)**:
```
cat=""
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": ""}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body đúng bằng `{"Items": []}` — không có `errorMessage`.

**H/I**: LOW / MINOR

**Business Rule**: BR-CAT4 — mã rỗng → danh sách rỗng, không báo lỗi (khác trường hợp thiếu hẳn field `cat`).
**Source**: category/business-scenario.md — BR-CAT4; category/detail-design.md — Hành vi biên & lỗi (`{cat:""}`)

**graph_refs**: `field:bycat_cat, business_rule:ma_danh_muc_hop_le`

### TC-012 — Mã danh mục phân biệt hoa thường

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi mã danh mục đúng chữ nhưng sai hoa thường.

**E (Data test)**:
```
cat=Phone
cat=PHONE
```

**F (Các bước)**:
1. Với mỗi giá trị `cat` ở E, gửi `POST /bycat` body `{"cat": "<cat>"}`.

**G (Kết quả mong muốn)**:
1. Cả 2 request trả HTTP 200, body đúng bằng `{"Items": []}`.

**H/I**: LOW / MINOR

**Business Rule**: BR-CAT4 — so khớp `cat` chính xác, phân biệt hoa thường.
**Source**: category/business-scenario.md — BR-CAT4; category/detail-design.md — bảng Request body (`cat` phân biệt hoa thường), Hành vi biên & lỗi (`{cat:"Phone"}` / `{cat:"PHONE"}`)

**graph_refs**: `field:bycat_cat, business_rule:so_khop_phan_biet_hoa_thuong`

### TC-013 — Lọc danh mục thiếu tham số cat báo lỗi nghiệp vụ

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi body JSON hợp lệ nhưng không có field `cat`.

**E (Data test)**:
```
body={}
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body `{}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body đúng bằng `{"errorMessage": "Bad parameter, missing cat."}` — không có key `Items`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-CAT5 — thiếu field `cat` → lỗi nghiệp vụ HTTP 200 + `errorMessage`.
**Source**: category/business-scenario.md — BR-CAT5; category/detail-design.md — Hành vi biên & lỗi (`{}`), "Lưu ý automation" (assert theo body)

**graph_refs**: `field:bycat_cat, validation_rule:bat_buoc_cat`

## Lỗi giao thức HTTP (POST /bycat)

### TC-014 — Gọi /bycat bằng method GET bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Client gọi `/bycat` sai method.

**F (Các bước)**:
1. Gửi `GET https://api.demoblaze.com/bycat`.

**G (Kết quả mong muốn)**:
1. HTTP 405, body HTML chứa "Method Not Allowed".

**H/I**: LOW / MINOR

**Business Rule**: `/bycat` chỉ nhận method POST.
**Source**: category/business-scenario.md — "Scenario bổ sung đề xuất" (GET → 405); category/detail-design.md — Hành vi biên & lỗi (`GET /bycat`)

**graph_refs**: `activity_flow:chon_danh_muc`

### TC-015 — Gọi /bycat không có Content-Type JSON bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi body hợp lệ nhưng thiếu header `Content-Type: application/json`.

**E (Data test)**:
```
body={"cat": "notebook"}
header Content-Type: (không gửi)
```

**F (Các bước)**:
1. Gửi `POST /bycat` body chuỗi `{"cat": "notebook"}`, không kèm header `Content-Type`.

**G (Kết quả mong muốn)**:
1. HTTP 415, body HTML chứa "Unsupported Media Type".

**H/I**: LOW / MINOR

**Business Rule**: Header `Content-Type: application/json` là bắt buộc.
**Source**: category/detail-design.md — POST /bycat Header (bắt buộc), Hành vi biên & lỗi (415); category/business-scenario.md — "Scenario bổ sung đề xuất"

**graph_refs**: `activity_flow:chon_danh_muc`

### TC-016 — Gọi /bycat với JSON sai cú pháp bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi body không phải JSON hợp lệ.

**E (Data test)**:
```
body={cat:
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body thô `{cat:`.

**G (Kết quả mong muốn)**:
1. HTTP 400, body HTML chứa "Bad Request".

**H/I**: LOW / MINOR

**Business Rule**: Body phải là JSON hợp lệ.
**Source**: category/detail-design.md — Hành vi biên & lỗi (`{cat:` → 400); category/business-scenario.md — "Scenario bổ sung đề xuất"

**graph_refs**: `activity_flow:chon_danh_muc`

### TC-017 — Gọi /bycat có query string rỗng `?=` vẫn lọc đúng

**Category**: Edge Case

**D (Trường hợp)**: TH: Client dùng đúng URL của Postman `Laptops Category` — `/bycat?=` (query rỗng thừa).

**E (Data test)**:
```
url=https://api.demoblaze.com/bycat?=
cat=notebook
```

**F (Các bước)**:
1. Gửi `POST https://api.demoblaze.com/bycat?=` header `Content-Type: application/json`, body `{"cat": "notebook"}`.
2. Tìm phần tử sản phẩm neo `id = 8` trong `Items`.

**G (Kết quả mong muốn)**:
1. HTTP 200, `Items` không rỗng, mọi phần tử có `cat = "notebook"`.
2. Có phần tử `id = 8` với `title = "Sony vaio i5"`, `price = 790.0`.

**H/I**: LOW / MINOR

**Business Rule**: URL `/bycat?=` (query rỗng) vẫn chạy như `/bycat`.
**Source**: api-reference.md — Ghi chú mục 2 ("URL lỗi `{{baseUrl}}/bycat?=` (query rỗng) — vẫn chạy nhưng nên bỏ `?=`"); category/detail-design.md — POST /bycat URL

**Z (Notes)**: Bổ sung từ audit độc lập.

**graph_refs**: `field:bycat_cat, activity_flow:chon_danh_muc`
