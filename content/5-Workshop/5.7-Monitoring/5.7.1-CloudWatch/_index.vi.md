---
title: "CloudWatch"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

## Tổng quan

**CloudWatch** là dịch vụ giám sát của AWS:
- **Logs**: Lưu trữ logs từ Lambda, API Gateway, etc.
- **Metrics**: Giám sát performance (invocations, errors, duration, etc.)
- **Alarms**: Cảnh báo khi metrics vượt ngưỡng
- **Dashboards**: Hiển thị metrics trực quan

### Xem Logs

#### Lambda Logs

```bash
# Xem logs của Lambda function
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Hoặc xem logs cụ thể
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --since 1h
```

#### API Gateway Logs

```bash
# Xem logs API Gateway
aws logs tail /aws/apigateway/<API_NAME> \
  --region <REGION> \
  --follow
```

#### Liệt kê Log Groups

```bash
# Liệt kê tất cả log groups
aws logs describe-log-groups \
  --region <REGION>

# Hoặc lọc theo prefix
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION>
```

### Xem Metrics

#### Lambda Metrics

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

#### API Gateway Metrics

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

#### DynamoDB Metrics

```bash
# Xem read capacity
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedReadCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem write capacity
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedWriteCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Tạo Alarms

```bash
# Tạo alarm cho Lambda errors
aws cloudwatch put-metric-alarm \
  --alarm-name lexi-lambda-errors \
  --alarm-description "Alert when Lambda errors exceed 5" \
  --metric-name Errors \
  --namespace AWS/Lambda \
  --statistic Sum \
  --period 300 \
  --threshold 5 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --region <REGION>

# Tạo alarm cho Lambda duration
aws cloudwatch put-metric-alarm \
  --alarm-name lexi-lambda-duration \
  --alarm-description "Alert when Lambda duration exceeds 5 seconds" \
  --metric-name Duration \
  --namespace AWS/Lambda \
  --statistic Average \
  --period 300 \
  --threshold 5000 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --region <REGION>

# Tạo alarm cho API errors
aws cloudwatch put-metric-alarm \
  --alarm-name lexi-api-errors \
  --alarm-description "Alert when API 4XX errors exceed 10" \
  --metric-name 4XXError \
  --namespace AWS/ApiGateway \
  --statistic Sum \
  --period 300 \
  --threshold 10 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 \
  --region <REGION>
```

### Liệt kê Alarms

```bash
# Liệt kê tất cả alarms
aws cloudwatch describe-alarms \
  --region <REGION>

# Hoặc lọc theo alarm name
aws cloudwatch describe-alarms \
  --alarm-name-prefix lexi \
  --region <REGION>
```

### Xóa Alarms

```bash
# Xóa alarm
aws cloudwatch delete-alarms \
  --alarm-names <ALARM_NAME> \
  --region <REGION>
```

### Tạo Dashboards

```bash
# Tạo dashboard
cat > dashboard.json << EOF
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/Lambda", "Invocations"],
          [".", "Errors"],
          [".", "Duration"]
        ],
        "period": 300,
        "stat": "Sum",
        "region": "<REGION>",
        "title": "Lambda Metrics"
      }
    }
  ]
}
EOF

aws cloudwatch put-dashboard \
  --dashboard-name lexi-dashboard \
  --dashboard-body file://dashboard.json \
  --region <REGION>
```

### Khắc phục sự cố

**Vấn đề: Logs không xuất hiện**

```bash
# Kiểm tra log group
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/<FUNCTION_NAME> \
  --region <REGION>

# Hoặc kiểm tra Lambda role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Vấn đề: Metrics không xuất hiện**

```bash
# Kiểm tra metric namespace
aws cloudwatch list-metrics \
  --namespace AWS/Lambda \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [Cost Optimization](../5.7.2-Cost-Optimization/) để tối ưu hóa chi phí.

### Danh sách kiểm tra

- [ ] Log groups accessible
- [ ] Metrics visible
- [ ] Alarms configured
- [ ] Dashboard created
- [ ] Logs retention set
