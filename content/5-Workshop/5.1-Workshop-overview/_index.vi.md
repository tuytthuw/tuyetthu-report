---
title: "Overview"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## What is Lexi?

**Lexi** là nền tảng học tiếng Anh ứng dụng AI, được thiết kế để giải quyết vấn đề: thiếu môi trường luyện nói thực sự, không sợ mắc lỗi, và có thể luyện tập 24/7.

### Core Benefits

1. **Learn Anytime** — Không cần đặt lịch, luyện tập 24/7
2. **Personalized Feedback** — Góp ý chính xác theo trình độ
3. **Safe Learning Space** — Không áp lực, không phán xét
4. **Cost Effective** — Hiệu quả cao, chi phí thấp

## System Architecture

![Kiến trúc Lexi](/images/5-Workshop/lexi-architecture.jpeg)

## Main Components

### 1. Frontend (Next.js + Amplify)

- **Framework**: Next.js 15 với App Router
- **UI**: Shadcn/ui
- **Hosting**: AWS Amplify
- **Features**:
  - Speaking practice (WebSocket real-time)
  - Flashcard management (SRS algorithm)
  - Progress dashboard
  - User profile

### 2. Backend (Lambda + SAM)

**23 Lambda Functions** organized by modules:

#### Onboarding Module
- `CompleteOnboardingFunction` - Complete initial setup

#### Admin Module
- `ListAdminUsersFunction` - List users
- `UpdateAdminUserFunction` - Update user info
- `ListAdminScenariosFunction` - List scenarios
- `CreateAdminScenarioFunction` - Create scenario
- `UpdateAdminScenarioFunction` - Update scenario

#### Profile Module
- `GetProfileFunction` - Get profile
- `UpdateProfileFunction` - Update profile

#### Flashcard Module (SRS)
- `CreateFlashcardFunction` - Create card
- `ListFlashcardsFunction` - List cards
- `ListDueCardsFunction` - Get due cards
- `ReviewFlashcardFunction` - Review card (SM-2)
- `GetFlashcardFunction` - Get card details
- `UpdateFlashcardFunction` - Update card
- `DeleteFlashcardFunction` - Delete card
- `ExportFlashcardsFunction` - Export (CSV/JSON)
- `ImportFlashcardsFunction` - Import cards
- `GetStatisticsFunction` - Get statistics

#### Speaking Module (AI + Voice)
- `SpeakingWebSocketFunction` - WebSocket handler
- `SpeakingSessionFunction` - Manage sessions

#### Vocabulary Module
- `TranslateVocabularyFunction` - Translate word
- `TranslateSentenceFunction` - Translate sentence

#### Scenarios Module
- `ListScenariosFunction` - List public scenarios

### 3. Database (DynamoDB)

**Single-Table Design** with `LexiApp` table:

```
Partition Key: PK (e.g., "USER#user-id", "FLASHCARD#card-id")
Sort Key: SK (e.g., "PROFILE", "CREATED#timestamp")

Global Secondary Indexes (GSI):
- GSI1: PK=GSI1PK, SK=GSI1SK
- GSI2: PK=GSI2PK, SK=GSI2SK
- GSI3: PK=GSI3PK, SK=GSI3SK
- GSI4: PK=GSI4PK, SK=GSI4SK
- GSI5: PK=GSI5PK, SK=GSI5SK
```

**Ghi chú**: 
- GSI1: Truy vấn theo user
- GSI2: Truy vấn theo thời gian
- GSI3: SRS - due cards
- GSI4: Admin queries
- GSI5: Analytics

### 4. Authentication (Cognito)

- **User Pool**: `LexiUserPool-prod` (ap-southeast-1_I9ri7n518)
- **Lambda Triggers**:
  - `PreSignUp` - Auto-confirm email
  - `PostConfirmation` - Create user profile
  - `PostAuthentication` - Log login

### 5. AI & Voice Processing

#### Amazon Bedrock (Claude 3 Haiku)
- AI conversation for speaking practice
- Feedback on grammar, pronunciation, vocabulary
- Cost: ~$0.25/1M input tokens

#### Amazon Transcribe
- Speech-to-Text (STT)
- English, Vietnamese support
- Cost: ~$0.024/minute

#### Amazon Polly
- Text-to-Speech (TTS)
- Neural voices
- Cost: ~$0.02/1K characters

#### Amazon Comprehend
- Natural language analysis
- Sentence structure, keywords
- Cost: ~$0.0001/unit

### 6. Storage (S3)

- **Bucket**: `lexi-be-speakingaudiobucket-*`
- **Purpose**: Store speaking audio
- **Encryption**: AES-256
- **CORS**: Allow frontend access

### 7. API Gateway

#### REST API
- **Endpoint**: `https://mnjxcw3o1e.execute-api.ap-southeast-1.amazonaws.com/Prod/`
- **Auth**: Cognito Authorizer
- **Routes**: /flashcards, /profile, /sessions, /admin, /scenarios

#### WebSocket API
- **Endpoint**: `wss://{api-id}.execute-api.ap-southeast-1.amazonaws.com/Prod`
- **Purpose**: Real-time speaking sessions
- **Routes**: $connect, $disconnect, $default

## Speaking Flow

| Bước | Mô tả |
|------|-------|
| 1 | User kết nối WebSocket |
| 2 | Frontend gửi audio stream (Transcribe) |
| 3 | Lambda nhận audio → Transcribe: Audio → Text |
| 4 | Lambda gửi text + context → Bedrock (AI) |
| 5 | Bedrock trả về phản hồi |
| 6 | Lambda tổng hợp → Polly (TTS) |
| 7 | Lambda gửi audio phản hồi về frontend |
| 8 | Frontend phát audio cho user |
| 9 | Lưu session vào DynamoDB |

## Flashcard SRS Flow

| Bước | Mô tả |
|------|-------|
| 1 | User yêu cầu danh sách card cần ôn |
| 2 | Lambda truy vấn DynamoDB (GSI3) |
| 3 | Trả về card theo SM-2 algorithm |
| 4 | User ôn tập card |
| 5 | User chọn: Easy / Good / Hard / Again |
| 6 | Lambda tính toán next review date (SM-2) |
| 7 | Cập nhật card trong DynamoDB |
| 8 | Cập nhật statistics |

## Operating Costs (Estimated)

### Development Stage (100 users)
| Service | Cost/month |
|---------|-----------|
| Lambda | $0.44 |
| DynamoDB | $0.02 |
| S3 | $1.28 |
| API Gateway | $0.19 |
| Bedrock | $5.00 |
| Transcribe | $2.40 |
| Polly | $0.20 |
| Comprehend | $1.00 |
| Translate | $1.50 |
| **Total** | **$12.03** |

### Growth Stage (500 users)
| Service | Cost/month |
|---------|-----------|
| Lambda | $2.20 |
| DynamoDB | $0.10 |
| S3 | $6.40 |
| API Gateway | $0.95 |
| AI/ML Services | $50.00 |
| **Total** | **$59.65** |

## Clean Architecture

Backend organized in 5 layers:

### 1. Domain Layer (`src/domain/`)
**Core business logic** - Independent from other layers

- **Entities**: Flashcard, Scenario, Session, Turn, UserProfile, Vocabulary, Scoring
- **Value Objects**: Character, Enums (DifficultyLevel, ReviewQuality, SessionStatus)
- **Domain Services**: 
  - SRS Engine (Spaced Repetition System)
  - Speaking Performance Scorer
  - Conversation Orchestrator & Analyzer
  - Prompt Builder & Validator
  - Model Router, Greeting Generator
  - Metrics & Cost Aggregator
  - CloudWatch Dashboard Builder
- **Domain Exceptions**: Dictionary exceptions

### 2. Application Layer (`src/application/`)
**Use cases and business workflows** - Depends on Domain

- **Use Cases**: Admin, Flashcard, Onboarding, Profile, Scenario, Speaking, Vocabulary
- **DTOs**: Auth, Flashcard, Onboarding, Profile, Speaking, Vocabulary
- **Repository Interfaces**: FlashCard, Scenario, Scoring, Session, Turn, UserProfile
- **Service Ports**: Dictionary, LLM Scoring, Speaking Services, Translation
- **Validators**: Onboarding validators
- **Exceptions**: Application, Auth, Flashcard, Vocabulary errors

### 3. Infrastructure Layer (`src/infrastructure/`)
**Repository and service implementations** - Depends on Application

- **Handlers**: Lambda handlers (Admin, Auth, Flashcard, Onboarding, Profile, Speaking, Vocabulary)
- **Persistence**: DynamoDB repositories (Flashcard, Metrics, Scenario, Scoring, Session, Turn, User)
- **Services**: AWS Translate, Bedrock Scorer, Cache, Retry, Speaking Pipeline, Transcribe
- **Adapters**: Dictionary Service Adapter
- **Configuration**: Config, Logging
- **Factories**: Repository Factory, Service Factory, Handler Factory

### 4. Interfaces Layer (`src/interfaces/`)
**API contracts and presentation logic** - Depends on Application

- **Controllers**: Admin, Auth, Flashcard, Onboarding, Profile, Scenario, Session, Vocabulary
- **View Models**: Admin, Flashcard, Onboarding, Scenario, Session, User, Vocabulary
- **Mappers**: Auth, Flashcard, Onboarding, Profile, Session, Vocabulary
- **Presenters**: Base, HTTP Presenter

### 5. Shared Layer (`src/shared/`)
**Common utilities** - Independent from other layers

- **HTTP Utils**: Response formatting, error handling
- **Result**: Result pattern (Success/Failure)
- **ULID Util**: ID generation

## Dependency Flow

![Clean Architecture Layers](/images/5-Workshop/clean_arch_layers.png)

**Nguyên tắc**:
- Domain không phụ thuộc vào layer nào
- Application chỉ phụ thuộc vào Domain
- Infrastructure implement interfaces từ Application
- Interfaces orchestrate use cases từ Application
- Shared được dùng bởi tất cả layers

## Deployment Strategy

### CloudFormation Stacks

1. **lexi-database** - DynamoDB table
2. **lexi-auth-base** - Cognito User Pool
3. **lexi-auth-lambdas** - Cognito Lambda Triggers
4. **lexi-auth-providers** - OAuth providers (future)
5. **lexi-be** - Main application (Lambda, API Gateway, S3)

### Deployment Order

| Thứ tự | Stack | Mô tả |
|--------|-------|-------|
| 1 | lexi-database | DynamoDB table |
| 2 | lexi-auth-base | Cognito User Pool |
| 3 | lexi-auth-lambdas | Auth Lambda triggers |
| 4 | lexi-be | Main application (Lambda, API Gateway, S3) |
| 5 | Frontend | Amplify deployment |

## Danh sách kiểm tra

Sau khi đọc phần tổng quan, bạn nên:

- [ ] Hiểu kiến trúc serverless của Lexi
- [ ] Nắm được các thành phần chính (Lambda, DynamoDB, API Gateway, Cognito, S3)
- [ ] Hiểu quy trình luyện nói (Speaking Flow)
- [ ] Hiểu quy trình ôn tập flashcard (SRS Flow)
- [ ] Nắm được ước tính chi phí vận hành
- [ ] Hiểu Clean Architecture của backend
- [ ] Nắm được deployment strategy

## Next Steps

Tiếp tục đến [Prerequisites & Setup](../5.2-Prerequiste/) để bắt đầu triển khai.
