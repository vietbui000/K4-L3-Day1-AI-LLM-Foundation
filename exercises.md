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
> Temperature 0.0 trả về phản hồi cố định, nhất quán, logic và đi thẳng vào thực tế. Khi tăng lên 0.5 - 1.0, văn phong tự nhiên, đa dạng và sáng tạo hơn. Ở mức 1.5, phản hồi trở nên quá ngẫu nhiên, thiếu liên kết và có thể xuất hiện thông tin tưởng tượng (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature ở mức **0.0 - 0.2** cho chatbot hỗ trợ khách hàng. Lý do là ứng dụng CSKH yêu cầu tính chính xác tuyệt đối, thông tin chính sách/sản phẩm nhất quán và cần tránh tối đa việc model tự sáng tạo thông tin sai sự thật.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Với 10.000 users/ngày × 3 lượt × 350 token output = 10,5 triệu token output/ngày. Chi phí GPT-4o ($0.010/1k token) đắt hơn GPT-4o-mini ($0.0006/1k token) khoảng 16.7 lần (~$105/ngày so với ~$6.3/ngày). GPT-4o xứng đáng với chi phí khi cần phân tích hợp đồng pháp lý phức tạp hay lập luận kỹ thuật chuyên sâu. Nên dùng GPT-4o-mini cho chatbot tra cứu FAQ đơn giản hay phân loại tin nhắn khách hàng.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Với vai giáo viên tiểu học, phản hồi ngắn gọn, dùng từ ngữ đơn giản và ví dụ trực quan ("cuốn sổ nhật ký chuyền tay"). Với vai chuyên gia tài chính, phản hồi dùng thuật ngữ chuyên ngành (hash, mã hóa bất đối xứng, thuật toán đồng thuận) và đi sâu vào tính toàn vẹn dữ liệu. System prompt định hình rõ vai trò, văn phong, độ sâu kiến thức và góc nhìn phản hồi của model.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Đếm bằng tiktoken cho ra số token cao hơn ước lượng `số từ / 0.75` khoảng 30% - 60%. Nguyên nhân là tokenizer của OpenAI được tối ưu chủ yếu cho tiếng Anh; tiếng Việt chứa nhiều ký tự có dấu (Unicode) và từ ghép nên bị chia nhỏ thành nhiều sub-word token hơn so với tiếng Anh cùng độ dài.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các ứng dụng chat tương tác trực tiếp (như ChatGPT UI/CLI assistant) vì giúp giảm đáng kể thời gian chờ token đầu tiên (Time-To-First-Token), tạo cảm giác phản hồi tức thì. Ngược lại, non-streaming phù hợp hơn cho các tác vụ chạy ngầm (background jobs), gọi API lấy dữ liệu cấu trúc (JSON schema), hoặc khi cần kiểm duyệt toàn bộ câu trả lời trước khi hiển thị.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp giảm áp lực dồn dập lên server bị quá tải bằng cách giãn khoảng cách giữa các lần thử lại ngày càng xa. Nếu hàng nghìn client cùng retry với delay cố định (ví dụ 1s), sẽ gây ra hiện tượng Thundering Herd (tất cả gửi lại cùng một thời điểm), khiến hệ thống liên tục bị nghẽn và không thể phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> System prompt: "Bạn là trợ giảng thân thiện của khóa học AI, giải thích ngắn gọn, dễ hiểu bằng tiếng Việt và có kèm ví dụ minh họa." Yêu cầu "ngắn gọn" để tiết kiệm token và giữ tập trung vào ý chính; chỉ định "tiếng Việt" để đảm bảo ngôn ngữ phản hồi thống nhất cho học viên; vai "trợ giảng thân thiện" giúp tạo văn phong gần gũi và hỗ trợ học viên hiệu quả.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất hiện tại là lịch sử hội thoại chỉ giữ 3 lượt gần nhất, làm trợ lý quên mất bối cảnh ban đầu khi hội thoại kéo dài. Đề xuất cải thiện: Triển khai cơ chế Summarized History. Khi history vượt quá 6 messages, gọi model nhỏ (`gpt-4o-mini`) để tóm tắt các lượt hội thoại cũ thành một đoạn văn ngắn lưu vào System Prompt/Context thay vì xén bỏ hoàn toàn.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
