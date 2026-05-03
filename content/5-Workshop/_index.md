---
title: "Workshop"
date: "2026-05-02"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Lexi - AI Tutor Platform: Serverless Workshop

## Overview

**Lexi** is an AI-powered English learning platform built on a completely **AWS Serverless** architecture. This workshop guides the deployment, operation, and scaling of the system from development to production.

### Architecture

Lexi uses the following AWS services:
- **API Gateway** (REST + WebSocket) - Communication between frontend and backend
- **Lambda** - Business logic processing (23 functions)
- **DynamoDB** - Data storage (Single-table design)
- **Cognito** - User authentication
- **Amazon Bedrock** - AI conversation (Nova Lite)
- **Amazon Transcribe + Polly** - Voice processing
- **S3** - Audio storage
- **CloudWatch** - Monitoring and logging
- **Amplify** - Next.js frontend deployment
- **CloudFront** - Frontend CDN
- **Route 53** - DNS management (optional)

### Budget & Cost

- **Total Budget**: $200 for 6 months
- **Development (2 months)**: ~$50
- **MVP (1 month)**: ~$50
- **Growth (3 months)**: ~$100

## Content

1. [Overview & Architecture](5.1-Workshop-overview/) - Serverless architecture
2. [Prerequisites & Setup](5.2-Prerequiste/) - Tool installation
3. [Backend Deployment](5.3-Backend/) - Lambda, DynamoDB, API Gateway
4. [Frontend Development](5.4-Frontend/) - Next.js + Amplify
5. [AI & Voice Integration](5.5-AI-Voice/) - Bedrock, Transcribe, Polly
6. [CI/CD Pipeline](5.6-CI-CD/) - Automated deployment
7. [Monitoring & Optimization](5.7-Monitoring/) - CloudWatch, cost optimization
8. [Cleanup & Management](5.8-Clean-up/) - Resource cleanup

## Learning Goals

- ✅ Understand serverless architecture
- ✅ Deploy Lambda, DynamoDB, API Gateway, Cognito
- ✅ Integrate AI (Bedrock) and voice processing
- ✅ Build CI/CD pipeline
- ✅ Monitor and optimize costs
- ✅ Deploy production-ready application
