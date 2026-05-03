---
title: "Proposal"
date: "2026-03-09"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Lexi - The AI Tutor That Never Judges

---

## 1. Tổng quan dự án

### Tại sao hầu hết người học tiếng Anh ở Việt Nam không thể nói thành thạo?

Một nghịch lý quen thuộc trong việc học tiếng Anh tại Việt Nam: nhiều người nắm vững ngữ pháp, làm bài thi viết trôi chảy, nhưng lại “đứng hình” khi cần giao tiếp thực tế. Vấn đề không nằm ở năng lực, mà nằm trong các rào cản sau.

1. **Thiếu môi trường luyện nói thực sự** — Khó tìm được người để nói chuyện thường xuyên
2. **Sợ mắc lỗi** — Tâm lý xấu hổ khiến người học né tránh giao tiếp, dù biết rằng mắc lỗi mới học được
3. **Từ vựng học xong rồi quên** — Không có hệ thống ôn tập, 80% từ mới biến mất trong vòng 30 ngày
4. **Rào cản về chi phí** — Gia sư chất lượng thường cao không hề dễ tiếp cận với đa số người học.


### Điều gì xảy ra khi người học có thể luyện nói mà không lo bị phán xét?

Hãy hình dung một người học có nền tảng ngữ pháp vững chắc, nhưng mỗi khi cần nói tiếng Anh ở nơi công cộng thì lo lắng, ngại ngùng. Dù đã bỏ tiền và thời gian học nhiều năm, họ vẫn không thể bắt đầu một cuộc trò chuyện đơn giản mà không bị căng thẳng.

Với một AI Tutor có thể luyện nói bất kỳ lúc nào, không phán xét, không ngại lặp lại với chi phí thấp hơn nhiều so với gia sư truyền thống.

### Lexi là gì?

**Lexi** là nền tảng học tiếng Anh ứng dụng AI, được thiết kế để giải quyết một khoảng trống lớn mà các phương pháp học truyền thống thường bỏ qua: **luyện nói đủ nhiều, đủ đều, mà không sợ mắc lỗi**.

Thay vì cố gắng thay thế gia sư, Lexi tập trung vào những gì con người khó làm tốt: luôn sẵn sàng 24/7, kiên nhẫn không giới hạn, và tạo môi trường học tập không áp lực.

**Bốn lợi thế cốt lõi:**
- **Học bất cứ lúc nào** — Không cần đặt lịch, bạn có thể luyện tập ngay cả lúc 2 giờ sáng
- **Phản hồi cá nhân hóa** — Góp ý chính xác, phù hợp với trình độ và điểm yếu của từng người học
- **Không gian an toàn để mắc lỗi** — Không áp lực, không phán xét, giúp bạn tự tin nói nhiều hơn
- **Tiết kiệm chi phí** — Hiệu quả tương đương nhiều giờ học với gia sư, nhưng với chi phí thấp hơn đáng kể

---

## 2. Mục tiêu dự án

### Mục tiêu sản phẩm

1. Ra mắt MVP đủ chức năng với AI Tutor và hệ thống quản lý từ vựng
2. Có 50–100 người dùng trải nghiệm thực sự nhận được phản hồi và đánh giá
3. Hoàn thành trong ngân sách AWS 200 USD

### Mục tiêu kỹ thuật

1. Tổ chức và triển khai backend Clean Architecture trên AWS Lambda với kiến trúc Serverless
2. Frontend dùng Next.js App Router với TypeScript và Shadcn/ui
3. Kết nối Amazon Bedrock cho AI hội thoại
4. Lưu trữ dữ liệu bằng Amazon DynamoDB
5. CI/CD pipeline cho kiểm thử và triển khai tự động

### Phạm vi và giới hạn

- Ưu tiên mọi tính năng MVP hoạt động được
- AI hội thoại ở mức đủ dùng, không yêu cầu hoàn hảo
- Hạ tầng đảm bảo ổn định cho giai đoạn phát triển
- Xây dựng trên nền tảng web, chưa triển khai trên mobile
- Kiểm thử tập trung vào các luông chính


---

## 3. Tính năng chính

### 1. Luyện nói với AI Tutor
- Giao diện chat tương tác, AI đóng vai đối tác hội thoại
- Nhập giọng nói (Amazon Transcribe) → AI xử lý → Phản hồi bằng giọng (Amazon Polly)
- Phản hồi chi tiết về ngữ pháp, phát âm, từ vựng và cách phát triển thêm
- Lưu lịch sử hội thoại để theo dõi tiến độ

### 2. Flashcard theo thuật toán SRS
- Tạo và ôn tập từ vựng theo lịch tự động
- Thuật toán SM-2 tính toán thời điểm ôn tập tối ưu
- Theo dõi tỷ lệ nhớ từng thẻ (easy / good / hard / again)
- Thống kê: tổng từ đã học, từ đến hạn ôn, mức độ thuộc bài

### 3. Bài học có cấu trúc
- Nội dung phân theo cấp CEFR (A1–C2)
- Giải thích ngữ pháp, từ vựng, cách dùng thực tế
- Ví dụ âm thanh và video từ người bản ngữ

### 4. Dashboard theo dõi tiến độ
- Thống kê: số giờ học, số từ tích lũy, tỷ lệ ghi nhớ
- Streak — chuỗi ngày học liên tiếp không bị gián đoạn
- Huy hiệu thành tích theo các mốc học tập
- Biểu đồ tiến độ theo thời gian

---

## 4. Kiến trúc giải pháp

### Tech stack và lý do lựa chọn

Stack được chọn theo một tiêu chí: **phù hợp nhất với timeline 8 tuần của một developer solo**.

| Tầng | Công nghệ | Lý do |
| :--- | :--- | :--- |
| **Frontend** | Next.js + TypeScript | Cân bằng tốc độ code với type safety; App Router đơn giản hóa routing và API |
| **Backend** | Python 3.12 + AWS Lambda | Serverless loại bỏ quản lý hạ tầng; Python có SDK mạnh và cú pháp gọn |
| **AI** | Amazon Bedrock Nova Lite | Hội thoại AI không cần chuyên môn ML; chi phí thấp hơn 90% so với model tùy chỉnh |
| **Giọng nói** | Amazon Transcribe + Polly | WebSocket streaming tạo trải nghiệm nói tự nhiên; giảm phức tạp nhờ dịch vụ managed |
| **Database** | DynamoDB single-table design | Scale tự động cùng Lambda; hiệu suất dự đoán được với pricing pay-per-use |
| **Auth** | Amazon Cognito | Auth managed sẵn, tích hợp đăng nhập OAuth, không cần tự xây |
| **Triển khai** | Amplify (frontend) + SAM (backend) | Pipeline tách biệt — frontend và backend có thể lặp lại độc lập |

**Những gì được chủ động loại bỏ:**
- Giám sát nâng cao (Sentry, PostHog) → CloudWatch cơ bản đủ cho MVP
- CI/CD phức tạp → GitHub Actions + Amplify đảm nhiệm đủ
- Test framework nặng → Chỉ unit test cho business logic cốt lõi

> Mọi lựa chọn đều hướng đến một mục tiêu: **ship nhanh, scale được, không lãng phí thời gian vào hạ tầng**.

### Kiến trúc hệ thống

![Lexi Architecture Diagram](/images/2-Proposal/lexi-architecture.jpeg)


---

## 5. Timeline

### Giai đoạn 1 — Học và chuẩn bị (Tuần 1–2)

| Tuần | Việc cần làm | Kết quả |
| :--- | :--- | :--- |
| **1** | Học AWS Serverless, Lambda, DynamoDB | Nắm vững các dịch vụ AWS nền tảng |
| **1** | Học Amazon Bedrock, Transcribe, Polly | Biết cách gắn AI và xử lý giọng nói |
| **2** | Học Clean Architecture trong Python | Có bản thiết kế backend |
| **2** | Thiết kế database schema và API spec | Sẵn sàng bắt đầu code |

### Giai đoạn 2 — Phát triển (Tuần 3–8)

| Tuần | Sprint | Công việc | Deliverable |
| :--- | :--- | :--- | :--- |
| **3–4** | Sprint 1 | Backend: Auth, API, Database | API endpoints chạy được |
| **3–4** | Sprint 1 | Frontend: UI components, layout cơ bản | Giao diện khung hoạt động |
| **5–6** | Sprint 2 | AI Tutor — kết nối Bedrock | Luồng luyện nói đầu cuối hoạt động |
| **5–6** | Sprint 2 | Flashcard — thuật toán SRS | Ôn tập flashcard chạy đúng |
| **7** | Sprint 3 | Voice — Transcribe + Polly | Nhập và xuất giọng nói |
| **7** | Sprint 3 | Dashboard — analytics, tiến độ | Trang theo dõi có dữ liệu thực |
| **8** | Sprint 4 | Kiểm thử, tối ưu, triển khai | MVP sẵn sàng demo |

### Các mốc quan trọng

- **Cuối tuần 2** — Thiết kế và chuẩn bị hoàn tất, sẵn sàng code
- **Cuối tuần 4** — Backend + Frontend cơ bản chạy được
- **Cuối tuần 6** — AI Tutor và Flashcard hoạt động đầy đủ
- **Cuối tuần 7** — Voice integration xong
- **Cuối tuần 8** — MVP lên production, sẵn sàng demo

---

## 6. Ngân sách

### Phân tích chi phí dựa trên kiến trúc hệ thống

Dựa trên thiết kế kiến trúc serverless và phân tích chi tiết từ tài liệu chính thức của AWS, đây là ước tính chi phí vận hành cho dự án Lexi:

### Chi phí vận hành theo các kịch bản sử dụng

**Kịch bản 1: Giai đoạn phát triển và thử nghiệm (100 người dùng)**
| Dịch vụ AWS | Chi phí ước tính/tháng | Cơ sở tính toán |
| :--- | :--- | :--- |
| **AWS Lambda** | $0.44 | 100.000 lượt gọi hàm, bộ nhớ 256MB, thời gian thực thi trung bình 1 giây |
| **Amazon DynamoDB** | $0.02 | Lưu trữ 10GB, 50.000 lượt đọc và 20.000 lượt ghi |
| **Amazon S3** | $1.28 | Lưu trữ 50GB cho file audio, 5.000 lượt upload và 5.000 lượt download |
| **Amazon API Gateway** | $0.19 | 50.000 request REST API, 100 giờ kết nối WebSocket |
| **Amazon Bedrock** | $5.00 | Sử dụng mô hình Nova Lite cho hội thoại AI với lượng tương tác vừa phải |
| **Amazon Translate** | $1.50 | Dịch 100.000 ký tự văn bản |
| **Amazon Comprehend** | $1.00 | Xử lý 10.000 đơn vị phân tích ngôn ngữ tự nhiên |
| **Amazon Polly** | $0.20 | Tổng hợp 50.000 ký tự thành giọng nói |
| **Amazon Transcribe** | $2.40 | Chuyển đổi 100 phút giọng nói thành văn bản |
| **Tổng chi phí** | **$12.03/tháng** | *Áp dụng free tier cho các dịch vụ đủ điều kiện* |

**Kịch bản 2: Tăng trưởng vừa phải (500 người dùng)**
| Dịch vụ AWS | Chi phí ước tính/tháng |
| :--- | :--- |
| AWS Lambda | $2.20 |
| Amazon DynamoDB | $0.10 |
| Amazon S3 | $6.40 |
| Amazon API Gateway | $0.95 |
| Các dịch vụ AI/ML | $50.00 |
| **Tổng chi phí** | **$59.65/tháng** |

**Kịch bản 3: Tăng trưởng cao (1.000+ người dùng)**
| Dịch vụ AWS | Chi phí ước tính/tháng |
| :--- | :--- |
| AWS Lambda | $4.40 |
| Amazon DynamoDB | $0.20 |
| Amazon S3 | $12.80 |
| Amazon API Gateway | $1.90 |
| Các dịch vụ AI/ML | $100.00 |
| **Tổng chi phí** | **$119.30/tháng** |

### Phân bổ ngân sách tổng thể $200

| Giai đoạn triển khai | Ngân sách phân bổ | Mục đích sử dụng |
| :--- | :--- | :--- |
| **Tháng 1-2: Phát triển** | $50 | Môi trường phát triển, testing tích hợp, prototype |
| **Tháng 3: Ra mắt MVP** | $50 | Triển khai production, testing người dùng thực tế |
| **Tháng 4-6: Tăng trưởng** | $100 | Mở rộng quy mô, tối ưu hiệu suất, thu thập phản hồi |
| **Dự phòng rủi ro** | $50 | Chi phí phát sinh, tối ưu hóa, scaling không dự tính |

### Chiến lược quản lý và tối ưu chi phí

#### 1. Tận dụng tối đa AWS Free Tier
- **AWS Lambda**: 1 triệu request/tháng miễn phí trong 12 tháng đầu
- **Amazon DynamoDB**: 25GB lưu trữ miễn phí vĩnh viễn
- **Amazon S3**: 5GB lưu trữ + 20.000 request GET + 2.000 request PUT miễn phí
- **Amazon API Gateway**: 1 triệu request REST API + 750.000 phút kết nối WebSocket miễn phí

#### 2. Kiểm soát chi phí dịch vụ AI/ML
- Ưu tiên sử dụng Amazon Bedrock Nova Lite với chi phí tối ưu
- Triển khai cơ chế caching cho các prompt và phản hồi thường xuyên
- Batch processing cho các tác vụ không yêu cầu real-time
- Thiết lập giới hạn sử dụng theo người dùng để tránh lạm dụng

#### 3. Giám sát và cảnh báo chi phí
- Cấu hình AWS Budgets với ngưỡng cảnh báo tại $50, $100, $150
- Thiết lập CloudWatch alarms cho từng dịch vụ chính
- Sử dụng AWS Cost Explorer để phân tích chi tiết hàng tuần
- Áp dụng cost allocation tags để theo dõi chi phí theo tính năng

#### 4. Tối ưu kiến trúc hệ thống
- Điều chỉnh bộ nhớ Lambda phù hợp với workload thực tế
- Sử dụng DynamoDB on-demand capacity mode cho workload không ổn định
- Áp dụng S3 lifecycle policies để chuyển file cũ sang storage class rẻ hơn
- Kích hoạt caching cho API Gateway với các response tĩnh

### Các chi phí bổ sung cần cân nhắc

1. **Data Transfer**: $0.09/GB cho traffic ra khỏi region (ước tính $5-20/tháng)
2. **CloudWatch Logs**: $0.50/GB cho lưu trữ và truy vấn logs (ước tính $2-10/tháng)
3. **AWS X-Ray**: $5.00/1 triệu request tracing (ước tính $5-15/tháng)
4. **AWS Support**: Developer Support plan $29/tháng (khuyến nghị cho môi trường production)

### Đánh giá khả năng thực hiện với ngân sách $200

Với tổng ngân sách $200 cho giai đoạn MVP, dự án Lexi-BE có khả năng:
- **Phát triển và thử nghiệm** trong 2 tháng đầu với chi phí kiểm soát
- **Ra mắt MVP** với 100 người dùng đầu tiên trong tháng thứ 3
- **Vận hành ổn định** trong 3-4 tháng tiếp theo với khả năng mở rộng
- **Dự phòng đủ** cho các tối ưu hóa và điều chỉnh cần thiết

**Nhận định quan trọng:** Chi phí cho các dịch vụ AI/ML (Bedrock, Transcribe, Polly, Comprehend) chiếm tỷ trọng lớn (~80%) trong tổng chi phí vận hành. Việc tối ưu hiệu quả sử dụng các dịch vụ này là yếu tố then chốt để đảm bảo dự án vận hành trong phạm vi ngân sách.

---

## 7. Đánh giá rủi ro

### Rủi ro kỹ thuật

| Rủi ro | Mức độ | Cách xử lý |
| :--- | :--- | :--- |
| AI phản hồi chậm (> 2s) | Cao | Tối ưu kích thước prompt, bật response streaming, cảnh báo qua CloudWatch |
| Transcribe / Polly lỗi | Trung bình | Fallback sang giao tiếp văn bản, graceful degradation, cache phản hồi phổ biến |
| Vượt ngân sách AWS | Cao | Giới hạn quota mỗi người dùng, theo dõi AWS Budgets hàng ngày, dùng Nova Lite để tiết kiệm |
| Hệ thống không chịu tải | Trung bình | Lambda auto-scale với provisioned concurrency, DynamoDB on-demand capacity |

### Rủi ro tiến độ

| Rủi ro | Mức độ | Cách xử lý |
| :--- | :--- | :--- |
| Mất nhiều thời gian học AWS | Cao | Học dịch vụ cốt lõi trước (Lambda, DynamoDB), cắt phạm vi nếu cần, làm prototype sớm |
| Tích hợp AI phức tạp hơn dự kiến | Cao | Build prototype Bedrock đơn giản trước, dùng mock response khi phát triển song song |
| Clean Architecture làm chậm tốc độ code | Trung bình | Áp dụng phiên bản đơn giản hóa, chỉ giữ ba lớp cốt lõi, hoãn các pattern phức tạp |

### Rủi ro sản phẩm

| Rủi ro | Mức độ | Cách xử lý |
| :--- | :--- | :--- |
| Phản hồi AI kém chất lượng | Cao | Tinh chỉnh prompt theo vòng lặp phản hồi, A/B test hiệu quả hội thoại |
| Chất lượng giọng nói không đủ tự nhiên | Thấp | Dùng giọng neural của Polly, cho phép người dùng chuyển sang text nếu cần |
| Lỗ hổng bảo mật | Cao | IAM theo nguyên tắc least privilege, mã hóa at-rest và in-transit, review định kỳ |

### Thứ tự ưu tiên xử lý rủi ro

1. **Hàng đầu** — Chất lượng và độ trễ AI (ảnh hưởng trực tiếp đến trải nghiệm nói)
2. **Cao** — Kiểm soát chi phí (đảm bảo dự án không chết giữa chừng)
3. **Trung bình** — Rủi ro tiến độ (để đảm bảo giao MVP đúng hạn)
4. **Thấp** — Cải tiến giọng nói và tính năng bổ sung (có thể trì hoãn nếu cần)

---

## 8. Chỉ số thành công

### Kỹ thuật
- ✅ MVP hoàn chỉnh, tất cả tính năng chính hoạt động
- ✅ Thời gian phản hồi < 2 giây
- ✅ Uptime 99%+
- ✅ Không có lỗi nghiêm trọng (critical)
- ✅ Clean Architecture được áp dụng đúng

### Người dùng
- ✅ 100+ người dùng beta
- ✅ Rating 4.0+/5.0
- ✅ Daily active rate ≥ 50%

### Năng lực cá nhân
- ✅ Nắm vững AWS Serverless
- ✅ Hiểu sâu Clean Architecture
- ✅ Thành thạo tích hợp AI/LLM
- ✅ Có thể tự triển khai production app độc lập

---

## 9. Kết luận

Lexi giải quyết đúng vấn đề mà các phương pháp truyền thống bỏ sót: **thiếu môi trường luyện nói đủ an toàn và đủ thường xuyên** để người học thực sự mở miệng.

Trong 8 tuần, dự án tập trung vào một MVP chức năng, ưu tiên giá trị người dùng thực hơn là độ hoàn thiện. Việc dùng AWS serverless và các dịch vụ AI managed giúp phát triển nhanh mà vẫn đảm bảo khả năng mở rộng về sau.

Khác với gia sư truyền thống — vốn bị giới hạn bởi thời gian và chi phí — Lexi giải quyết đúng phần khó nhất trong hành trình học nói: **luyện đủ nhiều, đủ đều, mà không sợ bị nhìn thấy khi mắc lỗi**.