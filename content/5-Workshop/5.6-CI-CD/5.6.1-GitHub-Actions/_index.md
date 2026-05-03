---
title: "GitHub Actions Setup"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Overview

**GitHub Actions** provides CI/CD automation for Lexi. This section guides you through setting up workflows to automatically deploy backend and frontend.

## Workflow Architecture

| Step | Description |
|------|-------------|
| 1 | Git Push → GitHub |
| 2 | GitHub Actions triggered |
| 3 | Backend Workflow: SAM Deploy → Lambda |
| 4 | Frontend Workflow: Amplify Deploy → Amplify CDN |

## Step 1: Create GitHub Repository

```bash
# Initialize git (if not already done)
git init

# Add remote
git remote add origin https://github.com/<USERNAME>/lexi.git

# Push code
git add .
git commit -m "Initial commit"
git push -u origin main
```

## Step 2: Setup GitHub Secrets

Go to **Settings → Secrets and variables → Actions**, add secrets:

```
AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY>
AWS_SECRET_ACCESS_KEY=<YOUR_SECRET_KEY>
AWS_REGION=ap-southeast-1
```

**Get AWS credentials**:

```bash
# Create IAM user for GitHub Actions
aws iam create-user --user-name github-actions-lexi

# Attach policy
aws iam attach-user-policy \
  --user-name github-actions-lexi \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Create access key
aws iam create-access-key --user-name github-actions-lexi
```

## Step 3: Create Backend Workflow

Create `.github/workflows/deploy-backend.yml`:

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

## Step 4: Create Frontend Workflow

Create `.github/workflows/deploy-frontend.yml`:

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

## Step 5: Setup Branch Protection

Go to **Settings → Branches → Add rule**:

```
Branch name pattern: main

☑ Require a pull request before merging
☑ Require status checks to pass before merging
  - deploy-backend
  - deploy-frontend
☑ Require branches to be up to date before merging
```

## Troubleshooting

### Issue: Workflow failed - AWS credentials

```bash
# Verify credentials
aws sts get-caller-identity
```

**Solution**: Check GitHub Secrets are correct

### Issue: SAM build failed

```bash
# Test locally
cd lexi-be
sam build
```

**Solution**: Check Python dependencies in `requirements.txt`

### Issue: Amplify deployment failed

```bash
# Check Amplify app
aws amplify list-apps --region <REGION>
```

**Solution**: Verify Amplify App ID in secrets

## Checklist

- [ ] GitHub repository created
- [ ] GitHub Secrets setup
- [ ] Backend workflow created
- [ ] Frontend workflow created
- [ ] Workflows running successfully
- [ ] Branch protection setup

## Next Steps

Continue to [SAM Deploy](../5.6.2-SAM-Deploy/) to understand backend deployment details.
