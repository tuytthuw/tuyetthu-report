---
title: "SAM Deployment"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Overview

**AWS SAM** (Serverless Application Model) makes it easy to deploy Lambda, API Gateway, and DynamoDB. This section guides you through backend deployment with SAM.

## SAM Deployment Flow

SAM deployment follows these steps:
1. **sam build** - Compile Python code, install dependencies
2. **sam deploy** - Upload to S3, create CloudFormation stack
3. **Deploy resources** - Lambda, API Gateway, DynamoDB, S3, IAM roles

## Step 1: Install SAM CLI

```bash
# macOS
brew install aws-sam-cli

# Linux
pip install aws-sam-cli

# Windows
choco install aws-sam-cli

# Verify
sam --version
```

## Step 2: Build Application

```bash
cd lexi-be

# Build
sam build

# Result: .aws-sam/build/ directory
```

**SAM build will**: Compile Python code, install dependencies, package Lambda functions, prepare CloudFormation template

## Step 3: First Deployment (Guided)

```bash
# Deploy with guided mode
sam deploy --guided
```

**Answer prompts**:

```
Stack Name [lexi-be]: lexi-be
AWS Region [ap-southeast-1]: ap-southeast-1
Parameter AuthBaseStackName [lexi-auth-base]: lexi-auth-base
Parameter DatabaseStackName [lexi-database]: lexi-database
Confirm changes before deploy [Y/n]: Y
Allow SAM CLI IAM role creation [Y/n]: Y
Disable rollback [y/N]: N
Save arguments to configuration file [Y/n]: Y
SAM configuration file [samconfig.toml]: samconfig.toml
SAM configuration environment [default]: default
```

## Step 4: Subsequent Deployments (Automatic)

```bash
# Deploy with saved config
sam deploy

# Or deploy specific stack
sam deploy --stack-name lexi-be --region ap-southeast-1
```

## Step 5: Verify Deployment

```bash
# List stacks
aws cloudformation list-stacks \
  --region <REGION> \
  --query 'StackSummaries[?StackStatus!=`DELETE_COMPLETE`]'

# Describe stack
aws cloudformation describe-stacks \
  --stack-name lexi-be \
  --region <REGION>

# Get outputs
sam list stack-outputs \
  --stack-name lexi-be \
  --region <REGION>
```

## Step 6: Test Deployed APIs

```bash
# Get API URL
API_URL=$(aws cloudformation describe-stacks \
  --stack-name lexi-be \
  --region <REGION> \
  --query 'Stacks[0].Outputs[?OutputKey==`ApiUrl`].OutputValue' \
  --output text)

# Test health endpoint
curl $API_URL/health

# Test with auth
curl -H "Authorization: Bearer <TOKEN>" \
  $API_URL/profile
```

## Step 7: Update Deployment

```bash
# After code changes
sam build
sam deploy

# SAM will:
# 1. Detect changes
# 2. Create changeset
# 3. Execute changeset
# 4. Update resources
```

## Step 8: Rollback (if needed)

```bash
# Rollback stack
aws cloudformation cancel-update-stack \
  --stack-name lexi-be \
  --region <REGION>

# Or delete and redeploy
sam delete --stack-name lexi-be --region <REGION>
sam deploy
```

## SAM Configuration File

File `samconfig.toml` stores configuration:

```toml
version = 0.1

[default.deploy.parameters]
stack_name = "lexi-be"
region = "ap-southeast-1"
capabilities = "CAPABILITY_IAM"
parameter_overrides = "AuthBaseStackName=lexi-auth-base DatabaseStackName=lexi-database"
confirm_changeset = true
resolve_s3 = true
```

## Troubleshooting

### Issue: Build failed

```bash
# Check Python version
python --version  # Should be 3.12+

# Check requirements.txt
cat requirements.txt
```

### Issue: Deploy failed - Stack exists

```bash
# Update existing stack
sam deploy --no-confirm-changeset

# Or delete and recreate
sam delete --stack-name lexi-be
sam deploy --guided
```

### Issue: Lambda function error

```bash
# View logs
sam logs -n <FUNCTION_NAME> --stack-name lexi-be --tail

# Invoke function locally
sam local invoke <FUNCTION_NAME> -e events/event.json
```

## Checklist

- [ ] SAM CLI installed
- [ ] sam build successful
- [ ] sam deploy successful
- [ ] Stack outputs contain API URL
- [ ] API endpoints working
- [ ] Lambda functions deployed
- [ ] samconfig.toml created

## Next Steps

Continue to [Amplify Deploy](../5.6.3-Amplify-Deploy/) to deploy the frontend.
