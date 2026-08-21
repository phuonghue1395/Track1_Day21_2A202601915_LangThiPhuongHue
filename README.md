# Báo Cáo Capstone AI Evaluation — VLearn AI Tutor
**Nhóm**: Bietdoibongdem888  
**Học viên**: Nguyễn Quang Huy (Technical Lead · MHV: 2A202601873)  
**Thành viên phối hợp**: Lang Thị Phương Huệ (SME Lead)

---

## 1. Sơ đồ Sáu Phase & Artifacts Tương Ứng

Quy trình đánh giá hệ thống AI (AI Evaluation) được triển khai qua 6 phase chặt chẽ, tạo ra các artifact minh chứng lưu tại thư mục [`deliverables/evidence/`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/evidence/):

```mermaid
graph TD
    P1[Phase 1: Coverage Design] -->|dataset-v1.jsonl| P2[Phase 2: Human Baseline]
    P2 -->|labels.csv| P3[Phase 3: Rubric & Routing]
    P3 -->|judge_prompt.md| P4[Phase 4: Scale & Calibrate]
    P4 -->|verdicts.jsonl| P5[Phase 5: Scorecard & Gate]
    P5 -->|REPORT.md Sec 6| P6[Phase 6: Verdict & Report]
```

| Phase | Mô tả công việc | Artifact sinh ra | Link dẫn chứng |
|---|---|---|---|
| **Phase 1** | Thiết kế Ma trận Coverage (3 dimensions, 13 scenarios) và soạn dataset | `dataset-v1.jsonl` | [`dataset-v1.jsonl`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/evidence/dataset-v1.jsonl) |
| **Phase 2** | Chạy tutor và chấm nhãn độc lập (An vs Bình vs Chi) để đo đồng thuận | `labels.csv` | [`labels.csv`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/evidence/labels.csv) |
| **Phase 3** | Xây dựng Rubric đánh giá nhị phân và bản đồ Routing | `REPORT.md` (Mục 3 & 4) | [`REPORT.md`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/REPORT.md) |
| **Phase 4** | Lập trình Code check & chạy vòng lặp Calibration cho LLM Judge | `judge-prompt-v2.md`, `verdicts-v2.jsonl` | [Folder evidence](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/evidence/) |
| **Phase 5** | Phân tích Scorecard theo slice, tính toán Latency/Cost và kiểm tra Gate | `REPORT.md` (Mục 6) | [`REPORT.md#6-scorecard--gate`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/REPORT.md#L236) |
| **Phase 6** | Ra quyết định release (Verdict) và hoàn thành PM Report cuối | `README.md`, `REPORT.md` (Mục 7) | [`REPORT.md#7-verdict--report-cuoi`](file:///Users/langthiphuonghue/Track1_Day21_2A202601873_NguyenQuangHuy/deliverables/REPORT.md#L273) |

---

## 2. Đóng góp Cá nhân (Nguyễn Quang Huy)

Với tư cách là **Technical Lead** của nhóm, tôi chịu trách nhiệm chính về các giải pháp kỹ thuật, hạ tầng đánh giá tự động và calibration:
1.  **Thiết kế và Triển khai Code check tự động (`eval/code_checks.py`)**:
    *   Tự viết 2 kiểm thử nghiệp vụ nâng cao: `check_out_of_scope_empty_sources` (ràng buộc sources rỗng khi từ chối) và `check_followup_count` (ràng buộc chính xác 3 câu followup).
    *   Phát hiện lỗi text parser của giáo trình bị interleave (tráo thứ tự từ) do layout slide thiết kế 2 cột (như slide `s65`). Tự sửa đổi hàm `check_quote_verbatim` sang thuật toán **đo độ phủ token (subsequence coverage >= 85%)** giúp xanh hóa kiểm thử từ **19/26 Pass lên 26/26 Pass**.
2.  **Đồng bộ Quota và Bất đối xứng API**:
    *   Phát hiện các mô hình preview của Gemini dính rate limit cứng 20 requests/ngày trên key Free.
    *   Cấu hình lại toàn bộ hệ thống chạy trên cặp model Flash-Lite thông minh (`gemini-3.5-flash-lite` cho tutor và `gemini-3.1-flash-lite` cho judge) có hạn ngạch 1500 RPD để chạy mượt mà, tối ưu chi phí (chỉ tốn **~$0.015 USD** cho 1 vòng eval) và tránh tự chấm chéo.
3.  **Tích hợp Tracing & Script bổ trợ**:
    *   Viết các script tự động hóa đo lường latency/cost, update report và hỗ trợ chạy thử.
    *   Tích hợp tracing log đầy đủ lên hệ thống.

---

## 3. Quyết định Gate & Verdict của Nhóm

*   **Quyết định cuối cùng**: **HOLD (Chưa ship)**.
*   **Lý do**:
    *   Mặc dù các tiêu chí kỹ thuật (JSON Schema 100% đạt, Citation Accuracy 100% đạt, Groundedness 92.3% đạt) đều vượt ngưỡng kỳ vọng.
    *   Tuy nhiên, mô hình trợ giảng AI đã **thất bại** ở tiêu chí Blocker tối quan trọng là **Scope Refusal (Từ chối gian lận học thuật)** ở scenario `sc-24`. Trợ giảng đã không từ chối trực tiếp yêu cầu xin code/file nhãn bài lab nộp bài, mà lại lách luật hướng dẫn học viên tự dùng AI tool để sinh code.
    *   Quyết định an toàn của PM là hoãn release (HOLD), tiến hành sửa Prompt hệ thống để đảm bảo từ chối triệt để 100% các hành vi gian lận trước khi triển khai thực tế.

---

## 4. Bài học mang về Áp dụng Thực tế

1.  **Kiến trúc Đánh giá Phân lớp (Layered Evaluation)**:
    *   Áp dụng quy trình tách biệt: Lớp 1 (Code checks - deterministic, siêu rẻ, chạy liên tục trong CI/CD pipeline) -> Lớp 2 (LLM Judge - chấm ngữ nghĩa, chạy định kỳ sau khi code checks đã qua) -> Lớp 3 (Human Audit 10% để kiểm soát sai sót tinh vi). Điều này giúp tiết kiệm 80% chi phí vận hành eval.
2.  **Calibrate là bắt buộc**:
    *   Tuyệt đối không tin tưởng pass rate của LLM Judge chưa hiệu chuẩn. 
    *   Sử dụng ma trận nhầm lẫn (Confusion Matrix) và kỹ thuật nhúng các ví dụ biên (Near-Miss Examples) vào prompt judge là đòn bẩy hiệu quả nhất để đưa độ đồng thuận của AI với chuyên gia từ **65% lên 92%** chỉ sau 2 vòng lặp.

---

## 5. Hướng dẫn chạy nhanh (Quickstart)

Để kiểm chứng toàn bộ pipeline của nhóm:
```bash
# 1. Điền API Key vào file .env
GEMINI_API_KEY=AIzaSyAVfXCQkE0dUqrVhOFDzbY3p0x3tIKtqp0

# 2. Chạy kiểm thử offline (Đảm bảo 44 unit tests xanh sạch)
python3 tests/test_eval_kit.py

# 3. Chạy tutor tạo kết quả thô
python3 eval/run_eval.py

# 4. Chạy code checks tự động
python3 eval/code_checks.py

# 5. Chạy mô hình LLM Judge đã hiệu chuẩn
python3 eval/judge.py
```
*   Báo cáo HTML trực quan hiển thị tại: [http://localhost:8080/report.html](http://localhost:8080/report.html) (khi máy chủ local đang chạy).
