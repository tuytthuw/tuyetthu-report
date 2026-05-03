---
title: "API Gateway"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Overview

Lexi uses **two API Gateways**:
1. **REST API** - For HTTP requests (flashcards, profile, admin, etc.)
2. **WebSocket API** - For real-time speaking sessions

### Check API Gateway

```bash
# List REST APIs
aws apigateway get-rest-apis \
  --region <REGION>

# List WebSocket APIs
aws apigatewayv2 get-apis \
  --region <REGION>
```

### Get API Endpoint

```bash
# Get REST API ID
API_ID=$(aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[0].id' \
  --output text)

echo "REST API Endpoint: https://$API_ID.execute-api.<REGION>.amazonaws.com/Prod/"

# Get WebSocket API ID
WS_API_ID=$(aws apigatewayv2 get-apis \
  --region <REGION> \
  --query 'Items[0].ApiId' \
  --output text)

echo "WebSocket Endpoint: wss://$WS_API_ID.execute-api.<REGION>.amazonaws.com/Prod"
```

### List API Resources

```bash
# List all resources
aws apigateway get-resources \
  --rest-api-id <API_ID> \
  --region <REGION> \
  --output table

# Or get specific resource
aws apigateway get-resource \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --region <REGION>
```

**Results will include resources**:
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
# Test public endpoint (no auth required)
curl -X GET \
  "https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/scenarios" \
  -H "Content-Type: application/json"

# Test protected endpoint (requires Cognito token)
curl -X GET \
  "https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod/profile" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <COGNITO_TOKEN>"
```

### Check API Stage

```bash
# List stages
aws apigateway get-stages \
  --rest-api-id <API_ID> \
  --region <REGION>

# Get specific stage
aws apigateway get-stage \
  --rest-api-id <API_ID> \
  --stage-name Prod \
  --region <REGION>
```

### Check API Authorizer

```bash
# List authorizers
aws apigateway get-authorizers \
  --rest-api-id <API_ID> \
  --region <REGION>

# Get specific authorizer
aws apigateway get-authorizer \
  --rest-api-id <API_ID> \
  --authorizer-id <AUTHORIZER_ID> \
  --region <REGION>
```

### Check API Integration

```bash
# Get integration for resource
aws apigateway get-integration \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method POST \
  --region <REGION>

# Results will include:
# - Type (AWS_PROXY for Lambda)
# - Uri (Lambda function ARN)
# - HttpMethod
# - IntegrationResponses
```

### Monitor API Metrics

```bash
# View API calls
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Count \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View API errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name 4XXError \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View API latency
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Latency \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Average,Maximum \
  --region <REGION>
```

### Check API Logs

```bash
# View API logs
aws logs tail /aws/apigateway/<API_NAME> \
  --region <REGION> \
  --follow

# Or list log groups
aws logs describe-log-groups \
  --log-group-name-prefix /aws/apigateway \
  --region <REGION>
```

### Troubleshooting

**Issue: 403 Forbidden**

```bash
# Check authorizer
aws apigateway get-authorizers \
  --rest-api-id <API_ID> \
  --region <REGION>

# Check Cognito user pool
aws cognito-idp describe-user-pool \
  --user-pool-id <USER_POOL_ID> \
  --region <REGION>
```

**Issue: 502 Bad Gateway**

```bash
# Check Lambda integration
aws apigateway get-integration \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method POST \
  --region <REGION>

# Check Lambda logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow
```

**Issue: 504 Gateway Timeout**

```bash
# Increase Lambda timeout
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

## Next Steps

Continue to [Cognito](../5.3.4-Cognito/) to learn how to manage authentication.

### Checklist

- [ ] API Gateway endpoints accessible
- [ ] REST API test successful
- [ ] WebSocket API test successful
- [ ] Authorizer working
- [ ] Metrics visible in CloudWatch
