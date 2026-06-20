# **14\. Những vấn đề cần làm rõ**

| ID  | Mức độ   | Vấn đề                                                              | Ảnh hưởng đến ERD                                      | Câu hỏi cần xác nhận                                                       |
| --- | -------- | ------------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------- |
| Q01 | Critical | Price List hay Item Price riêng từng item                           | Quyết định tồn tại entity Price List                   | Xác nhận loại bỏ Price List và dùng Item Price History?                    |
| Q02 | Critical | Inventory status đang trộn trạng thái vận hành và tình trạng vật lý | Ảnh hưởng cấu trúc Inventory Balance                   | Có cần tách Availability State và Condition State không?                   |
| Q03 | Major    | Một user có một hay nhiều role                                      | Ảnh hưởng Role–User 1:N hay M:N                        | Mỗi account chỉ có một role chính hay có thể kiêm nhiệm?                   |
| Q04 | Major    | Request có thể tồn tại khi chưa tạo Order không                     | Quyết định entity Customer Request                     | Có cần lưu lead/request bị từ chối hoặc chưa chuyển thành Order?           |
| Q05 | Major    | Một Order có nhiều quotation version không                          | Quotation 0..1 hay 0..\*                               | Khi sửa báo giá, hệ thống ghi đè hay tạo phiên bản mới?                    |
| Q06 | Major    | Một Settlement có được sửa và lưu phiên bản không                   | Settlement 0..1 hay 0..\*                              | Khi Customer không đồng ý, có giữ bản Settlement cũ không?                 |
| Q07 | Major    | Một survey task có nhiều report/submission không                    | Task–Survey Report 1:1 hay 1:N                         | Gửi lại survey report có tạo phiên bản mới không?                          |
| Q08 | Major    | Technical Staff có được nhập survey data không                      | Actor không ảnh hưởng entity nhưng ảnh hưởng ownership | Technical Staff khảo sát thì ai nhập và gửi Survey Report?                 |
| Q09 | Major    | Role ghi nhận Supplier receiving không thống nhất                   | Nguồn tạo Supplier receipt data                        | Chỉ Leader Staff ghi nhận hay Technical Staff cũng được ghi nhận?          |
| Q10 | Major    | Role ghi nhận warehouse return không thống nhất                     | Ownership của Warehouse Transaction                    | Technical Staff chỉ thực hiện vật lý hay được nhập số lượng hoàn kho?      |
| Q11 | Major    | Nhiều Warehouse và cách reservation                                 | Cardinality Reservation–Inventory                      | Reservation có xác định Warehouse ngay khi Order confirmed không?          |
| Q12 | Major    | Supplier Payable được tổng hợp theo phiếu hay Order+Supplier        | Identifier và cardinality                              | Một Order–Supplier có một payable hay mỗi transaction một payable?         |
| Q13 | Major    | Công nợ Supplier unpaid có chặn đóng Order không                    | Điều kiện kết thúc Order                               | Order có thể Completed khi Supplier vẫn chưa được thanh toán không?        |
| Q14 | Major    | Trách nhiệm hỏng/mất có thể thuộc nhiều Staff không                 | Có cần Responsibility Allocation entity                | Một Damage Item có thể chia trách nhiệm cho nhiều Staff không?             |
| Q15 | Major    | Package có cấu thành từ item khác không                             | Có thể cần Catalog Item Composition                    | Package dịch vụ là item độc lập hay gồm nhiều thiết bị/dịch vụ con?        |
| Q16 | Minor    | Vehicle có được quản lý thành danh mục không                        | Có cần Vehicle entity                                  | Doanh nghiệp cần lưu biển số, trạng thái và lịch sử phương tiện không?     |
| Q17 | Minor    | Customer agreement evidence                                         | Có cần Evidence File bắt buộc                          | Khi ghi Customer đã đồng ý, có bắt buộc đính kèm tin nhắn/biên nhận không? |
| Q18 | Major    | Status có thực sự cho Admin chỉnh sửa                               | Status entity/reference                                | Admin được sửa tên hiển thị hay thay đổi workflow status?                  |
| Q19 | Major    | Một Order có thể đổi ngày bao nhiêu lần                             | Cardinality Date Change                                | Chính sách có giới hạn số lần đổi ngày không?                              |
| Q20 | Minor    | Item mua một lần có vào Catalog không                               | Supplier Item–Catalog Item optionality                 | Mọi vật tư mua có bắt buộc tạo Catalog Item trước không?                   |

# **CONCEPTUAL ERD – BINH NGUYEN WEDDING MANAGEMENT SYSTEM**

## **I. Nguyên tắc thiết kế đã chốt**

1. Order được tạo khi Manager tiếp nhận yêu cầu Customer.
2. Order chỉ chuyển sang Confirmed sau khi tiền cọc được xác nhận hợp lệ.
3. Không tạo Service Request hoặc Customer Request riêng.
4. Không sử dụng Price List; thay bằng Item Price theo từng Catalog Item.
5. Mỗi Internal User chỉ có một Role chính.
6. Availability State và Condition State được tách riêng, nhưng không tạo thành business entity lớn trong Conceptual ERD.
7. Mỗi lần sửa Quotation quan trọng tạo một Quotation version mới.
8. Không tạo Quotation History riêng vì chính Quotation đã là version.
9. Mỗi Survey Task có tối đa một Survey Report; nếu gửi lại thì cập nhật report hiện có.
10. Leader Staff là người nhập và gửi Survey Report.
11. Technical Staff chỉ thực hiện công việc vật lý và xác nhận task/attendance cá nhân.
12. Leader Staff ghi nhận dữ liệu hiện trường chính thức.
13. Manager xác nhận hoặc phê duyệt các nghiệp vụ quan trọng.
14. Chỉ có một Warehouse chính, nhưng vẫn giữ Warehouse entity.
15. Không quản lý Vehicle vì doanh nghiệp chỉ có một xe chở đồ.
16. Thêm Order Schedule để quản lý các mốc lịch vận hành của Order.
17. Mỗi Supplier Transaction tạo tối đa một Supplier Payable.
18. Supplier Payable chưa thanh toán hết không chặn Order Completed nếu công nợ đã được ghi nhận đầy đủ.
19. Nếu lỗi hỏng/mất do Staff, hệ thống ghi nhận đúng Staff chịu trách nhiệm.
20. Evidence File bắt buộc trong các nghiệp vụ như Payment thủ công, Damage/Loss, Customer agreement quan trọng, Handover, Settlement hoặc tranh chấp.

---

# **II. Danh sách Entity cuối cùng**

| ID  | Entity                     | Tên tiếng Việt              | Loại Entity             | Định nghĩa ngắn                                                                                                            |
| --- | -------------------------- | --------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| E01 | Role                       | Vai trò                     | Supporting/Master       | Nhóm trách nhiệm và quyền truy cập như Admin, Manager, Leader Staff, Technical Staff.                                      |
| E02 | Permission                 | Quyền truy cập              | Supporting/Master       | Quyền hoặc chức năng được phép thực hiện trong hệ thống.                                                                   |
| E03 | Role Permission            | Phân quyền vai trò          | Associative             | Ghi nhận Permission được gán cho Role.                                                                                     |
| E04 | Internal User              | Người dùng nội bộ           | Master                  | Nhân sự nội bộ có tài khoản sử dụng hệ thống.                                                                              |
| E04b | Password Reset Token      | Token khôi phục mật khẩu    | Transaction/Security    | Lưu trữ mã OTP và token đặt lại mật khẩu của người dùng.                                                                   |
| E05 | Customer                   | Khách hàng                  | Master                  | Người hoặc tổ chức thuê dịch vụ cưới hỏi/sự kiện.                                                                          |
| E06 | Supplier                   | Nhà cung cấp                | Master                  | Đối tác cung cấp thiết bị, vật tư hoặc dịch vụ bổ sung.                                                                    |
| E07 | Warehouse                  | Kho                         | Master                  | Kho nội bộ lưu trữ thiết bị của doanh nghiệp.                                                                              |
| E08 | Catalog Item               | Hạng mục danh mục           | Master                  | Thiết bị, dịch vụ, vật tư hoặc package được sử dụng trong Order.                                                           |
| E09 | Catalog Item Composition   | Cấu thành package           | Associative             | Ghi nhận Package gồm các Catalog Item thành phần.                                                                          |
| E10 | Item Price                 | Lịch sử giá hạng mục        | History/Master          | Giá của Catalog Item theo thời gian hiệu lực.                                                                              |
| E11 | Business Policy            | Chính sách nghiệp vụ        | Supporting/History      | Chính sách cọc, hoàn cọc, phí phát sinh, miễn/giảm phí.                                                                    |
| E12 | Wage Rule                  | Quy tắc tiền công           | Supporting/History      | Mức tiền công theo Role, buổi làm và thời gian hiệu lực.                                                                   |
| E13 | Order                      | Đơn hàng                    | Transaction             | Hồ sơ quản lý yêu cầu dịch vụ từ lúc tiếp nhận đến khi hoàn tất hoặc hủy.                                                  |
| E14 | Order Item                 | Hạng mục đơn hàng           | Associative/Transaction | Catalog Item, số lượng và giá áp dụng trong Order.                                                                         |
| E15 | Quotation                  | Báo giá                     | Transaction/Versioned   | Một phiên bản báo giá được lập cho Order.                                                                                  |
| E16 | Quotation Item             | Hạng mục báo giá            | Associative/Transaction | Dòng item, số lượng và giá trong Quotation.                                                                                |
| E17 | Payment                    | Thanh toán                  | Transaction             | Khoản cọc, thanh toán cuối, thanh toán tại hiện trường hoặc hoàn tiền.                                                     |
| E18 | Order Date Change          | Lịch sử đổi ngày            | Event/History           | Một lần thay đổi ngày tổ chức hoặc ngày lắp đặt của Order.                                                                 |
| E19 | Order Cancellation         | Hủy đơn hàng                | Event                   | Hồ sơ hủy Order và kết quả áp dụng chính sách hoàn cọc.                                                                    |
| E20 | Order Status History       | Lịch sử trạng thái Order    | History                 | Một lần Order thay đổi trạng thái.                                                                                         |
| E21 | Order Schedule             | Lịch vận hành đơn hàng      | Planning/Transaction    | Các mốc thời gian quan trọng của Order như khảo sát, xuất kho, vận chuyển, thi công, thu hồi, hoàn kho, nhận/trả Supplier. |
| E22 | Work Task                  | Nhiệm vụ công việc          | Transaction             | Công việc khảo sát, chuẩn bị, vận chuyển, thi công, thu hồi hoặc hoàn kho.                                                 |
| E23 | Assignment                 | Phân công công việc         | Associative             | Ghi nhận Internal User được phân công vào Work Task.                                                                       |
| E24 | Survey Report              | Báo cáo khảo sát            | Transaction             | Kết quả khảo sát hiện trường do Leader Staff nhập và gửi.                                                                  |
| E25 | Task Progress Update       | Cập nhật tiến độ            | Event/History           | Một lần Leader Staff cập nhật tiến độ hoặc ghi chú của Work Task.                                                          |
| E26 | Pick List                  | Danh sách chuẩn bị/thu hồi  | Transaction Document    | Danh sách thiết bị/vật tư cần chuẩn bị, xuất kho hoặc thu hồi.                                                             |
| E27 | Pick List Item             | Hạng mục Pick List          | Associative             | Dòng item và số lượng trong Pick List.                                                                                     |
| E28 | Inventory Balance          | Số dư tồn kho               | Associative/State       | Số lượng Catalog Item trong Warehouse theo availability và condition.                                                      |
| E29 | Inventory Reservation      | Giữ tồn kho                 | Transaction             | Số lượng Catalog Item được khóa cho Order sau khi Order confirmed.                                                         |
| E30 | Warehouse Transaction      | Giao dịch kho               | Transaction             | Xuất kho, hoàn kho hoặc điều chỉnh tồn kho.                                                                                |
| E31 | Warehouse Transaction Item | Hạng mục giao dịch kho      | Associative/Transaction | Dòng item và số lượng trong Warehouse Transaction.                                                                         |
| E32 | Supplier Transaction       | Giao dịch Supplier          | Transaction             | Giao dịch thuê hoặc mua thiết bị/vật tư từ Supplier cho Order.                                                             |
| E33 | Supplier Transaction Item  | Hạng mục giao dịch Supplier | Associative/Transaction | Dòng item, số lượng, đơn giá, tình trạng nhận/trả trong Supplier Transaction.                                              |
| E34 | Supplier Payable           | Công nợ Supplier            | Transaction             | Khoản phải trả phát sinh từ một Supplier Transaction.                                                                      |
| E35 | Change Request             | Yêu cầu thay đổi            | Transaction/Event       | Yêu cầu thêm, bớt hoặc thay thế item tại hiện trường.                                                                      |
| E36 | Change Request Item        | Hạng mục thay đổi           | Associative/Transaction | Dòng item được thêm, bớt hoặc thay thế trong Change Request.                                                               |
| E37 | Handover Record            | Biên bản bàn giao           | Transaction Document    | Hồ sơ bàn giao sau thi công, có minh chứng và trạng thái Customer agreement.                                               |
| E38 | Damage/Loss Record         | Biên bản hỏng/mất           | Transaction/Event       | Hồ sơ tổng hợp các hạng mục hỏng hoặc mất của Order.                                                                       |
| E39 | Damage/Loss Item           | Hạng mục hỏng/mất           | Associative/Transaction | Catalog Item, số lượng, trách nhiệm và bồi thường của item hỏng/mất.                                                       |
| E40 | Settlement                 | Quyết toán                  | Transaction Document    | Hồ sơ tổng hợp giá trị cuối cùng, phí phát sinh, bồi thường và số tiền còn lại.                                            |
| E41 | Attendance                 | Chấm công                   | Event                   | Một buổi làm của Staff theo Assignment.                                                                                    |
| E42 | Wage Summary               | Tổng hợp tiền công          | Transaction Summary     | Tổng hợp tiền công Staff theo tháng.                                                                                       |
| E43 | Wage Deduction             | Khấu trừ tiền công          | Transaction             | Khoản trừ vào tiền công, có thể phát sinh từ Damage/Loss do Staff.                                                         |
| E44 | Evidence File              | Tệp minh chứng              | Supporting              | Hình ảnh, biên nhận, tin nhắn hoặc tài liệu minh chứng cho hồ sơ nghiệp vụ.                                                |

---

# **III. Entity/khái niệm không đưa vào Conceptual ERD**

| Khái niệm                          | Quyết định                       | Lý do                                                                        |
| ---------------------------------- | -------------------------------- | ---------------------------------------------------------------------------- |
| Price List                         | Loại bỏ                          | Dùng Item Price cho từng Catalog Item.                                       |
| Service Request / Customer Request | Không tạo                        | Order được tạo ngay khi Manager tiếp nhận yêu cầu.                           |
| Vehicle                            | Không tạo                        | Doanh nghiệp chỉ có một xe, không cần quản lý danh mục xe.                   |
| Vehicle Assignment                 | Không tạo                        | Không có nghiệp vụ phân công giữa nhiều xe.                                  |
| Responsibility Allocation          | Không tạo                        | Nếu lỗi do Staff thì xác định trực tiếp Staff chịu trách nhiệm.              |
| Quotation History                  | Không tạo                        | Mỗi Quotation là một version.                                                |
| Payment Request                    | Không tạo                        | Có thể xem là Payment ở trạng thái Pending.                                  |
| Deposit / Final Payment / Refund   | Không tạo                        | Là Payment Type của Payment.                                                 |
| Checkout / Check-in                | Không tạo                        | Là Transaction Type của Warehouse Transaction.                               |
| Dashboard / Report                 | Không tạo                        | Là dữ liệu tổng hợp.                                                         |
| Status                             | Không tạo entity nghiệp vụ chính | Trạng thái là controlled attribute; lịch sử Order dùng Order Status History. |
| Availability State                 | Không tạo entity riêng           | Là trạng thái khả dụng của Inventory Balance.                                |
| Condition State                    | Không tạo entity riêng           | Là tình trạng vật lý của Inventory Balance hoặc transaction item.            |

---

# **IV. Relationship và Connector đầy đủ để vẽ Visual Paradigm**

## **A. User, Role và Permission**

| \#  | Entity A   | Connector | Entity B        | Relationship Name  | Business Rule                                                                   |
| --- | ---------- | --------- | --------------- | ------------------ | ------------------------------------------------------------------------------- |
| R01 | Role       | 1:N       | Internal User   | is assigned to     | Một Role có nhiều Internal User; mỗi Internal User có đúng một Role chính.      |
| R02 | Role       | N:N       | Permission      | has permission     | Một Role có nhiều Permission; một Permission có thể thuộc nhiều Role.           |
| R03 | Role       | 1:N       | Role Permission | has                | Nếu vẽ entity trung gian, một Role có nhiều Role Permission.                    |
| R04 | Permission | 1:N       | Role Permission | is granted through | Nếu vẽ entity trung gian, một Permission xuất hiện trong nhiều Role Permission. |
| R04b| Internal User| 1:N       | Password Reset Token | has         | Một Internal User có thể tạo ra nhiều yêu cầu khôi phục mật khẩu.               |

Gợi ý vẽ: nên vẽ Role Permission để thể hiện rõ quan hệ N:N giữa Role và Permission.

---

## **B. Customer, Order và Catalog**

| \#  | Entity A     | Connector | Entity B                 | Relationship Name | Business Rule                                                                               |
| --- | ------------ | --------- | ------------------------ | ----------------- | ------------------------------------------------------------------------------------------- |
| R05 | Customer     | 1:N       | Order                    | places            | Một Customer có thể có nhiều Order; mỗi Order thuộc đúng một Customer.                      |
| R06 | Order        | 1:N       | Order Item               | contains          | Một Order có thể có nhiều Order Item; mỗi Order Item thuộc đúng một Order.                  |
| R07 | Catalog Item | 1:N       | Order Item               | is selected in    | Một Catalog Item có thể xuất hiện trong nhiều Order Item.                                   |
| R08 | Catalog Item | 1:N       | Item Price               | has price history | Một Catalog Item có nhiều mức giá theo thời gian.                                           |
| R09 | Catalog Item | N:N       | Catalog Item             | is composed of    | Một Package có thể gồm nhiều Catalog Item; một Catalog Item có thể nằm trong nhiều Package. |
| R10 | Catalog Item | 1:N       | Catalog Item Composition | acts as package   | Nếu vẽ entity trung gian, một Package có nhiều dòng composition.                            |
| R11 | Catalog Item | 1:N       | Catalog Item Composition | acts as component | Nếu vẽ entity trung gian, một item thành phần có thể nằm trong nhiều package.               |

Gợi ý vẽ: nên vẽ Catalog Item Composition để thể hiện rõ Package gồm các item thành phần.

---

## **C. Quotation và Payment**

| \#  | Entity A        | Connector | Entity B       | Relationship Name | Business Rule                                                                  |
| --- | --------------- | --------- | -------------- | ----------------- | ------------------------------------------------------------------------------ |
| R12 | Order           | 1:N       | Quotation      | receives          | Một Order có thể có nhiều phiên bản Quotation.                                 |
| R13 | Quotation       | 1:N       | Quotation Item | contains          | Một Quotation có nhiều Quotation Item.                                         |
| R14 | Catalog Item    | 1:N       | Quotation Item | is quoted in      | Một Catalog Item có thể xuất hiện trong nhiều Quotation Item.                  |
| R15 | Order           | 1:N       | Payment        | has payment       | Một Order có thể có nhiều Payment.                                             |
| R16 | Business Policy | 1:N       | Payment        | governs           | Một Business Policy có thể áp dụng cho nhiều Payment, ví dụ cọc hoặc hoàn cọc. |

Business rule cho Quotation:

Mỗi lần Manager thay đổi nội dung báo giá quan trọng như danh sách item, số lượng, giá, Supplier cost hoặc phụ phí, hệ thống tạo một Quotation version mới. Quotation cũ được giữ lại để theo dõi lịch sử. Một Order chỉ có tối đa một Quotation ở trạng thái Accepted tại một thời điểm.

---

## **D. Đổi ngày, hủy đơn và trạng thái Order**

| \#  | Entity A           | Connector | Entity B             | Relationship Name    | Business Rule                                                                |
| --- | ------------------ | --------- | -------------------- | -------------------- | ---------------------------------------------------------------------------- |
| R17 | Order              | 1:N       | Order Date Change    | has date changes     | Một Order có thể đổi ngày nhiều lần.                                         |
| R18 | Order              | 1:1       | Order Cancellation   | is cancelled through | Một Order có tối đa một hồ sơ hủy chính thức.                                |
| R19 | Business Policy    | 1:N       | Order Cancellation   | governs              | Một chính sách hoàn cọc có thể áp dụng cho nhiều trường hợp hủy Order.       |
| R20 | Order Cancellation | 1:1       | Payment              | results in refund    | Một Order Cancellation có thể tạo một Payment loại Refund nếu được hoàn cọc. |
| R21 | Order              | 1:N       | Order Status History | has status history   | Một Order có nhiều lần thay đổi trạng thái.                                  |
| R22 | Internal User      | 1:N       | Order Status History | changes              | Một Internal User có thể thực hiện nhiều lần thay đổi trạng thái.            |

---

## **E. Order Schedule, Task, Assignment, Survey và Progress**

| \#  | Entity A             | Connector | Entity B             | Relationship Name     | Business Rule                                                                 |
| --- | -------------------- | --------- | -------------------- | --------------------- | ----------------------------------------------------------------------------- |
| R23 | Order                | 1:N       | Order Schedule       | has schedule          | Một Order có nhiều mốc lịch vận hành.                                         |
| R24 | Internal User        | 1:N       | Order Schedule       | creates or updates    | Một Manager có thể tạo hoặc cập nhật nhiều Order Schedule.                    |
| R25 | Supplier Transaction | 1:N       | Order Schedule       | has supplier schedule | Một Supplier Transaction có thể có lịch nhận và lịch trả thiết bị.            |
| R26 | Order Schedule       | 1:1       | Work Task            | schedules             | Một mốc lịch vận hành có thể gắn với một Work Task cụ thể.                    |
| R27 | Order                | 1:N       | Work Task            | requires              | Một Order có thể có nhiều Work Task.                                          |
| R28 | Internal User        | N:N       | Work Task            | is assigned to        | Một User có thể tham gia nhiều Work Task; một Work Task có thể có nhiều User. |
| R29 | Work Task            | 1:N       | Assignment           | is staffed by         | Nếu vẽ entity trung gian, một Work Task có nhiều Assignment.                  |
| R30 | Internal User        | 1:N       | Assignment           | receives              | Nếu vẽ entity trung gian, một Internal User có nhiều Assignment.              |
| R31 | Work Task            | 1:1       | Survey Report        | produces              | Một Survey Task có tối đa một Survey Report.                                  |
| R32 | Internal User        | 1:N       | Survey Report        | records               | Một Leader Staff có thể ghi nhận nhiều Survey Report.                         |
| R33 | Work Task            | 1:N       | Task Progress Update | has progress updates  | Một Work Task có nhiều lần cập nhật tiến độ.                                  |
| R34 | Internal User        | 1:N       | Task Progress Update | records               | Một Leader Staff có thể ghi nhận nhiều Task Progress Update.                  |

Gợi ý vẽ: nên vẽ Assignment để thể hiện rõ quan hệ N:N giữa Internal User và Work Task.

---

## **F. Pick List**

| \#  | Entity A     | Connector | Entity B       | Relationship Name | Business Rule                                                   |
| --- | ------------ | --------- | -------------- | ----------------- | --------------------------------------------------------------- |
| R35 | Order        | 1:N       | Pick List      | has               | Một Order có thể có nhiều Pick List, ví dụ xuất kho và thu hồi. |
| R36 | Pick List    | 1:N       | Pick List Item | contains          | Một Pick List có nhiều Pick List Item.                          |
| R37 | Catalog Item | 1:N       | Pick List Item | is listed in      | Một Catalog Item có thể nằm trong nhiều Pick List.              |

---

## **G. Inventory và Warehouse**

| \#  | Entity A          | Connector | Entity B              | Relationship Name   | Business Rule                                                                                |
| --- | ----------------- | --------- | --------------------- | ------------------- | -------------------------------------------------------------------------------------------- |
| R38 | Warehouse         | 1:N       | Inventory Balance     | holds               | Một Warehouse có nhiều Inventory Balance.                                                    |
| R39 | Catalog Item      | 1:N       | Inventory Balance     | is stocked as       | Một Catalog Item có thể có Inventory Balance nếu được quản lý tồn kho.                       |
| R40 | Order Item        | 1:N       | Inventory Reservation | is reserved through | Một Order Item có thể tạo nhiều Inventory Reservation khi đổi ngày hoặc điều chỉnh số lượng. |
| R41 | Inventory Balance | 1:N       | Inventory Reservation | supplies            | Một Inventory Balance có thể được dùng cho nhiều Inventory Reservation.                      |

Business rule:

Doanh nghiệp hiện chỉ có một Warehouse chính. Inventory Reservation chỉ được kích hoạt khi Order đã được xác nhận sau khi thanh toán cọc.

---

## **H. Warehouse Transaction**

| \#  | Entity A              | Connector | Entity B                   | Relationship Name | Business Rule                                                  |
| --- | --------------------- | --------- | -------------------------- | ----------------- | -------------------------------------------------------------- |
| R42 | Order                 | 1:N       | Warehouse Transaction      | causes            | Một Order có thể phát sinh nhiều Warehouse Transaction.        |
| R43 | Warehouse             | 1:N       | Warehouse Transaction      | processes         | Một Warehouse xử lý nhiều Warehouse Transaction.               |
| R44 | Warehouse Transaction | 1:N       | Warehouse Transaction Item | contains          | Một Warehouse Transaction có nhiều Warehouse Transaction Item. |
| R45 | Catalog Item          | 1:N       | Warehouse Transaction Item | is moved in       | Một Catalog Item có thể xuất, nhập hoặc hoàn kho nhiều lần.    |
| R46 | Internal User         | 1:N       | Warehouse Transaction      | records           | Một Leader Staff có thể ghi nhận nhiều Warehouse Transaction.  |
| R47 | Internal User         | 1:N       | Warehouse Transaction      | confirms          | Một Manager có thể xác nhận nhiều Warehouse Transaction.       |

Business rule:

Technical Staff chỉ thực hiện vật lý như bốc xếp, kiểm đếm, vận chuyển và hoàn kho. Leader Staff nhập số lượng và tình trạng chính thức. Manager xác nhận hoàn kho.

---

## **I. Supplier Transaction và Supplier Payable**

| \#  | Entity A             | Connector | Entity B                  | Relationship Name | Business Rule                                                                          |
| --- | -------------------- | --------- | ------------------------- | ----------------- | -------------------------------------------------------------------------------------- |
| R48 | Supplier             | 1:N       | Supplier Transaction      | fulfills          | Một Supplier có thể có nhiều Supplier Transaction.                                     |
| R49 | Order                | 1:N       | Supplier Transaction      | requires          | Một Order có thể có nhiều Supplier Transaction khi thiếu thiết bị hoặc cần mua vật tư. |
| R50 | Supplier Transaction | 1:N       | Supplier Transaction Item | contains          | Một Supplier Transaction có nhiều Supplier Transaction Item.                           |
| R51 | Catalog Item         | 1:N       | Supplier Transaction Item | is sourced as     | Mọi item thuê/mua từ Supplier phải thuộc Catalog Item.                                 |
| R52 | Supplier Transaction | 1:1       | Supplier Payable          | generates         | Mỗi Supplier Transaction tạo tối đa một Supplier Payable.                              |
| R53 | Internal User        | 1:N       | Supplier Transaction      | records           | Một Manager có thể ghi nhận nhiều Supplier Transaction.                                |
| R54 | Internal User        | 1:N       | Supplier Transaction Item | receives          | Một Leader Staff có thể ghi nhận nhiều item nhận/trả từ Supplier.                      |

Business rule:

Supplier Payable chưa thanh toán hết không chặn Order chuyển sang Completed, miễn là Supplier Transaction và Supplier Payable đã được ghi nhận đầy đủ.

---

## **J. Change Request**

| \#  | Entity A        | Connector | Entity B            | Relationship Name | Business Rule                                                      |
| --- | --------------- | --------- | ------------------- | ----------------- | ------------------------------------------------------------------ |
| R55 | Order           | 1:N       | Change Request      | receives          | Một Order có thể có nhiều Change Request.                          |
| R56 | Change Request  | 1:N       | Change Request Item | contains          | Một Change Request có nhiều Change Request Item.                   |
| R57 | Catalog Item    | 1:N       | Change Request Item | is requested in   | Một Catalog Item có thể xuất hiện trong nhiều Change Request Item. |
| R58 | Order Item      | 1:N       | Change Request Item | is changed by     | Một Order Item có thể bị thay đổi bởi nhiều Change Request Item.   |
| R59 | Business Policy | 1:N       | Change Request      | governs           | Chính sách phí phát sinh có thể áp dụng cho nhiều Change Request.  |
| R60 | Internal User   | 1:N       | Change Request      | records           | Một Leader Staff có thể ghi nhận nhiều Change Request.             |
| R61 | Internal User   | 1:N       | Change Request      | approves          | Một Manager có thể phê duyệt nhiều Change Request.                 |

Business rule:

Leader Staff ghi nhận yêu cầu thêm, bớt hoặc thay thế tại hiện trường. Manager là người phê duyệt cuối. Change Request chỉ được áp dụng vào Order sau khi Manager phê duyệt.

---

## **K. Handover, Damage/Loss và Settlement**

| \#  | Entity A                  | Connector | Entity B           | Relationship Name  | Business Rule                                                                            |
| --- | ------------------------- | --------- | ------------------ | ------------------ | ---------------------------------------------------------------------------------------- |
| R62 | Order                     | 1:1       | Handover Record    | has handover       | Một Order có tối đa một Handover Record chính thức.                                      |
| R63 | Internal User             | 1:N       | Handover Record    | records            | Một Leader Staff có thể ghi nhận nhiều Handover Record.                                  |
| R64 | Internal User             | 1:N       | Handover Record    | confirms           | Một Manager có thể xác nhận nhiều Handover Record.                                       |
| R65 | Order                     | 1:N       | Damage/Loss Record | has damage reports | Một Order có thể có nhiều Damage/Loss Record.                                            |
| R66 | Damage/Loss Record        | 1:N       | Damage/Loss Item   | contains           | Một Damage/Loss Record có nhiều Damage/Loss Item.                                        |
| R67 | Catalog Item              | 1:N       | Damage/Loss Item   | is reported in     | Một Catalog Item có thể xuất hiện trong nhiều Damage/Loss Item.                          |
| R68 | Internal User             | 1:N       | Damage/Loss Record | records            | Một Leader Staff có thể ghi nhận nhiều Damage/Loss Record.                               |
| R69 | Internal User             | 1:N       | Damage/Loss Record | confirms           | Một Manager có thể xác nhận nhiều Damage/Loss Record.                                    |
| R70 | Internal User             | 1:N       | Damage/Loss Item   | is responsible for | Một Staff có thể chịu trách nhiệm cho nhiều Damage/Loss Item.                            |
| R71 | Supplier Transaction Item | 1:N       | Damage/Loss Item   | may be affected by | Một Supplier Transaction Item có thể phát sinh Damage/Loss Item nếu trả thiếu hoặc hỏng. |
| R72 | Order                     | 1:1       | Settlement         | is settled through | Một Order có tối đa một Settlement chính thức.                                           |
| R73 | Internal User             | 1:N       | Settlement         | records            | Một Leader Staff có thể hỗ trợ ghi nhận nhiều Settlement.                                |
| R74 | Internal User             | 1:N       | Settlement         | confirms           | Một Manager có thể xác nhận nhiều Settlement.                                            |

Business rule cho Damage/Loss:

Mỗi Damage/Loss Item có một Responsibility Type: Customer, Staff, Company hoặc Unresolved. Nếu Responsibility Type là Staff, hệ thống phải ghi nhận đúng một Internal User chịu trách nhiệm chính. Nếu lỗi không thuộc Staff, Damage/Loss Item không bắt buộc liên kết với Internal User chịu trách nhiệm.

---

## **L. Attendance và Staff Wage**

| \#  | Entity A         | Connector | Entity B       | Relationship Name   | Business Rule                                                                    |
| --- | ---------------- | --------- | -------------- | ------------------- | -------------------------------------------------------------------------------- |
| R75 | Assignment       | 1:N       | Attendance     | produces            | Một Assignment có thể phát sinh nhiều Attendance theo buổi làm.                  |
| R76 | Internal User    | 1:N       | Attendance     | submits             | Một Staff có thể ghi nhận nhiều Attendance.                                      |
| R77 | Internal User    | 1:N       | Attendance     | confirms            | Một Leader Staff hoặc Manager có thể xác nhận nhiều Attendance.                  |
| R78 | Wage Rule        | 1:N       | Attendance     | determines rate for | Một Wage Rule có thể áp dụng cho nhiều Attendance.                               |
| R79 | Internal User    | 1:N       | Wage Summary   | receives            | Một Staff có nhiều Wage Summary theo tháng.                                      |
| R80 | Wage Summary     | 1:N       | Attendance     | summarizes          | Một Wage Summary tổng hợp nhiều Attendance.                                      |
| R81 | Wage Summary     | 1:N       | Wage Deduction | includes            | Một Wage Summary có thể có nhiều Wage Deduction.                                 |
| R82 | Damage/Loss Item | 1:N       | Wage Deduction | may cause           | Một Damage/Loss Item có thể tạo khoản khấu trừ tiền công nếu lỗi thuộc về Staff. |

Business rule:

Leader Staff không được tự xác nhận chấm công của chính mình. Manager xác nhận Attendance của Leader Staff. Leader Staff xác nhận Attendance của Technical Staff trong task mình phụ trách.

---

## **M. Evidence File**

| \#  | Entity A             | Connector | Entity B      | Relationship Name | Business Rule                                                                   |
| --- | -------------------- | --------- | ------------- | ----------------- | ------------------------------------------------------------------------------- |
| R83 | Survey Report        | 1:N       | Evidence File | is supported by   | Một Survey Report có thể có nhiều hình ảnh khảo sát.                            |
| R84 | Payment              | 1:N       | Evidence File | is supported by   | Một Payment có thể có nhiều minh chứng thanh toán.                              |
| R85 | Task Progress Update | 1:N       | Evidence File | is supported by   | Một cập nhật tiến độ có thể có nhiều hình ảnh hiện trường.                      |
| R86 | Change Request       | 1:N       | Evidence File | is supported by   | Một Change Request có thể có nhiều hình ảnh, tin nhắn hoặc minh chứng trao đổi. |
| R87 | Handover Record      | 1:N       | Evidence File | is supported by   | Một Handover Record nên có hình ảnh hoặc tin nhắn xác nhận bàn giao.            |
| R88 | Damage/Loss Record   | 1:N       | Evidence File | is supported by   | Một Damage/Loss Record bắt buộc có hình ảnh minh chứng.                         |
| R89 | Settlement           | 1:N       | Evidence File | is supported by   | Một Settlement nên có minh chứng Customer đồng ý hoặc bằng chứng trao đổi.      |

Business rule cho Evidence File:

Evidence File là minh chứng nghiệp vụ được đính kèm cho Survey Report, Payment, Task Progress Update, Change Request, Handover Record, Damage/Loss Record hoặc Settlement. Evidence File bắt buộc đối với Payment tiền mặt/chuyển khoản thủ công, Damage/Loss Record, tranh chấp và các trường hợp Customer agreement quan trọng như bàn giao hoặc settlement.

---

# **V. Bảng connector rút gọn để vẽ Visual Paradigm**

| \#  | Entity A                  | Connector | Entity B                   |
| --- | ------------------------- | --------- | -------------------------- |
| 1   | Role                      | 1:N       | Internal User              |
| 3   | Role                      | 1:N       | Role Permission            |
| 4   | Permission                | 1:N       | Role Permission            |
| 4b  | Internal User             | 1:N       | Password Reset Token       |
| 5   | Customer                  | 1:N       | Order                      |
| 6   | Order                     | 1:N       | Order Item                 |
| 7   | Catalog Item              | 1:N       | Order Item                 |
| 8   | Catalog Item              | 1:N       | Item Price                 |
| 10  | Catalog Item              | 1:N       | Catalog Item Composition   |
| 11  | Order                     | 1:N       | Quotation                  |
| 12  | Quotation                 | 1:N       | Quotation Item             |
| 13  | Catalog Item              | 1:N       | Quotation Item             |
| 14  | Order                     | 1:N       | Payment                    |
| 15  | Business Policy           | 1:N       | Payment                    |
| 16  | Order                     | 1:N       | Order Date Change          |
| 17  | Order                     | 1:1       | Order Cancellation         |
| 18  | Business Policy           | 1:N       | Order Cancellation         |
|     | Business Policy           | 1:N       | Order Date Change          |
| 19  | Order Cancellation        | 1:1       | Payment                    |
| 20  | Order                     | 1:N       | Order Status History       |
| 21  | Internal User             | 1:N       | Order Status History       |
| 22  | Order                     | 1:N       | Order Schedule             |
| 23  | Internal User             | 1:N       | Order Schedule             |
| 24  | Supplier Transaction      | 1:N       | Order Schedule             |
| 25  | Order Schedule            | 1:1       | Work Task                  |
| 26  | Order                     | 1:N       | Work Task                  |
| 28  | Work Task                 | 1:N       | Assignment                 |
| 29  | Internal User             | 1:N       | Assignment                 |
| 30  | Work Task                 | 1:1       | Survey Report              |
| 31  | Internal User             | 1:N       | Survey Report              |
| 32  | Work Task                 | 1:N       | Task Progress Update       |
| 33  | Internal User             | 1:N       | Task Progress Update       |
| 34  | Order                     | 1:N       | Pick List                  |
| 35  | Pick List                 | 1:N       | Pick List Item             |
| 36  | Catalog Item              | 1:N       | Pick List Item             |
| 37  | Warehouse                 | 1:N       | Inventory Balance          |
| 38  | Catalog Item              | 1:N       | Inventory Balance          |
| 39  | Order Item                | 1:N       | Inventory Reservation      |
| 40  | Inventory Balance         | 1:N       | Inventory Reservation      |
| 41  | Order                     | 1:N       | Warehouse Transaction      |
| 42  | Warehouse                 | 1:N       | Warehouse Transaction      |
| 43  | Warehouse Transaction     | 1:N       | Warehouse Transaction Item |
| 44  | Catalog Item              | 1:N       | Warehouse Transaction Item |
| 45  | Internal User             | 1:N       | Warehouse Transaction      |
| 46  | Supplier                  | 1:N       | Supplier Transaction       |
| 47  | Order                     | 1:N       | Supplier Transaction       |
| 48  | Supplier Transaction      | 1:N       | Supplier Transaction Item  |
| 49  | Catalog Item              | 1:N       | Supplier Transaction Item  |
| 50  | Supplier Transaction      | 1:1       | Supplier Payable           |
| 51  | Internal User             | 1:N       | Supplier Transaction       |
| 52  | Internal User             | 1:N       | Supplier Transaction Item  |
| 53  | Order                     | 1:N       | Change Request             |
| 54  | Change Request            | 1:N       | Change Request Item        |
| 55  | Catalog Item              | 1:N       | Change Request Item        |
| 56  | Order Item                | 1:N       | Change Request Item        |
| 57  | Business Policy           | 1:N       | Change Request             |
| 58  | Internal User             | 1:N       | Change Request             |
| 59  | Order                     | 1:1       | Handover Record            |
| 60  | Internal User             | 1:N       | Handover Record            |
| 61  | Order                     | 1:N       | Damage/Loss Record         |
| 62  | Damage/Loss Record        | 1:N       | Damage/Loss Item           |
| 63  | Catalog Item              | 1:N       | Damage/Loss Item           |
| 64  | Internal User             | 1:N       | Damage/Loss Item           |
| 65  | Supplier Transaction Item | 1:N       | Damage/Loss Item           |
| 66  | Order                     | 1:1       | Settlement                 |
| 67  | Internal User             | 1:N       | Settlement                 |
| 68  | Assignment                | 1:N       | Attendance                 |
| 69  | Internal User             | 1:N       | Attendance                 |
| 70  | Wage Rule                 | 1:N       | Attendance                 |
| 71  | Internal User             | 1:N       | Wage Summary               |
| 72  | Wage Summary              | 1:N       | Attendance                 |
| 73  | Wage Summary              | 1:N       | Wage Deduction             |
| 74  | Damage/Loss Item          | 1:N       | Wage Deduction             |
| 75  | Survey Report             | 1:N       | Evidence File              |
| 76  | Payment                   | 1:N       | Evidence File              |
| 77  | Task Progress Update      | 1:N       | Evidence File              |
| 78  | Change Request            | 1:N       | Evidence File              |
| 79  | Handover Record           | 1:N       | Evidence File              |
| 80  | Damage/Loss Record        | 1:N       | Evidence File              |
| 81  | Settlement                | 1:N       | Evidence File              |

---

# **VI. Các quan hệ N:N nên vẽ bằng entity trung gian**

| Quan hệ N:N nghiệp vụ         | Entity trung gian nên vẽ | Cách vẽ trong Visual Paradigm                                                                                                  |
| ----------------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| Role N:N Permission           | Role Permission          | Role 1:N Role Permission; Permission 1:N Role Permission                                                                       |
| Internal User N:N Work Task   | Assignment               | Internal User 1:N Assignment; Work Task 1:N Assignment                                                                         |
| Catalog Item N:N Catalog Item | Catalog Item Composition | Catalog Item 1:N Catalog Item Composition với vai trò Package; Catalog Item 1:N Catalog Item Composition với vai trò Component |

Nếu vẽ entity trung gian rồi, không cần vẽ thêm đường N:N trực tiếp để tránh trùng nghĩa.

---

# **VII. Bố cục gợi ý khi vẽ trong Visual Paradigm**

## **Vùng 1: User & Access**

- Role
- Permission
- Role Permission
- Internal User

## **Vùng 2: Order Core**

- Customer
- Order
- Order Item
- Catalog Item
- Catalog Item Composition
- Item Price
- Quotation
- Quotation Item
- Payment
- Business Policy

## **Vùng 3: Schedule & Operation**

- Order Schedule
- Work Task
- Assignment
- Survey Report
- Task Progress Update
- Pick List
- Pick List Item
- Attendance

## **Vùng 4: Inventory & Supplier**

- Warehouse
- Inventory Balance
- Inventory Reservation
- Warehouse Transaction
- Warehouse Transaction Item
- Supplier
- Supplier Transaction
- Supplier Transaction Item
- Supplier Payable

## **Vùng 5: Closing, Wage & Evidence**

- Change Request
- Change Request Item
- Handover Record
- Damage/Loss Record
- Damage/Loss Item
- Settlement
- Wage Summary
- Wage Deduction
- Evidence File

---

# **VIII. Kết luận bản cuối**

Bản Conceptual ERD cuối cùng gồm:

- 44 entity.
- 81 relationship/connector.
- Connector được chuẩn hóa thành 3 dạng: 1:1, 1:N, N:N.
- Có bổ sung Order Schedule để thể hiện timeline vận hành.
- Không có Vehicle vì doanh nghiệp chỉ có một xe.
- Không có Service Request vì Order được tạo ngay từ yêu cầu ban đầu.
- Không có Price List vì dùng Item Price theo Catalog Item.
- Không có Responsibility Allocation vì nếu Staff gây lỗi thì xác định trực tiếp Staff chịu trách nhiệm.
- Evidence File được giữ lại và dùng cho payment, handover, damage/loss, settlement, customer agreement và tranh chấp.

# **Entities Description – English Version**

| \#  | Entity                     | Description                                                                                                                                                                           |
| --- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Role                       | Defines the main responsibility group of an internal user, such as Admin, Manager, Leader Staff, or Technical Staff.                                                                  |
| 2   | Permission                 | Defines a system function or access right that can be granted to a role.                                                                                                              |
| 3   | Role Permission            | Represents the permissions assigned to a specific role.                                                                                                                               |
| 4   | Internal User              | Stores information about internal users who access the system, including Admin, Manager, Leader Staff, and Technical Staff.                                                           |
| 5   | Customer                   | Stores customer information used for order creation, quotation, payment, handover, and settlement tracking.                                                                           |
| 6   | Supplier                   | Stores supplier information for rental, purchase, receiving, returning, compensation, and payable tracking.                                                                           |
| 7   | Warehouse                  | Represents the company’s internal storage location for equipment and inventory management.                                                                                            |
| 8   | Catalog Item               | Represents a service, equipment, material, consumable item, or package used in quotations and orders.                                                                                 |
| 9   | Catalog Item Composition   | Defines the relationship between a package item and its component catalog items.                                                                                                      |
| 10  | Item Price                 | Stores the price history of a catalog item based on its effective period.                                                                                                             |
| 11  | Business Policy            | Stores business rules such as deposit policy, refund policy, date change policy, and additional fee policy.                                                                           |
| 12  | Wage Rule                  | Stores staff wage rules based on role, working session, and effective period.                                                                                                         |
| 13  | Order                      | Represents a customer service order from initial request recording to completion or cancellation.                                                                                     |
| 14  | Order Item                 | Represents a catalog item included in an order, including quantity and agreed price.                                                                                                  |
| 15  | Quotation                  | Represents a quotation version created for an order.                                                                                                                                  |
| 16  | Quotation Item             | Represents a catalog item, quantity, and quoted price within a quotation.                                                                                                             |
| 17  | Payment                    | Stores payment records related to an order, such as deposit, final payment, on-site payment, or refund.                                                                               |
| 18  | Order Date Change          | Records each approved or rejected date change request for an order.                                                                                                                   |
| 19  | Order Cancellation         | Records the cancellation information of an order, including reason and refund result.                                                                                                 |
| 20  | Order Status History       | Stores the history of status changes throughout the order lifecycle.                                                                                                                  |
| 21  | Order Schedule             | Stores important operational schedule milestones of an order, such as survey, checkout, transportation, installation, collection, warehouse return, and supplier receiving/returning. |
| 22  | Work Task                  | Represents an operational task related to an order, such as survey, preparation, transportation, installation, collection, or warehouse return.                                       |
| 23  | Assignment                 | Represents the assignment of an internal user to a specific work task.                                                                                                                |
| 24  | Survey Report              | Stores field survey results submitted by Leader Staff.                                                                                                                                |
| 25  | Task Progress Update       | Records progress updates, notes, or operational issues during task execution.                                                                                                         |
| 26  | Pick List                  | Represents a list of items prepared for checkout, installation, collection, or warehouse return.                                                                                      |
| 27  | Pick List Item             | Represents a catalog item and required quantity in a pick list.                                                                                                                       |
| 28  | Inventory Balance          | Stores the quantity of a catalog item in the warehouse according to availability and physical condition.                                                                              |
| 29  | Inventory Reservation      | Records inventory quantities reserved for a confirmed order.                                                                                                                          |
| 30  | Warehouse Transaction      | Represents a warehouse movement event, such as checkout, return, or inventory adjustment.                                                                                             |
| 31  | Warehouse Transaction Item | Represents a catalog item and quantity included in a warehouse transaction.                                                                                                           |
| 32  | Supplier Transaction       | Records rental or purchase transactions with a supplier for an order.                                                                                                                 |
| 33  | Supplier Transaction Item  | Represents an item, quantity, cost, and receiving/returning condition within a supplier transaction.                                                                                  |
| 34  | Supplier Payable           | Represents the payable amount generated from a supplier transaction.                                                                                                                  |
| 35  | Change Request             | Records on-site requests to add, remove, or replace items in an order.                                                                                                                |
| 36  | Change Request Item        | Represents a specific item affected by a change request.                                                                                                                              |
| 37  | Handover Record            | Records handover information, evidence, and customer agreement after installation.                                                                                                    |
| 38  | Damage/Loss Record         | Records damaged or lost items found during collection or warehouse return.                                                                                                            |
| 39  | Damage/Loss Item           | Represents a specific damaged or lost catalog item, including quantity and responsibility.                                                                                            |
| 40  | Settlement                 | Represents the final settlement of an order, including order value, additional fees, compensation, payments, and remaining amount.                                                    |
| 41  | Attendance                 | Records a staff working session based on an assignment.                                                                                                                               |
| 42  | Wage Summary               | Summarizes staff wages for a monthly payment period.                                                                                                                                  |
| 43  | Wage Deduction             | Records deductions from staff wages, such as compensation for damaged or lost items.                                                                                                  |
| 44  | Evidence File              | Stores supporting evidence such as images, receipts, payment proof, handover evidence, or customer agreement messages.                                                                |

---

# **Bảng mô tả Entity – Phiên bản Tiếng Việt**

| \#  | Entity                     | Mô tả                                                                                                                                     |
| --- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Role                       | Lưu nhóm vai trò chính của người dùng nội bộ, bao gồm Admin, Manager, Leader Staff và Technical Staff.                                    |
| 2   | Permission                 | Lưu các quyền hoặc chức năng mà hệ thống cho phép gán cho từng vai trò.                                                                   |
| 3   | Role Permission            | Thể hiện các quyền được gán cho một vai trò cụ thể.                                                                                       |
| 4   | Internal User              | Lưu thông tin người dùng nội bộ có tài khoản sử dụng hệ thống, bao gồm Admin, Manager, Leader Staff và Technical Staff.                   |
| 5   | Customer                   | Lưu thông tin khách hàng phục vụ tạo đơn hàng, báo giá, thanh toán, bàn giao và quyết toán.                                               |
| 6   | Supplier                   | Lưu thông tin nhà cung cấp phục vụ thuê/mua thiết bị, nhận/trả hàng, bồi thường và theo dõi công nợ.                                      |
| 7   | Warehouse                  | Đại diện cho kho nội bộ của doanh nghiệp dùng để quản lý thiết bị và tồn kho.                                                             |
| 8   | Catalog Item               | Đại diện cho dịch vụ, thiết bị, vật tư, vật tư tiêu hao hoặc gói dịch vụ được sử dụng trong báo giá và đơn hàng.                          |
| 9   | Catalog Item Composition   | Mô tả cấu thành của một package, tức package gồm những Catalog Item thành phần nào.                                                       |
| 10  | Item Price                 | Lưu lịch sử giá của từng Catalog Item theo thời gian hiệu lực.                                                                            |
| 11  | Business Policy            | Lưu các chính sách nghiệp vụ như chính sách cọc, hoàn cọc, đổi ngày và phí phát sinh.                                                     |
| 12  | Wage Rule                  | Lưu quy tắc tiền công Staff theo vai trò, buổi làm và thời gian áp dụng.                                                                  |
| 13  | Order                      | Đại diện cho đơn hàng của Customer từ lúc tiếp nhận yêu cầu đến khi hoàn thành hoặc bị hủy.                                               |
| 14  | Order Item                 | Lưu các hạng mục thuộc đơn hàng, bao gồm Catalog Item, số lượng và giá đã thỏa thuận.                                                     |
| 15  | Quotation                  | Đại diện cho một phiên bản báo giá được lập cho Order.                                                                                    |
| 16  | Quotation Item             | Lưu từng dòng hạng mục trong báo giá, bao gồm Catalog Item, số lượng và giá báo.                                                          |
| 17  | Payment                    | Lưu các khoản thanh toán liên quan đến Order như tiền cọc, thanh toán cuối, thanh toán tại hiện trường hoặc hoàn tiền.                    |
| 18  | Order Date Change          | Lưu từng lần yêu cầu đổi ngày của Order, bao gồm kết quả được duyệt hoặc bị từ chối.                                                      |
| 19  | Order Cancellation         | Lưu thông tin hủy Order, bao gồm lý do hủy và kết quả hoàn cọc.                                                                           |
| 20  | Order Status History       | Lưu lịch sử thay đổi trạng thái trong vòng đời của Order.                                                                                 |
| 21  | Order Schedule             | Lưu các mốc lịch vận hành quan trọng của Order như khảo sát, xuất kho, vận chuyển, thi công, thu hồi, hoàn kho và lịch nhận/trả Supplier. |
| 22  | Work Task                  | Đại diện cho nhiệm vụ vận hành thuộc Order, ví dụ khảo sát, chuẩn bị, vận chuyển, thi công, thu hồi hoặc hoàn kho.                        |
| 23  | Assignment                 | Lưu việc phân công một người dùng nội bộ tham gia một Work Task cụ thể.                                                                   |
| 24  | Survey Report              | Lưu kết quả khảo sát hiện trường do Leader Staff nhập và gửi.                                                                             |
| 25  | Task Progress Update       | Lưu các lần cập nhật tiến độ, ghi chú hoặc vấn đề phát sinh trong quá trình thực hiện task.                                               |
| 26  | Pick List                  | Đại diện cho danh sách thiết bị/vật tư cần chuẩn bị, xuất kho, thi công, thu hồi hoặc hoàn kho.                                           |
| 27  | Pick List Item             | Lưu từng Catalog Item và số lượng cần xử lý trong Pick List.                                                                              |
| 28  | Inventory Balance          | Lưu số lượng Catalog Item trong kho theo trạng thái khả dụng và tình trạng vật lý.                                                        |
| 29  | Inventory Reservation      | Lưu số lượng tồn kho được giữ cho một Order đã xác nhận.                                                                                  |
| 30  | Warehouse Transaction      | Đại diện cho một giao dịch kho như xuất kho, hoàn kho hoặc điều chỉnh tồn kho.                                                            |
| 31  | Warehouse Transaction Item | Lưu từng Catalog Item và số lượng trong một Warehouse Transaction.                                                                        |
| 32  | Supplier Transaction       | Lưu giao dịch thuê hoặc mua thiết bị/vật tư từ Supplier cho một Order.                                                                    |
| 33  | Supplier Transaction Item  | Lưu item, số lượng, chi phí và tình trạng nhận/trả trong một Supplier Transaction.                                                        |
| 34  | Supplier Payable           | Lưu khoản công nợ phải trả phát sinh từ một Supplier Transaction.                                                                         |
| 35  | Change Request             | Lưu yêu cầu thay đổi tại hiện trường như thêm, bớt hoặc thay thế item trong Order.                                                        |
| 36  | Change Request Item        | Lưu từng item cụ thể bị ảnh hưởng bởi Change Request.                                                                                     |
| 37  | Handover Record            | Lưu thông tin bàn giao sau thi công, minh chứng và trạng thái đồng ý của Customer.                                                        |
| 38  | Damage/Loss Record         | Lưu hồ sơ tổng hợp các thiết bị bị hỏng hoặc mất trong quá trình thu hồi hoặc hoàn kho.                                                   |
| 39  | Damage/Loss Item           | Lưu từng Catalog Item bị hỏng/mất, bao gồm số lượng và trách nhiệm bồi thường.                                                            |
| 40  | Settlement                 | Đại diện cho quyết toán cuối cùng của Order, bao gồm giá trị đơn hàng, phí phát sinh, bồi thường, thanh toán và số tiền còn lại.          |
| 41  | Attendance                 | Lưu một buổi làm của Staff dựa trên Assignment.                                                                                           |
| 42  | Wage Summary               | Tổng hợp tiền công Staff theo kỳ thanh toán hằng tháng.                                                                                   |
| 43  | Wage Deduction             | Lưu các khoản khấu trừ tiền công, ví dụ bồi thường do làm hỏng hoặc mất thiết bị.                                                         |
| 44  | Evidence File              | Lưu các tệp minh chứng như hình ảnh, biên nhận, bằng chứng thanh toán, hình ảnh bàn giao hoặc tin nhắn xác nhận của Customer.             |
