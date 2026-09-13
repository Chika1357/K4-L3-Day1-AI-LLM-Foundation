# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Ở temperature thấp (0.0 – 0.5), mô hình mang tính tất định cao (deterministic), luôn tập trung vào các sự thật phổ biến nhất (như hang Sơn Đoòng lớn nhất thế giới) với cấu trúc câu nhất quán và lặp lại nếu gọi nhiều lần. Khi temperature tăng lên (1.0 – 1.5), không gian phân phối xác suất mở rộng, mô hình khám phá các token đa dạng hơn, chuyển hướng sang các chủ đề bất ngờ (như xuất khẩu cà phê Robusta, ẩm thực cà phê trứng) nhưng cách diễn đạt bắt đầu tản mạn hơn và giảm tính cô đọng.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature trong khoảng 0.0 đến 0.3 (tối ưu nhất là 0.2). Chatbot hỗ trợ khách hàng cần ưu tiên tính chính xác, nhất quán và độ tin cậy của thông tin (chính sách bảo hành, hoàn tiền, hướng dẫn kỹ thuật); cùng một câu hỏi cần nhận được câu trả lời đồng nhất. Mức nhiệt độ thấp giúp kiểm soát tối đa hiện tượng ảo giác (hallucination) và hạn chế rủi ro cung cấp thông tin sai lệch cho khách hàng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> - **Ước tính chi phí:**
>   + Tổng token đầu ra mỗi ngày = 10.000 users × 3 lượt × 350 tokens = 10.500.000 tokens (10.500K tokens).
>   + Chi phí với GPT-4o ($0.010 / 1K output tokens) = 10.500 × $0.010 = $105.00 / ngày (~$3.150 / tháng).
>   + Chi phí với GPT-4o-mini ($0.0006 / 1K output tokens) = 10.500 × $0.0006 = $6.30 / ngày (~$189 / tháng).
>   + GPT-4o đắt hơn GPT-4o-mini: 105.00 / 6.30 = 16.67 lần (tiết kiệm 94% ngân sách nếu dùng mini).
> - **Trường hợp GPT-4o xứng đáng:** Các tác vụ đòi hỏi suy luận logic phức tạp, giải quyết bài toán đa bước, phân tích điều khoản hợp đồng pháp lý hoặc hỗ trợ chẩn đoán y khoa chuyên sâu — nơi sai sót nhỏ gây tổn thất lớn hơn nhiều so với chi phí API.
> - **Trường hợp nên dùng GPT-4o-mini:** Các tác vụ phân loại ý định người dùng (intent classification), tóm tắt tin nhắn ngắn, trích xuất thực thể (NER), hoặc dịch thuật thông thường với khối lượng truy vấn lớn của ứng dụng tiêu dùng.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với persona "giáo viên tiểu học", câu trả lời ngắn gọn, sử dụng ngôn từ đời thường và hình ảnh ẩn dụ trực quan (ví dụ: cuốn sổ ghi điểm chung của cả lớp mà ai cũng giữ một bản sao, không ai được tự ý sửa). Ngược lại, persona "chuyên gia tài chính" sử dụng hệ thống thuật ngữ chuyên sâu (sổ cái phân tán DLT, cơ chế đồng thuận PoW/PoS, hàm băm mật mã cryptographic hash, tính bất biến immutability), ngữ điệu trang trọng và phân tích theo góc độ quản trị rủi ro thanh khoản. System prompt đóng vai trò như một bộ lọc ngữ cảnh (conditioning context), định hình trực tiếp không gian phân phối xác suất của từ vựng, độ sâu tri thức và phương pháp lập luận của mô hình trước khi tiếp nhận câu hỏi của người dùng.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> - **Thử nghiệm so sánh:** Với đoạn văn tiếng Việt chuẩn 100 từ (khoảng 550 ký tự), công thức ước lượng `số từ / 0.75` cho ra xấp xỉ 133 tokens. Tuy nhiên, khi đếm bằng `tiktoken` (bộ mã hóa `o200k_base` của GPT-4o), kết quả thực tế đạt khoảng 175 – 185 tokens, chênh lệch thực tế từ 31% đến 39% so với ước lượng thô.
> - **Nguyên nhân tiếng Việt tốn nhiều token hơn:** Thuật toán Byte-Pair Encoding (BPE) của OpenAI được huấn luyện chủ yếu trên tập dữ liệu tiếng Anh áp đảo, nơi các từ hoặc gốc từ phổ biến được gộp nguyên vẹn thành 1 token. Tiếng Việt là ngôn ngữ có dấu thanh và các ký tự đặc biệt (ă, â, đ, ê, ô, ơ, ư); khi mã hóa sang UTF-8, mỗi ký tự có dấu chiếm 2 đến 3 bytes khiến tokenizer phải bẻ nhỏ một âm tiết tiếng Việt thành nhiều sub-tokens, làm tăng đáng kể tổng lượng token so với văn bản tiếng Anh có cùng số từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming đóng vai trò tối quan trọng trong các ứng dụng tương tác trực tiếp với người dùng cuối như chatbot, trợ lý ảo hoặc công cụ hỗ trợ viết code (AI coding assistant), vì nó giảm thiểu tối đa chỉ số Time-to-First-Token (TTFT) — người dùng thấy chữ xuất hiện ngay sau vài trăm mili-giây thay vì phải nhìn màn hình loading trống rỗng trong nhiều giây, tạo cảm giác hệ thống phản hồi tức thì và sống động. Ngược lại, non-streaming lại phù hợp hơn trong các đường ống xử lý tự động ngầm (backend data pipelines, batch processing), các tác vụ yêu cầu trích xuất cấu trúc dữ liệu nghiêm ngặt (như xuất định dạng JSON/XML hoặc Tool/Function Calling) cần chờ toàn bộ payload hoàn tất để parse và validate dữ liệu trước khi chuyển sang bước tiếp theo.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> So với delay cố định, exponential backoff (thời gian chờ tăng theo cấp số nhân: 0.1s, 0.2s, 0.4s, 0.8s...) vừa giúp ứng dụng nhanh chóng thử lại ở các lỗi kết nối tức thời (transient hiccups), vừa tự động giãn dần tần suất gọi nếu máy chủ gặp sự cố kéo dài, tạo khoảng thời gian cần thiết cho server hạ nhiệt và hồi phục tài nguyên. Nếu hàng nghìn client cùng dùng một khoảng delay cố định (ví dụ 1 giây), hiện tượng "Thundering Herd Problem" (hiệu ứng đàn trâu dẫm đạp) sẽ xảy ra: toàn bộ lượng request thất bại sẽ đồng loạt dội ngược lại server tại cùng một thời điểm ở chu kỳ kế tiếp, tạo nên một đợt tấn công từ chối dịch vụ tự phát (accidental DDoS), khiến máy chủ tiếp tục nghẽn sâu hơn và không thể tự phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> - **System prompt đã chọn:**
>   `"Bạn là Trợ giảng AI đồng hành tận tâm của chương trình đào tạo AI thực chiến. Hãy trả lời cô đọng, súc tích, giải thích trực diện bản chất kỹ thuật kèm ví dụ code minh họa ngắn gọn bằng tiếng Việt, tránh lý thuyết rườm rà."`
> - **Giải thích các lựa chọn từ ngữ quan trọng:**
>   1. Yêu cầu *"cô đọng, súc tích, tránh lý thuyết rườm rà"*: Tối ưu hóa số lượng output token (giúp streaming hiển thị nhanh hơn, tiết kiệm chi phí) và tránh gây mệt mỏi cho người dùng khi đọc trên giao diện terminal dòng lệnh.
>   2. Chỉ định *"bằng tiếng Việt"*: Cố định ngôn ngữ phản hồi nhất quán, ngăn chặn tình trạng mô hình tự động chuyển sang tiếng Anh khi người dùng nhập câu hỏi có chứa các thuật ngữ lập trình chuyên ngành.
>   3. Cụm từ *"kèm ví dụ code minh họa ngắn gọn"*: Phục vụ trực tiếp cho mục tiêu học tập thực chiến, giúp người học dễ dàng đối chiếu lý thuyết với cách triển khai mã lệnh thực tế.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> - **Hạn chế lớn nhất:** Trợ lý hiện tại sử dụng cơ chế cửa sổ trượt cố định (sliding window) chỉ lưu 3 lượt hội thoại gần nhất (tối đa 6 messages) và chỉ lưu trong bộ nhớ RAM tạm thời của tiến trình terminal. Khi cuộc trò chuyện kéo dài quá 3 câu hỏi hoặc khi người dùng thoát phiên làm việc, toàn bộ bối cảnh ngữ cảnh trước đó (thông tin cá nhân, mục tiêu dự án, các ràng buộc đã thỏa thuận) sẽ bị lãng quên hoàn toàn.
> - **Đề xuất cải thiện & Cách triển khai:** Triển khai **Bộ nhớ tóm tắt ngữ cảnh kết hợp cơ sở dữ liệu Vector (Context Summarization + Vector RAG Memory)**:
>   + *Cách triển khai:* Khi lịch sử hội thoại vượt quá 3 lượt, thay vì xóa bỏ hoàn toàn tin nhắn cũ, hệ thống sử dụng một model nhỏ gọn (như GPT-4o-mini) để tự động tóm tắt các điểm mấu chốt của các lượt đã qua thành một đoạn `conversation_summary` chèn vào ngay sau System Prompt. Đồng thời, lưu trữ các lượt hỏi-đáp vào một cơ sở dữ liệu vector cục bộ (như SQLite-vss hoặc ChromaDB); khi người dùng hỏi một chủ đề liên quan đến quá khứ, hệ thống sẽ tự động truy vấn tìm lại thông tin cũ để đưa vào context, giúp trợ lý sở hữu trí nhớ dài hạn mà không làm bùng nổ token.

---

## Danh Sách Kiểm Tra Nộp Bài

- [x] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [x] Cả 4 checkpoint pytest đều pass
- [x] Tất cả 9 câu trong file này đã được trả lời
- [x] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
