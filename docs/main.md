# Kế hoạch Chi tiết Dự án Ứng dụng Giao dịch Tiền điện tử Tự động (AI-Driven Crypto Trading Bot)

## I. Tổng quan và Hướng đi Chiến lược

Dự án này nhằm xây dựng một hệ thống giao dịch tự động thông minh, kết hợp tốc độ phản ứng của bot giao dịch truyền thống với khả năng phân tích đa chiều của mô hình Ngôn ngữ Lớn (LLM).

**Mục tiêu chính:**

1.  **Phát hiện sớm:** Tự động phát hiện các đồng coin mới niêm yết trên Binance và các meme coin đang có xu hướng mạnh mẽ (trending).
2.  **Phân tích thông minh:** Sử dụng LLM (GPT-5) thông qua LangChain/LangGraph để phân tích dữ liệu thị trường, tâm lý, và on-chain để đưa ra quyết định mua/bán tối ưu.
3.  **Thực thi chính xác:** Thực hiện các lệnh giao dịch tốc độ cao (Sniper Buy) và quản lý vị thế thông minh (Smart Auto-Sell, OCO).

## II. Kiến trúc Hệ thống Đề xuất

Hệ thống được chia thành ba thành phần chính hoạt động độc lập và giao tiếp với nhau, lý tưởng để triển khai bằng Python.

| Thành phần               | Công nghệ                                    | Chức năng Chính                                                                                                                                            |
| :----------------------- | :------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Data Collector**    | Python (Asyncio, Binance API, External APIs) | Thu thập dữ liệu theo thời gian thực: Phát hiện coin mới (Polling `exchangeInfo`), giá (Websocket), Sentiment (API bên ngoài), On-chain (API bên ngoài).   |
| **2. AI Agent (Bộ não)** | Python (LangGraph, OpenAI SDK)               | Nhận dữ liệu từ Collector, thực hiện quy trình **Plan-and-Execute** để phân tích, đưa ra quyết định giao dịch, và xử lý các sự kiện **Perfect Interrupt**. |
| **3. Trading Engine**    | Python (python-binance)                      | Nhận lệnh từ AI Agent, thực thi lệnh giao dịch (Market, Limit, OCO) trên Binance, và quản lý các vị thế đang mở (Stop-Loss, Take-Profit).                  |

## III. Kế hoạch Thực hiện Chi tiết (6 Giai đoạn)

Dự án sẽ được chia thành 6 giai đoạn phát triển chính, mỗi giai đoạn có các bước cụ thể và tiêu chí hoàn thành rõ ràng.

### Giai đoạn 1: Thiết lập Cơ sở, Cấu hình và Thu thập Dữ liệu Binance

| Bước    | Mô tả Công việc                                                                                                                                                                    | Tiêu chí Hoàn thành                                                                                            |
| :------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| **1.1** | Thiết lập môi trường phát triển Python, cài đặt các thư viện cần thiết (`python-binance`, `langchain`, `langgraph`, `pandas`, `python-dotenv`, thư viện Telegram, thư viện Email). | Cài đặt thành công tất cả các thư viện, tạo cấu trúc thư mục dự án cơ bản.                                     |
| **1.2** | Triển khai mô-đun đọc cấu hình từ file `.env` và tải vào biến môi trường.                                                                                                          | Hệ thống có thể đọc thành công tất cả các thông số cấu hình (Binance Keys, Telegram Token, Email Credentials). |
| **1.3** | Xây dựng cơ chế **Binance API Polling** để phát hiện coin mới niêm yết (sử dụng `GET /api/v3/exchangeInfo`).                                                                       | Hệ thống có thể so sánh danh sách symbols và in ra thông báo ngay lập tức khi một symbol mới xuất hiện.        |
| **1.4** | Xây dựng cơ chế **Websocket Data Stream** để nhận dữ liệu giá theo thời gian thực (real-time price) cho các cặp coin quan tâm.                                                     | Kết nối Websocket ổn định, có thể nhận và lưu trữ dữ liệu giá (ví dụ: 1 giây/lần) vào bộ nhớ đệm.              |

### Giai đoạn 2: Tích hợp AI Agent và Tools

| Bước    | Mô tả Công việc                                                                                                                              | Tiêu chí Hoàn thành                                                                                                                       |
| :------ | :------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| **2.1** | Định nghĩa các **Tools** cho LangChain/LangGraph: `get_binance_price(symbol)`, `get_sentiment_score(symbol)`, `execute_trade(order_params)`. | Các hàm Tools được định nghĩa rõ ràng, có thể gọi và trả về kết quả giả lập (mock data) thành công.                                       |
| **2.2** | Xây dựng **AI Agent** cơ bản sử dụng LangGraph và OpenAI SDK (GPT-5).                                                                        | Agent có thể nhận một yêu cầu (ví dụ: "Phân tích BTCUSDT"), sử dụng Tool `get_binance_price`, và đưa ra một câu trả lời dựa trên dữ liệu. |
| **2.3** | Triển khai mô hình **Plan-and-Execute** cơ bản trong LangGraph.                                                                              | Agent có thể tự động lên kế hoạch, gọi Tool, và thực hiện phân tích theo luồng công việc đã định.                                         |

### Giai đoạn 3: Tích hợp Dữ liệu Meme Coin và Phân tích Nâng cao

| Bước    | Mô tả Công việc                                                                                                                                                                       | Tiêu chí Hoàn thành                                                                                                      |
| :------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------- |
| **3.1** | Nghiên cứu và tích hợp API của bên thứ ba (ví dụ: LunarCrush, The Tie, hoặc một API on-chain) để thu thập dữ liệu **Sentiment** và **On-chain**.                                      | Data Collector có thể lấy được điểm Sentiment (ví dụ: từ -1 đến 1) và dữ liệu giao dịch cá voi (whale transaction data). |
| **3.2** | Xây dựng **Technical Indicator Tool** để tính toán các chỉ báo (RSI, MACD, Volume Profile) từ dữ liệu giá Binance.                                                                    | Tool có thể nhận dữ liệu OHLCV và trả về một JSON chứa các chỉ báo kỹ thuật chính.                                       |
| **3.3** | Tinh chỉnh Prompt cho LLM (GPT-5) để thực hiện **Phân tích Đa chiều** (kết hợp dữ liệu giá, kỹ thuật, sentiment, on-chain) và đưa ra **Xác suất Tăng/Giảm** (ví dụ: 75% cơ hội tăng). | LLM trả về một đối tượng JSON chuẩn hóa chứa `action` (BUY/SELL/HOLD), `probability`, và `target_price`.                 |

### Giai đoạn 4: Triển khai Cơ chế Giao dịch Thông minh

| Bước    | Mô tả Công việc                                                                     | Tiêu chí Hoàn thành                                                                                                                                                           |
| :------ | :---------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **4.1** | Xây dựng chức năng **Sniper Buy** (Mua ngay lập tức) cho coin mới niêm yết.         | Bot có thể phát hiện coin mới và gửi lệnh Market Buy thành công trong vòng dưới 1 giây.                                                                                       |
| **4.2** | Triển khai cơ chế **Smart Auto-Sell** sử dụng lệnh **OCO (One-Cancels-the-Other)**. | Sau khi mua, Trading Engine tự động đặt một lệnh OCO bao gồm **Take-Profit Limit Order** và **Stop-Loss Limit Order** dựa trên thông số từ AI Agent.                          |
| **4.3** | Xây dựng cơ chế **Perfect Interrupt** (Ngắt hoàn hảo).                              | Khi một sự kiện khẩn cấp (ví dụ: giá giảm đột ngột) xảy ra, AI Agent được kích hoạt lại để đánh giá lại vị thế và có thể thực hiện lệnh bán khẩn cấp (Market Sell) để cắt lỗ. |

### Giai đoạn 5: Quản lý Rủi ro và Backtesting

| Bước    | Mô tả Công việc                                                       | Tiêu chí Hoàn thành                                                                                                                           |
| :------ | :-------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------- |
| **5.1** | Triển khai chức năng **Quản lý Vốn (Position Sizing)**.               | Hệ thống giới hạn số vốn tối đa cho mỗi giao dịch (ví dụ: 1% tổng vốn) và đảm bảo không vượt quá giới hạn này.                                |
| **5.2** | Xây dựng mô-đun **Backtesting** (kiểm thử trên dữ liệu lịch sử).      | Hệ thống có thể chạy mô phỏng toàn bộ chiến lược (từ phát hiện đến quyết định của AI và thực thi) trên dữ liệu lịch sử để đánh giá hiệu suất. |
| **5.3** | Triển khai **Paper Trading** (Giao dịch trên tài khoản Demo/Testnet). | Bot hoạt động ổn định trên Binance Testnet, thực hiện các lệnh mua/bán mà không sử dụng tiền thật.                                            |

### Giai đoạn 6: Hoàn thiện và Triển khai

| Bước    | Mô tả Công việc                                                                           | Tiêu chí Hoàn thành                                                                                                                                          |
| :------ | :---------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **6.1** | Tối ưu hóa hiệu suất và tốc độ của Data Collector và Trading Engine.                      | Giảm thiểu độ trễ (latency) của Sniper Buy xuống mức tối đa có thể (ví dụ: dưới 500ms).                                                                      |
| **6.2** | Hoàn thiện hệ thống thông báo qua **Telegram** và **Email**.                              | Hệ thống có thể gửi thông báo chi tiết qua Telegram và thông báo giản lược qua Email khi có sự kiện quan trọng (ví dụ: mua/bán thành công, cảnh báo rủi ro). |
| **6.3** | Hoàn thiện giao diện người dùng (nếu có) để theo dõi hoạt động.                           | Người dùng có thể theo dõi các giao dịch, lợi nhuận/thua lỗ, và quyết định của AI Agent theo thời gian thực.                                                 |
| **6.4** | Chuyển đổi từ Paper Trading sang **Giao dịch Bằng Tiền Thật** (Live Trading) với vốn nhỏ. | Bot hoạt động ổn định trên tài khoản Live Trading trong 7 ngày liên tiếp mà không gặp lỗi nghiêm trọng.                                                      |

---

## IV. Quản lý Cấu hình An toàn

Để đảm bảo an toàn cho các thông tin nhạy cảm như khóa API và mật khẩu, hệ thống sẽ sử dụng **Biến Môi trường (Environment Variables)**, được tải từ một file `.env` cục bộ.

| Thông số                   | Mục đích                            | Phương pháp Quản lý                                       |
| :------------------------- | :---------------------------------- | :-------------------------------------------------------- |
| Binance API Key/Secret     | Giao dịch và truy cập dữ liệu       | Biến môi trường (ví dụ: `BINANCE_API_KEY`)                |
| Telegram Bot Token/Chat ID | Gửi cảnh báo và thông báo liên tục  | Biến môi trường (ví dụ: `TELEGRAM_BOT_TOKEN`)             |
| Email Credentials (SMTP)   | Gửi thông báo ngắn gọn, giản lược   | Biến môi trường (ví dụ: `MAIL_USERNAME`, `MAIL_PASSWORD`) |
| OpenAI API Key             | Cung cấp năng lượng cho LLM (GPT-5) | Biến môi trường (ví dụ: `OPENAI_API_KEY`)                 |

**Lưu ý An toàn:** File `.env` chứa các khóa bí mật **phải** được thêm vào `.gitignore` và **không bao giờ** được chia sẻ công khai.

## V. Tóm tắt Công nghệ và Yêu cầu

| Yêu cầu          | Công nghệ/Thư viện         | Ghi chú                                                               |
| :--------------- | :------------------------- | :-------------------------------------------------------------------- |
| **Ngôn ngữ**     | Python 3.11+               | Đề xuất mạnh mẽ.                                                      |
| **LLM**          | OpenAI API (GPT-5)         | Cần có **OpenAI API Key** và quyền truy cập vào mô hình GPT-5.        |
| **AI Framework** | LangChain, LangGraph       | Cần thiết để xây dựng luồng công việc Agentic.                        |
| **Giao dịch**    | `python-binance`           | Thư viện Binance API chính thức/phổ biến.                             |
| **Dữ liệu**      | Pandas, NumPy              | Dùng cho xử lý dữ liệu và tính toán chỉ báo kỹ thuật.                 |
| **API Key**      | Binance API Key (Spot)     | Cần quyền đọc (Read) và giao dịch (Enable Trading) để thực hiện lệnh. |
| **Thông báo**    | Telegram API, SMTP (Email) | Cần **Telegram Bot Token** và **Email Credentials** để gửi thông báo. |
