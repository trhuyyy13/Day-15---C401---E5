# Worksheet 2 – Cost Anatomy Lab 

---

# 1. Assumptions (Giả định chi tiết)

## 1.1 User Segments
| Role              | % User | Behavior |
|-------------------|--------|---------|
| Bác sĩ            | 40%    | dùng summary + SOP |
| Điều dưỡng        | 40%    | SOP + checklist |
| Admin/reception   | 20%    | FAQ hành chính |

Phân chia người dùng theo vai trò giúp phản ánh đúng hành vi sử dụng thực tế trong bệnh viện. Bác sĩ thường sử dụng các chức năng nặng như tóm tắt hồ sơ, trong khi nhân viên hành chính chủ yếu thực hiện các truy vấn đơn giản. Điều này ảnh hưởng trực tiếp đến phân bố tải hệ thống và chi phí, đặc biệt là token usage và nhu cầu human review.

---

## 1.2 Traffic Breakdown

### Requests/người/ngày

| Use case           | Requests/user/day |
|--------------------|------------------|
| Admin Q&A          | 5                |
| SOP Retrieval      | 3                |
| Clinical Summary   | 2                |
| **Total**          | **10**           |

Các con số này đóng vai trò là giả định workload cơ bản của hệ thống. Tổng số request phát sinh sẽ quyết định toàn bộ các chi phí phía sau như token, compute và cả chi phí vận hành.

---

### Tổng hệ thống (MVP)
- Users/day: 100  
→ **1,000 requests/day**

Đây là baseline để tính toán quy mô hệ thống trong giai đoạn MVP. Từ giá trị này, có thể suy ra tổng token tiêu thụ, tải hệ thống và nhu cầu tài nguyên.

---

### Peak Traffic
- Peak factor: 5x  
→ ~5,000 requests/day  

Hệ thống thực tế không được thiết kế dựa trên mức trung bình mà phải chịu được các thời điểm cao điểm, chẳng hạn như đầu ca khám. Nếu chỉ tối ưu cho average load, hệ thống rất dễ bị quá tải trong giờ cao điểm, dẫn đến latency cao hoặc thậm chí downtime.

---

# 2. Token Modeling (Granular)

## 2.1 Theo từng use case

Admin Q&A là loại truy vấn đơn giản, sử dụng ít context nên chi phí token thấp. Ngược lại, SOP retrieval sử dụng cơ chế RAG nên cần đưa thêm tài liệu vào prompt, làm tăng số lượng token đáng kể. Clinical summary là use case tốn kém nhất do phải xử lý lượng lớn dữ liệu bệnh án và sinh ra output dài hơn, đồng thời đây cũng là loại output cần human review.

---

## 2.2 Weighted Average

Việc tính trung bình có trọng số theo tỷ lệ từng use case giúp phản ánh chính xác hơn mức tiêu thụ token thực tế, thay vì lấy trung bình đơn giản. Kết quả ~1,220 tokens/request cho thấy hệ thống không quá nặng về mặt token.

---

## 2.3 Total Tokens/ngày

- ~1.22M tokens/day  
- ~36.6M tokens/month  

Con số này được dùng để ước lượng chi phí LLM API, tuy nhiên đây chỉ là một phần nhỏ trong tổng chi phí hệ thống.

---

# 3. Cost Breakdown (Deep Dive)

## 3.1 Token Cost (LLM API)

Mặc dù token usage khá lớn về mặt số lượng, chi phí thực tế lại thấp (khoảng ~$110/tháng). Điều này cho thấy chi phí model không phải là yếu tố chi phối trong hệ thống AI ở mức enterprise.

---

## 3.2 Compute Cost

Chi phí compute đến từ toàn bộ pipeline phía sau, bao gồm embedding, vector search và reranking. Đây là các thành phần bắt buộc để hệ thống RAG hoạt động hiệu quả, và chúng tiêu tốn tài nguyên liên tục ngay cả khi không có request lớn. Vì vậy, compute cost thường cao hơn nhiều so với token cost.

---

## 3.3 Storage Cost

Storage không chỉ dùng để lưu dữ liệu mà còn phục vụ logging và audit, đặc biệt quan trọng trong môi trường y tế. Việc lưu lại lịch sử truy cập và truy vấn là yêu cầu bắt buộc để đảm bảo tuân thủ quy định về dữ liệu bệnh nhân.

---

## 3.4 Human Review Cost

Do các output như clinical summary nằm trong vùng rủi ro trung bình, hệ thống bắt buộc phải có con người kiểm duyệt. Điều này khiến chi phí human review tăng tuyến tính theo số lượng request và trở thành bottleneck lớn nhất khi hệ thống mở rộng.

---

## 3.5 Logging, Monitoring, Compliance

Các thành phần này đảm bảo hệ thống có thể theo dõi lỗi, kiểm soát truy cập và đáp ứng yêu cầu pháp lý. Trong bối cảnh dữ liệu y tế nhạy cảm, đây không phải là chi phí tùy chọn mà là bắt buộc.

---

## 3.6 Maintenance & Operations

Chi phí vận hành bao gồm DevOps, xử lý sự cố và duy trì hệ thống ổn định. Đây là khoản chi phí dài hạn và thường bị đánh giá thấp trong giai đoạn thiết kế ban đầu.

---

# 4. Tổng Cost MVP

Tổng chi phí nằm trong khoảng $3,200 – $8,900/tháng, phù hợp với ước lượng ban đầu. Điều này xác nhận rằng phần lớn chi phí đến từ vận hành và con người, thay vì từ model.

---

# 5. Scaling Analysis (5x – 10x)

Khi số lượng người dùng tăng, chi phí token và compute tăng gần tuyến tính và có thể tối ưu. Tuy nhiên, human review tăng trực tiếp theo số lượng request và không thể tự động hóa hoàn toàn, khiến nó trở thành điểm nghẽn chính. Ngoài ra, chi phí vận hành không tăng tuyến tính mà tăng theo từng giai đoạn khi hệ thống cần mở rộng hạ tầng và đội ngũ.

---

# 6. Key Questions

Chi phí lớn nhất của hệ thống đến từ human review và vận hành, không phải từ token hay model. Những chi phí dễ bị bỏ qua bao gồm tích hợp với hệ thống HIS/EMR, làm sạch dữ liệu và các yêu cầu bảo mật như RBAC hay audit logging. Bên cạnh đó, nhiều giả định ban đầu thường quá lạc quan, đặc biệt là về chất lượng dữ liệu, thời gian review và mức độ phức tạp của hệ thống khi triển khai thực tế.

---

# 7. Sensitivity Analysis

Khi tỷ lệ clinical summary tăng, chi phí sẽ tăng mạnh do kéo theo nhu cầu human review. Tương tự, việc tăng kích thước context sẽ làm tăng token usage, latency và compute cost, cho thấy rõ trade-off giữa chất lượng và chi phí.

---

# 8. Key Insights

Chi phí của một hệ thống AI không nằm chủ yếu ở model mà ở toàn bộ hệ thống xung quanh, bao gồm con người, hạ tầng và tích hợp. Khả năng mở rộng của hệ thống bị giới hạn bởi các yếu tố này, không phải bởi bản thân LLM.

---

# 9. Recommendations

Việc tối ưu nên tập trung vào giảm số lượng request tốn kém (như summary), giảm kích thước context và sử dụng các chiến lược như caching hoặc model cascading. Đồng thời, cần giảm phụ thuộc vào human review thông qua cơ chế đánh giá độ tin cậy và chỉ giữ lại review cho các trường hợp rủi ro cao.

---

# 10. Final Takeaway

HCSA không chỉ là một bài toán về AI model mà là bài toán tổng thể của hệ thống, quy trình và con người. Việc hiểu đúng cấu trúc chi phí giúp tránh tối ưu sai hướng và thiết kế hệ thống phù hợp hơn với thực tế triển khai.