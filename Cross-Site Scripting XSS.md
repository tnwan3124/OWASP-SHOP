# 2 Cross-Site Scripting XSS

XSS hay Cross-site scripting là một lỗ hổng cho phép kẻ tấn công chạy mã JavaScript trong các ứng dụng web. Đây là một trong những lỗi phổ biến nhất được tìm thấy trong các ứng dụng web. Mức độ phức tạp của chúng trải dài từ dễ đến cực kỳ khó, vì mỗi ứng dụng web phân tích các truy vấn theo một cách khác nhau.

Có ba loại tấn công XSS chính:

**1. DOM (Đặc biệt)**

DOM XSS (Document Object Model-based Cross-site Scripting) sử dụng môi trường HTML để thực thi mã JavaScript độc hại. Loại tấn công này thường sử dụng thẻ HTML `<script></script>`.

**2. Persistent (Phía máy chủ)**

Persistent XSS là mã JavaScript được chạy khi máy chủ tải trang có chứa nó. Điều này có thể xảy ra khi máy chủ không vệ sinh dữ liệu người dùng khi nó được tải lên một trang. Chúng thường được tìm thấy trên các bài đăng blog.

**3. Reflected (Phía máy khách)**

Reflected XSS là mã JavaScript được chạy ở phía máy khách của ứng dụng web. Chúng thường được tìm thấy nhất khi máy chủ không vệ sinh dữ liệu tìm kiếm.

**Thông tin thêm:**  [Cross-Site Scripting XSS](https://owasp.org/www-project-top-ten/OWASP_Top_Ten_2017/Top_10-2017_A7-Cross-Site_Scripting_(XSS))

---

### **DOM (Đặc biệt)**

DOM XSS (Document Object Model-based Cross-site Scripting) sử dụng môi trường HTML để thực thi mã JavaScript độc hại. Loại tấn công này thường sử dụng thẻ HTML `<script></script>`.

**Câu hỏi số 1: Thực hiện một cuộc tấn công DOM XSS!**

![image.png](<img/6.png>)

Chúng ta sẽ sử dụng phần tử iframe với một thẻ cảnh báo javascript:

**HTML**

```jsx
<iframe src="javascript:alert(`xss`)">
```

Nhập đoạn mã này vào thanh tìm kiếm sẽ kích hoạt cảnh báo.

![image.png](<img/7.png>)

Lưu ý rằng chúng ta đang sử dụng `iframe`, một phần tử HTML phổ biến được tìm thấy trong nhiều ứng dụng web, có những phần tử khác cũng tạo ra kết quả tương tự.

Loại XSS này còn được gọi là XFS (Cross-Frame Scripting), là một trong những hình thức phổ biến nhất để phát hiện XSS trong các ứng dụng web.

Các trang web cho phép người dùng sửa đổi iframe hoặc các phần tử DOM khác rất có thể sẽ dễ bị tấn công XSS.

**Tại sao điều này hoạt động?**

Thông thường, thanh tìm kiếm sẽ gửi một yêu cầu đến máy chủ, sau đó máy chủ sẽ gửi lại thông tin liên quan, nhưng đây là nơi tồn tại lỗ hổng. Nếu không có sự kiểm duyệt đầu vào đúng cách, chúng ta có thể thực hiện một cuộc tấn công XSS vào thanh tìm kiếm.

**Giải thích DOM XSS trong bài này:**

Trong ngữ cảnh của bài này, DOM XSS đề cập đến việc khai thác lỗ hổng trong cách ứng dụng web xử lý và hiển thị dữ liệu từ thanh tìm kiếm. Khi người dùng nhập đoạn mã độc hại vào thanh tìm kiếm, thay vì chỉ đơn giản hiển thị kết quả tìm kiếm, ứng dụng lại thực thi mã JavaScript bên trong thẻ `<iframe>`. Điều này cho phép kẻ tấn công thực thi mã tùy ý trên trình duyệt của nạn nhân, có thể dẫn đến đánh cắp thông tin đăng nhập, chiếm quyền điều khiển phiên làm việc hoặc thực hiện các hành động độc hại khác.

---

**Persistent (Phía máy chủ)**

Persistent XSS là mã JavaScript được chạy khi máy chủ tải trang có chứa nó. Điều này có thể xảy ra khi máy chủ không kiểm duyệt dữ liệu người dùng khi nó được tải lên một trang. Chúng thường được tìm thấy trên các bài đăng blog.

**Câu hỏi #2: Thực hiện một cuộc tấn công XSS liên tục!**

Đầu tiên, đăng nhập vào tài khoản `admin`.

Chúng ta sẽ điều hướng đến trang `"Last Login IP"` (IP đăng nhập cuối cùng) để thực hiện cuộc tấn công này.

![image.png](<img/8.png>)

Nó sẽ hiển thị địa chỉ IP đăng nhập cuối cùng là 0.0.0.0 hoặc 10.x.x.x.

![image.png](<img/9.png>)

Vì nó ghi lại IP đăng nhập 'cuối cùng', bây giờ chúng ta sẽ `đăng xuất` để nó ghi lại IP 'mới'.

Đảm bảo rằng Burp `intercept` được bật, để nó sẽ bắt yêu cầu đăng xuất.

Sau đó, chúng ta sẽ chuyển đến tab Headers (Tiêu đề), nơi chúng ta sẽ thêm một tiêu đề mới:

```jsx
True-Client-IP
<iframe src="javascript:alert(`xss`)">
```

![image.png](<img/10.png>)

Sau đó, chuyển tiếp yêu cầu đến máy chủ!

Khi đăng nhập lại vào tài khoản admin và điều hướng đến trang Last Login IP một lần nữa, chúng ta sẽ thấy cảnh báo XSS!

**Tại sao chúng ta phải gửi tiêu đề này?**

Tiêu đề `True-Client-IP` tương tự như tiêu đề `X-Forwarded-For`, cả hai đều cho máy chủ hoặc proxy biết IP của máy khách là gì. Do không có sự kiểm duyệt trong tiêu đề, chúng ta có thể thực hiện một cuộc tấn công XSS.

**Giải thích Persistent (Server-side) trong bài:**

Trong ngữ cảnh của bài này, **Persistent (Server-side) XSS** (XSS liên tục phía máy chủ) đề cập đến một loại tấn công XSS trong đó mã độc hại được lưu trữ vĩnh viễn trên máy chủ web. Khi người dùng truy cập trang web bị ảnh hưởng, mã độc hại này sẽ được thực thi trên trình duyệt của họ, ngay cả khi họ không thực hiện bất kỳ hành động cụ thể nào.

**Điều này khác với DOM XSS (được đề cập trước đó), trong đó mã độc hại chỉ được thực thi khi có sự tương tác cụ thể của người dùng với trang web.**

Trong ví dụ này, kẻ tấn công đã lợi dụng việc thiếu kiểm duyệt đầu vào trong tiêu đề `True-Client-IP` để chèn mã XSS vào yêu cầu đăng xuất. Mã này sau đó được máy chủ lưu trữ và hiển thị trên trang "Last Login IP", khiến bất kỳ ai truy cập trang này (bao gồm cả quản trị viên) đều bị ảnh hưởng bởi cuộc tấn công.

**Tóm lại:** Persistent XSS là một loại tấn công XSS nguy hiểm vì nó có thể ảnh hưởng đến nhiều người dùng trong thời gian dài và khó phát hiện hơn so với các loại XSS khác.

---

**Phản chiếu (Phía máy khách)**

Reflected XSS là mã JavaScript được chạy ở phía máy khách của ứng dụng web. Chúng thường được tìm thấy khi máy chủ không kiểm duyệt dữ liệu tìm kiếm.

**Câu hỏi #3: Thực hiện một cuộc tấn công Reflected XSS!**

Đầu tiên, chúng ta cần phải ở đúng trang để thực hiện Reflected XSS!

Đăng nhập vào tài khoản `admin` và điều hướng đến trang 'Lịch sử Đơn hàng' ('Order History’).

![image.png](<img/11.png>)

Từ đó bạn sẽ thấy một biểu tượng "Xe tải" (Truck), nhấp vào đó sẽ đưa bạn đến trang kết quả theo dõi. Bạn cũng sẽ thấy rằng có một id được ghép nối với đơn hàng.

![image.png](<img/12.png>)

Chúng ta sẽ sử dụng iframe XSS, `<iframe src="javascript:alert(`xss`)">`, thay cho `5267-f73dcd000abcc353`.

![image.png](<img/13.png>)

Sau khi gửi URL, hãy làm mới trang và bạn sẽ nhận được một cảnh báo nói rằng XSS!

![image.png](<img/14.png>)

**Tại sao điều này hoạt động?**

Máy chủ sẽ có một bảng tra cứu hoặc cơ sở dữ liệu (tùy thuộc vào loại máy chủ) cho mỗi ID theo dõi. Vì tham số 'id' không được kiểm duyệt trước khi được gửi đến máy chủ, chúng ta có thể thực hiện một cuộc tấn công XSS.

**Giải thích Reflected (Client-side) trong bài:**

Trong ngữ cảnh của bài này, **Reflected (Client-side) XSS** (XSS phản chiếu phía máy khách) đề cập đến một loại tấn công XSS trong đó mã độc hại được phản hồi lại từ máy chủ về phía máy khách và sau đó được thực thi trên trình duyệt của nạn nhân.

**Khác với Persistent XSS (được đề cập trước đó), mã độc hại trong Reflected XSS không được lưu trữ trên máy chủ.** Thay vào đó, nó được nhúng vào một liên kết hoặc yêu cầu được gửi đến máy chủ, và sau đó máy chủ vô tình đưa mã này vào phản hồi gửi về cho người dùng. Khi người dùng nhấp vào liên kết hoặc gửi yêu cầu, mã độc hại sẽ được thực thi trên trình duyệt của họ.

Trong ví dụ này, kẻ tấn công đã khai thác việc thiếu kiểm duyệt đầu vào trong tham số 'id' của URL theo dõi đơn hàng. Bằng cách chèn mã XSS vào tham số này, kẻ tấn công đã khiến máy chủ phản hồi lại một trang chứa mã độc hại. Khi nạn nhân truy cập trang này, mã XSS sẽ được thực thi trên trình duyệt của họ.

**Tóm lại:** Reflected XSS là một loại tấn công XSS phổ biến, thường liên quan đến việc khai thác các lỗ hổng trong việc xử lý dữ liệu do người dùng cung cấp, chẳng hạn như trong các tham số URL hoặc trường nhập liệu.