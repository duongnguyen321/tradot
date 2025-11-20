# Nghiên cứu Phase 2: Tích hợp LangChain/LangGraph và LLM cho Phân tích & Dự đoán

## 1. Vai trò của LLM (GPT-5) trong Hệ thống

LLM (cụ thể là GPT-5 theo yêu cầu) sẽ đóng vai trò là **Bộ não Phân tích và Ra quyết định (Analytical and Decision-Making Engine)** của hệ thống.

| Chức năng                                   | Mô tả                                                                                                  | Dữ liệu đầu vào                                                                           | Kết quả đầu ra                                                                                                      |
| :------------------------------------------ | :----------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------ |
| **Phân tích Cơ bản (Fundamental Analysis)** | Phân tích thông báo niêm yết, thông tin dự án, và tâm lý cộng đồng để đánh giá tiềm năng tăng trưởng.  | Dữ liệu niêm yết (tên, ký hiệu), dữ liệu On-chain, dữ liệu Sentiment (Twitter, Telegram). | Đánh giá rủi ro/lợi nhuận (Risk/Reward Score), Xác suất tăng/giảm (Probability), Đề xuất hành động (BUY/HOLD/SELL). |
| **Phân tích Kỹ thuật (Technical Analysis)** | Phân tích các chỉ báo kỹ thuật cơ bản (Volume, RSI, MACD, v.v.) dựa trên dữ liệu giá lịch sử.          | Dữ liệu giá/khối lượng (OHLCV) từ Binance API.                                            | Tóm tắt các tín hiệu kỹ thuật quan trọng.                                                                           |
| **Tối ưu hóa Hành động (Optimal Action)**   | Tổng hợp tất cả các phân tích (cơ bản và kỹ thuật) để đưa ra quyết định giao dịch cuối cùng và tối ưu. | Tất cả các kết quả phân tích trên.                                                        | Lệnh giao dịch chi tiết (Symbol, Side, Type, Quantity, Price/Stop-Loss/Take-Profit).                                |

## 2. Ứng dụng LangChain và LangGraph

Để triển khai một hệ thống AI phức tạp, cần sử dụng mô hình **Agentic Workflow** (Luồng công việc dựa trên Agent) với LangChain/LangGraph.

| Công cụ       | Vai trò                                                                                                         | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                               |
| :------------ | :-------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **LangChain** | Cung cấp các công cụ cơ bản (Chains, Prompts, Tools) để kết nối LLM với các nguồn dữ liệu và công cụ bên ngoài. | Sẽ được sử dụng để tạo các **Tools** (công cụ) cho LLM, ví dụ: `binance_api_tool` (để lấy giá), `sentiment_analysis_tool` (để lấy tâm lý thị trường).                                                                                                                                                                                                                                                                               |
| **LangGraph** | Xây dựng luồng công việc có trạng thái (Stateful Workflow) và khả năng lặp lại (Iterative/Recursive).           | Sẽ được sử dụng để thiết kế mô hình **Plan-and-Execute** và **Perfect Interrupt** theo yêu cầu: <br> 1. **Plan:** LLM lập kế hoạch phân tích. <br> 2. **Execute:** Thực thi các Tools (lấy dữ liệu). <br> 3. **Analyze & Decide:** LLM phân tích dữ liệu và đưa ra quyết định. <br> 4. **Interrupt:** Hệ thống có thể bị ngắt bởi một sự kiện thị trường (ví dụ: giá giảm mạnh) và quay lại bước **Analyze & Decide** ngay lập tức. |

## 3. Yêu cầu về Dữ liệu và Công cụ (Tools)

Để LLM hoạt động hiệu quả, cần cung cấp các công cụ (Tools) sau:

1.  **Binance Data Tool:** Lấy dữ liệu giá lịch sử (OHLCV) và thông tin tài khoản/vị thế hiện tại.
2.  **Sentiment Tool:** Lấy điểm tâm lý (sentiment score) từ các nguồn mạng xã hội.
3.  **On-chain Tool:** Lấy dữ liệu về ví cá voi, dòng tiền, và hợp đồng thông minh.
4.  **Technical Indicator Tool:** Tính toán các chỉ báo kỹ thuật (RSI, MACD, Bollinger Bands) và tóm tắt chúng.

---

_Tiếp theo, tôi sẽ chuyển sang Phase 3: Nghiên cứu các chiến lược giao dịch tự động và quản lý rủi ro._
