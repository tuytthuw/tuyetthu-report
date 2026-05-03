---
title: "CI/CD Pipeline"
date: "2026-05-02"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Overview

CI/CD (Continuous Integration/Continuous Deployment) automates the build, test, and deploy process. This section guides you through setting up CI/CD for Lexi.

> **📸 TODO**: Add CI/CD pipeline diagram

## CI/CD Architecture

```
Developer → Git Push
    ↓
GitHub/GitLab
    ↓
┌─────────────────┴─────────────────┐
│                                   │
▼                                   ▼
GitHub Actions                  Amplify
│                                   │
├─ Backend Workflow                 ├─ Auto-detect push
│  ├─ Checkout code                 ├─ Build Next.js
│  ├─ Setup Python                  ├─ Deploy to CDN
│  ├─ SAM Build                     └─ Invalidate cache
│  └─ SAM Deploy                    
│                                   
└─ Frontend Workflow                
   ├─ Checkout code                 
   ├─ Setup Node.js                 
   ├─ Build Next.js                 
   └─ Trigger Amplify               
```

## CI/CD Content

CI/CD is divided into 3 main parts:

1. **[GitHub Actions](5.6.1-GitHub-Actions/)** - CI/CD automation
   - Setup GitHub repository
   - Configure secrets
   - Create workflows
   - Branch protection
   - Monitoring

2. **[SAM Deployment](5.6.2-SAM-Deploy/)** - Backend deployment
   - SAM CLI setup
   - Build application
   - Deploy to AWS
   - Rollback strategy
   - Troubleshooting

3. **[Amplify Auto-Deploy](5.6.3-Amplify-Deploy/)** - Frontend deployment
   - Connect repository
   - Configure build settings
   - Environment variables
   - Custom domain
   - Branch deployments

## Deployment Strategies

### 1. Backend Deployment (SAM)

```
Code Change → GitHub
    ↓
GitHub Actions
    ↓
sam build
    ↓
sam deploy
    ↓
CloudFormation Update
    ↓
Lambda Functions Updated
```

**Rollback**: CloudFormation automatic rollback on failure

### 2. Frontend Deployment (Amplify)

```
Code Change → GitHub
    ↓
Amplify Webhook
    ↓
npm ci
    ↓
npm run build
    ↓
Deploy to CDN
    ↓
Invalidate Cache
```

**Rollback**: Redeploy previous version

## Workflow Triggers

### Backend Workflow

```yaml
on:
  push:
    branches: [main, develop]
    paths:
      - 'lexi-be/**'
  workflow_dispatch:  # Manual trigger
```

### Frontend Workflow

```yaml
on:
  push:
    branches: [main]
    paths:
      - 'lexi-fe/**'
  pull_request:
    branches: [main]
```

## Environment Management

### Development
- Branch: `develop`
- Backend: `lexi-be-dev` stack
- Frontend: `develop.d<APP_ID>.amplifyapp.com`

### Staging
- Branch: `staging`
- Backend: `lexi-be-staging` stack
- Frontend: `staging.d<APP_ID>.amplifyapp.com`

### Production
- Branch: `main`
- Backend: `lexi-be` stack
- Frontend: `main.d<APP_ID>.amplifyapp.com` or custom domain

## Monitoring Deployments

### GitHub Actions

```bash
# View workflow runs
gh run list

# View specific run
gh run view <RUN_ID>

# View logs
gh run view <RUN_ID> --log
```

### CloudFormation

```bash
# List stacks
aws cloudformation list-stacks --region <REGION>

# Describe stack events
aws cloudformation describe-stack-events \
  --stack-name lexi-be \
  --region <REGION>
```

### Amplify

```bash
# List apps
aws amplify list-apps --region <REGION>

# List jobs
aws amplify list-jobs \
  --app-id <APP_ID> \
  --branch-name main \
  --region <REGION>
```

## Best Practices

### 1. Automated Testing

```yaml
# Add test step before deploy
- name: Run tests
  run: |
    cd lexi-be
    python -m pytest tests/
```

### 2. Deployment Approval

```yaml
# Require manual approval for production
environment:
  name: production
  url: https://lexi.example.com
```

### 3. Notifications

```yaml
# Notify on failure
- name: Notify on failure
  if: failure()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### 4. Rollback Strategy

- Backend: CloudFormation automatic rollback
- Frontend: Amplify redeploy previous version
- Database: Backup before migration

## Troubleshooting

### Issue: Workflow failed

```bash
# Check workflow logs in GitHub Actions
# Common issues:
# - AWS credentials invalid
# - Build errors
# - Deployment timeout
```

### Issue: Deployment slow

**Optimization**:
- Use caching (npm, pip)
- Parallel jobs
- Optimize build commands

### Issue: Rollback needed

```bash
# Backend
aws cloudformation cancel-update-stack \
  --stack-name lexi-be

# Frontend
# Redeploy previous version in Amplify Console
```

## Checklist

After completing the CI/CD section, you should:

- [ ] Understand Lexi's CI/CD architecture
- [ ] Know how to setup GitHub repository
- [ ] Understand how to configure GitHub Secrets
- [ ] Know how to create GitHub Actions workflows
- [ ] Understand backend deployment with SAM
- [ ] Understand frontend deployment with Amplify
- [ ] Know how to monitor deployments
- [ ] Understand rollback strategies
- [ ] Understand environment management (dev, staging, production)

> **📸 TODO**: Add CI/CD pipeline diagram

> **📸 TODO**: Add screenshot of successful GitHub Actions workflow

## Next Steps

You've completed the CI/CD section! Continue to [Monitoring & Cost Optimization](../5.7-Monitoring/) to learn monitoring and cost optimization.
