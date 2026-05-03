---
title: "Giám sát & Tối ưu Chi phí"
date: "2026-05-02"
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Giới thiệu

Phần này hướng dẫn cách giám sát ứng dụng Lexi và tối ưu hóa chi phí AWS.

## Nội dung

Giám sát được chia thành 2 phần:

1. **[CloudWatch](5.7.1-CloudWatch/)** - Giám sát ứng dụng
   - Xem logs từ Lambda, API Gateway
   - Giám sát metrics (invocations, errors, duration)
   - Tạo alarms
   - Tạo dashboards

2. **[Cost Optimization](5.7.2-Cost-Optimization/)** - Tối ưu hóa chi phí
   - Tối ưu DynamoDB
   - Tối ưu Lambda
   - Tối ưu S3
   - Tối ưu Bedrock
   - AWS Budgets
   - Cost Explorer

## Danh sách Kiểm tra

Sau khi hoàn thành phần Giám sát, bạn nên:

- [ ] Biết cách xem logs từ Lambda và API Gateway
- [ ] Nắm được cách giám sát metrics (invocations, errors, duration)
- [ ] Biết cách tạo CloudWatch alarms
- [ ] Biết cách tạo CloudWatch dashboards
- [ ] Hiểu cách tối ưu hóa chi phí DynamoDB
- [ ] Hiểu cách tối ưu hóa chi phí Lambda
- [ ] Hiểu cách tối ưu hóa chi phí S3
- [ ] Hiểu cách tối ưu hóa chi phí Bedrock
- [ ] Biết cách sử dụng AWS Budgets
- [ ] Biết cách sử dụng Cost Explorer

> **📸 CẦN THÊM**: Thêm screenshot CloudWatch dashboard

> **📸 CẦN THÊM**: Thêm screenshot Cost Explorer

## Bước Tiếp Theo

Tiếp tục đến [Dọn dẹp & Quản lý Tài nguyên](../5.8-Clean-up/) để học cách xóa resources khi không cần thiết.
