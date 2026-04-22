# WORKSHEET 3 – OPTIMIZATION STRATEGY

## Scenario 2 – Hospital Clinical Support Assistant (HCSA)

---

## 1. Mục tiêu

Chọn đúng chiến lược tối ưu phù hợp với bối cảnh bệnh viện:

* Ưu tiên **độ chính xác, an toàn và compliance** hơn chi phí
* Giảm chi phí nhưng **không làm tăng rủi ro lâm sàng**
* Tối ưu dựa trên **risk zone (Green / Yellow / Red)**

---

## 2. Chọn 3 chiến lược optimization phù hợp nhất

---

### 2.1. Model Cascading (Selective Inference theo Risk Zone)

| Tiêu chí                | Nội dung                                                                                                                                                                           |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tiết kiệm phần nào?** | Giảm chi phí model lớn (GPT-4/5 class), giảm latency cho request đơn giản                                                                                                          |
| **Cách làm**            | Phân tầng theo risk zone: <br> - Green (FAQ, SOP) → model nhỏ <br> - Yellow (summary draft) → model trung bình + citation <br> - Red (clinical decision) → block hoặc human review |
| **Lợi ích**             | - Giảm chi phí 40–70% <br> - Đảm bảo safety theo đúng mô hình rủi ro <br> - Align với Zero-Trust AI                                                                                |
| **Trade-off**           | - Cần classifier / policy engine <br> - Sai routing → rủi ro cao                                                                                                                   |
| **Thời điểm áp dụng**   | Bắt buộc từ MVP (Phase 1)                                                                                                                                                          |

---

### 2.2. Caching + Batch Processing (Semantic + Operational Cache)

| Tiêu chí                | Nội dung                                                                                                         |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Tiết kiệm phần nào?** | Giảm số lần gọi LLM, giảm chi phí inference, giảm tải hệ thống                                                   |
| **Cách làm**            | - Cache câu hỏi SOP/FAQ lặp lại <br> - Cache kết quả RAG có citation <br> - Batch summary khi không cần realtime |
| **Use case phù hợp**    | - Green zone: SOP, biểu mẫu, quy trình <br> - Administrative Q&A                                                 |
| **Lợi ích**             | - Latency gần như instant cho câu hỏi phổ biến <br> - Giảm cost đáng kể khi scale                                |
| **Trade-off**           | - Risk stale data nếu SOP update <br> - Không dùng cho dữ liệu patient-specific (PHI)                            |
| **Thời điểm áp dụng**   | Áp dụng sớm (Phase 1–2) nhưng giới hạn scope                                                                     |

---

### 2.3. Context Minimization (Prompt Optimization)

| Tiêu chí                | Nội dung                                                                                                                                               |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Tiết kiệm phần nào?** | Giảm token usage → giảm chi phí LLM + tăng tốc độ                                                                                                      |
| **Cách làm**            | - Chỉ gửi phần hồ sơ liên quan (relevant chunk) <br> - Dùng metadata filtering (khoa, version, thời gian) <br> - Kết hợp Hybrid Search (BM25 + vector) |
| **Lợi ích**             | - Tăng accuracy (ít noise) <br> - Giảm hallucination <br> - Giảm cost đáng kể                                                                          |
| **Trade-off**           | - Nếu retrieval sai → mất context quan trọng <br> - Phụ thuộc chất lượng RAG pipeline                                                                  |
| **Thời điểm áp dụng**   | Bắt buộc từ MVP                                                                                                                                        |

---

## 3. Các chiến lược KHÔNG ưu tiên (hiện tại)

| Chiến lược                      | Lý do không ưu tiên                    | Khi nào cân nhắc                         |
| ------------------------------- | -------------------------------------- | ---------------------------------------- |
| Prompt Compression              | Rủi ro mất thông tin y khoa quan trọng | Khi đã validate không ảnh hưởng accuracy |
| Self-hosted model (early stage) | Tốn hạ tầng, khó đảm bảo chất lượng    | Khi volume lớn + yêu cầu privacy cao     |
| Aggressive fine-tuning          | Tốn chi phí, khó kiểm soát drift       | Khi có đủ dataset chuẩn hóa              |

---

## 4. Chốt chiến lược

### Làm NGAY (Phase 1 – MVP)

* Model Cascading (risk-based routing)
* Context Minimization (RAG chuẩn + metadata filter)
* Caching (giới hạn cho SOP / FAQ)

### Làm SAU (Phase 2–3)

* Advanced model routing (multi-model optimization)
* Batch processing cho summary

### Làm khi scale lớn

* Self-hosted / distilled model
* Tối ưu sâu inference (quantization, distillation)

---

## 5. Liên kết với kiến trúc hệ thống

Các chiến lược trên map trực tiếp vào kiến trúc HCSA:

* **Middleware Layer**

  * RBAC / ABAC → phục vụ selective inference
  * Audit log → kiểm soát caching & routing
  * Policy engine → enforce risk zone

* **RAG Pipeline**

  * Hybrid search → context minimization
  * Metadata filtering → giảm noise
  * Citation requirement → giảm hallucination

* **Model Layer**

  * Cascading / routing
  * Fallback model
  * Guardrails

---

## 6. Ràng buộc đặc thù bệnh viện

* Không cache dữ liệu chứa PHI theo cách reusable
* Không reuse response giữa các bệnh nhân
* Luôn enforce:

  * RBAC
  * Audit log
  * Citation bắt buộc với SOP
* Red zone:

  * Không cho AI tự suy luận
  * Không optimize bằng cách “đoán nhanh”

---

## 7. Nguyên tắc tối ưu cốt lõi

* Không tối ưu chi phí trước **độ chính xác**
* Không tối ưu latency bằng cách giảm **context quan trọng**
* Không tối ưu hệ thống bằng cách **bỏ qua compliance**
* Mọi optimization phải:

  * pass evaluation
  * không tăng hallucination
  * không vi phạm policy

---

## 8. Kết luận

Chiến lược tối ưu của HCSA không phải là giảm cost tối đa, mà là:

* **Tối ưu theo rủi ro (risk-aware optimization)**
* **Tối ưu theo use case (admin vs clinical-adjacent)**
* **Tối ưu nhưng vẫn giữ kiểm soát (human-in-the-loop + guardrails)**

Ba chiến lược cốt lõi:

* Model Cascading
* Caching (có kiểm soát)
* Context Minimization

Đây là cách tiếp cận thực tế, phù hợp với môi trường bệnh viện, giúp:

* giảm chi phí,
* tăng hiệu quả,
* nhưng vẫn đảm bảo an toàn và tuân thủ.

---
