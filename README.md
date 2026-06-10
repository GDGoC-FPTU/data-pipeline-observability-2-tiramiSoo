# Day 10 Lab: Data Pipeline & Data Observability

**Mã số sinh viên:** AI20K-2A202600583  
**Email:** 26ai.dungnt3@vinuni.edu.vn  
**Họ và tên:** Nguyễn Tuấn Dũng

---

## Mô tả

Dự án này xây dựng một pipeline ETL đơn giản để xử lý dữ liệu sản phẩm từ file JSON. Pipeline gồm 4 bước chính:

1. Extract: đọc dữ liệu từ `raw_data.json`.
2. Validate: loại bỏ record không hợp lệ, bao gồm `price <= 0` và `category` bị rỗng.
3. Transform: tính `discounted_price = price * 0.9`, chuẩn hóa `category` sang Title Case, và thêm cột `processed_at`.
4. Load: lưu dữ liệu đã xử lý vào `processed_data.csv`.

Ngoài pipeline ETL, bài lab còn có phần stress test để quan sát tác động của dữ liệu rác lên một agent mô phỏng. Kết quả cho thấy khi dữ liệu có record bị poison hoặc có outlier, agent có thể đưa ra câu trả lời sai dù câu hỏi truy vấn không thay đổi.

---

## Cách chạy

### Cài đặt thư viện

Cần cài Python và pandas:

```bash
pip install pandas
```

Nếu dùng virtual environment:

```bash
python -m venv venv
.\venv\Scripts\activate
pip install pandas
```

### Chạy ETL Pipeline

```bash
python solution.py
```

Sau khi chạy thành công, chương trình sẽ tạo file `processed_data.csv`.

### Tạo dữ liệu rác

```bash
python generate_garbage.py
```

Lệnh này tạo file `garbage_data.csv` chứa các record bị poison để dùng cho stress test.

### Chạy Agent Simulation

```bash
python agent_simulation.py
```

Script này kiểm tra phản hồi của agent với dữ liệu sạch và dữ liệu rác.

---

## Cấu trúc thư mục

```text
.
|-- solution.py             # Script ETL pipeline
|-- raw_data.json           # Dữ liệu nguồn ban đầu
|-- processed_data.csv      # Dữ liệu đầu ra sau khi ETL
|-- generate_garbage.py     # Script tạo dữ liệu rác
|-- garbage_data.csv        # Dữ liệu rác/poisoned cho stress test
|-- agent_simulation.py     # Agent mô phỏng truy vấn dữ liệu
|-- experiment_report.md    # Báo cáo thí nghiệm
|-- README.md               # Tài liệu hướng dẫn
`-- tests/
    `-- test_autograder.py  # Test tự động của bài lab
```

---

## Kết quả ETL

Khi chạy:

```bash
python solution.py
```

Pipeline in ra kết quả:

```text
Validation summary: 3 kept, 2 dropped.
Errors found: [{'id': 3, 'reason': 'Price <= 0'}, {'id': 4, 'reason': 'Missing Category'}]
Successfully loaded 3 records to processed_data.csv
Pipeline completed! 3 records saved.
```

Tóm tắt kết quả:

| Chỉ số | Giá trị |
|--------|---------|
| Tổng số record đầu vào | 5 |
| Số record hợp lệ được giữ lại | 3 |
| Số record bị loại | 2 |
| Lý do loại record 1 | Record id 3 có `price <= 0` |
| Lý do loại record 2 | Record id 4 bị thiếu `category` |
| File đầu ra | `processed_data.csv` |

File đầu ra có thêm các cột quan trọng:

| Cột | Ý nghĩa |
|-----|---------|
| `discounted_price` | Giá sau khi giảm 10% |
| `processed_at` | Thời điểm dữ liệu được xử lý |

---

## Kết quả Agent Simulation

Sau khi chạy:

```bash
python generate_garbage.py
python agent_simulation.py
```

Kết quả terminal:

```text
Testing with CLEAN data:
Agent Error: I'm choking on the data! ([Errno 2] No such file or directory: '../exercise-etl-automation/solution-code/processed_data.csv')

Testing with GARBAGE data:
Agent: Based on my data, the best choice is Nuclear Reactor at $999999.
```

Diễn giải câu trả lời bằng tiếng Việt:

| Kịch bản | Câu trả lời của agent | Nhận xét |
|----------|------------------------|----------|
| Dữ liệu sạch | Agent báo lỗi vì không tìm thấy file `processed_data.csv` theo đường dẫn đang được cấu hình trong `agent_simulation.py`. | Đây là lỗi đường dẫn, không phải lỗi logic của dữ liệu sạch. |
| Dữ liệu rác | Agent chọn `Nuclear Reactor` với giá `$999999`. | Đây là câu trả lời không hợp lý vì agent bị ảnh hưởng bởi record bị poison/outlier trong `garbage_data.csv`. |

Kết quả này minh họa rõ rằng chất lượng dữ liệu ảnh hưởng trực tiếp đến câu trả lời của agent. Khi dữ liệu có outlier giá quá lớn hoặc record bị poison, logic chọn sản phẩm theo giá cao nhất sẽ bị kéo về kết quả sai. Vì vậy, validation, cleaning và monitoring là các bước quan trọng trước khi đưa dữ liệu vào hệ thống AI.

---

## Kết luận

Pipeline ETL đã xử lý thành công dữ liệu đầu vào, loại 2 record không hợp lệ và lưu 3 record hợp lệ vào CSV. Phần stress test cho thấy prompt tốt vẫn chưa đủ nếu dữ liệu sai hoặc bị poison. Trong hệ thống AI/RAG, chất lượng dữ liệu là nền tảng quan trọng để agent trả lời đáng tin cậy.
