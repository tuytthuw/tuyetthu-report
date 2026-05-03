---
title: "Dọn dẹp & Quản lý Tài nguyên"
date: "2026-05-02"
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Xóa CloudFormation Stacks

Xóa stacks theo thứ tự ngược lại (xóa main stack trước, sau đó auth lambdas, auth base, cuối cùng database):

Xóa main stack:
```bash
aws cloudformation delete-stack \
  --stack-name <MAIN_STACK_NAME> \
  --region <REGION>
```

Xóa auth lambdas stack:
```bash
aws cloudformation delete-stack \
  --stack-name <AUTH_LAMBDAS_STACK_NAME> \
  --region <REGION>
```

Xóa auth base stack:
```bash
aws cloudformation delete-stack \
  --stack-name <AUTH_BASE_STACK_NAME> \
  --region <REGION>
```

Xóa database stack:
```bash
aws cloudformation delete-stack \
  --stack-name <DATABASE_STACK_NAME> \
  --region <REGION>
```

Kiểm tra trạng thái xóa:
```bash
aws cloudformation list-stacks \
  --region <REGION> \
  --query 'StackSummaries[?StackStatus==`DELETE_IN_PROGRESS`]'
```

**Lưu ý**: Xóa stacks sẽ xóa tất cả resources được tạo bởi CloudFormation (Lambda, DynamoDB, API Gateway, etc.)

## Xóa S3 Buckets

Làm trống bucket (xóa tất cả objects):
```bash
aws s3 rm s3://<BUCKET_NAME> --recursive
```

Xóa bucket:
```bash
aws s3 rb s3://<BUCKET_NAME>
```

Hoặc xóa tất cả buckets của Lexi:
```bash
aws s3 ls | grep lexi | awk '{print $3}' | xargs -I {} aws s3 rb s3://{} --force
```

**Lưu ý**: S3 bucket phải trống trước khi xóa.

## Xóa Amplify App

Xóa Amplify app (từ project directory):
```bash
amplify delete
```

Hoặc qua AWS CLI:
```bash
aws amplify delete-app \
  --app-id <APP_ID> \
  --region <REGION>
```

## Xóa CloudWatch Logs

Xóa log group cụ thể:
```bash
aws logs delete-log-group \
  --log-group-name <LOG_GROUP_NAME> \
  --region <REGION>
```

Hoặc xóa tất cả log groups của Lexi:
```bash
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION> \
  --query 'logGroups[].logGroupName' \
  --output text | xargs -I {} aws logs delete-log-group --log-group-name {} --region <REGION>
```

## Xóa IAM Roles

Liệt kê roles:
```bash
aws iam list-roles \
  --query 'Roles[?contains(RoleName, `lexi`)].RoleName'
```

Xóa role (phải xóa inline policies trước):
```bash
aws iam delete-role --role-name <ROLE_NAME>
```

## Kiểm tra Tài nguyên Còn lại

Sau khi xóa, hãy kiểm tra xem có tài nguyên nào còn lại không:

Kiểm tra Lambda functions:
```bash
aws lambda list-functions \
  --region <REGION> \
  --query 'Functions[?contains(FunctionName, `lexi`)].FunctionName'
```

Kiểm tra DynamoDB tables:
```bash
aws dynamodb list-tables --region <REGION>
```

Kiểm tra S3 buckets:
```bash
aws s3 ls | grep lexi
```

Kiểm tra Cognito user pools:
```bash
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION>
```

Kiểm tra API Gateway:
```bash
aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[?contains(name, `lexi`)].name'
```

## Kiểm tra Chi phí

Xem chi phí hiện tại:
```bash
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE
```

Xem chi phí theo tháng:
```bash
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-31 \
  --granularity MONTHLY \
  --metrics "UnblendedCost"
```

## Danh sách Kiểm tra Dọn dẹp

Sau khi hoàn thành dọn dẹp, bạn nên đã:

- [ ] Xóa tất cả CloudFormation stacks
- [ ] Xóa tất cả S3 buckets
- [ ] Xóa Lambda functions (nếu còn)
- [ ] Xóa DynamoDB tables (nếu còn)
- [ ] Xóa Cognito user pools (nếu còn)
- [ ] Xóa API Gateway (nếu còn)
- [ ] Xóa CloudWatch logs
- [ ] Xóa IAM roles không cần thiết
- [ ] Xóa Amplify app
- [ ] Kiểm tra chi phí cuối cùng

## Tóm tắt Workshop

Bạn đã hoàn thành workshop Lexi! Bạn đã học:

✅ Kiến trúc serverless của Lexi
✅ Triển khai Lambda, DynamoDB, API Gateway
✅ Tích hợp Cognito, Bedrock, Transcribe, Polly
✅ Xây dựng WebSocket real-time
✅ Triển khai frontend với Next.js + Amplify
✅ Thiết lập CI/CD pipeline
✅ Giám sát và tối ưu chi phí
✅ Quản lý tài nguyên AWS

## Bước Tiếp Theo

- Tối ưu hóa hiệu suất
- Thêm tính năng mới
- Mở rộng quy mô
- Tích hợp thêm dịch vụ AWS

Chúc mừng bạn đã hoàn thành workshop!
