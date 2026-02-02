# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AnythingLLM is a full-stack application for creating private ChatGPT-like interfaces with any documents. It supports multiple LLM providers, vector databases, and features multi-user management with workspaces.

## Architecture

This is a monorepo with four main components:

- **server/** - Node.js/Express backend (port 3001) handling API endpoints, LLM interactions, and vector DB management
- **frontend/** - Vite + React frontend (port 3000) with Tailwind CSS
- **collector/** - Node.js service (port 8888) for document processing and parsing
- **docker/** - Docker configuration for containerized deployment

### Key Server Directories

- `server/endpoints/` - Express route handlers (admin, chat, workspaces, API endpoints)
- `server/models/` - Prisma model abstractions for database entities
- `server/utils/AiProviders/` - LLM provider implementations (OpenAI, Anthropic, Ollama, etc.)
- `server/utils/vectorDbProviders/` - Vector database implementations (LanceDB, Pinecone, Chroma, etc.)
- `server/utils/agents/` - AI agent system (aibitat framework) with plugins for web browsing, SQL, etc.
- `server/prisma/` - Database schema and migrations (SQLite by default, PostgreSQL supported)

### Key Frontend Directories

- `frontend/src/pages/` - React page components organized by feature
- `frontend/src/components/` - Reusable UI components
- `frontend/src/locales/` - i18n translation files

### Collector Processing

- `collector/processSingleFile/` - File type converters (PDF, DOCX, audio, images, etc.)
- `collector/processLink/` - Web page and URL processing

## Development Commands

```bash
# Initial setup (run once from repo root)
yarn setup

# Start all services concurrently
yarn dev:all

# Or start services individually in separate terminals
yarn dev:server      # Backend API server
yarn dev:frontend    # React frontend
yarn dev:collector   # Document collector

# Linting
yarn lint            # Lint all packages

# Tests
yarn test            # Run Jest tests (from root)

# Database
yarn prisma:generate  # Generate Prisma client
yarn prisma:migrate   # Run migrations
yarn prisma:seed      # Seed database
yarn prisma:setup     # Run all Prisma commands
yarn prisma:reset     # Reset database

# Build for production
yarn prod:frontend    # Build frontend
yarn prod:server      # Start production server
```

## Environment Configuration

After `yarn setup`, configure these files:
- `server/.env.development` - Server configuration (required)
- `frontend/.env` - Frontend configuration
- `collector/.env` - Collector configuration
- `docker/.env` - Docker deployment configuration

## Database

Uses SQLite by default (`server/storage/anythingllm.db`). PostgreSQL is supported by uncommenting the datasource in `server/prisma/schema.prisma` and setting `DATABASE_URL`.

## Adding New LLM Providers

1. Create provider in `server/utils/AiProviders/{provider}/index.js`
2. Add agent support in `server/utils/agents/aibitat/providers/{provider}.js`
3. Register in model map at `server/utils/AiProviders/modelMap/`

## Adding New Vector Database Providers

1. Create provider in `server/utils/vectorDbProviders/{provider}/index.js`
2. Implement the base interface from `server/utils/vectorDbProviders/base.js`

## Testing

Tests are in `server/__tests__/` using Jest. Run specific test files with:
```bash
cd server && npx jest __tests__/path/to/test.test.js
```

## Workspaces Concept

Workspaces are isolated containers for documents and chat history. Documents can be shared between workspaces, but conversations remain separate. Each workspace can have its own LLM provider, model, and system prompt configuration.
