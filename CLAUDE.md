# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Development
npm run dev          # Start dev server with turbopack (localhost:3000)
npm run build        # Production build
npm run lint         # ESLint check

# Testing
npm test             # Run all tests with vitest
npm test -- --run    # Run tests once (no watch mode)
npm test -- <file>   # Run specific test file

# Database
npm run setup        # Install deps, generate Prisma client, run migrations
npm run db:reset     # Reset database (destructive)
npx prisma studio    # Open Prisma Studio GUI
```

## Architecture

UIGen is an AI-powered React component generator with live preview. Users describe components via chat, and Claude generates React/Tailwind code that renders in real-time.

### Core Flow

1. **Chat API** (`src/app/api/chat/route.ts`) - Receives messages, streams AI responses via Vercel AI SDK
2. **Virtual File System** (`src/lib/file-system.ts`) - In-memory FS for generated components. No files written to disk.
3. **JSX Transformer** (`src/lib/transform/jsx-transformer.ts`) - Babel transforms JSX, creates blob URLs, builds import maps for esm.sh
4. **Preview Frame** (`src/components/preview/PreviewFrame.tsx`) - Sandboxed iframe renders transformed code with Tailwind CDN

### AI Tools

The AI uses two tools to manipulate the virtual file system:

- `str_replace_editor` (`src/lib/tools/str-replace.ts`) - view, create, str_replace, insert commands
- `file_manager` (`src/lib/tools/file-manager.ts`) - rename, delete commands

### Context Providers

- `FileSystemProvider` - Manages VirtualFileSystem state, handles tool calls from AI
- `ChatProvider` - Wraps Vercel AI SDK's useChat, syncs with file system

### Key Conventions

- Generated projects must have `/App.jsx` as entry point
- Imports use `@/` alias (e.g., `@/components/Button`)
- All generated files use React + Tailwind CSS
- Anonymous users can work without auth; registered users get persistence via Prisma/SQLite
- Use comments sparingly. Only comment complex code.
- The database schema is defined in the @prisma\schema.prisma file. Reference it anytime you need to understand the structure of data stored in the database.