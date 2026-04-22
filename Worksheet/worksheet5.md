# Kết nối dự án với năng lực hiện tại và hướng đi Phase 2

## Mục tiêu
Kết nối dự án HCSA với năng lực hiện tại của nhóm và xác định hướng đi phù hợp cho **Phase 2**.

## Dự án đang xét
**HCSA - Hospital Clinical Support Assistant**

Đây là dự án AI hỗ trợ môi trường bệnh viện, tập trung vào 3 năng lực chính của MVP:
- Tóm tắt hồ sơ bệnh án ở dạng draft
- Tra cứu SOP/quy trình có citation
- Hỏi đáp hành chính cho nhân viên bệnh viện

Định hướng quan trọng của dự án:
- Không thay bác sĩ ra quyết định lâm sàng
- Chỉ làm trong phạm vi administrative + clinical-adjacent
- Ưu tiên read-only, human review, citation, RBAC và audit log

## Nhóm cần làm gì?

### 1. Mỗi thành viên tự chấm điểm năng lực (thang 1–5)
Mỗi người tự đánh giá ở 3 mảng sau:
- **Business/Product**
- **Infra/Data/Ops**
- **AI Engineering/Application**

#### Bảng tự đánh giá


| Thành viên | Business/Product (1–5) | Infra/Data/Ops (1–5) | AI Engineering/Application (1–5) | 
|---|---:|---:|---:|
| Sơn  | 4 | 2 | 4 |   
| Đạt  | 2 | 3 | 5 |   
| Dũng | 2 | 3 | 4 |   
| Huy  | 4 | 2 | 4 |   
| Đăng | 2 | 2 | 5 |   
| Tuấn | 2 | 5 | 3 |   

### 2. Cả nhóm thảo luận điểm mạnh hiện tại

- **Điểm mạnh nổi bật của nhóm:** Có khả năng phù hợp hơn ở hướng xây dựng AI application thực tế cho người dùng cuối,
- **Mảng có thể dẫn dắt tốt ở Phase 2:** AI Engineering/Application, kết hợp một mức Infra/Data/Ops vừa đủ để dựng pipeline RAG, phân quyền truy cập và logging cơ bản.

#### Nhận định theo nội dung dự án HCSA
- HCSA là bài toán tạo giá trị trực tiếp ở lớp ứng dụng, không phải bài toán nghiên cứu model thuần túy.
- Giá trị MVP đến từ trải nghiệm sử dụng an toàn: tra cứu đúng SOP, hỏi đáp hành chính nhanh, summary draft có citation và human review.
- Phần khó nhất về dài hạn nằm ở enterprise integration, compliance và reliability, nhưng đó là lớp năng lực cần bù thêm chứ chưa phải nơi tạo giá trị sớm nhất cho Phase 2.

### 3. Chọn Track Phase 2 phù hợp nhất
Dựa trên kết quả tự đánh giá và thảo luận, nhóm chọn **1 track Phase 2** phù hợp nhất với năng lực hiện tại.

#### Track được chọn
- **Track Phase 2:** Track 3 - Application
- **Lý do chọn:**
  - MVP của HCSA xoay quanh các use case ứng dụng cụ thể: clinical summary draft, SOP RAG và administrative Q&A.
  - Đây là hướng giúp nhóm tạo demo và giá trị người dùng nhanh nhất mà vẫn bám đúng ranh giới an toàn của dự án.
  - Track Application cho phép triển khai read-only assistant có citation, human review và guardrails trước, trong khi các phần hạ tầng sâu như hybrid deployment, hardening và enterprise integration có thể phát triển dần theo giai đoạn.



### 4. Ghi ra 2–3 kỹ năng cần bù để tiếp tục dự án
Nếu muốn tiếp tục dự án trong Phase 2, nhóm cần thống nhất các kỹ năng cần bổ sung thêm.

#### Kỹ năng cần bù
1. Tích hợp dữ liệu và middleware cho HIS/EMR cũ, bao gồm chuẩn hóa dữ liệu, xử lý file PDF scan và đồng bộ vào kho truy xuất.
2. Bảo mật và tuân thủ cho dữ liệu y tế: RBAC/ABAC, audit log, DLP/redaction, kiểm soát PHI/ePHI và nguyên tắc minimum necessary.
3. Evaluation và reliability cho AI trong môi trường bệnh viện: citation correctness, authorization correctness, guardrails, fallback flow và quy trình human review.


