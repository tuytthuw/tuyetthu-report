---
title: "Backend Deployment: DynamoDB, Lambda, API Gateway, Cognito"
date: "2025-12-07"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Backend Deployment: DynamoDB, Lambda, API Gateway, Cognito

## Overview

In this section, you will deploy the backend infrastructure for the Serverless Student Management System. This includes:

- **DynamoDB**: NoSQL database for storing student data
- **Lambda**: Serverless functions for business logic
- **API Gateway**: REST API for frontend communication
- **Cognito**: User authentication and authorization

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Frontend (React/Amplify)                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│                    API Gateway (REST)                       │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  /students (GET, POST, PUT, DELETE)                 │   │
│  │  /students/{id} (GET, PUT, DELETE)                  │   │
│  │  /auth (POST) - Login/Register                      │   │
│  └──────────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
   ┌─────────┐      ┌─────────┐      ┌─────────┐
   │ Lambda  │      │ Lambda  │      │ Lambda  │
   │ CRUD    │      │ Auth    │      │ Admin   │
   └────┬────┘      └────┬────┘      └────┬────┘
        │                │                │
        └────────────────┼────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │      DynamoDB Table            │
        │  ┌──────────────────────────┐  │
        │  │ PK: studentId            │  │
        │  │ SK: timestamp            │  │
        │  │ Attributes: name, email, │  │
        │  │            grade, etc.   │  │
        │  └──────────────────────────┘  │
        └────────────────────────────────┘
```

## Step 1: CDK Bootstrap

Before deploying CDK stacks, bootstrap your AWS account:

```bash
# Bootstrap CDK
cdk bootstrap aws://ACCOUNT_ID/REGION

# Example:
cdk bootstrap aws://123456789012/us-east-1
```

## Step 2: Configure DynamoDB

### Create DynamoDB Table

```typescript
// lib/stacks/database-stack.ts
import * as cdk from 'aws-cdk-lib';
import * as dynamodb from 'aws-cdk-lib/aws-dynamodb';

export class DatabaseStack extends cdk.Stack {
  public readonly table: dynamodb.Table;

  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    this.table = new dynamodb.Table(this, 'StudentTable', {
      partitionKey: { name: 'studentId', type: dynamodb.AttributeType.STRING },
      sortKey: { name: 'timestamp', type: dynamodb.AttributeType.NUMBER },
      billingMode: dynamodb.BillingMode.PAY_PER_REQUEST,
      removalPolicy: cdk.RemovalPolicy.DESTROY,
    });

    // Add GSI for querying by email
    this.table.addGlobalSecondaryIndex({
      indexName: 'emailIndex',
      partitionKey: { name: 'email', type: dynamodb.AttributeType.STRING },
      projectionType: dynamodb.ProjectionType.ALL,
    });
  }
}
```

## Step 3: Configure Cognito

### Create Cognito User Pool

```typescript
// lib/stacks/auth-stack.ts
import * as cdk from 'aws-cdk-lib';
import * as cognito from 'aws-cdk-lib/aws-cognito';

export class AuthStack extends cdk.Stack {
  public readonly userPool: cognito.UserPool;
  public readonly userPoolClient: cognito.UserPoolClient;

  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    this.userPool = new cognito.UserPool(this, 'StudentUserPool', {
      userPoolName: 'student-management-pool',
      selfSignUpEnabled: true,
      autoVerifiedAttributes: [cognito.UserPoolAttribute.EMAIL],
      passwordPolicy: {
        minLength: 8,
        requireLowercase: true,
        requireUppercase: true,
        requireDigits: true,
        requireSymbols: false,
      },
    });

    this.userPoolClient = this.userPool.addClient('StudentAppClient', {
      clientName: 'student-app-client',
      generateSecret: false,
      authFlows: {
        userPassword: true,
        userSrp: true,
      },
    });
  }
}
```

## Step 4: Configure Lambda Functions

### Create Lambda Functions

```typescript
// lib/stacks/backend-stack.ts
import * as cdk from 'aws-cdk-lib';
import * as lambda from 'aws-cdk-lib/aws-lambda';
import * as apigateway from 'aws-cdk-lib/aws-apigateway';

export class BackendStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // Create Lambda function for CRUD operations
    const crudFunction = new lambda.Function(this, 'CrudFunction', {
      runtime: lambda.Runtime.NODEJS_20_X,
      handler: 'index.handler',
      code: lambda.Code.fromAsset('lambda/crud'),
      environment: {
        TABLE_NAME: 'StudentTable',
      },
    });

    // Create API Gateway
    const api = new apigateway.RestApi(this, 'StudentApi', {
      restApiName: 'Student Management API',
    });

    // Add resources and methods
    const students = api.root.addResource('students');
    students.addMethod('GET', new apigateway.LambdaIntegration(crudFunction));
    students.addMethod('POST', new apigateway.LambdaIntegration(crudFunction));

    const student = students.addResource('{id}');
    student.addMethod('GET', new apigateway.LambdaIntegration(crudFunction));
    student.addMethod('PUT', new apigateway.LambdaIntegration(crudFunction));
    student.addMethod('DELETE', new apigateway.LambdaIntegration(crudFunction));
  }
}
```

## Step 5: Deploy Backend

```bash
# Deploy all stacks
cdk deploy

# Or deploy specific stack
cdk deploy DatabaseStack
cdk deploy AuthStack
cdk deploy BackendStack
```

## Step 6: Verify Deployment

```bash
# Check deployed stacks
aws cloudformation list-stacks --region us-east-1

# Check DynamoDB table
aws dynamodb describe-table --table-name StudentTable --region us-east-1

# Check Lambda functions
aws lambda list-functions --region us-east-1

# Check API Gateway
aws apigateway get-rest-apis --region us-east-1
```

## Lambda Function Structure

### CRUD Operations

```typescript
// lambda/crud/index.ts
import { DynamoDBClient } from '@aws-sdk/client-dynamodb';
import { DynamoDBDocumentClient, GetCommand, PutCommand, QueryCommand, UpdateCommand, DeleteCommand } from '@aws-sdk/lib-dynamodb';

const client = new DynamoDBClient({});
const docClient = DynamoDBDocumentClient.from(client);

export const handler = async (event: any) => {
  const method = event.httpMethod;
  const path = event.path;

  try {
    if (method === 'GET' && path === '/students') {
      return await getAllStudents();
    } else if (method === 'POST' && path === '/students') {
      return await createStudent(JSON.parse(event.body));
    } else if (method === 'GET' && path.includes('/students/')) {
      const id = path.split('/')[2];
      return await getStudent(id);
    } else if (method === 'PUT' && path.includes('/students/')) {
      const id = path.split('/')[2];
      return await updateStudent(id, JSON.parse(event.body));
    } else if (method === 'DELETE' && path.includes('/students/')) {
      const id = path.split('/')[2];
      return await deleteStudent(id);
    }
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: 'Internal server error' }),
    };
  }
};

async function getAllStudents() {
  const result = await docClient.send(new QueryCommand({
    TableName: process.env.TABLE_NAME,
    KeyConditionExpression: 'studentId = :id',
    ExpressionAttributeValues: {
      ':id': 'STUDENT',
    },
  }));

  return {
    statusCode: 200,
    body: JSON.stringify(result.Items),
  };
}

async function createStudent(data: any) {
  const studentId = `STUDENT#${Date.now()}`;
  await docClient.send(new PutCommand({
    TableName: process.env.TABLE_NAME,
    Item: {
      studentId,
      timestamp: Date.now(),
      ...data,
    },
  }));

  return {
    statusCode: 201,
    body: JSON.stringify({ id: studentId }),
  };
}

// Implement other CRUD operations...
```

## Troubleshooting

**Issue: CDK bootstrap failed**
```bash
# Check AWS credentials
aws sts get-caller-identity

# Try bootstrap again with explicit credentials
cdk bootstrap aws://ACCOUNT_ID/REGION --profile your-profile
```

**Issue: Lambda function not found**
```bash
# Check Lambda function code path
ls -la lambda/crud/

# Ensure index.ts is compiled to index.js
npm run build
```

**Issue: DynamoDB table not created**
```bash
# Check CloudFormation stack status
aws cloudformation describe-stacks --stack-name DatabaseStack

# Check stack events for errors
aws cloudformation describe-stack-events --stack-name DatabaseStack
```

## Next Steps

Once the backend is deployed, proceed to [Frontend Development](../5.4-Frontend/) to build and deploy the frontend application.
