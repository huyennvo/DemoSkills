# Business Scenario — Gói ưu đãi: kiểm tra gói đang sử dụng (ViettelMoney Android)

> Cập nhật 2026-10-22 (khám phá trên thiết bị thật SM-A725F / Android 14): app cài trên máy test là **`8.8.62-DEBUG`** (versionCode 1885), không phải bản store. Xem các mục "Chi tiết gói chưa dùng" và "Nút Trợ giúp".
>
> Nguồn: khảo sát app bản store `com.bplus.vtpay` 8.8.62 ngày 2026-10-22, tài khoản test (`APP_USERNAME`) đang dùng **Gói Standard**. Phần đánh dấu *(chưa verify)* là suy luận, cần xác nhận với BA/PO.

## Luồng nghiệp vụ end-to-end: Login → Cá nhân → Gói ưu đãi
1. Mở app, chờ màn loading xong.
2. Đăng nhập: nhập SĐT → **Tiếp tục** → nhập mật khẩu 6 số, đủ 6 số là app tự đăng nhập (xem `features/auth/login/business-scenario.md`).
3. Vào Home (`Chào <Tên>`).
4. Bấm **Cá nhân** ở bottom bar.
5. Màn Cá nhân: bấm mục **Gói ưu đãi** (badge `Mới`, mô tả `Gia tăng ưu đãi, miễn phí chuyển tiền`). Mục này nằm ngay đầu danh sách, không cần cuộn.
6. Màn **Gói ưu đãi** hiện danh sách thẻ gói. **Gói đang dùng nằm đầu danh sách**:
   - Tên: `Gói Standard`
   - Trạng thái: chấm xanh + `Đang sử dụng` + `>`
   - Giá: `Miễn phí`
   - Quyền lợi tóm tắt: `Ưu đãi 2% nạp điện thoại/thanh toán cước từ nguồn Tiền di động.` / `Ưu đãi 5% khi mua data từ nguồn Tiền di động.`
   - **Không có** nút `Đăng ký` / `Xem chi tiết`.
7. **Kết quả mong đợi:** thẻ `Gói Standard` có trạng thái `Đang sử dụng`. Các gói khác không có trạng thái này.

## Danh sách gói quan sát được (thứ tự trên màn hình)
| # | Gói | Giá | Chu kỳ | Trạng thái với tài khoản test | Nút |
|---|---|---|---|---|---|
| 1 | Gói Standard | Miễn phí | — | **Đang sử dụng** | — (tap thẻ → trang chi tiết) |
| 2 | Gói Start | 15.000đ | /30 ngày | — | Đăng ký, Xem chi tiết |
| 3 | Gói Smart | 25.000đ *(đã đối chiếu lại 2026-10-22)* | /30 ngày | — | Đăng ký, Xem chi tiết |
| 4 | Gói Super | 50.000đ | /30 ngày | — | Đăng ký, Xem chi tiết |
| 5 | Gói Plus CBNV Viettel | 5.500đ | /30 ngày | — | Đăng ký, Xem chi tiết |
| 6 | Gói Premium CBNV Viettel | 16.500đ | /30 ngày | — | Đăng ký, Xem chi tiết |
| 7 | Gói Plus | 5.500đ | /30 ngày | — | Đăng ký, Xem chi tiết |
| 8 | Gói Premium | 16.500đ | /30 ngày | — | Đăng ký, Xem chi tiết |

> Danh sách gói, giá và quyền lợi do backend cấu hình, có thể thay đổi theo thời kỳ hoặc đối tượng (vd gói CBNV Viettel). Test chỉ nên assert cứng **gói đang dùng + trạng thái**; danh sách gói khác chỉ dùng tham khảo.

## Trang chi tiết Gói Standard (tap vào thẻ gói đang dùng)
- Tiêu đề `Gói Standard`, `Giá gói` = `Miễn phí`.
- **Ưu đãi viễn thông:**
  - 2% khi nạp tiền điện thoại Viettel từ nguồn Tiền di động
  - 2% khi thanh toán cước di động Viettel từ nguồn Tiền di động
  - 5% khi mua mới data từ nguồn Tiền di động, các nguồn khác ưu đãi 3%
  - Khuyến mãi 2% khi thanh toán cước internet, truyền hình Viettel từ nguồn Tiền di động
  - Khuyến mãi 1% khi mua thẻ cào, thanh toán cước điện thoại cố định, Homephone Viettel từ nguồn Tiền di động
- **Ghi chú:** ưu đãi chuyển tiền và chính sách khác áp dụng theo chính sách chung từng thời kỳ, có link `Tại đây`.
- Nút **Đăng ký gói ưu đãi** vẫn hiện, kể cả khi gói này đang dùng *(chưa verify hành vi khi bấm, KHÔNG bấm khi chạy test)*.

## Chi tiết gói chưa dùng (bấm `Xem chi tiết`) — khảo sát 2026-10-22, bản 8.8.62-DEBUG
- Mở dạng **bottom sheet** chồng lên danh sách (khác trang chi tiết Gói Standard là trang riêng có `←`).
- Ví dụ Gói Start: tiêu đề `Gói Start`, `Giá gói` = `15.000đ/30 ngày`, dòng thời hạn `Từ <hôm nay> đến hết <hôm nay + 29 ngày>` (vd `Từ 22/10/2026 đến hết 20/11/2026`).
- Quyền lợi Gói Start: thêm 15 giao dịch chuyển tiền liên ngân hàng miễn phí trong 30 ngày (ngoài ưu đãi của gói Standard); 20% nạp ĐT/thanh toán cước di động Viettel cho chính mình (tối đa 15.000đ); 15% mua data Viettel (tối đa 15.000đ, không gồm Roaming); SMS biến động số dư không giới hạn.
- **Ghi chú (trả lời một phần Q-P1, Q-P2):** không áp dụng với giao dịch thanh toán tự động; **tự động gia hạn gói khi hết chu kỳ**, nguồn tiền gia hạn gồm ViettelPay, Tiền di động, ngân hàng liên kết trực tiếp với Viettel Money; muốn dừng gia hạn thì **Hủy gia hạn trong mục Quản lý gói**.
- Nút `Đăng ký gói ưu đãi` ở cuối sheet (**không bấm**).
- Back hệ thống đóng sheet, về danh sách.

## Nút Trợ giúp (?) — khảo sát 2026-10-22
Bấm `?` trên màn Gói ưu đãi → mở **Trung tâm hỗ trợ**, có ô tìm kiếm (bàn phím tự bật) và nhóm câu hỏi thường gặp `Gói ưu đãi` (sản phẩm là gì, khác gì gói tài khoản cũ, đổi gói có cần huỷ gói cũ, chu kỳ gia hạn & huỷ gia hạn tự động, đăng ký nhầm gói...). Back → về màn Gói ưu đãi.

## Khảo sát bổ sung 2026-10-22 (đợt 2)
- **Bấm thân thẻ gói chưa dùng** (vd tên `Gói Start`) → mở cùng bottom sheet chi tiết như nút `Xem chi tiết`.
- **Link `Tại đây`** trong Ghi chú trang chi tiết Gói Standard → mở trình duyệt ngoài tới `https://viettelmoney.vn/` (rời app).
- **Cuối trang chi tiết Gói Standard** có ảnh hộp quà + câu `Đăng ký gói ưu đãi để nhận thêm ưu đãi hấp dẫn từ Viettel Money`.
- **Loading:** khi mở màn Gói ưu đãi, app hiện lớp phủ xám + icon app có vòng xoay cho tới khi tải xong danh sách (thường ~3–4s; mạng chậm có thể lâu hơn nhiều).
- **Mất mạng:** popup `Thông báo` — `Bạn vui lòng đợi vài phút trước khi thử lại nhé.` — nút `Đóng`. Đóng xong vẫn ở màn Gói ưu đãi, danh sách trống, không có nút thử lại; phải Back ra rồi vào lại.

### Câu trả lời từ FAQ trong Trung tâm hỗ trợ (đọc trong app, 2026-10-22)
| Câu hỏi mở | Trả lời theo FAQ |
|---|---|
| Q-P1 (nguồn trừ tiền) | Ghi chú gói trả phí: gia hạn trừ từ ViettelPay, Tiền di động, ngân hàng liên kết trực tiếp. Hành vi khi **không đủ số dư** lúc đăng ký: FAQ không nêu. |
| Q-P2 (gia hạn) | Gói hiệu lực 30 ngày, **tự động gia hạn** vào ngày đầu chu kỳ mới (hệ thống quét và trừ phí). Gia hạn thất bại (hết tiền, khoá TK...) → gói chuyển **tạm dừng**, chờ khôi phục tối đa **90 ngày**, quá hạn thì huỷ; trong thời gian tạm dừng không được hưởng ưu đãi; mua gói mới khi đang tạm dừng thì gói cũ không được khôi phục. Huỷ gia hạn: nút `Hủy gia hạn tự động` trong FAQ / mục `Quản lý gói`. |
| Q-P3 (đổi gói) | Không cần huỷ gói cũ — đăng ký gói mới thành công thì hệ thống tự huỷ gói cũ. **Hạn mức ưu đãi gói cũ không được bảo lưu**; áp dụng ngay chính sách và chu kỳ gói mới. Tại 1 thời điểm chỉ duy trì 1 gói (ngoài gói mặc định Standard). Tối đa **5 lần đăng ký/gói/chu kỳ**. Đăng ký nhầm: **không hỗ trợ huỷ và hoàn tiền**. |
| Q-P4 (điều kiện gói CBNV) | FAQ không có câu nào — vẫn mở. |
| Q-P5 (nút `Đăng ký gói ưu đãi` trên gói đang dùng) | Chưa verify — không bấm trên tài khoản thật (đăng ký nhầm không được hoàn tiền). |
| Q-P6 (môi trường UAT) | Thiết bị không trả lời được — vẫn mở. |

## Business rules / điểm cần kiểm thử
| # | Rule | Nguồn |
|---|---|---|
| BR-P1 | Mỗi tài khoản có đúng 1 gói ở trạng thái `Đang sử dụng` | Quan sát (chỉ 1 `tv_status` trong cả danh sách) |
| BR-P2 | Gói đang dùng hiện ở đầu danh sách | Quan sát |
| BR-P3 | Thẻ gói đang dùng không có nút `Đăng ký` | Quan sát |
| BR-P4 | Gói chưa dùng có `Đăng ký` + `Xem chi tiết`, giá dạng `<số>đ` + `/30 ngày` | Quan sát |
| BR-P5 | Tài khoản test mặc định dùng Gói Standard (miễn phí) | Quan sát |
| BR-P6 | Đăng ký gói trả phí sẽ trừ tiền thật → **ngoài phạm vi automation** trên tài khoản production | Ràng buộc môi trường |

## Câu hỏi mở (cần BA/PO xác nhận)
Các nghiệp vụ dưới đây chưa khảo sát được vì tài khoản test là tài khoản thật. Chưa có thông tin thì chưa viết test cho các luồng này.
| # | Câu hỏi | Ảnh hưởng tới test |
|---|---|---|
| Q-P1 | Đăng ký gói trả phí trừ tiền từ nguồn nào (Tiền di động / tài khoản liên kết)? Không đủ số dư thì báo gì? | Luồng đăng ký + negative case |
| Q-P2 | Gói có tự gia hạn sau 30 ngày không? Huỷ gia hạn ở đâu? | Luồng huỷ/gia hạn |
| Q-P3 | Đổi từ gói trả phí sang gói khác giữa chu kỳ thì xử lý thế nào (hiệu lực ngay, hoàn tiền)? | Luồng đổi gói |
| Q-P4 | Điều kiện để thấy/đăng ký gói CBNV Viettel? Tài khoản thường bấm `Đăng ký` gói CBNV thì sao? | Rule hiển thị theo đối tượng |
| Q-P5 | Bấm `Đăng ký gói ưu đãi` ở trang chi tiết của gói đang dùng thì có gì xảy ra? | Edge case |
| Q-P6 | Có môi trường UAT/staging và tài khoản test riêng để đi luồng đăng ký gói trả phí không? | Mở rộng phạm vi automation (bỏ BR-P6) |

## Kịch bản kiểm thử gợi ý
- **TC01 (chính):** Login → Cá nhân → Gói ưu đãi → thẻ đầu tiên có `tvSubName` = `Gói Standard` và `tv_status` = `Đang sử dụng`.
- TC02: Thẻ Gói Standard có giá `Miễn phí` và không có nút `Đăng ký`.
- TC03: Trong toàn bộ danh sách chỉ có 1 phần tử `tv_status` (cuộn hết danh sách để kiểm).
- TC04: Tap thẻ Gói Standard → trang chi tiết có tiêu đề `Gói Standard`, `Giá gói` = `Miễn phí`. Back → về danh sách.
- TC05: Bấm Back ở màn Gói ưu đãi → về màn Cá nhân.
