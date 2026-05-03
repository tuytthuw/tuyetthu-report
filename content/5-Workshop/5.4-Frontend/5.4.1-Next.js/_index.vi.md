---
title: "Next.js Setup"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Tổng quan

Lexi frontend được xây dựng với **Next.js 16** (App Router) và **TypeScript**. Section này hướng dẫn setup project từ đầu.

> **📸 CẦN THÊM**: Thêm screenshot cấu trúc thư mục Next.js project

## Cấu trúc Project

```
lexi-fe/
├── app/                          # Next.js App Router
│   ├── layout.tsx               # Root layout
│   ├── page.tsx                 # Home page
│   ├── (auth)/                  # Auth routes group
│   │   ├── login/page.tsx
│   │   └── signup/page.tsx
│   ├── (app)/                   # Protected routes group
│   │   ├── dashboard/page.tsx
│   │   ├── flashcards/page.tsx
│   │   ├── speaking/page.tsx
│   │   └── profile/page.tsx
│   └── api/                     # API routes
│       └── auth/[...nextauth]/route.ts
├── components/                   # React components
│   ├── ui/                      # Shadcn/ui components
│   ├── flashcard/               # Flashcard components
│   ├── speaking/                # Speaking components
│   └── dashboard/               # Dashboard components
├── features/                     # Feature modules
│   ├── auth/                    # Authentication
│   ├── flashcards/              # Flashcard feature
│   ├── speaking/                # Speaking feature
│   └── profile/                 # Profile feature
├── lib/                         # Utilities
│   ├── api.ts                   # API client
│   ├── websocket.ts             # WebSocket client
│   └── auth.ts                  # Auth utilities
├── hooks/                       # Custom React hooks
├── public/                      # Static assets
├── next.config.ts               # Next.js config
├── package.json                 # Dependencies
└── tsconfig.json                # TypeScript config
```

## Bước 1: Khởi tạo Next.js Project

```bash
# Tạo Next.js project với TypeScript
npx create-next-app@latest lexi-fe \
  --typescript \
  --tailwind \
  --app \
  --no-eslint \
  --import-alias "@/*"

cd lexi-fe
```

> **📸 CẦN THÊM**: Thêm screenshot terminal với output của create-next-app

**Kết quả mong đợi**:
- Project được tạo với App Router
- TypeScript đã được cấu hình
- Tailwind CSS đã được setup

## Bước 2: Cài đặt Dependencies

```bash
# Core dependencies
npm install aws-amplify @aws-amplify/adapter-nextjs
npm install @tanstack/react-query @tanstack/react-query-devtools
npm install axios zustand
npm install date-fns clsx class-variance-authority

# UI components (Shadcn/ui)
npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu
npm install @radix-ui/react-select @radix-ui/react-tabs
npm install lucide-react

# Form handling
npm install react-hook-form @hookform/resolvers zod

# Dev dependencies
npm install -D @types/node @types/react @types/react-dom
```

> **📸 CẦN THÊM**: Thêm screenshot package.json với dependencies

## Bước 3: Cấu hình TypeScript

Cập nhật `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

## Bước 4: Cấu hình Next.js

Cập nhật `next.config.ts`:

```typescript
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  reactStrictMode: true,
  
  // Environment variables
  env: {
    NEXT_PUBLIC_API_URL: process.env.NEXT_PUBLIC_API_URL,
    NEXT_PUBLIC_WEBSOCKET_URL: process.env.NEXT_PUBLIC_WEBSOCKET_URL,
    NEXT_PUBLIC_COGNITO_REGION: process.env.NEXT_PUBLIC_COGNITO_REGION,
    NEXT_PUBLIC_COGNITO_USER_POOL_ID: process.env.NEXT_PUBLIC_COGNITO_USER_POOL_ID,
    NEXT_PUBLIC_COGNITO_CLIENT_ID: process.env.NEXT_PUBLIC_COGNITO_CLIENT_ID,
  },
  
  // Image optimization
  images: {
    domains: ['<S3_BUCKET>.s3.<REGION>.amazonaws.com'],
  },
};

export default nextConfig;
```

## Bước 5: Tạo Environment Variables

Tạo file `.env.local`:

```bash
# API Endpoints
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod

# Cognito Configuration
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>

# Optional
NEXT_PUBLIC_S3_BUCKET=<BUCKET_NAME>
```

**Lấy giá trị từ AWS**:

```bash
# Lấy API Gateway URL
aws apigateway get-rest-apis \
  --region <REGION> \
  --query 'items[?name==`lexi-be-LexiApi`].id' \
  --output text

# Lấy WebSocket API URL
aws apigatewayv2 get-apis \
  --region <REGION> \
  --query 'Items[?Name==`lexi-be-SpeakingWebSocketApi`].ApiId' \
  --output text

# Lấy Cognito User Pool ID
aws cognito-idp list-user-pools \
  --max-results 10 \
  --region <REGION> \
  --query 'UserPools[?Name==`LexiUserPool-prod`].Id' \
  --output text
```

> **📸 CẦN THÊM**: Thêm screenshot .env.local file

## Bước 6: Setup Root Layout

Tạo `app/layout.tsx`:

```typescript
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import './globals.css';
import { Providers } from '@/components/providers';

const inter = Inter({ subsets: ['latin'] });

export const metadata: Metadata = {
  title: 'Lexi - AI English Tutor',
  description: 'Learn English with AI-powered speaking practice',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

## Bước 7: Setup Providers

Tạo `components/providers.tsx`:

```typescript
'use client';

import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { useState } from 'react';

export function Providers({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            staleTime: 60 * 1000, // 1 minute
            refetchOnWindowFocus: false,
          },
        },
      })
  );

  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

## Bước 8: Tạo API Client

Tạo `lib/api.ts`:

```typescript
import axios from 'axios';
import { fetchAuthSession } from 'aws-amplify/auth';

const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add auth token to requests
apiClient.interceptors.request.use(async (config) => {
  try {
    const session = await fetchAuthSession();
    if (session?.tokens?.accessToken) {
      config.headers.Authorization = `Bearer ${session.tokens.accessToken.toString()}`;
    }
  } catch (error) {
    console.error('Failed to get auth token:', error);
  }
  return config;
});

// Handle errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Redirect to login
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export { apiClient };
```

## Bước 9: Test Development Server

```bash
# Chạy dev server
npm run dev

# Truy cập http://localhost:3000
```

> **📸 CẦN THÊM**: Thêm screenshot browser với Next.js welcome page

**Kiểm tra**:
- [ ] Dev server chạy thành công
- [ ] Không có TypeScript errors
- [ ] Hot reload hoạt động
- [ ] Environment variables được load

## Khắc phục sự cố

### Vấn đề: Module not found

```bash
# Xóa node_modules và reinstall
rm -rf node_modules package-lock.json
npm install
```

### Vấn đề: TypeScript errors

```bash
# Rebuild TypeScript
npm run build
```

### Vấn đề: Port 3000 đã được sử dụng

```bash
# Chạy trên port khác
PORT=3001 npm run dev
```

## Danh sách kiểm tra

- [ ] Next.js project đã được tạo
- [ ] Dependencies đã được cài đặt
- [ ] TypeScript đã được cấu hình
- [ ] Environment variables đã được setup
- [ ] Root layout đã được tạo
- [ ] Providers đã được setup
- [ ] API client đã được tạo
- [ ] Dev server chạy thành công

## Bước Tiếp Theo

Tiếp tục đến [Amplify Deployment](../5.4.2-Amplify/) để deploy frontend lên AWS.
