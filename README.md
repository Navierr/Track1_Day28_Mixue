# Track 1 - Day 28: Decision Memo & Action Dashboard

## 👥 Bảng thành viên (§6.3 — bắt buộc)

| Tên | MSSV | Phần phụ trách | Góp ý cho nhóm bạn (Chặng 3) |
| :--- | :---: | :--- | :--- |
| **Phạm Tiến Hưng** | 2A202601800 | Chẩn đoán điểm nghẽn (5 câu hỏi + ADKAR); Thiết kế AS-IS/TO-BE; Dashboard Hành Động v1/v2; Memo quyết định (§6.4); Gate 2 Owner (Pilot & FinOps) | *(Nhóm phản biện: **Mixue\_02** )* **[Trục Hành động]** Lộ trình Gate 2 của nhóm bạn đã có Owner rõ và bằng chứng vật lý để chuyển pha, nhưng chưa thấy baseline thật của chỉ số Self-Resolved — nên ghi rõ đây là ước tính thiết kế và cam kết đo chính thức từ tuần đầu go-live để Kill Criteria có giá trị thực chất. **[Trục Chỉ số]** Dashboard thiếu chỉ số FinOps (chi phí/session); nếu không có ngưỡng trigger cắt token từ đầu, rất dễ vượt mức cho phép khi scale pilot. |
| **Nguyễn Quang Sơn** | 2A202601956 | Phân tích Kiến trúc tin cậy; Code Gate `quote_verbatim`; LTI 1.3 telemetry; Mollick 3 vùng; Gate 1 Owner (Trust Repair); cơ chế Handoff/Fallback/Freshness | *(Nhóm phản biện: **Mixue\_02** )* **[Trục Phạm vi]** Sản phẩm và người dùng chính được mô tả rõ, nhưng ranh giới 3 quy trình còn chồng lấn — cần tách bạch rõ quy trình nào AI tự xử lý và quy trình nào bắt buộc có con người kiểm tra trước khi output ra ngoài. **[Trục Framework]** ADKAR được áp dụng nhưng chưa thấy bằng chứng cứng (hard evidence) để kết luận người dùng đang kẹt ở tầng nào; nếu chỉ dựa trên quan sát định tính thì nguyên nhân gốc dễ bị suy diễn sai. |


---

## 📁 Cấu trúc Thư mục Nộp bài (§6.3)

```text
Day28_Track01_PhamTIenHung/
├── README.md                          # Báo cáo tổng hợp toàn diện theo §6.3 (Đủ 6 bước)
├── dashboard/
│   └── dashboard_hanh_dong_v2.xlsx    # Dashboard Hành Động bản v2 hoàn thiện sau kiểm tra chéo
├── memo/
│   └── memo_quyet_dinh.md             # Memo quyết định gồm 5 phần chuẩn hóa theo §6.4 & §6.5
└── v1/
    └── dashboard_hanh_dong_v1.xlsx    # Dashboard Hành Động bản v1 trước phản biện (để đối chiếu)
```

---

## 📌 NĂM MỤC TÓM TẮT CỐT LÕI (§6.3)

### Mục 2 — Phạm vi
- **1 Sản phẩm AI:** VLearn AI Tutor (trợ lý học thuật nhúng LMS qua LTI 1.3).
- **1 Nhóm người dùng chính:** Sinh viên đại học (Stakeholder liên đới: Giảng viên & Lab Coach — Người giữ quyền & tiếp nhận Handoff).
- **3 Quy trình:** (1) Tra cứu khái niệm & gợi mở Socratic theo giáo trình; (2) Đối chiếu nguồn `doc_id#section_id` + tự giải bài; (3) Báo lỗi & Handoff ca khó sang Lab Coach trực đêm (21h–23h).

### Mục 3 — Nguyên nhân gốc
1. **Gốc 1 — Kiến trúc tin cậy hỏng** *(Framework: ADKAR Desire + 5 câu hỏi Trục Tin cậy)*:  
   Sinh viên không tin câu trả lời vì **40% trích dẫn bị cắt xén/chắp vá** (quote không nguyên văn, ghép đoạn bằng `...`). Bằng chứng: `REPORT.md` Day 20–21 — `quote_verbatim` chỉ đạt **60% (15/25 câu)**; Composite Release Gate chỉ đạt **48%** → Quyết định chính thức **HOLD**.
2. **Gốc 2 — Quy trình đứt gãy & Rủi ro liêm chính** *(Framework: ADKAR Ability + Mollick Vùng 2→1)*:  
   Không có cơ chế Handoff khi AI không chắc chắn; AI vi phạm liêm chính khi giải hộ bài tập capstone. Bằng chứng: `sc-20` Day 20–21 — Tutor đưa khung đáp án hoàn chỉnh cho sinh viên nộp bài thay.

### Mục 4 — Cách làm mới (3 thay đổi bắt buộc)
1. **Nguồn kiểm chứng:** Mọi câu trả lời bắt buộc kèm link `doc_id#section_id` **nguyên văn liên tiếp** + **Timestamp ≤12 tháng** (tránh trích dẫn giáo trình hết hạn).
2. **Người chịu trách nhiệm:** Sinh viên → kết quả bài làm; Lab Coach → giải đáp ca Handoff; Giảng viên → duyệt Corpus và chấm điểm.
3. **Cách xử lý khi AI không chắc chắn:** Nút `[Handoff to Lab Coach]` có **Friction** (đọc ≥1 gợi ý + chọn lý do escalate), Lab Coach nhận full context trong ≤2 giờ.

### Mục 5 — Chỉ số (§6.4)

| Loại | Tên chỉ số | Baseline | Target | Nguồn dữ liệu | Owner |
| :--- | :--- | :---: | :---: | :--- | :---: |
| **Product Metric** | *Fresh Citation Integrity Rate* | **60%** *(Day 20–21: 15/25 câu)* | **≥95%** *(Gate 1)* | Code Check Runner (eval-kit) trên telemetry log | **Nguyễn Quang Sơn** |
| **Workflow Metric** | *Qualified Self-Resolved Rate* | **~35%** *(ước tính thiết kế — sẽ đo tuần đầu Gate 2)* | **≥75%** *(Gate 2)* | LTI 1.3 Event Log Canvas/Moodle | **Phạm Tiến Hưng** |

### Mục 6 — Quyết định
- **Quyết định:** **SỬA (PIVOT / FIX TRƯỚC KHI MỞ RỘNG)** — Đóng băng onboard trường mới trong 30 ngày, dồn toàn lực vượt Gate 1.
- **Lý do một câu:** Composite Release Gate chỉ đạt 48% và Gartner-Lite cho thấy 4/5 điều kiện sẵn sàng THIẾU — mở rộng khi chưa đủ tin cậy sẽ hủy hoại niềm tin học thuật.
- **2 thay đổi so với v1 sau phản biện chéo:**
  1. Bổ sung điều kiện **Freshness / Timestamp ≤12 tháng** vào Code Gate (tránh trích dẫn giáo trình cũ).
  2. Bổ sung **cơ chế Friction chống spam Handoff** (đọc ≥1 gợi ý + gắn tag lý do trước khi nút kích hoạt).

---

## 🎯 BƯỚC 1: CHỌN MỘT VẤN ĐỀ ĐỦ CỤ THỂ — KHÓA PHẠM VI (§4.1)

### 1.1. Bốn thành tố cốt lõi
- **Product (Sản phẩm AI):** **VLearn AI Tutor** — Trợ lý học thuật nhúng trực tiếp vào LMS (Moodle/Canvas) qua chuẩn IMS LTI 1.3, giải quyết điểm đau thiếu trợ giảng ngoài giờ (đặc biệt khung giờ 21h–23h).
- **User (Nhóm người dùng chính):** **Sinh viên đại học** đang học các môn cơ sở ngành/kỹ thuật. *(Stakeholder liên đới: Giảng viên & Lab Coach đóng vai trò Người giữ quyền & tiếp nhận Handoff).*
- **Workflow (3 Quy trình liên quan):**
  1. *Quy trình 1:* Tra cứu khái niệm & hỏi đáp gợi ý giải bài tập theo giáo trình (Socratic Guidance).
  2. *Quy trình 2:* Đối chiếu nguồn trích dẫn nguyên văn (`doc_id#section_id`) với giáo trình và tự hoàn thành bài làm.
  3. *Quy trình 3:* Báo lỗi trích dẫn / nghi ngờ ảo giác $\rightarrow$ Handoff ca khó cho Lab Coach trực đêm (21h–23h).
- **Problem (Vấn đề quan sát và đo được):**  
  *Sinh viên ít quay lại sử dụng (Reach chỉ đạt 14%), vẫn quay lại tìm tài liệu thủ công hoặc hỏi bạn bè vì 40% trích dẫn nguồn không nguyên văn/chắp vá; Giảng viên e ngại cấm đoán vì sợ AI làm hộ bài tập.*

> 📌 **Một dòng phạm vi khóa cứng:**  
> *"Áp dụng VLearn AI Tutor cho Sinh viên đại học trong quy trình tra cứu và gợi mở giải bài tập theo giáo trình, nhằm giải quyết vấn đề sinh viên e ngại sử dụng và quay lại tìm tài liệu thủ công do trích dẫn nguồn thiếu chuẩn xác (40% lỗi cite) và quy trình thiếu cơ chế chuyển giao cho trợ giảng (Lab Coach) khi AI không chắc chắn."*

---

## 🔍 BƯỚC 2: CHẨN ĐOÁN TRƯỚC KHI CHỌN GIẢI PHÁP (§4.2)

### 2.1. Năm câu hỏi mở đầu chẩn đoán

| # | Trục | Câu hỏi | Nhận định thực tế tại VLearn AI Tutor | Mức độ |
| :---: | :--- | :--- | :--- | :---: |
| **01** | **Workflow** | AI có nằm trong công việc thật không? | AI mới được cấp tài khoản nhưng chưa nằm trong quy trình học chính thức; chưa có bước handoff sang trợ giảng khi gặp câu hỏi khó. | **GỐC** |
| **02** | **Con người** | Người dùng thiếu động lực, kiến thức hay khả năng? | Sinh viên sợ bị trừ điểm vì không đối chiếu được nguồn; Giảng viên lo ngại gian lận vì AI có xu hướng giải hộ bài tập. | **Liên quan** |
| **03** | **Sẵn sàng** | Dữ liệu, quyền truy cập và governance đã đủ chưa? | Corpus 18 tài liệu có sẵn nhưng thiếu cơ chế kiểm tra tính cập nhật (timestamp); chính sách liêm chính học thuật chưa rõ ràng. | **Liên quan** |
| **04** | **Tin cậy** | AI sai thì phát hiện, kiểm tra và xử lý thế nào? | Tỷ lệ trích dẫn nguyên văn chỉ đạt 60%; chưa có cơ chế phát hiện ảo giác tự động và nút báo lỗi/handoff cho con người. | **GỐC** |
| **05** | **Đo lường** | Chúng ta đang đo activity hay giá trị? | Hệ thống chỉ đếm lượt đăng nhập và số câu hỏi; chưa đo tỷ lệ giải quyết thành công không cần làm lại hay chất lượng trích dẫn. | **Liên quan** |

> **Kết luận sơ bộ:** Sửa **Độ tin cậy** và **Quy trình** trước khi tìm cách tăng mức độ sử dụng (Usage).

### 2.2. Gartner-Lite — Tổ chức đã sẵn sàng chưa?

| Hạng mục | Nhận định thực tế tại dự án | Kết quả |
| :--- | :--- | :---: |
| **Hướng đi (Direction)** | Mục tiêu rất rõ: giải quyết điểm đau thiếu trợ giảng khung giờ 21h–23h, tăng tỷ lệ hoàn thành bài tập đúng hạn. | **ĐẠT** |
| **Dữ liệu (Data Readiness)** | Đã có Corpus 18 tài liệu chuẩn, nhưng thiếu phân loại độ mới (Freshness/Timestamp) và kiểm soát section metadata. | **THIẾU** |
| **Governance** | Tiêu chuẩn Zero-PII đã cam kết (Day 22), nhưng quy định ranh giới bài tập được phép hỗ trợ chưa ban hành chính thức. | **THIẾU** |
| **Vận hành (Operations)** | Chưa có phân công Lab Coach trực tiếp nhận handoff; chưa có SLA phản hồi cho ca khó. | **THIẾU** |
| **Khả năng hấp thụ (Absorption)**| Người dùng chưa có kênh phản hồi lỗi trực tiếp; chưa có vòng lặp cập nhật lỗi vào Knowledge Base. | **THIẾU** |

> **Kết luận Gartner-Lite:** **Pilot có kiểm soát trên 2 lớp học để củng cố Mức sẵn sàng (Readiness) và Khả năng hấp thụ (Absorption); TUYỆT ĐỐI CHƯA rollout trên diện rộng.**

### 2.3. Mollick — Phân chia việc Người – AI (Jagged Frontier)

| Vùng | Nguyên tắc | Nội dung áp dụng vào VLearn AI Tutor |
| :--- | :--- | :--- |
| **Người làm (Giữ quyền)** | **Giữ quyền quyết định & Chịu trách nhiệm** | **Giảng viên & Lab Coach:** Thẩm định giáo trình chuẩn, chấm điểm, đánh giá ngoại lệ học thuật và trực tiếp giải đáp các ca khó được AI handoff sang. |
| **AI hỗ trợ** | **AI làm, người kiểm tra** | **AI Tutor + Sinh viên:** AI tìm kiếm trong 18 tài liệu, tóm tắt và đưa ra câu hỏi gợi mở từng bước (Socratic); Sinh viên bắt buộc bấm link trích dẫn đối chiếu giáo trình trước khi làm bài. |
| **AI tự động** | **Chỉ với tác vụ rõ & rủi ro thấp** | **Hệ thống AI:** Tự động format contract JSON, tự động từ chối câu hỏi ngoài phạm vi môn học (out-of-scope), tự động chặn từ khóa yêu cầu "giải hộ đề thi/capstone". |

### 2.4. ADKAR — Người dùng đang kẹt ở đâu?

| Bước | Định nghĩa | Nhận định tại VLearn AI Tutor | Trạng thái |
| :--- | :--- | :--- | :---: |
| **Awareness** | Hiểu vì sao công việc cần thay đổi | Sinh viên biết có AI trên LMS nhưng chưa rõ AI được phép hỗ trợ đến mức nào mà không bị tính là gian lận. | **NGHẼN** |
| **Desire** | Có lý do để muốn thay đổi | **Điểm nghẽn cốt lõi:** Sinh viên ngại tin câu trả lời vì 40% trích dẫn bị cắt xén, không tìm thấy đoạn văn trong sách; sợ bị điểm liệt nên không dám dùng. | **NGHẼN NẶNG (GỐC)** |
| **Knowledge** | Biết cách làm | Chưa được hướng dẫn cách đặt câu hỏi theo ngữ cảnh slide và cách đọc link `doc_id#section_id`. | Cần làm |
| **Ability** | Làm được trong thực tế | **Quy trình bị đứt:** Khi AI trả lời mâu thuẫn hoặc không chắc chắn, sinh viên không có nút bấm để chuyển tiếp cho trợ giảng (Lab Coach) hỗ trợ. | **NGHẼN NẶNG (GỐC)** |
| **Reinforcement**| Duy trì hành vi mới | Chưa có ghi nhận điểm chuyên cần tự học và chưa có cơ chế tuyên dương giảng viên tích cực dùng AI. | Cần làm |

> **Kết luận ADKAR:** Mở lớp đào tạo (Knowledge) là hoàn toàn vô nghĩa nếu không giải quyết được **nỗi sợ thiếu tin cậy (Desire)** và **thiếu công cụ chuyển giao ca khó (Ability)**.

### 2.5. Bằng chứng thực nghiệm (Hard Evidence) & Chốt 2 Nguyên nhân gốc
- **Bằng chứng định lượng Day 20–21 (`REPORT.md`):** Tỷ lệ `quote_verbatim` đạt **60.0% (15/25 câu)**; có tới **10/25 câu (40.0%)** vi phạm nghiêm trọng vì chắp vá bằng dấu `...` (`sc-04, 05, 08, 13, 15`). Composite Release Gate chỉ đạt **48.0%**, dẫn tới quyết định chính thức là **HOLD (Chưa được ship)**.
- **Bằng chứng rủi ro liêm chính:** Kịch bản đối kháng `sc-20`: AI tự động đưa khung giải bài tập capstone thay vì gợi ý sư phạm.
- **Bài học thực tế:** *Morgan Stanley* — Độ tin cậy và tuân thủ phải đi trước khi mở rộng quy mô.

> 🎯 **Chốt 2 Nguyên nhân gốc có căn cứ:**
> 1. **Gốc 1 (Kiến trúc tin cậy):** 40% trích dẫn không nguyên văn làm mất niềm tin học thuật của sinh viên.
> 2. **Gốc 2 (Quy trình & Liêm chính):** Thiếu ranh giới sư phạm (nguy cơ giải hộ bài tập) và thiếu điểm chuyển giao (Handoff) cho Lab Coach khi AI không chắc chắn.

---

## 🔄 BƯỚC 3: THIẾT KẾ CÁCH LÀM MỚI (§4.3)

### 3.1. Bảng đối chiếu quy trình: TRƯỚC (AS-IS) vs SAU (TO-BE)

| Giai đoạn | TRƯỚC (AS-IS) | SAU (TO-BE) |
| :--- | :--- | :--- |
| **1. Tiếp cận** | **Tìm file / hỏi bạn:** Sinh viên tìm tài liệu rời rạc trên Google Drive/Zalo; khi gặp bài khó thì chờ hôm sau hỏi trợ giảng (bế tắc khung 21h–23h). | **Hỏi AI theo quy trình:** Mở widget VLearn nhúng trực tiếp trong LMS Canvas/Moodle, đặt câu hỏi kèm ngữ cảnh slide bài giảng. |
| **2. Phản hồi** | **Nhận câu trả lời chắp vá:** AI trả lời chung chung hoặc đưa trích dẫn chắp vá (40% lỗi cite), không biết nằm ở trang sách nào. | **Xem nguồn & Ngày cập nhật:** AI gợi mở từng bước (Socratic), đính kèm link trích dẫn nguyên văn (`doc_id#section_id`) và timestamp cập nhật $\le 12$ tháng. |
| **3. Kiểm chứng** | **Vô chủ & Hoang mang:** Không có ai chịu trách nhiệm; sinh viên sợ bị trừ điểm do trích dẫn sai hoặc chép nguyên văn vi phạm liêm chính. | **Kiểm tra có trách nhiệm:** Sinh viên bắt buộc bấm link đối chiếu giáo trình và tự làm bài (Sinh viên chịu trách nhiệm bài nộp; Lab Coach kiểm duyệt nguồn). |
| **4. Xử lý ngoại lệ**| **Bỏ dở bài tập:** Khi AI bị ảo giác hoặc trả lời sai, sinh viên không biết hỏi ai, đành bỏ dở bài tập hoặc học vẹt. | **Dùng / Báo lỗi có Handoff:** Nếu câu trả lời khó hiểu hoặc mâu thuẫn $\rightarrow$ Bấm nút `[Handoff to Lab Coach]`. Lab Coach nhận full ngữ cảnh giải đáp trong 2h. |

### 3.2. Ba thay đổi bắt buộc
1. **Nguồn kiểm chứng:** Mọi câu trả lời bắt buộc gắn link `doc_id#section_id` đối chiếu trực tiếp đoạn văn giáo trình kèm **Timestamp trong vòng 12 tháng**.
2. **Người chịu trách nhiệm:** Phân định minh bạch — Sinh viên chịu trách nhiệm kết quả bài làm; Lab Coach chịu trách nhiệm tri thức giải đáp ca khó; Giảng viên duyệt Corpus và chấm điểm.
3. **Cách xử lý khi AI không chắc chắn:** Nút bấm `[Handoff to Lab Coach]` có cơ chế **Friction** (sinh viên đọc ít nhất 1 gợi ý và gắn tag lý do), tự động đẩy ngữ cảnh sang dashboard của trợ giảng.

### 3.3. Chọn giải pháp theo đúng nguyên nhân

| Nguyên nhân nằm ở | Khung giải pháp tương ứng | Hành động cụ thể tại dự án VLearn |
| :--- | :--- | :--- |
| **Chia việc chưa rõ** | **Mollick** | Thiết lập 3 vùng: Giảng viên giữ quyền chấm & duyệt nguồn; AI hỗ trợ tìm & gợi ý; Sinh viên tự giải. |
| **Người dùng e ngại** | **ADKAR** | Xử lý điểm nghẽn `Desire` bằng cách đảm bảo 100% trích dẫn nguyên văn; xử lý `Ability` bằng nút Handoff. |
| **Tổ chức chưa sẵn sàng** | **Gartner-Lite** | Giới hạn thử nghiệm (Pilot) trong 2 lớp học (120 SV); củng cố Data Readiness & SLA vận hành trước khi scale. |
| **Độ tin cậy chưa đạt** | **Kiểm soát & Tin cậy** | Ép rule Code Gate `quote_verbatim` liên tiếp từ retrieval; kiểm tra mẫu 10% log tương tác hàng tuần. |
| **Cách đo lệch lạc** | **Đo lường ra quyết định** | Chuyển từ đếm lượt đăng nhập (Activity) sang đo *Tỷ lệ trích dẫn nguyên văn* và *Tỷ lệ tự giải quyết thành công*. |

### 3.4. Kiến trúc tin cậy (5 tầng) và Bốn điều kiện sẵn sàng

$$\text{Nguồn (18 docs + Timestamp)} \longrightarrow \text{Trích nguồn (Verbatim 100\%)} \longrightarrow \text{QA mẫu (10\%/tuần)} \longrightarrow \text{Chuyển người (Handoff + Friction)} \longrightarrow \text{Phản hồi (Cập nhật Corpus)}$$

**Bốn điều kiện về mức sẵn sàng:**
1. *Nguồn dữ liệu:* Giảng viên cốt cán làm Data Owner; rà soát cập nhật tài liệu mỗi đầu học kỳ.
2. *Quyền truy cập:* Xác thực bảo mật qua IMS LTI 1.3; phân cấp sinh viên / trợ giảng; cam kết Zero-PII.
3. *Phạm vi sử dụng AI:* Cấm AI giải hộ bài tập capstone/đề thi; chỉ được phép đóng vai trò gợi mở Socratic.
4. *Chi phí & Vận hành:* Định mức chi phí token $\le 1.200$ đ/session; SLA Lab Coach trực đêm phản hồi $\le 2$ giờ.

---

## 🚦 BƯỚC 4: LỘ TRÌNH 30–60–90 NGÀY DỰA TRÊN BẰNG CHỨNG (§4.4)

> ⚠️ *Nguyên tắc cốt lõi: Mỗi giai đoạn là một Cổng quyết định (Evidence Gate). Gate chỉ được thông qua khi các bằng chứng chất lượng, hành vi và giá trị đạt mục tiêu — KHÔNG PHẢI KHI HẾT SỐ NGÀY. Gate FAIL có Kill Criteria rõ ràng.*

| Giai đoạn | Mục tiêu cổng | Trọng tâm công việc | Bằng chứng vật lý để thông qua Gate | Kill Criteria (nếu FAIL) | Phụ trách (Owner) |
| :---: | :--- | :--- | :--- | :--- | :---: |
| **0–30 ngày**<br>*(Gate 1)* | **Chứng minh vấn đề & Độ tin cậy** | - Sửa triệt để lỗi chắp vá trích dẫn ở tầng Code Gate.<br>- Bổ sung ràng buộc cấm giải hộ bài tập capstone.<br>- Gắn Timestamp cho 18 tài liệu corpus. | - `quote_verbatim` đạt **100% (25/25)**.<br>- `sc-20` đạt chuẩn Socratic (0% giải hộ).<br>- Composite Release Gate đạt **$\ge 90\%$**. | **Nếu Composite < 80% sau 30 ngày:** Dừng pilot, thiết kế lại cơ chế retrieval (thay BM25 bằng dense retrieval hoặc chunking lại corpus trước khi thử lại). | **Nguyễn Quang Sơn** |
| **31–60 ngày**<br>*(Gate 2)* | **Chứng minh chất lượng & Quy trình** | - Nhúng nút Handoff có Friction lên giao diện LMS.<br>- Thử nghiệm pilot trên 2 lớp học (120 SV).<br>- Thiết lập SLA trực ca 21h–23h cho Lab Coach.<br>- **Đo baseline thật của Self-Resolved Rate từ tuần đầu.** | - Tỷ lệ trích dẫn hợp lệ đạt **$\ge 95\%$**.<br>- Tỷ lệ tự giải quyết thành công đạt **$\ge 75\%$**.<br>- SLA Lab Coach phản hồi handoff **$\le 2$ giờ**. | **Nếu Self-Resolved < 50% sau 60 ngày:** Xem xét PIVOT — redesign phương pháp Socratic hoặc rút ngắn Friction để hạ ngưỡng vào. | **Phạm Tiến Hưng** |
| **61–90 ngày**<br>*(Gate 3)* | **Quyết định mở rộng & Chuẩn hóa** | - Đưa AI Tutor vào đề cương môn học chính thức (SOP).<br>- **Bắt đầu onboard 3–5 trường mới từ ngày 60 (sau Gate 2 PASS).** Đạt ≥70% trên ≥10 trường là điều kiện Gate 3.<br>- So sánh chi phí và giá trị nghiệp vụ. | - $\ge 70\%$ trường *(trong ≥10 trường go-live)* có $\ge 20$ SV active.<br>- Tỷ lệ phàn nàn ảo giác học thuật **$<3\%$**.<br>- Gross Margin sau 25% rev-share đạt **$\ge 58\%$**. | **Nếu GM < 45% sau 2 vòng tối ưu & Reach < 10% sau 60 ngày go-live:** KILL dừng phân phối B2B2C (theo Luật R-05 Day 26). | **Hưng & Sơn** |

---

## 📊 BƯỚC 5: ĐIỀN DASHBOARD HÀNH ĐỘNG — BẢN v1 (§4.5)

### 5.1. Phân biệt Activity Metric vs Decision Metric
- *Chỉ số dễ đo nhưng chưa đủ (Activity):* Số tài khoản được tạo (200 tài khoản), số lượt login, số câu hỏi hỏi AI $\rightarrow$ Không phản ánh giá trị học tập thực chất.
- *Chỉ số đủ mạnh để ra quyết định (Decision):* Tỷ lệ trích dẫn nguyên văn kiểm chứng được, tỷ lệ tự giải quyết không cần gọi trợ giảng, chi phí token trên phiên phụ đạo hoàn tất.

### 5.2. Bảng Dashboard Hành Động bản v1 (5 trường + hành động khi xấu)

| STT | Tầng đo lường | Loại chỉ số | Tên chỉ số | Baseline (Mốc đầu) | Target (Mục tiêu) | Nguồn dữ liệu | Phụ trách (Owner) | Hành động khi chỉ số xấu (Action Trigger) |
| :---: | :--- | :--- | :--- | :---: | :---: | :--- | :--- | :--- |
| **1** | **Tầng 4:** Chất lượng & Tin cậy | **Product Metric** | **Tỷ lệ trích dẫn nguyên văn chuẩn xác** | **60.0%**<br>*(Day 20-21: 15/25)* | **$\ge 95.0\%$**<br>*(sau 30 ngày)* | Code Check Runner trên tập telemetry log thật (eval-kit) | **Nguyễn Quang Sơn** | **Nếu $<90\%$:** Tự động chặn câu trả lời chắp vá, kích hoạt fallback chuyển hướng và thông báo nguồn đang cập nhật. |
| **2** | **Tầng 3:** Năng suất & Quy trình | **Workflow Metric** | **Tỷ lệ tự giải quyết qua AI (Self-Resolved Rate)** | **~35%** *(ước tính thiết kế — chưa có LTI Event Log thật; sẽ đo tuần đầu Gate 2 làm mốc chính thức)* | **$\ge 75.0\%$**<br>*(sau 60 ngày)* | Event Log trên widget LTI 1.3 Canvas/Moodle (bắt đầu thu từ ngày go-live pilot) | **Phạm Tiến Hưng** | **Nếu $<60\%$:** Họp đối soát 24h giữa Product Lead & Lab Coach, trích xuất 20 ca bị escalate nhiều nhất để bổ sung Corpus. |
| **3** | **Tầng 2:** Hành vi & Tốc độ | **Workflow Metric** | **Thời gian từ khi cấp LMS đến SV đầu tiên dùng (TTF)** | **10 ngày**<br>*(Day 26)* | **$\le 7$ ngày**<br>*(Day 26 Target)* | Audit log tích hợp LTI trên LMS trường đối tác | **Nguyễn Quang Sơn** | **Nếu $>14$ ngày (Luật R-02):** Kỹ sư Sơn trực tiếp cấu hình 1-1 với IT trường trong 48h để đưa nút AI lên nav-bar môn học. |
| **4** | **Tầng 5:** Giá trị & FinOps | **Product Metric** | **Chi phí AI trên mỗi phiên giải quyết** | **1.450 đ**<br>*(Day 25-26)* | **$\le 1.200$ đ**<br>*(ngưỡng tối ưu)* | Billing telemetry LangSmith & OpenAI/Vertex API | **Phạm Tiến Hưng** | **Nếu $>1.800$ đ (Luật R-04):** Bật prompt caching cho toàn bộ đề cương, chuyển 70% truy vấn tra cứu sang model Flash. |

- 📄 *File tính Excel v1:* [v1/dashboard_hanh_dong_v1.xlsx](file:///e:/Classroom/Code/Codelabs/Day28_Track01_PhamTIenHung/v1/dashboard_hanh_dong_v1.xlsx)

---

## 🤝 BƯỚC 6: KIỂM TRA CHÉO & HOÀN THIỆN BẢN v2 + MEMO (§4.6)

### 6.1. Phản biện chéo theo Bốn trục

| Trục phản biện | Câu hỏi thẩm định | Kết quả tự soi & Đối thoại nhóm phản biện | Đánh giá |
| :--- | :--- | :--- | :---: |
| **Phạm vi** | Sản phẩm, người dùng và 2–4 quy trình đã rõ chưa? | Đã khóa cứng VLearn AI Tutor, người dùng chính là Sinh viên đại học, 3 quy trình học tập - kiểm chứng - handoff được mô tả chi tiết. | **ĐẠT** |
| **Framework** | Gartner-Lite, Mollick, ADKAR có thật sự tìm nguyên nhân? | Sử dụng rất thực chất: ADKAR bác bỏ việc mở lớp đào tạo; Mollick phân định rõ vùng cấm giải hộ bài tập; Gartner-Lite dẫn thẳng đến quyết định pilot nhỏ thay vì rollout rộng. Cả ba framework hội tụ đến cùng 2 nguyên nhân gốc (không chia cơ học). | **ĐẠT** |
| **Chỉ số** | Có đo quy trình, chất lượng, giá trị và nguồn dữ liệu không? | Đo đủ từ Tầng 2 (TTF) → Tầng 3 (Self-Resolved) → Tầng 4 (Citation) → Tầng 5 (Cost/Job), nguồn dữ liệu lấy từ log máy đo thật. Baseline Self-Resolved ghi rõ là ước tính thiết kế, sẽ đo chính thức tuần đầu Gate 2. | **ĐẠT** |
| **Hành động** | Lộ trình, owner và cách xử lý khi chỉ số xấu đã rõ chưa? | Từng Cổng 30-60-90 ngày có owner (Sơn/Hưng), mọi chỉ số đều có trigger hành động khi rơi vào vùng đỏ. **Gate 1 và Gate 3 đã bổ sung Kill Criteria rõ ràng.** | **ĐẠT** |

### 6.2. Tiếp thu Ít nhất 2 Góp ý cụ thể và Cập nhật Bản v2
1. **Góp ý 1 (Từ Trục Kiến trúc tin cậy):** *"AI có thể trích dẫn nguyên văn nhưng từ một phiên bản giáo trình đã lỗi thời."*  
   $\rightarrow$ **Thay đổi trong Bản v2:** Nâng cấp thành *Fresh Citation Integrity Rate* — bổ sung điều kiện kiểm tra **Timestamp tài liệu $\le 12$ tháng** vào Corpus Manifest và Code Gate.
2. **Góp ý 2 (Từ Trục Quy trình & Con người):** *"Sinh viên có thể lười đọc gợi ý Socratic và spam nút Handoff làm quá tải Lab Coach."*  
   $\rightarrow$ **Thay đổi trong Bản v2:** Nâng cấp thành *Qualified Self-Resolved Rate* — bổ sung **cơ chế Friction**: Sinh viên bắt buộc đọc ít nhất 1 gợi ý và chọn lý do escalate trước khi nút Handoff được kích hoạt.

### 6.3. File Dashboard Hành Động bản v2 và Memo Quyết định
- 📄 **File Excel v2:** [dashboard/dashboard_hanh_dong_v2.xlsx](file:///e:/Classroom/Code/Codelabs/Day28_Track01_PhamTIenHung/dashboard/dashboard_hanh_dong_v2.xlsx)
- 📄 **Memo Quyết định (§6.4–6.5):** [memo/memo_quyet_dinh.md](file:///e:/Classroom/Code/Codelabs/Day28_Track01_PhamTIenHung/memo/memo_quyet_dinh.md)
  - **Quyết định cuối:** **SỬA (PIVOT / FIX TRƯỚC KHI MỞ RỘNG)**.
  - **Lý do:** Composite Gate 48%, Gartner-Lite 4/5 THIẾU — không mở rộng khi chưa đủ độ tin cậy.

---

## ✅ CHECKLIST TRƯỚC KHI NỘP (§6.6)

- [ ] **1.** Mở link repo ở cửa sổ ẩn danh (chưa đăng nhập) — vẫn xem được.
- [x] **2.** Tên repo đúng mẫu `Day28_Track01_PhamTIenHung` — đúng số track, không dấu, không khoảng trắng.
- [x] **3.** Có đủ 3 thứ: **dashboard v2** · **memo** · **dashboard v1** để đối chiếu.
- [x] **4.** README có bảng thành viên đủ 4 cột: **tên · MSSV · phần phụ trách · góp ý cho nhóm bạn** *(ô "góp ý" sẽ điền sau Chặng 3)*.
- [x] **5.** README có đủ 5 mục còn lại; số liệu (60%, 48%, ~35%, 1.450đ) khớp file dashboard.
- [x] **6.** Không có dữ liệu nội bộ nhạy cảm của doanh nghiệp trong file.
- [ ] **7.** Mọi thành viên đã dán CÙNG một link vào LMS của mình.

---

## 🎯 BỐN ĐIỀU MANG VỀ (§6.7)
1. **Phân biệt Usage, Deployment và Adoption** — Adoption chỉ bắt đầu khi Workflow, Owner và Handoff thay đổi.
2. **Chẩn đoán bằng Gartner-Lite, Mollick và ADKAR** — Không mặc định mọi vấn đề đều giải bằng đào tạo.
3. **Thiết kế lại quy trình và lộ trình 30–60–90** — Mỗi gate là bằng chứng, không phải lịch triển khai.
4. **Đo từ mức sử dụng đến chất lượng và giá trị** — Chỉ số không dẫn tới hành động là chỉ số vô nghĩa.

> *"Việc ứng dụng AI chỉ có ý nghĩa khi quy trình thay đổi và kết quả được đo bằng dữ liệu."*
