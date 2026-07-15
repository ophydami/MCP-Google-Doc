# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build & Run

```bash
npm install          # install dependencies
npm run build        # compile TypeScript → build/
npm start            # run the MCP server
npm run dev          # watch mode — recompiles on change
```

Entry point: `build/server.js`. Requires env vars or local `credentials.json` + `token.json` for Google OAuth.

## Architecture

### Current State
Single-file MCP server (`src/server.ts`) exposing Google Docs/Drive CRUD operations over the Model Context Protocol via stdio. Auth: OAuth 2.0 via `@google-cloud/local-auth`; tokens cached in `token.json`.

**Tools:** `list-docs`, `get-doc`, `create-doc`, `update-doc`, `search-docs`, `delete-doc`, `list-tabs`, `get-tab`
**Resources:** `googledocs://list`, `googledocs://{docId}`
**Prompts:** `create-doc-template`, `analyze-doc`

### Target Use Case: Research Assistant

This server is being shaped as a **research assistant** — helping students, academics, and researchers use Claude alongside their Google Docs to organize, search, and synthesize research.

Key workflows:
- **Search & synthesize** — search across an entire Docs library and summarize findings from multiple documents
- **Structured document creation** — generate outlines, draft papers, literature reviews, and annotated bibliographies
- **Tab-based organization** — use Google Docs tabs to separate research sections (Sources, Notes, Draft) and let Claude read or update specific tabs
- **Shared Drive access** — collaborate with research teams via Shared Drives

## Hard Rules

- **Never commit `credentials.json` or `token.json`** — OAuth secrets, already in `.gitignore`.
- **Create a PR after every completed task** before starting the next one.

## Connecting to Claude

**Claude Desktop** (`%APPDATA%\Claude\claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "googledocs": {
      "command": "node",
      "args": ["C:/absolute/path/to/build/server.js"]
    }
  }
}
```

**Claude Code CLI:**
```bash
claude mcp add --transport stdio google-docs node /absolute/path/to/build/server.js
```
