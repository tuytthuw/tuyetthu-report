---
title: "CloudWatch"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

## Overview

**CloudWatch** is AWS's monitoring service:
- **Logs**: Store logs from Lambda, API Gateway, etc.
- **Metrics**: Monitor performance (invocations, errors, duration, etc.)
- **Alarms**: Alert when metrics exceed thresholds
- **Dashboards**: Display metrics visually

### View Logs

#### Lambda Logs

```bash
# View Lambda function logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --follow

# Or view specific logs
aws logs tail /aws/lambda/<FUNCTION_NAME> \
  --region <REGION> \
  --since 1h
```

#### API Gateway Logs

```bash
# View API Gateway logs
aws logs tail /aws/apigateway/<API_NAME> \
  --region <REGION> \
  --follow
```

#### List Log Groups

```bash
# List all log groups
aws logs describe-log-groups \
  --region <REGION>

# Or filter by prefix
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION>
```

### View Metrics

#### Lambda Metrics

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

#### API Gateway Metrics

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

#### DynamoDB Metrics

```bash
# View read capacity
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedReadCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View write capacity
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedWriteCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Create Alarms

```bash
# Create alarm for Lambda errors
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

# Create alarm for Lambda duration
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

# Create alarm for API errors
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

### List Alarms

```bash
# List all alarms
aws cloudwatch describe-alarms \
  --region <REGION>

# Or filter by alarm name
aws cloudwatch describe-alarms \
  --alarm-name-prefix lexi \
  --region <REGION>
```

### Delete Alarms

```bash
# Delete alarm
aws cloudwatch delete-alarms \
  --alarm-names <ALARM_NAME> \
  --region <REGION>
```

### Create Dashboards

```bash
# Create dashboard
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

### Troubleshooting

**Issue: Logs not appearing**

```bash
# Check log group
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/<FUNCTION_NAME> \
  --region <REGION>

# Or check Lambda role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Issue: Metrics not appearing**

```bash
# Check metric namespace
aws cloudwatch list-metrics \
  --namespace AWS/Lambda \
  --region <REGION>
```

## Next Steps

Continue to [Cost Optimization](../5.7.2-Cost-Optimization/) to optimize costs.

### Checklist

- [ ] Log groups accessible
- [ ] Metrics visible
- [ ] Alarms configured
- [ ] Dashboard created
- [ ] Logs retention set
