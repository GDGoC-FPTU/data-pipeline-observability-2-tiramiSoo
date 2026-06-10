# Experiment Report: Data Quality Impact on AI Agent

**Student ID:** AI20K-2A202600583  
**Name:** Nguyễn Tuấn Dũng  
**Date:** 10/06/2026

---

## 1. Kết quả thí nghiệm

Chạy `agent_simulation.py` với 2 bộ dữ liệu và ghi lại kết quả:

| Scenario | Agent Response | Accuracy (1-10) | Notes |
|----------|----------------|-----------------|-------|
| Clean Data (`processed_data.csv`) | Agent báo lỗi: không tìm thấy file `../exercise-etl-automation/solution-code/processed_data.csv`. | 0 | Đây là lỗi đường dẫn trong `agent_simulation.py`, vì script đang trỏ tới một thư mục khác thay vì file `processed_data.csv` trong repo hiện tại. |
| Garbage Data (`garbage_data.csv`) | Agent trả lời: `Based on my data, the best choice is Nuclear Reactor at $999999.` | 1 | Câu trả lời không hợp lý vì agent bị ảnh hưởng bởi record outlier/poisoned có giá quá lớn. |

---

## 2. Phân tích và nhận xét

### Tại sao Agent trả lời sai khi dùng Garbage Data?

Agent trả lời sai vì logic trong `agent_simulation.py` chọn sản phẩm thuộc category `electronics` có giá cao nhất. Trong `garbage_data.csv`, record `Nuclear Reactor` được gán category là `electronics` và giá là `999999`, đây là một outlier rất lớn so với dữ liệu sản phẩm bình thường. Vì script không có bước validate hoặc lọc outlier trước khi đưa dữ liệu cho agent, record độc hại này trở thành lựa chọn tốt nhất theo logic hiện tại.

Ngoài outlier, file garbage còn có nhiều vấn đề chất lượng dữ liệu khác như duplicate ID, sai kiểu dữ liệu ở cột `price`, giá trị null, và record không đầy đủ. Duplicate ID làm hệ thống khó xác định bản ghi nào là đúng. Sai kiểu dữ liệu có thể làm bước tính toán hoặc so sánh bị lỗi. Null values khiến category hoặc product bị thiếu, làm agent không thể lọc dữ liệu chính xác. Những lỗi này cho thấy nếu dữ liệu đầu vào không được kiểm tra kỹ, agent có thể đưa ra câu trả lời sai dù prompt hoặc câu hỏi của người dùng vẫn hợp lý.

---

## 3. Kết luận

**Quality Data > Quality Prompt?** Tôi đồng ý.

Prompt tốt chỉ giúp agent hiểu yêu cầu của người dùng, nhưng câu trả lời cuối cùng vẫn phụ thuộc rất nhiều vào dữ liệu mà agent đang sử dụng. Nếu dữ liệu bị sai, thiếu, trùng lặp, sai kiểu hoặc chứa outlier độc hại, agent có thể tự tin trả lời nhưng kết quả lại không đáng tin cậy. Vì vậy, trong hệ thống AI/RAG, cần ưu tiên data quality thông qua validation, cleaning, monitoring và observability trước khi tối ưu prompt.
