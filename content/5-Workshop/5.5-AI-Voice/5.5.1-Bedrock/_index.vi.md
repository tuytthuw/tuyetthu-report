---
title: "Amazon Bedrock"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Tổng quan

**Amazon Bedrock** cung cấp các foundation models (AI models) để tạo ra phản hồi tự động cho speaking practice:
- **Model**: Claude 3 Haiku (hoặc Nova Lite)
- **Chức năng**: Phân tích giọng nói, cung cấp feedback
- **Pricing**: Tính theo tokens (input + output)

### Kiểm tra Available Models

```bash
# Liệt kê available models
aws bedrock list-foundation-models \
  --region <REGION>

# Hoặc lọc theo model name
aws bedrock list-foundation-models \
  --region <REGION> \
  --query 'modelSummaries[?contains(modelId, `nova`)]'
```

**Kết quả sẽ bao gồm**:
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

# Xem kết quả
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

### Giám sát Bedrock Usage

```bash
# Xem input tokens
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name InputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem output tokens
aws cloudwatch get-metric-statistics \
  --namespace AWS/Bedrock \
  --metric-name OutputTokens \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Tối ưu hóa Chi phí

**Tips để giảm token usage**:
1. Viết prompts ngắn gọn
2. Sử dụng prompt caching
3. Chọn model nhỏ hơn (Nova Lite thay vì Ultra)
4. Giới hạn max_tokens

### Khắc phục sự cố

**Vấn đề: Model không available**

```bash
# Kiểm tra available models
aws bedrock list-foundation-models \
  --region <REGION>

# Thử region khác
aws bedrock list-foundation-models \
  --region us-east-1
```

**Vấn đề: Access Denied**

```bash
# Kiểm tra IAM permissions
aws iam get-role-policy \
  --role-name <LAMBDA_ROLE> \
  --policy-name <POLICY_NAME>
```

**Vấn đề: Timeout**

```bash
# Tăng Lambda timeout
aws lambda update-function-configuration \
  --function-name <FUNCTION_NAME> \
  --timeout 60 \
  --region <REGION>
```

## Bước Tiếp Theo

Tiếp tục đến [Transcribe](../5.5.2-Transcribe/) để học cách chuyển giọng nói thành văn bản.

### Danh sách kiểm tra

- [ ] Bedrock model available
- [ ] IAM permissions cấu hình
- [ ] Bedrock API test thành công
- [ ] Lambda integration hoạt động
- [ ] CloudWatch metrics hiển thị
