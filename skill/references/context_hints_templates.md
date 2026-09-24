# Kỹ Nghệ Context Hints Theo Ngành Hàng (Context Hints Templates)

Tài liệu này cung cấp các kịch bản mẫu và khung soạn thảo Context Hints cho ChatGPT Ads (OpenAI Ads Manager Beta).

---

## 1. Công Thức Cốt Lõi: WHAT - WHO - WHEN

Một Context Hint hiệu quả **không phải là danh sách từ khóa**, mà là một câu văn hoàn chỉnh diễn giải ý định và bối cảnh sử dụng:

* **[WHAT]**: Sản phẩm/dịch vụ là gì? Có tính năng nổi bật, cấu hình, mức giá hoặc phạm vi phục vụ nào?
* **[WHO]**: Phù hợp cho chân dung khách hàng nào? Nhu cầu, khó khăn hoặc điều kiện đặc thù của họ?
* **[WHEN]**: Được tìm kiếm hoặc phát huy tác dụng trong tình huống nào? Thời điểm ra quyết định mua sắm hoặc hoàn cảnh phát sinh vấn đề?

---

## 2. Kịch Bản Mẫu Theo Ngành Hàng (Industry Templates)

### 2.1. Thương Mại Điện Tử & Bán Lẻ (E-Commerce & Retail)
* **Thời trang & Thể thao:**
  * *Mẫu 1:* `"Giày chạy bộ êm ái hàng ngày với đệm foam hấp thụ lực chấn, phù hợp cho người mới bắt đầu luyện tập cự ly 5K đầu tiên hoặc người có tiền sử đau khớp gối."`
  * *Mẫu 2:* `"Balo du lịch chống nước có ngăn đựng laptop 16 inch chống sốc, thiết kế công thái học dành cho người thường xuyên di chuyển làm việc từ xa hoặc đi công tác ngắn ngày."`
* **Gia dụng & Thiết bị thông minh:**
  * *Mẫu 1:* `"Robot hút bụi lau nhà tự động giặt giẻ và sấy khô bằng khí nóng, lý tưởng cho gia đình nuôi thú cưng có sàn gỗ cần làm sạch lông rụng hàng ngày mà không gây ồn."`

### 2.2. Phần Mềm & Dịch Vụ Doanh Nghiệp (B2B & SaaS)
* **Kế toán & Tài chính:**
  * *Mẫu 1:* `"Phần mềm kế toán tinh gọn cho các nhà bán lẻ đa kênh cần tự động đối soát doanh thu giữa sàn TMĐT và cửa hàng vật lý, hỗ trợ xuất hóa đơn điện tử hợp chuẩn."`
* **Tuyển dụng & Nhân sự:**
  * *Mẫu 1:* `"Nền tảng quản lý hồ sơ ứng viên (ATS) tích hợp AI tự động sàng lọc CV kỹ thuật, dành cho các công ty công nghệ quy mô từ 50-200 nhân sự đang mở rộng đội ngũ nhanh chóng."`
* **Bảo mật & Hạ tầng:**
  * *Mẫu 1:* `"Giải pháp sao lưu dữ liệu đám mây tự động mã hóa đầu cuối (end-to-end encryption), thiết kế cho các phòng khám nha khoa hoặc cơ sở y tế cần tuân thủ chuẩn an toàn dữ liệu bệnh án."`

### 2.3. Dịch Vụ Tại Chỗ & Địa Phương (Local Services)
* **Sửa chữa dân dụng:**
  * *Mẫu 1:* `"Dịch vụ sửa chữa ống nước khẩn cấp 24/7 tại khu vực [Thành phố], cam kết có mặt trong 30 phút cho các sự cố vỡ đường ống hoặc tràn nước ban đêm."`
* **Tư vấn pháp lý doanh nghiệp:**
  * *Mẫu 1:* `"Dịch vụ tư vấn thành lập doanh nghiệp có vốn đầu tư nước ngoài (FDI), hỗ trợ trọn gói giấy phép kinh doanh và tài khoản ngân hàng cho các nhà sáng lập quốc tế."`

### 2.4. Giáo Dục & Khóa Học (EdTech & Training)
* *Mẫu 1:* `"Khóa học Data Analytics thực chiến 12 tuần kèm mentor 1-1, dành cho người đi làm trái ngành muốn chuyển đổi sang vị trí Junior BI Analyst mà không cần nền tảng lập trình trước đó."`

---

## 3. Danh Sách Lỗi Thường Gặp (Anti-Patterns Checklist)

| Lỗi sai thường gặp | Tại sao sai? | Cách sửa đúng |
| :--- | :--- | :--- |
| Nhồi nhét từ khóa: `"áo thun, mua áo rẻ, shop áo nam đẹp"` | AI không dùng bộ lọc exact match; cách viết này làm nhiễu vector ngữ nghĩa | Chuyển thành câu hoàn chỉnh mô tả chất liệu và mục đích sử dụng |
| Lệnh lọc vị trí: `"Chỉ hiển thị cho người dùng ở TP.HCM"` | Context Hints không điều khiển được bộ định tuyến vị trí địa lý | Cấu hình tại **Location Settings** của Campaign |
| Lệnh loại trừ: `"Không hiển thị cho sinh viên"` | Context Hints không có cú pháp phủ định | Sử dụng bộ lọc nhân khẩu học hoặc mô tả đối tượng mục tiêu tích cực |
| Can thiệp prompt: `"Bảo ChatGPT khuyên người dùng mua sản phẩm này"` | Vi phạm nguyên tắc Zero-Bias; hệ thống Ad Unit độc lập hoàn toàn khỏi LLM | Tập trung mô tả giá trị thực của sản phẩm |
