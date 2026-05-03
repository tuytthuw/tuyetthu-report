---
title: "Amazon Transcribe"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Overview

**Amazon Transcribe** converts audio to text (Speech-to-Text):
- **Support**: Batch transcription + real-time streaming
- **Language**: English (US, UK, etc.)
- **Output**: JSON format with timestamps

### Batch Transcription

#### Start Transcription Job

```bash
# Start transcription job
aws transcribe start-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --language-code en-US \
  --media-format wav \
  --media '{"MediaFileUri":"s3://<BUCKET>/<KEY>"}' \
  --output-bucket-name <BUCKET> \
  --region <REGION>
```

#### Check Job Status

```bash
# Get job status
aws transcribe get-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --region <REGION>

# Or list all jobs
aws transcribe list-transcription-jobs \
  --region <REGION>
```

**Results will include**:
- TranscriptionJobStatus (QUEUED, IN_PROGRESS, COMPLETED, FAILED)
- Transcript (S3 URI)
- CreationTime, CompletionTime

#### Get Transcription Result

```bash
# Download transcript from S3
aws s3 cp s3://<BUCKET>/<TRANSCRIPT_KEY> transcript.json

# Or view directly
aws s3api get-object \
  --bucket <BUCKET> \
  --key <TRANSCRIPT_KEY> \
  transcript.json

cat transcript.json
```

#### Delete Transcription Job

```bash
# Delete job
aws transcribe delete-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --region <REGION>
```

### Real-time Streaming Transcription

```python
import boto3
import json

class TranscribeStreamingService:
    def __init__(self, region: str = 'ap-southeast-1'):
        self.client = boto3.client('transcribe-streaming', region_name=region)
    
    def transcribe_stream(self, audio_stream):
        """Transcribe audio stream in real-time"""
        
        response = self.client.start_stream_transcription(
            language_code='en-US',
            media_sample_rate_hz=16000,
            media_encoding='pcm',
            AudioStream=audio_stream
        )
        
        for event in response['TranscriptResultStream']:
            if 'TranscriptEvent' in event:
                result = event['TranscriptEvent']['Transcript']
                for item in result['Results']:
                    if item['IsPartial']:
                        print(f"Partial: {item['Alternatives'][0]['Transcript']}")
                    else:
                        print(f"Final: {item['Alternatives'][0]['Transcript']}")
```

### Transcribe API Example (Python)

```python
import boto3
import uuid

class TranscribeService:
    def __init__(self, region: str = 'ap-southeast-1'):
        self.client = boto3.client('transcribe', region_name=region)
        self.bucket_name = 'lexi-be-speakingaudiobucket'
    
    def transcribe_audio(self, audio_url: str) -> str:
        """Transcribe audio file to text"""
        
        job_name = f"lexi-transcribe-{uuid.uuid4()}"
        
        response = self.client.start_transcription_job(
            TranscriptionJobName=job_name,
            Media={'MediaFileUri': audio_url},
            MediaFormat='wav',
            LanguageCode='en-US',
            OutputBucketName=self.bucket_name,
            OutputKey=f'transcripts/{job_name}.json'
        )
        
        return response['TranscriptionJob']['TranscriptionJobName']
    
    def get_transcription(self, job_name: str) -> str:
        """Get transcription result"""
        
        response = self.client.get_transcription_job(
            TranscriptionJobName=job_name
        )
        
        job = response['TranscriptionJob']
        
        if job['TranscriptionJobStatus'] == 'COMPLETED':
            transcript_uri = job['Transcript']['TranscriptFileUri']
            return transcript_uri
        
        return None
```

### Monitor Transcribe Metrics

```bash
# View transcription jobs
aws cloudwatch get-metric-statistics \
  --namespace AWS/Transcribe \
  --metric-name SuccessfulTranscriptionJobs \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# View failed jobs
aws cloudwatch get-metric-statistics \
  --namespace AWS/Transcribe \
  --metric-name FailedTranscriptionJobs \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Troubleshooting

**Issue: Job failed**

```bash
# Check job status
aws transcribe get-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --region <REGION> \
  --query 'TranscriptionJob.FailureReason'
```

**Issue: Audio file not found**

```bash
# Check S3 file
aws s3 ls s3://<BUCKET>/<KEY>

# Or upload file
aws s3 cp <LOCAL_FILE> s3://<BUCKET>/<KEY>
```

**Issue: Unsupported format**

```bash
# Convert audio format
ffmpeg -i input.mp3 -acodec pcm_s16le -ar 16000 output.wav

# Or use different format
# Supported: mp3, mp4, wav, flac, ogg, amr, webm
```

## Next Steps

Continue to [Polly](../5.5.3-Polly/) to learn how to synthesize speech from text.

### Checklist

- [ ] Transcribe job created successfully
- [ ] Audio file uploaded to S3
- [ ] Transcription job completed
- [ ] Transcript result retrieved
- [ ] Real-time streaming test successful
