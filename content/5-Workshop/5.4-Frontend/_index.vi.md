---
title: "Frontend Development"
date: "2026-05-02"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Giới thiệu

Frontend Lexi được xây dựng với **Next.js** (App Router), **TypeScript**, triển khai trên **AWS Amplify**. Phần này hướng dẫn xây dựng và triển khai ứng dụng frontend.

## Thông tin Triển khai

- **Platform**: AWS Amplify (WEB_COMPUTE)
- **Framework**: Next.js với App Router (SSR)
- **Package Manager**: pnpm
- **Region**: ap-southeast-1
- **CDN**: CloudFront
- **Custom Domain**: Hỗ trợ (Route 53 + ACM)

## Tech Stack

- **Framework**: Next.js với App Router (SSR)
- **Language**: TypeScript
- **UI Library**: Shadcn/ui (Radix UI + Tailwind CSS)
- **Package Manager**: pnpm
- **Authentication**: AWS Cognito
- **Hosting**: AWS Amplify (CloudFront CDN)
- **Real-time**: WebSocket API

## Nội dung Frontend

Frontend được chia thành 3 phần chính:

1. **[Next.js Setup](5.4.1-Next.js/)** - Khởi tạo và cấu hình
   - Tạo Next.js project với App Router
   - Cài đặt dependencies với pnpm
   - Cấu hình TypeScript
   - Setup API client
   - Environment variables

2. **[Amplify Deployment](5.4.2-Amplify/)** - Triển khai lên AWS
   - Tạo Amplify app từ GitHub repository
   - Cấu hình build settings (pnpm)
   - Setup environment variables
   - Kết nối custom domain (ngoctin.me)
   - SSL certificate verification

3. **[WebSocket Integration](5.4.3-WebSocket/)** - Real-time speaking
   - WebSocket client
   - Audio recording
   - Real-time communication
   - Error handling
   - Performance optimization

## Kiến trúc Frontend

**Luồng giao tiếp:**
- Browser → Next.js Application (App Router, TypeScript, Shadcn/ui)
- Next.js → Cognito (Authentication)
- Next.js → REST API (HTTPS) cho CRUD operations
- Next.js → WebSocket API (WSS) cho real-time speaking
- Backend Services: Lambda, DynamoDB, Cognito, Bedrock, Transcribe, Polly

**Deployment:**
- GitHub Repository → Amplify → CloudFront CDN
- Custom Domain: Route 53 + ACM SSL Certificate

## Cấu trúc Thư mục

```
lexi-fe/
├── app/                          # Next.js App Router
│   ├── layout.tsx               # Root layout
│   ├── page.tsx                 # Home page
│   ├── (auth)/                  # Auth routes (login, signup)
│   ├── (app)/                   # Protected routes
│   │   ├── dashboard/
│   │   ├── flashcards/
│   │   ├── speaking/
│   │   └── profile/
│   └── api/                     # API routes
├── components/                   # React components
│   ├── ui/                      # Shadcn/ui base components
│   ├── flashcard/               # Flashcard components
│   ├── speaking/                # Speaking components
│   └── dashboard/               # Dashboard components
├── features/                     # Feature modules
│   ├── auth/                    # Authentication
│   ├── flashcards/              # Flashcard feature
│   ├── speaking/                # Speaking feature
│   └── profile/                 # Profile feature
├── lib/                         # Utilities & clients
│   ├── api.ts                   # REST API client
│   ├── websocket.ts             # WebSocket client
│   └── auth.ts                  # Auth utilities
├── hooks/                       # Custom React hooks
│   ├── useAudioRecorder.ts
│   ├── useWebSocket.ts
│   └── useFlashcards.ts
├── public/                      # Static assets
├── amplify/                     # Amplify configuration
├── next.config.ts               # Next.js config
├── package.json                 # Dependencies
└── tsconfig.json                # TypeScript config
```

## Tính năng Chính

### 1. Authentication (Cognito)
- Sign up với email verification
- Login với username/email + password
- Forgot password flow
- Protected routes
- Session management

### 2. Dashboard
- Learning statistics
- Progress tracking
- Recent activities
- Upcoming reviews

### 3. Flashcards
- Create/Edit/Delete flashcards
- Spaced Repetition System (SRS)
- Review due cards
- Import/Export (CSV, JSON)
- Statistics & analytics

### 4. Speaking Practice
- Real-time WebSocket connection
- Audio recording
- Speech-to-Text (Transcribe)
- AI conversation (Bedrock)
- Text-to-Speech (Polly)
- Session history

### 5. Profile Management
- Update user information
- Change password
- Preferences
- Learning goals

## Environment Variables (Amplify)

```bash
# API Endpoints
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/
NEXT_PUBLIC_WS_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod

# AWS Configuration
NEXT_PUBLIC_AWS_REGION=<REGION>

# Cognito Configuration
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
NEXT_PUBLIC_COGNITO_DOMAIN=<COGNITO_DOMAIN>
NEXT_PUBLIC_IDENTITY_POOL_ID=<IDENTITY_POOL_ID>

# OAuth (Optional)
NEXT_PUBLIC_GOOGLE_CLIENT_ID=<GOOGLE_CLIENT_ID>

# Feature Flags
NEXT_PUBLIC_USE_STREAMING=true
```

**Note**: Replace `<...>` with actual values.

## Deployment Flow

| Bước | Mô tả |
|------|-------|
| 1 | Developer push code → GitHub Repository |
| 2 | GitHub webhook → AWS Amplify |
| 3 | Amplify Build (STANDARD_8GB) |
| 3.1 | npm install -g pnpm |
| 3.2 | pnpm install |
| 3.3 | pnpm build |
| 4 | Deploy to CloudFront CDN |
| 5 | Invalidate cache |
| 6 | App live at https://<APP_ID>.amplifyapp.com hoặc custom domain |

## Build Specification (amplify.yml)

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm install -g pnpm
        - pnpm install
    build:
      commands:
        - pnpm build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
      - .pnpm-store/**/*
```

## Custom Domain Configuration

**Domain**: Use Route 53 or other domain provider

**DNS Records**:
```
# Root domain
<YOUR_DOMAIN> → CNAME → <CLOUDFRONT_DISTRIBUTION>.cloudfront.net

# WWW subdomain
www.<YOUR_DOMAIN> → CNAME → <CLOUDFRONT_DISTRIBUTION>.cloudfront.net

# SSL Certificate Verification (ACM)
_<VERIFICATION_TOKEN>.<YOUR_DOMAIN> → CNAME → _<ACM_TOKEN>.acm-validations.aws.
```

**Certificate**: AWS Certificate Manager (ACM) - Amplify Managed

## Custom Rules (Routing)

```json
{
  "source": "/<*>",
  "target": "/index.html",
  "status": "404-200"
}
```

Redirect all 404 requests to `/index.html` for Next.js App Router to handle client-side routing.

## Performance Optimization

### 1. Code Splitting
- Automatic route-based splitting (Next.js)
- Dynamic imports for heavy components
- Lazy loading for images

### 2. Caching Strategy
- TanStack Query for API caching
- Service Worker for offline support
- CDN caching (Amplify)

### 3. Bundle Optimization
- Tree shaking
- Minification
- Compression (gzip/brotli)

## Check Deployment

### 1. Check Amplify App

```bash
# List all Amplify apps
aws amplify list-apps --region <REGION>

# Get app details
aws amplify get-app --app-id <APP_ID> --region <REGION>

# List branches
aws amplify list-branches --app-id <APP_ID> --region <REGION>
```

### 2. Check Custom Domain

```bash
# List domain associations
aws amplify list-domain-associations \
  --app-id <APP_ID> \
  --region <REGION>

# Check DNS records
dig <YOUR_DOMAIN>
dig www.<YOUR_DOMAIN>
```

### 3. Check Build Jobs

```bash
# List recent jobs
aws amplify list-jobs \
  --app-id <APP_ID> \
  --branch-name main \
  --region <REGION> \
  --max-results 5

# Get job details
aws amplify get-job \
  --app-id <APP_ID> \
  --branch-name main \
  --job-id <JOB_ID> \
  --region <REGION>
```

## Danh sách kiểm tra

- [ ] Next.js project created
- [ ] Dependencies installed (pnpm)
- [ ] TypeScript configured
- [ ] Environment variables setup (Amplify Console)
- [ ] Amplify app created
- [ ] Git repository connected
- [ ] Build specification configured (pnpm build)
- [ ] Custom domain setup (optional)
- [ ] SSL certificate verified (ACM)
- [ ] Deployment successful (CloudFront CDN)

## Completion Checklist

After completing Frontend section, you should:

- [ ] Understand Lexi frontend architecture
- [ ] Know how to initialize Next.js project with App Router
- [ ] Understand pnpm package manager usage
- [ ] Know how to create API client connecting to backend
- [ ] Know how to create WebSocket client for real-time
- [ ] Understand building components (Auth, Flashcard, Speaking)
- [ ] Know how to deploy to Amplify from GitHub
- [ ] Know how to setup custom domain with Route 53
- [ ] Understand build specification with pnpm
- [ ] Understand CloudFront CDN caching strategy

## Next Steps

You've completed Frontend! Continue to [AI & Voice Integration](../5.5-AI-Voice/) to connect Bedrock, Transcribe, Polly.

