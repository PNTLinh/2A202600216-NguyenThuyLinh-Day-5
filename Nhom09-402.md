# SPEC — AI Product Hackathon

**Nhóm:** 9
**Track:** ☐ VinFast · 🗹 Vinmec · ☐ VinUni-VinSchool · ☐ XanhSM · ☐ Open

**Problem statement (1 câu):** *Bệnh nhân đặt lịch khám trên app thường bị bối rối trước danh sách 30+ chuyên khoa. Họ mất thời gian tra cứu hoặc chọn đại, dẫn đến việc khám nhầm khoa và phải làm lại thủ tục. AI giúp chuyển đổi từ "Chọn khoa" sang "Kể triệu chứng*

---

## 1. AI Product Canvas (Hoàng Khải Minh -  2A202600159)

|   | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Trả lời** | *Bệnh nhân hoặc nguời có nhu cầu khám bệnh* | *Đề xuất sai khoa, sai lịch khám so với nhu cầu* | *Latency < 5s, hallucinate lịch khám* |

**Automation hay augmentation?** ☐ Automation · 🗹 Augmentation

Justify: *Augmentation — Vì đây là lĩnh vực y tế, tính chính xác là tuyệt đối. AI chỉ đóng vai trò "trợ lý" thu thập thông tin và gợi ý khung giờ/bác sĩ phù hợp. User phải là người kiểm tra lại thông tin cá nhân và nhấn nút "Xác nhận đặt lịch" để chịu trách nhiệm về thời gian đi khám của mình.*

**Learning signal:**

1. User correction đi vào đâu? 
    - Lưu vào Feedback log để dev kiểm tra và cải thiện AI agent
2. Product thu signal gì để biết tốt lên hay tệ đi?
   - Conversion Rate: Tỷ lệ từ lúc chat đến khi đặt lịch thành công.

    - Correction Rate: Tỷ lệ user phải bấm "Sửa lại" thông tin do AI tổng hợp.

    - Human Fallback Rate: Tỷ lệ user yêu cầu gặp nhân viên thật vì AI không giải quyết được.
3. Data thuộc loại nào? 
   
    🗹 User-specific (cung cấp thông tin khách hàng
)

    🗹 Domain-specific (cung cấp thông tin chuyên khoa về các bệnh thường gặp
)

    🗹 Real-time (trả lời ngay lập tức về danh sách bác sĩ trực thời gian đó
) 

    🗹 Human-judgment (chuyển tiếp yêu cầu đến tư vấn viên có chuyên môn) 

    ☐ Khác: ___
 

---

## 2. User Stories — 4 paths (Nguyễn Thùy Linh - 2A202600216)

Mỗi feature chính = 1 bảng. AI trả lời xong → chuyện gì xảy ra?

### Feature: *Sắp xếp lịch khám tự động*

**Trigger:** *VD: User nhắn hỏi triệu chứng → AI phân loại → AI hỏi lịch trống → AI đề xuất lịch khám.*

| Path | Câu hỏi thiết kế | Mô tả |
|------|-------------------|-------|
| Happy — AI đúng, tự tin | User thấy gì? Flow kết thúc ra sao? | *AI đề xuất đúng chuyên khoa, → người dùng xem và đưa ra lịch rảnh → AI cung cấp thông tin về lịch trống phù hợp → người dùng xác nhận → hệ thống ghi nhận* |
| Low-confidence — AI không chắc | System báo "không chắc" bằng cách nào? User quyết thế nào? | *AI hỏi người dùng thêm thông tin về các triệu chứng → người dùng trả lời → AI cập nhật lại đề xuất* |
| Failure — AI sai | User biết AI sai bằng cách nào? Recover ra sao? | *AI trả lời vòng vo → user chủ động bấm “Gặp tư vấn viên” → chuyển sang người tư vấn* |
| Correction — user sửa | User sửa bằng cách nào? Data đó đi vào đâu? | *AI đề xuất lịch → người dùng thấy chưa phù hợp → người dùng chọn lại dựa trên các lịch còn trống* |


---

## 3. Eval metrics + threshold (Nguyễn Hoàng Duy - 2A202600158)

**Optimize precision hay recall?** 
🗹 Precision · ☐ Recall

Tại sao? 

AI chỉ gợi ý khoa khi đủ chắc chắn. Thà bệnh nhân phải hỏi thêm còn hơn tự tin gợi ý sai khoa → bệnh nhân đi khám nhầm, mất tiền, mất thời gian, mất tin tưởng vào sản phẩm.
Nếu sai ngược lại thì chuyện gì xảy ra? *VD: Nếu chọn precision nhưng low recall → user không tìm thấy kết quả cần → bỏ dùng*

Nếu chọn ngược lại (Recall): AI cố gắng bắt hết mọi ca → gợi ý nhiều khoa hơn, chấp nhận có cái sai lẫn vào → bệnh nhân bị suggest nhầm khoa mà không hay → nguy hiểm hơn trong bối cảnh y tế.

| Metric | Threshold | Dừng khi |
|--------|-----------|---------------------|
| Accuracy đề xuất đúng chuyên khoa | ≥ 88% | < 75% trong 1 tuần |
| Tỉ lệ user xác nhận khoa mà không sửa | ≥ 80% | < 65% liên tục 3 ngày |
| Correction Rate (user sửa lại thông tin AI tổng hợp) | ≤ 15% | > 25% trong 1 tuần |
| Human Fallback Rate (chuyển nhân viên thật) | ≤ 10% | > 20% trong 1 tuần |
| Latency phản hồi | < 5s | > 8s trung bình trong ngày |

---

## 4. Top 3 failure modes (Nguyễn Triệu Gia Khánh - 2A202600225)

| # | Tình huống kích hoạt | Hậu quả | Giải pháp giảm thiểu |
| :---- | :---- | :---- | :---- |
| 1 | Nhầm lẫn triệu chứng nguy hiểm: Người dùng không biết mình có dấu hiệu bệnh nặng nhưng AI phân loại vào các khoa không ưu tiên. | Người bệnh chủ quan, trì hoãn việc điều trị khẩn cấp do quá tin tưởng vào sự điều hướng của máy. | Ưu tiên hiển thị cảnh báo cấp cứu khi phát hiện triệu chứng nguy hiểm, chuyển tiếp cho nhân viên tư vấn|
| 2 | Mâu thuẫn đặc điểm cá nhân: AI gợi ý chuyên khoa không khớp với tuổi hoặc giới tính của người bệnh. (ví dụ: gợi ý khoa người lớn cho trẻ em)| Khách hàng mất công sức di chuyển nhưng bị từ chối tiếp nhận tại viện, gây phiền hà và trải nghiệm dịch vụ kém. | Bắt buộc đối chiếu kết quả triệu chứng với thông tin hồ sơ (tuổi, giới tính) trước khi hiển thị gợi ý chuyên khoa cuối cùng. |
| 3 | Phản hồi sai lệch ngữ cảnh: Hệ thống sử dụng kịch bản chào hỏi hào hứng . | Gây cảm giác bị thiếu tôn trọng và vô cảm, làm sụt giảm nghiêm trọng uy tín và niềm tin vào thương hiệu. | Tùy chỉnh giọng văn AI trở nên điềm tĩnh và trung tính. Loại bỏ hoàn toàn các câu khen ngợi tự động trong giao diện y tế. |

---

## 5. ROI 3 kịch bản (Nguyễn Thùy Linh - 2A202600216)

|   | Conservative | Realistic | Optimistic |
|---|-------------|-----------|------------|
| **Assumption** | *80 user/ngày, 60% hài lòng* | *200 user/ngày, 80% hài lòng* | *500 user/ngày, 90% hài lòng* |
| **Cost** | *$20/ngày inference* | *$50/ngày* | *$125/ngày* |
| **Benefit** | *Giảm 2-3h support/ngày, chốt thêm 2 lịch hẹn* | *Giảm 10h/ngày, chốt thêm 15 lịch hẹn* | *Giảm 20h/ngày, chốt thêm 50 lịch hẹn* |
| **Net** | 20$  | 80$  | 200$  |

**Kill criteria:** *Khi nào nên dừng? VD: cost > benefit 3 tháng liên tục*

---

## 6. Mini AI spec (1 trang) (Nguyễn Thị Diệu Linh -  2A202600209)

Sản phẩm là một AI assistant hỗ trợ đặt lịch khám cho bệnh nhân tại hệ thống Vinmec, tập trung giải quyết vấn đề: người dùng không biết nên khám khoa nào, chọn bác sĩ nào, và đặt lịch ra sao cho phù hợp với tình trạng sức khỏe của mình. Hiện tại, người dùng thường phải tự tra cứu thông tin rời rạc hoặc hỏi tổng đài, gây mất thời gian và dễ chọn sai chuyên khoa. AI giúp rút ngắn quá trình này bằng cách hiểu triệu chứng → gợi ý đúng hướng → hỗ trợ đặt lịch nhanh hơn.

Đối tượng chính là bệnh nhân hoặc người có nhu cầu khám bệnh, đặc biệt là những người không có kiến thức y khoa để tự phân loại triệu chứng. Trong bối cảnh y tế có rủi ro cao, sản phẩm được thiết kế theo hướng augmentation (trợ lý hỗ trợ) chứ không tự động hoàn toàn. AI không thay thế quyết định của con người mà đóng vai trò thu thập thông tin, đưa ra gợi ý và để user xác nhận cuối cùng.

Về chức năng, AI thực hiện 3 nhiệm vụ chính:

Hiểu và phân loại triệu chứng từ input tự nhiên của người dùng.
Hỏi thêm thông tin khi chưa chắc chắn (clarification loop) để tăng độ chính xác trước khi đưa ra đề xuất.
Đề xuất chuyên khoa, bác sĩ và lịch khám phù hợp, dựa trên dữ liệu real-time (lịch trống) và thông tin cá nhân.

Chất lượng hệ thống được tối ưu theo hướng precision > recall. AI chỉ đưa ra đề xuất khi đủ tự tin, nếu không sẽ hỏi thêm hoặc chuyển sang tư vấn viên. Điều này giúp tránh các rủi ro nghiêm trọng như gợi ý sai chuyên khoa, có thể dẫn đến chẩn đoán sai hoặc trì hoãn điều trị. Đổi lại, hệ thống chấp nhận việc phải hỏi thêm câu hỏi hoặc tăng friction nhẹ trong flow.

Các rủi ro chính bao gồm:

Phân loại sai triệu chứng nghiêm trọng → được giảm thiểu bằng rule-based alert (flag triệu chứng nguy hiểm, ưu tiên cảnh báo).
Sai lệch ngữ cảnh hoặc thông tin cá nhân → kiểm tra chéo với profile user (tuổi, giới tính).
Hallucination về lịch khám → chỉ sử dụng dữ liệu real-time từ hệ thống backend, không để LLM tự sinh.

Hệ thống có cơ chế fallback sang con người khi:

AI không đủ confidence
User yêu cầu trực tiếp
Phát hiện case nhạy cảm/khẩn cấp

Về data flywheel, sản phẩm liên tục học từ hành vi người dùng:

Khi user sửa lại chuyên khoa hoặc thông tin → log lại thành correction data
Khi user bỏ flow hoặc gọi nhân viên → ghi nhận failure case
Khi user đặt lịch thành công → ghi nhận positive signal

Các tín hiệu này được dùng để:

Improve system prompt / logic hỏi thêm
phân loại triệu chứng
Tối ưu decision khi nào nên hỏi thêm vs đề xuất vs fallback

Theo thời gian, hệ thống sẽ:
→ Hỏi ít hơn nhưng chính xác hơn
→ Giảm tỷ lệ sửa (correction rate)
→ Tăng tỷ lệ chuyển đổi đặt lịch (conversion rate)

Tóm lại, đây là một AI product mang tính high-trust domain, thiết kế theo nguyên tắc: “Không chắc thì hỏi thêm, không đủ chắc thì không trả lời, luôn cho người dùng quyền kiểm soát cuối cùng