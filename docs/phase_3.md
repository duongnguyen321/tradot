# Nghiên cứu Phase 3: Chiến lược Giao dịch Tự động và Quản lý Rủi ro

## 1. Cơ chế Auto-Buy (Mua tự động)

Mục tiêu chính của cơ chế này là **tốc độ** và **chính xác** để tận dụng lợi thế của việc niêm yết coin mới (thường có đợt tăng giá mạnh ban đầu).

| Loại Coin                       | Chiến lược Mua                           | Cơ chế Thực hiện                                                                                                                                                                                                                                    |
| :------------------------------ | :--------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Coin mới niêm yết (Binance)** | **Mua ngay lập tức (Sniper Buy)**        | Sử dụng `exchangeInfo` Polling (Phase 1) để phát hiện coin mới. Ngay lập tức gửi lệnh **Market Order** hoặc **Limit Order** với giá cao hơn giá thị trường một chút (để đảm bảo khớp lệnh nhanh) thông qua Binance Spot API (`POST /api/v3/order`). |
| **Meme Coin Trending**          | **Mua theo tín hiệu AI (AI-Driven Buy)** | Sau khi LLM (Phase 2) phân tích dữ liệu sentiment/on-chain và đưa ra quyết định **BUY** với xác suất cao, hệ thống sẽ thực hiện lệnh **Limit Order** hoặc **Market Order** theo các thông số tối ưu do LLM đề xuất.                                 |

## 2. Cơ chế Smart Auto-Sell (Bán tự động thông minh)

Đây là phần quan trọng nhất để **chốt lời** và **cắt lỗ** một cách hiệu quả, đặc biệt trong môi trường biến động cao của coin mới và meme coin.

| Cơ chế                                             | Mô tả                                                                                                                                                                                                       | Lệnh Binance API                                                                                |
| :------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------- |
| **Chốt lời theo Xác suất (AI-Driven Take Profit)** | LLM sẽ tính toán **mục tiêu giá (Target Price)** dựa trên phân tích. Hệ thống sẽ đặt lệnh **Take-Profit Limit Order** ngay sau khi mua.                                                                     | `POST /api/v3/order` với `type=TAKE_PROFIT_LIMIT` hoặc kết hợp **OCO (One-Cancels-the-Other)**. |
| **Cắt lỗ thông minh (Smart Stop-Loss)**            | Đặt lệnh **Stop-Loss** ngay sau khi mua để giới hạn rủi ro. LLM có thể điều chỉnh mức Stop-Loss (Trailing Stop-Loss) dựa trên diễn biến thị trường.                                                         | `POST /api/v3/order` với `type=STOP_LOSS_LIMIT` hoặc **OCO**.                                   |
| **Bán theo Tín hiệu AI (AI-Driven Sell)**          | Nếu LLM nhận thấy tâm lý thị trường thay đổi tiêu cực hoặc các chỉ báo kỹ thuật xấu đi, nó sẽ đưa ra quyết định **SELL**. Hệ thống sẽ hủy các lệnh chờ và thực hiện lệnh **Market Order** bán ngay lập tức. | `DELETE /api/v3/order` (hủy lệnh cũ) và `POST /api/v3/order` (lệnh Market Sell).                |

## 3. Quản lý Rủi ro (Risk Management)

| Yếu tố Rủi ro                        | Biện pháp Giảm thiểu                                                                                                                                                                                     |
| :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Biến động cao (Volatility)**       | **Phân bổ vốn (Position Sizing):** Chỉ sử dụng một phần nhỏ tổng vốn cho mỗi giao dịch (ví dụ: < 1-2% vốn).                                                                                              |
| **Thanh khoản thấp (Low Liquidity)** | **Sử dụng Limit Order:** Tránh trượt giá (slippage) quá lớn bằng cách sử dụng lệnh Limit, đặc biệt với meme coin mới. Tuy nhiên, đối với Sniper Buy, Market Order có thể cần thiết để đảm bảo khớp lệnh. |
| **Lỗi hệ thống/API**                 | **Perfect Interrupt (LangGraph):** Đảm bảo hệ thống có thể phản ứng ngay lập tức với các sự kiện bất ngờ (giá sập, lỗi API) và chuyển sang trạng thái an toàn (ví dụ: bán khẩn cấp).                     |
| **Rủi ro mô hình AI**                | **Backtesting và Paper Trading:** Luôn kiểm tra mô hình LLM trên dữ liệu lịch sử và chạy thử nghiệm trên tài khoản demo trước khi giao dịch bằng tiền thật.                                              |

---

_Tiếp theo, tôi sẽ chuyển sang Phase 4: Xây dựng kiến trúc hệ thống và lựa chọn công nghệ (NodeJS vs Python)._
