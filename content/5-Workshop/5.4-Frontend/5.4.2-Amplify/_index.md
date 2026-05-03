---
title: "Amplify Deployment"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Overview

**AWS Amplify** provides hosting for Next.js with automatic CI/CD. This section guides you through deploying Lexi frontend to Amplify.

## Step 1: Install Amplify CLI

```bash
npm install -g @aws-amplify/cli
amplify --version
```

## Step 2: Initialize Amplify Project

```bash
cd lexi-fe
amplify init
```

## Step 3: Create amplify.yml

Create `amplify.yml` at project root:

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

## Step 4: Connect Git Repository

### Option A: Via Amplify Console

1. Go to [Amplify Console](https://console.aws.amazon.com/amplify/)
2. Click "New app" → "Host web app"
3. Select GitHub/GitLab/Bitbucket
4. Authorize and select repository
5. Configure build settings
6. Click "Save and deploy"

### Option B: Via CLI

```bash
amplify add hosting
# Choose: Hosting with Amplify Console
# Choose: Continuous deployment (Git-based)
```

## Step 5: Configure Environment Variables

In Amplify Console → App settings → Environment variables:

```
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
```

## Step 6: Deploy

```bash
amplify publish
```

## Monitoring Deployment

```bash
amplify status
amplify console
```

## Troubleshooting

### Issue: Build failed
- Check build logs in Amplify Console
- Verify environment variables
- Check Node version

### Issue: Authentication not working
- Verify Cognito configuration
- Check User Pool ID and Client ID

## Checklist

- [ ] Amplify CLI installed
- [ ] Project initialized
- [ ] amplify.yml created
- [ ] Git repository connected
- [ ] Environment variables configured
- [ ] Deployment successful

## Next Steps

Continue to [WebSocket Integration](../5.4.3-WebSocket/) for real-time speaking.
