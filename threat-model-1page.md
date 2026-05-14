# Threat Model - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Nguyễn Xuân Bách

## Assets

Các tài sản quan trọng cần bảo vệ trong hệ thống bao gồm:


Bản tin gốc (Plaintext): Nội dung thông tin nhạy cảm được nhập từ bàn phím hoặc file sample_input.txt.  

Khóa mã hóa (AES Key): Khóa đối xứng dùng để mã hóa và giải mã, nếu lộ khóa này thì toàn bộ dữ liệu sẽ bị giải mã.

Vector khởi tạo (IV): Thành phần dùng trong chế độ CBC để đảm bảo các bản rõ giống nhau không tạo ra bản mã giống nhau.

Bản mã (Ciphertext): Dữ liệu sau khi mã hóa được truyền qua mạng.

Các file log: Chứa lịch sử thực thi và có thể vô tình chứa cả thông tin về khóa hoặc nội dung bản tin.

## Attacker model

Mô hình kẻ tấn công được giả định bao gồm:

Nghe lén (Sniffing): Kẻ tấn công nằm trong cùng mạng LAN, có khả năng sử dụng các công cụ như Wireshark để bắt các gói tin truyền qua cổng 6000 và 6001.

Sửa đổi dữ liệu (Tampering): Kẻ tấn công có khả năng can thiệp vào luồng dữ liệu TCP, thay đổi nội dung của bản mã hoặc tiêu đề độ n dài (length header) trước khi nó tới Receiver.

Tấn công phát lại (Replay Attack): Kẻ tấn công ghi lại gói tin chứa khóa và gói tin chứa bản mã, sau đó gửi lại cho Receiver tại một thời điểm khác.

Truy cập trái phép vào hệ thống: Kẻ tấn công có quyền đọc các file nhật ký (log) hoặc file output trên ổ cứng của máy tính đang chạy chương trình.

## Threats

Tiết lộ khóa (Key Disclosure): Do hệ thống gửi khóa AES và IV ở dạng plaintext qua Kênh khóa (Key Channel), kẻ tấn công chỉ cần bắt được gói tin trên cổng 6001 là có thể lấy được toàn bộ thông tin cần thiết để giải mã dữ liệu.

Vi phạm tính toàn vẹn (Tampering): AES-CBC không có cơ chế kiểm tra tính toàn vẹn tích hợp. Kẻ tấn công có thể sửa đổi bản mã trên đường truyền, dẫn đến việc Receiver giải mã ra dữ liệu sai lệch mà không hề hay biết.

Rò rỉ dữ liệu qua nhật ký (Log Leakage): Mã nguồn hiện tại ghi trực tiếp khóa AES (dạng hex) và nội dung bản tin gốc vào file log nếu biến môi trường LOG_FILE được thiết lập, tạo cơ hội cho kẻ tấn công lấy được thông tin nhạy cảm từ ổ cứng.

Thiếu cơ chế xác thực (No Authentication): Receiver không kiểm tra danh tính của Sender. Bất kỳ ai biết địa chỉ IP và cổng của Receiver đều có thể gửi dữ liệu rác hoặc dữ liệu độc hại.

## Mitigations
Sử dụng TLS/SSL: Thay vì truyền khóa trực tiếp, sử dụng giao thức TLS để tạo một kênh truyền được mã hóa và xác thực cho cả Kênh khóa và Kênh dữ liệu.

Chuyển sang chế độ AES-GCM: Sử dụng AES-GCM thay vì AES-CBC vì GCM cung cấp cả tính bảo mật và tính xác thực (Authenticated Encryption), giúp phát hiện ngay lập tức nếu dữ liệu bị chỉnh sửa trên đường truyền.

Quản lý Log an toàn: Loại bỏ việc ghi khóa bí mật vào file nhật ký. Chỉ nên ghi lại các thông báo trạng thái hoặc mã định danh thay vì các giá trị nhạy cảm.

Cơ chế chống phát lại (Anti-replay): Thêm mã định danh duy nhất (nonce) hoặc dấu thời gian (timestamp) vào trong gói tin và yêu cầu Receiver kiểm tra tính duy nhất/hợp lệ về thời gian của gói tin đó.

## Residual risks

Rủi ro còn lại lớn nhất là hệ thống hiện tại vẫn chỉ mang tính chất mô phỏng học thuật. Ngay cả khi áp dụng các biện pháp trên, nếu môi trường lưu trữ (máy chủ/máy trạm) bị xâm nhập ở mức đặc quyền cao, kẻ tấn công vẫn có thể chiếm đoạt khóa từ bộ nhớ RAM hoặc can thiệp trực tiếp vào quy trình thực thi của Python. Ngoài ra, việc tách kênh khóa và kênh dữ liệu mà không có cơ chế liên kết chặt chẽ (binding) vẫn có thể bị tấn công bằng cách tráo đổi cặp khóa-bản mã từ hai phiên giao dịch khác nhau.
