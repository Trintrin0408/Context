Dưới đây là chi tiết toàn bộ 44 bảng (bao gồm 3 bảng mới bổ sung), không tóm tắt, chứa đầy đủ mọi thuộc tính và ràng buộc.

_(Ghi chú: Tất cả cột id đều có kiểu BIGINT PRIMARY KEY AUTO_INCREMENT)_

#### **I. HỆ THỐNG TÀI KHOẢN & PHÂN QUYỀN (Auth & Internal Users)**

| Bảng                     | Thuộc tính (Column)    | Kiểu dữ liệu | Ràng buộc (Constraints)    | Mô tả chi tiết (Description)                |
| :----------------------- | :--------------------- | :----------- | :------------------------- | :------------------------------------------ |
| **1\. roles**            | name                   | VARCHAR(50)  | NOT NULL, UNIQUE           | Tên vai trò (Admin, Manager, Leader, Tech). |
|                          | description            | TEXT         | NULL                       | Chú thích chức năng.                        |
|                          | status                 | ENUM         | NOT NULL, DEFAULT 'active' | active, inactive.                           |
|                          | created_at, updated_at | TIMESTAMP    | NOT NULL                   | Thời gian tạo/cập nhật.                     |
| **2\. permissions**      | name                   | VARCHAR(100) | NOT NULL                   | Tên quyền hạn hiển thị.                     |
|                          | code                   | VARCHAR(100) | NOT NULL, UNIQUE           | Mã quyền Backend (VD: order.view).          |
|                          | description            | TEXT         | NULL                       | Diễn giải quyền.                            |
|                          | created_at             | TIMESTAMP    | NOT NULL                   | Thời gian tạo.                              |
| **3\. role_permissions** | role_id                | BIGINT       | NOT NULL, FK, CASCADE      | Trỏ về roles.id.                            |
|                          | permission_id          | BIGINT       | NOT NULL, FK, CASCADE      | Trỏ về permissions.id.                      |
| **4\. users**            | role_id                | BIGINT       | NOT NULL, FK               | Vai trò của nhân sự.                        |
|                          | username               | VARCHAR(50)  | NOT NULL, UNIQUE           | Tên đăng nhập.                              |
|                          | password_hash          | VARCHAR(255) | NOT NULL                   | Mật khẩu băm (Bcrypt).                      |
|                          | full_name              | VARCHAR(100) | NOT NULL                   | Họ và tên.                                  |
|                          | email, phone           | VARCHAR      | NULL, UNIQUE               | Liên hệ cá nhân.                            |
|                          | status                 | ENUM         | NOT NULL, DEFAULT 'active' | active, inactive, suspended.                |
|                          | created_by             | BIGINT       | NULL, FK                   | Người cấp tài khoản.                        |
|                          | created_at, updated_at | TIMESTAMP    | NOT NULL                   | Thời gian hệ thống.                         |
| **5\. user_devices**     | user_id                | BIGINT       | NOT NULL, FK, CASCADE      | Của nhân viên nào.                          |
|                          | device_token           | VARCHAR(255) | NOT NULL, UNIQUE           | Token Push Notification/WebSocket.          |
|                          | device_type            | ENUM         | NOT NULL                   | android, web.                               |
|                          | created_at, updated_at | TIMESTAMP    | NOT NULL                   | Lần cuối truy cập.                          |

#### **II. ĐỐI TÁC, KHÁCH HÀNG & CHÍNH SÁCH (Entities 4, 5, 7\)**

| Bảng                              | Thuộc tính (Column)    | Kiểu dữ liệu  | Ràng buộc (Constraints)    | Mô tả chi tiết (Description)     |
| :-------------------------------- | :--------------------- | :------------ | :------------------------- | :------------------------------- |
| **6\. business_policies** _(Mới)_ | code                   | VARCHAR(50)   | NOT NULL, UNIQUE           | Mã chính sách (VD: MIN_DEPOSIT). |
|                                   | name                   | VARCHAR(200)  | NOT NULL                   | Tên (Tiền cọc tối thiểu).        |
|                                   | policy_value           | DECIMAL(15,2) | NOT NULL                   | Giá trị (VD: 30).                |
|                                   | unit                   | VARCHAR(20)   | NOT NULL                   | Đơn vị (%, VNĐ, Ngày).           |
|                                   | description            | TEXT          | NULL                       | Giải thích cách tính.            |
|                                   | updated_at             | TIMESTAMP     | NOT NULL                   | Lần đổi quy định cuối.           |
|                                   | updated_by             | BIGINT        | NOT NULL, FK               | Quản lý thay đổi luật.           |
| **7\. customers**                 | full_name              | VARCHAR(100)  | NOT NULL                   | Tên khách hàng.                  |
|                                   | phone                  | VARCHAR(20)   | NOT NULL, UNIQUE           | Định danh số điện thoại.         |
|                                   | email, address         | VARCHAR, TEXT | NULL                       | Liên lạc và gửi hợp đồng.        |
|                                   | notes                  | TEXT          | NULL                       | Ghi chú yêu cầu đặc biệt.        |
|                                   | status                 | ENUM          | NOT NULL, DEFAULT 'active' | active, inactive.                |
|                                   | created_by, updated_by | BIGINT        | NOT NULL, FK               | Nhân sự quản lý.                 |
|                                   | created_at, updated_at | TIMESTAMP     | NOT NULL                   | Thời gian hệ thống.              |
| **8\. suppliers**                 | name                   | VARCHAR(200)  | NOT NULL                   | Tên công ty cung cấp.            |
|                                   | contact_person         | VARCHAR(100)  | NULL                       | Người đại diện.                  |
|                                   | phone, email, address  | VARCHAR, TEXT | NULL                       | Thông tin liên lạc.              |
|                                   | status                 | ENUM          | NOT NULL, DEFAULT 'active' | active, inactive.                |
|                                   | created_by             | BIGINT        | NULL, FK                   | Người thêm hồ sơ.                |
|                                   | created_at, updated_at | TIMESTAMP     | NOT NULL                   | Thời gian hệ thống.              |

#### **III. DANH MỤC, VẬT TƯ & KHO BÃI (Entities 6, 16, 17\)**

| Bảng                            | Thuộc tính (Column)    | Kiểu dữ liệu                    | Ràng buộc (Constraints)    | Mô tả chi tiết (Description)               |
| :------------------------------ | :--------------------- | :------------------------------ | :------------------------- | :----------------------------------------- |
| **9\. catalog_categories**      | name                   | VARCHAR(100)                    | NOT NULL, UNIQUE           | Nhóm dịch vụ (Bàn ghế).                    |
|                                 | description            | TEXT                            | NULL                       | Mô tả phân loại.                           |
|                                 | status                 | ENUM                            | NOT NULL, DEFAULT 'active' | Tình trạng kinh doanh.                     |
|                                 | created_by             | BIGINT                          | NULL, FK                   | Người lập danh mục.                        |
|                                 | created_at             | TIMESTAMP                       | NOT NULL                   | Ngày tạo.                                  |
| **10\. catalog_items**          | category_id            | BIGINT                          | NOT NULL, FK               | Thuộc danh mục nào.                        |
|                                 | code                   | VARCHAR(50)                     | NOT NULL, UNIQUE           | Mã hàng nội bộ.                            |
|                                 | name                   | VARCHAR(200)                    | NOT NULL                   | Tên hiển thị.                              |
|                                 | unit                   | VARCHAR(50)                     | NOT NULL                   | Đơn vị (Bộ, Gói, Cái).                     |
|                                 | description            | TEXT                            | NULL                       | Chi tiết kỹ thuật.                         |
|                                 | status                 | ENUM                            | NOT NULL, DEFAULT 'active' | Cho phép kinh doanh không.                 |
|                                 | created_by             | BIGINT                          | NULL, FK                   | Người tạo.                                 |
|                                 | created_at, updated_at | TIMESTAMP                       | NOT NULL                   | Lịch sử sửa đổi.                           |
| **11\. item_price_history**     | catalog_item_id        | BIGINT                          | NOT NULL, FK               | Thuộc mặt hàng nào.                        |
|                                 | price                  | DECIMAL(15,2)                   | NOT NULL, CHECK \>= 0      | Đơn giá.                                   |
|                                 | valid_from             | DATETIME                        | NOT NULL                   | Hiệu lực từ.                               |
|                                 | valid_to               | DATETIME                        | NULL                       | Hiệu lực đến (NULL=hiện tại).              |
|                                 | created_by             | BIGINT                          | NOT NULL, FK               | Người set giá.                             |
|                                 | created_at             | TIMESTAMP                       | NOT NULL                   | Lúc thiết lập.                             |
| **12\. warehouses** _(Mới)_     | name                   | VARCHAR(200)                    | NOT NULL, UNIQUE           | Tên kho (Kho Tổng, Kho Phụ).               |
|                                 | address                | TEXT                            | NULL                       | Địa chỉ vật lý.                            |
|                                 | status                 | ENUM                            | NOT NULL, DEFAULT 'active' | Đang hoạt động.                            |
|                                 | created_at, updated_at | TIMESTAMP                       | NOT NULL                   | Thời gian hệ thống.                        |
| **13\. inventory**              | warehouse_id _(Mới)_   | BIGINT                          | NOT NULL, FK               | Nằm ở kho nào.                             |
|                                 | catalog_item_id        | BIGINT                          | NOT NULL, FK               | Quản lý món đồ nào.                        |
|                                 | quantity_total         | DECIMAL(10,2)                   | NOT NULL, DEFAULT 0        | Tổng thực tế.                              |
|                                 | quantity_available     | DECIMAL(10,2)                   | NOT NULL, DEFAULT 0        | Có sẵn (Rảnh).                             |
|                                 | quantity_reserved      | DECIMAL(10,2)                   | NOT NULL, DEFAULT 0        | Đã bị giữ chỗ.                             |
|                                 | quantity_damaged       | DECIMAL(10,2)                   | NOT NULL, DEFAULT 0        | Hỏng hóc chờ sửa.                          |
|                                 | location               | VARCHAR(200)                    | NULL                       | Vị trí ô/kệ.                               |
|                                 | last_updated           | TIMESTAMP                       | NOT NULL                   | Biến động gần nhất.                        |
| _(Constraint)_                  | UNIQUE                 | (warehouse_id, catalog_item_id) | Chỉ 1 record/kho/mặt hàng. |                                            |
| **14\. inventory_transactions** | warehouse_id _(Mới)_   | BIGINT                          | NOT NULL, FK               | Xảy ra tại kho nào.                        |
|                                 | catalog_item_id        | BIGINT                          | NOT NULL, FK               | Mặt hàng nào.                              |
|                                 | transaction_type       | ENUM                            | NOT NULL                   | in, out, reserve, release, damage, adjust. |
|                                 | quantity               | DECIMAL(10,2)                   | NOT NULL                   | Độ lớn biến động.                          |
|                                 | before_qty             | DECIMAL(10,2)                   | NOT NULL                   | Tồn khả dụng trước.                        |
|                                 | after_qty              | DECIMAL(10,2)                   | NOT NULL                   | Tồn khả dụng sau.                          |
|                                 | reference_type         | VARCHAR(100)                    | NULL                       | (Polymorphic) Bảng nguyên nhân.            |
|                                 | reference_id           | BIGINT                          | NULL                       | (Polymorphic) ID nguyên nhân.              |
|                                 | notes                  | TEXT                            | NULL                       | Chú giải.                                  |
|                                 | created_by             | BIGINT                          | NOT NULL, FK               | Kẻ gây ra biến động.                       |
|                                 | created_at             | TIMESTAMP                       | NOT NULL                   | Ngày lưu sổ.                               |

#### **IV. CÔNG NỢ NHÀ CUNG CẤP (Entities 18, 22, 23\)**

| Bảng                                   | Thuộc tính (Column)    | Kiểu dữ liệu  | Ràng buộc (Constraints)    | Mô tả chi tiết (Description)                   |
| :------------------------------------- | :--------------------- | :------------ | :------------------------- | :--------------------------------------------- |
| **15\. supplier_payables** _(Đổi tên)_ | supplier_id            | BIGINT        | NOT NULL, FK               | Nợ đối tác nào.                                |
|                                        | transaction_type       | ENUM          | NOT NULL                   | purchase (mua), return (trả hàng), adjustment. |
|                                        | total_amount           | DECIMAL(15,2) | NOT NULL                   | Tổng tiền đơn hàng/công nợ.                    |
|                                        | paid_amount _(Mới)_    | DECIMAL(15,2) | NOT NULL, DEFAULT 0        | Tiền đã trả cho đơn này.                       |
|                                        | transaction_date       | DATE          | NOT NULL                   | Ngày chốt mua.                                 |
|                                        | due_date               | DATE          | NULL                       | Hạn chót phải trả tiền.                        |
|                                        | reference_code         | VARCHAR(100)  | NULL                       | Mã hóa đơn của đối tác.                        |
|                                        | status                 | ENUM          | NOT NULL, DEFAULT 'unpaid' | unpaid, partial, paid, cancelled.              |
|                                        | created_by             | BIGINT        | NOT NULL, FK               | Người lập sổ nợ.                               |
|                                        | created_at, updated_at | TIMESTAMP     | NOT NULL                   | Thời gian.                                     |
| **16\. supplier_payable_items**        | supplier_payable_id    | BIGINT        | NOT NULL, FK, CASCADE      | Trỏ về chứng từ nợ.                            |
|                                        | catalog_item_id        | BIGINT        | NOT NULL, FK               | Mặt hàng mua.                                  |
|                                        | quantity, unit_price   | DECIMAL       | NOT NULL                   | Số lượng, đơn giá.                             |
|                                        | total_price            | DECIMAL(15,2) | NOT NULL                   | Thành tiền.                                    |
|                                        | notes                  | TEXT          | NULL                       | Ghi chú mặt hàng.                              |
| **17\. supplier_payments** _(Mới)_     | supplier_id            | BIGINT        | NOT NULL, FK               | Trả cho ai.                                    |
|                                        | supplier_payable_id    | BIGINT        | NULL, FK                   | Trả đích danh cho hóa đơn nợ nào (Nếu có).     |
|                                        | amount                 | DECIMAL(15,2) | NOT NULL                   | Số tiền chi trả.                               |
|                                        | payment_date           | DATETIME      | NOT NULL                   | Ngày chi tiền.                                 |
|                                        | payment_method         | ENUM          | NOT NULL                   | bank_transfer, cash.                           |
|                                        | reference_code         | VARCHAR(200)  | NULL                       | Số UNC/Phiếu chi.                              |
|                                        | notes                  | TEXT          | NULL                       | Diễn giải lý do chi.                           |
|                                        | created_by             | BIGINT        | NOT NULL, FK               | Kế toán chi tiền.                              |
|                                        | created_at             | TIMESTAMP     | NOT NULL                   | Giờ hệ thống.                                  |

#### **V. VÒNG ĐỜI SỰ KIỆN (Entities 9, 10, 11, 12, 13\)**

| Bảng                            | Thuộc tính (Column)       | Kiểu dữ liệu        | Ràng buộc (Constraints)      | Mô tả chi tiết (Description)                                                                 |
| :------------------------------ | :------------------------ | :------------------ | :--------------------------- | :------------------------------------------------------------------------------------------- |
| **18\. orders**                 | code                      | VARCHAR(50)         | NOT NULL, UNIQUE             | Mã tiệc (ORD-XXX).                                                                           |
|                                 | customer_id               | BIGINT              | NOT NULL, FK                 | Khách nào đặt.                                                                               |
|                                 | event_type                | VARCHAR(100)        | NOT NULL                     | Thể loại sự kiện.                                                                            |
|                                 | event_date                | DATE                | NOT NULL                     | Khởi điểm sự kiện.                                                                           |
|                                 | event_end_date            | DATE                | NULL                         | Ngày kết thúc tiệc.                                                                          |
|                                 | venue_name, venue_address | VARCHAR, TEXT       | NULL                         | Tên địa điểm và Tọa độ/địa chỉ.                                                              |
|                                 | guest_count               | INT                 | NULL                         | Quy mô khách dự.                                                                             |
|                                 | notes                     | TEXT                | NULL                         | Lưu ý chung.                                                                                 |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'new'      | new \-\> surveyed \-\> quoted \-\> confirmed \-\> in_progress \-\> completed \-\> cancelled. |
|                                 | created_by, updated_by    | BIGINT              | FK                           | Sale/Manager tạo và chốt đơn.                                                                |
|                                 | created_at, updated_at    | TIMESTAMP           | NOT NULL                     | Thời gian.                                                                                   |
| **19\. inventory_reservations** | order_id                  | BIGINT              | NOT NULL, FK                 | Đặt gạch cho tiệc nào.                                                                       |
|                                 | catalog_item_id           | BIGINT              | NOT NULL, FK                 | Giữ đồ gì.                                                                                   |
|                                 | quantity_reserved         | DECIMAL(10,2)       | NOT NULL                     | Lượng giữ.                                                                                   |
|                                 | event_date                | DATE                | NOT NULL                     | Dùng ngày nào (Chống trùng).                                                                 |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'reserved' | reserved, released, fulfilled.                                                               |
|                                 | created_by                | BIGINT              | NULL, FK                     | Ai thao tác.                                                                                 |
|                                 | created_at, updated_at    | TIMESTAMP           | NOT NULL                     | Thời gian.                                                                                   |
| **20\. quotations**             | order_id                  | BIGINT              | NOT NULL, FK                 | Báo giá cho đơn nào.                                                                         |
|                                 | version                   | INT                 | NOT NULL, DEFAULT 1          | Phiên bản (V1, V2).                                                                          |
|                                 | total_amount              | DECIMAL(15,2)       | NOT NULL                     | Tiền hàng.                                                                                   |
|                                 | discount_amount           | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Chiết khấu.                                                                                  |
|                                 | final_amount              | DECIMAL(15,2)       | NOT NULL                     | Thực trả.                                                                                    |
|                                 | notes                     | TEXT                | NULL                         | Lời nhắn kèm báo giá.                                                                        |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'draft'    | draft, sent, approved, rejected, superseded.                                                 |
|                                 | sent_at, approved_at      | TIMESTAMP           | NULL                         | Giờ gửi / Giờ chốt.                                                                          |
|                                 | created_by                | BIGINT              | NOT NULL, FK                 | Ai tạo giá.                                                                                  |
|                                 | created_at, updated_at    | TIMESTAMP           | NOT NULL                     | Thời gian.                                                                                   |
| _(Constraint)_                  | UNIQUE                    | (order_id, version) | Mỗi version 1 bản.           |                                                                                              |
| **21\. quotation_lines**        | quotation_id              | BIGINT              | NOT NULL, FK, CASCADE        | Trỏ về bảng báo giá.                                                                         |
|                                 | catalog_item_id           | BIGINT              | NOT NULL, FK                 | Hạng mục cung cấp.                                                                           |
|                                 | item_name                 | VARCHAR(200)        | NOT NULL                     | Tên cứng lúc báo.                                                                            |
|                                 | quantity                  | DECIMAL(10,2)       | NOT NULL                     | Số lượng.                                                                                    |
|                                 | unit_price                | DECIMAL(15,2)       | NOT NULL                     | Giá cứng lúc báo.                                                                            |
|                                 | total_price               | DECIMAL(15,2)       | NOT NULL                     | Thành tiền.                                                                                  |
|                                 | notes                     | TEXT                | NULL                         | Ghi chú hạng mục.                                                                            |
| **22\. change_requests**        | order_id                  | BIGINT              | NOT NULL, FK                 | Phát sinh tại tiệc nào.                                                                      |
|                                 | requested_by              | BIGINT              | NOT NULL, FK                 | Ai báo cáo.                                                                                  |
|                                 | change_type               | ENUM                | NOT NULL                     | Thêm đồ, bỏ đồ, đổi ngày...                                                                  |
|                                 | description               | TEXT                | NOT NULL                     | Kể rõ mong muốn khách.                                                                       |
|                                 | requested_at              | TIMESTAMP           | NOT NULL                     | Thời điểm báo.                                                                               |
|                                 | reviewed_by               | BIGINT              | NULL, FK                     | Ai duyệt y.                                                                                  |
|                                 | reviewed_at               | TIMESTAMP           | NULL                         | Lúc duyệt.                                                                                   |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'pending'  | pending, approved, rejected.                                                                 |
|                                 | review_notes              | TEXT                | NULL                         | Bình luận phê duyệt.                                                                         |
| **23\. payments**               | order_id                  | BIGINT              | NOT NULL, FK                 | Khách đóng cho đơn nào.                                                                      |
|                                 | payment_type              | ENUM                | NOT NULL                     | deposit (Cọc), final (Chốt).                                                                 |
|                                 | amount                    | DECIMAL(15,2)       | NOT NULL                     | Tiền đóng.                                                                                   |
|                                 | payment_method            | ENUM                | NOT NULL                     | vnpay, cash.                                                                                 |
|                                 | transaction_ref           | VARCHAR(200)        | NULL                         | Mã tham chiếu / Mã VNPay.                                                                    |
|                                 | payment_date              | DATETIME            | NOT NULL                     | Lúc chuyển khoản.                                                                            |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'pending'  | pending, confirmed, failed, refunded.                                                        |
|                                 | notes                     | TEXT                | NULL                         | Diễn giải.                                                                                   |
|                                 | confirmed_by              | BIGINT              | NULL, FK                     | Kế toán xác thực.                                                                            |
|                                 | confirmed_at              | TIMESTAMP           | NULL                         | Giờ xác thực.                                                                                |
|                                 | created_by                | BIGINT              | NOT NULL, FK                 | Ai tạo lệnh thu.                                                                             |
|                                 | created_at, updated_at    | TIMESTAMP           | NOT NULL                     | Thời gian.                                                                                   |
| **24\. settlements**            | order_id                  | BIGINT              | NOT NULL, FK, UNIQUE         | Chốt sổ cho đơn nào (1-1).                                                                   |
|                                 | total_service_amount      | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Tiền thu đúng hợp đồng.                                                                      |
|                                 | total_extra_amount        | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Tiền phụ thu.                                                                                |
|                                 | total_discount            | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Trừ bớt/Đền bù.                                                                              |
|                                 | total_damage_recovery     | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Phạt vỡ ly/chén.                                                                             |
|                                 | total_paid                | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Tiền khách đã đóng.                                                                          |
|                                 | balance                   | DECIMAL(15,2)       | NOT NULL, DEFAULT 0          | Khoản dư cuối cùng.                                                                          |
|                                 | status                    | ENUM                | NOT NULL, DEFAULT 'draft'    | draft, pending_approval, approved, completed.                                                |
|                                 | notes                     | TEXT                | NULL                         | Tóm tắt tài chính.                                                                           |
|                                 | created_by                | BIGINT              | NOT NULL, FK                 | Kế toán chốt sổ.                                                                             |
|                                 | approved_by               | BIGINT              | NULL, FK                     | Sếp ký duyệt.                                                                                |
|                                 | approved_at               | TIMESTAMP           | NULL                         | Giờ ký.                                                                                      |
|                                 | created_at, updated_at    | TIMESTAMP           | NOT NULL                     | Thời gian.                                                                                   |
| **25\. settlement_lines**       | settlement_id             | BIGINT              | NOT NULL, FK, CASCADE        | Của bảng chốt sổ nào.                                                                        |
|                                 | line_type                 | ENUM                | NOT NULL                     | Loại dòng (Phụ thu phí ship...).                                                             |
|                                 | description               | TEXT                | NOT NULL                     | Giải thích dòng phí.                                                                         |
|                                 | amount                    | DECIMAL(15,2)       | NOT NULL                     | Độ lớn số tiền.                                                                              |
|                                 | created_at                | TIMESTAMP           | NOT NULL                     | Giờ ghi nhận.                                                                                |

#### **VI. VẬN HÀNH THỰC ĐỊA (Entities 14, 15, 19, 20, 21\)**

| Bảng                         | Thuộc tính (Column)      | Kiểu dữ liệu      | Ràng buộc (Constraints)      | Mô tả chi tiết (Description)                       |
| :--------------------------- | :----------------------- | :---------------- | :--------------------------- | :------------------------------------------------- |
| **26\. survey_reports**      | order_id                 | BIGINT            | NOT NULL, FK                 | Khảo sát cho đơn.                                  |
|                              | surveyed_by              | BIGINT            | NOT NULL, FK                 | Nhân sự đi khảo sát.                               |
|                              | survey_date              | DATE              | NOT NULL                     | Ngày đi xem rạp.                                   |
|                              | venue_notes              | TEXT              | NULL                         | Đo đạc chiều dài rộng.                             |
|                              | requirement_notes        | TEXT              | NULL                         | Yêu cầu màu sắc, phong cách.                       |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'draft'    | Trạng thái biên bản.                               |
|                              | submitted_at             | TIMESTAMP         | NULL                         | Giờ nộp tài liệu.                                  |
|                              | approved_by, approved_at | BIGINT, TIMESTAMP | FK, NULL                     | Thông tin phê duyệt.                               |
|                              | created_at, updated_at   | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **27\. survey_items**        | survey_report_id         | BIGINT            | NOT NULL, FK, CASCADE        | Trỏ về biên bản khảo sát.                          |
|                              | catalog_item_id          | BIGINT            | NULL, FK                     | Đồ công ty cấp.                                    |
|                              | item_name                | VARCHAR(200)      | NOT NULL                     | Đồ cần mua thêm.                                   |
|                              | quantity_required        | DECIMAL(10,2)     | NOT NULL                     | Số lượng dự phòng.                                 |
|                              | notes                    | TEXT              | NULL                         | Ghi chú kích thước.                                |
| **28\. assignments**         | order_id                 | BIGINT            | NOT NULL, FK                 | Trực tại tiệc nào.                                 |
|                              | user_id                  | BIGINT            | NOT NULL, FK                 | Cắt cử người nào.                                  |
|                              | assigned_date            | DATE              | NOT NULL                     | Ngày trực.                                         |
|                              | session_type             | ENUM              | NOT NULL                     | Ca sáng/chiều/tối/setup đêm.                       |
|                              | role_in_event            | VARCHAR(100)      | NULL                         | Vai trò (Thợ chính, MC).                           |
|                              | notes                    | TEXT              | NULL                         | Lời nhắn lúc phân ca.                              |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'assigned' | Tình trạng nhận việc.                              |
|                              | created_by               | BIGINT            | NOT NULL, FK                 | Quản lý sắp lịch.                                  |
|                              | created_at, updated_at   | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **29\. tasks**               | assignment_id            | BIGINT            | NOT NULL, FK, CASCADE        | Đầu việc của ca trực nào.                          |
|                              | title                    | VARCHAR(200)      | NOT NULL                     | Tên Task (Đi dây led).                             |
|                              | description              | TEXT              | NULL                         | Cách làm.                                          |
|                              | priority                 | ENUM              | NOT NULL, DEFAULT 'medium'   | Độ ưu tiên.                                        |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'todo'     | Đã xong chưa.                                      |
|                              | completed_at             | TIMESTAMP         | NULL                         | Lúc tích Done.                                     |
|                              | created_at, updated_at   | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **30\. pick_lists**          | order_id                 | BIGINT            | NOT NULL, FK                 | Danh sách xuất đồ cho tiệc.                        |
|                              | assignment_id            | BIGINT            | NULL, FK                     | Ai lãnh trách nhiệm nhặt.                          |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'pending'  | Tình trạng soạn xe.                                |
|                              | created_by               | BIGINT            | NOT NULL, FK                 | Người chốt danh sách xuất.                         |
|                              | created_at, updated_at   | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **31\. pick_list_items**     | pick_list_id             | BIGINT            | NOT NULL, FK, CASCADE        | Trỏ về phiếu soạn.                                 |
|                              | catalog_item_id          | BIGINT            | NOT NULL, FK                 | Món đồ phải nhặt.                                  |
|                              | quantity_required        | DECIMAL(10,2)     | NOT NULL                     | Số lượng giấy tờ.                                  |
|                              | quantity_picked          | DECIMAL(10,2)     | NOT NULL, DEFAULT 0          | Số lượng thực cho lên xe.                          |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'pending'  | pending, picked, short.                            |
|                              | notes                    | TEXT              | NULL                         | Ghi chú (kho báo thiếu).                           |
| **32\. handovers**           | order_id                 | BIGINT            | NOT NULL, FK                 | Bàn giao cho tiệc nào.                             |
|                              | handover_type            | ENUM              | NOT NULL                     | Giao đi (pre_event) hay Giao lại kho (post_event). |
|                              | from_user_id             | BIGINT            | NULL, FK                     | Người đưa (Thủ kho / Leader).                      |
|                              | to_user_id               | BIGINT            | NULL, FK                     | Người cầm (Leader / Thủ kho).                      |
|                              | handover_date            | DATETIME          | NOT NULL                     | Lúc điểm chỉ tay.                                  |
|                              | notes                    | TEXT              | NULL                         | Chú thích chung.                                   |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'pending'  | Tình trạng ký nhận.                                |
|                              | created_by               | BIGINT            | NOT NULL, FK                 | Ai tạo biên bản.                                   |
|                              | created_at               | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **33\. handover_items**      | handover_id              | BIGINT            | NOT NULL, FK, CASCADE        | Trỏ về biên bản bàn giao.                          |
|                              | catalog_item_id          | BIGINT            | NOT NULL, FK                 | Mặt hàng kiểm đếm.                                 |
|                              | quantity_expected        | DECIMAL(10,2)     | NOT NULL                     | Theo lý thuyết phải có.                            |
|                              | quantity_actual          | DECIMAL(10,2)     | NULL                         | Mắt thấy tay sờ đếm được.                          |
|                              | condition_notes          | TEXT              | NULL                         | Trạng thái vật lý lúc nhận.                        |
|                              | item_status              | ENUM              | NOT NULL, DEFAULT 'ok'       | ok, damaged, missing.                              |
| **34\. damage_loss_reports** | order_id                 | BIGINT            | NOT NULL, FK                 | Đền bù ở tiệc nào.                                 |
|                              | reported_by              | BIGINT            | NOT NULL, FK                 | Kẻ báo tin/Leader.                                 |
|                              | report_date              | DATE              | NOT NULL                     | Ngày xảy ra xô xát.                                |
|                              | description              | TEXT              | NULL                         | Nguyên nhân cháy/mất.                              |
|                              | status                   | ENUM              | NOT NULL, DEFAULT 'draft'    | Quy trình xét duyệt đền bù.                        |
|                              | reviewed_by, reviewed_at | BIGINT, TIMESTAMP | FK, NULL                     | Sếp duyệt án phạt.                                 |
|                              | created_at, updated_at   | TIMESTAMP         | NOT NULL                     | Lịch sử.                                           |
| **35\. damage_loss_items**   | damage_loss_report_id    | BIGINT            | NOT NULL, FK, CASCADE        | Trỏ về biên bản sự cố.                             |
|                              | catalog_item_id          | BIGINT            | NOT NULL, FK                 | Món bị hỏng.                                       |
|                              | quantity                 | DECIMAL(10,2)     | NOT NULL                     | Số lượng hỏng.                                     |
|                              | damage_type              | ENUM              | NOT NULL                     | Vỡ (damaged) hay Trộm (lost).                      |
|                              | estimated_cost           | DECIMAL(15,2)     | NOT NULL                     | Dự toán sửa/mua đền.                               |
|                              | responsible_user_id      | BIGINT            | NULL, FK                     | Đứa nào làm hỏng bắt đền đứa đó.                   |
|                              | notes                    | TEXT              | NULL                         | Bằng chứng sự việc.                                |

#### **VII. LƯƠNG & CHẤM CÔNG (Entities 8, 24, 25, 26, 27\)**

| Bảng                     | Thuộc tính (Column)      | Kiểu dữ liệu                             | Ràng buộc (Constraints)           | Mô tả chi tiết (Description)            |
| :----------------------- | :----------------------- | :--------------------------------------- | :-------------------------------- | :-------------------------------------- |
| **36\. wage_rules**      | role_id                  | BIGINT                                   | NOT NULL, FK                      | Cấp bậc áp dụng.                        |
|                          | session_type             | ENUM                                     | NOT NULL                          | Ca (morning, night_setup...).           |
|                          | wage_amount              | DECIMAL(15,2)                            | NOT NULL                          | Định mức (VD: 500k/ca).                 |
|                          | valid_from               | DATE                                     | NOT NULL                          | Bắt đầu áp dụng.                        |
|                          | valid_to                 | DATE                                     | NULL                              | Hết hạn luật này.                       |
|                          | created_by               | BIGINT                                   | NOT NULL, FK                      | Cán bộ C\&B.                            |
|                          | created_at               | TIMESTAMP                                | NOT NULL                          | Thời gian tạo.                          |
| **37\. attendance**      | assignment_id            | BIGINT                                   | NOT NULL, FK                      | Lấy lịch phân ca.                       |
|                          | user_id                  | BIGINT                                   | NOT NULL, FK                      | Dữ liệu bấm thẻ của ai.                 |
|                          | work_date                | DATE                                     | NOT NULL                          | Của ngày nào.                           |
|                          | session_type             | ENUM                                     | NOT NULL                          | Chấm ca nào.                            |
|                          | check_in_time            | DATETIME                                 | NULL                              | Bấm thẻ vào.                            |
|                          | check_out_time           | DATETIME                                 | NULL                              | Bấm thẻ ra.                             |
|                          | status                   | ENUM                                     | NOT NULL, DEFAULT 'present'       | Xét duyệt hợp lệ không.                 |
|                          | verified_by              | BIGINT                                   | NULL, FK                          | Tổ trưởng kiểm tra.                     |
|                          | created_at, updated_at   | TIMESTAMP                                | NOT NULL                          | Lịch sử bấm thẻ.                        |
| _(Constraint)_           | UNIQUE                   | (assignment_id, work_date, session_type) | 1 ca chỉ bấm 1 phiếu.             |                                         |
| **38\. wage_summaries**  | user_id                  | BIGINT                                   | NOT NULL, FK                      | Trả lương tháng cho ai.                 |
|                          | period_month             | TINYINT                                  | NOT NULL                          | Của tháng (1-12).                       |
|                          | period_year              | SMALLINT                                 | NOT NULL                          | Năm tài chính.                          |
|                          | total_sessions           | INT                                      | NOT NULL, DEFAULT 0               | Tổng công đi làm.                       |
|                          | total_base_wage          | DECIMAL(15,2)                            | NOT NULL, DEFAULT 0               | Tổng lương cơ sở.                       |
|                          | total_deductions         | DECIMAL(15,2)                            | NOT NULL, DEFAULT 0               | Tổng bị phạt đền.                       |
|                          | net_wage                 | DECIMAL(15,2)                            | NOT NULL, DEFAULT 0               | Thực lãnh bting ting.                   |
|                          | status                   | ENUM                                     | NOT NULL, DEFAULT 'draft'         | Trạng thái phiếu lương.                 |
|                          | created_by               | BIGINT                                   | NOT NULL, FK                      | Kế toán chốt.                           |
|                          | approved_by, approved_at | BIGINT, TIMESTAMP                        | FK, NULL                          | Sếp duyệt chi.                          |
|                          | created_at, updated_at   | TIMESTAMP                                | NOT NULL                          | Lịch sử.                                |
| _(Constraint)_           | UNIQUE                   | (user_id, period_month, period_year)     | Mỗi tháng chốt 1 bảng/người.      |                                         |
| **39\. wage_deductions** | wage_summary_id          | BIGINT                                   | NOT NULL, FK, CASCADE             | Đẩy vào bảng lương nào.                 |
|                          | damage_loss_item_id      | BIGINT                                   | NULL, FK                          | (Nếu có) Đền do vỡ món nào.             |
|                          | reason                   | TEXT                                     | NOT NULL                          | Lý do ăn biên bản phạt.                 |
|                          | amount                   | DECIMAL(15,2)                            | NOT NULL                          | Độ lớn tiền phạt.                       |
|                          | created_at               | TIMESTAMP                                | NOT NULL                          | Lịch sử.                                |
| **40\. wage_payments**   | wage_summary_id          | BIGINT                                   | NOT NULL, FK                      | Giao dịch giải ngân cho bảng lương nào. |
|                          | amount                   | DECIMAL(15,2)                            | NOT NULL                          | Tiền bắn đi.                            |
|                          | payment_date             | DATE                                     | NOT NULL                          | Ngày trả lương.                         |
|                          | payment_method           | ENUM                                     | NOT NULL, DEFAULT 'bank_transfer' | TK Ngân hàng / Tiền mặt.                |
|                          | transaction_ref          | VARCHAR(200)                             | NULL                              | Số ủy nhiệm chi/lệnh chuyển.            |
|                          | status                   | ENUM                                     | NOT NULL, DEFAULT 'pending'       | Tình trạng bắn tiền.                    |
|                          | created_by               | BIGINT                                   | NOT NULL, FK                      | Kế toán chi tiền.                       |
|                          | created_at               | TIMESTAMP                                | NOT NULL                          | Lịch sử.                                |

#### **VIII. HỆ THỐNG GHI LOG & MINH CHỨNG (Entities 28, 29, 30\)**

| Bảng                          | Thuộc tính (Column) | Kiểu dữ liệu | Ràng buộc (Constraints) | Mô tả chi tiết (Description)            |
| :---------------------------- | :------------------ | :----------- | :---------------------- | :-------------------------------------- |
| **41\. evidence_files**       | file_name           | VARCHAR(255) | NOT NULL                | Tên tệp tin (HD_Cưới.pdf).              |
|                               | file_url            | VARCHAR(500) | NOT NULL                | Link CDN/AWS lưu trữ thật.              |
|                               | file_type           | VARCHAR(100) | NOT NULL                | Mime (image, pdf).                      |
|                               | file_size           | BIGINT       | NULL                    | Kích thước File.                        |
|                               | uploaded_by         | BIGINT       | NOT NULL, FK            | Người đăng.                             |
|                               | uploaded_at         | TIMESTAMP    | NOT NULL                | Giờ đăng.                               |
| **42\. evidence_attachments** | evidence_file_id    | BIGINT       | NOT NULL, FK, CASCADE   | Lấy file số mấy.                        |
|                               | entity_type         | VARCHAR(100) | NOT NULL                | (Polymorphic) Đính vào nghiệp vụ nào.   |
|                               | entity_id           | BIGINT       | NOT NULL                | (Polymorphic) Đính vào biên bản ID mấy. |
|                               | created_at          | TIMESTAMP    | NOT NULL                | Lịch sử.                                |
| **43\. audit_logs**           | user_id             | BIGINT       | NULL, FK                | Truy vết ai bấm sửa.                    |
|                               | action              | VARCHAR(50)  | NOT NULL                | CREATE, UPDATE, DELETE.                 |
|                               | entity_type         | VARCHAR(100) | NOT NULL                | Mảnh dữ liệu bị tác động.               |
|                               | entity_id           | BIGINT       | NOT NULL                | Ở bản ghi số mấy.                       |
|                               | old_values          | JSON         | NULL                    | Backup data cũ.                         |
|                               | new_values          | JSON         | NULL                    | Hiện trạng data mới.                    |
|                               | ip_address          | VARCHAR(45)  | NULL                    | IP kết nối.                             |
|                               | user_agent          | TEXT         | NULL                    | Thiết bị sử dụng.                       |
|                               | created_at          | TIMESTAMP    | NOT NULL                | Dấu vết vi thời gian.                   |
| **44\. notifications**        | user_id             | BIGINT       | NOT NULL, FK, CASCADE   | Thông báo cho ai.                       |
|                               | title               | VARCHAR(200) | NOT NULL                | Tiêu đề Pop-up.                         |
|                               | message             | TEXT         | NOT NULL                | Chi tiết sự kiện báo.                   |
|                               | type                | ENUM         | NOT NULL                | Thể loại cảnh báo.                      |
|                               | related_entity_type | VARCHAR(100) | NULL                    | Metadata nhúng định tuyến màn hình app. |
|                               | related_entity_id   | BIGINT       | NULL                    | Tải data tự động khi bấm.               |
|                               | is_read             | TINYINT(1)   | NOT NULL, DEFAULT 0     | Xem chưa.                               |
|                               | read_at             | TIMESTAMP    | NULL                    | Giờ mở đọc.                             |
|                               | created_at          | TIMESTAMP    | NOT NULL                | Giờ bắn đi.                             |

### **PHẦN 3: CHI TIẾT TẤT CẢ CÁC MỐI QUAN HỆ (FULL RELATIONSHIPS)**

Dưới đây là 100% các kết nối khóa ngoại và liên kết logic cấu thành nên bức tranh tổng thể của hệ thống.

#### **A. Quan hệ 1-Nhiều (1-N) Cốt Lõi**

1. **roles \-\> users:** 1 Role (Vai trò) quản lý tập hợp n tài khoản Users.
2. **roles \-\> role_permissions:** 1 Role gắn với n Quyền hạn.
3. **permissions \-\> role_permissions:** 1 Quyền hạn được chia sẻ cho n Roles.
4. **users \-\> user_devices:** 1 Nhân sự có n thiết bị đăng nhập nhận thông báo.
5. **users (fk: created_by, updated_by, approved_by, verified_by, requested_by...) \-\> Tất cả các bảng nghiệp vụ:** 1 Nhân sự sinh ra n dấu vết thao tác tạo/duyệt/sửa tài liệu hệ thống.
6. **catalog_categories \-\> catalog_items:** 1 Danh mục cha ôm n Mã hàng.
7. **catalog_items \-\> item_price_history:** 1 Mã hàng có n Mốc thời gian đổi giá.
8. **warehouses \-\> inventory:** 1 Kho chứa n Bản ghi kiểm kê tổng thể.
9. **warehouses \-\> inventory_transactions:** 1 Kho diễn ra n Giao dịch xuất/nhập/hao hụt.
10. **suppliers \-\> supplier_payables:** 1 Đối tác có n Đợt mua bán/công nợ với công ty.
11. **supplier_payables \-\> supplier_payable_items:** 1 Chứng từ mua sỉ chiết xuất ra n Mặt hàng nhập.
12. **catalog_items \-\> supplier_payable_items:** Mã hàng công ty mapping n lần vào các phiếu nhập sỉ.
13. **suppliers \-\> supplier_payments:** 1 Đối tác nhận n Đợt tiền giải ngân từ công ty.
14. **supplier_payables \-\> supplier_payments:** (Tùy chọn) 1 Chứng từ nợ được trả góp/thanh toán qua n Đợt giải ngân.
15. **catalog_items \-\> inventory_transactions:** 1 Mã hàng có vô số lịch sử nhật ký kho.
16. **customers \-\> orders:** 1 Khách hàng (Hồ sơ gốc) sinh ra n Hợp đồng tiệc theo thời gian.
17. **orders \-\> inventory_reservations:** 1 Hợp đồng tiệc kích hoạt n Phiếu giữ chỗ kho.
18. **catalog_items \-\> inventory_reservations:** Mã hàng bị n lần giữ chỗ bởi các tiệc khác nhau.
19. **orders \-\> survey_reports:** 1 Hợp đồng tiệc có n Biên bản khảo sát mặt bằng.
20. **survey_reports \-\> survey_items:** 1 Lần đo đạc yêu cầu n Món đồ cần chuẩn bị.
21. **orders \-\> quotations:** 1 Hợp đồng tiệc sinh ra n Phiên bản làm giá (V1, V2).
22. **quotations \-\> quotation_lines:** 1 Bản báo giá in ra n Dòng đơn giá chi tiết cứng.
23. **catalog_items \-\> quotation_lines:** Mã hàng đưa vào n bản báo giá của khách.
24. **orders \-\> change_requests:** 1 Hợp đồng tiệc phát sinh n Phiếu yêu cầu thay đổi (thêm mâm, đổi rạp).
25. **orders \-\> payments:** 1 Hợp đồng tiệc nhận n Đợt thu tiền từ khách.
26. **orders \-\> assignments:** 1 Hợp đồng tiệc cắt cử n Ca trực phân công nhân sự.
27. **users \-\> assignments:** 1 Nhân sự gồng gánh n Ca trực rải rác các ngày.
28. **assignments \-\> tasks:** 1 Ca trực chẻ ra thành n Đầu việc cho nhân sự đó tích Done.
29. **orders \-\> pick_lists:** 1 Hợp đồng tiệc in n Phiếu yêu cầu bốc hàng ra xe tải.
30. **assignments \-\> pick_lists:** 1 Ca trực chịu trách nhiệm cho n Phiếu bốc hàng.
31. **pick_lists \-\> pick_list_items:** 1 Phiếu bốc chứa n Hạng mục nhặt đồ.
32. **catalog_items \-\> pick_list_items:** Mã đồ bị bốc n lần lên các xe tải.
33. **orders \-\> handovers:** 1 Hợp đồng tiệc sinh n Biên bản giao nhận thực địa (đi và về).
34. **handovers \-\> handover_items:** 1 Lần giao nhận đếm tay n Mặt hàng đối chiếu.
35. **catalog_items \-\> handover_items:** Mã đồ được kiểm đếm n lần tại hiện trường.
36. **orders \-\> damage_loss_reports:** 1 Hợp đồng tiệc xui xẻo dính n Biên bản sự cố/cháy nổ.
37. **damage_loss_reports \-\> damage_loss_items:** 1 Lần hỏa hoạn bắt đền n Mặt hàng bị cháy.
38. **catalog_items \-\> damage_loss_items:** Mã đồ chịu tang n lần do bị làm hỏng.
39. **users \-\> damage_loss_items:** 1 Nhân sự bị quy trách nhiệm cho n Món đồ bị vỡ.
40. **settlements \-\> settlement_lines:** 1 Bản Quyết toán cõng n Dòng phụ phí sinh thêm.
41. **roles \-\> wage_rules:** 1 Role nhận n Định mức lương (Chia ca ngày/đêm).
42. **assignments \-\> attendance:** 1 Phiếu điều phối Ca trực sinh ra n Dữ liệu quét điện thoại (in/out).
43. **users \-\> attendance:** 1 Nhân sự có n Dữ liệu chấm công rải rác các ngày.
44. **users \-\> wage_summaries:** 1 Nhân sự cuối năm nhận n Bảng lương tổng kết tháng.
45. **wage_summaries \-\> wage_deductions:** 1 Tháng lãnh lương bị n Phiếu phạt trừ tiền hụt vào.
46. **damage_loss_items \-\> wage_deductions:** 1 Món đồ vỡ bị đòi nợ khấu trừ n lần (Nếu phạt trừ dần).
47. **wage_summaries \-\> wage_payments:** 1 Bảng lương tháng được CK thanh toán n Đợt (nếu cty nợ lương trả góp).
48. **evidence_files \-\> evidence_attachments:** 1 Ảnh Gốc trên Cloud lôi ra đính kèm n chỗ trong phần mềm.
49. **users \-\> audit_logs:** 1 Nhân sự để lại n Vết log sửa xóa thao tác.
50. **users \-\> notifications:** 1 Nhân sự bị n Tin nhắn hệ thống réo trên điện thoại.

#### **B. Quan hệ 1-1 (1-1) Ràng Buộc Kép**

51. **catalog_items \-\> inventory:** UNIQUE Constraint bắt buộc 1 Mã đồ tại 1 Kho TỔNG HỢP DUY NHẤT 1 Bảng dữ liệu tồn kho hiện thời.
52. **orders \-\> settlements:** UNIQUE Constraint bắt buộc 1 Hợp đồng Sự kiện kết thúc xong CHỈ LẬP 1 Bảng Quyết toán tài chính nội bộ.

#### **C. Quan hệ Đa Hình Kép (Polymorphic Relationships)**

53. **evidence_attachments (entity_type, entity_id):** Bản lề đa hướng. Đóng vai trò là bảng Con (Nhiều) phục vụ cho MỌI BẢNG (Một) khác nhau trên toàn hệ thống. Nhờ cấu trúc này, bạn có thể đính kèm ảnh chụp hóa đơn vào Phiếu giải ngân lương (wage_payments), đính ảnh hiện trường vỡ vào Báo cáo (damage_loss_reports), đính hợp đồng PDF vào Báo giá (quotations). Kiến trúc này đặc biệt thuận lợi nếu team 5 người của bạn chia task: backend trả API chuẩn mực, frontend xử lý UI/UX cho component Upload Ảnh dùng chung (reusable widget), và dữ liệu có thể dễ dàng liên kết không dư thừa.
54. **inventory_transactions (reference_type, reference_id):** Bản lề định tuyến. Mỗi dòng sụt giảm/tăng kho không liên kết khóa ngoại chết vào 1 bảng. Nó trỏ linh hoạt: Tụt kho do Phiếu Giao nhận (handovers), Nhập kho do Mua sỉ (supplier_payables), Hụt kho do Cháy nổ (damage_loss_reports). Hệ thống truy nguyên dấu vết hạch toán cực kỳ minh bạch và mạnh mẽ.
