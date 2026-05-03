---
title: "Amazon Bedrock"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Overview

**Amazon Bedrock** provides foundation models (AI models) to generate automatic responses for speaking practice:
- **Model**: Claude 3 Haiku (or Nova Lite)
- **Function**: Analyze speech, provide feedback
- **Pricing**: Calculated by tokens (input + output)

### Check Available Models

```bash
# List available models
aws bedrock list-foundation-models \
  --region <REGION>

# Or filter by model name
aws bedrock list-foundation-models \
  --region <REGION> \
  --query 'modelSummaries[?contains(modelId, `nova`)]'
```

**Results will include**:
- us.amazon.nova-lite-v1:0 (recommended)
- us.amazon.nova-pro-v1:0
- us.amazon.nova-ultra-v1:0
- anthropic.claude-3-haiku-20240307-v1:0

### Invoke Bedrock Model

```bash
# Invoke model
aws bedrock-runtime invoke-model \
  --model-id <MODEL_ID> \
  --region <REGION> \
  --body '{"prompt":"Hello","max_tokens":100}' \
  response.json

# View results
cat response.json
```

### Bedrock API Example (Python)

```python
import boto3
import json

class BedrockService:
    def __init__(self, region: str = 'us-east-1'):
        self.client = boto3.client('bedrock-runtime', region_name=region)
        self.model_id = 'us.amazon.nova-lite-v1:0'
    
    def generate_response(self, user_text: str, context: str) -> str:
        """Generate AI response using Bedrock"""
        
        prompt = f"""You are an English tutor. Provide feedback on the user's English.

Context: {context}
User said: {user_text}

Provide:
1. Grammar feedback
2. Pronunciation tips
3. Vocabulary suggestions
4. Encouragement

Keep response concise and helpful."""
        
        response = self.client.invoke_model(
            modelId=self.model_id,
            contentType='application/json',
            accept='application/json',
            body=json.dumps({
                'prompt': prompt,
                'max_tokens': 500,
                'temperature': 0.7
            })
        )
        
        result = json.loads(response['body'].read())
        return result['content'][0]['text']
```

### Monitor Bedrock Usage

```bash
# View input tokens
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name InputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View output tokens
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name OutputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Cost Optimization

**Tips to reduce token usage**:
1. Write concise prompts
2. Use prompt caching
3. Choose smaller model (Nova Lite instead of Ultra)
4. Limit max_tokens

### Troubleshooting

**Issue: Model not available**

```bash
# Check available models
aws bedrock list-foundation-models \
  --region <REGION>

# Try different region
aws bedrock list-foundation-models \
  --region us-east-1
```

**Issue: Access Denied**

```bash
# Check IAM permissions
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Issue: Timeout**

```bash
# Increase Lambda timeout
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

## Next Steps

Continue to [Transcribe](../5.5.2-Transcribe/) to learn how to convert speech to text.

### Checklist

- [ ] Bedrock model available
- [ ] IAM permissions configured
- [ ] Bedrock API test successful
- [ ] Lambda integration working
- [ ] CloudWatch metrics displayed
