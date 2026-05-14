# Peer Review Response - Lab 6 AES-CBC Socket

## Thành viên nhóm
- NGUYỄN XUÂN BÁCH

## Phản hồi đánh giá chéo

### 1. Về Sender và Receiver
- **Nhận xét**: Code sender và receiver hoạt động đúng, có xử lý timeout và log.
- **Phản hồi**: Cảm ơn nhận xét. Chúng tôi đã kiểm tra kỹ và đảm bảo luồng gửi/nhận qua 2 kênh (KEY_PORT và DATA_PORT) hoạt động chính xác.

### 2. Về mã hóa AES-CBC
- **Nhận xét**: Sử dụng đúng AES-128, PKCS#7 padding, IV ngẫu nhiên.
- **Phản hồi**: Đúng vậy. Chúng tôi đã cài đặt encrypt_aes_cbc và decrypt_aes_cbc theo đúng chuẩn.

### 3. Về kiểm thử
- **Nhận xét**: Có đủ test cho happy path, wrong key, tamper ciphertext.
- **Phản hồi**: Chúng tôi đã bổ sung 7 test cases, tất cả đều pass.

### 4. Về Threat Model
- **Nhận xét**: Đã chỉ ra key disclosure, tampering, replay attack.
- **Phản hồi**: Cảm ơn. Chúng tôi đã phân tích đầy đủ assets, threats, mitigations và residual risks.

### 5. Cải thiện đề xuất
- **Nhận xét**: Nên thêm xác thực (AES-GCM) thay vì CBC.
- **Phản hồi**: Đồng ý. Trong thực tế, nên dùng GCM để có cả mã hóa và xác thực.

## Kết luận
Nhóm đã hoàn thành đầy đủ yêu cầu của lab, bao gồm: mã nguồn, test, log, report, threat model và peer review.
