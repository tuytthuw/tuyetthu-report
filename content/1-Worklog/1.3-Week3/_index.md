---
title: "Worklog Week 3"
date: 2026-03-23
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

## Week 3: Backend Foundation & Authentication UI

**Date:** 23/03/2026 - 28/03/2026  
**Total Time:** ~40 hours

### Week 3 Objectives:

* **Design Database Schema:** Analyze access patterns, design DynamoDB single-table
* **Design API Specification:** Define endpoints, write OpenAPI spec
* **Learn Cognito:** Configure User Pool, OAuth providers, hosted UI
* **Build Auth UI:** Create login/signup pages with Next.js
* **Integrate Frontend-Backend:** Connect auth UI with Cognito
* **Learn AI Services:** Research Bedrock, Transcribe, Polly
* **Support Backend:** Participate in discussions about Clean Architecture and repository pattern

### Work Content:

| Day | Task | Status | References |
| --- | --- | --- | --- |
| **Monday** | **Analyze Access Patterns & Design DynamoDB** <br> - Analyze application access patterns <br> - Design DynamoDB single-table design <br> - Identify partition key, sort key, GSI <br> - Document schema with examples | ✅ Completed | [DynamoDB Design](https://www.alexdebrie.com/posts/dynamodb-single-table/) |
| **Tuesday** | **Design API Specification** <br> - Define all endpoints (Auth, Profile, Flashcards, Sessions) <br> - Write OpenAPI 3.0 specification <br> - Define request/response schemas <br> - Discuss with team about API design | ✅ Completed | [OpenAPI Spec](https://swagger.io/specification/) |
| **Wednesday** | **Configure Amazon Cognito** <br> - Create User Pool with email/password authentication <br> - Configure password policy and MFA <br> - Setup Google OAuth provider <br> - Create App Client and hosted UI <br> - Test signup/login flow | ✅ Completed | [Cognito User Pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) |
| **Thursday** | **Build Auth Pages** <br> - Create `/app/auth/login/page.tsx` with form validation <br> - Create `/app/auth/signup/page.tsx` with password confirmation <br> - Style with Tailwind CSS and Shadcn/ui components <br> - Implement form state management | ✅ Completed | [Next.js Forms](https://nextjs.org/docs/app/building-your-application/data-fetching) |
| **Friday** | **Integrate Cognito Hosted UI** <br> - Integrate Cognito Hosted UI into Next.js <br> - Implement token management (access, ID, refresh tokens) <br> - Create auth context/provider for app <br> - Test authentication flow end-to-end | ✅ Completed | [Cognito Hosted UI](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-app-integration.html) |
| **Sunday** | **Learn AI Services & Support Backend** <br> - Learn Amazon Bedrock, Transcribe, Polly <br> - Participate in discussions about Clean Architecture <br> - Understand repository pattern and dependency injection <br> - Take notes on knowledge to apply to frontend | ✅ Completed | [Bedrock](https://docs.aws.amazon.com/bedrock/)<br>[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) |

### 🎓 Knowledge Learned:

| Topic | Content | Result |
|---------|---------|--------|
| **DynamoDB Design** | Single-table, PK/SK, GSI | ✅ Understand schema design |
| **API Design** | OpenAPI, REST principles | ✅ Know how to define endpoints |
| **Cognito** | User Pool, OAuth, MFA | ✅ Know how to configure authentication |
| **Auth UI** | Forms, validation, state | ✅ Know how to build auth pages |
| **Token Management** | JWT, refresh tokens | ✅ Understand token management |
| **AI Services** | Bedrock, Transcribe, Polly | ✅ Understand basic services |
| **Clean Architecture** | Layers, dependencies | ✅ Understand design principles |

### Results:

* **Database Design:**
  * ✅ DynamoDB schema complete with single-table design
  * ✅ All access patterns covered
  * ✅ GSI designed for complex queries
  * ✅ Detailed documentation with examples

* **API Specification:**
  * ✅ OpenAPI specification complete
  * ✅ All endpoints clearly defined
  * ✅ Request/Response schemas with validation
  * ✅ Error responses standardized

* **Authentication System:**
  * ✅ Cognito User Pool configured with email/password + Google OAuth
  * ✅ Signup flow working: email verification, password validation
  * ✅ Login flow returns tokens
  * ✅ Hosted UI setup

* **Frontend Auth Pages:**
  * ✅ Login page with form validation
  * ✅ Signup page with password confirmation
  * ✅ Beautiful styling with Tailwind CSS + Shadcn/ui
  * ✅ Form state management working

* **Token Management:**
  * ✅ Access token, ID token, refresh token managed
  * ✅ Token refresh mechanism working
  * ✅ Auth context/provider setup
  * ✅ Protected routes middleware ready

* **AI Services Knowledge:**
  * ✅ Understand Bedrock, Transcribe, Polly basics
  * ✅ Know how to integrate into application
  * ✅ Take notes on knowledge to apply

### Lessons Learned:

* **Lesson 1: API Design Before Code** - Writing OpenAPI spec first helps team align interface, reduces miscommunication.

* **Lesson 2: Cognito Hosted UI Convenient** - Using Hosted UI instead of building auth form saves time and increases security.

* **Lesson 3: Token Management Important** - Managing tokens correctly (short-lived access token, long-lived refresh token) is crucial for security.

### Week 4 Plan:

* Implement Profile management pages (GET/PUT /profile)
* Create Dashboard layout and components
* Implement Flashcard CRUD UI
* Integrate frontend with backend APIs
* Deploy frontend to AWS Amplify
* Participate in team review meeting

---

## 📊 Week 3 Statistics:

| Criteria | Value |
|----------|-------|
| **Total Time** | ~40 hours |
| **Working Days** | 6 days |
| **AWS Services** | 3 services |
| **Frontend Technologies** | 3 technologies |

---

**Updated:** 29/04/2026  
**Status:** ✅ Completed
