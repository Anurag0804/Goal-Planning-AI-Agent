# Goal Planning AI Agent

A production-ready, fullstack template demonstrating how to connect a Python backend powered by Google ADK (Agent Development Kit) with a modern Next.js frontend for goal-planning LLM agents. This repository provides a robust reference architecture for both local development and cloud-scale deployment, including streaming LLM responses, health checks, and seamless cloud integrations.

---

## Overview

- **Backend:** Python application using Google ADK to run a goal-planning LLM agent, easily deployable to Vertex AI Agent Engine.
- **Frontend:** Next.js app with a chat UI, streaming responses, and activity timeline, ready for Vercel deployment.
- **Tooling:** Makefile targets and scripts for local development, linting, type-checking, and production deployment.

---

## Features

- **Goal-planning LLM agent** leveraging Google ADK.
- **Streaming architecture** for real-time chat and agent responses, with robust SSE and incremental JSON fragment processing.
- **Environment-driven routing**: Supports local backend, Vertex AI Agent Engine, or custom proxy/cloud setups.
- **Chat UI**: Modern chat and timeline interface with streaming content and activity visualization.
- **Health checks** and developer-friendly error formatting.
- **Production-grade tooling**: Automated lint/type-check/test for both frontend and backend.

---

## Tech Stack

- **Backend:** Python, [google-adk](https://pypi.org/project/google-adk/), vertexai, python-dotenv
- **Frontend:** Next.js 15, React 19, TailwindCSS, shadcn/ui
- **Tooling:** uv (Python deps), ESLint & Jest (frontend), Ruff & Mypy (backend)

---

## Project Structure

```
app/                       # Python ADK backend
  agent.py                 # Root agent definition (goal-planning)
  agent_engine_app.py      # Deployment helper for Vertex AI Agent Engine
  config.py                # Env loading, Vertex init, deployment config
  utils/                   # GCS + tracing helpers

nextjs/                    # Next.js frontend
  src/app/api/health       # Proxies health checks to backend
  src/app/api/run_sse      # Streaming endpoint (local or Agent Engine)
  src/lib/config.ts        # Env detection + endpoint resolution
  src/lib/handlers/        # Streaming handlers (local/agent-engine)
  src/components/chat/     # Chat UI and timeline components

Makefile                   # install/dev/lint + Agent Engine deploy helper
pyproject.toml             # Python deps and linters
```

---

## Backend (Python, ADK)

- The core agent (in `app/agent.py`) is an ADK `LlmAgent` with built-in planning, accepting high-level goals and producing structured plans and execution steps.
- Environment variables (`app/.env`) control Google Cloud Project config, staging buckets for deployment, model choices, and agent metadata.
- Configuration is validated at import time in `app/config.py` and the Makefile automates dependency management and deployment packaging.
- The backend is served via the ADK HTTP API at `http://127.0.0.1:8000` for local development.

---

## Frontend (Next.js)

- Environment variables (`nextjs/.env.local`) support three deployment targets:
  - **Local backend** (default): `BACKEND_URL=http://127.0.0.1:8000`
  - **Vertex AI Agent Engine**: `AGENT_ENGINE_ENDPOINT` + base64 service account key
  - **Cloud Run proxy**: `CLOUD_RUN_SERVICE_URL`
- Routing logic in `nextjs/src/lib/config.ts` auto-detects the appropriate backend.
- Streaming chat handled via `nextjs/src/app/api/run_sse/route.ts`, with handlers for both local and Agent Engine streaming.
- Health checks are forwarded from frontend (`/api/health`) to backend (`/health`).

---

## Development and Tooling

- **Python:** Lint and type-check using `make lint` (Ruff, Mypy).
- **Node/TypeScript:** Lint with ESLint and test with Jest.
- **Dependencies:** Managed via `uv` for Python and npm for Node.
- **Makefile:** Simplifies install, dev, lint, and deployment workflows.

---

## Deployment

- **Backend:** Deploy to Vertex AI Agent Engine using `make deploy-adk`. This exports Python dependencies, packages the agent, deploys via `app/agent_engine_app.py`, and writes deployment metadata.
- **Frontend:** Deploy to Vercel, providing relevant environment variables (`AGENT_ENGINE_ENDPOINT`, `GOOGLE_SERVICE_ACCOUNT_KEY_BASE64`). See `NEXTJS_VERCEL_DEPLOYMENT_GUIDE.md` for details.

---

## Health & Troubleshooting

- Health checks are available via `/api/health`.
- Backend environment is validated at startup; ensure required Google Cloud env vars are set.
- For Agent Engine deployments, ensure service account permissions and endpoint values are correct.
- Local streaming requires an active backend and correct `BACKEND_URL` in the frontend env.
- Authentication for Agent Engine streaming requires a valid base64-encoded service account key.

---

## License

Apache-2.0 (unless noted otherwise in third-party files).

---
