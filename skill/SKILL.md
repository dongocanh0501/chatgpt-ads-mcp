---
name: chatgpt-ads
description: "Use when planning, creating, reviewing, or troubleshooting campaigns for ChatGPT Ads (OpenAI Ads Manager Beta): Context Hints engineering (What-Who-When), Product Feeds catalog management, conversion tracking (oCPC/CPM), crawler readiness (OAI-AdsBot/OAI-SearchBot), and Sponsored Agents."
compatibility: "Universal for marketers, advertisers, and developers managing campaigns, landing pages, or technical setups for OpenAI Ads Manager Beta."
---

# ChatGPT Ads (OpenAI Ads Manager Beta) Skill

## 1. Overview & Architecture

Kỹ năng này cung cấp hướng dẫn, khuôn mẫu hành động (actionable workflows) và quy chuẩn kỹ thuật cho việc xây dựng, tối ưu hóa và xử lý sự cố các chiến dịch trên **ChatGPT Ads (OpenAI Ads Manager Beta)**.

### Bản Chất Hệ Thống & Nguyên Tắc Trung Lập (Zero-Bias Isolation)
* **Phân phối ngữ nghĩa (Semantic Intent Matching):** ChatGPT Ads không dùng từ khóa khớp chuỗi (Exact/Phrase Keywords). Hệ thống phân tích vector ngữ nghĩa từ cuộc hội thoại của người dùng, kết hợp với **Context Hints (WHAT - WHO - WHEN)** và nội dung Landing Page được quét thực tế.
* **Độc lập tuyệt đối:** Hệ thống quảng cáo chạy tách rời hoàn toàn khỏi mô hình LLM. Nhà quảng cáo không thể can thiệp, mua chuộc hay làm thiên vị câu trả lời tự nhiên của ChatGPT. ChatGPT mặc định không đọc được quảng cáo trừ khi người dùng bấm `"Ask ChatGPT"`.
* **Phân tầng hiển thị:** Chỉ phục vụ người dùng tài khoản **Free** và **Go**. Miễn nhiễm hoàn toàn với **Plus, Pro, Business, Enterprise, Edu**, tài khoản dưới 18 tuổi và **Temporary Chats**.

---

## 2. Khi Nào Kích Hoạt Kỹ Năng Này (When to Use)

Kích hoạt kỹ năng này khi:
1. **Soạn thảo Context Hints:** Cần viết hoặc tối ưu các đoạn gợi ý ngữ cảnh tự nhiên cho Ad Groups thay thế cho danh sách từ khóa truyền thống.
2. **Cấu hình Web Server & Crawler:** Chuẩn bị Landing Page vượt qua quy trình kiểm duyệt tự động của `OAI-AdsBot` và `OAI-SearchBot` (tránh lỗi 403 WAF / 429 Rate Limit).
3. **Thiết lập Chiến Dịch & Đấu Thầu:** Lựa chọn mục tiêu chiến dịch (Views CPM, Clicks CPC, Conversions oCPC / Impression-based), đặt Bid Cap hoặc Maximize Results.
4. **Tích hợp Product Feeds:** Thiết lập danh mục bán lẻ quy mô lớn qua CSV, Hosted HTTPS URL hoặc SFTP đồng bộ định kỳ (chu kỳ 14 ngày).
5. **Đo lường & Chuyển đổi:** Cài đặt JavaScript Pixel, kích hoạt Automatic Advanced Matching hoặc tích hợp Conversions API / MMP.

---

## 3. Quy Trình Vận Hành Cốt Lõi (Core Workflows)

### Workflow 1: Kỹ Nghệ Context Hints (WHAT - WHO - WHEN Formula)
Context Hints được thiết lập ở cấp độ **Ad Group**. Bắt buộc tuân theo công thức 3 thành phần:
* **WHAT (Sản phẩm là gì):** Tính năng đặc thù, giá cả, dịch vụ chính.
* **WHO (Phục vụ ai):** Nhu cầu, phân khúc người dùng, hoàn cảnh cụ thể.
* **WHEN (Hữu ích khi nào):** Tình huống thực tế, thời điểm ra quyết định hoặc bài toán khách hàng cần giải quyết.

```
+--------------------------------------------------------------------------------+
| CÔNG THỨC: [WHAT: Sản phẩm/tính năng] + [WHO: Đối tượng/nhu cầu] + [WHEN: Hoàn cảnh] |
|                                                                                |
| VÍ DỤ CHUẨN:                                                                  |
| "Phần mềm kế toán tinh gọn cho các nhà bán lẻ đa kênh (WHO) cần đối soát doanh |
| thu giữa sàn thương mại điện tử và cửa hàng vật lý (WHEN), hỗ trợ tích hợp hóa  |
| đơn điện tử tự động (WHAT)."                                                   |
+--------------------------------------------------------------------------------+
```

> [!CAUTION]
> **Anti-Pattern & Exclusion Hints:** 
> - KHÔNG nhồi nhét từ khóa: `"phần mềm kế toán, mua phần mềm rẻ, hóa đơn đỏ"`.
> - KHÔNG đưa điều kiện phân vùng/hạn chế vào hint: `"Chỉ hiện cho doanh nghiệp tại Hà Nội"` (Phải cài đặt tại mục **Location Settings** của chiến dịch).
> - **Exclusion Hints:** Sử dụng trường `exclusion_hints` (hỗ trợ qua API/Bulk, tối đa 2,000 hints) để chủ động loại trừ các ngữ cảnh không mong muốn (ví dụ: `"giày cao gót"`, `"giày trẻ em"`).

---

### Workflow 2: Chuẩn Bị Landing Page & Vượt Kiểm Duyệt Web Crawlers
Trước khi nộp chiến dịch, bắt buộc kiểm tra 3 chốt chặn hạ tầng:

1. **Cấu hình `robots.txt`**:
   ```txt
   User-agent: OAI-AdsBot
   Allow: /

   User-agent: OAI-SearchBot
   Allow: /
   ```
2. **WAF / Cloudflare Bypass**:
   - `OAI-AdsBot` đã được Cloudflare xác thực (Verified Bot). Cần đảm bảo các WAF Custom Rules không chặn User-Agent này.
   - Với các hệ thống yêu cầu dải IP tĩnh, đối chiếu danh sách chính thức tại:
     - `https://openai.com/adsbot.json`
     - `https://openai.com/searchbot.json`
3. **Tránh lỗi 429 (Rate Limit):** Khi tải lên số lượng lớn quảng cáo (Bulk Upload), chia nhỏ thành các batch để tránh crawler kích hoạt bộ hạn chế tần suất.

---

### Workflow 3: Cấu Trúc Chiến Dịch & Chiến Lược Đấu Giá

| Mục tiêu (Objective) | Phương thức tính phí | Cơ chế đấu giá | Ứng dụng tối ưu |
| :--- | :--- | :--- | :--- |
| **Views** | CPM (Mỗi 1,000 lượt xem) | Second-price Auction | Nhận diện thương hiệu diện rộng |
| **Clicks** | CPC (Mỗi click hợp lệ) | Khuyến nghị khởi điểm **$3 – $5 USD** | Kéo traffic chất lượng cao |
| **Conversions (Click)** | oCPC (Tính phí theo Clicks) | Bid Cap (Dự đoán xác suất chuyển đổi) | Tối ưu hành động sau khi click |
| **Conversions (Impression)**| CPM (Tính phí theo Views) | Tối ưu toàn phễu (View-through & Click-through) | Tối đa hóa tổng chuyển đổi |
| **Product Feed** | Maximize Results | Tự động điều chỉnh theo danh mục | Bán lẻ E-commerce nhiều SKU |

---

### Workflow 4: Quản Trị Product Feed Danh Mục Hàng Hóa
* **Giao thức khuyến nghị:** Sử dụng **Hosted HTTPS URL** hoặc **SFTP định kỳ**. Tuyệt đối không dùng CSV thủ công cho catalog lớn vì sản phẩm hết hạn sau **14 ngày**.
* **Cập nhật vi mô qua Delta Feeds API:** Khi giá hoặc tồn kho thay đổi, dùng `PATCH /v1/feeds/{id}/products` để cập nhật biến thể tức thì mà không cần nạp lại toàn bộ catalog.
* **Phân nhóm sản phẩm bằng `ads_metadata`:** Thêm các trường nhãn tùy biến (ví dụ `custom_label_0: "bestseller"`, `bidding_tier: "high_margin"`) vào feed để tạo bộ lọc Ad Group trong Ads Manager.

---

## 4. Bảng Kiểm Tra Trước Khi Launch (Pre-Launch Checklist)

- [ ] **Tài khoản & Phân vùng:** Quốc gia thuộc danh sách Ads Manager Beta Availability.
- [ ] **Crawler Allowlist:** `OAI-AdsBot` (kiểm duyệt trang) và `OAI-SearchBot` (tải ảnh/catalog) trả về HTTP 200 OK trên URL đích.
- [ ] **Context Hints:** Đã viết ít nhất 2–4 hints chuẩn ngữ nghĩa (What-Who-When) cho mỗi Ad Group. Không chứa từ khóa spam hay chỉ thị loại trừ.
- [ ] **Bidding Check:** Chiến dịch CPC đặt mức khởi điểm trong khoảng $3.00 – $5.00 USD/click.
- [ ] **Conversion Pixel:** JavaScript Pixel đã bật `Automatic Advanced Matching` và gửi sự kiện chuẩn (Purchase, Lead, Sign-up).
- [ ] **Tracking Click ID:** Landing page không được làm mất tham số `oppref` khi redirect; Pixel/CAPI dùng chung `event_id` để khử trùng.
- [ ] **Brand Safety Check:** Nội dung không vi phạm các ngành hàng nhạy cảm bị cấm (chính trị, nội dung y tế/khủng hoảng tâm lý cá nhân).

---

## 5. Tài Liệu Tham Khảo Mở Rộng (Reference Modules)

* **Mẫu Context Hints 5 ngành hàng:** [context_hints_templates.md](./references/context_hints_templates.md)
* **Quy chuẩn Bulk Upload CSV (3 Tabs):** [bulk_upload_schema.md](./references/bulk_upload_schema.md)
* **Đo lường `oppref`, Pixel & Conversions API:** [tracking_and_capi.md](./references/tracking_and_capi.md)
* **Cấu hình Crawlers (OAI-AdsBot) & Product Feeds:** [crawler_and_feed_specs.md](./references/crawler_and_feed_specs.md)
* **Đặc tả OpenAI Advertiser API & MCP:** [advertiser_api_specs.md](./references/advertiser_api_specs.md)
* **Đúc kết bài học & Pattern 8 hệ thống:** [learned_patterns.md](file:///home/dongocanh/.agents/learned_patterns.md#L519-L570)
