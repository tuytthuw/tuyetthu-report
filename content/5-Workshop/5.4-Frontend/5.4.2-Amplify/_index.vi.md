---
title: "Amplify Deployment"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Tổng quan

**AWS Amplify** cung cấp hosting cho Next.js với CI/CD tự động. Section này hướng dẫn deploy Lexi frontend lên Amplify.

> **📸 CẦN THÊM**: Thêm screenshot Amplify Console

## Bước 1: Cài đặt Amplify CLI

```bash
# Cài đặt Amplify CLI global
npm install -g @aws-amplify/cli

# Kiểm tra version
amplify --version
```

> **📸 CẦN THÊM**: Thêm screenshot terminal với amplify version

## Bước 2: Khởi tạo Amplify Project

```bash
# Di chuyển vào thư mục frontend
cd lexi-fe

# Khởi tạo Amplify
amplify init
```

**Trả lời các prompts**:

```
? Enter a name for the project: lexife
? Initialize the project with the above configuration? Yes
? Select the authentication method you want to use: AWS profile
? Please choose the profile you want to use: default
```

> **📸 CẦN THÊM**: Thêm screenshot amplify init prompts

**Kết quả**:
- Thư mục `amplify/` được tạo
- File `amplify.yml` được tạo
- Amplify backend được khởi tạo

## Bước 3: Cấu hình Amplify Auth

```bash
# Thêm authentication
amplify add auth
```

**Chọn cấu hình**:

```
? Do you want to use the default authentication and security configuration? 
  > Manual configuration

? Select the authentication/authorization services that you want to use:
  > User Sign-Up, Sign-In, connected with AWS IAM controls

? Please provide a friendly name for your resource:
  > lexiAuth

? Please enter a name for your identity pool:
  > lexiIdentityPool

? Allow unauthenticated logins? 
  > No

? Do you want to enable 3rd party authentication providers?
  > No

? Do you want to add User Pool Groups?
  > No

? Do you want to add an admin queries API?
  > No

? Multifactor authentication (MFA) user login options:
  > OFF

? Email based user registration/forgot password:
  > Enabled

? Please specify an email verification subject:
  > Your Lexi verification code

? Please specify an email verification message:
  > Your verification code is {####}

? Do you want to override the default password policy?
  > No

? What attributes are required for signing up?
  > Email

? Specify the app's refresh token expiration period (in days):
  > 30

? Do you want to specify the user attributes this app can read and write?
  > No

? Do you want to enable any of the following capabilities?
  > (None)

? Do you want to use an OAuth flow?
  > No
```

> **📸 CẦN THÊM**: Thêm screenshot amplify add auth configuration

## Bước 4: Cấu hình Amplify trong Next.js

Tạo `src/amplify-config.ts`:

```typescript
import { Amplify } from 'aws-amplify';

export const configureAmplify = () => {
  Amplify.configure({
    Auth: {
      Cognito: {
        userPoolId: process.env.NEXT_PUBLIC_COGNITO_USER_POOL_ID!,
        userPoolClientId: process.env.NEXT_PUBLIC_COGNITO_CLIENT_ID!,
        region: process.env.NEXT_PUBLIC_COGNITO_REGION!,
        loginWith: {
          email: true,
        },
        signUpVerificationMethod: 'code',
        userAttributes: {
          email: {
            required: true,
          },
        },
        passwordFormat: {
          minLength: 8,
          requireLowercase: true,
          requireUppercase: true,
          requireNumbers: true,
          requireSpecialCharacters: true,
        },
      },
    },
  });
};
```

Cập nhật `app/layout.tsx`:

```typescript
import { configureAmplify } from '@/src/amplify-config';

// Configure Amplify
configureAmplify();

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  // ... rest of layout
}
```

## Bước 5: Tạo amplify.yml

Tạo file `amplify.yml` ở root project:

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
      - .next/cache/**/*
```

> **📸 CẦN THÊM**: Thêm screenshot amplify.yml file

## Bước 6: Push lên Amplify

```bash
# Push changes lên Amplify
amplify push

# Hoặc push và publish
amplify publish
```

**Amplify sẽ**:
1. Tạo CloudFormation stack
2. Tạo Cognito User Pool (nếu chưa có)
3. Deploy frontend lên Amplify Hosting
4. Cấu hình CI/CD

> **📸 CẦN THÊM**: Thêm screenshot amplify push output

## Bước 7: Kết nối Git Repository

### Option A: Kết nối qua Amplify Console

1. Truy cập [Amplify Console](https://console.aws.amazon.com/amplify/)
2. Click "New app" → "Host web app"
3. Chọn GitHub/GitLab/Bitbucket
4. Authorize Amplify
5. Chọn repository và branch
6. Cấu hình build settings (sử dụng amplify.yml)
7. Click "Save and deploy"

> **📸 CẦN THÊM**: Thêm screenshot Amplify Console - Connect repository

### Option B: Kết nối qua CLI

```bash
# Kết nối với GitHub
amplify add hosting

# Chọn:
# ? Select the plugin module to execute: Hosting with Amplify Console
# ? Choose a type: Continuous deployment (Git-based deployments)
```

## Bước 8: Cấu hình Environment Variables

Trong Amplify Console:

1. Vào App settings → Environment variables
2. Thêm các biến:

```
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
```

> **📸 CẦN THÊM**: Thêm screenshot Amplify Console - Environment variables

## Bước 9: Cấu hình Custom Domain (Tùy chọn)

```bash
# Thêm custom domain
amplify add domain
```

Hoặc trong Amplify Console:
1. Vào Domain management
2. Click "Add domain"
3. Nhập domain name (e.g., `lexi.example.com`)
4. Cấu hình DNS records

> **📸 CẦN THÊM**: Thêm screenshot Amplify Console - Custom domain

## Bước 10: Kiểm tra Deployment

```bash
# Xem status
amplify status

# Xem URL
amplify console
```

**Truy cập URL**:
- Development: `https://dev.d<APP_ID>.amplifyapp.com`
- Production: `https://main.d<APP_ID>.amplifyapp.com`

> **📸 CẦN THÊM**: Thêm screenshot deployed app trong browser

## Monitoring Deployment

### Xem Build Logs

```bash
# Mở Amplify Console
amplify console

# Hoặc xem logs trực tiếp
aws amplify list-apps --region <REGION>
```

### Kiểm tra Build Status

Trong Amplify Console:
1. Vào app
2. Click vào branch (e.g., `main`)
3. Xem build history

> **📸 CẦN THÊM**: Thêm screenshot Amplify Console - Build history

## Rollback Deployment

```bash
# Rollback về version trước
# Trong Amplify Console:
# 1. Vào Deployments
# 2. Chọn version muốn rollback
# 3. Click "Redeploy this version"
```

## Khắc phục sự cố

### Vấn đề: Build failed

```bash
# Kiểm tra build logs trong Amplify Console
# Common issues:
# - Missing environment variables
# - Node version mismatch
# - Build command errors
```

**Giải pháp**:
1. Kiểm tra `amplify.yml` build commands
2. Verify environment variables
3. Check Node version (use `.nvmrc` file)

### Vấn đề: Authentication not working

```bash
# Verify Cognito configuration
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

**Giải pháp**:
1. Verify User Pool ID và Client ID
2. Check Amplify Auth configuration
3. Ensure CORS is configured

### Vấn đề: API calls failing

```bash
# Check API Gateway endpoint
curl https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/health
```

**Giải pháp**:
1. Verify API URL in environment variables
2. Check CORS configuration
3. Verify Cognito Authorizer

## CI/CD Workflow

Amplify tự động trigger build khi:
1. Push code lên branch được kết nối
2. Merge pull request
3. Manual trigger trong Console

**Build process**:
```
1. Clone repository
   ↓
2. Install dependencies (npm ci)
   ↓
3. Run build (npm run build)
   ↓
4. Deploy to CDN
   ↓
5. Invalidate cache
```

## Danh sách kiểm tra

- [ ] Amplify CLI đã được cài đặt
- [ ] Amplify project đã được khởi tạo
- [ ] Auth đã được cấu hình
- [ ] amplify.yml đã được tạo
- [ ] Git repository đã được kết nối
- [ ] Environment variables đã được setup
- [ ] Custom domain đã được cấu hình (nếu có)
- [ ] Deployment thành công
- [ ] App có thể truy cập được

## Bước Tiếp Theo

Tiếp tục đến [WebSocket Integration](../5.4.3-WebSocket/) để tích hợp real-time speaking.
