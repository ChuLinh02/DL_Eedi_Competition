# DL_Eedi_Competition

## Pipeline hoạt động chi tiết

### Bước 1: Chuẩn bị dữ liệu
Dữ liệu bao gồm:
-   Câu truy vấn (train_df): Chứa thông tin câu hỏi, câu trả lời đúng/sai, và ID thứ tự (order_index).
-   Misconception mapping: Tập ánh xạ giữa tên hiểu nhầm (MisconceptionName) và ID (MisconceptionId).
Câu truy vấn được mã hóa dưới dạng query_text sử dụng get_detailed_instruct.

### Bước 2: Tính toán vector nhúng
Hàm inference
Nhập dữ liệu đầu vào:
-   Mã hóa văn bản (sentences) bằng tokenizer.
-   Chuyển batch dữ liệu đến thiết bị (GPU/CPU).
Tính toán embedding:
-   Trạng thái ẩn cuối cùng từ mô hình Transformer (last_hidden_states).
-   Lấy vector của token cuối cùng bằng last_token_pool.
-   Chuẩn hóa vector (torch.nn.functional.normalize) để đảm bảo giá trị chuẩn hóa.
Kết quả: Một từ điển với khóa là order_index và giá trị là vector nhúng tương ứng.

### Bước 3: Tính toán độ tương đồng (Cosine Similarity)
Tính độ tương đồng cos giữa vector nhúng của câu truy vấn và tất cả các vector nhúng trong misconception_mapping.
Sắp xếp kết quả:
-   Lấy top-k ID (misconception) có độ tương đồng cao nhất.
-   Kết quả dự đoán (predicts_test) là danh sách các ID sắp xếp theo độ tương đồng giảm dần.

## Hoạt động của mô hình
Qwen xử lý embedding:
-   Sử dụng kiến trúc Transformer để trích xuất vector biểu diễn của câu truy vấn và các misconception.
-   RoPE và Attention giúp mô hình hiểu ngữ cảnh dài.
LoRA tinh chỉnh:
-   Trọng số LoRA giúp mô hình thích nghi với nhiệm vụ mới mà không cần huấn luyện toàn bộ mô hình.
Pipeline inference:
-   Từ dữ liệu văn bản đầu vào, pipeline chuyển đổi thành vector nhúng, tính toán độ tương đồng và đưa ra các kết quả dự đoán.

## Ưu điểm của kiến trúc
Qwen:
-   Hiệu quả với ngữ cảnh dài và dữ liệu đa ngôn ngữ.
-   Khả năng mạnh mẽ trong xử lý văn bản và tạo vector nhúng.
LoRA:
-   Tiết kiệm tài nguyên huấn luyện.
-   Dễ dàng áp dụng và tích hợp vào các mô hình lớn.