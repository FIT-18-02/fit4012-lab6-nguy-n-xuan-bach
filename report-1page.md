# Report 1 page - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Nguyễn Xuân Bách

## Mục tiêu

Mục tiêu của bài lab này là xây dựng một hệ thống truyền tin an toàn giữa hai thực thể Sender và Receiver thông qua lập trình Socket. Hệ thống triển khai cơ chế mã hóa đối xứng AES trong chế độ hoạt động CBC để bảo vệ tính bí mật của dữ liệu. Ngoài ra, bài lab tập trung vào việc thiết kế kiến trúc tách biệt giữa kênh truyền khóa (Key Channel) và kênh truyền dữ liệu (Data Channel) để mô phỏng môi trường phân phối khóa. Qua đó, sinh viên có thể kiểm thử khả năng vận hành thực tế của thuật toán, thực hiện phân tích các điểm yếu bảo mật tiềm tàng và đề xuất mô hình đe dọa (Threat Model) phù hợp

## Phân công thực hiện
Nguyễn Xuân Bách:
Phụ trách thiết kế cấu trúc gói tin, triển khai logic cho sender.py và viết các hàm bổ trợ mã hóa/giải mã AES-CBC trong aes_socket_utils.py.Làm chung: Cùng thực hiện kiểm thử hệ thống , phân tích kết quả giải mã và hoàn thiện báo cáo tổng kết. 
Phụ trách triển khai receiver.py để xử lý kết nối đồng thời từ các kênh , thiết lập hệ thống ghi log và xây dựng tài liệu threat-model-1page.md.  Làm chung: Cùng thực hiện kiểm thử hệ thống, phân tích kết quả giải mã và hoàn thiện báo cáo tổng kết.

## Cách làm

Sender & Receiver: Sử dụng thư viện socket của Python để thiết lập kết nối TCP/IP. Receiver đóng vai trò máy chủ lắng nghe tại cổng 6000 và 6001, trong khi Sender đóng vai trò máy khách kết nối để truyền tin.  

AES-CBC & PKCS#7 Padding: Sử dụng thư viện pycryptodome để thực hiện mã hóa. Do AES hoạt động theo khối 16 byte, dữ liệu được độn bằng hàm pad theo chuẩn PKCS#7 trước khi mã hóa và loại bỏ độn bằng hàm unpad sau khi giải mã. 

Key & Data Channel: Hệ thống tách thành hai luồng truyền: Kênh khóa truyền gói tin gồm độ dài khóa, khóa AES và IV; Kênh dữ liệu truyền gói tin gồm độ dài bản mã và bản mã (ciphertext).  

Length Header: Để Receiver biết chính xác số lượng byte cần nhận trên luồng TCP, mỗi gói tin đều được đính kèm một Header 4 byte (sử dụng struct.pack("!I", ...) để đảm bảo thứ tự byte mạng) chứa thông tin độ dài

## Kết quả

Quá trình chạy: Receiver khởi chạy trước và lắng nghe thành công. Sender kết nối, tạo khóa AES ngẫu nhiên, mã hóa thông điệp và gửi đi thành công qua hai kênh riêng biệt.  

Log minh chứng: Các file log (ví dụ: receiver.log) ghi lại chi tiết các bước: nhận Key/IV thành công, nhận ciphertext và giải mã ra bản tin gốc chính xác.

Test quan trọng: Đã thực hiện kiểm thử với bản tin mẫu trong sample_input.txt. Kết quả Receiver khôi phục được nguyên vẹn nội dung: "Xin chao FIT4012 - Day la file input mau cho Lab 6 AES Socket."

## Kết luận

Bài học kỹ thuật: Hiểu rõ cách quản lý luồng dữ liệu trên Socket TCP và tầm quan trọng của việc xử lý Length Header để tránh mất mát hoặc dính dữ liệu khi truyền tin. Nắm vững cách triển khai AES-CBC và xử lý padding trong thực tế.  

Bài học bảo mật: Nhận thấy rằng việc gửi khóa và IV trực tiếp qua mạng (dù tách kênh) vẫn tồn tại rủi ro bị nghe lén nếu không có kênh truyền bảo mật (như TLS) hoặc cơ chế trao đổi khóa an toàn (như Diffie-Hellman). Việc ghi log quá chi tiết (bao gồm cả khóa) cũng là một nguy cơ rò rỉ dữ liệu nhạy cảm.
