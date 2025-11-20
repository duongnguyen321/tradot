# Nghiên cứu Phase 1: Phát hiện Coin mới và Meme Coin Trending

## 1. Phát hiện Coin mới niêm yết trên Binance

**Phương pháp chính:** Sử dụng Binance REST API.

| Endpoint               | Phương thức | Mô tả                                                                                                                    | Chiến lược                                                                                                                                                                                                                         |
| :--------------------- | :---------- | :----------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/api/v3/exchangeInfo` | `GET`       | Trả về thông tin về các cặp giao dịch (symbols) hiện có trên sàn giao dịch Spot.                                         | **Polling:** Định kỳ (ví dụ: mỗi 1-5 giây) gọi endpoint này. So sánh danh sách `symbols` hiện tại với danh sách đã lưu trữ trước đó. Bất kỳ `symbol` mới nào xuất hiện trong danh sách hiện tại đều là một coin mới được niêm yết. |
| **Tốc độ:**            |             | Phương pháp này được cho là đủ nhanh để phát hiện niêm yết mới trong vòng dưới 1 giây, cho phép thực hiện giao dịch sớm. |                                                                                                                                                                                                                                    |

## 2. Phát hiện Meme Coin đang thịnh hành (Trending Meme Coins)

Việc này đòi hỏi sử dụng các nguồn dữ liệu bên ngoài Binance API, chủ yếu là dữ liệu về **tâm lý thị trường (sentiment)** và **hoạt động on-chain**.

| Nguồn Dữ liệu                             | Mục tiêu                                                                                | Công cụ/Chiến lược                                                                                                                                               |
| :---------------------------------------- | :-------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Dữ liệu On-chain**                      | Phát hiện các giao dịch lớn, ví cá voi, và các hợp đồng thông minh mới được triển khai. | Cần tích hợp với các dịch vụ cung cấp dữ liệu on-chain (ví dụ: Etherscan API, Solscan API, hoặc các dịch vụ tổng hợp như Bubblemaps).                            |
| **Tâm lý Mạng xã hội (Social Sentiment)** | Đo lường mức độ quan tâm và cảm xúc của cộng đồng (Twitter, Telegram, Discord).         | Cần sử dụng các API phân tích tâm lý chuyên biệt (ví dụ: LunarCrush, The Tie, Stockgeist.ai) hoặc tự xây dựng bộ thu thập dữ liệu và phân tích sentiment cơ bản. |
| **Sàn giao dịch phi tập trung (DEX)**     | Phát hiện các coin mới được giao dịch trên DEX trước khi lên sàn CEX (như Binance).     | Theo dõi các cặp giao dịch có khối lượng lớn trên các DEX (ví dụ: Uniswap, PancakeSwap) thông qua các API của chúng.                                             |

**Kết luận Phase 1:**

- **Coin mới niêm yết:** Có thể thực hiện hiệu quả bằng cách **Polling** endpoint `/api/v3/exchangeInfo` của Binance API.
- **Meme Coin Trending:** Cần tích hợp thêm các API/dịch vụ bên ngoài để thu thập dữ liệu on-chain và sentiment. Đây là nguồn dữ liệu đầu vào quan trọng cho mô hình AI (LLM) ở Phase 2.

---

_Tiếp theo, tôi sẽ chuyển sang Phase 2: Nghiên cứu tích hợp LangChain/LangGraph với LLM để phân tích và dự đoán._
