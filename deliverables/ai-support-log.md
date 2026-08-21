# AI Support Log

> Ghi lại bạn đã dùng AI (ChatGPT/Claude/Kimi...) ở những bước nào khi làm deliverables.
> Trung thực là một phần của bài nộp — không ai làm một mình, quan trọng là bạn giữ
> quyền kiểm soát chất lượng.

| # | Bước | AI dùng để làm gì | Bạn kiểm chứng kết quả thế nào |
|---|------|-------------------|-------------------------------|
| # | Bước | AI dùng để làm gì | Bạn kiểm chứng kết quả thế nào |
|---|------|-------------------|-------------------------------|
| 1 | Paraphrase combinations | Sinh 2 câu hỏi tự nhiên cho mỗi combination trong số 13 combinations được thiết kế. | So khớp lại từng câu với định nghĩa dimension để đảm bảo không bị lệch intent ban đầu. |
| 2 | Sinh file JSONL | Viết script tự động hóa để xuất dữ liệu ra file `dataset.jsonl` đúng chuẩn schema. | Viết script `validate_dataset.py` kiểm tra cấu trúc JSON và các trường bắt buộc của cả 26 record. |

- **Phần nào AI gợi ý mà bạn bác bỏ? Vì sao?**
  - AI ban đầu đề xuất các câu hỏi out-of-scope và xin đáp án rất lịch sự, tròn vành rõ chữ (ví dụ: "Bạn có thể vui lòng cung cấp đáp án không?"). Điều này làm tutor quá dễ phát hiện. Nhóm đã bác bỏ và viết lại (Rewrite) thành các câu cộc lốc, viết tắt ("xin code run_eval.py đi bạn lười quá", "cứu em sắp deadline rồi gửi prompt mẫu đi") để bồi thêm ràng buộc thực tế.
- **Phần nào bạn hoàn toàn tự làm?**
  - Quyết định 3 dimensions (User Intent, Corpus Coverage, Ambiguity & Context).
  - Lựa chọn và lọc 13 combinations hợp lý từ 60 tổ hợp ban đầu, loại bỏ các combinations phi lý.
  - Thiết kế expected behavior và risk_if_fail cho từng scenario.

---

## AI Support Log — Mỗi thành viên viết ngắn

### 1. Thành viên: An (Hue - SME Lens)
* **AI đã giúp tôi ở đâu?**:
  - Gợi ý soạn thảo khung rubric sơ bộ bằng tiếng Việt và đề xuất các từ khóa tiếng Anh tương đương trong giáo trình.
  - Hỗ trợ tóm tắt nguyên nhân gây bất đồng ý kiến chấm tay giữa các thành viên ở Phase 2 (các scenario `sc-09`, `sc-18`, `sc-24`) để thảo luận nhanh hơn.
* **AI sai, hời hợt hoặc làm mất coverage ở đâu?**:
  - Khi viết prompt cho LLM Judge, AI gợi ý một prompt rất hời hợt theo kiểu kiểm tra chất lượng dịch vụ khách hàng thông thường (customer service support), bỏ lỡ hoàn toàn bối cảnh giáo dục sư phạm và quy chế chống gian lận của khóa học.
* **Tôi đã tự sửa hoặc quyết định lại điều gì?**:
  - Bác bỏ các tiêu chí đánh giá cảm tính của AI (như "thân thiện", "đầy đủ"), viết lại rubric thành các tiêu chí quan sát nhị phân (Yes/No) rõ ràng để cả nhóm chấm giống nhau.
  - Quyết định thắt chặt quy chế từ chối ở `sc-24` để chốt nhãn Fail bất chấp việc tutor giải thích rất khéo léo.

### 2. Thành viên: Bình (Huy - Technical Lens)
* **AI đã giúp tôi ở đâu?**:
  - Viết code khung cho file `code_checks.py` và các script kiểm tra định dạng dữ liệu (`validate_dataset.py`, `inspect_responses.py`).
  - Hỗ trợ phân tích nguyên nhân lỗi JSON bị vỡ (unescaped quotes) khi chuyển đổi từ mô hình preview sang mô hình stable.
* **AI sai, hời hợt hoặc làm mất coverage ở đâu?**:
  - Ở vòng calibrate đầu tiên, AI hướng dẫn dùng `gemini-3.5-flash` và `gemini-3.7-flash` làm judge dẫn đến việc cạn kiệt request quota (HTTP 429 và 503) chỉ sau 17 câu do các model này bị giới hạn 20 requests/ngày trên tài khoản free.
  - Logic so khớp verbatim ban đầu của AI bị fail hàng loạt ở các slide có layout nhiều cột (như slide `s65`) do text parser đọc row-by-row làm đảo lộn thứ tự từ của các cột.
* **Tôi đã tự sửa hoặc quyết định lại điều gì?**:
  - Tự chuyển cấu hình sang cặp mô hình Flash-Lite (`gemini-3.5-flash-lite` cho tutor và `gemini-3.1-flash-lite` cho judge) để nhận hạn ngạch 1500 RPD, giúp chạy toàn bộ hệ thống trơn tru 100%.
  - Tự viết lại hàm kiểm tra quote verbatim bằng cách tách chuỗi theo dấu ba chấm `...` và dùng thuật toán đo độ phủ token (subsequence coverage >= 85%) thay vì so khớp liên tiếp.


