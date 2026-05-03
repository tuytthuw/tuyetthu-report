---
title: "Amazon Amplify"
date: "2025-12-07"
weight: 1
chapter: false
pre: "<b> 5.4.1 </b>"
---

## Triển khai Frontend với Amplify

**Amazon Amplify** cung cấp 2 cách deploy Next.js frontend: Amplify Console (tự động) hoặc Amplify CLI (thủ công).

### Cách 1: Amplify Console (Recommended)

**Bước 1-3**: Kết nối repository
1. Vào **AWS Console → Amplify → Create new app**
2. Chọn **Host web app**
3. Kết nối GitHub/GitLab/Bitbucket repository

**Bước 4**: Cấu hình build settings

File `amplify.yml` (đã có trong project):
```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

**Bước 5**: Thiết lập Environment Variables
```
VITE_API_BASE_URL = <API_Gateway_URL>
VITE_COGNITO_USER_POOL_ID = <User_Pool_ID>
VITE_COGNITO_CLIENT_ID = <Client_ID>
VITE_COGNITO_REGION = ap-southeast-1
```

**Bước 6**: Deploy - Amplify sẽ tự động build và deploy khi push code

### Cách 2: Amplify CLI (Manual)

```bash
# Cài đặt CLI
npm install -g @aws-amplify/cli

# Cấu hình AWS credentials
amplify configure

# Khởi tạo Amplify trong project
cd lexi-fe
amplify init

# Thêm hosting
amplify add hosting
# Chọn: Hosting with Amplify Console
# Chọn: Manual deployment

# Deploy
amplify publish
```

### Khắc phục sự cố

**Build failed**: Kiểm tra `amplify.yml` và `requirements.txt`
```bash
amplify logs --follow
```

**Environment variables không load**: Xác nhận biến được set trong Amplify Console
```bash
# Verify locally
echo $VITE_API_BASE_URL
```

**Deploy timeout**: Tăng timeout trong Amplify settings (default 30 min)

### Danh sách kiểm tra

- [ ] Repository kết nối với Amplify
- [ ] Build settings cấu hình đúng
- [ ] Environment variables đã set
- [ ] Build thành công
- [ ] Amplify URL hoạt động
- [ ] Frontend kết nối được API Gateway

## Bước Tiếp Theo

Tiếp tục đến [CloudFront & WAF](../5.4.2-CloudFront&WAF/) để bảo vệ frontend.


