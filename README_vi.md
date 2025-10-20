# OWASP Top-10 (2017) - Khởi động nhanh nhận thức về bảo mật của bạn

## Cảnh báo

Các bản demo này chứa mã cố tình dễ bị tấn công.

Không chạy bất kỳ bản demo nào nào trong số này trên máy có thể bị truy cập bởi người dùng bên ngoài.

## Điều kiện tiên quyết

1. [Git](https://git-scm.com/) - Để clone kho lưu trữ (repo)
2. [npm](https://www.npmjs.com/get-npm)
3. [curl](https://curl.haxx.se/) - Không bắt buộc để chạy demo, nhưng một số hướng dẫn sử dụng nó

## Cài đặt

Clone kho lưu trữ:

```
git clone https://github.com/nguyenanhung/cybersecurity-owasp-top10-demo.git
```

Cài đặt các gói phụ thuộc:

```
npm install
```

## Demos

### A1:2017 - Injection (Tiêm nhiễm)

Chạy demo Log Injection:

```
node logi.js
```

Gửi một payload có dạng `username=XYZ logged in.\nABC&password=123`:

```
curl -d $'username=allon logged in with the password: fakepassword.\nguyenanhung&password=123' http://localhost:3000/logi
```

Bạn sẽ thấy hai thông báo đăng nhập trong console của ứng dụng.

### A2:2017 - Broken Authentication (Xác thực bị lỗi)

Chạy demo đăng nhập:

```
node logi.js
```

Mở trình duyệt của bạn và điều hướng đến http://localhost:3000/logi.html. Như bạn có thể dễ dàng thấy, bất kỳ sự kết hợp
nào của tên người dùng và mật khẩu đều sẽ được hệ thống chấp nhận. Một hệ thống phù hợp cần phải triển khai quản lý
người dùng thực tế.

### A3:2017 Sensitive data exposure (Lộ dữ liệu nhạy cảm)

Chạy demo đăng nhập:

```
node logi.js
```

Mở trình duyệt của bạn và điều hướng đến http://localhost:3000/logi.html. Bạn có thể sử dụng bất kỳ sự kết hợp nào của
tên người dùng và mật khẩu để đăng nhập, và mật khẩu sẽ được hiển thị dưới dạng văn bản thuần túy trong console của ứng
dụng.

### A4:2017 XML External Entities (XXE) (Thực thể ngoài XML)

Chạy demo XXE:

```
node xxe.js
```

Gửi một payload có dạng sau:

```
curl -d '<!DOCTYPE foo [<!ENTITY xxe SYSTEM "/full/path/to/cybersecurity-owasp-top10-demo/secret.txt">]><name>&xxe;</name>' http://localhost:3000/xxe
```

Và bạn sẽ nhận lại **nội dung** của file [secret.txt](secret.txt), tức là, `THIS IS A SECRET!!!`.

### A5:2017 Broken Access Control (Kiểm soát truy cập bị lỗi)

Chạy demo session:

```
node session.js
```

Nếu bạn sử dụng trình duyệt của mình để điều hướng đến http://localhost:3000/data bạn sẽ nhận được một lỗi cho biết
`not logged in` (chưa đăng nhập), điều này là đúng như mong đợi.

Bạn có thể điều hướng đến http://localhost:3000/session.html và sử dụng thông tin đăng nhập `user1`/`password1` để đăng
nhập, sau đó bạn sẽ được chuyển hướng đến http://localhost:3000/data?username=user1 và thấy dữ liệu của người dùng đó.
Tương tự, bạn có thể sử dụng thông tin đăng nhập `user2`/`password2`, và sẽ thấy một bộ dữ liệu khác. Tuy nhiên, nếu bạn
đăng nhập với tư cách là `user1`, bạn có thể thủ công điều hướng đến http://localhost:3000/data?username=user2, và sẽ
thấy dữ liệu của người dùng đó.

Nói cách khác, bản demo này triển khai **xác thực (authentication)**, nhưng không triển khai **ủy quyền (authorization)
**.

### A6:2017 Security Misconfiguration (Cấu hình bảo mật sai)

Có một số cấu hình bảo mật sai trong các bản demo này. Một vài cái rõ ràng bao gồm:

- Tất cả các bản demo đều phục vụ HTTP chứ không phải HTTPS
- [xxe.js](xxe.js) đặt `noent: true` khi tạo trình phân tích libxmljs, do đó làm cho bản demo dễ bị tấn công XXE
- [session.js](session.js) sử dụng Express Session, nhưng sử dụng cấu hình mặc định (ví dụ: nó không đặt các thuộc tính
  `secure` hoặc `maxAge`)

### A7:2017 Cross-Site Scripting (XSS) (Tấn công tập lệnh liên trang)

Chạy demo XSS:

```
node xss.js
```

Nếu bạn sử dụng trình duyệt của mình để điều hướng đến http://localhost:3000/xss, bạn sẽ thấy một biểu mẫu bình luận nơi
bạn có thể thêm ý kiến của mình và xem các bình luận trước đó

Gửi một payload có dạng sau:

```
curl -X POST -d 'comment=<script>window.location.replace("https://github.com/nguyenanhung/cybersecurity-owasp-top10-demo")</script>' http://localhost:3000/xss
```

Lần tiếp theo bạn điều hướng đến http://localhost:3000/xss, bạn sẽ bị chuyển hướng đến trang README này.

### A8:2017 Insecure Deserialization (Khử tuần tự không an toàn)

Chạy demo keys:

```
node keys.js
```

Gửi một payload chứa định nghĩa hàm theo sau bởi `()`:

```
curl -X POST -H "Content-Type: text/plain" -d '{"key": "_$$ND_FUNC$$_function (){ console.log(\"unserialized!\"); }()"}' http://localhost:3000/keys
```

Bạn sẽ thấy văn bản "userialized!" được in ra trong console của ứng dụng, chứng minh rằng mã tùy ý có thể được thực thi.
Tất nhiên, bạn có thể sử dụng mã độc hại hơn là `console.log`.

### A9:2017 Using Components with Known Vulnerabilities (Sử dụng các thành phần với các lỗ hổng đã biết)

Chạy một kiểm tra (audit):

```
npm audit
```

Bạn sẽ thấy các gói dễ bị tấn công mà dự án này phụ thuộc.

### A10:2017 Insufficient Logging & Monitoring (Ghi nhật ký & Giám sát không đầy đủ)

Chạy demo session:

```
node session.js
```

Sử dụng trình duyệt của bạn để điều hướng đến http://localhost:3000/session.html.

Nếu bạn sử dụng thông tin đăng nhập sai (ví dụ: `wronguser`/`wrongpassword`) bạn sẽ nhận được thông báo lỗi, nhưng không
có gì sẽ được ghi nhật ký.
Trên thực tế, "ứng dụng" này thậm chí không có bất kỳ nhật ký thực sự nào.
