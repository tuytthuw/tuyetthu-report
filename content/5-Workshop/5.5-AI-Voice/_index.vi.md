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

## Speaking Flow

| Bước | Mô tả |
|------|-------|
| 1 | User speaks → Frontend records → sends audio stream |
| 2 | Lambda receives audio → Transcribe: Audio → Text |
| 3 | Bedrock: Text + Context → AI Response |
| 4 | Comprehend: Language analysis |
| 5 | Polly: Response → Audio |
| 6 | Lambda sends audio to frontend → Frontend plays audio |
| 7 | Save session to DynamoDB |

## AI & Voice Sections

AI & Voice organized in 3 main sections:

1. **[Amazon Bedrock](5.5.1-Bedrock/)** - AI conversation
   - List available models
   - Invoke models
   - Monitor usage
   - Optimize costs

2. **[Amazon Transcribe](5.5.2-Transcribe/)** - Speech-to-Text
   - Batch transcription
   - Real-time streaming
   - Check job status
   - Get transcription result

3. **[Amazon Polly](5.5.3-Polly/)** - Text-to-Speech
   - Synthesize speech
   - List available voices
   - SSML support
   - Optimize costs

## Danh sách kiểm tra

After completing AI & Voice section, you should:

- [ ] Understand complete Speaking Flow
- [ ] Know how to use Amazon Bedrock for AI conversation
- [ ] Understand how Transcribe converts speech to text
- [ ] Know how Polly synthesizes speech from text
- [ ] Understand how to integrate 3 services with Lambda
- [ ] Understand costs and optimization

## Next Steps

You've completed AI & Voice! Continue to [CI/CD Pipeline](../5.6-CI-CD/) to setup deployment automation.
