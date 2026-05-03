---
title: "WebSocket Integration"
date: "2026-05-02"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Tổng quan

WebSocket cho phép giao tiếp **real-time** giữa frontend và backend cho tính năng **Speaking Practice**. Section này hướng dẫn tích hợp WebSocket vào Next.js.

> **📸 CẦN THÊM**: Thêm diagram WebSocket flow

## WebSocket Flow

| Bước | Mô tả |
|------|-------|
| 1 | User speaks |
| 2 | Frontend ghi âm → gửi audio chunks qua WebSocket |
| 3 | Lambda nhận audio → Transcribe (STT) |
| 4 | Lambda gửi text → Bedrock (AI response) |
| 5 | Lambda nhận AI response → Polly (TTS) |
| 6 | Lambda gửi audio response về frontend |
| 7 | Frontend phát audio cho user |

## Bước 1: Tạo WebSocket Client

Tạo `lib/websocket.ts`:

```typescript
export class WebSocketClient {
  private ws: WebSocket | null = null;
  private url: string;
  private reconnectAttempts = 0;
  private maxReconnectAttempts = 5;
  private reconnectDelay = 1000;

  // Event handlers
  public onOpen: (() => void) | null = null;
  public onMessage: ((data: any) => void) | null = null;
  public onClose: (() => void) | null = null;
  public onError: ((error: Event) => void) | null = null;

  constructor(url: string) {
    this.url = url;
  }

  connect(token?: string) {
    try {
      // Add auth token to URL if provided
      const wsUrl = token ? `${this.url}?token=${token}` : this.url;
      
      this.ws = new WebSocket(wsUrl);

      this.ws.onopen = () => {
        console.log('WebSocket connected');
        this.reconnectAttempts = 0;
        this.onOpen?.();
      };

      this.ws.onmessage = (event) => {
        try {
          const data = JSON.parse(event.data);
          this.onMessage?.(data);
        } catch (error) {
          console.error('Failed to parse WebSocket message:', error);
        }
      };

      this.ws.onclose = () => {
        console.log('WebSocket disconnected');
        this.onClose?.();
        this.attemptReconnect(token);
      };

      this.ws.onerror = (error) => {
        console.error('WebSocket error:', error);
        this.onError?.(error);
      };
    } catch (error) {
      console.error('Failed to create WebSocket:', error);
    }
  }

  private attemptReconnect(token?: string) {
    if (this.reconnectAttempts < this.maxReconnectAttempts) {
      this.reconnectAttempts++;
      console.log(`Reconnecting... Attempt ${this.reconnectAttempts}`);
      
      setTimeout(() => {
        this.connect(token);
      }, this.reconnectDelay * this.reconnectAttempts);
    }
  }

  send(data: any) {
    if (this.ws && this.ws.readyState === WebSocket.OPEN) {
      this.ws.send(JSON.stringify(data));
    } else {
      console.error('WebSocket is not connected');
    }
  }

  sendBinary(data: ArrayBuffer) {
    if (this.ws && this.ws.readyState === WebSocket.OPEN) {
      this.ws.send(data);
    } else {
      console.error('WebSocket is not connected');
    }
  }

  close() {
    if (this.ws) {
      this.ws.close();
      this.ws = null;
    }
  }

  isConnected(): boolean {
    return this.ws?.readyState === WebSocket.OPEN;
  }
}
```

## Bước 2: Tạo Audio Recorder Hook

Tạo `hooks/useAudioRecorder.ts`:

```typescript
import { useState, useRef, useCallback } from 'react';

export const useAudioRecorder = () => {
  const [isRecording, setIsRecording] = useState(false);
  const mediaRecorderRef = useRef<MediaRecorder | null>(null);
  const audioChunksRef = useRef<Blob[]>([]);

  const startRecording = useCallback(async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ 
        audio: {
          echoCancellation: true,
          noiseSuppression: true,
          sampleRate: 16000,
        } 
      });

      const mediaRecorder = new MediaRecorder(stream, {
        mimeType: 'audio/webm',
      });

      mediaRecorderRef.current = mediaRecorder;
      audioChunksRef.current = [];

      mediaRecorder.ondataavailable = (event) => {
        if (event.data.size > 0) {
          audioChunksRef.current.push(event.data);
        }
      };

      mediaRecorder.start(100); // Collect data every 100ms
      setIsRecording(true);
    } catch (error) {
      console.error('Failed to start recording:', error);
      throw error;
    }
  }, []);

  const stopRecording = useCallback((): Promise<Blob> => {
    return new Promise((resolve) => {
      if (!mediaRecorderRef.current) {
        resolve(new Blob());
        return;
      }

      mediaRecorderRef.current.onstop = () => {
        const audioBlob = new Blob(audioChunksRef.current, { 
          type: 'audio/webm' 
        });
        
        // Stop all tracks
        mediaRecorderRef.current?.stream
          .getTracks()
          .forEach(track => track.stop());
        
        setIsRecording(false);
        resolve(audioBlob);
      };

      mediaRecorderRef.current.stop();
    });
  }, []);

  const getAudioChunks = useCallback(() => {
    return audioChunksRef.current;
  }, []);

  return {
    isRecording,
    startRecording,
    stopRecording,
    getAudioChunks,
  };
};
```

## Bước 3: Tạo Speaking Component

Tạo `app/(app)/speaking/page.tsx`:

```typescript
'use client';

import { useEffect, useRef, useState } from 'react';
import { WebSocketClient } from '@/lib/websocket';
import { useAudioRecorder } from '@/hooks/useAudioRecorder';
import { fetchAuthSession } from 'aws-amplify/auth';
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';

export default function SpeakingPage() {
  const [isConnected, setIsConnected] = useState(false);
  const [messages, setMessages] = useState<string[]>([]);
  const [transcript, setTranscript] = useState('');
  const wsRef = useRef<WebSocketClient | null>(null);
  const audioRef = useRef<HTMLAudioElement | null>(null);
  
  const { 
    isRecording, 
    startRecording, 
    stopRecording 
  } = useAudioRecorder();

  // Initialize WebSocket
  useEffect(() => {
    const initWebSocket = async () => {
      try {
        // Get auth token
        const session = await fetchAuthSession();
        const token = session?.tokens?.accessToken?.toString();

        // Create WebSocket client
        const ws = new WebSocketClient(
          process.env.NEXT_PUBLIC_WEBSOCKET_URL || ''
        );

        ws.onOpen = () => {
          setIsConnected(true);
          addMessage('Connected to server');
        };

        ws.onMessage = (data) => {
          handleWebSocketMessage(data);
        };

        ws.onClose = () => {
          setIsConnected(false);
          addMessage('Disconnected from server');
        };

        ws.onError = (error) => {
          console.error('WebSocket error:', error);
          addMessage('Connection error');
        };

        ws.connect(token);
        wsRef.current = ws;
      } catch (error) {
        console.error('Failed to initialize WebSocket:', error);
      }
    };

    initWebSocket();

    return () => {
      wsRef.current?.close();
    };
  }, []);

  const handleWebSocketMessage = (data: any) => {
    switch (data.type) {
      case 'transcript':
        setTranscript(data.text);
        addMessage(`You: ${data.text}`);
        break;
      
      case 'ai_response':
        addMessage(`AI: ${data.text}`);
        break;
      
      case 'audio':
        playAudio(data.audioUrl);
        break;
      
      case 'error':
        addMessage(`Error: ${data.message}`);
        break;
      
      default:
        console.log('Unknown message type:', data);
    }
  };

  const addMessage = (message: string) => {
    setMessages((prev) => [...prev, message]);
  };

  const playAudio = (audioUrl: string) => {
    if (audioRef.current) {
      audioRef.current.src = audioUrl;
      audioRef.current.play();
    }
  };

  const handleStartRecording = async () => {
    try {
      await startRecording();
      addMessage('Recording started...');
    } catch (error) {
      console.error('Failed to start recording:', error);
      addMessage('Failed to start recording');
    }
  };

  const handleStopRecording = async () => {
    try {
      const audioBlob = await stopRecording();
      
      // Convert blob to base64
      const reader = new FileReader();
      reader.onloadend = () => {
        const base64Audio = reader.result as string;
        
        // Send audio to server
        wsRef.current?.send({
          action: 'process_audio',
          audio: base64Audio.split(',')[1], // Remove data:audio/webm;base64,
        });
      };
      reader.readAsDataURL(audioBlob);
      
      addMessage('Processing audio...');
    } catch (error) {
      console.error('Failed to stop recording:', error);
      addMessage('Failed to process audio');
    }
  };

  return (
    <div className="container mx-auto p-6">
      <h1 className="text-3xl font-bold mb-6">Speaking Practice</h1>

      {/* Connection Status */}
      <Card className="p-4 mb-6">
        <div className="flex items-center gap-2">
          <div 
            className={`w-3 h-3 rounded-full ${
              isConnected ? 'bg-green-500' : 'bg-red-500'
            }`}
          />
          <span className="text-lg">
            {isConnected ? 'Connected' : 'Disconnected'}
          </span>
        </div>
      </Card>

      {/* Recording Controls */}
      <Card className="p-6 mb-6">
        <div className="flex flex-col items-center gap-4">
          <Button
            size="lg"
            variant={isRecording ? 'destructive' : 'default'}
            onClick={isRecording ? handleStopRecording : handleStartRecording}
            disabled={!isConnected}
          >
            {isRecording ? 'Stop Recording' : 'Start Recording'}
          </Button>
          
          {transcript && (
            <div className="text-center">
              <p className="text-sm text-gray-500">Transcript:</p>
              <p className="text-lg">{transcript}</p>
            </div>
          )}
        </div>
      </Card>

      {/* Messages */}
      <Card className="p-6">
        <h2 className="text-xl font-semibold mb-4">Conversation</h2>
        <div className="space-y-2 max-h-96 overflow-y-auto">
          {messages.map((msg, idx) => (
            <div 
              key={idx} 
              className={`p-3 rounded ${
                msg.startsWith('You:') 
                  ? 'bg-blue-100 ml-auto max-w-[80%]' 
                  : msg.startsWith('AI:')
                  ? 'bg-gray-100 mr-auto max-w-[80%]'
                  : 'bg-yellow-50 text-center'
              }`}
            >
              {msg}
            </div>
          ))}
        </div>
      </Card>

      {/* Hidden audio element */}
      <audio ref={audioRef} className="hidden" />
    </div>
  );
}
```

> **📸 CẦN THÊM**: Thêm screenshot Speaking page UI

## Bước 4: Test WebSocket Connection

```bash
# Chạy dev server
npm run dev

# Truy cập http://localhost:3000/speaking
```

**Kiểm tra**:
1. Connection status hiển thị "Connected"
2. Click "Start Recording" → microphone permission
3. Nói vào microphone
4. Click "Stop Recording" → audio được gửi
5. Nhận transcript và AI response

> **📸 CẦN THÊM**: Thêm screenshot WebSocket connection trong browser DevTools

## Bước 5: Handle WebSocket Errors

Tạo `components/websocket-error-boundary.tsx`:

```typescript
'use client';

import { Component, ReactNode } from 'react';
import { Alert, AlertDescription, AlertTitle } from '@/components/ui/alert';
import { Button } from '@/components/ui/button';

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class WebSocketErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: any) {
    console.error('WebSocket error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <Alert variant="destructive">
          <AlertTitle>WebSocket Connection Error</AlertTitle>
          <AlertDescription>
            {this.state.error?.message || 'Failed to connect to server'}
          </AlertDescription>
          <Button 
            onClick={() => window.location.reload()}
            className="mt-4"
          >
            Retry Connection
          </Button>
        </Alert>
      );
    }

    return this.props.children;
  }
}
```

Wrap Speaking page:

```typescript
// app/(app)/speaking/layout.tsx
import { WebSocketErrorBoundary } from '@/components/websocket-error-boundary';

export default function SpeakingLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <WebSocketErrorBoundary>
      {children}
    </WebSocketErrorBoundary>
  );
}
```

## Khắc phục sự cố

### Vấn đề: WebSocket connection failed

```bash
# Kiểm tra WebSocket API endpoint
aws apigatewayv2 get-apis \
  --region <REGION> \
  --query 'Items[?Name==`lexi-be-SpeakingWebSocketApi`]'
```

**Giải pháp**:
1. Verify WebSocket URL trong `.env.local`
2. Check Lambda function có permission invoke
3. Verify Cognito token

### Vấn đề: Audio not recording

```bash
# Check browser permissions
# Chrome: chrome://settings/content/microphone
# Firefox: about:preferences#privacy
```

**Giải pháp**:
1. Grant microphone permission
2. Use HTTPS (required for getUserMedia)
3. Check browser compatibility

### Vấn đề: No audio playback

**Giải pháp**:
1. Check audio element ref
2. Verify audio URL format
3. Check browser audio permissions

## Performance Optimization

### 1. Audio Compression

```typescript
// Compress audio before sending
const compressAudio = async (blob: Blob): Promise<Blob> => {
  // Use Web Audio API to compress
  const audioContext = new AudioContext();
  const arrayBuffer = await blob.arrayBuffer();
  const audioBuffer = await audioContext.decodeAudioData(arrayBuffer);
  
  // Downsample to 16kHz
  const offlineContext = new OfflineAudioContext(
    1, // mono
    audioBuffer.duration * 16000,
    16000
  );
  
  const source = offlineContext.createBufferSource();
  source.buffer = audioBuffer;
  source.connect(offlineContext.destination);
  source.start();
  
  const renderedBuffer = await offlineContext.startRendering();
  
  // Convert to blob
  // ... conversion logic
  
  return blob;
};
```

### 2. Message Batching

```typescript
// Batch messages to reduce WebSocket overhead
class MessageBatcher {
  private queue: any[] = [];
  private timer: NodeJS.Timeout | null = null;
  
  add(message: any) {
    this.queue.push(message);
    
    if (!this.timer) {
      this.timer = setTimeout(() => this.flush(), 100);
    }
  }
  
  flush() {
    if (this.queue.length > 0) {
      wsClient.send({
        action: 'batch',
        messages: this.queue,
      });
      this.queue = [];
    }
    this.timer = null;
  }
}
```

## Danh sách kiểm tra

- [ ] WebSocket client đã được tạo
- [ ] Audio recorder hook đã được tạo
- [ ] Speaking component đã được tạo
- [ ] Error boundary đã được setup
- [ ] WebSocket connection thành công
- [ ] Audio recording hoạt động
- [ ] Audio playback hoạt động
- [ ] Transcript hiển thị đúng
- [ ] AI response nhận được

## Bước Tiếp Theo

Frontend đã hoàn thành! Tiếp tục đến [CI/CD Pipeline](../../5.6-CI-CD/) để setup automation.
