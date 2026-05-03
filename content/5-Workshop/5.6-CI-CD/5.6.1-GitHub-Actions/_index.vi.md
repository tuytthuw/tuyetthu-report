---
title: "GitHub Actions Setup"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Tổng quan

**GitHub Actions** cung cấp CI/CD automation cho Lexi. Section này hướng dẫn setup workflow tự động deploy backend và frontend.

## Workflow Architecture

| Bước | Mô tả |
|------|-------|
| 1 | Git Push → GitHub |
| 2 | GitHub Actions triggered |
| 3 | Backend Workflow: SAM Deploy → Lambda |
| 4 | Frontend Workflow: Amplify Deploy → Amplify CDN |

## Bước 1: Tạo GitHub Repository

```bash
# Initialize git (nếu chưa có)
git init

# Add remote
git remote add origin https://github.com/<USERNAME>/lexi.git

# Push code
git add .
git commit -m "Initial commit"
git push -u origin main
```

## Bước 2: Setup GitHub Secrets

Vào **Settings → Secrets and variables → Actions**, thêm secrets:

```
AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY>
AWS_SECRET_ACCESS_KEY=<YOUR_SECRET_KEY>
AWS_REGION=ap-southeast-1
```

**Lấy AWS credentials**:

```bash
# Tạo IAM user cho GitHub Actions
aws iam create-user --user-name github-actions-lexi

# Attach policy
aws iam attach-user-policy \
  --user-name github-actions-lexi \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Tạo access key
aws iam create-access-key --user-name github-actions-lexi
```

## Bước 3: Tạo Backend Workflow

Tạo `.github/workflows/deploy-backend.yml`:

```yaml
name: Deploy Backend

on:
  push:
    branches: [main, develop]
    paths:
      - 'lexi-be/**'
      - '.github/workflows/deploy-backend.yml'
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Setup AWS SAM
        uses: aws-actions/setup-sam@v2

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: SAM Build
        working-directory: ./lexi-be
        run: sam build

      - name: SAM Deploy
        working-directory: ./lexi-be
        run: |
          sam deploy \
            --no-confirm-changeset \
            --no-fail-on-empty-changeset \
            --stack-name lexi-be \
            --capabilities CAPABILITY_IAM \
            --region ${{ secrets.AWS_REGION }}

      - name: Get API Endpoint
        working-directory: ./lexi-be
        run: |
          sam list stack-outputs \
            --stack-name lexi-be \
            --region ${{ secrets.AWS_REGION }}
```

## Bước 4: Tạo Frontend Workflow

Tạo `.github/workflows/deploy-frontend.yml`:

```yaml
name: Deploy Frontend

on:
  push:
    branches: [main]
    paths:
      - 'lexi-fe/**'
      - '.github/workflows/deploy-frontend.yml'
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
          cache-dependency-path: lexi-fe/package-lock.json

      - name: Install dependencies
        working-directory: ./lexi-fe
        run: npm ci

      - name: Build
        working-directory: ./lexi-fe
        env:
          NEXT_PUBLIC_API_URL: ${{ secrets.NEXT_PUBLIC_API_URL }}
          NEXT_PUBLIC_WEBSOCKET_URL: ${{ secrets.NEXT_PUBLIC_WEBSOCKET_URL }}
          NEXT_PUBLIC_COGNITO_REGION: ${{ secrets.AWS_REGION }}
          NEXT_PUBLIC_COGNITO_USER_POOL_ID: ${{ secrets.COGNITO_USER_POOL_ID }}
          NEXT_PUBLIC_COGNITO_CLIENT_ID: ${{ secrets.COGNITO_CLIENT_ID }}
        run: npm run build

      - name: Deploy to Amplify
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
      
      - name: Trigger Amplify deployment
        run: |
          aws amplify start-job \
            --app-id ${{ secrets.AMPLIFY_APP_ID }} \
            --branch-name main \
            --job-type RELEASE
```

## Bước 6: Setup Branch Protection

Vào **Settings → Branches → Add rule**:

```
Branch name pattern: main

☑ Require a pull request before merging
☑ Require status checks to pass before merging
  - deploy-backend
  - deploy-frontend
☑ Require branches to be up to date before merging
```

## Khắc phục sự cố

### Vấn đề: Workflow failed - AWS credentials

```bash
# Verify credentials
aws sts get-caller-identity
```

**Giải pháp**: Check GitHub Secrets có đúng không

### Vấn đề: SAM build failed

```bash
# Test locally
cd lexi-be
sam build
```

**Giải pháp**: Check Python dependencies trong `requirements.txt`

### Vấn đề: Amplify deployment failed

```bash
# Check Amplify app
aws amplify list-apps --region <REGION>
```

**Giải pháp**: Verify Amplify App ID trong secrets

## Danh sách kiểm tra

- [ ] GitHub repository đã được tạo
- [ ] GitHub Secrets đã được setup
- [ ] Backend workflow đã được tạo
- [ ] Frontend workflow đã được tạo
- [ ] Workflows chạy thành công
- [ ] Branch protection đã được setup

## Bước Tiếp Theo

Tiếp tục đến [SAM Deploy](../5.6.2-SAM-Deploy/) để hiểu chi tiết về backend deployment.
