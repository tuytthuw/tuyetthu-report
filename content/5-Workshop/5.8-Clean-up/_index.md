---
title: "Clean-up & Resource Management"
date: "2026-05-02"
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Delete CloudFormation Stacks

Delete stacks in reverse order (delete main stack first, then auth lambdas, auth base, finally database):

Delete main stack:
```bash
aws cloudformation delete-stack \
  --stack-name <MAIN_STACK_NAME> \
  --region <REGION>
```

Delete auth lambdas stack:
```bash
aws cloudformation delete-stack \
  --stack-name <AUTH_LAMBDAS_STACK_NAME> \
  --region <REGION>
```

Delete auth base stack:
```bash
aws cloudformation delete-stack \
  --stack-name <AUTH_BASE_STACK_NAME> \
  --region <REGION>
```

Delete database stack:
```bash
aws cloudformation delete-stack \
  --stack-name <DATABASE_STACK_NAME> \
  --region <REGION>
```

Check deletion status:
```bash
aws cloudformation list-stacks \
  --region <REGION> \
  --query 'StackSummaries[?StackStatus==`DELETE_IN_PROGRESS`]'
```

**Note**: Deleting stacks will delete all resources created by CloudFormation (Lambda, DynamoDB, API Gateway, etc.)

## Delete S3 Buckets

Empty bucket (delete all objects):
```bash
aws s3 rm s3://<BUCKET_NAME> --recursive
```

Delete bucket:
```bash
aws s3 rb s3://<BUCKET_NAME>
```

Or delete all Lexi buckets:
```bash
aws s3 ls | grep lexi | awk '{print $3}' | xargs -I {} aws s3 rb s3://{} --force
```

**Note**: S3 bucket must be empty before deletion.

## Delete Amplify App

Delete Amplify app (from project directory):
```bash
amplify delete
```

Or via AWS CLI:
```bash
aws amplify delete-app \
  --app-id <APP_ID> \
  --region <REGION>
```

## Delete CloudWatch Logs

Delete specific log group:
```bash
aws logs delete-log-group \
  --log-group-name <LOG_GROUP_NAME> \
  --region <REGION>
```

Or delete all Lexi log groups:
```bash
aws logs describe-log-groups \
  --log-group-name-prefix /aws/lambda/lexi-be \
  --region <REGION> \
  --query 'logGroups[].logGroupName' \
  --output text | xargs -I {} aws logs delete-log-group --log-group-name {} --region <REGION>
```

## Delete IAM Roles

List roles:
```bash
aws iam list-roles \
  --query 'Roles[?contains(RoleName, `lexi`)].RoleName'
```

Delete role (must delete inline policies first):
```bash
aws iam delete-role --role-name <ROLE_NAME>
```

## Check Remaining Resources

After deletion, verify no resources remain:

Check Lambda functions:
```bash
aws lambda list-functions \
  --region <REGION> \
  --query 'Functions[?contains(FunctionName, `lexi`)].FunctionName'
```

Check DynamoDB tables:
```bash
aws dynamodb list-tables --region <REGION>
```

Check S3 buckets:
```bash
aws s3 ls | grep lexi
```

Check Cognito user pools:
```bash
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION>
```

Check API Gateway:
```bash
aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[?contains(name, `lexi`)].name'
```

## Check Costs

View current costs:
```bash
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-02 \
  --granularity DAILY \
  --metrics "UnblendedCost" \
  --group-by Type=DIMENSION,Key=SERVICE
```

View monthly costs:
```bash
aws ce get-cost-and-usage \
  --time-period Start=2026-05-01,End=2026-05-31 \
  --granularity MONTHLY \
  --metrics "UnblendedCost"
```

## Clean-up Checklist

After completing clean-up, you should have:

- [ ] Deleted all CloudFormation stacks
- [ ] Deleted all S3 buckets
- [ ] Deleted Lambda functions (if any remain)
- [ ] Deleted DynamoDB tables (if any remain)
- [ ] Deleted Cognito user pools (if any remain)
- [ ] Deleted API Gateway (if any remain)
- [ ] Deleted CloudWatch logs
- [ ] Deleted unnecessary IAM roles
- [ ] Deleted Amplify app
- [ ] Verified final costs

## Workshop Summary

You have completed the Lexi workshop! You learned:

✅ Lexi's serverless architecture
✅ Deploy Lambda, DynamoDB, API Gateway
✅ Integrate Cognito, Bedrock, Transcribe, Polly
✅ Build WebSocket real-time communication
✅ Deploy frontend with Next.js + Amplify
✅ Setup CI/CD pipeline
✅ Monitor and optimize costs
✅ Manage AWS resources

## Next Steps

- Optimize performance
- Add new features
- Scale up
- Integrate additional AWS services

Congratulations on completing the workshop!
