---
title: "CloudFront & WAF"
date: "2025-12-07"
weight: 2
chapter: false
pre: "<b> 5.4.2 </b>"
---

## Bảo vệ Frontend với CloudFront & WAF

**CloudFront** là CDN toàn cầu, **WAF** cung cấp bảo vệ DDoS và tấn công web.

### Bước 1: Tạo WAF Web ACL

1. Vào **AWS Console → WAF & Shield → Create web ACL**
2. Cấu hình:
   - Name: `lexi-waf`
   - Resource type: CloudFront distributions
   - Region: Global (CloudFront)

3. Thêm AWS Managed Rules:
   - `AWSManagedRulesCommonRuleSet` - Bảo vệ lỗ hổng phổ biến
   - `AWSManagedRulesSQLiRuleSet` - Chống SQL injection
   - `AWSManagedRulesKnownBadInputsRuleSet` - Chặn input xấu

4. Thêm Rate Limiting:
   - Name: `RateLimitRule`
   - Limit: 2000 requests per 5 minutes per IP

### Bước 2: Tạo CloudFront Distribution

1. Vào **AWS Console → CloudFront → Create distribution**

2. **Origin Settings**:
   - Origin domain: Amplify app URL (xxx.amplifyapp.com)
   - Protocol: HTTPS only

3. **Default Cache Behavior**:
   - Viewer protocol: Redirect HTTP to HTTPS
   - Allowed methods: GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE
   - Cache policy: CachingOptimized
   - Origin request policy: AllViewer

4. **Settings**:
   - Price class: Use all edge locations
   - WAF web ACL: Chọn WAF đã tạo
   - SSL certificate: Default CloudFront certificate

5. **Thêm Origin cho API Gateway**:
   - Add origin: API Gateway URL
   - Create behavior: `/api/*` → API Gateway origin

### Khắc phục sự cố

**CloudFront returns 403**: Kiểm tra origin security group
```bash
aws cloudfront get-distribution --id <DISTRIBUTION_ID>
```

**WAF blocking legitimate traffic**: Review WAF logs
```bash
aws wafv2 get-sampled-requests --web-acl-arn <WAF_ARN>
```

**Cache not updating**: Invalidate CloudFront cache
```bash
aws cloudfront create-invalidation --distribution-id <ID> --paths "/*"
```

### Danh sách kiểm tra

- [ ] WAF Web ACL tạo thành công
- [ ] CloudFront distribution tạo thành công
- [ ] WAF gắn vào CloudFront
- [ ] Origin settings đúng
- [ ] Cache behavior cấu hình
- [ ] SSL certificate hoạt động
- [ ] API Gateway origin thêm thành công

## Bước Tiếp Theo

Tiếp tục đến [Route53](../5.4.3-Route53/) để cấu hình custom domain.