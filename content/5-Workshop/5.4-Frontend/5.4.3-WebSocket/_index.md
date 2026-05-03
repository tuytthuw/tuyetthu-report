---
title: "WebSocket Integration"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Overview

WebSocket enables **real-time** communication between frontend and backend for **Speaking Practice**. This section guides you through integrating WebSocket into Next.js.

## WebSocket Flow

```
User speaks
    ↓
Frontend records → sends audio chunks via WebSocket
    ↓
Lambda receives audio → Transcribe (STT)
    ↓
Lambda sends text → Bedrock (AI response)
    ↓
Lambda receives AI response → Polly (TTS)
    ↓
Lambda sends audio response to frontend
    ↓
Frontend plays audio for user
```

## Step 1: Create WebSocket Client

Create `lib/websocket.ts`:

```typescript
export class WebSocketClient {
  private ws: WebSocket | null = null;
  private url: string;
  
  public onOpen: (() => void) | null = null;
  public onMessage: ((data: any) => void) | null = null;
  public onClose: (() => void) | null = null;
  public onError: ((error: Event) => void) | null = null;

  constructor(url: string) {
    this.url = url;
  }

  connect(token?: string) {
    const wsUrl = token ? `${this.url}?token=${token}` : this.url;
    this.ws = new WebSocket(wsUrl);

    this.ws.onopen = () => {
      console.log('WebSocket connected');
      this.onOpen?.();
    };

    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.onMessage?.(data);
    };

    this.ws.onclose = () => {
      console.log('WebSocket disconnected');
      this.onClose?.();
    };

    this.ws.onerror = (error) => {
      console.error('WebSocket error:', error);
      this.onError?.(error);
    };
  }

  send(data: any) {
    if (this.ws?.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify(data));
    }
  }

  close() {
    this.ws?.close();
  }
}
```

## Step 2: Create Audio Recorder Hook

Create `hooks/useAudioRecorder.ts`:

```typescript
import { useState, useRef, useCallback } from 'react';

export const useAudioRecorder = () => {
  const [isRecording, setIsRecording] = useState(false);
  const mediaRecorderRef = useRef<MediaRecorder | null>(null);

  const startRecording = useCallback(async () => {
    const stream = await navigator.mediaDevices.getUserMedia({ 
      audio: { echoCancellation: true, noiseSuppression: true } 
    });
    
    const mediaRecorder = new MediaRecorder(stream);
    mediaRecorderRef.current = mediaRecorder;
    mediaRecorder.start(100);
    setIsRecording(true);
  }, []);

  const stopRecording = useCallback((): Promise<Blob> => {
    return new Promise((resolve) => {
      if (!mediaRecorderRef.current) return resolve(new Blob());
      
      mediaRecorderRef.current.onstop = () => {
        const audioBlob = new Blob([], { type: 'audio/webm' });
        setIsRecording(false);
        resolve(audioBlob);
      };
      
      mediaRecorderRef.current.stop();
    });
  }, []);

  return { isRecording, startRecording, stopRecording };
};
```

## Step 3: Create Speaking Component

Create `app/(app)/speaking/page.tsx`:

```typescript
'use client';

import { useEffect, useRef, useState } from 'react';
import { WebSocketClient } from '@/lib/websocket';
import { useAudioRecorder } from '@/hooks/useAudioRecorder';

export default function SpeakingPage() {
  const [isConnected, setIsConnected] = useState(false);
  const wsRef = useRef<WebSocketClient | null>(null);
  const { isRecording, startRecording, stopRecording } = useAudioRecorder();

  useEffect(() => {
    const ws = new WebSocketClient(process.env.NEXT_PUBLIC_WEBSOCKET_URL || '');
    ws.onOpen = () => setIsConnected(true);
    ws.connect();
    wsRef.current = ws;
    
    return () => ws.close();
  }, []);

  return (
    <div className="container mx-auto p-6">
      <h1 className="text-3xl font-bold mb-6">Speaking Practice</h1>
      <div className={`w-3 h-3 rounded-full ${isConnected ? 'bg-green-500' : 'bg-red-500'}`} />
    </div>
  );
}
```

## Troubleshooting

### Issue: WebSocket connection failed
- Verify WebSocket URL in `.env.local`
- Check Lambda permissions
- Verify Cognito token

### Issue: Audio not recording
- Grant microphone permission
- Use HTTPS (required for getUserMedia)

## Checklist

- [ ] WebSocket client created
- [ ] Audio recorder hook created
- [ ] Speaking component created
- [ ] WebSocket connection successful
- [ ] Audio recording works

## Next Steps

Frontend complete! Continue to [CI/CD Pipeline](../../5.6-CI-CD/) for automation.
