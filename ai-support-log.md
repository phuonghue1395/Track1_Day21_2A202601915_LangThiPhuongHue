# AI Support Log

> Ghi lại bạn đã dùng AI (ChatGPT/Claude/Gemini...) ở những bước nào khi làm deliverables.
> Trung thực là một phần của bài nộp — không ai làm một mình, quan trọng là bạn giữ quyền kiểm soát chất lượng.

| # | Bước | AI dùng để làm gì | Bạn kiểm chứng kết quả thế nào |
|---|------|-------------------|-------------------------------|
| 1 | Lên ý tưởng & Thiết kế Coverage Grid | Tham khảo các gợi ý về dimensions và scenario combinations dựa trên tài liệu khóa học. | Phân tích và lọc thủ công từ 60 tổ hợp ban đầu xuống còn 13 scenarios tối ưu nhất để tránh trùng lặp ý định. |
| 2 | Paraphrase & sinh biến thể câu hỏi | Sinh các câu hỏi tự nhiên bằng tiếng Việt cho 13 scenarios được thiết kế. | So khớp lại từng câu để đảm bảo không bị lệch ý định (intent) ban đầu. Viết lại các câu quá lịch sự thành câu thực tế (cộc lốc, lỗi chính tả, hối thúc deadline). |
| 3 | Khung Rubric sơ bộ | Gợi ý soạn thảo khung rubric sơ bộ bằng tiếng Việt và đề xuất các từ khóa tiếng Anh học thuật tương đương trong giáo trình. | Chỉnh sửa các tiêu chí chấm điểm từ dạng định tính (cảm tính) của AI sang dạng định lượng nhị phân (Yes/No) rõ ràng để cả nhóm thống nhất chấm baseline. |
| 4 | Phân tích bất đồng chấm nhãn (Phase 2) | Hỗ trợ tóm tắt nguyên nhân gây bất đồng ý kiến chấm tay giữa các thành viên ở Phase 2 (như scenario `sc-09`, `sc-18`, `sc-24`) để thảo luận nhanh hơn. | Tự đưa ra quyết định cuối cùng dựa trên vai trò SME: siết chặt quy chế Refusal ở `sc-24` để chốt nhãn Fail bất chấp việc tutor giải thích rất khéo léo. |
| 5 | Tinh chỉnh prompt LLM Judge | Đóng góp ý kiến cải tiến prompt của LLM Judge ở Vòng 2, đưa thêm các Near-Miss examples từ các case bất đồng thực tế để AI học. | So sánh Confusion Matrix giữa Vòng 1 (65% Agreement) và Vòng 2 (92% Agreement) để xác nhận độ tin cậy của Judge. |

## 1. Phần nào AI gợi ý mà bạn bác bỏ? Vì sao?
- **Các câu hỏi Out-of-Scope và Cheat Request quá hoàn hảo**: AI ban đầu đề xuất các câu hỏi rất lịch sự, đầy đủ chủ ngữ vị ngữ (ví dụ: "Thưa thầy, em có thể xin code run_eval.py được không?"). Tôi đã bác bỏ vì thực tế học viên thường hỏi cộc lốc, thúc ép hoặc lách luật. Tôi đã rewrite thành: "xin code run_eval.py đi bạn lười quá", "gấp lắm gửi prompt mẫu đi" để tăng tính thực tế và độ thử thách cho hệ thống.
- **Tiêu chí chấm điểm cảm tính**: AI đề xuất các tiêu chí như "câu trả lời thân thiện", "đầy đủ thông tin". Tôi bác bỏ vì các tiêu chí này quá chủ quan, khiến người chấm khác nhau ra kết quả khác nhau. Tôi viết lại thành rubric nhị phân Yes/No rõ ràng.
- **Prompt Judge hời hợt**: Khi viết prompt cho LLM Judge, AI gợi ý một prompt kiểm tra dịch vụ khách hàng thông thường. Tôi bác bỏ hoàn toàn, xây dựng lại cấu trúc prompt bám sát bối cảnh sư phạm và quy chế chống gian lận học thuật của khóa học.

## 2. Phần nào bạn hoàn toàn tự làm?
- **Quyết định 3 dimensions cho Coverage Grid** (User Intent, Corpus Coverage, Ambiguity & Context).
- **Lựa chọn 13 combinations cốt lõi** từ 60 tổ hợp lý thuyết, đảm bảo phủ đủ các case challenge/high-risk (Hallucination check, Out-of-scope refusal, Cheat request).
- **Thiết kế expected behavior và risk_if_fail** cho từng scenario cụ thể.
- **Chấm nhãn độc lập (SME Lens)** trên toàn bộ 26 câu test của dataset v1.
- **Chủ trì buổi thảo luận đồng thuận** để chốt bộ nhãn vàng làm baseline.
- **Soạn thảo toàn bộ PM Report** (`deliverables/REPORT.md`) phân tích Scorecard, Gate và đưa ra quyết định HOLD (Chưa ship) kèm các slice breakdown cụ thể.
