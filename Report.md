# Report — Spark NLP TF-IDF Pipeline

## 1) Implementation steps (rõ ràng)
1. **Khởi tạo SparkSession** ở chế độ local: `appName = "NLP Pipeline Example"`, `master = "local[*]"`.
2. **Đọc dữ liệu C4** (định dạng `json.gz`) vào DataFrame và **giới hạn 1000 bản ghi** để chạy nhanh:  
   `spark.read.json(dataPath).limit(1000)`.
3. **Xây dựng Spark ML Pipeline** gồm các stage:
   - **RegexTokenizer**: tách từ từ cột `text` → `tokens`.
   - **StopWordsRemover**: loại stop words từ `tokens` → `filtered_tokens`.
   - **HashingTF**: biến `filtered_tokens` → vector thưa `raw_features` (kích thước 20,000).
   - **IDF**: tính trọng số nghịch tài liệu, tạo `features` cuối cùng.
4. **Fit & Transform**:
   - `pipeline.fit(initialDF)` để ước lượng IDF.
   - `pipelineModel.transform(initialDF)` để sinh cột `features`.
5. **Ghi kết quả & log**:
   - Ghi **20 dòng đầu** (text rút gọn + TF-IDF vector) vào `../results/lab17_pipeline_output.txt`.
   - Ghi **thời gian fit/transform**, **kích thước từ vựng sau tiền xử lý**, cấu hình vectorizer… vào `../log/lab17_metrics.log`.

---

## 2) How to run the code and log the results
- **Chuẩn bị dữ liệu**: đặt file `c4-train.00000-of-01024-30K.json.gz` đúng với biến `dataPath` trong code. Với log hiện có, chương trình chạy từ `C:\Users\fpt\Downloads`, nên `../data/...` trỏ tới `C:\Users\fpt\data\...`.
- **Chạy**:
  ```bash
  sbt run
  ```
- **Quan sát Spark UI** (tùy chọn) tại: `http://localhost:4040`.
- **File sinh ra** (theo đường dẫn tương đối trong code):
  - Kết quả: `../results/lab17_pipeline_output.txt`  → tương ứng `C:\Users\fpt\results\lab17_pipeline_output.txt`.
  - Log: `../log/lab17_metrics.log` → tương ứng `C:\Users\fpt\log\lab17_metrics.log`.

---

## 3) Explain the obtained results (giải thích kết quả)
(Từ log chạy thực tế)
- **Số bản ghi đọc**: 1000 (do `limit(1000)`).
- **Thời gian**:
  - Fit pipeline: **~3.05 giây**.
  - Transform 1000 bản ghi: **~1.32 giây**.
- **Kích thước từ vựng sau tiền xử lý** (tokenize + remove stop words + lọc token 1 ký tự): **~31,355** từ duy nhất.
- **Đặc trưng đầu ra**:
  - `HashingTF` tạo vector cố định kích thước **20,000** (dạng thưa), sau đó `IDF` gán trọng số theo độ phổ biến nghịch.
  - Mẫu hiển thị `features` có dạng:  
    `(20000,[idx1, idx2, ...],[val1, val2, ...])`, phản ánh các chỉ số đặc trưng đã băm và trọng số TF-IDF tương ứng.
- **Ghi file**:
  - Console xác nhận: *“Successfully wrote metrics to ../log/lab17_metrics.log”* và *“Successfully wrote 20 results to ../results/lab17_pipeline_output.txt”*.
- **Diễn giải**:  
  RegexTokenizer giúp tách từ tinh hơn tách theo khoảng trắng; StopWordsRemover giảm nhiễu; HashingTF cho phép vector hóa nhanh, cố định chiều (đổi lại có thể có **va chạm băm** khi vocab thực (~31k) lớn tương đối so với 20k chiều); IDF làm nổi bật các từ mang tính phân biệt.

---

## 4) Difficulties encountered & solutions (khó khăn & cách giải quyết)


---

## 5) References (tài liệu tham khảo)
- Apache Spark ML: **Tokenizer**, **RegexTokenizer**, **StopWordsRemover**, **HashingTF**, **IDF**, **Pipeline** (tài liệu chính thức).


---

## 6) Pre-trained models (nếu dùng)
- Em không sử dụng pre-trained models trong bài này.
