---
title: "Cost Optimization"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

## Overview

Lexi uses AWS services with a pay-as-you-go model. To optimize costs:
- Monitor usage
- Set budgets
- Optimize each service

### DynamoDB Optimization

```bash
# Check DynamoDB usage
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedReadCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Use on-demand billing mode (if not already)
aws dynamodb update-billing-mode \
  --table-name <TABLE_NAME> \
  --billing-mode PAY_PER_REQUEST \
  --region <REGION>
```

**Tips**:
- Lexi already uses PAY_PER_REQUEST (on-demand)
- No provisioned capacity needed
- Costs calculated by actual requests

### Lambda Optimization

```bash
# Check Lambda duration
aws logs start-query \
  --log-group-name /aws/lambda/<FUNCTION_NAME> \
  --start-time 1714521600 \
  --end-time 1714608000 \
  --query-string 'fields @duration, @memoryUsed | stats avg(@duration), max(@memoryUsed)'

# Or use CloudWatch Insights
aws logs get-query-results \
  --query-id <QUERY_ID>
```

**Tips**:
- Increase memory allocation if duration is high (Lambda scales CPU with memory)
- Decrease memory if not needed
- Use Lambda Layers for shared code

### S3 Optimization

```bash
# Check S3 usage
aws s3 ls s3://<BUCKET_NAME>/ --recursive --summarize

# Set lifecycle policy to delete old files
cat > lifecycle.json << EOF
{
  "Rules": [
    {
      "Id": "DeleteOldAudioFiles",
      "Status": "Enabled",
      "Prefix": "audio/",
      "Expiration": {
        "Days": 30
      }
    }
  ]
}
EOF

aws s3api put-bucket-lifecycle-configuration \
  --bucket <BUCKET_NAME> \
  --lifecycle-configuration file://lifecycle.json \
  --region <REGION>
```

**Tips**:
- Auto-delete old files
- Use S3 Intelligent-Tiering
- Compress files before upload

### Bedrock Optimization

```bash
# Monitor Bedrock usage
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name InputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View output tokens
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name OutputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

**Tips**:
- Write concise prompts
- Use prompt caching
- Choose smaller model (Nova Lite instead of Ultra)
- Limit max_tokens

### AWS Budgets

```bash
# Create budget
cat > budget.json << EOF
{
  "BudgetName": "Lexi-Monthly-Budget",
  "BudgetLimit": {
    "Amount": "200",
    "Unit": "USD"
  },
  "TimeUnit": "MONTHLY",
  "BudgetType": "COST"
}
EOF

aws budgets create-budget \
  --account-id <ACCOUNT_ID> \
  --budget file://budget.json \
  --region <REGION>
```

### Cost Explorer

```bash
# View costs by service
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE \
  --region <REGION>

# View costs by resource
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=TAG,Key=Environment \
  --region <REGION>
```

### Optimize Each Service

| Service | Optimization | Savings |
|---------|-------------|---------|
| Lambda | Increase memory, reduce duration | 20-30% |
| DynamoDB | On-demand mode | 50%+ (if low usage) |
| S3 | Lifecycle policies | 30-50% |
| Bedrock | Prompt optimization | 20-40% |
| Transcribe | Batch processing | 10-20% |
| Polly | Caching | 30-50% |

### Troubleshooting

**Issue: High costs**

```bash
# Check top services
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE \
  --sort-by KEY_DESC \
  --region <REGION>

# Check top resources
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=RESOURCE \
  --region <REGION>
```

**Issue: Budget exceeded**

```bash
# Check budget status
aws budgets describe-budgets \
  --account-id <ACCOUNT_ID> \
  --region <REGION>

# Update budget
aws budgets update-budget \
  --account-id <ACCOUNT_ID> \
  --new-budget file://budget.json \
  --region <REGION>
```

## Next Steps

You have completed the Monitoring section! Continue to [Clean-up](../../5.8-Clean-up/) to clean up resources.

### Checklist

- [ ] DynamoDB optimization applied
- [ ] Lambda optimization applied
- [ ] S3 lifecycle policies configured
- [ ] Bedrock optimization applied
- [ ] Budget created
- [ ] Cost Explorer reviewed
