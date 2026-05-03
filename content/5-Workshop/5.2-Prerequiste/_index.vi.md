---
title: "Prerequisites"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## System Requirements

### Required Tools

1. **Node.js 20.x** - Frontend (Next.js)
2. **Python 3.12** - Backend (Lambda)
3. **AWS CLI v2** - AWS resource management
4. **AWS SAM CLI** - Lambda deployment
5. **Git** - Version control
6. **Docker** (optional) - Local testing

### Installation

#### Node.js 20.x

```bash
nvm install 20
nvm use 20
```

Hoặc tải từ https://nodejs.org/

Kiểm tra version:
```bash
node --version
npm --version
```

**Kết quả mong đợi**: v20.x và 10.x

#### Python 3.12

```bash
brew install python@3.12
```

Ubuntu/Debian:
```bash
sudo apt-get install python3.12 python3.12-venv
```

Windows: Tải từ https://www.python.org/

Kiểm tra version:
```bash
python3 --version
```

**Kết quả mong đợi**: Python 3.12.x

#### AWS CLI v2

```bash
brew install awscli
```

Ubuntu/Debian:
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Windows: Tải từ https://aws.amazon.com/cli/

Kiểm tra version:
```bash
aws --version
```

**Kết quả mong đợi**: aws-cli/2.x

#### AWS SAM CLI

```bash
brew install aws-sam-cli
```

Ubuntu/Debian:
```bash
pip install aws-sam-cli
```

Windows: Tải từ https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html

Kiểm tra version:
```bash
sam --version
```

**Kết quả mong đợi**: SAM CLI 1.x

#### Git

```bash
brew install git
```

Ubuntu/Debian:
```bash
sudo apt-get install git
```

Windows: Tải từ https://git-scm.com/

Kiểm tra version:
```bash
git --version
```

**Kết quả mong đợi**: git version 2.x

## AWS Account Setup

### 1. Create AWS Account

If you don't have one:
1. Visit https://aws.amazon.com/
2. Click "Create an AWS Account"
3. Follow signup process
4. Add payment method

### 2. Create IAM User

**Don't use root account** - Create IAM user with necessary permissions:

```bash
# Or use AWS Console:
# 1. IAM → Users → Create user
# 2. Name: lexi-dev
# 3. Attach policies: AdministratorAccess (for dev)
# 4. Create Access Key
```

### 3. Configure AWS CLI

```bash
aws configure

# Nhập các giá trị:
# AWS Access Key ID: [Your Access Key]
# AWS Secret Access Key: [Your Secret Key]
# Default region name: ap-southeast-1
# Default output format: json
```

Xác minh:
```bash
aws sts get-caller-identity
```

### 4. Set Region

Lexi uses **ap-southeast-1 (Singapore)**:

```bash
aws configure get region
```

Hoặc set environment variable:
```bash
export AWS_REGION=ap-southeast-1
```

## Clone Lexi Project

### 1. Clone Repository

```bash
git clone https://gitlab.com/your-username/lexi-be.git
cd lexi-be
```

Hoặc nếu đã clone:
```bash
cd lexi-be
git pull origin main
```

### 2. Project Structure

```
lexi-be/
├── src/                          # Backend code (Python)
│   ├── domain/                   # Business logic
│   ├── application/              # Services
│   ├── infrastructure/           # AWS handlers
│   │   ├── handlers/             # Lambda handlers
│   │   ├── repositories/         # DynamoDB access
│   │   └── services/             # AWS services
│   └── shared/                   # Utilities
├── template.yaml                 # SAM template
├── samconfig.toml                # SAM config
├── requirements.txt              # Python dependencies
└── tests/                        # Unit tests
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

Frontend dependencies (nếu có):
```bash
cd frontend
npm install
cd ..
```

## CloudFormation Stacks

### 1. Check Current Stacks

```bash
aws cloudformation list-stacks \
  --region <REGION> \
  --query 'StackSummaries[?StackStatus!=`DELETE_COMPLETE`]' \
  --output table
```

Kết quả mong đợi:
- lexi-database (DynamoDB)
- lexi-auth-base (Cognito)
- lexi-auth-lambdas (Auth triggers)
- lexi-be (Main app)

### 2. Check DynamoDB Table

```bash
aws dynamodb list-tables --region <REGION>
```

Mô tả table:
```bash
aws dynamodb describe-table \
  --table-name <TABLE_NAME> \
  --region <REGION>
```

### 3. Check Cognito User Pool

```bash
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION>
```

Mô tả user pool:
```bash
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

### 4. Check Lambda Functions

```bash
aws lambda list-functions \
  --region <REGION> \
  --output table
```

Mô tả function cụ thể:
```bash
aws lambda get-function \
  --function-name <FUNCTION_NAME> \
  --region <REGION>
```

### 5. Check API Gateway

```bash
aws apigateway get-rest-apis \
  --region <REGION>
```

Lấy API ID:
```bash
API_ID=$(aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[0].id' \
  --output text)

echo "API ID: $API_ID"
echo "API URL: https://$API_ID.execute-api.<REGION>.amazonaws.com/Prod/"
```

## Environment Variables

### 1. Create .env file

```bash
# Create .env file
cat > .env << EOF
# AWS Configuration
AWS_REGION=<REGION>
AWS_ACCOUNT_ID=<ACCOUNT_ID>

# DynamoDB
LEXI_TABLE_NAME=<TABLE_NAME>

# Cognito
COGNITO_USER_POOL_ID=<USER_POOL_ID>
COGNITO_APP_CLIENT_ID=<APP_CLIENT_ID>

# S3
SPEAKING_AUDIO_BUCKET_NAME=<BUCKET_NAME>

# Logging
LOG_LEVEL=INFO

# API Gateway
API_ENDPOINT=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/
WEBSOCKET_ENDPOINT=wss://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod

# Bedrock
BEDROCK_MODEL_ID=us.amazon.nova-lite-v1:0
BEDROCK_REGION=<BEDROCK_REGION>
EOF
```

**Note**: Replace `<...>` with actual values.

### 2. Load Environment Variables

```bash
source .env
```

Hoặc export từng biến:
```bash
export AWS_REGION=<REGION>
export LEXI_TABLE_NAME=<TABLE_NAME>
```

## Verify Connection

### 1. Check AWS Credentials

```bash
aws sts get-caller-identity
```

Kết quả mong đợi:
```json
{
    "UserId": "AIDA...",
    "Account": "<ACCOUNT_ID>",
    "Arn": "arn:aws:iam::<ACCOUNT_ID>:user/<USERNAME>"
}
```

### 2. Check DynamoDB Connection

```bash
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --max-items 1 \
  --region <REGION>
```

### 3. Check Cognito Connection

```bash
aws cognito-idp list-users \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

### 4. Check S3 Connection

```bash
aws s3 ls
```

Liệt kê objects trong bucket:
```bash
aws s3 ls s3://<BUCKET_NAME>/
```

## IDE Setup

### Visual Studio Code

Install extensions:
- **AWS Toolkit** - AWS resource management
- **Python** - Python support
- **Pylance** - Python language server
- **Thunder Client** - API testing
- **GitLens** - Git integration

### PyCharm

- Install AWS Toolkit plugin
- Configure Python interpreter (3.12)
- Configure AWS credentials

## Khắc phục sự cố

### Vấn đề: AWS CLI không tìm thấy credentials

```bash
cat ~/.aws/credentials
```

Hoặc cấu hình lại:
```bash
aws configure
```

### Vấn đề: Phiên bản Python không đúng

```bash
python3 --version
```

Sử dụng nvm cho Node.js:
```bash
nvm use 20
```

Sử dụng pyenv cho Python:
```bash
pyenv install 3.12
pyenv local 3.12
```

### Vấn đề: SAM CLI không được tìm thấy

```bash
pip install --upgrade aws-sam-cli
```

Hoặc sử dụng Homebrew:
```bash
brew install aws-sam-cli
```

### Vấn đề: Region không đúng

```bash
aws configure get region
```

Set region:
```bash
export AWS_REGION=ap-southeast-1
```

Hoặc trong command:
```bash
aws dynamodb list-tables --region ap-southeast-1
```

## Danh sách kiểm tra

After completing setup, you should have:

- [ ] Node.js 20.x installed and verified
- [ ] Python 3.12 installed and verified
- [ ] AWS CLI v2 installed and verified
- [ ] AWS SAM CLI installed and verified
- [ ] Git installed and verified
- [ ] AWS credentials configured
- [ ] Region set to ap-southeast-1
- [ ] IAM user created with necessary permissions
- [ ] .env file created with environment variables
- [ ] Project cloned from repository
- [ ] Dependencies installed
- [ ] AWS connection verified successfully

> **📸 CẦN THÊM**: Add screenshot of terminal showing installed tools

> **📸 CẦN THÊM**: Add screenshot of aws sts get-caller-identity output

## Next Steps

After setup, continue to [Backend Deployment](../5.3-Backend/) to start deploying Lambda functions.
