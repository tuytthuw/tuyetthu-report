---
title: "Cognito"
date: "2026-05-02"
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

## Overview

Lexi uses **Amazon Cognito** for authentication:
- **User Pool**: Manages users
- **App Client**: For frontend
- **Lambda Triggers**: Automates workflows
  - PreSignUp: Auto-confirm users
  - PostConfirmation: Create user profile
  - PostAuthentication: Log sign-in

### Check Cognito User Pool

```bash
# List user pools
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION>

# Describe user pool details
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

**Results will include**:
- Id, Name, Status (ACTIVE)
- LambdaConfig (Lambda triggers)
- Policies, Schema
- MfaConfiguration

### Check App Client

```bash
# List app clients
aws cognito-idp list-user-pool-clients \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# Describe app client
aws cognito-idp describe-user-pool-client \
  --user-pool-id <USER_POOL_ID> \
  --client-id <CLIENT_ID> \
  --region <REGION>
```

**Results will include**:
- ClientId, ClientName
- AllowedOAuthFlows
- CallbackURLs, LogoutURLs
- AllowedOAuthScopes

### Manage Users

#### List Users

```bash
# List all users
aws cognito-idp list-users \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# List users with filter
aws cognito-idp list-users \
  --user-pool-id <USER_POOL_ID> \
  --filter 'email = "user@example.com"' \
  --region <REGION>
```

#### Create User

```bash
# Create user
aws cognito-idp admin-create-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --user-attributes Name=email,Value=user@example.com Name=name,Value="John Doe" \
  --temporary-password TempPassword123! \
  --region <REGION>
```

#### Get User Details

```bash
# Get specific user
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

#### Update User

```bash
# Update user attributes
aws cognito-idp admin-update-user-attributes \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --user-attributes Name=email,Value=newemail@example.com \
  --region <REGION>
```

#### Delete User

```bash
# Delete user
aws cognito-idp admin-delete-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

#### Reset Password

```bash
# Reset password
aws cognito-idp admin-set-user-password \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --password NewPassword123! \
  --permanent \
  --region <REGION>
```

### Check Lambda Triggers

```bash
# Get Lambda config
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION> \
  --query 'UserPool.LambdaConfig'
```

**Results will include**:
- PreSignUp: Lambda function ARN
- PostConfirmation: Lambda function ARN
- PostAuthentication: Lambda function ARN

### Check User Groups

```bash
# List groups
aws cognito-idp list-groups \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>

# List groups for user
aws cognito-idp admin-list-groups-for-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

### Check User Sessions

```bash
# List user devices
aws cognito-idp list-devices \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

### Monitor Cognito Metrics

```bash
# View sign-in attempts
aws cloudwatch get-metric-statistics \
  --namespace AWS/Cognito \
  --metric-name SignInSuccesses \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View sign-in failures
aws cloudwatch get-metric-statistics \
  --namespace AWS/Cognito \
  --metric-name SignInThrottles \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Troubleshooting

**Issue: User cannot sign in**

```bash
# Check user status
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>

# Check user attributes
aws cognito-idp admin-get-user \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION> \
  --query 'UserAttributes'
```

**Issue: Lambda trigger not working**

```bash
# Check Lambda logs
aws logs tail /aws/lambda/<TRIGGER_FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Check Lambda permissions
aws lambda get-policy \
  --function-name <TRIGGER_FUNCTION_NAME> \
  --region <REGION>
```

**Issue: User locked**

```bash
# Unlock user
aws cognito-idp admin-reset-user-password \
  --user-pool-id <USER_POOL_ID> \
  --username <USERNAME> \
  --region <REGION>
```

## Next Steps

Continue to [S3](../5.3.5-S3/) to learn how to manage audio storage.

### Checklist

- [ ] User Pool created successfully
- [ ] App Client configured
- [ ] Lambda triggers working
- [ ] User management test successful
- [ ] Metrics visible
