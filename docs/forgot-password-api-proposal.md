# Đề xuất API thay thế cho Forgot Password

> Thay thế cho `POST /auth/forgot-password` hiện tại (trong `01-auth.md`), vốn chỉ ghi nhận yêu cầu và yêu cầu liên hệ Admin — không cho phép người dùng tự hoàn tất việc khôi phục mật khẩu.

---

## 1. Vấn đề của API hiện tại

API hiện tại chỉ làm **một việc mơ hồ**: ghi nhận yêu cầu rồi báo "liên hệ Admin", không có bước hoàn tất nào trong REST API — toàn bộ việc đặt lại mật khẩu bị đẩy sang quy trình **ngoài hệ thống** (Admin gọi điện xác minh → dùng UC-12 `POST /users/{id}/reset-password`). Điều này gây ra một số vấn đề:

- **Không khớp với đặc tả UC**: UC mô tả rõ một luồng có *Postcondition: "use case completed successfully"* với message hoàn tất `MSG-AUTH0303` — nghĩa là người dùng **tự hoàn tất được**, không phải chỉ dừng ở "đã ghi nhận, chờ Admin".
- **Không có cơ chế xác minh danh tính** (OTP/token) trước khi cho phép đặt mật khẩu mới — nếu giữ nguyên kiểu "chờ Admin" thì an toàn nhưng trải nghiệm kém; nếu thêm bước đặt mật khẩu mới mà thiếu xác minh thì lại mất an toàn.
- **Một endpoint nhưng hai trách nhiệm lẫn lộn**: vừa "ghi nhận yêu cầu" vừa ngầm hiểu là "đặt lại mật khẩu", khiến FE không biết nên thiết kế màn hình tiếp theo như thế nào.

---

## 2. Phương án đề xuất: luồng 3 bước (request → verify → reset)

Pattern chuẩn cho self-service forgot password, dùng OTP/token có hạn gửi qua email hoặc SMS đã đăng ký.

| Bước | Endpoint | Mục đích |
|---|---|---|
| 1 | `POST /auth/forgot-password` | Người dùng nhập `username`/`email`. Hệ thống tạo OTP/token có hạn (vd 10 phút), gửi qua email/SMS đã đăng ký. |
| 2 | `POST /auth/forgot-password/verify` | Người dùng nhập OTP/token nhận được. Hệ thống xác minh, nếu đúng → cấp `reset_token` ngắn hạn (vd 5 phút, dùng một lần). |
| 3 | `POST /auth/reset-password` | Người dùng gửi `reset_token` + `new_password`. Hệ thống xác thực token, đặt mật khẩu mới, vô hiệu hóa token. |

---

## 3. Chi tiết endpoint

### Bước 1 — Yêu cầu khôi phục

`POST /auth/forgot-password`

**Request body**
```json
{ "username": "manager01" }
```

**Response `200`**
*(luôn trả về cùng message dù tài khoản có tồn tại hay không, để tránh user enumeration attack)*
```json
{
  "success": true,
  "code": "MSG-AUTH0301-OK",
  "message": "Nếu tài khoản tồn tại, mã xác nhận đã được gửi",
  "data": null
}
```

---

### Bước 2 — Xác minh OTP

`POST /auth/forgot-password/verify`

**Request body**
```json
{ "username": "manager01", "otp": "839204" }
```

**Response `200`**
```json
{
  "success": true,
  "data": { "reset_token": "rst_8f2c1a...", "expires_in": 300 }
}
```

**Lỗi có thể gặp**

| HTTP | Khi nào |
|------|---------|
| 400 | OTP sai hoặc hết hạn |
| 429 | Thử quá số lần cho phép (chống brute-force) |

---

### Bước 3 — Đặt mật khẩu mới

`POST /auth/reset-password`

**Request body**
```json
{ "reset_token": "rst_8f2c1a...", "new_password": "newSecret456" }
```

**Response `200`**
```json
{
  "success": true,
  "code": "MSG-AUTH0303",
  "message": "Đặt lại mật khẩu thành công",
  "data": null
}
```

**Lỗi có thể gặp**

| HTTP | code | Khi nào |
|------|------|---------|
| 400 | MSG-AUTH0301 | Thiếu field bắt buộc |
| 400/409 | MSG-AUTH0302 | `reset_token` không hợp lệ / hết hạn / đã sử dụng, hoặc mật khẩu mới không đạt chính sách |

---

## 4. Business rules đi kèm (map theo BR-AUTH trong đặc tả UC)

| BR | Mô tả | Áp dụng |
|---|---|---|
| **BR-AUTH01** | Chỉ actor hợp lệ thực hiện | Không yêu cầu token đăng nhập (vì user đang mất quyền truy cập), nhưng giới hạn theo tài khoản tồn tại + đúng kênh liên hệ đã đăng ký |
| **BR-AUTH02** | Dữ liệu hợp lệ trước khi lưu | Validate OTP; validate `new_password` theo chính sách mật khẩu trước khi update |
| **BR-AUTH03** | Cập nhật trạng thái nghiệp vụ | Sau khi reset thành công, vô hiệu hóa toàn bộ token đăng nhập hiện có của user (buộc đăng nhập lại ở mọi thiết bị) |
| **BR-AUTH04** | Ghi audit log | Ghi log ở cả 3 bước (đặc biệt bước 3 — ai, lúc nào, từ IP nào) |

---

## 5. Lưu ý triển khai bổ sung

- **Rate limiting** bắt buộc ở bước 1 và 2 (chống spam OTP / brute-force OTP) — hiện chưa có trong tài liệu gốc, cần bổ sung.
- **Token một lần dùng**: `reset_token` phải bị hủy ngay sau khi dùng thành công hoặc hết hạn, lưu ở bảng riêng (vd `password_reset_tokens`) chứ không tái dùng cơ chế JWT đăng nhập.
- Vẫn nên **giữ song song UC-12** (`POST /users/{id}/reset-password` của Admin) như một kênh dự phòng cho trường hợp user không còn quyền truy cập email/SĐT đã đăng ký — không loại bỏ, chỉ bổ sung luồng tự phục vụ.

> ⚠️ **Cần xác nhận:** gửi OTP qua **email hay SMS**? Quyết định này ảnh hưởng tới việc bảng `users` có cần bắt buộc `email`/`phone` hợp lệ và duy nhất hay không.
