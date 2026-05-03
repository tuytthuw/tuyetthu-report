---
title: "API Gateway"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Tổng quan

Lexi sử dụng **hai API Gateway**:
1. **REST API** - Cho HTTP requests (flashcards, profile, admin, etc.)
2. **WebSocket API** - Cho real-time speaking sessions

### Kiểm tra API Gateway

```bash
# Liệt kê REST APIs
aws apigateway get-rest-apis \
  --region <REGION>

# Liệt kê WebSocket APIs
aws apigatewayv2 get-apis \
  --region <REGION>
```

### Lấy API Endpoint

```bash
# Lấy REST API ID
API_ID=$(aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[0].id' \
  --output text)

echo "REST API Endpoint: https://$API_ID.execute-api.<REGION>.amazonaws.com/Prod/"

# Lấy WebSocket API ID
WS_API_ID=$(aws apigatewayv2 get-apis \
  --region <REGION> \
  --query 'Items[0].ApiId' \
  --output text)

echo "WebSocket Endpoint: wss://$WS_API_ID.execute-api.<REGION>.amazonaws.com/Prod"
```

### Liệt kê API Resources

```bash
# Liệt kê tất cả resources
aws apigateway get-resources \
  --rest-api-id <API_ID> \
  --region <REGION> \
  --output table

# Hoặc lấy resource cụ thể
aws apigateway get-resource \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --region <REGION>
```

**Kết quả sẽ bao gồm các resources**:
- `/onboarding/complete` (POST)
- `/admin/users` (GET, PATCH)
- `/admin/scenarios` (GET, POST, PATCH)
- `/profile` (GET, PATCH)
- `/flashcards` (GET, POST, PATCH, DELETE)
- `/flashcards/due` (GET)
- `/flashcards/{flashcard_id}/review` (POST)
- `/flashcards/export` (GET)
- `/flashcards/import` (POST)
- `/flashcards/statistics` (GET)
- `/vocabulary/translate` (POST)
- `/vocabulary/translate-sentence` (POST)
- `/scenarios` (GET)
- `/sessions` (GET, POST)
- `/sessions/{session_id}` (GET)
- `/sessions/{session_id}/turns` (POST)
- `/sessions/{session_id}/complete` (POST)

### Test API Endpoint

```bash
# Test public endpoint (không cần auth)
curl -X GET \
  "https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/scenarios" \
  -H "Content-Type: application/json"

# Test protected endpoint (cần Cognito token)
curl -X GET \
  "https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/profile" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <COGNITO_TOKEN>"
```

### Kiểm tra API Stage

```bash
# Liệt kê stages
aws apigateway get-stages \
  --rest-api-id <API_ID> \
  --region <REGION>

# Lấy stage cụ thể
aws apigateway get-stage \
  --rest-api-id <API_ID> \
  --stage-name Prod \
  --region <REGION>
```

### Kiểm tra API Authorizer

```bash
# Liệt kê authorizers
aws apigateway get-authorizers \
  --rest-api-id <API_ID> \
  --region <REGION>

# Lấy authorizer cụ thể
aws apigateway get-authorizer \
  --rest-api-id <API_ID> \
  --authorizer-id <AUTHORIZER_ID> \
  --region <REGION>
```

### Kiểm tra API Integration

```bash
# Lấy integration cho resource
aws apigateway get-integration \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method POST \
  --region <REGION>

# Kết quả sẽ bao gồm**:
# - Type (AWS_PROXY cho Lambda)
# - Uri (Lambda function ARN)
# - HttpMethod
# - IntegrationResponses
```

### Giám sát API Metrics

```bash
# Xem API calls
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Count \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem API errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name 4XXError \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem API latency
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Latency \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Average,Maximum \
  --region <REGION>
```

### Kiểm tra API Logs

```bash
# Xem API logs
aws logs tail /aws/apigateway/<API_NAME> \
  --region <REGION> \
  --follow

# Hoặc liệt kê log groups
aws logs describe-log-groups \
  --log-group-name-prefix /aws/apigateway \
  --region <REGION>
```

### Khắc phục sự cố

**Vấn đề: 403 Forbidden**

```bash
# Kiểm tra authorizer
aws apigateway get-authorizers \
  --rest-api-id <API_ID> \
  --region <REGION>

# Kiểm tra Cognito user pool
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

**Vấn đề: 502 Bad Gateway**

```bash
# Kiểm tra Lambda integration
aws apigateway get-integration \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method POST \
  --region <REGION>

# Kiểm tra Lambda logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow
```

**Vấn đề: 504 Gateway Timeout**

```bash
# Tăng timeout Lambda
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [Cognito](../5.3.4-Cognito/) để học cách quản lý authentication.

### Danh sách kiểm tra

- [ ] API Gateway endpoints accessible
- [ ] REST API test thành công
- [ ] WebSocket API test thành công
- [ ] Authorizer hoạt động
- [ ] Metrics visible trong CloudWatch
