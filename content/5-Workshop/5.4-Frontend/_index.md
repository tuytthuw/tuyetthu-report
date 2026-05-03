---
title: "Frontend Development: Amplify, Route 53, CloudFront, WAF"
date: "2025-12-07"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

# Frontend Development: Amplify, Route 53, CloudFront, WAF

## Overview

In this section, you will build and deploy the frontend application for the Serverless Student Management System using AWS Amplify, with Route 53 for DNS, CloudFront for CDN, and WAF for security.

## Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Users (Browser)                       │
└────────────────────────┬─────────────────────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │   Route 53 (DNS)               │
        │   everyonecook.cloud           │
        └────────────────┬────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │   CloudFront (CDN)             │
        │   - Caching                    │
        │   - Edge Locations             │
        └────────────────┬────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │   WAF (Web Application         │
        │   Firewall)                    │
        │   - Rate limiting              │
        │   - IP filtering               │
        └────────────────┬────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │   AWS Amplify                  │
        │   - React/Next.js App          │
        │   - Static hosting             │
        │   - CI/CD integration          │
        └────────────────────────────────┘
```

## Step 1: Create React Application

### Initialize React Project

```bash
# Create React app with TypeScript
npx create-react-app student-management-frontend --template typescript

# Navigate to project
cd student-management-frontend

# Install dependencies
npm install
```

### Install Required Libraries

```bash
# Install Amplify
npm install aws-amplify @aws-amplify/ui-react

# Install UI components
npm install flowbite-react

# Install HTTP client
npm install axios

# Install state management
npm install zustand
```

## Step 2: Configure Amplify

### Initialize Amplify

```bash
# Install Amplify CLI
npm install -g @aws-amplify/cli

# Initialize Amplify project
amplify init

# Follow prompts:
# - Project name: student-management-frontend
# - Environment: dev
# - Editor: Visual Studio Code
# - App type: javascript
# - Framework: react
# - Source directory: src
# - Distribution directory: build
# - Build command: npm run build
# - Start command: npm start
```

### Configure Authentication

```bash
# Add Cognito authentication
amplify add auth

# Follow prompts:
# - Do you want to use the default authentication and security configuration? Yes
# - How do you want users to be able to sign in? Username
# - Do you want to configure advanced settings? No
```

### Configure API

```bash
# Add API
amplify add api

# Follow prompts:
# - Select from one of the below mentioned services: REST
# - Provide a friendly name: StudentManagementAPI
# - Provide a path: /api
# - Select a Lambda source: Create a new Lambda function
# - Function name: studentManagementFunction
```

## Step 3: Build Frontend Components

### Create Authentication Component

```typescript
// src/components/Auth.tsx
import React, { useState } from 'react';
import { Amplify, Auth } from 'aws-amplify';
import { withAuthenticator } from '@aws-amplify/ui-react';
import '@aws-amplify/ui-react/styles.css';

interface AuthProps {
  user: any;
  signOut: () => void;
}

const AuthComponent: React.FC<AuthProps> = ({ user, signOut }) => {
  return (
    <div className="p-4">
      <h1>Welcome, {user.username}!</h1>
      <button
        onClick={signOut}
        className="px-4 py-2 bg-red-500 text-white rounded"
      >
        Sign Out
      </button>
    </div>
  );
};

export default withAuthenticator(AuthComponent);
```

### Create Student List Component

```typescript
// src/components/StudentList.tsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

interface Student {
  id: string;
  name: string;
  email: string;
  grade: string;
}

const StudentList: React.FC = () => {
  const [students, setStudents] = useState<Student[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchStudents();
  }, []);

  const fetchStudents = async () => {
    try {
      const response = await axios.get('/api/students');
      setStudents(response.data);
    } catch (error) {
      console.error('Error fetching students:', error);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <div>Loading...</div>;

  return (
    <div className="p-4">
      <h2 className="text-2xl font-bold mb-4">Students</h2>
      <table className="w-full border-collapse border border-gray-300">
        <thead>
          <tr className="bg-gray-100">
            <th className="border border-gray-300 p-2">Name</th>
            <th className="border border-gray-300 p-2">Email</th>
            <th className="border border-gray-300 p-2">Grade</th>
          </tr>
        </thead>
        <tbody>
          {students.map((student) => (
            <tr key={student.id}>
              <td className="border border-gray-300 p-2">{student.name}</td>
              <td className="border border-gray-300 p-2">{student.email}</td>
              <td className="border border-gray-300 p-2">{student.grade}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};

export default StudentList;
```

## Step 4: Deploy to Amplify

### Push to Amplify

```bash
# Push changes to Amplify
amplify push

# Follow prompts to confirm deployment
```

### Configure Custom Domain

```bash
# Add custom domain
amplify add hosting

# Follow prompts:
# - Select the environment setup: Prod (S3 with CloudFront)
# - Hosting bucket name: student-management-frontend-hosting
# - Index doc for the website: index.html
# - Error doc for the website: index.html
```

### Connect Domain

```bash
# Update Route 53 records
# 1. Go to Route 53 console
# 2. Create A record pointing to CloudFront distribution
# 3. Or use Amplify's custom domain feature
```

## Step 5: Configure CloudFront and WAF

### CloudFront Configuration

```typescript
// lib/stacks/frontend-stack.ts
import * as cdk from 'aws-cdk-lib';
import * as cloudfront from 'aws-cdk-lib/aws-cloudfront';
import * as s3 from 'aws-cdk-lib/aws-s3';

export class FrontendStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Create S3 bucket for frontend
    const bucket = new s3.Bucket(this, 'FrontendBucket', {
      blockPublicAccess: s3.BlockPublicAccess.BLOCK_ALL,
      removalPolicy: cdk.RemovalPolicy.DESTROY,
    });

    // Create CloudFront distribution
    const distribution = new cloudfront.Distribution(this, 'FrontendDistribution', {
      defaultBehavior: {
        origin: new cloudfront.S3Origin(bucket),
        viewerProtocolPolicy: cloudfront.ViewerProtocolPolicy.REDIRECT_TO_HTTPS,
        cachePolicy: cloudfront.CachePolicy.CACHING_OPTIMIZED,
      },
      defaultRootObject: 'index.html',
      errorResponses: [
        {
          httpStatus: 404,
          responseHttpStatus: 200,
          responsePagePath: '/index.html',
        },
      ],
    });
  }
}
```

### WAF Configuration

```typescript
// lib/stacks/waf-stack.ts
import * as cdk from 'aws-cdk-lib';
import * as wafv2 from 'aws-cdk-lib/aws-wafv2';

export class WafStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Create WAF Web ACL
    const webAcl = new wafv2.CfnWebACL(this, 'FrontendWAF', {
      scope: 'CLOUDFRONT',
      defaultAction: { allow: {} },
      visibilityConfig: {
        sampledRequestsEnabled: true,
        cloudWatchMetricsEnabled: true,
        metricName: 'FrontendWAF',
      },
      rules: [
        {
          name: 'RateLimitRule',
          priority: 0,
          action: { block: {} },
          statement: {
            rateBasedStatement: {
              limit: 2000,
              aggregateKeyType: 'IP',
            },
          },
          visibilityConfig: {
            sampledRequestsEnabled: true,
            cloudWatchMetricsEnabled: true,
            metricName: 'RateLimitRule',
          },
        },
      ],
    });
  }
}
```

## Step 6: Test Frontend

```bash
# Start development server
npm start

# Build for production
npm run build

# Test production build locally
npm install -g serve
serve -s build
```

## Deployment Checklist

- [ ] React app created and configured
- [ ] Amplify initialized and connected
- [ ] Authentication configured with Cognito
- [ ] API integration working
- [ ] Components built and tested
- [ ] CloudFront distribution created
- [ ] WAF rules configured
- [ ] Custom domain configured
- [ ] SSL/TLS certificate installed
- [ ] Frontend deployed to Amplify

## Troubleshooting

**Issue: Amplify deployment failed**
```bash
# Check Amplify logs
amplify console

# Rebuild and redeploy
amplify push --force
```

**Issue: CORS errors**
```bash
# Add CORS headers to API Gateway
# In backend-stack.ts, add:
api.addCorsPreflightOptions({
  allowOrigins: apigateway.Cors.ALL_ORIGINS,
  allowMethods: apigateway.Cors.ALL_METHODS,
});
```

**Issue: CloudFront caching issues**
```bash
# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id DISTRIBUTION_ID --paths "/*"
```

## Next Steps

Once the frontend is deployed, proceed to [CI/CD Pipeline](../5.5-CI-CD/) to set up automated deployment.
