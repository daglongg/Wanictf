# JQ Playground

![image](https://github.com/daglongg/Wanictf/assets/138242812/52eec896-ada3-4597-9318-1100498e35e0)


Tại đây trong file main.py ta thấy được rằng 

![image](https://github.com/daglongg/Wanictf/assets/138242812/177d4890-0ca3-4b0e-945b-e7f855303c70)

Đầu tiên ta thấy rằng: 
* Định nghĩa route cho đường dẫn "/" khi truy cập bằng phương thức POST.
* Lấy giá trị của filter từ form gửi lên.
* In giá trị của filter ra console.
* Kiểm tra độ dài của filter. Nếu độ dài lớn hơn hoặc bằng 9, trả về template với thông báo lỗi "Filter is too long".
* Kiểm tra xem filter có chứa các ký tự không hợp lệ như ;, |, hoặc &. Nếu có, trả về template với thông báo lỗi "Filter contains invalid character".
* Tạo lệnh jq để chạy với filter và tệp test.json.
* Chạy lệnh jq bằng subprocess.run.
* Trả về template với nội dung kết quả từ lệnh jq (stdout) và lỗi nếu có (stderr).

Vậy hướng làm ở đây là gì. Tôi sẽ sử dụng tấn công chèn lệnh (command injection). Tôi gửi một yêu cầu POST đến máy chủ, với dữ liệu form chứa trường filter có giá trị %0A (ký tự xuống dòng) và lệnh nl. Do mã trên máy chủ không kiểm tra kỹ lưỡng đầu vào, ký tự xuống dòng làm gián đoạn lệnh jq ban đầu và chèn lệnh nl /* để liệt kê tất cả các tệp và thư mục trong thư mục gốc (/).

Đây là payload của bài này

`curl -X POST http://chal-lz56g6.wanictf.org:8000/ -H "Content-Type: application/x-www-form-urlencoded" -d "filter='%0Anl /*'" | grep FLAG`

Và tôi có flag: `FLAG{jqj6jqjqjqjqjqj6jqjqjqjqj6jqjqjq}`

