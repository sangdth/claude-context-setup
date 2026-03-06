# claude-context local setup

Local infrastructure for [claude-context](https://github.com/zilliztech/claude-context) — an MCP plugin that gives AI coding agents semantic code search over your entire codebase using vector embeddings.

Includes [Attu](https://github.com/zilliztech/attu), a GUI for managing and inspecting your Milvus vector database.

## What is claude-context?

claude-context is an MCP (Model Context Protocol) server that indexes your codebase into a Milvus vector database using hybrid search (BM25 keyword matching + dense vector embeddings). Instead of loading entire directories into an AI agent's context, it retrieves only the semantically relevant code snippets — achieving ~40% token reduction at equivalent retrieval quality.

**MCP tools provided:**

- `index_codebase` — index a directory for hybrid search
- `search_code` — query indexed code with natural language
- `clear_index` — remove indexes for a project
- `get_indexing_status` — monitor indexing progress

## Prerequisites

- Docker and Docker Compose
- Node.js 20.0.0 to <24.0.0
- OpenAI API key (for embeddings)

## Setup

### 1. Start Milvus and necessary services

```bash
docker compose up -d
```

This starts four services:

| Service | Port                            | Description                 |
| ------- | ------------------------------- | --------------------------- |
| Milvus  | `19530` (gRPC), `9091` (health) | Vector database             |
| Attu    | `8000`                          | Milvus GUI                  |
| MinIO   | `9000` (API), `9001` (console)  | Object storage for Milvus   |
| etcd    | `2379`                          | Metadata storage for Milvus |

Verify Milvus is healthy:

```bash
curl -f http://localhost:9091/healthz
```

### 2. Configure claude-context MCP in Claude Code

```bash
claude mcp add claude-context \
  -e OPENAI_API_KEY=sk-your-key \
  -- npx @zilliz/claude-context-mcp@latest
```

or manually:

```json
    "claude-context": {
      "type": "stdio",
      "command": "npx",
      "args": ["@zilliz/claude-context-mcp@latest"],
      "env": {
        "MILVUS_ADDRESS": "127.0.0.1:19530"
      }
    }
```

### 3. Use it

Once configured, Claude Code can index and search your codebase through the MCP tools. Use natural language to search for code patterns, functions, or concepts across your indexed projects.

Browse your Milvus collections visually at **http://localhost:8000** (Attu).

## Data

All data is persisted in `./volumes/` (etcd, minio, milvus). To reset everything:

```bash
docker compose down
rm -rf volumes
```
