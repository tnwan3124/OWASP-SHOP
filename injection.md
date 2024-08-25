# Injection

Nhiệm vụ này sẽ tập trung vào các lỗ hổng tiêm (injection). Lỗ hổng tiêm là khá nguy hiểm cho một công ty vì chúng có thể gây ra sự ngừng hoạt động và/hoặc mất dữ liệu. Việc xác định các điểm tiêm trong một ứng dụng web thường khá đơn giản, vì hầu hết chúng sẽ trả về lỗi. Có nhiều loại tấn công tiêm, một số trong số đó là:

- **Tiêm SQL (SQL Injection)**
Tiêm SQL xảy ra khi kẻ tấn công nhập một truy vấn độc hại hoặc không đúng định dạng để lấy hoặc giả mạo dữ liệu từ cơ sở dữ liệu. Và trong một số trường hợp, đăng nhập vào tài khoản.
- **Tiêm Lệnh (Command Injection)**
Tiêm Lệnh xảy ra khi các ứng dụng web lấy đầu vào hoặc dữ liệu do người dùng kiểm soát và chạy chúng như các lệnh hệ thống. Kẻ tấn công có thể giả mạo dữ liệu này để thực thi các lệnh hệ thống của riêng họ. Điều này có thể thấy trong các ứng dụng thực hiện các bài kiểm tra ping không đúng cấu hình.
- **Tiêm Email (Email Injection)**
Tiêm Email là một lỗ hổng bảo mật cho phép người dùng độc hại gửi email mà không cần sự cho phép trước của máy chủ email. Điều này xảy ra khi kẻ tấn công thêm dữ liệu bổ sung vào các trường, mà máy chủ không diễn giải chính xác.

Nhưng trong trường hợp của chúng ta, chúng ta sẽ sử dụng Tiêm SQL.
Để biết thêm thông tin: Tiêm  [Injection](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A1-Injection)

---

Bây giờ chúng ta sẽ thay đổi ký tự "a" bên cạnh email thành: ' or 1=1-- và gửi nó đến máy chủ.

![image.png](<img/0.png>)

**Tại sao cách này lại hoạt động?**

- Ký tự ' sẽ đóng ngoặc đơn trong truy vấn SQL.
- 'OR' trong câu lệnh SQL sẽ trả về true nếu một trong hai vế của nó là true. Vì 1=1 luôn luôn đúng, toàn bộ câu lệnh là true. Do đó, nó sẽ báo cho máy chủ rằng email là hợp lệ và đăng nhập chúng ta vào user id 0, tình cờ là tài khoản quản trị viên.
- Ký tự -- được sử dụng trong SQL để bình luận dữ liệu, bất kỳ hạn chế nào trên đăng nhập sẽ không còn hoạt động vì chúng được hiểu là một bình luận. Điều này giống như bình luận # và // trong Python và JavaScript tương ứng.

![image.png](<img/1.png>)

**Giải thích:**

Kẻ tấn công đang lợi dụng việc ứng dụng web không xử lý đúng đầu vào của người dùng trước khi đưa nó vào truy vấn SQL. Bằng cách chèn đoạn mã độc hại ' or 1=1--, kẻ tấn công đã thay đổi hoàn toàn logic của truy vấn ban đầu.

- Ban đầu, truy vấn có thể trông giống như thế này:

**SQL**

```jsx
SELECT * FROM users WHERE email = 'user@example.com' AND password = 'some_password'
```

- Sau khi tiêm, truy vấn trở thành:

**SQL**

```jsx
SELECT * FROM users WHERE email = '' OR 1=1--' AND password = 'some_password'
```

Phần `AND password = 'some_password'` bị vô hiệu hóa do bình luận `--`, và điều kiện `email = '' OR 1=1` luôn luôn đúng. Kết quả là, truy vấn sẽ trả về tất cả các bản ghi trong bảng `users`, bao gồm cả tài khoản quản trị viên (user id 0). Kẻ tấn công có thể đăng nhập vào tài khoản này mà không cần biết mật khẩu.

Đây là một ví dụ điển hình về tấn công SQL Injection, một lỗ hổng bảo mật nghiêm trọng có thể dẫn đến mất dữ liệu, đánh cắp thông tin nhạy cảm, thậm chí chiếm quyền kiểm soát toàn bộ hệ thống.

---

Bây giờ chúng ta sẽ đăng nhập vào tài khoản của Bender! Chụp lại yêu cầu đăng nhập một lần nữa, nhưng lần này chúng ta sẽ đặt: **bender@juice-sh.op'-- làm email**.

![image.png](<img/2.png>)

Bây giờ, hãy chuyển tiếp nó đến máy chủ!

Nhưng tại sao chúng ta không đặt 1=1?

Vâng, vì địa chỉ email là hợp lệ (sẽ trả về true), chúng ta không cần phải ép buộc nó thành true. Do đó, chúng ta có thể sử dụng '-- để vượt qua hệ thống đăng nhập. Lưu ý rằng 1=1 có thể được sử dụng khi email hoặc tên người dùng không được biết hoặc không hợp lệ.

![image.png](<img/3.png>)