# Project Proposal: TicketBox

**Repository Link:** `https://github.com/`   
**Team Members:**
- 23120050: Nguyễn Nhật Khang
- 23120057: Lê Tấn Lộc
- 231200xx: Võ Thiện Nhân

---

## 1. Problem and Users

### A Named User in a Named Situation
- **Minh (28 tuổi)**, nhà sản xuất sự kiện âm nhạc tại một phòng trà/sân khấu liveband tại TP.HCM, chuẩn bị mở bán đợt vé Early Bird (800 vé) cho đêm nhạc acoustic vào đúng 12:00 trưa thứ Sáu.
- **Linh (21 tuổi)**, khán giả hâm mộ, đã nạp sẵn 600.000 VNĐ vào tài khoản, canh đúng 12:00 để vào website săn 1 vé hạng GA.

### One Problem Stated in One Sentence
> Khi lưu lượng truy cập tăng đột biến trong đợt mở bán chớp nhoáng (flash-sale), các truy vấn ghi đồng thời làm nghẽn cơ sở dữ liệu dẫn đến tình trạng bán vượt số lượng vé (overselling) và người mua bị trừ tiền nhưng không nhận được vé.

### What They Do Today Instead
- **Minh hiện tại:** Dùng Google Form kết hợp kiểm tra chuyển khoản thủ công. Khi mở bán, hơn 1.200 người điền form cùng lúc khiến số đơn vượt xa 800 vé ban đầu, buộc ban tổ chức phải mất 3 ngày rà soát sao kê và chuyển khoản hoàn tiền thủ công cho hơn 400 người.
- **Linh hiện tại:** Mua qua các trang web thông thường; khi bấm thanh toán, trang bị treo do nghẽn database, tài khoản bị trừ tiền nhưng hệ thống báo lỗi hết vé, khiến Linh rơi vào tình trạng mất tiền oan và phải chờ hỗ trợ đối soát kéo dài.

---

## 2. The LLM Feature & The Cost of Being Wrong

### One Concrete LLM Feature
Tính năng **PDF Press-Kit & Artist Bio Summarizer** tích hợp trong trang quản trị:
Khi Minh tạo concert, thay vì đọc thủ công tài liệu PDF giới thiệu nghệ sĩ (Press Kit dài 5–15 trang), Minh tải file PDF lên hệ thống. LLM trích xuất và trả về dữ liệu có cấu trúc (Strict JSON):
1. Đoạn tóm tắt tiểu sử ngắn (Teaser Bio 100–120 từ) hiển thị trên banner trang bán vé.
2. Danh sách bài hát tiêu biểu (hit tracks) và phong cách biểu diễn chính.
3. Quy định độ tuổi khán giả (Age Policy: e.g., 16+ hoặc All Ages) và các quyền lợi đặc biệt của từng hạng vé (ví dụ: vé VIP bao gồm áo thun lưu niệm).

### The Cost of Being Wrong: Who is harmed, how much, and can it be undone?
- **Ai bị ảnh hưởng (Who is affected):** Ban tổ chức (Minh), nghệ sĩ biểu diễn và 100 khán giả mua vé VIP (giá 1.500.000 VNĐ/vé).
- **Thiệt hại định lượng (How much):**
  - *Về mặt tài chính:* Nếu LLM bị ảo giác (hallucination) trích xuất sai từ file PDF: ghi nhận vé VIP được "Tặng kèm đĩa than có chữ ký" trong khi thực tế nghệ sĩ chỉ tặng "Poster in", 100 khán giả VIP khi nhận vé sẽ khiếu nại sai lệch thông tin cam kết, buộc Minh phải bồi thường hoặc hoàn tiền vé VIP (thiệt hại lên tới 150.000.000 VNĐ).
  - *Về mặt pháp lý & tổ chức:* Nếu tài liệu yêu cầu hạn chế độ tuổi "18+" nhưng LLM tóm tắt thành "Phù hợp mọi lứa tuổi", sự kiện có nguy cơ bị đình chỉ biểu diễn ngay trước giờ diễn do vi phạm quy định cấp phép văn hóa.
- **Có thể đảo ngược được không (Can it be undone?):** **KHÔNG THỂ.** Một khi vé đã mở bán công khai và người dùng đã thanh toán, thông tin công bố có giá trị như hợp đồng dịch vụ; việc đơn phương hủy quyền lợi sau đó sẽ gây ra khủng hoảng tẩy chay và tranh chấp pháp lý.
- **Cách phát hiện và kiểm soát (How would we know):** Hệ thống áp dụng cơ chế *Structured Outputs* (bắt buộc đúng schema JSON) và quy trình *Human-in-the-loop*: Bản tóm tắt của LLM luôn ở trạng thái `draft`, hiển thị đối chiếu trực quan bên cạnh tệp PDF; Minh bắt buộc phải đọc, chỉnh sửa nếu cần và bấm "Xác nhận duyệt" thì nội dung mới được hiển thị công khai.

---

## 3. Scope for the Semester

### Trong phạm vi (In-Scope)
- **Kiến trúc Modular Monolith (Web-only):** Triển khai trong một repository duy nhất, phân chia thư mục theo từng domain độc lập:
  - `Auth Module`: Phân quyền cơ bản 2 vai trò (`Audience` và `Organizer`) qua JWT.
  - `Catalog Module`: Quản lý danh sách concert, thông tin nghệ sĩ, phân hạng vé (Tier: Standard, VIP).
  - `Booking & Inventory Module`: Quản lý số lượng vé theo thời gian thực, giữ chỗ nguyên tử bằng Redis (atomic decrement), khóa vé tạm thời trong 10 phút.
  - `Order & Mock Payment Module`: Tạo đơn hàng, cơ chế `Idempotency-Key` chống bấm thanh toán 2 lần, giả lập thanh toán thành công/thất bại, xuất mã vé điện tử (E-ticket code).
  - `LLM Module`: Pipeline nhận file PDF, parse text, gọi LLM trích xuất JSON tóm tắt tiểu sử và quyền lợi.
- **Giao diện Web Responsive (React.js):**
  - Khán giả: Trang chủ danh sách sự kiện, trang chi tiết concert, luồng chọn vé kèm bộ đếm ngược giữ chỗ 10 phút, màn hình thanh toán mô phỏng và trang xem vé đã mua.
  - Ban tổ chức: Giao diện tạo sự kiện, cấu hình số lượng/giá vé, tải lên PDF tiểu sử và màn hình duyệt bản nháp do LLM sinh ra.

### Ngoài phạm vi (Out-of-Scope)
- **Tính năng Soát vé thực địa / Quét mã QR bằng Camera (On-site QR Check-in):** Toàn bộ quy trình quét mã bằng camera và vai trò nhân viên soát vé (`Staff`) được lược bỏ để tập trung hoàn toàn vào bài toán flash-sale và LLM. Người dùng chỉ cần xem mã vé điện tử dạng chuỗi ký tự trên web.
- **Ứng dụng di động (Mobile App native):** Không làm app iOS/Android; chỉ tập trung web responsive.
- **Tích hợp cổng thanh toán thực tế (VNPAY, MoMo):** Sử dụng Mock Payment để giả lập độ trễ mạng và trạng thái giao dịch.
- **Hoàn tiền tự động (Automated Refund):** Không xử lý luồng hủy vé/hoàn tiền sau khi đã mua thành công.
- **Sơ đồ ghế đồ họa phức tạp:** Chỉ bán theo hạng vé (loại vé và số lượng), không chọn ghế theo tọa độ visual.

---

## 4. Plan and Ownership

Kế hoạch 6 Checkpoints được thiết kế vừa sức cho nhóm 3 người, tương thích với lịch học kỳ:

| Checkpoint | Hạn nộp (Due Date) | Nội dung công việc bàn giao | Người phụ trách chính |
| :--- | :--- | :--- | :--- |
| **CP#1: Project Scaffold & Database Design** | 20/10/2026 | Khởi tạo repo, thiết lập cấu trúc thư mục Modular Monolith (Express.js + Vite/React), Docker Compose (PostgreSQL, Redis), thiết kế ERD cơ sở dữ liệu. | **Nguyễn Văn A** |
| **CP#2: Auth & Event Catalog** | 03/11/2026 | Hoàn thiện Auth API (JWT, phân quyền Audience/Organizer); CRUD sự kiện và hạng vé; UI xem danh sách và chi tiết sự kiện trên React. | **Trần Thị B** |
| **CP#3: Basic Booking & Mock Payment Flow** | 17/11/2026 | Tạo đơn hàng, tích hợp `Idempotency-Key` chống trùng giao dịch, Mock Payment (nút xác nhận thành công/thất bại), giao diện xem vé đã mua. | **Nguyễn Văn A** |
| **CP#4: Redis Concurrency & Anti-Overselling** | 01/12/2026 | Tích hợp Redis Lua script để trừ số lượng vé nguyên tử (atomic decrement); cơ chế giữ vé 10 phút (TTL rollback); viết script test tranh mua vé cơ bản. | **Nguyễn Văn A** |
| **CP#5: LLM PDF Summarization Pipeline** | 15/12/2026 | Pipeline nhận file PDF, parse text, gọi Gemini API sinh JSON có cấu trúc; giao diện upload PDF và màn hình duyệt/sửa bản nháp cho Organizer. | **Lê Văn C** |
| **CP#6: Integration, Polish & Final Demo** | 29/12/2026 | Nối hoàn chỉnh toàn bộ luồng từ tạo sự kiện, trích xuất bio, mở bán chống oversell; đóng gói Docker chạy toàn bộ hệ thống; hoàn thiện báo cáo và kịch bản demo. | **Trần Thị B** |

---

## 5. Risks & Immediate Mitigations

### Risk 1: Tranh chấp dữ liệu (Race Condition) làm bán vượt số lượng vé (Overselling)
- **Bản chất rủi ro:** Khi hàng trăm người dùng cùng bấm nút đặt vé trong 1 giây, các lệnh đọc/ghi đồng thời vào database nếu không được cô lập tốt sẽ dẫn đến tình trạng bán âm vé.
- **Biện pháp giảm thiểu bắt đầu ngay tuần này (Mitigation starting this week):**  
  *Ngay trong tuần này*, Nguyễn Văn A viết một script Node.js độc lập kiểm thử trên một instance Redis cục bộ, sử dụng lệnh `DECRBY` kết hợp kiểm tra điều kiện (hoặc Lua script đơn giản) để đảm bảo bộ đếm dừng chính xác khi số vé về 0 dưới 50 worker chạy song song.

### Risk 2: File PDF của nghệ sĩ bị lỗi font hoặc dàn trang phức tạp làm LLM trích xuất sai
- **Bản chất rủi ro:** Tài liệu PDF từ nghệ sĩ có thể sử dụng layout nhiều cột hoặc font chữ không tiêu chuẩn, khiến thư viện trích xuất text trả về chuỗi văn bản lộn xộn, làm LLM hiểu sai ngữ cảnh về quyền lợi vé và quy định độ tuổi.
- **Biện pháp giảm thiểu bắt đầu ngay tuần này (Mitigation starting this week):** Thu thập 3 file PDF profile nghệ sĩ thực tế, dùng thư viện `pdf-parse` để kiểm tra chất lượng chuỗi text trích xuất được; xây dựng prompt mẫu trên Google AI Studio với Gemini 1.5 Flash và thiết lập schema JSON cố định để đo lường độ chính xác của kết quả đầu ra.

---

## 6. Technology Choices

- **Node.js & Express.js (TypeScript):** Nền tảng backend gọn nhẹ, xử lý bất đồng bộ tốt, dễ tổ chức cấu trúc thư mục theo từng module nghiệp vụ độc lập mà không cần boilerplate phức tạp.
- **React.js (Vite / TypeScript):** Xây dựng Single Page Application (SPA) phản hồi nhanh, dễ dàng quản lý state cho bộ đếm thời gian giữ vé 10 phút và giao diện duyệt bản nháp LLM.
- **PostgreSQL 16:** Cơ sở dữ liệu quan hệ đảm bảo tính toàn vẹn dữ liệu chuẩn ACID, đóng vai trò lưu trữ vĩnh viễn (single source of truth) cho thông tin concert và đơn hàng.
- **Redis 7 (Alpine):** Bộ nhớ in-memory tốc độ cao, dùng để lưu trữ và thực thi trừ bộ đếm vé nguyên tử (atomic decrement), giải quyết triệt để vấn đề overselling khi mở bán.
- **Google Gemini 1.5 Flash (Google AI Studio API):** Chi phí cực kỳ tiết kiệm ($0,075 / 1 triệu input tokens, $0,30 / 1 triệu output tokens; có hạn mức miễn phí), ngữ cảnh lớn hỗ trợ đọc trọn vẹn file PDF và hỗ trợ chế độ Structured Outputs (JSON Schema) chuẩn xác.
- **Docker Compose:** Đóng gói toàn bộ môi trường phát triển (Express Backend, React Frontend, PostgreSQL, Redis) giúp triển khai cục bộ nhanh chóng chỉ với một câu lệnh.