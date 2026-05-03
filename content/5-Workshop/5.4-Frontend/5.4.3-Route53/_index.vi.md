---
title: "Route53 - Custom Domain"
date: "2025-12-07"
weight: 3
chapter: false
pre: "<b> 5.4.3 </b>"
---

## Cấu hình Custom Domain với Route53

**Route53** quản lý DNS và định tuyến traffic đến CloudFront hoặc Amplify.

### Bước 1: Tạo Hosted Zone

1. Vào **AWS Console → Route53 → Create hosted zone**
2. Domain name: `yourdomain.com`
3. Type: Public hosted zone
4. Lưu lại **Name Servers** để cập nhật tại domain registrar

### Bước 2: Cấu hình DNS Records

**A Record cho CloudFront** (nếu dùng CloudFront):
```
Record name: (để trống hoặc www)
Record type: A
Alias: Yes
Route traffic to: CloudFront distribution
```

**CNAME cho Amplify** (nếu không dùng CloudFront):
```
Record name: app
Record type: CNAME
Value: xxx.amplifyapp.com
```

### Bước 3: Cấu hình SSL Certificate (ACM)

1. Vào **AWS Console → Certificate Manager**
2. Request certificate (phải ở region **us-east-1** cho CloudFront)
3. Domain: `yourdomain.com`, `*.yourdomain.com`
4. Validation: DNS validation
5. Thêm CNAME records vào Route53

### Khắc phục sự cố

**DNS not resolving**: Kiểm tra Name Servers tại domain registrar
```bash
nslookup yourdomain.com
```

**Certificate validation failed**: Xác nhận CNAME records trong Route53
```bash
aws route53 list-resource-record-sets --hosted-zone-id <ZONE_ID>
```

**Traffic not routing to CloudFront**: Kiểm tra A record alias
```bash
dig yourdomain.com
```

### Danh sách kiểm tra

- [ ] Hosted Zone tạo thành công
- [ ] Name Servers cập nhật tại domain registrar
- [ ] A Record hoặc CNAME tạo thành công
- [ ] SSL Certificate requested
- [ ] Certificate validation CNAME added
- [ ] DNS resolving đúng
- [ ] Domain trỏ đến CloudFront/Amplify

## Bước Tiếp Theo

Bạn đã hoàn thành phần Frontend! Tiếp tục đến [AI & Voice](../../5.5-AI-Voice/) để tích hợp AI và xử lý giọng nói.