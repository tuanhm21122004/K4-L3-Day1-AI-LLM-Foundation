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
> - Khi temperature = 0.0: Câu trả lời mang tính tất định cao nhất (deterministic), tập trung vào các sự thật phổ biến nhất (như Việt Nam là nước xuất khẩu cà phê/hạt điều hàng đầu thế giới hoặc hình chữ S) và nội dung hầu như không đổi giữa các lần gọi lặp lại.
> - Khi temperature tăng lên 0.5 - 1.0: Văn phong trở nên tự nhiên, uyển chuyển hơn, từ vựng đa dạng và bắt đầu xuất hiện những chi tiết thú vị, độc đáo hơn (như hệ thống hang Sơn Đoòng, văn hóa ẩm thực cà phê trứng).
> - Khi temperature đạt mức 1.5: Câu trả lời trở nên khó đoán, bắt đầu dùng từ ngữ bay bổng quá mức hoặc rời rạc, có xu hướng bịa đặt chi tiết (ảo giác/hallucination) và câu văn đôi chỗ thiếu mạch lạc.
> Quy luật rút ra: Temperature tỷ lệ thuận với tính đa dạng và sáng tạo của câu trả lời, nhưng tỷ lệ nghịch với tính ổn định và độ tin cậy của thông tin.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature ở mức thấp, cụ thể là từ 0.0 đến 0.2 (khuyến nghị 0.1).
> Lý do: Đối với chatbot hỗ trợ khách hàng (CSKH), tiêu chí quan trọng nhất là tính chính xác, tính nhất quán và độ tin cậy của thông tin (chính sách đổi trả, bảng giá sản phẩm, quy trình hỗ trợ kỹ thuật). Đặt temperature thấp giúp giảm thiểu tối đa hiện tượng "ảo giác" (hallucination), đảm bảo bot trả lời trung thực, bám sát tài liệu hướng dẫn (knowledge base) và không tự ý sáng tác ra các chính sách sai lệch gây thiệt hại cho doanh nghiệp và khách hàng.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> - Ước tính chi phí:
>   + Đơn giá đầu ra: GPT-4o là $0.010 / 1K token, trong khi GPT-4o-mini là $0.0006 / 1K token.
>   + Như vậy, GPT-4o đắt hơn GPT-4o-mini xấp xỉ 16.67 lần (0.010 / 0.0006).
>   + Với khối lượng 10.000 users × 3 lần × 350 token = 10.500.000 token/ngày:
>     * Chi phí GPT-4o-mini: 10.500 × $0.0006 = $6.30 / ngày (~$189 / tháng).
>     * Chi phí GPT-4o: 10.500 × $0.010 = $105.00 / ngày (~$3.150 / tháng).
>   + Chi phí sử dụng GPT-4o đắt hơn mini gần $3.000 mỗi tháng.
> - Trường hợp xứng đáng dùng GPT-4o: Các tác vụ suy luận phức tạp, phân tích dữ liệu đa bước, giải toán/lập trình chuyên sâu, hoặc rà soát hợp đồng pháp lý đòi hỏi độ chính xác tuyệt đối mà model nhỏ dễ mắc sai sót.
> - Trường hợp nên dùng mini: Các tác vụ phân loại ý định người dùng (intent classification), trích xuất thông tin thực thể, chatbot hỏi đáp FAQ thông thường, tóm tắt bài viết ngắn hoặc các tác vụ chạy ngầm với tần suất lớn.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> - Về độ dài và từ vựng: Phản hồi của "giáo viên tiểu học" ngắn gọn, ngôn từ giản dị, gần gũi, sử dụng các hình ảnh ẩn dụ quen thuộc với trẻ em (như cuốn sổ nhật ký chung của cả lớp mà ai cũng có một bản copy, không ai tự tẩy xóa được). Ngược lại, phản hồi của "chuyên gia tài chính" dài hơn, có cấu trúc chặt chẽ và sử dụng nhiều thuật ngữ chuyên sâu (sổ cái phân tán - distributed ledger, mật mã học - cryptography, thuật toán đồng thuận - consensus mechanism, tính bất biến - immutability).
> - Vai trò của System Prompt: System prompt đóng vai trò thiết lập khung ngữ cảnh (conditioning), định hình phong cách giao tiếp, giọng điệu (tone of voice), độ sâu kiến thức và đối tượng mục tiêu trước khi mô hình tiếp nhận câu hỏi của người dùng. Nhờ đó, cùng một kiến thức cốt lõi nhưng model có thể linh hoạt chuyển hóa nội dung cho phù hợp hoàn hảo với ngữ cảnh sử dụng mà không cần thay đổi câu hỏi đầu vào.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> - Kết quả so sánh thực tế: Với một đoạn văn tiếng Việt mẫu 100 từ, công thức ước lượng của tiếng Anh tính ra khoảng 100 / 0.75 ≈ 133 token. Tuy nhiên, khi dùng thư viện `tiktoken` (bộ mã hóa `cl100k_base` hoặc `o200k_base`), số token thực tế đo được thường rơi vào khoảng 190 đến 220 token, cao hơn từ 42% đến 65% so với con số ước tính.
> - Lý do tiếng Việt tốn nhiều token hơn: Các thuật toán mã hóa như Byte Pair Encoding (BPE) của OpenAI được huấn luyện chủ yếu trên tập dữ liệu tiếng Anh, nơi các từ hoặc cụm từ hoàn chỉnh được gán thành 1 token duy nhất. Trong khi đó, tiếng Việt sử dụng bảng chữ cái Latin có dấu thanh (sắc, huyền, hỏi, ngã, nặng) và các ký tự đặc thù (ă, â, đ, ê, ô, ơ, ư). Khi mã hóa UTF-8, các ký tự này thường bị bẻ nhỏ thành nhiều byte và không nằm trong từ điển phổ biến của tokenizer, dẫn đến việc 1 từ tiếng Việt thường bị chia thành 2 đến 3 token, làm tăng đáng kể chi phí và độ trễ khi xử lý.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các ứng dụng có tương tác trực tiếp với con người (chatbots, trợ lý ảo cá nhân, công cụ hỗ trợ viết nội dung trực tiếp), nơi Time-to-First-Token (TTFT) đóng vai trò quyết định: người dùng nhìn thấy từng chữ xuất hiện ngay sau 0.5 – 1 giây, mang lại cảm giác phản hồi tức thì thay vì phải sốt ruột nhìn biểu tượng loading trong 10-15 giây để đợi trọn vẹn văn bản. Ngược lại, non-streaming lại phù hợp và tối ưu hơn trong các tác vụ ngầm (background batch jobs), tích hợp API giữa các hệ thống backend với nhau, hoặc khi cần trích xuất dữ liệu có cấu trúc nghiêm ngặt (JSON/XML qua Function Calling/Structured Outputs) nơi toàn bộ chuỗi phản hồi phải được hoàn thiện đầy đủ trước khi tiến hành parse và xử lý logic tiếp theo.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> - Lợi thế của Exponential Backoff: Khi máy chủ bị quá tải (mã lỗi 429 hoặc 503), việc kéo giãn thời gian chờ tăng theo cấp số nhân (ví dụ: 0.1s -> 0.2s -> 0.4s -> 0.8s) giúp giảm nhanh mật độ request gửi đến hệ thống, tạo ra những khoảng lặng cần thiết để máy chủ API kịp xử lý hàng đợi và giải phóng tài nguyên.
> - Hậu quả của delay cố định: Nếu hàng nghìn client cùng gặp lỗi và cùng chờ đúng 1 giây để thử lại, tất cả sẽ đồng loạt gửi lại request vào cùng một thời điểm ở giây tiếp theo. Hiện tượng này tạo ra các xung đột tải định kỳ mang tính hủy diệt gọi là **Thundering Herd Problem** (hiệu ứng bầy đàn xung đột), khiến máy chủ vừa định hồi phục lại ngay lập tức bị nhấn chìm trong làn sóng request dồn dập và rơi vào trạng thái tê liệt kéo dài.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> - Persona lựa chọn: Trợ lý gia sư lập trình Python tận tâm và súc tích dành cho người mới bắt đầu.
> - System prompt hoàn chỉnh:
>   `"Bạn là trợ lý gia sư lập trình Python thân thiện và giàu kinh nghiệm. Hãy giải thích các khái niệm bằng tiếng Việt giản dị, ngắn gọn, luôn đi kèm ví dụ code minh họa tối giản và đặt câu hỏi gợi mở để người học tự tư duy."`
> - Giải thích 2 lựa chọn từ ngữ then chốt:
>   1. `"ngắn gọn, ví dụ code minh họa tối giản"`: Giúp phản hồi hiển thị sạch sẽ và dễ theo dõi trên giao diện dòng lệnh (CLI), tránh việc in ra những khối văn bản khổng lồ gây quá tải thị giác, đồng thời tiết kiệm chi phí token đầu ra.
>   2. `"bằng tiếng Việt giản dị"`: Đảm bảo mô hình luôn trao đổi bằng tiếng Việt tự nhiên và chuẩn mực, không lạm dụng thuật ngữ tiếng Anh không cần thiết khi người học là người mới tiếp cận lập trình.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> - Hạn chế lớn nhất: Trợ lý chỉ duy trì được 3 lượt hội thoại gần nhất trong bộ nhớ tạm (`history[-6:]`) và toàn bộ ngữ cảnh sẽ mất sạch khi tắt terminal (không có bộ nhớ dài hạn). Khi người dùng hỏi một chuỗi vấn đề dài hoặc muốn quay lại thông tin đã cung cấp ở đầu buổi, trợ lý sẽ hoàn toàn "mất trí nhớ", gây gián đoạn trải nghiệm học tập.
> - Đề xuất cải thiện: Xây dựng cơ chế **Tóm tắt ngữ cảnh tự động (Rolling Context Summarization)** kết hợp lưu trữ phiên hội thoại vào cơ sở dữ liệu cục bộ (SQLite / JSON).
> - Cách triển khai:
>   1. Khi lịch sử hội thoại vượt quá 3 lượt, trước khi cắt bỏ các message cũ, sử dụng một model nhỏ (như `gemini-flash-lite` hoặc `gpt-4o-mini`) để tóm tắt các thông tin quan trọng nhất của các lượt cũ thành 2-3 câu ngắn.
>   2. Nhúng đoạn tóm tắt này vào ngay sau `system prompt` dưới dạng `Context Summary: <nội dung tóm tắt>` cho các lượt gọi API tiếp theo.
>   3. Lưu toàn bộ lịch sử thô kèm ID phiên (Session ID) vào file SQLite cục bộ trên máy, cho phép khi người dùng mở lại CLI có thể tiếp tục mạch thảo luận của ngày hôm trước.

---

## Danh Sách Kiểm Tra Nộp Bài

- [x] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [x] Cả 4 checkpoint pytest đều pass
- [x] Tất cả 9 câu trong file này đã được trả lời
- [x] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
