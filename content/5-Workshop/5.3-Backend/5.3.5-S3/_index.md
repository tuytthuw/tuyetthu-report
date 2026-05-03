---
title: "S3 Storage"
date: "2026-05-02"
weight: 5
chapter: false
pre: " <b> 5.3.5. </b> "
---

## Overview

Lexi uses **S3 bucket** to store audio files from speaking practice:
- **Encryption**: AES256 (Server-side encryption)
- **CORS**: Allows frontend upload/download
- **Lifecycle**: Auto-delete old files

### Check S3 Buckets

```bash
# List all S3 buckets
aws s3 ls

# Or use S3 API
aws s3api list-buckets
```

### Get Bucket Name

```bash
# List Lexi buckets
aws s3 ls | grep lexi

# Or get from CloudFormation stack
aws cloudformation describe-stacks \
  --stack-name lexi-be \
  --region <REGION> \
  --query 'Stacks[0].Outputs[?OutputKey==`SpeakingAudioBucket`].OutputValue' \
  --output text
```

### Check Bucket Configuration

```bash
# Get bucket encryption
aws s3api get-bucket-encryption \
  --bucket <BUCKET_NAME>

# Get CORS configuration
aws s3api get-bucket-cors \
  --bucket <BUCKET_NAME>

# Get bucket versioning
aws s3api get-bucket-versioning \
  --bucket <BUCKET_NAME>

# Get bucket lifecycle
aws s3api get-bucket-lifecycle-configuration \
  --bucket <BUCKET_NAME>
```

### List Objects

```bash
# List all objects
aws s3 ls s3://<BUCKET_NAME>/ --recursive

# List objects with summary
aws s3 ls s3://<BUCKET_NAME>/ --recursive --summarize

# List objects in folder
aws s3 ls s3://<BUCKET_NAME>/audio/ --recursive
```

### Upload File

```bash
# Upload file
aws s3 cp <LOCAL_FILE> s3://<BUCKET_NAME>/<KEY>

# Upload with metadata
aws s3api put-object \
  --bucket <BUCKET_NAME> \
  --key <KEY> \
  --body <LOCAL_FILE> \
  --metadata "user-id=123,session-id=456"

# Upload with server-side encryption
aws s3api put-object \
  --bucket <BUCKET_NAME> \
  --key <KEY> \
  --body <LOCAL_FILE> \
  --server-side-encryption AES256
```

### Download File

```bash
# Download file
aws s3 cp s3://<BUCKET_NAME>/<KEY> <LOCAL_FILE>

# Download folder
aws s3 cp s3://<BUCKET_NAME>/audio/ ./audio/ --recursive
```

### Get Object Metadata

```bash
# Get object metadata
aws s3api head-object \
  --bucket <BUCKET_NAME> \
  --key <KEY>

# Results will include:
# - ContentLength
# - LastModified
# - ETag
# - Metadata
# - ServerSideEncryption
```

### Delete Objects

```bash
# Delete single object
aws s3 rm s3://<BUCKET_NAME>/<KEY>

# Delete multiple objects
aws s3 rm s3://<BUCKET_NAME>/audio/ --recursive

# Delete with filter
aws s3 rm s3://<BUCKET_NAME>/ --recursive --exclude "*" --include "*.wav"
```

### Create Presigned URL

```bash
# Create presigned URL for download (1 hour)
aws s3 presign s3://<BUCKET_NAME>/<KEY> \
  --expires-in 3600

# Or use S3 API
aws s3api generate-presigned-url \
  --client-method get-object \
  --bucket <BUCKET_NAME> \
  --key <KEY> \
  --expires-in 3600
```

### Monitor S3 Metrics

```bash
# View bucket size
aws s3api list-bucket-metrics-configurations \
  --bucket <BUCKET_NAME>

# Or use CloudWatch
aws cloudwatch get-metric-statistics \
  --namespace AWS/S3 \
  --metric-name BucketSizeBytes \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 86400 \
  --statistics Average \
  --dimensions Name=BucketName,Value=<BUCKET_NAME> Name=StorageType,Value=StandardStorage
```

### Lifecycle Policy

```bash
# Create lifecycle policy (delete files after 30 days)
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

# Apply lifecycle policy
aws s3api put-bucket-lifecycle-configuration \
  --bucket <BUCKET_NAME> \
  --lifecycle-configuration file://lifecycle.json
```

### Backup & Restore

```bash
# Backup bucket
aws s3 sync s3://<BUCKET_NAME>/ ./backup/ --recursive

# Restore bucket
aws s3 sync ./backup/ s3://<BUCKET_NAME>/ --recursive
```

### Troubleshooting

**Issue: Access Denied**

```bash
# Check bucket policy
aws s3api get-bucket-policy \
  --bucket <BUCKET_NAME>

# Check IAM role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Issue: CORS error**

```bash
# Check CORS configuration
aws s3api get-bucket-cors \
  --bucket <BUCKET_NAME>

# Update CORS
cat > cors.json << EOF
{
  "CORSRules": [
    {
      "AllowedOrigins": ["*"],
      "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
      "AllowedHeaders": ["*"],
      "MaxAgeSeconds": 3000,
      "ExposeHeaders": ["ETag", "x-amz-version-id"]
    }
  ]
}
EOF

aws s3api put-bucket-cors \
  --bucket <BUCKET_NAME> \
  --cors-configuration file://cors.json
```

**Issue: File too large**

```bash
# Use multipart upload
aws s3 cp <LARGE_FILE> s3://<BUCKET_NAME>/<KEY> \
  --storage-class STANDARD_IA \
  --metadata "user-id=123"
```

## Next Steps

You have completed the Backend section! Continue to [Frontend](../../5.4-Frontend/) to build the user interface.

### Checklist

- [ ] S3 bucket created successfully
- [ ] CORS configured
- [ ] Encryption enabled
- [ ] Upload/Download test successful
- [ ] Lifecycle policy configured
