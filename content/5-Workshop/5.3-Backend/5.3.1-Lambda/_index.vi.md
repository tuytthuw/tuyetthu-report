---
title: "Lambda Functions"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Tổng quan

Lexi backend sử dụng **23 Lambda functions** được tổ chức thành các module:

| Module | Số Functions | Chức năng |
|--------|-------------|----------|
| Onboarding | 1 | Hoàn tất thông tin người dùng mới |
| Admin | 5 | Quản lý người dùng và kịch bản |
| Profile | 2 | Quản lý hồ sơ người dùng |
| Flashcard | 10 | Quản lý flashcard (CRUD, review, export/import, statistics) |
| Vocabulary | 2 | Dịch từ vựng và câu |
| Speaking | 2 | Luyện nói (WebSocket + Session management) |
| Scenarios | 1 | Danh sách kịch bản công khai |

### Kiểm tra Lambda Functions

```bash
# Liệt kê tất cả Lambda functions
aws lambda list-functions \
  --region <REGION> \
  --output table

# Liệt kê chỉ functions của Lexi
aws lambda list-functions \
  --region <REGION> \
  --query 'Functions[?starts_with(FunctionName, `lexi-be`)].FunctionName' \
  --output table
```

**Kết quả mong đợi**: 20+ functions

### Lấy thông tin Function cụ thể

```bash
# Lấy thông tin function
aws lambda get-function \
  --function-name <FUNCTION_NAME> \
  --region <REGION>

# Hoặc lấy chỉ configuration
aws lambda get-function-configuration \
  --function-name <FUNCTION_NAME> \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- FunctionName
- Runtime (python3.12)
- Handler path
- MemorySize, Timeout
- Environment variables
- IAM role

### Xem Lambda Logs

```bash
# Xem logs của function
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Hoặc liệt kê log groups
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION>
```

### Invoke Lambda Function

```bash
# Invoke function trực tiếp
aws lambda invoke \
  --function-name <FUNCTION_NAME> \
  --region <REGION> \
  --payload '{}' \
  response.json

# Xem kết quả
cat response.json
```

### Cập nhật Lambda Function

```bash
# Cập nhật code
aws lambda update-function-code \
  --function-name <FUNCTION_NAME> \
  --zip-file fileb://function.zip \
  --region <REGION>

# Cập nhật configuration
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --memory-size 256 \
  --timeout 30 \
  --region <REGION>

# Cập nhật environment variables
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --environment Variables={KEY=VALUE} \
  --region <REGION>
```

### Giám sát Lambda Metrics

```bash
# Xem Lambda invocations
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Invocations \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem Lambda errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Errors \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem Lambda duration
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Duration \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Average,Maximum \
  --region <REGION>
```

## Khắc phục sự cố

**Vấn đề: Hàm Lambda lỗi**

```bash
# Xem logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Kiểm tra IAM role
aws iam get-role-policy \
  --role-name <ROLE_NAME> \
  --policy-name <POLICY_NAME>
```

**Vấn đề: Lambda hết thời gian**

```bash
# Tăng timeout
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

**Vấn đề: Lambda hết bộ nhớ**

```bash
# Tăng memory
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --memory-size 512 \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [DynamoDB](../5.3.2-DynamoDB/) để học cách quản lý database.

### Danh sách kiểm tra

- [ ] Lambda functions deployed
- [ ] Function logs accessible
- [ ] Metrics visible trong CloudWatch
- [ ] Invocation test thành công
- [ ] IAM permissions cấu hình đúng
