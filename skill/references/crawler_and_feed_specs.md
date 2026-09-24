# Quy Chuẩn Kỹ Thuật: Web Crawlers & Product Feeds cho ChatGPT Ads

Tài liệu này cung cấp chi tiết thông số kỹ thuật cấu hình hạ tầng cho chiến dịch ChatGPT Ads (OpenAI Ads Manager Beta).

---

## 1. Web Crawlers: OAI-AdsBot & OAI-SearchBot

OpenAI sử dụng các crawler chuyên dụng để kiểm duyệt an toàn nội dung, tính xác thực của Landing Page và tải tài nguyên hình ảnh sản phẩm.

### 1.1. Bảng Phân Tách Nhiệm Vụ Của Crawlers
| Tên Bot | Bắt buộc / Khuyến nghị | Nhiệm vụ chính | Ghi chú WAF |
| :--- | :--- | :--- | :--- |
| **`OAI-AdsBot`** | **BẮT BUỘC (100%)** | Quét URL Landing Page của mẫu quảng cáo để duyệt chính sách và trích xuất vector ngữ nghĩa | Đã được Cloudflare xác thực toàn cầu (Verified Bot) |
| **`OAI-SearchBot`** | **Khuyến nghị cao** | Quét nội dung web công khai và tải các đường dẫn hình ảnh (`image_url`) trong Product Feed | Tránh chặn 403 trên CDN chứa hình ảnh |

### 1.2. Cấu Hình File `robots.txt`
```txt
# Cho phép bot kiểm duyệt quảng cáo
User-agent: OAI-AdsBot
Allow: /

# Cho phép bot lập chỉ mục tìm kiếm và nạp hình ảnh sản phẩm
User-agent: OAI-SearchBot
Allow: /
```

### 1.3. Xác Thực Danh Sách IP Chính Thức
Nếu máy chủ hoặc firewall mạng nội bộ yêu cầu cấu hình mở dải IP tĩnh (IP Whitelist), sử dụng 2 tệp JSON chính thức cập nhật tự động từ OpenAI:
* Dải IP cho `OAI-AdsBot`: `https://openai.com/adsbot.json`
* Dải IP cho `OAI-SearchBot`: `https://openai.com/searchbot.json`

### 1.4. Xử Lý Các Mã Lỗi Phổ Biến
* **HTTP 403 Forbidden:**
  * *Nguyên nhân:* Bộ lọc bot tự động của Cloudflare, Akamai, Wordfence hoặc WAF máy chủ chặn User-Agent không phải trình duyệt thông thường.
  * *Khắc phục:* Tạo Custom WAF Rule: `(http.user_agent contains "OAI-AdsBot") or (cf.client.bot)` -> Action: `Skip / Allow`.
* **HTTP 429 Too Many Requests:**
  * *Nguyên nhân:* Khi tải lên hàng loạt quảng cáo bằng Bulk Upload CSV, crawler quét đồng thời nhiều URL gây kích hoạt giới hạn tần suất (Rate Limiting).
  * *Khắc phục:* Chia nhỏ file upload thành các đợt 50-100 quảng cáo hoặc nâng ngưỡng rate limit cho User-Agent `OAI-AdsBot`.

---

## 2. Quy Chuẩn Kỹ Thuật Product Feeds (Catalog)

Chiến dịch Product Feed dành riêng cho các nhà bán lẻ trực tuyến có danh mục hàng hóa lớn hoặc thay đổi giá/tồn kho liên tục.

### 2.1. Chu Kỳ Đồng Bộ & Thời Hạn Hết Hạn
* **Quy tắc 14 ngày:** Mọi sản phẩm nạp vào Ads Manager sẽ **tự động hết hạn sau 14 ngày** nếu không có dữ liệu cập nhật mới.
* **Khuyến nghị phương thức nạp:**
  * **Hosted HTTPS URL (Tốt nhất):** Cung cấp đường dẫn cố định, Ads Manager tự động quét định kỳ.
  * **SFTP Server-to-Server (Cho hệ thống lớn):** Cấu hình tự động đẩy file nén qua SFTP vào tài khoản OpenAI Ads Manager.
  * **File CSV/TXT thủ công:** Chỉ khuyến nghị dùng thử nghiệm nhỏ.

### 2.2. Các Trường Dữ Liệu Bắt Buộc Trong Feed
| Tên trường | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- |
| `id` | String | Mã định danh duy nhất của sản phẩm (SKU) |
| `title` | String | Tên sản phẩm rõ ràng, không viết hoa toàn bộ |
| `description` | String | Mô tả ngắn gọn tính năng, chất liệu, ứng dụng |
| `link` | URL | Đường dẫn trang chi tiết sản phẩm (Landing Page) |
| `image_link` | URL | Ảnh sản phẩm chất lượng cao (phải cho phép `OAI-SearchBot` truy cập) |
| `price` | String | Giá bán kèm đơn vị tiền tệ (ví dụ: `150 USD` hoặc `350000 VND`) |
| `availability` | String | Tình trạng hàng: `in_stock`, `out_of_stock`, `preorder` |
| `brand` | String | Tên thương hiệu sản phẩm |

### 2.3. Sử Dụng `ads_metadata` Để Phân Nhóm Ad Group
Để chia nhỏ danh mục sản phẩm vào các nhóm quảng cáo khác nhau (ví dụ: nhóm sản phẩm biên lợi nhuận cao, nhóm hàng chạy mùa vụ), sử dụng trường `ads_metadata`:
```json
{
  "ads_metadata": {
    "bidding_tier": "high_margin",
    "seasonal_tag": "summer_2026",
    "custom_label_0": "bestseller"
  }
}
```
Trong Ads Manager Beta, khi tạo Ad Group, bạn có thể áp bộ lọc: `Filter: custom_label_0 == "bestseller"` để chỉ phân phối nhóm sản phẩm này.
