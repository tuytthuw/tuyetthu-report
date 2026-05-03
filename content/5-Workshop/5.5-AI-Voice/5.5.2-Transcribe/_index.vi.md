---
title: "Amazon Transcribe"
date: "2026-05-02"
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Tổng quan

**Amazon Transcribe** chuyển đổi audio thành text (Speech-to-Text):
- **Hỗ trợ**: Batch transcription + real-time streaming
- **Ngôn ngữ**: English (US, UK, etc.)
- **Output**: JSON format với timestamps

### Batch Transcription

#### Start Transcription Job

```bash
# Bắt đầu transcription job
aws transcribe start-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --language-code en-US \
  --media-format wav \
  --media '{"MediaFileUri":"s3://<BUCKET>/<KEY>"}' \
  --output-bucket-name <BUCKET> \
  --region <REGION>
```

#### Kiểm tra Job Status

```bash
# Lấy job status
aws transcribe get-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --region <REGION>

# Hoặc liệt kê tất cả jobs
aws transcribe list-transcription-jobs \
  --region <REGION>
```

**Kết quả sẽ bao gồm**:
- TranscriptionJobStatus (QUEUED, IN_PROGRESS, COMPLETED, FAILED)
- Transcript (S3 URI)
- CreationTime, CompletionTime

#### Lấy Transcription Result

```bash
# Download transcript từ S3
aws s3 cp s3://<BUCKET>/<TRANSCRIPT_KEY> transcript.json

# Hoặc xem trực tiếp
aws s3api get-object \
  --bucket <BUCKET> \
  --key <TRANSCRIPT_KEY> \
  transcript.json

cat transcript.json
```

#### Xóa Transcription Job

```bash
# Xóa job
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

### Giám sát Transcribe Metrics

```bash
# Xem transcription jobs
aws cloudwatch get-metric-statistics \
  --namespace AWS/Transcribe \
  --metric-name SuccessfulTranscriptionJobs \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>

# Xem failed jobs
aws cloudwatch get-metric-statistics \
  --namespace AWS/Transcribe \
  --metric-name FailedTranscriptionJobs \
  --start-time 2026-05-01T00:00:00Z \
  --end-time 2026-05-02T00:00:00Z \
  --period 3600 \
  --statistics Sum \
  --region <REGION>
```

### Khắc phục sự cố

**Vấn đề: Job failed**

```bash
# Kiểm tra job status
aws transcribe get-transcription-job \
  --transcription-job-name <JOB_NAME> \
  --region <REGION> \
  --query 'TranscriptionJob.FailureReason'
```

**Vấn đề: Audio file not found**

```bash
# Kiểm tra S3 file
aws s3 ls s3://<BUCKET>/<KEY>

# Hoặc upload file
aws s3 cp <LOCAL_FILE> s3://<BUCKET>/<KEY>
```

**Vấn đề: Unsupported format**

```bash
# Chuyển đổi audio format
ffmpeg -i input.mp3 -acodec pcm_s16le -ar 16000 output.wav

# Hoặc sử dụng format khác
# Supported: mp3, mp4, wav, flac, ogg, amr, webm
```

## Bước Tiếp Theo

Tiếp tục đến [Polly](../5.5.3-Polly/) để học cách tổng hợp giọng nói từ văn bản.

### Danh sách kiểm tra

- [ ] Transcribe job tạo thành công
- [ ] Audio file upload lên S3
- [ ] Transcription job completed
- [ ] Transcript result lấy được
- [ ] Real-time streaming test thành công
