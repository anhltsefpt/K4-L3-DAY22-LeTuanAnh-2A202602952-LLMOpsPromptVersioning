# Phân tích RAGAS — Prompt V1 vs V2

| Metric | V1 (ngắn gọn, 2-4 câu) | V2 (chuyên gia, 3-5 câu) |
|---|---|---|
| faithfulness | **0.9562** | 0.9263 |
| answer_relevancy | **0.9142** | 0.8893 |
| context_recall | 1.0000 | 1.0000 |
| context_precision | 0.9450 | 0.9439 |

Mục tiêu faithfulness ≥ 0.8: **đạt ở cả 2 phiên bản**.

## Nhận xét

- **context_recall / context_precision gần như bằng nhau**: 2 metric này chỉ đo chất lượng retriever,
  mà V1 và V2 dùng chung retriever (FAISS, k=3) — prompt chỉ ảnh hưởng tới câu trả lời.
  (Bảng terminal ghi "← V2" ở context_recall chỉ vì code dùng `>`; thực tế là hòa.)
- **V1 faithfulness cao hơn**: câu trả lời ngắn → ít claim → ít cơ hội thêm thông tin ngoài context.
  V2 yêu cầu 3-5 câu với giọng "chuyên gia" nên dễ diễn giải/khái quát vượt quá context.
- **V1 answer_relevancy cao hơn**: câu trả lời ngắn bám sát câu hỏi; V2 thêm ý phụ làm câu hỏi
  sinh ngược (do RAGAS tạo ra) lệch khỏi câu hỏi gốc.
- **Kết luận**: với knowledge base dạng fact ngắn, prompt ngắn gọn (V1) tốt hơn. V2 phù hợp hơn
  khi câu hỏi cần tổng hợp nhiều đoạn context.

## Ghi chú kỹ thuật

- RAGAS dùng LLM làm giám khảo (`gpt-4o-mini`, temperature=0) — cùng model với hệ thống được chấm
  nên có thể có thiên lệch; điểm dao động nhẹ giữa các lần chạy.
- Lần chạy đầu V2 faithfulness = NaN do 2 job lỗi `OpenAIConnectionError`; đã sửa code để bỏ qua
  cả `None` và `NaN` khi tính trung bình, rồi chạy lại (log: `03_ragas_run.log`).
