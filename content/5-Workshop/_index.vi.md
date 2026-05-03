---
title: "Workshop"
date: "2026-05-02"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Lexi - AI Tutor Platform: Serverless Workshop

## Overview

**Lexi** là nền tảng học tiếng Anh ứng dụng AI, được xây dựng trên kiến trúc **AWS Serverless** hoàn toàn. Workshop này hướng dẫn triển khai, vận hành và mở rộng hệ thống từ phát triển đến production.

### Architecture

Lexi sử dụng các dịch vụ AWS sau:
- **API Gateway** (REST + WebSocket) - Giao tiếp giữa frontend và backend
- **Lambda** - Xử lý logic nghiệp vụ (23 functions)
- **DynamoDB** - Lưu trữ dữ liệu (Single-table design)
- **Cognito** - Xác thực người dùng
- **Amazon Bedrock** - AI hội thoại (Nova Lite)
- **Amazon Transcribe + Polly** - Xử lý giọng nói
- **S3** - Lưu trữ audio
- **CloudWatch** - Giám sát và logging
- **Amplify** - Triển khai frontend Next.js
- **CloudFront** - CDN cho frontend
- **Route 53** - DNS management (optional)

### Budget & Cost

- **Total Budget**: $200 cho 6 tháng
- **Development (2 months)**: ~$50
- **MVP (1 month)**: ~$50
- **Growth (3 months)**: ~$100

## Content

1. [Overview & Architecture](5.1-Workshop-overview/) - Kiến trúc serverless
2. [Prerequisites & Setup](5.2-Prerequiste/) - Cài đặt công cụ
3. [Backend Deployment](5.3-Backend/) - Lambda, DynamoDB, API Gateway
4. [Frontend Development](5.4-Frontend/) - Next.js + Amplify
5. [AI & Voice Integration](5.5-AI-Voice/) - Bedrock, Transcribe, Polly
6. [CI/CD Pipeline](5.6-CI-CD/) - Tự động hóa triển khai
7. [Monitoring & Optimization](5.7-Monitoring/) - CloudWatch, chi phí
8. [Cleanup & Management](5.8-Clean-up/) - Xóa resources

## Learning Goals

- ✅ Hiểu kiến trúc serverless
- ✅ Triển khai Lambda, DynamoDB, API Gateway, Cognito
- ✅ Tích hợp AI (Bedrock) và xử lý giọng nói
- ✅ Xây dựng CI/CD pipeline
- ✅ Giám sát và tối ưu chi phí
- ✅ Triển khai production-ready application
