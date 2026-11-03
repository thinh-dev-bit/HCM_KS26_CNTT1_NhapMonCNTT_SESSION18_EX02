# Phần 1. Phân tích
Để giữ trạng thái người dùng (Giỏ hàng, Phiên đăng nhập), chúng ta phải hiểu rõ sự khác biệt giữa hai cơ chế lưu trữ của trình duyệt:

| Tiêu chí | Browser Cache (Bộ nhớ đệm) | HTTP Cookies (Thẻ nhận diện) |
| :--- | :--- | :--- |
| **Dung lượng** | Rất lớn (Tính bằng MB hoặc GB). | Siêu nhỏ (Giới hạn tối đa **~4KB** / Cookie). |
| **Bản chất dữ liệu** | Lưu tài nguyên tĩnh: Hình ảnh (logo), file CSS (màu sắc), file JavaScript. Giúp load trang cực nhanh (Cache Hit). | Lưu thông tin định danh dạng chuỗi (Session Token, User ID, User Preferences). |
| **Giao tiếp Server** | Trình duyệt tự đọc từ RAM/SSD, Server không biết. | Đính kèm tự động trong **mọi gói tin HTTP Request** gửi lên Server để nhận diện. |
| **Lựa chọn cho bài toán**| **KHÔNG PHÙ HỢP** để lưu phiên đăng nhập. | **LỰA CHỌN DUY NHẤT** để Server nhớ mặt shipper là ai. |

# Phần 2. Triển khai 

**Vấn đề hiện tại:** Shipper cứ đóng trình duyệt là bị đăng xuất, do hệ thống đang dùng **Session Cookie** (Cookie tạm thời, tự hủy khi đóng cửa sổ).

**Giải pháp:** Chuyển sang **Persistent Cookie** (Cookie bền vững) kết hợp với các cờ bảo mật theo cấu trúc sau:

**Cấu trúc mẩu tin Cookie (Set-Cookie Header từ Server):**
```http
Set-Cookie: shipper_session_token=abc123xyz890; Max-Age=2592000; HttpOnly; Secure; SameSite=Strict
```
---- 

# Phần 3. Kiểm thử
Chẩn đoán: Trình duyệt đang lấy file CSS cũ từ Memory Cache (Browser Cache) thay vì tải file mới từ Server.

**Hướng dẫn thao tác xử lý (Hard Reload):**

**Cách 1 (Phím tắt):** Nhấn tổ hợp phím Ctrl + Shift + R (trên Windows) để cưỡng chế trình duyệt xóa bộ nhớ đệm của trang hiện tại và tải lại toàn bộ tài nguyên gốc.

**Cách 2 (Dùng DevTools F12):**

Nhấn F12 để mở Chrome DevTools.

Chuyển sang tab Network (Mạng).

Tích chọn vào ô Disable Cache (Tắt bộ nhớ đệm) ở thanh công cụ.

Nhấn tải lại trang (F5). (Cách này rất hữu ích cho lập trình viên trong suốt quá trình ngồi code và test giao diện liên tục).