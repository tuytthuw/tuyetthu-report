---
title: "CI/CD Pipeline"
date: "2026-05-02"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Giới thiệu

CI/CD (Continuous Integration/Continuous Deployment) tự động hóa quá trình build, test, và deploy ứng dụng. Phần này hướng dẫn thiết lập CI/CD cho Lexi.

## Nội dung

CI/CD được chia thành 3 phần:

1. **[GitHub Actions](5.6.1-GitHub-Actions/)** - Tự động hóa CI/CD
2. **[SAM Deployment](5.6.2-SAM-Deploy/)** - Triển khai backend
3. **[Amplify Auto-Deploy](5.6.3-Amplify-Deploy/)** - Triển khai frontend

## Quy trình Triển khai

### Backend (SAM)

Quy trình:
1. Developer push code → GitHub Repository
2. GitHub Actions trigger
3. SAM build ứng dụng
4. SAM deploy lên AWS
5. CloudFormation cập nhật Lambda functions
6. Tự động rollback nếu có lỗi

### Frontend (Amplify)

Quy trình:
1. Developer push code → GitHub Repository
2. Amplify webhook trigger
3. Cài đặt dependencies (pnpm)
4. Build ứng dụng
5. Deploy lên CloudFront CDN
6. Invalidate cache

## Môi trường Triển khai

| Môi trường | Branch | Backend Stack | Frontend URL |
|-----------|--------|---------------|--------------|
| Development | develop | lexi-be-dev | develop.d<APP_ID>.amplifyapp.com |
| Staging | staging | lexi-be-staging | staging.d<APP_ID>.amplifyapp.com |
| Production | main | lexi-be | custom domain |

## Kiểm tra Triển khai

### GitHub Actions

Xem workflow runs:
```bash
gh run list
gh run view <RUN_ID>
gh run view <RUN_ID> --log
```

### CloudFormation

Kiểm tra stack events:
```bash
aws cloudformation list-stacks --region <REGION>
aws cloudformation describe-stack-events \
  --stack-name lexi-be \
  --region <REGION>
```

### Amplify

Kiểm tra build jobs:
```bash
aws amplify list-apps --region <REGION>
aws amplify list-jobs \
  --app-id <APP_ID> \
  --branch-name main \
  --region <REGION> \
  --max-results 5
```

## Danh sách Kiểm tra

Sau khi hoàn thành phần CI/CD, bạn nên:

- [ ] Hiểu quy trình CI/CD của Lexi
- [ ] Biết cách setup GitHub repository
- [ ] Nắm được cách cấu hình GitHub Secrets
- [ ] Biết cách tạo GitHub Actions workflows
- [ ] Hiểu backend deployment với SAM
- [ ] Hiểu frontend deployment với Amplify
- [ ] Biết cách kiểm tra triển khai
- [ ] Nắm được chiến lược rollback
- [ ] Hiểu quản lý môi trường (dev, staging, production)

## Bước Tiếp Theo

Tiếp tục đến [Giám sát & Tối ưu Chi phí](../5.7-Monitoring/) để học cách giám sát và tối ưu hóa chi phí.

