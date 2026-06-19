# BÁO CÁO ĐÁNH GIÁ API CONTRACT — ERP BÌNH NGUYỄN
**Người thực hiện:** Solution Architect & API QA Engineer  
**Ngày đánh giá:** 19/06/2026  
**Phạm vi:** 13 module API (file 01–13), đối chiếu với `database.md`, `ERD.md`, `documents.md`

---

## I. TÓM TẮT TỔNG QUAN

| Hạng mục | Điểm | Nhận xét |
|---|---|---|
| Độ chính xác & đồng bộ dữ liệu | 6.5/10 | Một số field DB thiếu hoặc sai kiểu trong Response |
| Độ đầy đủ theo nghiệp vụ | 6.0/10 | Nhiều bảng DB và entity ERD chưa có API tương ứng |
| Tính toàn vẹn quan hệ | 6.5/10 | Một số FK bắt buộc chưa được truyền trong Request body |
| Thiết kế RESTful | 7.5/10 | Về cơ bản đúng chuẩn, một số path chưa tối ưu |
| **TỔNG ĐIỂM** | **6.6/10** | **Cần xử lý 8 lỗi Critical và 15 lỗi Major trước khi triển khai** |

---

## II. BẢNG CHI TIẾT LỖI PHÁT HIỆN

### NHÓM A — ĐỘ CHÍNH XÁC & ĐỒNG BỘ DỮ LIỆU

| Mã Lỗi | Vị trí / API Path | Mô tả chi tiết | Tài liệu tham chiếu | Gợi ý sửa đổi |
|---|---|---|---|---|
| **A-01** 🔴 Critical | `POST /supplier-payables` (04-suppliers.md) | `transaction_type` trong Request body dùng giá trị `"purchase"`, nhưng DB (`supplier_payables.transaction_type`) chỉ định nghĩa enum `purchase | return | adjustment`. **Không có giá trị `rental`** — UC-71 (thuê thiết bị NCC) hiện không thể phân biệt với mua hàng thông thường. | database.md — Bảng 15 `supplier_payables` | Thêm giá trị `rental` vào ENUM `transaction_type` trong DB, hoặc tạo endpoint riêng `POST /supplier-rentals` cho UC-71. |
| **A-02** 🔴 Critical | `GET /inventory/availability` (05-warehouse-inventory.md) | Response trả về `quantity_total` và `available`, nhưng DB bảng `inventory` có 4 trường riêng: `quantity_total`, `quantity_available`, `quantity_reserved`, `quantity_damaged`. API gộp logic tính toán mà không làm rõ công thức `available = quantity_total - reserved_on_date`, có thể gây nhầm lẫn với `quantity_available` sẵn có trong DB. | database.md — Bảng 13 `inventory` | Đổi tên field trong Response thành `quantity_available_today` và giải thích rõ: đây là kết quả kiểm tra theo ngày cụ thể, khác với `inventory.quantity_available` là trạng thái real-time. |
| **A-03** 🟠 Major | `POST /auth/login` (01-auth.md) | `device_type` trong Request body nhận giá trị `"web"`, nhưng DB bảng `user_devices.device_type` chỉ có ENUM `android | web`. Trường này ổn với web, nhưng với mobile app (iOS) thì không có giá trị phù hợp. | database.md — Bảng 5 `user_devices` | Bổ sung `ios` vào ENUM `device_type` trong DB. API Contract cần liệt kê rõ các giá trị hợp lệ: `"android" | "web" | "ios"`. |
| **A-04** 🟠 Major | `PUT /business-policies/{code}` (06-policies-wage.md) | DB `business_policies.policy_value` có kiểu `DECIMAL(15,2)`, nhưng Response example trả về số nguyên `40` thay vì `40.00`. Mặc dù JSON không phân biệt, cần nhất quán để Frontend xử lý đúng. | database.md — Bảng 6 `business_policies` | Định nghĩa rõ `policy_value` luôn là `number` kiểu float trong contract. Ví dụ Response dùng `40.00` thay vì `40`. |
| **A-05** 🟠 Major | `GET /orders/{id}` (09-orders.md) | Response thiếu các trường `updated_by`, `updated_at` dù DB bảng `orders` có cả hai FK `created_by`, `updated_by`. Không nhất quán với các module khác (customer, user đều trả về `updated_at`). | database.md — Bảng 18 `orders` | Bổ sung `updated_by` và `updated_at` vào Response của `GET /orders/{id}`. |
| **A-06** 🟡 Minor | `GET /notifications` (01-auth.md) | DB `notifications.is_read` có kiểu `TINYINT(1)`, nhưng Response trả về `"is_read": false` (boolean). Cần xác nhận ORM/driver có tự chuyển đổi không. | database.md — Bảng 44 `notifications` | Ghi chú rõ trong contract: backend tự map `TINYINT(1)` → `boolean` trong JSON response. Nếu không, phải convert thủ công. |
| **A-07** 🟡 Minor | `POST /catalog-items/{id}/prices` (03-catalog.md) | `valid_from` trong Request body dùng định dạng ISO-8601 có giờ (`"2026-07-01T00:00:00Z"`), nhưng DB `item_price_history.valid_from` có kiểu `DATETIME` (không phải `TIMESTAMP`). Cần đảm bảo backend chuẩn hóa timezone. | database.md — Bảng 11 `item_price_history` | Quy ước chung (README §A.6) nên bổ sung: mọi field kiểu `DATETIME` trong DB sẽ serialize thành ISO-8601 UTC trong API. |
| **A-08** 🟡 Minor | `GET /customers` (07-customers.md) | Response không có `updated_by` dù DB `customers` có cả `created_by` và `updated_by` là NOT NULL FK. Thiếu thông tin kiểm toán quan trọng. | database.md — Bảng 7 `customers` | Bổ sung `updated_by` (hoặc object `{ id, full_name }`) vào Response của `GET /customers/{id}`. |

---

### NHÓM B — ĐỘ ĐẦY ĐỦ THEO NGHIỆP VỤ

| Mã Lỗi | Vị trí / API Path | Mô tả chi tiết | Tài liệu tham chiếu | Gợi ý sửa đổi |
|---|---|---|---|---|
| **B-01** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API nào cho `order_item`** — ERD E14 định nghĩa entity `Order Item` (catalog_item + quantity + giá trong đơn hàng), DB không có bảng riêng nhưng ERD và documents.md đề cập rõ. Hiện tại đơn hàng được tạo qua `POST /orders` chỉ có metadata sự kiện, chưa có cách gắn hạng mục vào đơn. | ERD.md — E14 Order Item; documents.md §3 | Nếu hàng hóa chỉ đưa vào qua báo giá thì cần note rõ trong contract. Nếu Order có danh sách hạng mục riêng, thêm `POST /orders/{id}/items` và `GET /orders/{id}/items`. |
| **B-02** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API nào cho `Order Status History`** — UC-55 (`GET /orders/{id}/status-history`) đọc tạm từ `audit_logs` thay vì bảng chuyên biệt. ERD E20 và documents.md đề cập `Order Status History` là entity riêng. | ERD.md — E20; 09-orders.md ⚠️ | Tạo bảng `order_status_history` trong DB, sau đó `GET /orders/{id}/status-history` đọc từ bảng này thay vì audit_logs. |
| **B-03** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API cho `Order Cancellation` entity** — UC-60 (`POST /orders/{id}/cancel`) hiện chỉ cập nhật field `status` trên bảng `orders`, không lưu `refund_amount`, `policy_applied`, lý do hủy chi tiết. ERD E19 định nghĩa đây là entity riêng với nhiều thuộc tính. | ERD.md — E19; documents.md §3 | Tạo bảng `order_cancellations` trong DB. API `POST /orders/{id}/cancel` nên tạo record trong bảng này và trả về `refund_amount` sau khi áp dụng chính sách. |
| **B-04** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API cho `Order Date Change` entity** — UC-59 (`POST /orders/{id}/change-date`) chỉ cập nhật `event_date` trên `orders`, không lưu lịch sử đổi ngày. ERD E18 định nghĩa entity riêng để lưu từng lần đổi ngày. | ERD.md — E18; 09-orders.md ⚠️ | Tạo bảng `order_date_changes`. API response nên trả về `{ old_date, new_date, reason, changed_at }`. |
| **B-05** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API cho `Order Schedule` (E21)** — UC-76 `POST /orders/{id}/schedules` bị đánh dấu ⚠️ vì thiếu bảng DB. ERD nguyên tắc 16 khẳng định cần `Order Schedule` để quản lý mốc lịch vận hành. | ERD.md — E21; 10-survey-assignment.md ⚠️ | Tạo bảng `order_schedules` trong DB với các cột: `order_id`, `schedule_type` (survey/checkout/transport/install/return), `scheduled_at`, `notes`. Sau đó implement đầy đủ `POST /orders/{id}/schedules`. |
| **B-06** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API cho `Task Progress Update` (E25)** — UC-79 `GET /orders/{id}/progress` chỉ đọc tạm từ `tasks.status`, không có lịch sử cập nhật tiến độ, ảnh bằng chứng theo từng mốc. | ERD.md — E25; 10-survey-assignment.md ⚠️ | Tạo bảng `task_progress_updates`. `PATCH /tasks/{id}/progress` (UC-95) nên tạo record mới thay vì ghi đè. `GET /orders/{id}/progress` đọc từ bảng này. |
| **B-07** 🔴 Critical | *(Thiếu hoàn toàn)* | **Không có API cho `Change Request Item` (E36)** — `POST /change-requests` (UC-97) chỉ có `description` text tự do, không có cấu trúc itemize. DB `change_requests` không có bảng con `change_request_items`. | ERD.md — E36; documents.md §3 | Tạo bảng `change_request_items` (change_request_id, catalog_item_id, quantity, change_type). Request body `POST /orders/{id}/change-requests` bổ sung field `items: []`. |
| **B-08** 🟠 Major | `POST /roles/{id}/permissions` (02-users-roles.md) | Không có API để **xóa user khỏi role** hoặc **xem user theo role**. Documents mô tả Admin cần giám sát được ai đang thuộc role nào. | documents.md — UC-13, UC-14 | Bổ sung `GET /roles/{id}/users` để Admin xem danh sách user thuộc một role. |
| **B-09** 🟠 Major | `GET /reports/inventory` (13-reports.md) | Báo cáo tồn kho chỉ trả về số lượng "theo tình trạng" chung chung, không có thông tin `quantity_available`, `quantity_reserved`, `quantity_damaged` cụ thể theo từng item. | database.md — Bảng 13 `inventory`; UC-42 | Bổ sung breakdown đầy đủ theo 4 trạng thái: `available | reserved | damaged | total` cho từng `catalog_item`. |
| **B-10** 🟠 Major | *(Thiếu)* | **Không có API `GET /orders/{id}/settlement`** dù được liệt kê trong bảng endpoint của `11-payments-settlement.md` và có entity `settlements` trong DB. | 11-payments-settlement.md; database.md — Bảng 24 `settlements` | Implement `GET /orders/{id}/settlement` trả về toàn bộ thông tin quyết toán gồm cả `settlement_lines`. |
| **B-11** 🟠 Major | `UC-6` (Toàn bộ API contract) | **UC-6 "Cập nhật hồ sơ cá nhân" hoàn toàn vắng mặt** — documents.md liệt kê UC-6 nhưng không có endpoint `PUT /me` hoặc tương tự trong file 01-auth.md. | documents.md §5 — UC-6 | Thêm endpoint `PUT /me` cho phép user cập nhật `full_name`, `email`, `phone`. |
| **B-12** 🟠 Major | *(Thiếu validation)* | **Không có validation cho `event_date` phải là ngày trong tương lai** khi tạo đơn hàng (`POST /orders`). Documents quy định không được tạo đơn cho ngày đã qua. | documents.md §3 — Event Date | Bổ sung lỗi `400 MSG-CO-04`: `event_date` phải lớn hơn ngày hiện tại. |
| **B-13** 🟠 Major | `POST /users` (02-users-roles.md) | Request body không có validation rõ ràng về **format email** và **độ dài/format số điện thoại**, dù DB quy định `phone VARCHAR(20)` UNIQUE. | database.md — Bảng 4 `users` | Bổ sung validation rule: `email` phải đúng format RFC 5322; `phone` phải là chuỗi 10–12 ký số. |
| **B-14** 🟡 Minor | `GET /catalog-items` (03-catalog.md) | Contract ghi rõ `current_price` lấy từ `item_price_history` có `valid_to = NULL`, nhưng không có lỗi xử lý trường hợp **không có giá nào** (`current_price = null`). | database.md — Bảng 11 `item_price_history` | Ghi rõ trong contract: nếu item chưa được thiết lập giá, `current_price` trả về `null`. Frontend phải xử lý case này khi hiển thị. |
| **B-15** 🟡 Minor | `PUT /quotations/{id}` (08-quotations.md) | Không có validation phòng trường hợp `discount_amount` lớn hơn `total_amount` (giá sau chiết khấu âm). | database.md — Bảng 20 `quotations` | Bổ sung lỗi `400 MSG-UQ-04`: `discount_amount` không được lớn hơn `total_amount`. |

---

### NHÓM C — TÍNH TOÀN VẸN QUAN HỆ (FK & JOIN)

| Mã Lỗi | Vị trí / API Path | Mô tả chi tiết | Tài liệu tham chiếu | Gợi ý sửa đổi |
|---|---|---|---|---|
| **C-01** 🔴 Critical | `POST /supplier-payables` (04-suppliers.md) | Request body **thiếu `order_id`** — DB bảng `supplier_payables` không có `order_id` trực tiếp, nhưng ERD (R49) chỉ rõ `Supplier Transaction` phải gắn với `Order`. Nếu không lưu liên kết order–supplier payable, không thể báo cáo công nợ NCC theo đơn hàng. | ERD.md — R49; database.md — Bảng 15 | Bổ sung `order_id` (nullable) vào bảng `supplier_payables` và Request body của endpoint này. |
| **C-02** 🟠 Major | `POST /orders/{id}/assignments` (10-survey-assignment.md) | Response trả về `{ id, order_id, user_id, status }` nhưng thiếu `assigned_date`, `session_type` — là hai trường NOT NULL trong DB `assignments`. Nếu client phụ thuộc vào Response để hiển thị ngay, sẽ thiếu dữ liệu. | database.md — Bảng 28 `assignments` | Bổ sung `assigned_date` và `session_type` vào Response của `POST /orders/{id}/assignments`. |
| **C-03** 🟠 Major | `POST /orders/{id}/pick-lists` (05-warehouse-inventory.md) | Khi tạo phiếu xuất kho, Response trả về `{ id, order_id, assignment_id, status }` nhưng **không có `warehouse_id`**. DB `pick_lists` không có `warehouse_id` trực tiếp, nhưng cần biết xuất từ kho nào khi hệ thống hỗ trợ đa kho. | database.md — Bảng 30 `pick_lists`; ERD — E26 | Bổ sung `warehouse_id` (với giá trị default là kho chính) vào Request và Response. |
| **C-04** 🟠 Major | `GET /orders/{id}/return-status` (05-warehouse-inventory.md) | Response thiếu liên kết với `pick_list_id` — không thể biết trạng thái hoàn trả này thuộc về phiếu xuất kho nào (một đơn có thể có nhiều phiếu xuất). | database.md — Quan hệ `orders → pick_lists → pick_list_items` | Bổ sung `pick_list_id` vào từng item trong Response. |
| **C-05** 🟠 Major | `POST /payments/vnpay/callback` (11-payments-settlement.md) | Callback VNPay không có cơ chế xác minh tính toàn vẹn — contract chỉ nói "xác thực bằng chữ ký" nhưng không định nghĩa field `vnp_SecureHash`, thuật toán, key source. Thiếu FK `payment_id` hoặc `order_id` trong payload mô tả để backend có thể map về đúng record. | 11-payments-settlement.md ⚠️; VNPay docs | Bổ sung đầy đủ payload VNPay IPN vào contract: `vnp_TxnRef`, `vnp_Amount`, `vnp_ResponseCode`, `vnp_SecureHash`. Ghi rõ thuật toán HMAC-SHA512 và cách verify. |
| **C-06** 🟡 Minor | `GET /quotations/{id}` (08-quotations.md) | Chi tiết báo cáo báo giá không trả về `created_by` (user tạo báo giá), dù DB `quotations.created_by` là NOT NULL FK. Cần cho audit trail. | database.md — Bảng 20 `quotations` | Bổ sung `created_by: { id, full_name }` vào Response `GET /quotations/{id}`. |
| **C-07** 🟡 Minor | `GET /surveys/{id}` (10-survey-assignment.md) | Response trả về `evidence_files: [{ id, file_url }]` nhưng thiếu `file_type`, `file_name` — cần thiết để mobile hiển thị đúng icon và tên file. | database.md — Bảng 41 `evidence_files` | Bổ sung `file_name`, `file_type`, `file_size` vào từng object trong `evidence_files[]`. |

---

### NHÓM D — THIẾT KẾ RESTful & HTTP STATUS CODE

| Mã Lỗi | Vị trí / API Path | Mô tả chi tiết | Tài liệu tham chiếu | Gợi ý sửa đổi |
|---|---|---|---|---|
| **D-01** 🔴 Critical | Toàn bộ contract | **Không có cơ chế refresh token** — README §A.1 ghi rõ "không refresh token". JWT hết hạn sau 7 ngày buộc người dùng đăng nhập lại. Với mobile app hoạt động liên tục tại hiện trường, đây là rủi ro UX nghiêm trọng (Leader Staff mất session giữa sự kiện). | README.md — §A.1 | Cân nhắc thêm refresh token với TTL dài hơn (30 ngày), hoặc implement silent re-auth. Ít nhất cần endpoint `POST /auth/refresh` trả về token mới khi token còn trong thời gian grace period. |
| **D-02** 🟠 Major | `PATCH /users/{id}/status` (02-users-roles.md) | Endpoint `PATCH /users/{id}/status` chỉ nhận `{ "status": "inactive" }` nhưng không định nghĩa **tất cả giá trị hợp lệ** (`active | inactive | suspended`). Thiếu validation lỗi khi truyền giá trị không hợp lệ. | database.md — `users.status` ENUM | Liệt kê đầy đủ giá trị ENUM, thêm lỗi `400` khi `status` không thuộc tập hợp hợp lệ. |
| **D-03** 🟠 Major | `POST /orders/{id}/confirm` (09-orders.md) | Hành động "xác nhận đơn hàng" là một state transition, phù hợp với PATCH hơn là POST. Tương tự `POST /quotations/{id}/approve`, `POST /payments/{id}/confirm`. Về ngữ nghĩa, POST tạo resource mới; PATCH/PUT cập nhật resource. | RESTful Best Practices | Đổi `POST /orders/{id}/confirm` → `PATCH /orders/{id}` với body `{ "status": "confirmed" }`, hoặc giữ nguyên nhưng document rõ đây là sub-resource action theo REST convention. |
| **D-04** 🟠 Major | `POST /auth/forgot-password` (01-auth.md) | HTTP Status `200` khi "username không tồn tại" là không chuẩn — nên trả về `200` nhưng với cùng message chung để tránh user enumeration attack, nhưng bên trong hệ thống cần log khác nhau. Contract hiện tại không định nghĩa behavior khi username không tồn tại. | 01-auth.md; OWASP | Thêm ghi chú: endpoint luôn trả `200` với cùng message bất kể username có tồn tại hay không (tránh user enumeration). |
| **D-05** 🟠 Major | `GET /inventory/availability` (05-warehouse-inventory.md) | Query param `?item_ids=10,15,20` dùng comma-separated string — không phải chuẩn REST phổ biến nhất. Một số HTTP client/framework xử lý khác nhau. | REST API Design | Cân nhắc đổi sang `?item_ids[]=10&item_ids[]=15&item_ids[]=20` hoặc ghi rõ format được accept. |
| **D-06** 🟠 Major | `PATCH /surveys/{id}/assign` (10-survey-assignment.md) | Hành động phân công khảo sát (`PATCH /surveys/{id}/assign`) dùng PATCH cho sub-action — nên dùng `PUT /surveys/{id}` với body chứa `surveyed_by` hoặc `POST /surveys/{id}/assign` nhất quán với các endpoint action khác trong hệ thống. | 10-survey-assignment.md | Thống nhất pattern: tất cả "action" endpoints dùng `POST /{resource}/{id}/{action}`. Đổi thành `POST /surveys/{id}/assign`. |
| **D-07** 🟡 Minor | `GET /orders/{id}/payments` (11-payments-settlement.md) | Endpoint được liệt kê trong bảng tóm tắt nhưng **không có chi tiết implementation** (request params, response schema). | 11-payments-settlement.md | Thêm chi tiết đầy đủ cho `GET /orders/{id}/payments` và `GET /orders/{id}/settlement`. |
| **D-08** 🟡 Minor | Toàn bộ contract | **Thiếu HTTP `401` response** trong hầu hết các endpoint. README định nghĩa `401` là "chưa đăng nhập/token sai" nhưng không endpoint nào list `401` trong bảng lỗi. | README.md — §A.5 | Thêm chú thích chung: "Mọi endpoint yêu cầu auth đều có thể trả `401` nếu token không hợp lệ hoặc hết hạn". |
| **D-09** 🟡 Minor | `DELETE` method | Không có endpoint `DELETE` nào trong toàn bộ contract — thay vào đó dùng `PATCH .../status` (soft delete). Nên document rõ đây là thiết kế có chủ ý. | README.md | Bổ sung vào README: "Hệ thống không hard-delete; mọi vô hiệu hóa dùng `PATCH .../status`". |

---

## III. TỔNG HỢP PHÁT HIỆN THEO MỨC ĐỘ

| Mức độ | Số lượng | Mô tả |
|---|---|---|
| 🔴 **Critical** | **8 lỗi** | A-01, A-02, B-01 → B-07, C-01, D-01 — Phải sửa trước khi deploy |
| 🟠 **Major** | **15 lỗi** | A-03 → A-05, B-08 → B-13, C-02 → C-05, D-02 → D-06 — Ảnh hưởng nghiệp vụ |
| 🟡 **Minor** | **8 lỗi** | A-06 → A-08, B-14, B-15, C-06, C-07, D-07 → D-09 — Cải thiện chất lượng |

---

## IV. KHUYẾN NGHỊ TỐI ƯU

### 4.1 Bảo mật (Security)

1. **Rate Limiting cho Auth endpoints:** `POST /auth/login` và `POST /auth/forgot-password` cần giới hạn số lần gọi (ví dụ: 5 lần/phút/IP). BR-LG04 đề cập khóa tài khoản nhưng không đủ để chống brute force distributed.
2. **Input Sanitization:** Tất cả field dạng `TEXT` (notes, description, venue_notes) cần xử lý XSS và SQL injection ở backend trước khi lưu.
3. **VNPay Callback Authentication:** Phải validate `vnp_SecureHash` (HMAC-SHA512) **trước khi** cập nhật bất kỳ dữ liệu nào. Endpoint callback không dùng JWT nên cần IP whitelist từ VNPay.
4. **Sensitive Data in Logs:** Audit log không nên lưu `password_hash` hay `payment details` vào `old_values`/`new_values` trong `audit_logs`.

### 4.2 Hiệu năng (Performance)

5. **Phân trang cho tất cả list endpoint:** Một số endpoint như `GET /permissions` và `GET /roles` không có pagination — cần thêm nếu dữ liệu có khả năng lớn.
6. **Index DB cho truy vấn thường xuyên:** Cần đảm bảo có index trên: `inventory_reservations(event_date)`, `orders(status, event_date)`, `notifications(user_id, is_read)`, `assignments(user_id, assigned_date)`.
7. **Caching cho dữ liệu master:** `GET /catalog-items`, `GET /roles`, `GET /permissions`, `GET /business-policies` là read-heavy và ít thay đổi — nên cache Redis/Memcached với TTL 5–15 phút.
8. **Dashboard endpoint:** `GET /dashboard/admin` và `GET /dashboard/operations` nên dùng pre-aggregated data hoặc materialized views, tránh tính toán real-time trên toàn bộ bảng khi load trang.

### 4.3 Tính nhất quán & Trải nghiệm phát triển (DX)

9. **Chuẩn hóa prefix MSG:** Hiện tại có ít nhất 2 conflict prefix nghiêm trọng cần giải quyết trước khi code:
   - `MSG-FP` dùng cho cả "Quên mật khẩu" (01-auth) và "Tiến độ hiện trường" (12-mobile)
   - `BR-FP01–05` trùng giữa Xác thực và Thanh toán cuối
   - Khuyến nghị: lập bảng mapping prefix tập trung ngay trong README và đánh số lại toàn bộ.

10. **Versioning API:** Hiện dùng `/api/v1/` nhưng không có cơ chế deprecation. Nên có header `Deprecation` và `Sunset` khi cần nâng lên v2.

11. **Error response chuẩn hóa:** Một số endpoint trả lỗi chỉ có HTTP code và text ngắn (ví dụ `404` — không tìm thấy khách hàng), không có `code` và `errors[]` theo chuẩn README §A.3. Cần thống nhất 100%.

12. **Pagination cho `GET /reports/*`:** Các báo cáo như `GET /reports/wages` trả về toàn bộ data của tháng — nếu số nhân sự lớn cần có pagination hoặc export async.

### 4.4 Bổ sung vào Database (DB Changes Required)

Những thay đổi DB bắt buộc để contract hoạt động đúng:

| Thay đổi | Mức độ ưu tiên |
|---|---|
| Thêm `rental` vào ENUM `supplier_payables.transaction_type` | 🔴 Critical |
| Tạo bảng `order_status_history` | 🔴 Critical |
| Tạo bảng `order_date_changes` | 🔴 Critical |
| Tạo bảng `order_cancellations` | 🔴 Critical |
| Tạo bảng `order_schedules` | 🔴 Critical |
| Tạo bảng `task_progress_updates` | 🔴 Critical |
| Tạo bảng `change_request_items` | 🔴 Critical |
| Thêm `ios` vào ENUM `user_devices.device_type` | 🟠 Major |
| Thêm `order_id` vào `supplier_payables` (nullable FK) | 🟠 Major |

---

## V. MA TRẬN RỦI RO TỔNG HỢP

```
Mức độ ảnh hưởng
    Cao │ B-01  B-02  B-03  B-04  │ A-01  D-01  C-01  │ B-05  B-06  B-07
        │ (thiếu Order lifecycle) │ (data integrity)   │ (thiếu operations)
        ├────────────────────────┼───────────────────┼─────────────────────
   Vừa │ A-02  A-03  B-08  B-09 │ C-02  C-03  D-02  │ B-10  B-11  B-12
        │                        │                   │
      Thấp │ A-04..A-08  D-08  D-09 │ C-06  C-07  B-14  │ D-07
           ├────────────────────────┼───────────────────┼─────────────────────
              Xác suất: Cao         Xác suất: Vừa         Xác suất: Thấp
```

---

*Báo cáo này được tạo dựa trên đối chiếu chéo 4 tài liệu nguồn: `database.md` (44 bảng), `ERD.md` (44 entity, 81 relationship), `documents.md` (SRS), và 13 file API Contract (01–13). Mọi phát hiện đều có tham chiếu cụ thể đến tài liệu gốc.*
