---
title: "S3 Storage"
date: "2026-05-02"
weight: 5
chapter: false
pre: " <b> 5.3.5. </b> "
---

## Tổng quan

Lexi sử dụng **S3 bucket** để lưu trữ audio files từ speaking practice:
- **Encryption**: AES256 (Server-side encryption)
- **CORS**: Cho phép frontend upload/download
- **Lifecycle**: Tự động xóa files cũ

### Kiểm tra S3 Buckets

```bash
# Liệt kê tất cả S3 buckets
aws s3 ls

# Hoặc dùng S3 API
aws s3api list-buckets
```

### Lấy Bucket Name

```bash
# Liệt kê buckets của Lexi
aws s3 ls | grep lexi

# Hoặc lấy từ CloudFormation stack
aws cloudformation describe-stacks \
  --stack-name lexi-be \
  --region <REGION> \
  --query 'Stacks[0].Outputs[?OutputKey==`SpeakingAudioBucket`].OutputValue' \
  --output text
```

### Kiểm tra Bucket Configuration

```bash
# Lấy bucket encryption
aws s3api get-bucket-encryption \
  --bucket <BUCKET_NAME>

# Lấy CORS configuration
aws s3api get-bucket-cors \
  --bucket <BUCKET_NAME>

# Lấy bucket versioning
aws s3api get-bucket-versioning \
  --bucket <BUCKET_NAME>

# Lấy bucket lifecycle
aws s3api get-bucket-lifecycle-configuration \
  --bucket <BUCKET_NAME>
```

### Liệt kê Objects

```bash
# Liệt kê tất cả objects
aws s3 ls s3://<BUCKET_NAME>/ --recursive

# Liệt kê objects với summary
aws s3 ls s3://<BUCKET_NAME>/ --recursive --summarize

# Liệt kê objects trong folder
aws s3 ls s3://<BUCKET_NAME>/audio/ --recursive
```

### Upload File

```bash
# Upload file
aws s3 cp <LOCAL_FILE> s3://<BUCKET_NAME>/<KEY>

# Upload với metadata
aws s3api put-object \
  --bucket <BUCKET_NAME> \
  --key <KEY> \
  --body <LOCAL_FILE> \
  --metadata "user-id=123,session-id=456"

# Upload với server-side encryption
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

### Lấy Object Metadata

```bash
# Lấy object metadata
aws s3api head-object \
  --bucket <BUCKET_NAME> \
  --key <KEY>

# Kết quả sẽ bao gồm**:
# - ContentLength
# - LastModified
# - ETag
# - Metadata
# - ServerSideEncryption
```

### Xóa Objects

```bash
# Xóa single object
aws s3 rm s3://<BUCKET_NAME>/<KEY>

# Xóa multiple objects
aws s3 rm s3://<BUCKET_NAME>/audio/ --recursive

# Xóa với filter
aws s3 rm s3://<BUCKET_NAME>/ --recursive --exclude "*" --include "*.wav"
```

### Tạo Presigned URL

```bash
# Tạo presigned URL cho download (1 giờ)
aws s3 presign s3://<BUCKET_NAME>/<KEY> \
  --expires-in 3600

# Hoặc dùng S3 API
aws s3api generate-presigned-url \
  --client-method get-object \
  --bucket <BUCKET_NAME> \
  --key <KEY> \
  --expires-in 3600
```

### Giám sát S3 Metrics

```bash
# Xem bucket size
aws s3api list-bucket-metrics-configurations \
  --bucket <BUCKET_NAME>

# Hoặc dùng CloudWatch
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
# Tạo lifecycle policy (xóa files sau 30 ngày)
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

# Áp dụng lifecycle policy
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

### Khắc phục sự cố

**Vấn đề: Access Denied**

```bash
# Kiểm tra bucket policy
aws s3api get-bucket-policy \
  --bucket <BUCKET_NAME>

# Kiểm tra IAM role
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Vấn đề: CORS error**

```bash
# Kiểm tra CORS configuration
aws s3api get-bucket-cors \
  --bucket <BUCKET_NAME>

# Cập nhật CORS
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

**Vấn đề: File quá lớn**

```bash
# Sử dụng multipart upload
aws s3 cp <LARGE_FILE> s3://<BUCKET_NAME>/<KEY> \
  --storage-class STANDARD_IA \
  --metadata "user-id=123"
```

## Bước Tiếp Theo

Bạn đã hoàn thành phần Backend! Tiếp tục đến [Frontend](../../5.4-Frontend/) để xây dựng giao diện người dùng.

### Danh sách kiểm tra

- [ ] S3 bucket tạo thành công
- [ ] CORS configured
- [ ] Encryption enabled
- [ ] Upload/Download test thành công
- [ ] Lifecycle policy configured
