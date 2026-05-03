---
title: "Amplify Auto-Deploy"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

## Tổng quan

**AWS Amplify** tự động deploy frontend khi code được push lên Git. Section này hướng dẫn cấu hình auto-deployment.

## Amplify Deployment Flow

| Bước | Mô tả |
|------|-------|
| 1 | Git Push → GitHub/GitLab |
| 2 | Webhook → Amplify |
| 3 | Amplify Build: Provision build environment |
| 4 | npm ci (install dependencies) |
| 5 | npm run build (Next.js build) |
| 6 | Deploy to CDN |
| 7 | Invalidate cache |
| 8 | App live at: https://main.d<APP_ID>.amplifyapp.com |

## Bước 1: Kết nối Repository

### Via Amplify Console

1. Truy cập [Amplify Console](https://console.aws.amazon.com/amplify/)
2. Click **"New app" → "Host web app"**
3. Chọn Git provider (GitHub/GitLab/Bitbucket)
4. Authorize Amplify
5. Chọn repository: `lexi`
6. Chọn branch: `main`

### Via CLI

```bash
cd lexi-fe

# Add hosting
amplify add hosting

# Chọn:
# ? Select the plugin module: Hosting with Amplify Console
# ? Choose a type: Continuous deployment (Git-based)

# Publish
amplify publish
```

## Bước 2: Cấu hình Build Settings

Amplify tự động detect `amplify.yml`. Verify cấu hình:

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

## Bước 3: Setup Environment Variables

Trong Amplify Console → **App settings → Environment variables**:

```
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
```

## Bước 4: Trigger Deployment

```bash
# Push code
git add .
git commit -m "Update frontend"
git push origin main

# Amplify tự động:
# 1. Detect push
# 2. Start build
# 3. Deploy
```

Xem build progress trong Amplify Console.

## Bước 5: Setup Branch Deployments

Amplify có thể deploy nhiều branches:

```
main → Production (main.d<APP_ID>.amplifyapp.com)
develop → Staging (develop.d<APP_ID>.amplifyapp.com)
feature/* → Preview (pr-<NUMBER>.d<APP_ID>.amplifyapp.com)
```

**Cấu hình**:
1. Vào **App settings → Branch deployments**
2. Click **"Connect branch"**
3. Chọn branch (e.g., `develop`)
4. Save

## Bước 6: Setup Custom Domain

```bash
# Via CLI
amplify add domain

# Hoặc via Console:
# 1. Domain management → Add domain
# 2. Nhập domain: lexi.example.com
# 3. Configure DNS records
# 4. Wait for SSL certificate
```

**DNS Configuration**:
```
Type: CNAME
Name: lexi
Value: <AMPLIFY_DOMAIN>
```

> **📸 CẦN THÊM**: Thêm screenshot Custom domain setup

## Bước 7: Setup Notifications

Nhận thông báo khi deployment thành công/thất bại:

1. Vào **App settings → Notifications**
2. Click **"Add notification"**
3. Chọn SNS topic hoặc email
4. Chọn events: Build succeeded, Build failed

> **📸 CẦN THÊM**: Thêm screenshot Notifications setup

## Bước 8: Monitor Deployments

```bash
# List apps
aws amplify list-apps --region <REGION>

# Get app details
aws amplify get-app \
  --app-id <APP_ID> \
  --region <REGION>

# List branches
aws amplify list-branches \
  --app-id <APP_ID> \
  --region <REGION>

# Get build status
aws amplify list-jobs \
  --app-id <APP_ID> \
  --branch-name main \
  --region <REGION>
```

## Deployment Strategies

### 1. Blue/Green Deployment

Amplify tự động thực hiện blue/green:
- Build new version
- Test new version
- Switch traffic
- Keep old version (rollback nếu cần)

### 2. Rollback

```bash
# Via Console:
# 1. Vào Deployments
# 2. Chọn previous version
# 3. Click "Redeploy this version"

# Via CLI:
aws amplify start-deployment \
  --app-id <APP_ID> \
  --branch-name main \
  --job-id <PREVIOUS_JOB_ID>
```

### 3. Preview Deployments

Pull requests tự động tạo preview:
```
PR #123 → https://pr-123.d<APP_ID>.amplifyapp.com
```

## Performance Optimization

### 1. Build Cache

Amplify cache `node_modules` và `.next/cache`:
```yaml
cache:
  paths:
    - node_modules/**/*
    - .next/cache/**/*
```

### 2. Build Time Optimization

```yaml
# Use npm ci instead of npm install
preBuild:
  commands:
    - npm ci --prefer-offline --no-audit
```

### 3. CDN Configuration

Amplify tự động:
- Gzip/Brotli compression
- HTTP/2
- Global CDN
- SSL/TLS

## Khắc phục sự cố

### Vấn đề: Build failed

```bash
# Check build logs trong Amplify Console
# Common issues:
# - Missing environment variables
# - Node version mismatch
# - Build command errors
```

**Giải pháp**:
1. Check `amplify.yml` syntax
2. Verify environment variables
3. Test build locally: `npm run build`

### Vấn đề: Deployment slow

**Giải pháp**:
1. Enable build cache
2. Use `npm ci` instead of `npm install`
3. Optimize dependencies

### Vấn đề: Custom domain not working

```bash
# Check DNS records
dig lexi.example.com

# Check SSL certificate
aws amplify get-domain-association \
  --app-id <APP_ID> \
  --domain-name lexi.example.com
```

## Danh sách kiểm tra

- [ ] Repository đã được kết nối
- [ ] Build settings đã được cấu hình
- [ ] Environment variables đã được setup
- [ ] Deployment thành công
- [ ] App có thể truy cập
- [ ] Custom domain đã được setup (nếu có)
- [ ] Notifications đã được setup
- [ ] Branch deployments đã được cấu hình

## Bước Tiếp Theo

Frontend CI/CD đã hoàn thành! Tiếp tục đến [Monitoring](../../5.7-Monitoring/) để setup giám sát.
