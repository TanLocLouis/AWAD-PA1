# Self Assessment Report — PA#1

**Repository:** `https://github.com/TanLocLouis/AWAD-PA1.git`  
**Team Members (IDs in ascending order):**
- 23120050: Nguyễn Nhật Khang
- 23120057: Lê Tấn Lộc
- 23120066: Võ Thiện Nhân

**Total Claimed Mark:** 98 / 100  
**Zip File Name:** `23120050-23120057-23120066_98.zip`

---

## Rubric Self-Assessment Table

| # | Criterion | Max | Claimed | Evidence (Section / File / Specific Detail) |
|---|---|:---:|:---:|---|
| **1** | **Problem and users** | 20 | 20 | **Section 1 (Problem and Users):** Nêu đích danh 2 persona trong ngữ cảnh cụ thể: *Minh (nhà sản xuất tại sân khấu acoustic)* và *Linh (sinh viên canh săn vé)* vào khung giờ 12:00 trưa thứ Sáu. Vấn đề cốt lõi được phát biểu cô đọng trong đúng 1 câu trích dẫn (Blockquote). Nêu rõ giải pháp chắp vá hiện tại họ đang dùng (Google Form + rà soát sao kê ngân hàng 3 ngày, web cũ bị treo gateway gây trừ tiền oan). |
| **2** | **The LLM feature and cost of being wrong** | 25 | 25 | **Section 2 (The LLM Feature & The Cost of Being Wrong):** Mô tả 1 tính năng cụ thể (*PDF Press-Kit & Artist Bio Summarizer*) trích xuất thông tin sang JSON có cấu trúc. Định lượng rõ người bị thiệt hại (*Minh, nghệ sĩ, 100 khán giả mua vé VIP*), số tiền thiệt hại cụ thể (*150.000.000 VNĐ* tiền hoàn vé + nguy cơ bị đình chỉ biểu diễn vì sai giới hạn tuổi). Khẳng định tính chất không thể hoàn tác (*un-undoable*) một khi vé đã mở bán, và đưa ra cơ chế kiểm soát phòng ngừa (*Strict Structured Outputs* + quy trình duyệt *Human-in-the-loop* bắt buộc). |
| **3** | **Scope: in and out** | 15 | 15 | **Section 3 (Scope for the Semester):** Xác định rõ kiến trúc Modular Monolith trên nền Web với 5 module chức năng và 2 nhóm người dùng (`Audience`, `Organizer`). Đưa ra danh sách loại trừ (*Out-of-Scope*) tường minh: loại bỏ hoàn toàn tính năng quét QR qua camera, ứng dụng di động native, cổng thanh toán thật, hoàn tiền tự động và sơ đồ ghế phức tạp. Danh sách phạm vi hoàn toàn khớp với kế hoạch các checkpoint. |
| **4** | **Plan and ownership** | 20 | 20 | **Section 4 (Plan and Ownership):** Bảng kế hoạch gồm đúng 6 checkpoints trải dài suốt học kỳ (từ 17/09/2026 đến 17/12/2026), phù hợp tiến độ môn học. Mỗi checkpoint bàn giao sản phẩm cụ thể và được gán trách nhiệm duy nhất cho một thành viên phụ trách chính (A, B hoặc C), không giao việc chung chung theo nhóm. |
| **5** | **Risks** | 10 | 9 | **Section 5 (Risks & Immediate Mitigations):** Xác định đúng 2 rủi ro kỹ thuật có thể đánh sập đồ án (*Lỗi Race Condition làm bán âm vé* và *File PDF định dạng phức tạp khiến trích xuất văn bản bị lỗi font/gãy câu*). Cả hai đều có hành động khắc phục cụ thể triển khai ngay trong tuần này kèm code/script thực nghiệm độc lập. *(Tự trừ 1 điểm vì chưa tính đến kịch bản server Redis bị crash đột ngột trong lúc đang giữ chỗ vé).* |
| **6** | **Technology choices** | 10 | 9 | **Section 6 (Technology Choices):** Toàn bộ 7 công nghệ lựa chọn đều có đúng 1 dòng giải thích gắn liền với nhu cầu kỹ thuật của đồ án. Chỉ rõ mô hình LLM (*Google Gemini 1.5 Flash* qua Google AI Studio), đơn giá token đầu vào/đầu ra cụ thể ($0,075 / $0,30 trên 1 triệu tokens) và lý do sử dụng cửa sổ ngữ cảnh lớn. *(Tự trừ 1 điểm vì chưa cấu hình mô hình Fallback offline cục bộ khi mất kết nối Internet).* |
| **Total** | | **100** | **98** | *(Điểm tự đánh giá 98 trùng khớp với số trong tên tệp `23120050-23120057-23120066_98.zip`)* |

---

## What I Did Not Manage (Những điểm chưa kịp xử lý)

1. **Cơ chế Fallback khi LLM API mất kết nối hoặc chạm Rate Limit:** Nhóm chưa kịp xây dựng module dự phòng sử dụng mô hình mã nguồn mở cục bộ (như Llama hoặc Qwen chạy qua Ollama) nếu kết nối tới Gemini API bị gián đoạn.
2. **Xử lý các tệp PDF bảo mật hoặc scan dạng ảnh hoàn toàn:** Pipeline hiện tại chỉ xử lý tốt các tệp PDF dạng văn bản số hóa (digital text); nhóm chưa tích hợp module OCR để xử lý các tài liệu PDF scan từ bản cứng hoặc các file có đặt mật khẩu bảo vệ.
3. **Cơ chế phân cụm Redis (Redis Cluster/Sentinel):** Hiện tại hệ thống đang dựa vào một instance Redis duy nhất trong Docker Compose; nhóm chưa xây dựng kịch bản dự phòng failover nếu container Redis bị dừng đột ngột.