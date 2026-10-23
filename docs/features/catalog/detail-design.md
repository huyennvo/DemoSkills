# Detail Design — Catalog

## API

### GET /entries
- **Response:** `{ "Items": [Product × 9], "LastEvaluatedKey": { "id": "9" } }` (đã xác minh)
- Lưu ý: mô tả trong Postman ghi response `entries[{id,title,content}]` — **sai**, thực tế là `Items` như trên.

### POST /bycat
- **Request:** `{ "cat": "phone" | "notebook" | "monitor" }`
- **Response:** `{ "Items": [Product] }` — phone 7, notebook 6, monitor 2 (snapshot 2026-10-21)

### POST /pagination
- **Request:** `{ "id": string }` — id cuối của trang hiện tại
- **Response:** `{ "Items": [Product], "LastEvaluatedKey": { "id": string }, "ScannedCount": number }`
- Ví dụ `{id:"9"}` → id 10–15, `LastEvaluatedKey.id = "15"`, `ScannedCount = 6` (đã xác minh)

### POST /view
- **Request:** `{ "id": string | number }`
- **Response:** `Product` — id không tồn tại → `{"errorMessage":"Not found."}` (đã xác minh)

## Hành vi biên & lỗi (xác minh bằng request thật 2026-10-21)

| Endpoint | Request | HTTP | Response |
|---|---|---|---|
| /bycat | `{cat:"tablet"}` (ngoài enum) | 200 | `{"Items":[]}` |
| /bycat | `{cat:""}` | 200 | `{"Items":[]}` |
| /bycat | `{cat:"Phone"}` / `{cat:"PHONE"}` (sai hoa thường) | 200 | `{"Items":[]}` — cat phân biệt hoa thường |
| /bycat | `{}` (thiếu cat) | 200 | `{"errorMessage":"Bad parameter, missing cat."}` |
| /bycat | `GET` | 405 | HTML "Method Not Allowed" |
| /bycat | POST không header `Content-Type: application/json` | 415 | HTML "Unsupported Media Type" |
| /bycat | body JSON sai cú pháp (`{cat:`) | 400 | HTML "Bad Request" |
| /entries | `POST` | 405 | HTML "Method Not Allowed" |
| /pagination | `{id:"14"}` | 200 | 1 item (id 15), `LastEvaluatedKey.id="15"`, `ScannedCount=1` |
| /pagination | `{id:"15"}` (id cuối) | 200 | `{"Items":[],"LastEvaluatedKey":{"id":"None"},"ScannedCount":0}` |
| /pagination | `{id:"0"}` | 200 | 9 item id 1–9 (giống hệt `/entries`), `LastEvaluatedKey.id="9"`, `ScannedCount=9` |
| /pagination | `{id:9}` (number) | 200 | giống `{id:"9"}` |
| /pagination | `{}` (thiếu id) | 200 | `{"errorMessage":"Bad parameter, missing id"}` |
| /pagination | `{id:"abc"}` | **500** | HTML "Internal Server Error" |
| /view | `{id:1}` (number) | 200 | Product id 1 |
| /view | `{id:"15"}` (id lớn nhất) | 200 | MacBook Pro, 1100.0 |
| /view | `{id:"16"}` / `{id:"-1"}` | 200 | `{"errorMessage":"Not found."}` |
| /view | `{}` (thiếu id) | 200 | `{"errorMessage":"Product not found."}` (khác message với id không tồn tại) |
| /view | `{id:"abc"}` / `{id:"0"}` | **500** | HTML "Internal Server Error" |
| (static) | `GET https://api.demoblaze.com/imgs/front.jpg` | 404 | HTML |
| (static) | `GET https://www.demoblaze.com/imgs/front.jpg` | 200 | `image/jpeg` |

> Các case 500 là hành vi hiện tại của server (nhiều khả năng là defect) — TC đang kiểm chứng đúng hành vi này (characterization), xem `doc-gaps.md` AMB-01.

## Data entities
* **PRODUCT:** `id` (number), `cat` (string: phone/notebook/monitor), `title` (string — có thể có `\n` cuối, vd id 9), `price` (number, float), `desc` (string), `img` (string tương đối, host `www.demoblaze.com`).

## JSON schema Product (dùng validate)
```json
{
  "type": "object",
  "required": ["cat", "desc", "id", "img", "price", "title"],
  "properties": {
    "cat": {"type": "string", "enum": ["phone", "notebook", "monitor"]},
    "desc": {"type": "string"},
    "id": {"type": "number"},
    "img": {"type": "string"},
    "price": {"type": "number"},
    "title": {"type": "string"}
  }
}
```

## Lưu ý automation
- So sánh `title` nên `strip()` (id 9 có `\n`).
- About/Contact không có API → chỉ test ở tầng web nếu cần.
