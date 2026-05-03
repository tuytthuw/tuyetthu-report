---
title: "Amazon Amplify"
date: "2025-12-07"
weight: 1
chapter: false
pre: "<b> 5.4.1 </b>"
---

## Deploy Frontend with Amplify

**Amazon Amplify** provides 2 ways to deploy Next.js frontend: Amplify Console (automatic) or Amplify CLI (manual).

### Method 1: Amplify Console (Recommended)

**Steps 1-3**: Connect repository
1. Go to **AWS Console → Amplify → Create new app**
2. Select **Host web app**
3. Connect GitHub/GitLab/Bitbucket repository

**Step 4**: Configure build settings

File `amplify.yml` (already in project):
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
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

**Step 5**: Set Environment Variables
```
VITE_API_BASE_URL = <API_Gateway_URL>
VITE_COGNITO_USER_POOL_ID = <User_Pool_ID>
VITE_COGNITO_CLIENT_ID = <Client_ID>
VITE_COGNITO_REGION = ap-southeast-1
```

**Step 6**: Deploy - Amplify will automatically build and deploy when you push code

### Method 2: Amplify CLI (Manual)

```bash
# Install CLI
npm install -g @aws-amplify/cli

# Configure AWS credentials
amplify configure

# Initialize Amplify in project
cd lexi-fe
amplify init

# Add hosting
amplify add hosting
# Select: Hosting with Amplify Console
# Select: Manual deployment

# Deploy
amplify publish
```

### Troubleshooting

**Build failed**: Check `amplify.yml` and `requirements.txt`
```bash
amplify logs --follow
```

**Environment variables not loading**: Verify variables are set in Amplify Console
```bash
# Verify locally
echo $VITE_API_BASE_URL
```

**Deploy timeout**: Increase timeout in Amplify settings (default 30 min)

### Checklist

- [ ] Repository connected to Amplify
- [ ] Build settings configured correctly
- [ ] Environment variables set
- [ ] Build successful
- [ ] Amplify URL working
- [ ] Frontend connected to API Gateway

## Next Steps

Continue to [CloudFront & WAF](../5.4.2-CloudFront&WAF/) to secure the frontend.
