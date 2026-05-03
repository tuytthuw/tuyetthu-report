---
title: "CloudFront & WAF"
date: "2025-12-07"
weight: 2
chapter: false
pre: "<b> 5.4.2 </b>"
---

## Protect Frontend with CloudFront & WAF

**CloudFront** is a global CDN, **WAF** provides DDoS and web attack protection.

### Step 1: Create WAF Web ACL

1. Go to **AWS Console → WAF & Shield → Create web ACL**
2. Configure:
   - Name: `lexi-waf`
   - Resource type: CloudFront distributions
   - Region: Global (CloudFront)

3. Add AWS Managed Rules:
   - `AWSManagedRulesCommonRuleSet` - Protects against common vulnerabilities
   - `AWSManagedRulesSQLiRuleSet` - Prevents SQL injection
   - `AWSManagedRulesKnownBadInputsRuleSet` - Blocks malicious input

4. Add Rate Limiting:
   - Name: `RateLimitRule`
   - Limit: 2000 requests per 5 minutes per IP

### Step 2: Create CloudFront Distribution

1. Go to **AWS Console → CloudFront → Create distribution**

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
   - WAF web ACL: Select the WAF created
   - SSL certificate: Default CloudFront certificate

5. **Add Origin for API Gateway**:
   - Add origin: API Gateway URL
   - Create behavior: `/api/*` → API Gateway origin

### Troubleshooting

**CloudFront returns 403**: Check origin security group
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

### Checklist

- [ ] WAF Web ACL created successfully
- [ ] CloudFront distribution created successfully
- [ ] WAF attached to CloudFront
- [ ] Origin settings correct
- [ ] Cache behavior configured
- [ ] SSL certificate working
- [ ] API Gateway origin added successfully

## Next Steps

Continue to [Route53](../5.4.3-Route53/) to configure custom domain.
