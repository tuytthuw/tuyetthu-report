---
title: "SAM Deployment"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Tổng quan

**AWS SAM** (Serverless Application Model) giúp deploy Lambda, API Gateway, DynamoDB một cách dễ dàng. Section này hướng dẫn deploy backend với SAM.

## SAM Deployment Flow

SAM deployment diễn ra theo các bước:
1. **sam build** - Compile Python code, cài đặt dependencies
2. **sam deploy** - Upload lên S3, tạo CloudFormation stack
3. **Deploy resources** - Lambda, API Gateway, DynamoDB, S3, IAM roles

## Bước 1: Cài đặt SAM CLI

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

## Bước 2: Build Application

```bash
cd lexi-be

# Build
sam build

# Kết quả: .aws-sam/build/ directory
```

**SAM build sẽ**: Compile Python code, cài đặt dependencies, package Lambda functions, chuẩn bị CloudFormation template

## Bước 3: Deploy lần đầu (Guided)

```bash
# Deploy với guided mode
sam deploy --guided
```

**Trả lời prompts**:

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

## Bước 4: Deploy tiếp theo (Automatic)

```bash
# Deploy với config đã lưu
sam deploy

# Hoặc deploy specific stack
sam deploy --stack-name lexi-be --region ap-southeast-1
```

## Bước 5: Kiểm tra Deployment

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

## Bước 6: Test Deployed APIs

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

## Bước 7: Update Deployment

```bash
# Sau khi thay đổi code
sam build
sam deploy

# SAM sẽ:
# 1. Detect changes
# 2. Create changeset
# 3. Execute changeset
# 4. Update resources
```

## Bước 8: Rollback (nếu cần)

```bash
# Rollback stack
aws cloudformation cancel-update-stack \
  --stack-name lexi-be \
  --region <REGION>

# Hoặc delete và redeploy
sam delete --stack-name lexi-be --region <REGION>
sam deploy
```

## SAM Configuration File

File `samconfig.toml` lưu cấu hình:

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

## Khắc phục sự cố

### Vấn đề: Build failed

```bash
# Check Python version
python --version  # Should be 3.12+

# Check requirements.txt
cat requirements.txt
```

### Vấn đề: Deploy failed - Stack exists

```bash
# Update existing stack
sam deploy --no-confirm-changeset

# Or delete and recreate
sam delete --stack-name lexi-be
sam deploy --guided
```

### Vấn đề: Lambda function error

```bash
# View logs
sam logs -n <FUNCTION_NAME> --stack-name lexi-be --tail

# Invoke function locally
sam local invoke <FUNCTION_NAME> -e events/event.json
```

## Danh sách kiểm tra

- [ ] SAM CLI đã được cài đặt
- [ ] sam build thành công
- [ ] sam deploy thành công
- [ ] Stack outputs có API URL
- [ ] API endpoints hoạt động
- [ ] Lambda functions deployed
- [ ] samconfig.toml đã được tạo

## Bước Tiếp Theo

Tiếp tục đến [Amplify Deploy](../5.6.3-Amplify-Deploy/) để deploy frontend.
