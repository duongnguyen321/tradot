# Nghiên cứu Phase 4: Kiến trúc Hệ thống và Lựa chọn Công nghệ

## 1. Lựa chọn Ngôn ngữ Lập trình: Python (Đề xuất)

Mặc dù người dùng ưu tiên NodeJS, nhưng với yêu cầu cốt lõi của dự án là tích hợp **AI/LLM (LangChain/LangGraph)** và **phân tích dữ liệu**, **Python** là lựa chọn tối ưu và được đề xuất.

| Tiêu chí                 | Python                                                                                                           | NodeJS                                                                                               | Lý do Lựa chọn Python                                                                            |
| :----------------------- | :--------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| **Hệ sinh thái AI/LLM**  | **Rất mạnh.** LangChain, LangGraph, OpenAI SDK, Pandas, NumPy, Scikit-learn đều là các thư viện Python hàng đầu. | **Yếu hơn.** LangChain.js tồn tại nhưng kém trưởng thành và ít tài liệu hơn so với phiên bản Python. | **Yêu cầu cốt lõi** là LangChain/LangGraph. Python cung cấp sự ổn định và tính năng đầy đủ nhất. |
| **Giao dịch Tốc độ cao** | **Tốt.** Có thể sử dụng các thư viện `asyncio` để xử lý các tác vụ I/O bất đồng bộ (như Polling API).            | **Rất tốt.** Bản chất là bất đồng bộ (non-blocking I/O), lý tưởng cho các tác vụ I/O như gọi API.    | Sự khác biệt không đáng kể. Python có thể đáp ứng yêu cầu tốc độ cao.                            |
| **Thư viện Binance API** | **Tuyệt vời.** `python-binance` là thư viện phổ biến và được duy trì tốt.                                        | **Tốt.** Nhiều thư viện cộng đồng chất lượng.                                                        | Cả hai đều có thư viện tốt.                                                                      |
| **Phân tích Dữ liệu**    | **Xuất sắc.** Pandas là tiêu chuẩn công nghiệp cho việc xử lý và phân tích dữ liệu.                              | **Trung bình.** Cần sử dụng các thư viện bên ngoài và không mạnh mẽ bằng Pandas.                     | Cần thiết cho việc tính toán các chỉ báo kỹ thuật (RSI, MACD).                                   |

**Kết luận:** Để đảm bảo tính khả thi và hiệu suất của phần **AI Agent** phức tạp, **Python** là lựa chọn công nghệ tốt nhất.

## 2. Kiến trúc Hệ thống Đề xuất

Hệ thống sẽ được thiết kế theo kiến trúc **Microservices** hoặc **Modular Monolith** để tách biệt các chức năng chính, đảm bảo tính ổn định và khả năng mở rộng.

| Thành phần                             | Công nghệ Chính                | Chức năng                                                                                                                                                                                                                                                      |
| :------------------------------------- | :----------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Data Collector (Thu thập Dữ liệu)**  | Python (với `asyncio`)         | 1. **Binance Polling:** Định kỳ gọi `/api/v3/exchangeInfo` để phát hiện coin mới. 2. **Websocket:** Duy trì kết nối Websocket với Binance để nhận dữ liệu giá theo thời gian thực (real-time price). 3. **External API:** Thu thập dữ liệu Sentiment/On-chain. |
| **AI Agent (Bộ não Quyết định)**       | Python (LangGraph, OpenAI SDK) | 1. **Phân tích:** Nhận dữ liệu từ Data Collector. 2. **Quyết định:** Sử dụng mô hình **Plan-and-Execute** để đưa ra lệnh giao dịch tối ưu (BUY/SELL/HOLD). 3. **Interrupt Handling:** Xử lý các tín hiệu ngắt khẩn cấp (Perfect Interrupt).                    |
| **Trading Engine (Công cụ Giao dịch)** | Python (python-binance)        | 1. **Thực thi Lệnh:** Gửi lệnh giao dịch (Market, Limit, OCO) đến Binance API. 2. **Quản lý Vị thế:** Theo dõi các vị thế đang mở, chốt lời/cắt lỗ tự động.                                                                                                    |
| **Database (Cơ sở Dữ liệu)**           | PostgreSQL/SQLite              | Lưu trữ lịch sử giao dịch, dữ liệu phân tích của LLM, và cấu hình hệ thống.                                                                                                                                                                                    |

## 3. Triển khai Cơ chế Plan-and-Execute và Perfect Interrupt

| Cơ chế                | Công cụ                | Mô tả                                                                                                                                                                                                                                                                                                                                                               |
| :-------------------- | :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Plan-and-Execute**  | LangGraph              | **Luồng công việc:** <br> 1. **Plan:** LLM xác định thông tin cần thiết (giá, sentiment, on-chain). <br> 2. **Execute:** Agent gọi các **Tools** (Data Collector) để lấy dữ liệu. <br> 3. **Analyze:** LLM phân tích dữ liệu và đưa ra quyết định. <br> 4. **Action:** Trading Engine thực thi lệnh.                                                                |
| **Perfect Interrupt** | Websocket/Event-Driven | **Xử lý sự kiện:** <br> 1. Data Collector nhận một sự kiện quan trọng (ví dụ: giá giảm 5% trong 1 phút, hoặc thông báo niêm yết mới). <br> 2. Gửi tín hiệu ngắt (Interrupt Signal) đến AI Agent. <br> 3. AI Agent ngay lập tức dừng luồng hiện tại và chuyển sang chế độ **Khẩn cấp (Emergency Mode)** để đánh giá lại vị thế và thực hiện lệnh bán/cắt lỗ nếu cần. |

---

_Tiếp theo, tôi sẽ chuyển sang Phase 5: Tạo tài liệu hướng dẫn chi tiết với các bước thực hiện và tiêu chí hoàn thành._
