---
title: "Proposal"
date: "2026-03-09"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Lexi - The AI Tutor That Never Judges

---

## 1. Project Overview

### Why Can't Most English Learners in Vietnam Speak Fluently?

A familiar paradox in English learning in Vietnam: many people master grammar, write fluently on exams, but "freeze up" when they need to communicate in real situations. The problem isn't ability—it's the barriers that follow.

1. **Lack of a real speaking environment** — It's hard to find someone to talk to regularly
2. **Fear of making mistakes** — Shame psychology causes learners to avoid communication, even though mistakes are how we learn
3. **Vocabulary learned, then forgotten** — Without a systematic review system, 80% of new words disappear within 30 days
4. **Cost barriers** — Quality tutors are expensive and not accessible to most learners

### What happens when learners can practice speaking without fear of judgment?

Imagine a learner with solid grammar foundations, but who feels anxious and embarrassed every time they need to speak English in public. Despite spending money and years studying, they can't start a simple conversation without stress.

With an AI Tutor available anytime, without judgment, willing to repeat endlessly, and costing far less than a traditional tutor—everything changes.

### What is Lexi?

**Lexi** is an AI-powered English learning platform designed to solve a major gap that traditional learning methods often overlook: **practicing speaking enough, consistently, without fear of making mistakes**.

Rather than trying to replace tutors, Lexi focuses on what humans struggle to do well: being available 24/7, having unlimited patience, and creating a pressure-free learning environment.

**Four core advantages:**
- **Learn anytime** — No scheduling needed; you can practice even at 2 AM
- **Personalized feedback** — Precise suggestions tailored to your level and weak points
- **Safe space to make mistakes** — No pressure, no judgment, helping you speak with confidence
- **Cost-effective** — Equivalent to many hours with a tutor, but at a fraction of the cost

---

## 2. Project Goals

### Product Goals

1. Launch a functional MVP with AI Tutor and vocabulary management system
2. Reach 50–100 real users for feedback and evaluation
3. Complete within a $200 AWS budget

### Technical Goals

1. Organize and deploy backend using Clean Architecture on AWS Lambda with Serverless architecture
2. Frontend using Next.js App Router with TypeScript and Shadcn/ui
3. Integrate Amazon Bedrock for AI conversation
4. Store data using Amazon DynamoDB
5. CI/CD pipeline for automated testing and deployment

### Scope and Constraints

- Prioritize all MVP features working
- AI conversation at a usable level, not requiring perfection
- Infrastructure stable for development phase
- Build on web platform, no mobile deployment yet
- Testing focused on main user flows

---

## 3. Core Features

### 1. Speaking Practice with AI Tutor
- Interactive chat interface with AI as conversation partner
- Voice input (Amazon Transcribe) → AI processing → Voice response (Amazon Polly)
- Detailed feedback on grammar, pronunciation, vocabulary, and development suggestions
- Save conversation history to track progress

### 2. Flashcards with SRS Algorithm
- Create and review vocabulary on automatic schedule
- SM-2 algorithm calculates optimal review timing
- Track retention rate for each card (easy / good / hard / again)
- Statistics: total words learned, words due for review, mastery level

### 3. Structured Lessons
- Content organized by CEFR levels (A1–C2)
- Grammar, vocabulary, and practical usage explanations
- Audio and video examples from native speakers

### 4. Progress Tracking Dashboard
- Statistics: study hours, accumulated words, retention rate
- Streak — consecutive days of uninterrupted learning
- Achievement badges for learning milestones
- Progress charts over time

---

## 4. Solution Architecture

### Tech Stack and Selection Rationale

The stack was chosen based on one criterion: **best fit for an 8-week timeline for a solo developer**.

| Layer | Technology | Rationale |
| :--- | :--- | :--- |
| **Frontend** | Next.js + TypeScript | Balances coding speed with type safety; App Router simplifies routing and APIs |
| **Backend** | Python 3.12 + AWS Lambda | Serverless eliminates infrastructure management; Python has strong SDKs and concise syntax |
| **AI** | Amazon Bedrock Nova Lite | AI conversation without ML expertise; 90% cheaper than custom models |
| **Voice** | Amazon Transcribe + Polly | WebSocket streaming creates natural speaking experience; reduces complexity with managed services |
| **Database** | DynamoDB single-table design | Auto-scales with Lambda; predictable performance with pay-per-use pricing |
| **Auth** | Amazon Cognito | Managed authentication, OAuth integration, no need to build from scratch |
| **Deployment** | Amplify (frontend) + SAM (backend) | Separate pipelines — frontend and backend can iterate independently |

**Intentionally excluded:**
- Advanced monitoring (Sentry, PostHog) → Basic CloudWatch sufficient for MVP
- Complex CI/CD → GitHub Actions + Amplify handles it well
- Heavy test frameworks → Only unit tests for core business logic

> Every choice aims at one goal: **ship fast, scale well, don't waste time on infrastructure**.

### System Architecture

![Lexi Architecture Diagram](/images/2-Proposal/lexi-architecture.jpeg)

**Architecture Description:**

The Lexi system is designed using a serverless model on AWS, comprising these main components:

1. **Frontend (Actors)** — Users (Admin, Learner) access via Custom Domain, Amazon CloudFront, and Next.js/SSR hosting on AWS Amplify
2. **Authentication** — Amazon Cognito handles login with OAuth (Google), Email/Password, and issues ID Token (JWT)
3. **API Layer** — Amazon API Gateway (REST + WebSocket) routes requests to Lambda functions
4. **Service Layer (AWS Lambda)** — Microservices handle:
   - **CRUD** — Profile, vocabulary, session management
   - **Flashcards** — SRS algorithm for review
   - **Profile/Sessions** — User progress storage
   - **Scenarios** — Structured lesson management
   - **Admin Operations** — User management, system configuration
   - **Authentication** — Authentication and authorization
5. **File Storage** — Amazon S3 stores audio files, media, imports/exports
6. **Database** — Amazon DynamoDB (single-table design) stores all application data
7. **AI & Voice Processing**:
   - **Amazon Bedrock** — Handles AI conversation (Nova Lite model)
   - **Amazon Transcribe** — Converts speech to text
   - **Amazon Polly** — Synthesizes text to speech
   - **Amazon Comprehend** — Natural language analysis
   - **Amazon Translate** — Text translation
8. **Monitoring & Logging** — Amazon CloudWatch tracks logs, metrics, alarms; CloudWatch Dashboards display system status

**Audio Streaming Flow (Speaking Practice):**
- User records audio → Amazon Transcribe receives transcript → Send request to API Gateway (with ID Token) → Lambda processes logic → Call Amazon Bedrock to generate response → Amazon Polly synthesizes speech → Return audio to user → Display results on frontend

---

## 5. Timeline

### Phase 1 — Learning and Preparation (Weeks 1–2)

| Week | Tasks | Outcome |
| :--- | :--- | :--- |
| **1** | Learn AWS Serverless, Lambda, DynamoDB | Master core AWS services |
| **1** | Learn Amazon Bedrock, Transcribe, Polly | Understand AI and voice processing integration |
| **2** | Learn Clean Architecture in Python | Have backend design ready |
| **2** | Design database schema and API spec | Ready to start coding |

### Phase 2 — Development (Weeks 3–8)

| Week | Sprint | Work | Deliverable |
| :--- | :--- | :--- | :--- |
| **3–4** | Sprint 1 | Backend: Auth, API, Database | Working API endpoints |
| **3–4** | Sprint 1 | Frontend: UI components, basic layout | Functional interface framework |
| **5–6** | Sprint 2 | AI Tutor — Bedrock integration | End-to-end speaking practice flow |
| **5–6** | Sprint 2 | Flashcard — SRS algorithm | Working flashcard review |
| **7** | Sprint 3 | Voice — Transcribe + Polly | Speech input and output |
| **7** | Sprint 3 | Dashboard — analytics, progress | Progress tracking with real data |
| **8** | Sprint 4 | Testing, optimization, deployment | MVP ready for demo |

### Key Milestones

- **End of Week 2** — Design and preparation complete, ready to code
- **End of Week 4** — Basic backend + frontend working
- **End of Week 6** — AI Tutor and Flashcard fully functional
- **End of Week 7** — Voice integration complete
- **End of Week 8** — MVP in production, ready for demo

---

## 6. Budget

### Cost Analysis Based on System Architecture

Based on serverless architecture design and detailed analysis from AWS official documentation, here's the estimated operating cost for the Lexi project:

### Operating Costs by Usage Scenarios

**Scenario 1: Development and Testing Phase (100 users)**
| AWS Service | Estimated Cost/Month | Calculation Basis |
| :--- | :--- | :--- |
| **AWS Lambda** | $0.44 | 100,000 function calls, 256MB memory, average 1 second execution |
| **Amazon DynamoDB** | $0.02 | 10GB storage, 50,000 reads and 20,000 writes |
| **Amazon S3** | $1.28 | 50GB storage for audio files, 5,000 uploads and 5,000 downloads |
| **Amazon API Gateway** | $0.19 | 50,000 REST API requests, 100 hours WebSocket connections |
| **Amazon Bedrock** | $5.00 | Nova Lite model for AI conversation with moderate interaction volume |
| **Amazon Translate** | $1.50 | 100,000 characters text translation |
| **Amazon Comprehend** | $1.00 | 10,000 natural language analysis units |
| **Amazon Polly** | $0.20 | 50,000 characters synthesized to speech |
| **Amazon Transcribe** | $2.40 | 100 minutes speech to text conversion |
| **Total Cost** | **$12.03/month** | *Free tier applied where eligible* |

**Scenario 2: Moderate Growth (500 users)**
| AWS Service | Estimated Cost/Month |
| :--- | :--- |
| AWS Lambda | $2.20 |
| Amazon DynamoDB | $0.10 |
| Amazon S3 | $6.40 |
| Amazon API Gateway | $0.95 |
| AI/ML Services | $50.00 |
| **Total Cost** | **$59.65/month** |

**Scenario 3: High Growth (1,000+ users)**
| AWS Service | Estimated Cost/Month |
| :--- | :--- |
| AWS Lambda | $4.40 |
| Amazon DynamoDB | $0.20 |
| Amazon S3 | $12.80 |
| Amazon API Gateway | $1.90 |
| AI/ML Services | $100.00 |
| **Total Cost** | **$119.30/month** |

### Overall $200 Budget Allocation

| Deployment Phase | Budget Allocation | Purpose |
| :--- | :--- | :--- |
| **Months 1-2: Development** | $50 | Development environment, integration testing, prototyping |
| **Month 3: MVP Launch** | $50 | Production deployment, real user testing |
| **Months 4-6: Growth** | $100 | Scaling, performance optimization, feedback collection |
| **Risk Reserve** | $50 | Unexpected costs, optimization, unplanned scaling |

### Cost Management and Optimization Strategy

#### 1. Maximize AWS Free Tier
- **AWS Lambda**: 1 million requests/month free for first 12 months
- **Amazon DynamoDB**: 25GB storage free indefinitely
- **Amazon S3**: 5GB storage + 20,000 GET requests + 2,000 PUT requests free
- **Amazon API Gateway**: 1 million REST API requests + 750,000 minutes WebSocket connections free

#### 2. Control AI/ML Service Costs
- Prioritize Amazon Bedrock Nova Lite for cost optimization
- Implement caching for frequent prompts and responses
- Batch processing for non-real-time tasks
- Set per-user usage limits to prevent abuse

#### 3. Cost Monitoring and Alerts
- Configure AWS Budgets with alerts at $50, $100, $150
- Set CloudWatch alarms for each major service
- Use AWS Cost Explorer for weekly detailed analysis
- Apply cost allocation tags to track costs by feature

#### 4. System Architecture Optimization
- Adjust Lambda memory based on actual workload
- Use DynamoDB on-demand capacity mode for variable workloads
- Apply S3 lifecycle policies to move old files to cheaper storage classes
- Enable API Gateway caching for static responses

### Additional Costs to Consider

1. **Data Transfer**: $0.09/GB for outbound traffic (estimated $5-20/month)
2. **CloudWatch Logs**: $0.50/GB for storage and queries (estimated $2-10/month)
3. **AWS X-Ray**: $5.00/1 million requests tracing (estimated $5-15/month)
4. **AWS Support**: Developer Support plan $29/month (recommended for production)

### Feasibility Assessment with $200 Budget

With a total $200 budget for MVP phase, the Lexi-BE project can:
- **Develop and test** for 2 months with controlled costs
- **Launch MVP** with first 100 users in month 3
- **Operate stably** for 3-4 months with scaling capability
- **Reserve enough** for necessary optimizations and adjustments

**Critical insight:** AI/ML services (Bedrock, Transcribe, Polly, Comprehend) account for ~80% of operating costs. Optimizing these services is key to keeping the project within budget.

---

## 7. Risk Assessment

### Technical Risks

| Risk | Level | Mitigation |
| :--- | :--- | :--- |
| AI response slow (> 2s) | High | Optimize prompt size, enable response streaming, CloudWatch alerts |
| Transcribe / Polly failures | Medium | Fallback to text communication, graceful degradation, cache common responses |
| Exceed AWS budget | High | Per-user quota limits, daily AWS Budgets monitoring, use Nova Lite for savings |
| System can't handle load | Medium | Lambda auto-scaling with provisioned concurrency, DynamoDB on-demand capacity |

### Timeline Risks

| Risk | Level | Mitigation |
| :--- | :--- | :--- |
| Steep AWS learning curve | High | Learn core services first (Lambda, DynamoDB), cut scope if needed, prototype early |
| AI integration more complex than expected | High | Build simple Bedrock prototype first, use mock responses during parallel development |
| Clean Architecture slows coding | Medium | Use simplified version, keep only three core layers, defer complex patterns |

### Product Risks

| Risk | Level | Mitigation |
| :--- | :--- | :--- |
| Poor AI response quality | High | Iterate on prompts with feedback loops, A/B test conversation effectiveness |
| Voice quality not natural enough | Low | Use Polly neural voices, allow text fallback if needed |
| Security vulnerabilities | High | IAM least privilege, encryption at-rest and in-transit, regular reviews |

### Risk Priority Order

1. **Top priority** — AI quality and latency (directly impacts speaking experience)
2. **High** — Cost control (ensures project doesn't run out of budget)
3. **Medium** — Timeline risks (ensures MVP delivery on schedule)
4. **Low** — Voice improvements and additional features (can be deferred if needed)

---

## 8. Success Metrics

### Technical
- ✅ Complete MVP with all core features working
- ✅ Response time < 2 seconds
- ✅ 99%+ uptime
- ✅ No critical errors
- ✅ Clean Architecture properly applied

### User
- ✅ 100+ beta users
- ✅ 4.0+/5.0 rating
- ✅ Daily active rate ≥ 50%

### Personal Capability
- ✅ Master AWS Serverless
- ✅ Deep understanding of Clean Architecture
- ✅ Proficient in AI/LLM integration
- ✅ Able to independently deploy production apps

---

## 9. Conclusion

Lexi solves the exact problem traditional methods overlook: **lack of a safe, consistent speaking practice environment** for learners to truly open their mouths.

In 8 weeks, the project focuses on a functional MVP, prioritizing real user value over perfection. Using AWS serverless and managed AI services enables rapid development while ensuring future scalability.

Unlike traditional tutors—limited by time and cost—Lexi solves the hardest part of the speaking journey: **practicing enough, consistently, without fear of judgment**.
