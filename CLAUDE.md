# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repo sets up the local infrastructure for [claude-context](https://github.com/zilliztech/claude-context), an MCP plugin that gives AI coding agents semantic code search over your codebase using Milvus vector database. It also includes [Attu](https://github.com/zilliztech/attu), a GUI for managing and inspecting Milvus collections.

**claude-context** indexes codebases into Milvus using hybrid search (BM25 + dense vector embeddings) so AI agents retrieve only semantically relevant code instead of loading entire directories.

## Infrastructure

Docker Compose runs a Milvus standalone deployment with four services:

- **Milvus** (v2.6.11) — vector database on port `19530` (gRPC) and `9091` (health/metrics)
- **Attu** (v2.6) — Milvus GUI on port `8000`
- **etcd** (v3.5.25) — metadata storage for Milvus
- **MinIO** — object storage for Milvus, with console on port `9001` and API on port `9000`

Data is persisted in `./volumes/` (etcd, minio, milvus).

### Start Services

```bash
docker compose up -d
```

### Verify Health

```bash
curl -f http://localhost:9091/healthz
```
