# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

UIGen is an AI-powered React component generator with live preview. Users describe components in chat, and Claude generates React code that renders in a live preview iframe.

## Commands

```bash
npm run setup        # Initial setup: install deps, generate Prisma client, run migrations
npm run dev          # Start development server (Turbopack)
npm run build        # Production build
npm run test         # Run tests with Vitest
npm run lint         # Run ESLint
npm run db:reset     # Reset SQLite database
```

## Architecture

### Core Flow
1. User sends message via ChatInterface → `/api/chat` endpoint
2. AI (Claude or mock provider) generates/edits components using tools
3. VirtualFileSystem stores generated files in memory
4. PreviewFrame transforms JSX with Babel and renders in iframe
5. Projects persisted to SQLite for authenticated users

### Key Directories
- `src/app/api/chat/route.ts` - Main AI endpoint, handles streaming responses
- `src/lib/tools/` - AI tool definitions (str-replace-editor, file-manager)
- `src/lib/prompts/generation.tsx` - System prompt for component generation
- `src/lib/file-system.ts` - VirtualFileSystem class for in-memory file management
- `src/lib/transform/jsx-transformer.ts` - Babel-based JSX transformation for preview
- `src/lib/contexts/` - React contexts for chat and file system state
- `src/actions/` - Server actions for auth and project CRUD

### AI Integration
- Uses Vercel AI SDK with Anthropic provider (claude-haiku-4-5)
- Mock provider returns static components when `ANTHROPIC_API_KEY` is not set
- Two AI tools available:
  - `str_replace_editor` - Create/view/edit files
  - `file_manager` - Rename/delete files
- Max 40 tool steps for real API, 4 for mock

### Virtual File System
- All files stored in memory, not written to disk
- Root path is `/`, paths must start with `/`
- Serializable to JSON for database persistence
- Auto-creates parent directories on file creation

### Preview System
- Transforms JSX using Babel standalone
- Entry point detection: /App.jsx, /App.tsx, /index.jsx, /index.tsx, or first .jsx/.tsx
- Creates iframe with import map for React/ReactDOM
- Handles CSS imports as style tags

## Tech Stack
- Next.js 15 (App Router), React 19, TypeScript (strict)
- Tailwind CSS v4, shadcn/ui components
- Prisma with SQLite, JWT auth (jose + bcrypt)
- Vitest for testing

## Path Alias
`@/*` maps to `src/*`

## Code Style
- Use comments sparingly. Only comment complex code.
