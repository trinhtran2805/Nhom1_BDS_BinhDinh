# Phân tích và Dự đoán Giá Bất động sản Bình Định 2026

Dự án nghiên cứu khoa học dữ liệu nhằm phân tích xu hướng và xây dựng mô hình dự báo giá bất động sản tại tỉnh Bình Định (trọng tâm TP. Quy Nhơn) dựa trên dữ liệu thực tế được thu thập từ các sàn giao dịch trực tuyến.


## Tổng quan dự án
* **Mục tiêu:** Thu thập $\ge 500$ bản ghi; Xây dựng mô hình Machine Learning dự báo giá với độ chính xác $R^2 \ge 0.75$.
* **Kết quả đạt được:** Thu thập **768 bản ghi** thực tế. Mô hình **Random Forest Tuned** đạt hiệu suất **$R^2 \approx 0.78$** trên tập kiểm tra độc lập.
* **Ứng dụng:** Cung cấp tri thức thị trường và làm cơ sở dữ liệu (knowledge base) cho các hệ thống AI Agent định giá.


## Quy trình triển khai

### 1. Thu thập dữ liệu (Web Scraping)
* **Nguồn:** `batdongsan.com.vn` và `alonhadat.com.vn`.
* **Thách thức:** Website áp dụng cơ chế chống bot mạnh (Cloudflare, Captcha, chặn IP tĩnh).
* **Giải pháp:** Sử dụng `undetected_chromedriver` giả lập trình duyệt thật kết hợp `Selenium` để tự động hóa xác thực và `BeautifulSoup` để bóc tách dữ liệu sạch.

### 2. Tiền xử lý & Làm sạch (Cleaning)
* **Xử lý số liệu:** Dùng RegEx bóc tách đơn vị dính kèm (tỷ, triệu, m²...) và quy đổi toàn bộ giá về đơn vị **Tỷ VNĐ**.
* **Hình học:** Tách cột kích thước thô (5x20m) thành hai biến độc lập: `ngang (m)` và `doc (m)`.
* **Xử lý ngoại lai:** Áp dụng **Log Transformation** (`np.log1p`) cho biến giá để nén sai số ở phân khúc giá cao, giúp phân phối dữ liệu hội tụ về dạng chuẩn.

### 3. Phân tích khám phá (EDA)
* **Phân phối:** Thị trường bị **lệch phải mạnh**; Trung vị giá đạt **2.99 tỷ**, trong khi trung bình là **4.39 tỷ** do bị kéo bởi các biệt thự hạng sang.
* **Vị trí:** TP. Quy Nhơn chiếm vị thế áp đảo với **82% tin đăng** (631/767 tin).
* **Quy luật:** Diện tích giao dịch phổ biến nhất là **80m²** với mặt tiền tiêu chuẩn **5m**.

### 4. Xây dựng mô hình (Modeling)
* **Kỹ thuật đặc trưng (Feature Engineering):**
    * **Target Encoding:** Mã hóa 138 tên đường dựa trên đơn giá trung vị nội bộ để cô lập yếu tố "vị trí đắc địa".
    * **Text Mining:** Trích xuất các thuộc tính "Premium" từ tiêu đề như: `is_lo_goc`, `is_mat_tien`, `is_ngop_cat_lo`.
* **Kiến trúc Pipeline:** Sử dụng `RobustScaler` để chống nhiễu bởi Outliers và `OneHotEncoder` xử lý biến định tính.
* **Hiệu suất mô hình:**

| Mô hình | CV $R^2$ (5-Fold) | Test $R^2$ (Hold-out) | MAE (Tỷ) |
| :--- | :---: | :---: | :---: |
| Ridge Regression (Log) | 0.7778 | 0.7031 | 1.192 |
| **Random Forest Tuned** | **0.7794** | **0.7799** | **1.005** |

*(Kết quả đạt MAE ~1 tỷ VNĐ - mức sai số chấp nhận được với biên độ giá lớn của thị trường)*.


## Cấu trúc thư mục
```text
nhom1-bds-binhdinh/
├── data/
│   ├── raw_data.csv          # Dữ liệu thô từ scraping
│   └── clean_data.csv        # Dữ liệu sạch bàn giao cho Nhóm 2
├── notebooks/
│   ├── 01_scraping.ipynb     # Thu thập & Làm sạch dữ liệu
│   ├── 02_EDA.ipynb          # Phân tích khám phá & Trực quan hóa
│   └── 03_model.ipynb        # Huấn luyện mô hình & Đánh giá
├── reports/
│   ├── bao_cao_final.pdf     # Báo cáo khoa học (25-40 trang)
│   └── slide_thuyet_trinh.pptx
├── README.md                 # Hướng dẫn dự án
└── requirements.txt          # Danh sách thư viện cài đặt
```



## Cài đặt và Sử dụng

1. **Clone Repo:**
   ```bash
   git clone https://github.com/your-username/nhom1-bds-binhdinh.git
   ```
2. **Cài đặt thư viện:**
   ```bash
   pip install -r requirements.txt
   ```
3. **Thứ tự thực thi:**
    * Chạy `01_scraping.ipynb` để thu thập và làm sạch dữ liệu mới.
    * Chạy `02_EDA.ipynb` để quan sát các biểu đồ phân tích thị trường.
    * Chạy `03_model.ipynb` để huấn luyện và kiểm tra mô hình dự báo giá.

## Thành viên thực hiện
* **Nhóm 1:** Khoa Toán Thống kê - Đại học Quy Nhơn.
* **Liên hệ:** [Trinhtran.28052005@gmail.com].

*Lưu ý: Dự án sử dụng dữ liệu thực tế tại thời điểm tháng 5/2026 cho mục đích nghiên cứu và học thuật.*
