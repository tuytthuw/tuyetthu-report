---
title: "Lambda Functions"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Overview

Lexi backend uses **23 Lambda functions** organized into modules:

| Module | Number of Functions | Purpose |
|--------|-------------|----------|
| Onboarding | 1 | Complete new user information |
| Admin | 5 | Manage users and scenarios |
| Profile | 2 | Manage user profiles |
| Flashcard | 10 | Manage flashcards (CRUD, review, export/import, statistics) |
| Vocabulary | 2 | Translate vocabulary and sentences |
| Speaking | 2 | Speaking practice (WebSocket + Session management) |
| Scenarios | 1 | List public scenarios |

### Check Lambda Functions

```bash
# List all Lambda functions
aws lambda list-functions \
  --region <REGION> \
  --output table

# List only Lexi functions
aws lambda list-functions \
  --region <REGION> \
  --query 'Functions[?starts_with(FunctionName, `lexi-be`)].FunctionName' \
  --output table
```

**Expected result**: 20+ functions

### Get Specific Function Information

```bash
# Get function information
aws lambda get-function \
  --function-name <FUNCTION_NAME> \
  --region <REGION>

# Or get only configuration
aws lambda get-function-configuration \
  --function-name <FUNCTION_NAME> \
  --region <REGION>
```

**Result will include**:
- FunctionName
- Runtime (python3.12)
- Handler path
- MemorySize, Timeout
- Environment variables
- IAM role

### View Lambda Logs

```bash
# View function logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Or list log groups
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION>
```

### Invoke Lambda Function

```bash
# Invoke function directly
aws lambda invoke \
  --function-name <FUNCTION_NAME> \
  --region <REGION> \
  --payload '{}' \
  response.json

# View result
cat response.json
```

### Update Lambda Function

```bash
# Update code
aws lambda update-function-code \
  --function-name <FUNCTION_NAME> \
  --zip-file fileb://function.zip \
  --region <REGION>

# Update configuration
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --memory-size 256 \
  --timeout 30 \
  --region <REGION>

# Update environment variables
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --environment Variables={KEY=VALUE} \
  --region <REGION>
```

### Monitor Lambda Metrics

```bash
# View Lambda invocations
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Invocations \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View Lambda errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Errors \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View Lambda duration
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Duration \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Average,Maximum \
  --region <REGION>
```

## Troubleshooting

**Issue: Lambda function error**

```bash
# View logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Check IAM role
aws iam get-role-policy \
  --role-name <ROLE_NAME> \
  --policy-name <POLICY_NAME>
```

**Issue: Lambda timeout**

```bash
# Increase timeout
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

**Issue: Lambda out of memory**

```bash
# Increase memory
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --memory-size 512 \
  --region <REGION>
```

## Next Steps

Continue to [DynamoDB](../5.3.2-DynamoDB/) to learn how to manage the database.

### Checklist

- [ ] Lambda functions deployed
- [ ] Function logs accessible
- [ ] Metrics visible in CloudWatch
- [ ] Invocation test successful
- [ ] IAM permissions configured correctly
