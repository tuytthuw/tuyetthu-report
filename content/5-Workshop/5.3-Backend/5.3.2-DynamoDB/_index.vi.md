---
title: "DynamoDB"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Tổng quan

Lexi sử dụng **Single-table design** với DynamoDB:
- **Table Name**: LexiApp
- **Billing Mode**: PAY_PER_REQUEST (on-demand)
- **Primary Key**: PK (Partition Key) + SK (Sort Key)
- **Global Secondary Indexes**: GSI1-5 cho các query patterns khác nhau

### Kiểm tra DynamoDB Table

```bash
# Liệt kê tất cả tables
aws dynamodb list-tables --region <REGION>

# Mô tả table chi tiết
aws dynamodb describe-table \
  --table-name <TABLE_NAME> \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- TableName, TableStatus (ACTIVE)
- ItemCount, TableSizeBytes
- BillingModeSummary (PAY_PER_REQUEST)
- KeySchema (PK, SK)
- GlobalSecondaryIndexes (GSI1-5)

### Hiểu Single-Table Design

**Partition Key (PK)**: Entity type + ID
```
USER#<user_id>
FLASHCARD#<flashcard_id>
SESSION#<session_id>
SCENARIO#<scenario_id>
```

**Sort Key (SK)**: Timestamp hoặc entity type
```
CREATED#2026-05-02T10:30:00Z
UPDATED#2026-05-02T10:30:00Z
METADATA#<type>
```

### Scan Table

```bash
# Scan table (lấy items)
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --region <REGION> \
  --limit 10

# Scan với filter
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --filter-expression "attribute_exists(#pk)" \
  --expression-attribute-names '{"#pk":"PK"}' \
  --region <REGION>
```

### Query Table

```bash
# Query với partition key
aws dynamodb query \
  --table-name <TABLE_NAME> \
  --key-condition-expression "PK = :pk" \
  --expression-attribute-values '{":pk":{"S":"USER#<user_id>"}}' \
  --region <REGION>

# Query với partition key + sort key
aws dynamodb query \
  --table-name <TABLE_NAME> \
  --key-condition-expression "PK = :pk AND begins_with(SK, :sk)" \
  --expression-attribute-values '{":pk":{"S":"USER#<user_id>"},":sk":{"S":"CREATED"}}' \
  --region <REGION>
```

### Put Item

```bash
# Thêm item vào table
aws dynamodb put-item \
  --table-name <TABLE_NAME> \
  --item '{
    "PK": {"S": "USER#123"},
    "SK": {"S": "CREATED#2026-05-02T10:30:00Z"},
    "email": {"S": "user@example.com"},
    "name": {"S": "John Doe"}
  }' \
  --region <REGION>
```

### Get Item

```bash
# Lấy item từ table
aws dynamodb get-item \
  --table-name <TABLE_NAME> \
  --key '{
    "PK": {"S": "USER#123"},
    "SK": {"S": "CREATED#2026-05-02T10:30:00Z"}
  }' \
  --region <REGION>
```

### Update Item

```bash
# Cập nhật item
aws dynamodb update-item \
  --table-name <TABLE_NAME> \
  --key '{
    "PK": {"S": "USER#123"},
    "SK": {"S": "CREATED#2026-05-02T10:30:00Z"}
  }' \
  --update-expression "SET #name = :name" \
  --expression-attribute-names '{"#name":"name"}' \
  --expression-attribute-values '{":name":{"S":"Jane Doe"}}' \
  --region <REGION>
```

### Delete Item

```bash
# Xóa item
aws dynamodb delete-item \
  --table-name <TABLE_NAME> \
  --key '{
    "PK": {"S": "USER#123"},
    "SK": {"S": "CREATED#2026-05-02T10:30:00Z"}
  }' \
  --region <REGION>
```

### Giám sát DynamoDB Metrics

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

# Xem user errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/DynamoDB \
  --metric-name UserErrors \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Backup & Restore

```bash
# Tạo backup
aws dynamodb create-backup \
  --table-name <TABLE_NAME> \
  --backup-name lexi-backup-2026-05-02 \
  --region <REGION>

# Liệt kê backups
aws dynamodb list-backups \
  --table-name <TABLE_NAME> \
  --region <REGION>

# Restore từ backup
aws dynamodb restore-table-from-backup \
  --target-table-name <NEW_TABLE_NAME> \
  --backup-arn <BACKUP_ARN> \
  --region <REGION>
```

## Khắc phục sự cố

**Vấn đề: Truy cập bị từ chối**

```bash
# Kiểm tra IAM role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Vấn đề: Bảng không tồn tại**

```bash
# Kiểm tra table status
aws dynamodb describe-table \
  --table-name <TABLE_NAME> \
  --region <REGION>
```

**Vấn đề: Quá nhiều mục**

```bash
# Xóa items cũ
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --filter-expression "attribute_exists(#pk)" \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [API Gateway](../5.3.3-API-Gateway/) để học cách cấu hình API endpoints.

### Danh sách kiểm tra

- [ ] DynamoDB table active
- [ ] Single-table design hiểu rõ
- [ ] Query/Scan test thành công
- [ ] Metrics visible
- [ ] Backup configured
