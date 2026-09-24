# Đặc Tả Cấu Trúc Bulk Upload Schema: ChatGPT Ads (OpenAI Ads Manager Beta)

Tài liệu này hướng dẫn chi tiết quy chuẩn tạo file bảng tính (.CSV / Excel) để tải lên hàng loạt hàng nghìn chiến dịch, nhóm quảng cáo và mẫu quảng cáo vào Ads Manager Beta.

---

## 1. Cấu Trúc File 3 Tabs Bắt Buộc

File Bulk Upload chuẩn bao gồm đúng 3 tab được đặt tên chính xác:
1. `campaigns` (Cấp Chiến dịch)
2. `ad groups` (Cấp Nhóm quảng cáo)
3. `ads` (Cấp Mẫu quảng cáo)

---

## 2. Quy Chuẩn Tab 1: `campaigns`

| Tên Cột (Header) | Kiểu Dữ Liệu | Ràng Buộc & Định Dạng | Ví Dụ |
| :--- | :--- | :--- | :--- |
| `campaign_id` | String | Để trống nếu tạo mới; điền ID nếu cập nhật | *để trống* hoặc `cmp_01j7...` |
| `campaign_name` | String | Duy nhất, không trùng lặp | `US_SearchIntent_Sneakers_2026` |
| `objective` | String | Bắt buộc là `Views` hoặc `Clicks` | `Clicks` |
| `budget_type` | String | `daily` hoặc `lifetime` | `daily` |
| `budget_amount` | Number | Số dương, đơn vị USD | `150.00` |
| `start_date` | String | Định dạng `YYYY-MM-DD` | `2026-10-01` |
| `end_date` | String | Định dạng `YYYY-MM-DD` (tùy chọn) | `2026-12-31` |
| `countries` | JSON Array | Mảng mã quốc gia định dạng JSON chuẩn | `["US"]` |

*Giới hạn: Tối đa 5,000 campaigns trong một file.*

---

## 3. Quy Chuẩn Tab 2: `ad groups`

| Tên Cột (Header) | Kiểu Dữ Liệu | Ràng Buộc & Định Dạng | Ví Dụ |
| :--- | :--- | :--- | :--- |
| `ad_group_id` | String | Để trống nếu tạo mới; điền ID nếu cập nhật | *để trống* |
| `campaign_name` | String | Khớp chính xác 100% với tên ở tab `campaigns` | `US_SearchIntent_Sneakers_2026` |
| `ad_group_name` | String | Duy nhất trong tài khoản | `AG_Beginner_5K_Cushioned` |
| `bid_amount` | Number | Mức thầu tối đa CPC hoặc CPM (USD) | `3.50` |
| `context_hints` | JSON Array | **BẮT BUỘC dạng chuỗi JSON Array**. Không merge khi cập nhật mà ghi đè toàn bộ! | `["Giày chạy bộ êm ái hàng ngày cho người mới tập 5K", "Giày thể thao đệm foam hỗ trợ người đứng lâu"]` |

*Giới hạn: Tối đa 5,000 ad groups trong một file.*

---

## 4. Quy Chuẩn Tab 3: `ads`

| Tên Cột (Header) | Kiểu Dữ Liệu | Ràng Buộc Ký Tự | Chi Tiết Kỹ Thuật |
| :--- | :--- | :--- | :--- |
| `ad_id` | String | Để trống nếu tạo mới | *để trống* |
| `ad_group_name` | String | Khớp chính xác với tên ở tab `ad groups` | `AG_Beginner_5K_Cushioned` |
| `ad_name` | String | Tên quản trị mẫu quảng cáo | `Creative_FoamRunner_v1` |
| `title` | String | **Tối ưu: 16–24 ký tự**. Tối đa: **50 ký tự** | `Giày Chạy Bộ Êm Ái 5K` |
| `copy` | String | **Tối ưu: 32–48 ký tự**. Tối đa: **100 ký tự** | `Đệm bọt khí trợ lực phục hồi, bảo vệ khớp gối.` |
| `landing_page_url` | URL | URL công khai trực tiếp, cho phép `OAI-AdsBot` crawl | `https://brand.com/shoes/cushion-5k` |
| `image_url` | URL | Tối đa 1200x1200px, tỷ lệ 1:1, ảnh trực tiếp | `https://cdn.brand.com/images/cushion-5k-sq.jpg` |

---

## 5. Danh Sách Lỗi Kỹ Thuật Gây Từ Chối File (Validation Pitfalls)

1. **Sai định dạng JSON ở trường `context_hints` hoặc `countries`:**
   * *Sai:* `hint1, hint2` hoặc `'["US"]'`
   * *Đúng:* `["hint1", "hint2"]` và `["US"]`
2. **Hình ảnh không mở trực tiếp:**
   * Đường dẫn hình ảnh yêu cầu đăng nhập, dẫn tới trang xem trước HTML (Drive Preview thay vì Direct Link) hoặc bị WAF chặn sẽ khiến file tải lên báo lỗi ngay lập tức.
3. **Ô bị merge (Merged Cells):**
   * Không được gộp bất kỳ ô nào trong file bảng tính.
4. **Vượt quá giới hạn ký tự Title/Copy:**
   * Title > 50 ký tự hoặc Copy > 100 ký tự sẽ bị hệ thống cắt ngắn hoặc từ chối import.
