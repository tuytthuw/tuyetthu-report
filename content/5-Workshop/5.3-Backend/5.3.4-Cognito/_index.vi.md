---
title: "Cognito"
date: "2026-05-02"
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

## Tổng quan

Lexi sử dụng **Amazon Cognito** cho authentication:
- **User Pool**: Quản lý users
- **App Client**: Cho frontend
- **Lambda Triggers**: Tự động hóa workflows
  - PreSignUp: Tự động xác nhận users
  - PostConfirmation: Tạo user profile
  - PostAuthentication: Ghi log đăng nhập

### Kiểm tra Cognito User Pool

```bash
# Liệt kê user pools
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION>

# Mô tả user pool chi tiết
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- Id, Name, Status (ACTIVE)
- LambdaConfig (Lambda triggers)
- Policies, Schema
- MfaConfiguration

### Kiểm tra App Client

```bash
# Liệt kê app clients
aws cognito-idp list-user-pool-clients \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# Mô tả app client
aws cognito-idp describe-user-pool-client \
  --user-pool-id <USER_POOL_ID> \
  --client-id <CLIENT_ID> \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- ClientId, ClientName
- AllowedOAuthFlows
- CallbackURLs, LogoutURLs
- AllowedOAuthScopes

### Quản lý Users

#### Liệt kê Users

```bash
# Liệt kê tất cả users
aws cognito-idp list-users \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# Liệt kê users với filter
aws cognito-idp list-users \
  --user-pool-id <USER_POOL_ID> \
  --filter 'email = "user@example.com"' \
  --region <REGION>
```

#### Tạo User

```bash
# Tạo user
aws cognito-idp admin-create-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --user-attributes Name=email,Value=user@example.com Name=name,Value="John Doe" \
  --temporary-password TempPassword123! \
  --region <REGION>
```

#### Lấy User Chi tiết

```bash
# Lấy user cụ thể
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

#### Cập nhật User

```bash
# Cập nhật user attributes
aws cognito-idp admin-update-user-attributes \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --user-attributes Name=email,Value=newemail@example.com \
  --region <REGION>
```

#### Xóa User

```bash
# Xóa user
aws cognito-idp admin-delete-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

#### Đặt Lại Mật Khẩu

```bash
# Đặt lại mật khẩu
aws cognito-idp admin-set-user-password \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --password NewPassword123! \
  --permanent \
  --region <REGION>
```

### Kiểm tra Lambda Triggers

```bash
# Lấy Lambda config
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION> \
  --query 'UserPool.LambdaConfig'
```

**Kết quả sẽ bao gồm**:
- PreSignUp: Lambda function ARN
- PostConfirmation: Lambda function ARN
- PostAuthentication: Lambda function ARN

### Kiểm tra User Groups

```bash
# Liệt kê groups
aws cognito-idp list-groups \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# Liệt kê groups của user
aws cognito-idp admin-list-groups-for-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

### Kiểm tra User Sessions

```bash
# Liệt kê user devices
aws cognito-idp list-devices \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

### Giám sát Cognito Metrics

```bash
# Xem sign-in attempts
aws cloudwatch get-metric-statistics \
  --namespace AWS/Cognito \
  --metric-name SignInSuccesses \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem sign-in failures
aws cloudwatch get-metric-statistics \
  --namespace AWS/Cognito \
  --metric-name SignInThrottles \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Khắc phục sự cố

**Vấn đề: User không thể đăng nhập**

```bash
# Kiểm tra user status
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>

# Kiểm tra user attributes
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION> \
  --query 'UserAttributes'
```

**Vấn đề: Lambda trigger không hoạt động**

```bash
# Kiểm tra Lambda logs
aws logs tail /aws/lambda/<TRIGGER_FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Kiểm tra Lambda permissions
aws lambda get-policy \
  --function-name <TRIGGER_FUNCTION_NAME> \
  --region <REGION>
```

**Vấn đề: User bị locked**

```bash
# Unlock user
aws cognito-idp admin-reset-user-password \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [S3](../5.3.5-S3/) để học cách quản lý audio storage.

### Danh sách kiểm tra

- [ ] User Pool tạo thành công
- [ ] App Client configured
- [ ] Lambda triggers hoạt động
- [ ] User management test thành công
- [ ] Metrics visible
