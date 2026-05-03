---
title: "DynamoDB"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Overview

Lexi uses **Single-table design** with DynamoDB:
- **Table Name**: LexiApp
- **Billing Mode**: PAY_PER_REQUEST (on-demand)
- **Primary Key**: PK (Partition Key) + SK (Sort Key)
- **Global Secondary Indexes**: GSI1-5 for different query patterns

### Check DynamoDB Table

```bash
# List all tables
aws dynamodb list-tables --region <REGION>

# Describe table in detail
aws dynamodb describe-table \
  --table-name <TABLE_NAME> \
  --region <REGION>
```

**Result will include**:
- TableName, TableStatus (ACTIVE)
- ItemCount, TableSizeBytes
- BillingModeSummary (PAY_PER_REQUEST)
- KeySchema (PK, SK)
- GlobalSecondaryIndexes (GSI1-5)

### Understand Single-Table Design

**Partition Key (PK)**: Entity type + ID
```
USER#<user_id>
FLASHCARD#<flashcard_id>
SESSION#<session_id>
SCENARIO#<scenario_id>
```

**Sort Key (SK)**: Timestamp or entity type
```
CREATED#2026-05-02T10:30:00Z
UPDATED#2026-05-02T10:30:00Z
METADATA#<type>
```

### Scan Table

```bash
# Scan table (retrieve items)
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --region <REGION> \
  --limit 10

# Scan with filter
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --filter-expression "attribute_exists(#pk)" \
  --expression-attribute-names '{"#pk":"PK"}' \
  --region <REGION>
```

### Query Table

```bash
# Query with partition key
aws dynamodb query \
  --table-name <TABLE_NAME> \
  --key-condition-expression "PK = :pk" \
  --expression-attribute-values '{":pk":{"S":"USER#<user_id>"}}' \
  --region <REGION>

# Query with partition key + sort key
aws dynamodb query \
  --table-name <TABLE_NAME> \
  --key-condition-expression "PK = :pk AND begins_with(SK, :sk)" \
  --expression-attribute-values '{":pk":{"S":"USER#<user_id>"},":sk":{"S":"CREATED"}}' \
  --region <REGION>
```

### Put Item

```bash
# Add item to table
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
# Get item from table
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
# Update item
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
# Delete item
aws dynamodb delete-item \
  --table-name <TABLE_NAME> \
  --key '{
    "PK": {"S": "USER#123"},
    "SK": {"S": "CREATED#2026-05-02T10:30:00Z"}
  }' \
  --region <REGION>
```

### Monitor DynamoDB Metrics

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

# View user errors
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
# Create backup
aws dynamodb create-backup \
  --table-name <TABLE_NAME> \
  --backup-name lexi-backup-2026-05-02 \
  --region <REGION>

# List backups
aws dynamodb list-backups \
  --table-name <TABLE_NAME> \
  --region <REGION>

# Restore from backup
aws dynamodb restore-table-from-backup \
  --target-table-name <NEW_TABLE_NAME> \
  --backup-arn <BACKUP_ARN> \
  --region <REGION>
```

## Troubleshooting

**Issue: Access denied**

```bash
# Check IAM role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Issue: Table does not exist**

```bash
# Check table status
aws dynamodb describe-table \
  --table-name <TABLE_NAME> \
  --region <REGION>
```

**Issue: Too many items**

```bash
# Delete old items
aws dynamodb scan \
  --table-name <TABLE_NAME> \
  --filter-expression "attribute_exists(#pk)" \
  --region <REGION>
```

## Next Steps

Continue to [API Gateway](../5.3.3-API-Gateway/) to learn how to configure API endpoints.

### Checklist

- [ ] DynamoDB table active
- [ ] Single-table design understood
- [ ] Query/Scan test successful
- [ ] Metrics visible
- [ ] Backup configured
