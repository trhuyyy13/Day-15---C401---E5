# WORKSHEET 4: SCALING & RELIABILITY TABLETOP

**Scenario:** Hospital Clinical Support Assistant (HCSA)

## 1\. Phân tích tình huống và Phản ứng

| Tình huống | Tác động tới người dùng (User Impact) | Phản ứng ngắn hạn (Short-term) | Giải pháp dài hạn (Long-term) |
| :--- | :--- | :--- | :--- |
| **1. Traffic tăng đột biến** (VD: Giờ giao ca sáng, toàn bộ bác sĩ cùng truy cập tóm tắt hồ sơ) | - Latency tăng cao.<br>- Một số yêu cầu bị "drop" (lỗi 429/503).<br>- Gián đoạn quy trình chuẩn bị hồ sơ. | - Kích hoạt **Rate Limiting** (ưu tiên bác sĩ hơn admin).<br>- Tự động mở rộng (Auto-scaling) các pods xử lý middleware. | - Triển khai **Load Balancer** thông minh.<br>- Áp dụng model casading: câu hỏi nhỏ- model nhỏ, câu hỏi lớn, model lớn<br>- Tối ưu RAG. |
| **2. Provider Timeout** (LLM hoặc Vector DB nội bộ bị treo/lỗi) | - Tính năng AI (tóm tắt, trả lời) không hoạt động.<br>- Màn hình hiển thị lỗi hoặc quay vòng vô tận. | - Kích hoạt **Circuit Breaker** để ngắt kết nối lỗi.<br>- Thông báo lỗi rõ ràng: "Tính năng AI tạm bảo trì, vui lòng dùng tra cứu thủ công". | - Triển khai **Multi-model Redundancy** (Nếu Azure OpenAI lỗi thì switch sang Local Llama hoặc Claude).<br>- Lưu trữ bản tóm tắt gần nhất trong cache/DB. |
| **3. Response chậm** (Do RAG tìm kiếm trên hàng triệu file PDF scan hoặc HIS phản hồi chậm) | - Bác sĩ mất kiên nhẫn, quay lại đọc hồ sơ giấy.<br>- User dễ spam click làm hệ thống tệ hơn | - Chuyển sang chế độ **Streaming response** (hiển thị chữ đến đâu hiện đến đó).<br>- Hiển thị thanh tiến trình/thông báo trạng thái cụ thể. | - Tối ưu hóa pipeline RAG (Preprocessing, Reranking).<br>- Sử dụng **Async Processing**: Cho phép bác sĩ đặt lệnh "Tóm tắt" và nhận thông báo khi xong thay vì đợi realtime. |

-----

## 2\. Các Metric cần Monitor (Giám sát)

Để đảm bảo hệ thống vận hành ổn định, cần theo dõi các chỉ số sau:

  * **Latency (P95/P99):** Thời gian phản hồi cho các yêu cầu tóm tắt và tra cứu SOP.
  * **Success/Error Rate:** Tỷ lệ các request thành công so với các lỗi (5xx, 4xx).
  * **Token Consumption:** Theo dõi lưu lượng token để tránh chạm ngưỡng giới hạn (Rate limit) của provider.
  * **Vector DB Search Time:** Thời gian truy xuất tài liệu từ kho dữ liệu.
  * **System Resource:** CPU/RAM của Middleware và Adapter Layer kết nối với HIS.

-----

## 3\. Fallback Proposal (Phương án dự phòng)

Khi hệ thống gặp sự cố nghiêm trọng, HCSA sẽ chuyển đổi theo quy trình sau:

### 3.1. Phân loại Request xử lý

  * **Real-time:** Các câu hỏi hành chính (FAQ), tra cứu biểu mẫu đơn giản.
  * **Async (Bất đồng bộ):** Tóm tắt hồ sơ bệnh án chuyên sâu (do dữ liệu lớn, cần thời gian xử lý và kiểm soát chất lượng).

### 3.2. Cơ chế bảo vệ (Safety Mechanisms)

  * **Circuit Breaker:** Nếu Provider lỗi \> 10% trong 1 phút, hệ thống tự động ngắt kết nối AI và chuyển sang chế độ "Read-only".
  * **Retry Policy:** Thực hiện tối đa 3 lần thử lại với khoảng cách tăng dần (Exponential backoff) trước khi báo lỗi cho user.

### 3.3. Chiến lược Fallback theo tầng

1.  **Level 1 (AI yếu):** Nếu Model lớn (GPT-4) lỗi, hạ cấp xuống Model nhỏ hơn (GPT-4o mini/Local Model) để xử lý các task đơn giản.
2.  **Level 2 (Rule-based):** Nếu toàn bộ AI lỗi, hệ thống chuyển về dạng **Search Engine truyền thống** (Elasticsearch) để người dùng tự tìm SOP qua từ khóa, không có tóm tắt.
3.  **Level 3 (Human Escalation):** Với các yêu cầu hỗ trợ hành chính khẩn cấp, cung cấp hotline hoặc nút "Gửi hỗ trợ IT/Admin" để con người xử lý trực tiếp.
