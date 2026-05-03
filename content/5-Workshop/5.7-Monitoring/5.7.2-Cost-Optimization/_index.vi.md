---
title: "Cost Optimization"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

## Tổng quan

Lexi sử dụng các dịch vụ AWS với mô hình pay-as-you-go. Để tối ưu hóa chi phí:
- Giám sát usage
- Thiết lập budgets
- Tối ưu hóa từng dịch vụ

### DynamoDB Optimization

```bash
# Kiểm tra DynamoDB usage
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name ConsumedReadCapacityUnits \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Sử dụng on-demand billing mode (nếu chưa)
aws dynamodb update-billing-mode \
  --table-name <TABLE_NAME> \
  --billing-mode PAY_PER_REQUEST \
  --region <REGION>
```

**Tips**:
- Lexi đã sử dụng PAY_PER_REQUEST (on-demand)
- Không cần provisioned capacity
- Chi phí tính theo requests thực tế

### Lambda Optimization

```bash
# Kiểm tra Lambda duration
aws logs start-query \
  --log-group-name /aws/lambda/<FUNCTION_NAME> \
  --start-time 1714521600 \
  --end-time 1714608000 \
  --query-string 'fields @duration, @memoryUsed | stats avg(@duration), max(@memoryUsed)'

# Hoặc sử dụng CloudWatch Insights
aws logs get-query-results \
  --query-id <QUERY_ID>
```

**Tips**:
- Tăng memory allocation nếu duration cao (Lambda scales CPU theo memory)
- Giảm memory nếu không cần thiết
- Sử dụng Lambda Layers cho shared code

### S3 Optimization

```bash
# Kiểm tra S3 usage
aws s3 ls s3://<BUCKET_NAME>/ --recursive --summarize

# Thiết lập lifecycle policy để xóa old files
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
- Xóa files cũ tự động
- Sử dụng S3 Intelligent-Tiering
- Compress files trước khi upload

### Bedrock Optimization

```bash
# Giám sát Bedrock usage
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name InputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem output tokens
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
- Viết prompts ngắn gọn
- Sử dụng prompt caching
- Chọn model nhỏ hơn (Nova Lite thay vì Ultra)
- Giới hạn max_tokens

### AWS Budgets

```bash
# Tạo budget
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
# Xem chi phí theo dịch vụ
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE \
  --region <REGION>

# Xem chi phí theo resource
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=TAG,Key=Environment \
  --region <REGION>
```

### Tối ưu hóa từng dịch vụ

| Dịch vụ | Tối ưu hóa | Tiết kiệm |
|---------|-----------|----------|
| Lambda | Tăng memory, giảm duration | 20-30% |
| DynamoDB | On-demand mode | 50%+ (nếu usage thấp) |
| S3 | Lifecycle policies | 30-50% |
| Bedrock | Prompt optimization | 20-40% |
| Transcribe | Batch processing | 10-20% |
| Polly | Caching | 30-50% |

### Khắc phục sự cố

**Vấn đề: Chi phí cao**

```bash
# Kiểm tra top services
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE \
  --sort-by KEY_DESC \
  --region <REGION>

# Kiểm tra top resources
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=RESOURCE \
  --region <REGION>
```

**Vấn đề: Budget exceeded**

```bash
# Kiểm tra budget status
aws budgets describe-budgets \
  --account-id <ACCOUNT_ID> \
  --region <REGION>

# Cập nhật budget
aws budgets update-budget \
  --account-id <ACCOUNT_ID> \
  --new-budget file://budget.json \
  --region <REGION>
```

## Bước Tiếp Theo

Bạn đã hoàn thành phần Monitoring! Tiếp tục đến [Clean-up](../../5.8-Clean-up/) để dọn dẹp tài nguyên.

### Danh sách kiểm tra

- [ ] DynamoDB optimization applied
- [ ] Lambda optimization applied
- [ ] S3 lifecycle policies configured
- [ ] Bedrock optimization applied
- [ ] Budget created
- [ ] Cost Explorer reviewed
