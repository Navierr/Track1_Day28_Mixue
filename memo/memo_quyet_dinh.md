# MEMO QUYẾT ĐỊNH (DECISION MEMO) — §6.4
## ĐỀ XUẤT HÀNH ĐỘNG CHO ÁP DỤNG AI: VLEARN AI TUTOR

- **Dự án:** VLearn AI Tutor (Trợ lý học thuật nhúng LMS Moodle/Canvas)
- **Nhóm thực hiện (2 thành viên):**
  - **Phạm Tiến Hưng (2A202601800)** — Team Lead / Product & Business Ops Lead
  - **Nguyễn Quang Sơn (2A202601956)** — Technical & Integration Lead
- **Ngày lập:** 03/09/2026
- **Trạng thái:** Bản v2 hoàn thiện sau kiểm tra chéo (Post Peer-Review)

---

## PHẦN 1: BỐI CẢNH & CHẨN ĐOÁN NGUYÊN NHÂN GỐC

### 1.1. Bối cảnh & Hiện trạng quan sát được
VLearn AI Tutor là sản phẩm EdTech B2B2C nhúng trực tiếp vào LMS trường đại học qua chuẩn LTI 1.3 nhằm giải quyết điểm đau lớn nhất: **sinh viên thiếu trợ giảng hỗ trợ học tập trong khung giờ 21h–23h** (đã xác thực tại Day 26–27). 

Tuy nhiên, sau giai đoạn cấp tài khoản thử nghiệm (Deployment), sản phẩm rơi vào tình trạng **"Ít người dùng quay lại, Giảng viên e ngại cấm đoán"**. Ban đầu, có ý kiến cho rằng cần *"mở thêm lớp đào tạo sử dụng prompt cho sinh viên"*, nhưng phân tích thực tế cho thấy **ít dùng chỉ là triệu chứng, không phải nguyên nhân gốc**.

### 1.2. Chẩn đoán 2 Nguyên nhân gốc qua ADKAR & Bằng chứng thực nghiệm Day 20–21
Dựa trên hồ sơ đánh giá thực nghiệm (`REPORT.md` và `results-v1.jsonl` từ Day 20–21), nhóm xác định 2 nguyên nhân gốc:

1. **Nguyên nhân 1 — Điểm nghẽn ở DESIRE / Niềm tin (Kiến trúc tin cậy bị hỏng):**
   - *Bằng chứng cứng Day 20–21:* Tỷ lệ trích dẫn nguyên văn (`quote_verbatim`) chỉ đạt **60.0% (15/25 câu)**; có tới **40.0% (10/25 câu)** bị lỗi cắt xén, ghép nối bằng dấu `...` hoặc dẫn sai section (`sc-04, 05, 08, 13, 15`).
   - *Hậu quả:* Sinh viên không thể đối chiếu câu trả lời với giáo trình, sợ bị trừ điểm khi nộp bài nên quay về cách làm cũ (tự tìm file hoặc hỏi bạn).
   - *Quyết định tại Day 21:* Đánh giá Composite Gate chỉ đạt **48.0% (12/25)**, hệ thống bị **HOLD (Chưa được ship)** vì không đủ độ tin cậy.

2. **Nguyên nhân 2 — Điểm nghẽn ở ABILITY / Quy trình & Rủi ro Liêm chính:**
   - *Bằng chứng cứng Day 20–21:* Ở kịch bản đối kháng `sc-20`, AI Tutor vi phạm nghiêm trọng liêm chính học thuật khi tự động giải và đưa nguyên khung đáp án capstone cho sinh viên nộp bài thay vì hướng dẫn Socratic.
   - *Hậu quả:* Giảng viên coi AI là công cụ "tiếp tay gian lận" và thiếu cơ chế chuyển giao (Handoff) cho Lab Coach khi AI không chắc chắn.

---

## PHẦN 2: CÁC PHƯƠNG ÁN XEM XÉT & PHÂN TÍCH ĐÁNH ĐỔI (TRADE-OFFS)

| Tiêu chí | Phương án A: Mở rộng đồng loạt (Aggressive Rollout) | Phương án B: SỬA TRƯỚC KHI MỞ RỘNG (Fix & Controlled Pilot) — **ĐỀ XUẤT** | Phương án C: Dừng dự án (Kill / Abandon) |
| :--- | :--- | :--- | :--- |
| **Mô tả hành động** | Bỏ qua các lỗi trích dẫn, tiếp tục onboard thêm 10 trường để chạy đua số lượng user và login. | **Đóng băng việc ký trường mới trong 30 ngày**, dồn toàn lực sửa Code Gate trích dẫn 100%, thiết lập nút Handoff có kiểm soát, sau đó mới thử nghiệm trên 2 lớp học (120 SV). | Dừng phát triển VLearn AI Tutor, chuyển nguồn lực sang dự án khác do rủi ro liêm chính học thuật. |
| **Ưu điểm** | Tăng nhanh chỉ số Activity (login, số câu hỏi), tạo ấn tượng tăng trưởng ban đầu. | Trị đúng nguyên nhân gốc, khôi phục niềm tin học thuật (Trust), bảo vệ Gross Margin $\ge 58\%$, giảm thiểu rủi ro pháp lý/uy tín trường. | Cắt lỗ chi phí API và hạ tầng ngay lập tức. |
| **Nhược điểm & Đánh đổi** | Rủi ro sập niềm tin giáo dục, vi phạm Luật R-01 (Partner Activation <50%), sinh viên tẩy chay vì ảo giác. | Chấp nhận làm chậm tiến độ thương mại hóa trong 30 ngày; chịu áp lực giải trình với ban điều hành về việc tạm dừng bán hàng. | Lãng phí toàn bộ thành quả nghiên cứu và tích hợp từ Day 20 đến Day 27. |
| **Mức độ khả thi** | Rất nguy hiểm (High Risk). | **Khả thi cao (High Feasibility)** — vì giải pháp kỹ thuật sửa quote bằng Code Gate là giải pháp rẻ nhất đã kiểm chứng ở Day 21. | Không phù hợp bối cảnh. |

---

## PHẦN 3: ĐỀ XUẤT QUYẾT ĐỊNH & CƠ SỞ LẬP LUẬN (RECOMMENDATION & RATIONALE)

### 3.1. Quyết định chính thức: **SỬA (PIVOT / FIX TRƯỚC KHI MỞ RỘNG)**
Nhóm thống nhất **tạm dừng mở rộng quy mô (Freeze Sales/Expansion)** trong 30 ngày tới. Toàn bộ nguồn lực kỹ thuật và sản phẩm tập trung vào việc vượt qua **Cổng 30 ngày (Gate 1)** trước khi kích hoạt giai đoạn thử nghiệm tiếp theo.

### 3.2. Cơ sở lập luận (Rationale)
1. **Sửa ở tầng Code là đòn bẩy rẻ nhất và kiểm chứng được ngay:** Theo kết luận Day 20–21, 10/13 lỗi trích dẫn hoàn toàn bắt được bằng rule Python `quote_verbatim` liên tiếp từ kết quả retrieval mà không tốn thêm chi phí gọi LLM Judge.
2. **Tuân thủ kỷ luật vận hành Day 26 (Luật dừng R-01 & R-03):** Nếu cố tình triển khai khi Partner Activation Rate $<50\%$ và Reach $<10\%$, dự án sẽ chịu tổn thất lớn về chi phí token (vượt ngưỡng 1.800 đ/session) và hủy hoại mối quan hệ với trường đối tác.
3. **Phân chia lại công việc chuẩn xác theo Khung Mollick (Jagged Frontier):**
   - *Người giữ quyền (Giảng viên / Lab Coach):* Giữ quyền quyết định học thuật, duyệt tài liệu nguồn và giải quyết các ca ngoại lệ được handoff.
   - *AI hỗ trợ, người kiểm (Sinh viên + AI Tutor):* AI gợi mở từng bước + trích dẫn link nguồn; Sinh viên bắt buộc click link đối soát trước khi làm bài.
   - *AI tự động (Rủi ro thấp):* Chỉ tự động từ chối câu hỏi out-of-scope và format JSON contract.

---

## PHẦN 4: KẾ HOẠCH THỰC THI & DASHBOARD HÀNH ĐỘNG V2

### 4.1. Thiết kế lại Quy trình: AS-IS vs TO-BE

```
[QUY TRÌNH HIỆN TẠI: AS-IS]
Sinh viên gặp bài khó ──> Tự hỏi AI riêng lẻ ──> AI trả lời chắp vá (40% lỗi cite) 
──> Sinh viên hoang mang / chép bừa ──> Giảng viên cấm AI ──> Quay lại mò tài liệu.
(Vô chủ - Không nguồn kiểm chứng - Không có handoff)

───────────────────────────────────────────────────────────────────────────────

[QUY TRÌNH THIẾT KẾ LẠI: TO-BE]
Sinh viên gặp bài khó ──> Mở widget VLearn (LMS) ──> AI gợi ý Socratic + Trích dẫn nguyên văn (doc_id#section_id)
                                                                 │
                          ┌──────────────────────────────────────┴──────────────────────────────────────┐
                          ▼                                                                             ▼
                [Trích dẫn rõ ràng, hiểu bài]                                           [AI mâu thuẫn / Không chắc chắn]
                          │                                                                             │
                          ▼                                                                             ▼
              Sinh viên tự giải bài tập                                                  Chọn lý do + Bấm [Handoff Lab Coach]
             & Đính kèm trích dẫn nguồn                                                                 │
                                                                                                        ▼
                                                                                           Lab Coach nhận full context
                                                                                           giải đáp trong 2h & cập nhật Corpus
```

### 4.2. Lộ trình 3 Cổng quyết định dựa trên bằng chứng (Evidence Gates)

| Cổng (Gate) | Thời hạn | Trọng tâm hành động | Bằng chứng vật lý để chuyển pha | Kill Criteria (nếu FAIL) | Owner |
| :--- | :---: | :--- | :--- | :--- | :---: |
| **Cổng 1 (Gate 30d)** | Ngày 1–30 | **Sửa Kiến trúc tin cậy & Liêm chính** | Chạy lại 25 scenarios: Code gate `quote_verbatim` đạt **100% (25/25)**; adversarial `sc-20` đạt chuẩn Socratic (0% nộp hộ); Composite pass-rate $\ge 90\%$. | **Nếu Composite < 80% sau 30 ngày:** Dừng pilot, thiết kế lại cơ chế retrieval (thay BM25 bằng dense retrieval hoặc chunking lại corpus trước khi thử lại). | **Nguyễn Quang Sơn** |
| **Cổng 2 (Gate 60d)** | Ngày 31–60 | **Tích hợp Workflow & Pilot 2 lớp học. Đo baseline thật Self-Resolved từ tuần đầu go-live.** | Telemetry thật trên 120 SV: Tỷ lệ trích dẫn hợp lệ $\ge 95\%$; Tỷ lệ tự giải quyết đạt $\ge 75\%$; SLA Lab Coach phản hồi handoff $\le 2$ giờ. | **Nếu Self-Resolved < 50% sau 60 ngày:** Redesign phương pháp Socratic hoặc rút ngắn Friction để hạ ngưỡng vào. | **Phạm Tiến Hưng** |
| **Cổng 3 (Gate 90d)** | Ngày 61–90 | **Adoption thực tế & Đưa vào đề cương. Bắt đầu onboard 3–5 trường mới từ ngày 60** (sau Gate 2 PASS) để đạt ≥70% trên ≥10 trường. | Đạt North Star: $\ge 70\%$ trong ≥10 trường go-live có $\ge 20$ SV active; Tỷ lệ phàn nàn ảo giác $<3\%$; Gross Margin sau 25% rev-share đạt $\ge 58\%$. | **Nếu GM < 45% sau 2 vòng tối ưu & Reach < 10% sau 60 ngày go-live:** KILL dừng phân phối B2B2C (Luật R-05). | **Hưng & Sơn** |

### 4.3. Bảng chỉ số Dashboard Hành Động v2 (Tích hợp [dashboard_hanh_dong_v2.xlsx](file:///e:/Classroom/Code/Codelabs/Day28_Track01_PhamTIenHung/dashboard/dashboard_hanh_dong_v2.xlsx))

1. **Product Metric:** *Tỷ lệ trích dẫn nguyên văn chuẩn xác & còn hiệu lực (Fresh Citation Integrity Rate)*  
   - Baseline: **60.0%** (Day 20–21) $\rightarrow$ Target: **$\ge 95.0\%$**.  
   - Owner: Nguyễn Quang Sơn.  
   - Trigger khi xấu ($<90\%$): Tự động chặn output chắp vá, cắm cờ cảnh báo [Tài liệu cũ], báo động Lab Coach kiểm tra giáo trình trong 24h.
2. **Workflow Metric:** *Tỷ lệ tự giải quyết có kiểm soát chất lượng (Qualified Self-Resolved Rate)*  
   - Baseline: **~35%** *(ước tính thiết kế — chưa có LTI Event Log thật; sẽ đo chính thức từ tuần đầu Gate 2 làm mốc khởi điểm)* $\rightarrow$ Target: **$\ge 75.0\%$**.  
   - Owner: Phạm Tiến Hưng.  
   - Trigger khi xấu ($<60\%$): Tổ chức đối soát 24h giữa Product Lead và Lab Coach, trích xuất top 20 ca bị escalate để cập nhật Corpus.
3. **Behavior/Speed Metric:** *Thời gian kích hoạt sinh viên đầu tiên (TTF-End-User)*  
   - Baseline: **10 ngày** $\rightarrow$ Target: **$\le 7$ ngày** (Luật R-02).  
   - Owner: Nguyễn Quang Sơn (Hỗ trợ 1-1 với IT trường trong 48h nếu $>14$ ngày).
4. **FinOps Metric:** *Chi phí AI trên mỗi phiên giải quyết (Cost per Resolved Session)*  
   - Baseline: **1.450 đ** $\rightarrow$ Target: **$\le 1.200$ đ** (Luật R-04).  
   - Owner: Phạm Tiến Hưng (Bật prompt caching và model Flash nếu $>1.800$ đ).

---

## PHẦN 5: QUẢN TRỊ RỦI RO & TIẾP THU PHẢN BIỆN CHÉO

### 5.1. Hai điểm cải tiến vượt bậc so với bản v1 sau phiên Phản biện chéo
- **Cải tiến 1 (Trục Kiến trúc tin cậy):** Nhóm phản biện chỉ ra nguy cơ AI trích dẫn đúng nguyên văn tài liệu nhưng tài liệu đó đã lỗi thời. Nhóm chủ đã **bổ sung trường kiểm tra Timestamp / Freshness trong vòng 12 tháng** vào Corpus Manifest và Code Gate.
- **Cải tiến 2 (Trục Quy trình & Con người):** Nhóm phản biện cảnh báo nguy cơ sinh viên spam nút Handoff làm quá tải Lab Coach lúc 21h–23h. Nhóm chủ đã **thiết kế thêm cơ chế Friction:** Sinh viên bắt buộc đọc ít nhất 1 gợi ý và chọn lý do escalate thì nút Handoff mới được kích hoạt.

### 5.2. Ma trận rủi ro và Kế hoạch dự phòng (Fallback Plan)
- **Khi hệ thống LLM gặp sự cố / độ trễ $>5s$:** Tự động chuyển sang chế độ "Tìm kiếm mục lục giáo trình" và hiển thị số điện thoại hotline trợ giảng ca trực.
- **Cam kết Liêm chính học thuật (Academic Integrity):** Bất kỳ câu hỏi nào có dấu hiệu yêu cầu giải bài tập thi hoặc capstone sẽ được kích hoạt bộ lọc Guardrail từ chối thẳng thắn và hướng dẫn sinh viên về rubric ôn tập.
