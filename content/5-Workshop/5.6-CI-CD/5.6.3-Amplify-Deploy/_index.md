---
title: "Amplify Auto-Deploy"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

## Overview

**AWS Amplify** automatically deploys frontend when code is pushed to Git. This section guides you through configuring auto-deployment.

## Amplify Deployment Flow

| Step | Description |
|------|-------------|
| 1 | Git Push → GitHub/GitLab |
| 2 | Webhook → Amplify |
| 3 | Amplify Build: Provision build environment |
| 4 | npm ci (install dependencies) |
| 5 | npm run build (Next.js build) |
| 6 | Deploy to CDN |
| 7 | Invalidate cache |
| 8 | App live at: https://main.d<APP_ID>.amplifyapp.com |

## Step 1: Connect Repository

### Via Amplify Console

1. Visit [Amplify Console](https://console.aws.amazon.com/amplify/)
2. Click **"New app" → "Host web app"**
3. Select Git provider (GitHub/GitLab/Bitbucket)
4. Authorize Amplify
5. Select repository: `lexi`
6. Select branch: `main`

### Via CLI

```bash
cd lexi-fe

# Add hosting
amplify add hosting

# Select:
# ? Select the plugin module: Hosting with Amplify Console
# ? Choose a type: Continuous deployment (Git-based)

# Publish
amplify publish
```

## Step 2: Configure Build Settings

Amplify automatically detects `amplify.yml`. Verify configuration:

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

## Step 3: Setup Environment Variables

In Amplify Console → **App settings → Environment variables**:

```
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
```

## Step 4: Trigger Deployment

```bash
# Push code
git add .
git commit -m "Update frontend"
git push origin main

# Amplify automatically:
# 1. Detects push
# 2. Starts build
# 3. Deploys
```

View build progress in Amplify Console.

## Step 5: Setup Branch Deployments

Amplify can deploy multiple branches:

```
main → Production (main.d<APP_ID>.amplifyapp.com)
develop → Staging (develop.d<APP_ID>.amplifyapp.com)
feature/* → Preview (pr-<NUMBER>.d<APP_ID>.amplifyapp.com)
```

**Configuration**:
1. Go to **App settings → Branch deployments**
2. Click **"Connect branch"**
3. Select branch (e.g., `develop`)
4. Save

## Step 6: Setup Custom Domain

```bash
# Via CLI
amplify add domain

# Or via Console:
# 1. Domain management → Add domain
# 2. Enter domain: lexi.example.com
# 3. Configure DNS records
# 4. Wait for SSL certificate
```

**DNS Configuration**:
```
Type: CNAME
Name: lexi
Value: <AMPLIFY_DOMAIN>
```

## Step 7: Setup Notifications

Receive notifications when deployment succeeds/fails:

1. Go to **App settings → Notifications**
2. Click **"Add notification"**
3. Select SNS topic or email
4. Select events: Build succeeded, Build failed

## Step 8: Monitor Deployments

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

Amplify automatically performs blue/green:
- Build new version
- Test new version
- Switch traffic
- Keep old version (rollback if needed)

### 2. Rollback

```bash
# Via Console:
# 1. Go to Deployments
# 2. Select previous version
# 3. Click "Redeploy this version"

# Via CLI:
aws amplify start-deployment \
  --app-id <APP_ID> \
  --branch-name main \
  --job-id <PREVIOUS_JOB_ID>
```

### 3. Preview Deployments

Pull requests automatically create previews:
```
PR #123 → https://pr-123.d<APP_ID>.amplifyapp.com
```

## Performance Optimization

### 1. Build Cache

Amplify caches `node_modules` and `.next/cache`:
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

Amplify automatically:
- Gzip/Brotli compression
- HTTP/2
- Global CDN
- SSL/TLS

## Troubleshooting

### Issue: Build failed

```bash
# Check build logs in Amplify Console
# Common issues:
# - Missing environment variables
# - Node version mismatch
# - Build command errors
```

**Solution**:
1. Check `amplify.yml` syntax
2. Verify environment variables
3. Test build locally: `npm run build`

### Issue: Deployment slow

**Solution**:
1. Enable build cache
2. Use `npm ci` instead of `npm install`
3. Optimize dependencies

### Issue: Custom domain not working

```bash
# Check DNS records
dig lexi.example.com

# Check SSL certificate
aws amplify get-domain-association \
  --app-id <APP_ID> \
  --domain-name lexi.example.com
```

## Checklist

- [ ] Repository connected
- [ ] Build settings configured
- [ ] Environment variables setup
- [ ] Deployment successful
- [ ] App accessible
- [ ] Custom domain setup (if applicable)
- [ ] Notifications setup
- [ ] Branch deployments configured

## Next Steps

Frontend CI/CD is complete! Continue to [Monitoring](../../5.7-Monitoring/) to set up monitoring.
