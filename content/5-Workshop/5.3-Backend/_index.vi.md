---
title: "Backend Deployment"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Giới thiệu

Backend Lexi triển khai trên AWS Serverless với:
- **23 Lambda Functions** for business logic
- **DynamoDB** for data storage (Single-table design)
- **API Gateway** (REST + WebSocket) for communication
- **Cognito** for user authentication
- **S3** for audio file storage

## Nội dung

Backend được chia thành 5 phần chính:

1. **[Lambda Functions](5.3.1-Lambda/)** - Logic nghiệp vụ
   - 23 functions tổ chức theo modules
   - Kiểm tra, invoke, giám sát functions
   - Troubleshoot Lambda issues

2. **[DynamoDB](5.3.2-DynamoDB/)** - Lưu trữ dữ liệu
   - Single-table design
   - Query, scan, put, get, update, delete items
   - Backup & restore
   - Giám sát metrics

3. **[API Gateway](5.3.3-API-Gateway/)** - Giao tiếp frontend-backend
   - REST API endpoints
   - WebSocket API cho real-time
   - Authorizer (Cognito)
   - Test endpoints
   - Giám sát metrics

4. **[Cognito](5.3.4-Cognito/)** - Xác thực người dùng
   - User Pool management
   - App Client configuration
   - Lambda triggers (PreSignUp, PostConfirmation, PostAuthentication)
   - User management (create, update, delete)
   - Giám sát metrics

5. **[S3 Storage](5.3.5-S3/)** - Lưu trữ audio files
   - Bucket configuration
   - Upload/download files
   - Presigned URLs
   - Lifecycle policies
   - Giám sát metrics

## Kiến trúc Deployment

**CloudFormation Stacks:**
- `lexi-database` - DynamoDB
- `lexi-auth-base` - Cognito
- `lexi-auth-lambdas` - Auth triggers
- `lexi-be` - Main app (Lambda, API Gateway, S3)

## Kiểm tra Stacks Hiện tại

Trước khi bắt đầu, kiểm tra CloudFormation stacks đã tồn tại:

```bash
aws cloudformation list-stacks \
  --region <REGION> \
  --query 'StackSummaries[?StackStatus!=`DELETE_COMPLETE`]' \
  --output table
```

Kiểm tra stack cụ thể:
```bash
aws cloudformation describe-stacks \
  --stack-name <STACK_NAME> \
  --region <REGION>
```

**Kết quả mong đợi**: Bạn sẽ thấy 3-4 stacks:
- `lexi-database` - DynamoDB table
- `lexi-auth-base` - Cognito User Pool
- `lexi-auth-lambdas` - Auth Lambda triggers
- `lexi-be` - Main application (Lambda, API Gateway, S3)

## Danh sách Kiểm tra

Sau khi hoàn thành phần Backend, bạn nên:

- [ ] Hiểu kiến trúc backend của Lexi
- [ ] Biết cách kiểm tra CloudFormation stacks
- [ ] Nắm được 23 Lambda functions và chức năng của chúng
- [ ] Hiểu Single-table design của DynamoDB
- [ ] Biết cách sử dụng API Gateway (REST + WebSocket)
- [ ] Nắm được Cognito authentication flow
- [ ] Biết cách quản lý S3 storage

## Bước Tiếp Theo

Bạn đã hoàn thành phần Backend! Tiếp tục đến [Frontend Development](../5.4-Frontend/) để xây dựng giao diện người dùng.
