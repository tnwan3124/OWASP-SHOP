# Broken Authentication

Trong nhiệm vụ này, chúng ta sẽ xem xét việc khai thác xác thực thông qua các lỗ hổng khác nhau. Khi nói về các lỗ hổng trong xác thực, chúng ta bao gồm các cơ chế dễ bị thao túng. Các cơ chế này, được liệt kê dưới đây, là những gì chúng ta sẽ khai thác.

- Mật khẩu yếu trong các tài khoản có đặc quyền cao
- Các trang quên mật khẩu

**Thông tin thêm:** [Broken Authentication](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A2-Broken_Authentication)

**Giải thích:**

Nhiệm vụ này tập trung vào việc tìm kiếm và khai thác các lỗ hổng trong quy trình xác thực của một ứng dụng hoặc hệ thống. Các lỗ hổng xác thực có thể cho phép kẻ tấn công truy cập trái phép vào hệ thống hoặc dữ liệu mà không cần biết thông tin đăng nhập hợp lệ.

Một số lỗ hổng xác thực phổ biến bao gồm:

- **Mật khẩu yếu trong các tài khoản có đặc quyền cao:** Nếu một tài khoản quản trị viên hoặc tài khoản có quyền truy cập nhạy cảm khác sử dụng mật khẩu yếu, kẻ tấn công có thể dễ dàng đoán hoặc bẻ khóa mật khẩu để giành quyền kiểm soát tài khoản.
- **Các trang quên mật khẩu:** Nếu một trang quên mật khẩu được thiết kế kém, kẻ tấn công có thể khai thác nó để đặt lại mật khẩu của người dùng khác hoặc để lấy thông tin cá nhân nhạy cảm.

Các lỗ hổng xác thực khác có thể bao gồm:

- **Xác thực dựa trên phiên không an toàn:** Nếu mã thông báo phiên không được tạo hoặc quản lý đúng cách, kẻ tấn công có thể đánh cắp hoặc giả mạo chúng để truy cập vào hệ thống dưới danh nghĩa của người dùng khác.
- **Lỗi logic trong quy trình xác thực:** Nếu có lỗi logic trong cách ứng dụng xử lý xác thực, kẻ tấn công có thể khai thác chúng để bỏ qua các bước xác thực hoặc để truy cập trái phép vào hệ thống.

Khai thác các lỗ hổng xác thực có thể có hậu quả nghiêm trọng, chẳng hạn như mất dữ liệu, đánh cắp thông tin cá nhân, hoặc thậm chí chiếm quyền kiểm soát toàn bộ hệ thống. Do đó, điều quan trọng là phải thiết kế và triển khai các cơ chế xác thực mạnh mẽ và kiểm tra chúng thường xuyên để tìm lỗ hổng.

---

**Bruteforce the Administrator account's password!**

Chúng ta đã sử dụng SQL Injection để đăng nhập vào tài khoản Administrator, nhưng vẫn chưa biết mật khẩu. Hãy thử một cuộc tấn công vét cạn (brute-force attack)! Một lần nữa, chúng ta sẽ bắt một yêu cầu đăng nhập, nhưng thay vì gửi nó qua proxy, chúng ta sẽ gửi nó đến Intruder.

Đi tới Positions và sau đó chọn nút Clear § (Xóa §). Trong trường mật khẩu, hãy đặt hai ký tự § vào bên trong dấu ngoặc kép. Để làm rõ, § § không phải là hai đầu vào riêng biệt mà là cách Burp thực hiện dấu ngoặc kép, ví dụ: "". Yêu cầu sẽ trông giống như hình ảnh bên dưới.

![image.png](<img/4.png>)

Đối với payload, chúng ta sẽ sử dụng best1050.txt từ Seclists. (Bạn có thể cài đặt thông qua: `apt-get install seclists`)
Bạn có thể tải danh sách từ: /usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt
Khi tệp được tải vào Burp, hãy bắt đầu cuộc tấn công. Bạn sẽ muốn lọc các yêu cầu theo trạng thái.

Một yêu cầu thất bại sẽ nhận được mã 401 Unauthorized (Không được phép)
Trong khi một yêu cầu thành công sẽ trả về mã 200 OK

![image.png](<img/5.png>)