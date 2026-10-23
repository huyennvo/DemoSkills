# KBKTCN — DEMOBLAZE_Catalog (V1.0)

- **Mã tài liệu**: KBKTCN_DEMOBLAZE_Catalog_V1.0
- **Người lập**: huyenvt31
- **Ngày lập**: 2026-10-21
- **Loại chức năng**: API
- **Tổng TC**: 38
- **Nguồn SRS**: https://www.postman.com/asmaa-2393668/demoblaze (collection `Demoblaze API Tests`) + `automation_test/docs/features/catalog/`

---

# Suite: Chức năng
## Danh sách sản phẩm trang chủ (GET /entries)

### TC-001 — Lấy danh sách sản phẩm trang chủ thành công

**Category**: Happy Path

**D (Trường hợp)**: TH: Gọi API trang chủ không cần đăng nhập, catalog đang có 15 sản phẩm (id 1–15).

**F (Các bước)**:
1. Gửi `GET https://api.demoblaze.com/entries` (không body).
2. Đọc trường `Items` và `LastEvaluatedKey` trong response.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` là array gồm đúng 9 phần tử có `id` lần lượt 1 → 9; `LastEvaluatedKey` = `{"id": "9"}`.

**H/I**: HIGH / CRITICAL

**Business Rule**: Trang chủ hiển thị 9 sản phẩm đầu kèm khoá phân trang `LastEvaluatedKey`.
**Source**: catalog/detail-design.md — "GET /entries"; catalog/business-scenario.md — "Luồng nghiệp vụ: Trang chủ & danh mục" bước 1; Postman `HomePage/Navigate to Homepage via Logo`

**graph_refs**: `activity_flow:xem_catalog, business_rule:trang_chu_9_san_pham`

### TC-002 — Mỗi sản phẩm trong /entries đúng schema Product

**Category**: Business Rule

**D (Trường hợp)**: TH: Kiểm tra cấu trúc dữ liệu của từng sản phẩm trả về ở trang chủ.

**F (Các bước)**:
1. Gửi `GET /entries`.
2. Đọc từng phần tử trong mảng `Items`.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. Mọi phần tử có đủ 6 field: `cat` (string, ∈ {`phone`, `notebook`, `monitor`}), `desc` (string), `id` (number), `img` (string, dạng `imgs/<file>`), `price` (number), `title` (string); không phần tử nào fail schema.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C2 — mỗi sản phẩm có `cat, desc, id(number), img, price(number), title`.
**Source**: catalog/detail-design.md — "JSON schema Product"; catalog/business-scenario.md — BR-C2

**graph_refs**: `business_rule:product_schema, field:product_cat, field:product_price`

### TC-003 — Gọi /entries bằng method POST bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Client gọi sai method cho endpoint chỉ hỗ trợ GET.

**E (Data test)**:
```
method=POST
body={}
```

**F (Các bước)**:
1. Gửi `POST /entries` với header `Content-Type: application/json`, body `{}`.

**G (Kết quả mong muốn)**:
1. HTTP 405, body HTML chứa "Method Not Allowed".

**H/I**: LOW / MINOR

**Business Rule**: /entries chỉ nhận GET.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `/entries POST`

**Z (Notes)**: AMB-02 resolved: hành vi biên/lỗi bổ sung vào detail-design.md từ request thật.

**graph_refs**: `validation_rule:http_method`

## Lọc sản phẩm theo danh mục (POST /bycat)

### TC-004 — Lọc danh mục Phones trả đúng danh sách điện thoại

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chọn danh mục Phones.

**E (Data test)**:
```
cat=phone
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "phone"}`.
2. Đọc mảng `Items` trong response.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` có 7 phần tử, tất cả có `cat = "phone"`; có phần tử `title = "Samsung galaxy s6"` với `price = 360.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C1 — danh mục `phone` tương ứng "Phones".
**Source**: catalog/business-scenario.md — BR-C1, TC #1 (Postman `PhoneCategory`); global-data.md §5

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `activity_flow:xem_catalog, field:bycat_cat, business_rule:loc_theo_danh_muc`

### TC-005 — Lọc danh mục Laptops trả đúng danh sách laptop

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chọn danh mục Laptops.

**E (Data test)**:
```
cat=notebook
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "notebook"}`.
2. Đọc mảng `Items` trong response.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` có 6 phần tử, tất cả có `cat = "notebook"`; có phần tử `title = "Sony vaio i5"` với `price = 790.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C1 — danh mục `notebook` tương ứng "Laptops".
**Source**: catalog/business-scenario.md — BR-C1, TC #2 (Postman `Laptops Category`); global-data.md §5

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `field:bycat_cat, business_rule:loc_theo_danh_muc`

### TC-006 — Lọc danh mục Monitors trả đúng danh sách màn hình

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng chọn danh mục Monitors.

**E (Data test)**:
```
cat=monitor
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "monitor"}`.
2. Đọc mảng `Items` trong response.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` có 2 phần tử, tất cả có `cat = "monitor"`; có phần tử `title = "Apple monitor 24"` với `price = 400.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C1 — danh mục `monitor` tương ứng "Monitors".
**Source**: catalog/business-scenario.md — BR-C1, TC #3 (Postman `Monitors Category`); global-data.md §5

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `field:bycat_cat, business_rule:loc_theo_danh_muc`

### TC-007 — Ba danh mục phủ đủ toàn bộ catalog, không trùng sản phẩm

**Category**: Business Rule

**D (Trường hợp)**: TH: Đối chiếu tổng sản phẩm theo 3 danh mục với toàn bộ catalog (`/entries` + `/pagination`).

**F (Các bước)**:
1. Gửi lần lượt `POST /bycat` với `cat` = `phone`, `notebook`, `monitor`; gộp `id` của cả 3 response.
2. Gửi `GET /entries`, rồi `POST /pagination` với `id` = `LastEvaluatedKey.id`; gộp `id` của 2 response.
3. So sánh 2 tập id thu được ở bước 1 và bước 2.

**G (Kết quả mong muốn)**:
1. Tập id từ 3 danh mục có 15 phần tử, không id nào xuất hiện ở 2 danh mục.
2. Tập id toàn catalog có 15 phần tử (id 1 → 15).
3. Hai tập id bằng nhau.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C1 — mọi sản phẩm thuộc đúng 1 trong 3 danh mục hợp lệ.
**Source**: catalog/business-scenario.md — BR-C1, "Scenario bổ sung đề xuất" (tổng 3 danh mục = toàn catalog); global-data.md §5

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `business_rule:loc_theo_danh_muc, business_rule:phan_trang_theo_id`

### TC-008 — Lọc với danh mục không tồn tại trả danh sách rỗng

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi giá trị `cat` ngoài 3 danh mục hợp lệ.

**E (Data test)**:
```
cat=tablet
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "tablet"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body đúng bằng `{"Items": []}`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-C1 — chỉ `phone`/`notebook`/`monitor` có dữ liệu.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{cat:"tablet"}`

**Z (Notes)**: AMB-02 resolved: hành vi bổ sung từ request thật.

**graph_refs**: `field:bycat_cat, validation_rule:bycat_cat_enum`

### TC-009 — Lọc với danh mục rỗng trả danh sách rỗng

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi `cat` là chuỗi rỗng.

**E (Data test)**:
```
cat=""
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": ""}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body đúng bằng `{"Items": []}` (không trả lỗi, không trả toàn bộ catalog).

**H/I**: LOW / MINOR

**Business Rule**: BR-C1 — `cat` rỗng không khớp danh mục nào.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{cat:""}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:bycat_cat, validation_rule:bycat_cat_enum`

### TC-010 — Danh mục phân biệt hoa thường

**Category**: Edge Case

**D (Trường hợp)**: TH: Client gửi tên danh mục đúng chữ nhưng sai hoa thường.

**E (Data test)**:
```
cat=Phone   → Items = []
cat=PHONE   → Items = []
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "Phone"}`.
2. Gửi `POST /bycat` body `{"cat": "PHONE"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body `{"Items": []}`.
2. HTTP 200, body `{"Items": []}`.

**H/I**: LOW / MINOR

**Business Rule**: Giá trị `cat` so khớp chính xác, phân biệt hoa thường.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{cat:"Phone"} / {cat:"PHONE"}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:bycat_cat, validation_rule:bycat_cat_enum`

### TC-011 — Lọc danh mục thiếu tham số cat báo lỗi

**Category**: Negative

**D (Trường hợp)**: TH: Body request không có field `cat`.

**E (Data test)**:
```
body={}
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{}`.

**G (Kết quả mong muốn)**:
1. HTTP 200, body `{"errorMessage": "Bad parameter, missing cat."}`, không có field `Items`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: `cat` là tham số bắt buộc; lỗi nghiệp vụ trả HTTP 200 + `errorMessage`.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{}` của /bycat; system-overview.md §4

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:bycat_cat, validation_rule:bycat_cat_required`

### TC-012 — Gọi /bycat bằng method GET bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Client gọi sai method cho endpoint chỉ hỗ trợ POST.

**F (Các bước)**:
1. Gửi `GET /bycat` (không body).

**G (Kết quả mong muốn)**:
1. HTTP 405, body HTML chứa "Method Not Allowed".

**H/I**: LOW / MINOR

**Business Rule**: /bycat chỉ nhận POST.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `/bycat GET`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `validation_rule:http_method`

### TC-013 — Gọi /bycat không có Content-Type JSON bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Body đúng nội dung nhưng không gửi header `Content-Type: application/json`.

**E (Data test)**:
```
header Content-Type: (không gửi)
body={"cat":"monitor"}
```

**F (Các bước)**:
1. Gửi `POST /bycat` body `{"cat": "monitor"}` không kèm header `Content-Type`.

**G (Kết quả mong muốn)**:
1. HTTP 415, body HTML chứa "Unsupported Media Type".

**H/I**: LOW / MINOR

**Business Rule**: API yêu cầu `Content-Type: application/json` cho request có body.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng không Content-Type; api-reference.md — Content-Type

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `validation_rule:content_type_json`

### TC-014 — Gọi /bycat với JSON sai cú pháp bị từ chối

**Category**: Negative

**D (Trường hợp)**: TH: Body không phải JSON hợp lệ.

**E (Data test)**:
```
body={cat:
```

**F (Các bước)**:
1. Gửi `POST /bycat` header `Content-Type: application/json`, body raw `{cat:`.

**G (Kết quả mong muốn)**:
1. HTTP 400, body HTML chứa "Bad Request".

**H/I**: LOW / MINOR

**Business Rule**: Body phải là JSON hợp lệ.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng JSON sai cú pháp

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `validation_rule:json_body`

## Phân trang sản phẩm (POST /pagination)

### TC-015 — Phân trang Next từ trang chủ trả trang 2

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng bấm Next ở trang chủ (trang 1 kết thúc ở id 9).

**E (Data test)**:
```
id="9"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "9"}`.
2. Đọc `Items`, `LastEvaluatedKey`, `ScannedCount`.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` có 6 phần tử id 10 → 15 theo thứ tự tăng dần; `LastEvaluatedKey` = `{"id": "15"}`; `ScannedCount` = 6.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C3 — phân trang theo id toàn catalog, trả sản phẩm có id > id gửi lên.
**Source**: catalog/detail-design.md — "POST /pagination" (ví dụ `{id:"9"}`); catalog/business-scenario.md — bước 2; Postman `Next button at phone category`

**graph_refs**: `activity_flow:xem_catalog, business_rule:phan_trang_theo_id, field:pagination_id`

### TC-016 — Phân trang từ id sản phẩm đầu tiên trả các sản phẩm có id lớn hơn

**Category**: Business Rule

**D (Trường hợp)**: TH: Gọi phân trang với id của sản phẩm điện thoại đầu tiên (như request Postman `Next button at phone category` với `phoneProductId`).

**E (Data test)**:
```
id="1"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "1"}`.
2. Đọc `Items`, `LastEvaluatedKey`, `ScannedCount`.

**G (Kết quả mong muốn)**:
1. HTTP 200.
2. `Items` có 9 phần tử id 2 → 10 (mọi id > 1, không chứa id 1); `LastEvaluatedKey` = `{"id": "10"}`; `ScannedCount` = 9.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-C3 — /pagination trả sản phẩm có id > id gửi lên, không lọc theo danh mục.
**Source**: catalog/business-scenario.md — Test scenarios #7 (Postman `Next button at phone category`); api-reference.md — Catalog, dòng /pagination; global-data.md §4 (`phoneProductId` = 1)

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `business_rule:phan_trang_theo_id, field:pagination_id`

### TC-017 — Chuỗi phân trang từ /entries không trùng và không sót sản phẩm

**Category**: Business Rule

**D (Trường hợp)**: TH: Duyệt toàn bộ catalog bằng cách lấy `LastEvaluatedKey.id` của trang trước làm `id` cho trang sau, đến khi `Items` rỗng.

**F (Các bước)**:
1. Gửi `GET /entries`, lưu id các sản phẩm và `LastEvaluatedKey.id`.
2. Gửi `POST /pagination` với `id` = `LastEvaluatedKey.id` vừa lưu; lặp lại với `LastEvaluatedKey.id` mới đến khi `Items` rỗng.
3. Gộp id của mọi trang.

**G (Kết quả mong muốn)**:
1. HTTP 200; 9 id (1 → 9); `LastEvaluatedKey.id = "9"`.
2. Trang `id="9"` trả id 10 → 15; trang `id="15"` trả `Items` rỗng.
3. Tập id gộp có đúng 15 phần tử (1 → 15), không id nào lặp lại giữa các trang.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C3 — phân trang theo id toàn catalog (không theo danh mục).
**Source**: catalog/business-scenario.md — BR-C3; catalog/detail-design.md — "POST /pagination", "Hành vi biên & lỗi" dòng `{id:"15"}`

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `business_rule:phan_trang_theo_id, field:pagination_id`

### TC-018 — Danh sách trang chủ trùng khớp phần đầu catalog

**Category**: Business Rule

**D (Trường hợp)**: TH: 9 sản phẩm trang chủ phải giống dữ liệu `/pagination` bắt đầu từ id 0.

**E (Data test)**:
```
id="0"
```

**F (Các bước)**:
1. Gửi `GET /entries`, lưu `Items`.
2. Gửi `POST /pagination` body `{"id": "0"}`, lưu `Items`.
3. So sánh 2 danh sách theo thứ tự.

**G (Kết quả mong muốn)**:
1. HTTP 200; 9 sản phẩm id 1 → 9.
2. HTTP 200; 9 phần tử id 1 → 9.
3. Hai danh sách giống hệt nhau (cả 6 field, đúng thứ tự).

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-C3 — /entries là trang đầu của chuỗi phân trang theo id.
**Source**: catalog/business-scenario.md — BR-C3; catalog/detail-design.md — "GET /entries", "Hành vi biên & lỗi" dòng `{id:"0"}`

**graph_refs**: `business_rule:phan_trang_theo_id, business_rule:trang_chu_9_san_pham`
### TC-019 — Phân trang từ id áp chót trả 1 sản phẩm cuối

**Category**: Edge Case

**D (Trường hợp)**: TH: Gửi id ngay trước sản phẩm cuối cùng (id lớn nhất = 15).

**E (Data test)**:
```
id="14"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "14"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `Items` có 1 phần tử `id = 15`, `title = "MacBook Pro"`; `LastEvaluatedKey` = `{"id": "15"}`; `ScannedCount` = 1.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-C3 — biên max-1 của id phân trang.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"14"}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `business_rule:phan_trang_theo_id, field:pagination_id`

### TC-020 — Phân trang từ id cuối cùng trả trang rỗng

**Category**: Edge Case

**D (Trường hợp)**: TH: Người dùng đang ở trang cuối (id lớn nhất = 15) và bấm Next.

**E (Data test)**:
```
id="15"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "15"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body đúng bằng `{"Items": [], "LastEvaluatedKey": {"id": "None"}, "ScannedCount": 0}`.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C3 — hết dữ liệu thì trả trang rỗng, `LastEvaluatedKey.id = "None"`.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"15"}`

**Z (Notes)**: AMB-02 resolved. Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `business_rule:phan_trang_theo_id, field:pagination_id`

### TC-021 — Phân trang từ id 0 trả từ sản phẩm đầu tiên

**Category**: Edge Case

**D (Trường hợp)**: TH: Gửi id nhỏ hơn id sản phẩm nhỏ nhất (id nhỏ nhất = 1).

**E (Data test)**:
```
id="0"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "0"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `Items` có 9 phần tử id 1 → 9 (phần tử đầu `title = "Samsung galaxy s6"`); `LastEvaluatedKey` = `{"id": "9"}`; `ScannedCount` = 9.

**H/I**: LOW / MINOR

**Business Rule**: BR-C3 — trả sản phẩm có id > id gửi lên (biên min-1).
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"0"}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `business_rule:phan_trang_theo_id, field:pagination_id`

### TC-022 — Phân trang chấp nhận id kiểu number

**Category**: Edge Case

**D (Trường hợp)**: TH: Client gửi `id` dạng number thay vì string.

**E (Data test)**:
```
id=9 (number)
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": 9}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `Items` có id 10 → 15, `LastEvaluatedKey` = `{"id": "15"}` (giống kết quả khi gửi `"9"`).

**H/I**: LOW / MINOR

**Business Rule**: `id` phân trang nhận cả string và number.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:9}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:pagination_id`

### TC-023 — Phân trang thiếu tham số id báo lỗi

**Category**: Negative

**D (Trường hợp)**: TH: Body request không có field `id`.

**E (Data test)**:
```
body={}
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body `{"errorMessage": "Bad parameter, missing id"}`, không có field `Items`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: `id` là tham số bắt buộc của /pagination.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{}` của /pagination

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:pagination_id, validation_rule:pagination_id_required`

### TC-024 — Phân trang với id không phải số gây lỗi server

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi `id` là chuỗi chữ.

**E (Data test)**:
```
id="abc"
```

**F (Các bước)**:
1. Gửi `POST /pagination` body `{"id": "abc"}`.

**G (Kết quả mong muốn)**:
1. HTTP 500; body HTML chứa "Internal Server Error".

**H/I**: MEDIUM / MAJOR

**Business Rule**: Hành vi hiện tại của server với id không phải số (characterization).
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"abc"}` của /pagination

**Z (Notes)**: AMB-01 resolved: user chọn kỳ vọng đúng hành vi hiện tại (HTTP 500); nhiều khả năng là defect — khi server sửa thành 200 + errorMessage, cập nhật TC này.

**graph_refs**: `field:pagination_id, validation_rule:pagination_id_numeric`

## Chi tiết sản phẩm (POST /view)

### TC-025 — Xem chi tiết sản phẩm điện thoại

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng mở chi tiết sản phẩm Samsung galaxy s6 (id 1).

**E (Data test)**:
```
id="1"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "1"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body là object: `id = 1`, `cat = "phone"`, `title = "Samsung galaxy s6"`, `price = 360.0`, `img = "imgs/galaxy_s6.jpg"`, `desc` không rỗng.

**H/I**: HIGH / CRITICAL

**Business Rule**: /view trả object Product theo id.
**Source**: catalog/detail-design.md — "POST /view"; catalog/business-scenario.md — "Luồng nghiệp vụ: Chi tiết sản phẩm"; Postman `Phone CategoryPoductDetails`

**graph_refs**: `activity_flow:xem_catalog, field:view_id, business_rule:product_schema`

### TC-026 — Xem chi tiết sản phẩm laptop

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng mở chi tiết sản phẩm Sony vaio i5 (id 8).

**E (Data test)**:
```
id="8"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "8"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `id = 8`, `cat = "notebook"`, `title = "Sony vaio i5"`, `price = 790.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: /view trả object Product theo id.
**Source**: catalog/detail-design.md — "POST /view"; global-data.md §4 (`LaptopProductId` = 8); Postman `Laptops CategoryPoductDetails`

**graph_refs**: `field:view_id`

### TC-027 — Xem chi tiết sản phẩm màn hình

**Category**: Happy Path

**D (Trường hợp)**: TH: Người dùng mở chi tiết sản phẩm Apple monitor 24 (id 10).

**E (Data test)**:
```
id="10"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "10"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `id = 10`, `cat = "monitor"`, `title = "Apple monitor 24"`, `price = 400.0`.

**H/I**: HIGH / CRITICAL

**Business Rule**: /view trả object Product theo id.
**Source**: catalog/detail-design.md — "POST /view"; global-data.md §4 (`MonitorProductId` = 10); Postman `Monitors CategoryPoductDetails`

**graph_refs**: `field:view_id`

### TC-028 — Xem chi tiết sản phẩm có id lớn nhất

**Category**: Edge Case

**D (Trường hợp)**: TH: Xem sản phẩm có id lớn nhất trong catalog (id 15).

**E (Data test)**:
```
id="15"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "15"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `id = 15`, `cat = "notebook"`, `title = "MacBook Pro"`, `price = 1100.0`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BVA — biên max của id sản phẩm.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"15"}` của /view

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:view_id`

### TC-029 — Xem chi tiết với id kiểu number

**Category**: Edge Case

**D (Trường hợp)**: TH: Client gửi `id` dạng number.

**E (Data test)**:
```
id=1 (number)
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": 1}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body giống hệt kết quả khi gửi `{"id": "1"}` (`title = "Samsung galaxy s6"`, `price = 360.0`).

**H/I**: LOW / MINOR

**Business Rule**: `id` của /view nhận cả string và number.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:1}`

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:view_id`

### TC-030 — Xem chi tiết sản phẩm không tồn tại

**Category**: Negative

**D (Trường hợp)**: TH: id vượt id lớn nhất hoặc là số âm.

**E (Data test)**:
```
id="16"   → Not found.
id="-1"   → Not found.
id="9999" → Not found.
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "16"}`.
2. Gửi `POST /view` body `{"id": "-1"}`.
3. Gửi `POST /view` body `{"id": "9999"}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body `{"errorMessage": "Not found."}`.
2. HTTP 200; body `{"errorMessage": "Not found."}`.
3. HTTP 200; body `{"errorMessage": "Not found."}`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: BR-C4 — id không tồn tại trả `errorMessage: "Not found."` (HTTP 200).
**Source**: catalog/business-scenario.md — BR-C4; catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"16"} / {id:"-1"}`, "Scenario bổ sung đề xuất" (id 9999)

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:view_id, business_rule:view_not_found`

### TC-031 — Xem chi tiết thiếu tham số id báo lỗi

**Category**: Negative

**D (Trường hợp)**: TH: Body request không có field `id`.

**E (Data test)**:
```
body={}
```

**F (Các bước)**:
1. Gửi `POST /view` body `{}`.

**G (Kết quả mong muốn)**:
1. HTTP 200; body `{"errorMessage": "Product not found."}`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: Thiếu id → message "Product not found." (khác "Not found." của id không tồn tại).
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{}` của /view

**Z (Notes)**: AMB-02 resolved.

**graph_refs**: `field:view_id, validation_rule:view_id_required`

### TC-032 — Xem chi tiết với id 0 gây lỗi server

**Category**: Negative

**D (Trường hợp)**: TH: id bằng 0 (biên min-1, id nhỏ nhất = 1).

**E (Data test)**:
```
id="0"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "0"}`.

**G (Kết quả mong muốn)**:
1. HTTP 500; body HTML chứa "Internal Server Error".

**H/I**: MEDIUM / MAJOR

**Business Rule**: Hành vi hiện tại của server với id 0 (characterization).
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"abc"} / {id:"0"}` của /view

**Z (Notes)**: AMB-01 resolved: kỳ vọng đúng hành vi hiện tại (HTTP 500); khác với id âm/vượt max trả "Not found." — nhiều khả năng là defect.

**graph_refs**: `field:view_id, validation_rule:view_id_numeric`

### TC-033 — Xem chi tiết với id không phải số gây lỗi server

**Category**: Negative

**D (Trường hợp)**: TH: Client gửi `id` là chuỗi chữ.

**E (Data test)**:
```
id="abc"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "abc"}`.

**G (Kết quả mong muốn)**:
1. HTTP 500; body HTML chứa "Internal Server Error".

**H/I**: MEDIUM / MAJOR

**Business Rule**: Hành vi hiện tại của server với id không phải số (characterization).
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng `{id:"abc"}` của /view

**Z (Notes)**: AMB-01 resolved: kỳ vọng đúng hành vi hiện tại (HTTP 500).

**graph_refs**: `field:view_id, validation_rule:view_id_numeric`

### TC-034 — Chi tiết sản phẩm khớp dữ liệu trong danh sách danh mục

**Category**: Business Rule

**D (Trường hợp)**: TH: Đối chiếu dữ liệu `/view` với dữ liệu cùng sản phẩm trong `/bycat` cho toàn bộ 15 sản phẩm.

**E (Data test)**:
```
cat=phone    → id 1, 2, 3, 4, 5, 6, 7
cat=notebook → id 8, 9, 11, 12, 13, 15
cat=monitor  → id 10, 14
```

**F (Các bước)**:
1. Gửi `POST /bycat` cho từng `cat` ở E, lưu object của từng sản phẩm.
2. Với mỗi id đã lưu, gửi `POST /view` body `{"id": "<id>"}`.
3. So sánh object `/view` với object cùng id lấy từ `/bycat`.

**G (Kết quả mong muốn)**:
1. HTTP 200 cho cả 3 request; tổng 15 sản phẩm.
2. HTTP 200 cho cả 15 request, không response nào có `errorMessage`.
3. 6 field `cat, desc, id, img, price, title` giống hệt nhau cho cả 15 sản phẩm.

**H/I**: HIGH / CRITICAL

**Business Rule**: BR-C2 — dữ liệu Product nhất quán giữa các endpoint.
**Source**: catalog/business-scenario.md — BR-C2; global-data.md §5 (bảng catalog)

**Z (Notes)**: Số lượng/id theo snapshot catalog 2026-10-21 (global-data.md §5); dữ liệu public có thể thay đổi — cập nhật E/G khi catalog đổi.

**graph_refs**: `business_rule:product_schema, field:view_id, field:bycat_cat`

### TC-035 — Tiêu đề sản phẩm id 9 chứa ký tự xuống dòng cuối chuỗi

**Category**: Edge Case

**D (Trường hợp)**: TH: Dữ liệu `title` của sản phẩm id 9 có ký tự `\n` thừa ở cuối.

**E (Data test)**:
```
id="9"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "9"}`.
2. So sánh `title` gốc và `title` sau khi trim.

**G (Kết quả mong muốn)**:
1. HTTP 200; `cat = "notebook"`, `price = 790.0`.
2. `title` gốc = `"Sony vaio i7\n"`; sau trim = `"Sony vaio i7"`.

**H/I**: LOW / MINOR

**Business Rule**: Dữ liệu thật có ký tự thừa — so sánh title phải trim.
**Source**: global-data.md §5 (id 9 — title có ký tự xuống dòng cuối); catalog/detail-design.md — "Data entities" (PRODUCT.title), "Lưu ý automation"

**graph_refs**: `field:product_title`

## Tài nguyên tĩnh (About us / ảnh sản phẩm)

### TC-036 — Ảnh sản phẩm từ field img tải được trên host www**Category**: Business Rule

**D (Trường hợp)**: TH: Dựng URL ảnh từ field `img` (đường dẫn tương đối) của sản phẩm id 1.

**E (Data test)**:
```
id="1"
```

**F (Các bước)**:
1. Gửi `POST /view` body `{"id": "1"}`, lấy giá trị `img`.
2. Gửi `GET https://www.demoblaze.com/<img>`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `img = "imgs/galaxy_s6.jpg"`.
2. HTTP 200; `Content-Type: image/jpeg`.

**H/I**: MEDIUM / MAJOR

**Business Rule**: `img` là đường dẫn tương đối, host là `www.demoblaze.com`.
**Source**: catalog/detail-design.md — "Data entities" (PRODUCT.img); global-data.md §1 (Ảnh sản phẩm)

**graph_refs**: `field:product_img`

### TC-037 — Ảnh About us không phục vụ trên host api

**Category**: Negative

**D (Trường hợp)**: TH: Gọi ảnh `front.jpg` của trang About us qua host api (như request Postman `About us link`).

**F (Các bước)**:
1. Gửi `GET https://api.demoblaze.com/imgs/front.jpg`.

**G (Kết quả mong muốn)**:
1. HTTP 404; header `Content-Type` có giá trị (`text/html`).

**H/I**: LOW / MINOR

**Business Rule**: Tài nguyên tĩnh không phục vụ trên host api.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng static api; catalog/business-scenario.md — TC #11 (Postman `About us link`)

**Z (Notes)**: AMB-03 resolved: giữ TC About us, bỏ Previous/Contact.

**graph_refs**: `ui_screen:about_us`

### TC-038 — Ảnh About us tải được trên host www

**Category**: Happy Path

**D (Trường hợp)**: TH: Gọi ảnh `front.jpg` qua host web.

**F (Các bước)**:
1. Gửi `GET https://www.demoblaze.com/imgs/front.jpg`.

**G (Kết quả mong muốn)**:
1. HTTP 200; `Content-Type: image/jpeg`; body không rỗng.

**H/I**: LOW / MINOR

**Business Rule**: Ảnh tĩnh phục vụ từ host www.
**Source**: catalog/detail-design.md — "Hành vi biên & lỗi" dòng static www; global-data.md §1

**Z (Notes)**: AMB-03 resolved.

**graph_refs**: `ui_screen:about_us`

