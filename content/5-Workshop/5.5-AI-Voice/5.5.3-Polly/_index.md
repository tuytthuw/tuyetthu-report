---
title: "Amazon Polly"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

## Overview

**Amazon Polly** synthesizes speech from text (Text-to-Speech):
- **Voices**: Multiple English voices (Joanna, Matthew, etc.)
- **Output**: MP3, OGG, PCM
- **Speed**: Real-time synthesis

### Synthesize Speech

```bash
# Synthesize speech
aws polly synthesize-speech \
  --text "Hello, how are you?" \
  --output-format mp3 \
  --voice-id Joanna \
  --region <REGION> \
  speech.mp3
```

### List Available Voices

```bash
# List all voices
aws polly describe-voices \
  --region <REGION>

# Or filter by language
aws polly describe-voices \
  --language-code en-US \
  --region <REGION>
```

**Results will include**:
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
            Engine='neural'  # Or 'standard'
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
# Get specific voice information
aws polly describe-voices \
  --voice-ids Joanna \
  --region <REGION>

# Or all voices
aws polly describe-voices \
  --region <REGION> \
  --output table
```

### Monitor Polly Metrics

```bash
# View synthesis requests
aws cloudwatch get-metric-statistics \
  --namespace AWS/Polly \
  --metric-name RequestCount \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/Polly \
  --metric-name UserErrors \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Cost Optimization

**Tips to reduce costs**:
1. Use standard engine instead of neural
2. Cache audio files
3. Use SSML for optimization
4. Limit text length

### SSML Support

```python
# Use SSML for optimization
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

### Troubleshooting

**Issue: Voice not available**

```bash
# Check available voices
aws polly describe-voices \
  --region <REGION>

# Try different voice
aws polly synthesize-speech \
  --text "Hello" \
  --voice-id Matthew \
  --output-format mp3 \
  --region <REGION> \
  speech.mp3
```

**Issue: Text too long**

```bash
# Limit text length (max 3000 characters)
text = "Your text here"[:3000]

aws polly synthesize-speech \
  --text "$text" \
  --voice-id Joanna \
  --output-format mp3 \
  --region <REGION> \
  speech.mp3
```

**Issue: Unsupported format**

```bash
# Supported formats: mp3, ogg, pcm, json
# Try different format
aws polly synthesize-speech \
  --text "Hello" \
  --voice-id Joanna \
  --output-format ogg \
  --region <REGION> \
  speech.ogg
```

## Next Steps

You have completed the AI & Voice section! Continue to [CI/CD Pipeline](../../5.6-CI-CD/) to set up deployment automation.

### Checklist

- [ ] Polly voices available
- [ ] Speech synthesis test successful
- [ ] Audio file created
- [ ] SSML support working
- [ ] Cost optimization applied
