---
title: "AI & Voice Integration"
date: "2026-05-02"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Overview

Lexi uses three main AWS services for AI and voice processing:

1. **Amazon Bedrock** - AI conversation (Claude 3 Haiku or Nova Lite)
2. **Amazon Transcribe** - Speech-to-Text (STT)
3. **Amazon Polly** - Text-to-Speech (TTS)

## Speaking Flow Process

```
User speaks
    ↓
Frontend records → sends audio stream
    ↓
Lambda receives audio
    ↓
Transcribe: Audio → Text
    ↓
Bedrock: Text + Context → AI Response
    ↓
Comprehend: Language analysis
    ↓
Polly: Response → Audio
    ↓
Lambda sends audio to frontend
    ↓
Frontend plays audio
    ↓
Save session to DynamoDB
```

## AI & Voice Content

AI & Voice is divided into 3 main parts:

1. **[Amazon Bedrock](5.5.1-Bedrock/)** - AI conversation
   - List available models
   - Invoke models
   - Monitor usage
   - Cost optimization

2. **[Amazon Transcribe](5.5.2-Transcribe/)** - Speech-to-Text
   - Batch transcription
   - Real-time streaming
   - Check job status
   - Get transcription result

3. **[Amazon Polly](5.5.3-Polly/)** - Text-to-Speech
   - Synthesize speech
   - List available voices
   - SSML support
   - Cost optimization

## Checklist

After completing the AI & Voice section, you should:

- [ ] Understand the complete Speaking Flow process
- [ ] Know how to use Amazon Bedrock for AI conversation
- [ ] Understand how Transcribe converts speech to text
- [ ] Know how Polly synthesizes speech from text
- [ ] Understand how to integrate these 3 services with Lambda
- [ ] Understand costs and optimization strategies

> **📸 TODO**: Add detailed Speaking Flow diagram

> **📸 TODO**: Add screenshot of Bedrock model invocation

## Next Steps

You've completed the AI & Voice section! Continue to [CI/CD Pipeline](../5.6-CI-CD/) to set up automated deployment.
