---
title: "Amazon Polly"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

## Tổng quan

**Amazon Polly** tổng hợp giọng nói từ văn bản (Text-to-Speech):
- **Giọng nói**: Nhiều giọng tiếng Anh (Joanna, Matthew, etc.)
- **Output**: MP3, OGG, PCM
- **Tốc độ**: Real-time synthesis

### Synthesize Speech

```bash
# Tổng hợp giọng nói
aws polly synthesize-speech \
  --text "Hello, how are you?" \
  --output-format mp3 \
  --voice-id Joanna \
  --region <REGION> \
  speech.mp3
```

### Liệt kê Available Voices

```bash
# Liệt kê tất cả voices
aws polly describe-voices \
  --region <REGION>

# Hoặc lọc theo ngôn ngữ
aws polly describe-voices \
  --language-code en-US \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- VoiceId (Joanna, Matthew, etc.)
- Name, LanguageCode
- Gender, SupportedEngines

### Polly API Example (Python)

```python
import boto3

class PollyService:
    def __init__(self, region: str = 'ap-southeast-1'):
        self.client = boto3.client('polly', region_name=region)
    
    def synthesize_speech(self, text: str, voice_id: str = 'Joanna') -> bytes:
        """Synthesize speech from text"""
        
        response = self.client.synthesize_speech(
            Text=text,
            OutputFormat='mp3',
            VoiceId=voice_id,
            Engine='neural'  # Hoặc 'standard'
        )
        
        return response['AudioStream'].read()
    
    def save_speech(self, text: str, filename: str, voice_id: str = 'Joanna'):
        """Synthesize and save speech to file"""
        
        audio = self.synthesize_speech(text, voice_id)
        
        with open(filename, 'wb') as f:
            f.write(audio)
```

### Describe Voices

```bash
# Lấy thông tin voice cụ thể
aws polly describe-voices \
  --voice-ids Joanna \
  --region <REGION>

# Hoặc tất cả voices
aws polly describe-voices \
  --region <REGION> \
  --output table
```

### Giám sát Polly Metrics

```bash
# Xem synthesis requests
aws cloudwatch get-metric-statistics \
  --namespace AWS/Polly \
  --metric-name RequestCount \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/Polly \
  --metric-name UserErrors \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Tối ưu hóa Chi phí

**Tips để giảm chi phí**:
1. Sử dụng standard engine thay vì neural
2. Cache audio files
3. Sử dụng SSML để tối ưu hóa
4. Giới hạn text length

### SSML Support

```python
# Sử dụng SSML để tối ưu hóa
ssml_text = """
<speak>
  <prosody rate="0.9">
    Hello, how are you today?
  </prosody>
</speak>
"""

response = client.synthesize_speech(
    Text=ssml_text,
    TextType='ssml',
    OutputFormat='mp3',
    VoiceId='Joanna'
)
```

### Khắc phục sự cố

**Vấn đề: Voice not available**

```bash
# Kiểm tra available voices
aws polly describe-voices \
  --region <REGION>

# Thử voice khác
aws polly synthesize-speech \
  --text "Hello" \
  --voice-id Matthew \
  --output-format mp3 \
  --region <REGION> \
  speech.mp3
```

**Vấn đề: Text too long**

```bash
# Giới hạn text length (max 3000 characters)
text = "Your text here"[:3000]

aws polly synthesize-speech \
  --text "$text" \
  --voice-id Joanna \
  --output-format mp3 \
  --region <REGION> \
  speech.mp3
```

**Vấn đề: Unsupported format**

```bash
# Supported formats: mp3, ogg, pcm, json
# Thử format khác
aws polly synthesize-speech \
  --text "Hello" \
  --voice-id Joanna \
  --output-format ogg \
  --region <REGION> \
  speech.ogg
```

## Bước Tiếp Theo

Bạn đã hoàn thành phần AI & Voice! Tiếp tục đến [CI/CD Pipeline](../../5.6-CI-CD/) để thiết lập tự động hóa triển khai.

### Danh sách kiểm tra

- [ ] Polly voices available
- [ ] Speech synthesis test thành công
- [ ] Audio file tạo được
- [ ] SSML support hoạt động
- [ ] Cost optimization applied
