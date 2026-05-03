---
title: "Overview"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## What is Lexi?

**Lexi** is an AI-powered English learning platform designed to address the challenge of limited real-world speaking practice environments. It enables learners to practice speaking without fear of making mistakes, available 24/7.

### Core Benefits

1. **Learn Anytime** — No scheduling required; practice 24/7 at your own pace
2. **Personalized Feedback** — Receive accurate guidance tailored to your proficiency level
3. **Safe Learning Space** — Practice without pressure or judgment
4. **Cost Effective** — High-quality learning at an affordable price

## System Architecture

![Lexi Architecture](/images/5-Workshop/lexi-architecture.jpeg)

## Main Components

### 1. Frontend (Next.js + Amplify)

- **Framework**: Next.js 15 with App Router
- **UI Library**: Shadcn/ui
- **Hosting**: AWS Amplify
- **Features**:
  - Real-time speaking practice via WebSocket
  - Flashcard management with SRS algorithm
  - Progress dashboard
  - User profile management

### 2. Backend (Lambda + SAM)

**23 Lambda Functions** organized by functional modules:

#### Onboarding Module
- `CompleteOnboardingFunction` - Complete initial user setup

#### Admin Module
- `ListAdminUsersFunction` - Retrieve user list
- `UpdateAdminUserFunction` - Update user information
- `ListAdminScenariosFunction` - Retrieve scenario list
- `CreateAdminScenarioFunction` - Create new scenario
- `UpdateAdminScenarioFunction` - Update scenario

#### Profile Module
- `GetProfileFunction` - Retrieve user profile
- `UpdateProfileFunction` - Update user profile

#### Flashcard Module (SRS)
- `CreateFlashcardFunction` - Create flashcard
- `ListFlashcardsFunction` - List flashcards
- `ListDueCardsFunction` - Retrieve due cards for review
- `ReviewFlashcardFunction` - Process card review (SM-2 algorithm)
- `GetFlashcardFunction` - Retrieve card details
- `UpdateFlashcardFunction` - Update flashcard
- `DeleteFlashcardFunction` - Delete flashcard
- `ExportFlashcardsFunction` - Export cards (CSV/JSON)
- `ImportFlashcardsFunction` - Import flashcards
- `GetStatisticsFunction` - Retrieve learning statistics

#### Speaking Module (AI + Voice)
- `SpeakingWebSocketFunction` - WebSocket connection handler
- `SpeakingSessionFunction` - Manage speaking sessions

#### Vocabulary Module
- `TranslateVocabularyFunction` - Translate individual words
- `TranslateSentenceFunction` - Translate sentences

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

**Index Purposes**:
- GSI1: User-based queries
- GSI2: Time-based queries
- GSI3: SRS - due cards retrieval
- GSI4: Admin queries
- GSI5: Analytics queries

### 4. Authentication (Cognito)

- **User Pool**: `LexiUserPool-prod` (ap-southeast-1_I9ri7n518)
- **Lambda Triggers**:
  - `PreSignUp` - Auto-confirm email verification
  - `PostConfirmation` - Create user profile
  - `PostAuthentication` - Log login events

### 5. AI & Voice Processing

#### Amazon Bedrock (Claude 3 Haiku)
- AI-powered conversation for speaking practice
- Provides feedback on grammar, pronunciation, and vocabulary
- Cost: ~$0.25/1M input tokens

#### Amazon Transcribe
- Speech-to-Text (STT) conversion
- Supports English and Vietnamese
- Cost: ~$0.024/minute

#### Amazon Polly
- Text-to-Speech (TTS) synthesis
- Neural voice technology
- Cost: ~$0.02/1K characters

#### Amazon Comprehend
- Natural language analysis
- Analyzes sentence structure and keywords
- Cost: ~$0.0001/unit

### 6. Storage (S3)

- **Bucket**: `lexi-be-speakingaudiobucket-*`
- **Purpose**: Store speaking practice audio files
- **Encryption**: AES-256 server-side encryption
- **CORS**: Configured for frontend access

### 7. API Gateway

#### REST API
- **Endpoint**: `https://mnjxcw3o1e.execute-api.ap-southeast-1.amazonaws.com/Prod/`
- **Authentication**: Cognito Authorizer
- **Routes**: /flashcards, /profile, /sessions, /admin, /scenarios

#### WebSocket API
- **Endpoint**: `wss://{api-id}.execute-api.ap-southeast-1.amazonaws.com/Prod`
- **Purpose**: Real-time speaking session communication
- **Routes**: $connect, $disconnect, $default

## Speaking Practice Flow

| Step | Description |
|------|-------------|
| 1 | User establishes WebSocket connection |
| 2 | Frontend streams audio to backend (Transcribe) |
| 3 | Lambda receives audio → Transcribe converts to text |
| 4 | Lambda sends text + context → Bedrock (AI) |
| 5 | Bedrock generates feedback response |
| 6 | Lambda synthesizes response → Polly (TTS) |
| 7 | Lambda sends audio response to frontend |
| 8 | Frontend plays audio response to user |
| 9 | Session data persisted to DynamoDB |

## Flashcard SRS Flow

| Step | Description |
|------|-------------|
| 1 | User requests list of cards due for review |
| 2 | Lambda queries DynamoDB (GSI3) |
| 3 | Returns cards based on SM-2 algorithm |
| 4 | User reviews flashcard |
| 5 | User selects: Easy / Good / Hard / Again |
| 6 | Lambda calculates next review date (SM-2) |
| 7 | Card updated in DynamoDB |
| 8 | Learning statistics updated |

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

**Principles**:
- Domain layer has no dependencies on other layers
- Application layer depends only on Domain
- Infrastructure implements interfaces from Application
- Interfaces orchestrate use cases from Application
- Shared utilities used across all layers

## Deployment Strategy

### CloudFormation Stacks

1. **lexi-database** - DynamoDB table
2. **lexi-auth-base** - Cognito User Pool
3. **lexi-auth-lambdas** - Cognito Lambda Triggers
4. **lexi-auth-providers** - OAuth providers (future)
5. **lexi-be** - Main application (Lambda, API Gateway, S3)

### Deployment Order

| Order | Stack | Description |
|-------|-------|-------------|
| 1 | lexi-database | DynamoDB table |
| 2 | lexi-auth-base | Cognito User Pool |
| 3 | lexi-auth-lambdas | Auth Lambda triggers |
| 4 | lexi-be | Main application (Lambda, API Gateway, S3) |
| 5 | Frontend | Amplify deployment |

## Checklist

After reviewing this overview, you should:

- [ ] Understand Lexi's serverless architecture
- [ ] Know the main components (Lambda, DynamoDB, API Gateway, Cognito, S3)
- [ ] Understand the speaking practice flow
- [ ] Understand the flashcard SRS flow
- [ ] Know the estimated operating costs
- [ ] Understand the backend's Clean Architecture
- [ ] Know the deployment strategy

## Next Steps

Continue to [Prerequisites & Setup](../5.2-Prerequiste/) to begin deployment.
