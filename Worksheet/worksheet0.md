# Worksheet 0: Phân tích dự án HCSA

## 1. Liệt kê 2-3 kỹ năng nhóm tự tin nhất
* **Kỹ thuật RAG (Retrieval-Augmented Generation):** Xây dựng hệ thống truy xuất dữ liệu từ tài liệu nội bộ (SOP, văn bản) với độ chính xác cao và dẫn nguồn (citation).
* **Thiết kế kiến trúc hệ thống Hybrid/Bảo mật:** Thiết kế hạ tầng cân bằng giữa tính năng đám mây và bảo mật dữ liệu y tế nhạy cảm (PHI), bao gồm phân quyền (RBAC) và kiểm soát truy cập.
* **Tối ưu hóa và Đánh giá (Evaluation/Optimization):** Xây dựng quy trình đánh giá offline, cơ chế fallback (fail-safe) và tối ưu hóa chi phí (caching, model cascading).

## 2. Mô tả ngắn sản phẩm (HCSA)
**HCSA** là một trợ lý AI dành cho môi trường bệnh viện, đóng vai trò cộng sự hỗ trợ nhân viên y tế thay vì đưa ra quyết định lâm sàng. Sản phẩm tập trung vào việc giảm tải hành chính và tối ưu hóa tra cứu thông tin thông qua 3 tính năng chính:
1. **Smart Clinical Summary:** Tạo bản nháp tóm tắt hồ sơ bệnh án.
2. **Policy/Protocol RAG:** Tra cứu quy trình nội bộ, SOP có dẫn nguồn.
3. **Administrative Q&A:** Giải đáp các câu hỏi về quy trình vận hành, bảo hiểm và hành chính.

## 3. Chủ đề xuyên suốt cả ngày
**"Triển khai AI an toàn và hiệu quả trong môi trường y tế (Healthcare AI Governance & Operations)"**

---

## Câu hỏi nhóm phải trả lời:

* **Sản phẩm này giải quyết bài toán gì?**
  Giải quyết sự quá tải hành chính, sự phân tán của dữ liệu bệnh viện (HIS/EMR cũ, PDF scan) và giảm thời gian tìm kiếm thông tin/quy trình, từ đó giúp nhân viên y tế tập trung hơn vào việc chăm sóc bệnh nhân.

* **Ai là người dùng chính?**
  * **Bác sĩ:** Sử dụng để xem tóm tắt hồ sơ và tra cứu SOP lâm sàng.
  * **Điều dưỡng:** Sử dụng để xem workflow, checklist và thông tin chăm sóc.
  * **Nhân viên tiếp nhận:** Sử dụng để tra cứu lịch khám, quy định hành chính và thủ tục bảo hiểm.

* **Vì sao chủ đề này phù hợp để phân tích deployment và cost?**
  Đây là bài toán phức tạp vì liên quan đến dữ liệu nhạy cảm (PHI/ePHI), yêu cầu tuân thủ pháp lý cao và hạ tầng đặc thù (Hybrid Architecture). Việc phân tích giúp làm rõ các khoản đầu tư không chỉ nằm ở model mà còn ở middleware, bảo mật, và chi phí vận hành (monitoring, audit log) – những yếu tố cốt lõi của một hệ thống doanh nghiệp (Enterprise AI).
