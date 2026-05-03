---
title: "Next.js Setup"
date: "2026-05-02"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Overview

Lexi frontend is built with **Next.js 16** (App Router) and **TypeScript**. This section guides you through setting up the project from scratch.

## Project Structure

```
lexi-fe/
├── app/                          # Next.js App Router
│   ├── layout.tsx               # Root layout
│   ├── page.tsx                 # Home page
│   ├── (auth)/                  # Auth routes group
│   ├── (app)/                   # Protected routes group
│   └── api/                     # API routes
├── components/                   # React components
├── features/                     # Feature modules
├── lib/                         # Utilities
├── hooks/                       # Custom React hooks
├── public/                      # Static assets
├── next.config.ts               # Next.js config
├── package.json                 # Dependencies
└── tsconfig.json                # TypeScript config
```

## Step 1: Initialize Next.js Project

```bash
# Create Next.js project with TypeScript
npx create-next-app@latest lexi-fe \
  --typescript \
  --tailwind \
  --app \
  --no-eslint \
  --import-alias "@/*"

cd lexi-fe
```

## Step 2: Install Dependencies

```bash
# Core dependencies
npm install aws-amplify @aws-amplify/adapter-nextjs
npm install @tanstack/react-query axios zustand
npm install date-fns clsx class-variance-authority

# UI components (Shadcn/ui)
npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu
npm install lucide-react

# Form handling
npm install react-hook-form @hookform/resolvers zod
```

## Step 3: Configure Environment Variables

Create `.env.local`:

```bash
NEXT_PUBLIC_API_URL=https://<API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_WEBSOCKET_URL=wss://<WEBSOCKET_API_ID>.execute-api.<REGION>.amazonaws.com/Prod
NEXT_PUBLIC_COGNITO_REGION=<REGION>
NEXT_PUBLIC_COGNITO_USER_POOL_ID=<USER_POOL_ID>
NEXT_PUBLIC_COGNITO_CLIENT_ID=<CLIENT_ID>
```

## Step 4: Create API Client

Create `lib/api.ts`:

```typescript
import axios from 'axios';
import { fetchAuthSession } from 'aws-amplify/auth';

const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  headers: { 'Content-Type': 'application/json' },
});

apiClient.interceptors.request.use(async (config) => {
  const session = await fetchAuthSession();
  if (session?.tokens?.accessToken) {
    config.headers.Authorization = `Bearer ${session.tokens.accessToken}`;
  }
  return config;
});

export { apiClient };
```

## Step 5: Test Development Server

```bash
npm run dev
# Access http://localhost:3000
```

## Checklist

- [ ] Next.js project created
- [ ] Dependencies installed
- [ ] Environment variables configured
- [ ] API client created
- [ ] Dev server running successfully

## Next Steps

Continue to [Amplify Deployment](../5.4.2-Amplify/) to deploy frontend to AWS.
