# Worksheet 1: Enterprise Deployment Clinic (HCSA)

## 1. Bối cảnh tổ chức/khách hàng
Hệ thống được triển khai trong môi trường bệnh viện, nơi có quy trình vận hành nghiêm ngặt, dữ liệu phân tán (HIS/EMR cũ), và đòi hỏi tính sẵn sàng cao. Người dùng cuối là nhân viên y tế (Bác sĩ, Điều dưỡng, Nhân viên tiếp nhận).

## 2. Dữ liệu hệ thống sẽ động đến
* **Hồ sơ bệnh án điện tử (EMR/HIS):** Các thông tin lâm sàng của bệnh nhân.
* **Tài liệu quy trình nội bộ (SOP/Policy):** Văn bản hướng dẫn chuyên môn và quy định hành chính.
* **Dữ liệu hành chính:** Lịch khám, biểu mẫu, thông tin bảo hiểm.
* **Audit Logs:** Nhật ký truy cập, dấu vết các hành động của người dùng và hệ thống.

## 3. Mức độ nhạy cảm của dữ liệu
**Rất cao.** Dữ liệu thuộc nhóm PHI (Protected Health Information) và ePHI (Electronic PHI). Việc rò rỉ hoặc truy cập trái phép có thể dẫn đến vi phạm quy định pháp lý nghiêm trọng và ảnh hưởng trực tiếp đến an toàn/quyền riêng tư của bệnh nhân.

## 4. 3 Ràng buộc Enterprise lớn nhất
1. **Tuân thủ bảo mật và quyền riêng tư:** Yêu cầu tuyệt đối về việc không làm lộ dữ liệu nhạy cảm ra môi trường công cộng (compliance với luật y tế).
2. **Khả năng tích hợp hệ thống cũ (Legacy Systems):** Khó khăn khi kết nối với các hệ thống HIS/EMR lâu đời, thiếu API hiện đại, yêu cầu kiến trúc trung gian (middleware).
3. **Đảm bảo an toàn lâm sàng (Zero-Trust AI):** Ràng buộc về việc AI không được tự ý thay đổi y lệnh, mọi kết quả nhạy cảm phải được human review và có citation rõ ràng.

## 5. Lựa chọn mô hình: Hybrid Architecture
* **Lý do 1 (Bảo mật):** Kết hợp sự an toàn của On-prem (giữ dữ liệu nhạy cảm bên trong mạng bệnh viện) với hiệu năng của Cloud (tận dụng sức mạnh LLM cho các tác vụ tóm tắt, RAG).
* **Lý do 2 (Khả năng mở rộng):** Cho phép duy trì quy trình kiểm soát (RBAC, Audit) tại local, trong khi vẫn có thể cập nhật các model AI mới nhất hoặc các tính năng phụ trợ từ môi trường Cloud một cách linh hoạt.

---
### Phân tích bổ sung (Gợi ý thảo luận):
* **Audit trail:** Bắt buộc có để truy vết mọi hành động của AI và người dùng, phục vụ kiểm tra an toàn sau này.
* **Rời khỏi tổ chức:** Dữ liệu thô (PHI) không được phép rời khỏi mạng nội bộ của bệnh viện nếu chưa được ẩn danh hóa.
* **Tích hợp:** Cần bắt buộc tích hợp middleware để kết nối với hệ thống HIS/EMR cũ.
* **Người bị ảnh hưởng nếu trả lời sai:** Bệnh nhân là người chịu hậu quả đầu tiên (sai sót y khoa, chậm trễ điều trị), tiếp theo là nhân viên y tế và uy tín của bệnh viện.
