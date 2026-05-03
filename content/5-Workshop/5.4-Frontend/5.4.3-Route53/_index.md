---
title: "Route53 - Custom Domain"
date: "2025-12-07"
weight: 3
chapter: false
pre: "<b> 5.4.3 </b>"
---

## Configure Custom Domain with Route53

**Route53** manages DNS and routes traffic to CloudFront or Amplify.

### Step 1: Create Hosted Zone

1. Go to **AWS Console → Route53 → Create hosted zone**
2. Domain name: `yourdomain.com`
3. Type: Public hosted zone
4. Save the **Name Servers** to update at your domain registrar

### Step 2: Configure DNS Records

**A Record for CloudFront** (if using CloudFront):
```
Record name: (leave empty or www)
Record type: A
Alias: Yes
Route traffic to: CloudFront distribution
```

**CNAME for Amplify** (if not using CloudFront):
```
Record name: app
Record type: CNAME
Value: xxx.amplifyapp.com
```

### Step 3: Configure SSL Certificate (ACM)

1. Go to **AWS Console → Certificate Manager**
2. Request certificate (must be in **us-east-1** region for CloudFront)
3. Domain: `yourdomain.com`, `*.yourdomain.com`
4. Validation: DNS validation
5. Add CNAME records to Route53

### Troubleshooting

**DNS not resolving**: Check Name Servers at domain registrar
```bash
nslookup yourdomain.com
```

**Certificate validation failed**: Confirm CNAME records in Route53
```bash
aws route53 list-resource-record-sets --hosted-zone-id <ZONE_ID>
```

**Traffic not routing to CloudFront**: Check A record alias
```bash
dig yourdomain.com
```

### Checklist

- [ ] Hosted Zone created successfully
- [ ] Name Servers updated at domain registrar
- [ ] A Record or CNAME created successfully
- [ ] SSL Certificate requested
- [ ] Certificate validation CNAME added
- [ ] DNS resolving correctly
- [ ] Domain pointing to CloudFront/Amplify

## Next Steps

You have completed the Frontend section! Continue to [AI & Voice](../../5.5-AI-Voice/) to integrate AI and voice processing.
