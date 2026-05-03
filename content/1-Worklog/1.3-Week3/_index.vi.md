---
title: "Worklog Tuần 3"
date: 2026-03-30
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Tuần 3: Backend Foundation & Authentication UI

**Ngày:** 30/03/2026 - 05/04/2026  
**Tổng thời gian:** ~40 giờ

### Mục tiêu Tuần 3:

* **Thiết kế Database Schema:** Phân tích access patterns, thiết kế DynamoDB single-table
* **Thiết kế API Specification:** Định nghĩa endpoints, viết OpenAPI spec
* **Tìm hiểu Cognito:** Cấu hình User Pool, OAuth providers, hosted UI
* **Xây dựng Auth UI:** Tạo login/signup pages với Next.js
* **Tích hợp Frontend-Backend:** Kết nối auth UI với Cognito
* **Tìm hiểu AI Services:** Nghiên cứu Bedrock, Transcribe, Polly
* **Hỗ trợ Backend:** Tham gia thảo luận về Clean Architecture và repository pattern

### Nội dung công việc:

| Ngày | Công việc | Trạng thái | Tài Liệu Tham Khảo |
| --- | --- | --- | --- |
| **Thứ 2** | **Phân tích Access Patterns & Thiết kế DynamoDB** <br> - Phân tích các access patterns của ứng dụng <br> - Thiết kế DynamoDB single-table design <br> - Xác định partition key, sort key, GSI <br> - Document schema với examples | ✅ Hoàn thành | [DynamoDB Design](https://www.alexdebrie.com/posts/dynamodb-single-table/) |
| **Thứ 3** | **Thiết kế API Specification** <br> - Định nghĩa tất cả endpoints (Auth, Profile, Flashcards, Sessions) <br> - Viết OpenAPI 3.0 specification <br> - Định nghĩa request/response schemas <br> - Thảo luận với team về API design | ✅ Hoàn thành | [OpenAPI Spec](https://swagger.io/specification/) |
| **Thứ 4** | **Cấu hình Amazon Cognito** <br> - Tạo User Pool với email/password authentication <br> - Cấu hình password policy và MFA <br> - Setup Google OAuth provider <br> - Tạo App Client và hosted UI <br> - Test signup/login flow | ✅ Hoàn thành | [Cognito User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) |
| **Thứ 5** | **Xây dựng Auth Pages** <br> - Tạo `/app/auth/login/page.tsx` với form validation <br> - Tạo `/app/auth/signup/page.tsx` với password confirmation <br> - Styling với Tailwind CSS và Shadcn/ui components <br> - Implement form state management | ✅ Hoàn thành | [Next.js Forms](https://nextjs.org/docs/app/building-your-application/data-fetching) |
| **Thứ 6** | **Tích hợp Cognito Hosted UI** <br> - Integrate Cognito Hosted UI vào Next.js <br> - Implement token management (access, ID, refresh tokens) <br> - Tạo auth context/provider cho app <br> - Test authentication flow end-to-end | ✅ Hoàn thành | [Cognito Hosted UI](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-app-integration.html) |
| **Chủ nhật** | **Tìm hiểu AI Services & Hỗ trợ Backend** <br> - Tìm hiểu Amazon Bedrock, Transcribe, Polly <br> - Tham gia thảo luận về Clean Architecture <br> - Hiểu repository pattern và dependency injection <br> - Ghi chép kiến thức để áp dụng vào frontend | ✅ Hoàn thành | [Bedrock](https://docs.aws.amazon.com/bedrock/)<br>[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) |

### 🎓 Kiến thức đã học:

| Chủ đề | Nội dung | Kết quả |
|---------|---------|--------|
| **DynamoDB Design** | Single-table, PK/SK, GSI | ✅ Hiểu cách thiết kế schema |
| **API Design** | OpenAPI, REST principles | ✅ Biết định nghĩa endpoints |
| **Cognito** | User Pool, OAuth, MFA | ✅ Biết cấu hình authentication |
| **Auth UI** | Forms, validation, state | ✅ Biết xây dựng auth pages |
| **Token Management** | JWT, refresh tokens | ✅ Hiểu cách quản lý tokens |
| **AI Services** | Bedrock, Transcribe, Polly | ✅ Hiểu các services cơ bản |
| **Clean Architecture** | Layers, dependencies | ✅ Hiểu nguyên tắc thiết kế |

### Kết quả:

* **Database Design:**
  * ✅ DynamoDB schema hoàn chỉnh với single-table design
  * ✅ Tất cả access patterns được cover
  * ✅ GSI được thiết kế cho query phức tạp
  * ✅ Document chi tiết với examples

* **API Specification:**
  * ✅ OpenAPI specification hoàn chỉnh
  * ✅ Tất cả endpoints được định nghĩa rõ ràng
  * ✅ Request/Response schemas với validation
  * ✅ Error responses được standardize

* **Authentication System:**
  * ✅ Cognito User Pool configured với email/password + Google OAuth
  * ✅ Signup flow hoạt động: email verification, password validation
  * ✅ Login flow trả về tokens
  * ✅ Hosted UI được setup

* **Frontend Auth Pages:**
  * ✅ Login page với form validation
  * ✅ Signup page với password confirmation
  * ✅ Styling đẹp với Tailwind CSS + Shadcn/ui
  * ✅ Form state management working

* **Token Management:**
  * ✅ Access token, ID token, refresh token được quản lý
  * ✅ Token refresh mechanism working
  * ✅ Auth context/provider setup
  * ✅ Protected routes middleware ready

* **AI Services Knowledge:**
  * ✅ Hiểu Bedrock, Transcribe, Polly cơ bản
  * ✅ Biết cách integrate vào ứng dụng
  * ✅ Ghi chép kiến thức để áp dụng

### Bài học:

* **Bài học 1: API Design trước khi code** - Viết OpenAPI spec trước giúp team thống nhất interface, giảm miscommunication.

* **Bài học 2: Cognito Hosted UI tiện lợi** - Sử dụng Hosted UI thay vì tự build auth form giúp tiết kiệm thời gian và tăng bảo mật.

* **Bài học 3: Token Management quan trọng** - Quản lý tokens đúng cách (access token ngắn hạn, refresh token dài hạn) là crucial cho bảo mật.

### Kế hoạch Tuần 4:

* Implement Profile management pages (GET/PUT /profile)
* Tạo Dashboard layout và components
* Implement Flashcard CRUD UI
* Tích hợp frontend với backend APIs
* Deploy frontend lên AWS Amplify
* Tham gia họp nhóm review tiến độ

---

## 📊 Thống kê Tuần 3:

| Tiêu chí | Giá trị |
|----------|--------|
| **Tổng thời gian** | ~40 giờ |
| **Số ngày làm việc** | 6 ngày |
| **Số AWS services học** | 3 dịch vụ |
| **Số công nghệ frontend** | 3 công nghệ |

---

**Cập nhật:** 29/04/2026  
**Trạng thái:** ✅ Hoàn thành
