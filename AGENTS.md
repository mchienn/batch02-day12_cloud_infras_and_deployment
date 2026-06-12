# Day 12 — Cloud Infrastructure & Deployment Lab

Training lab repo — Python 3.11+ AI agent deployment từ localhost đến production. Mỗi folder `01-` đến `06-` là một lab section, `06-lab-complete/` là final deliverable.

## Tech Stack

- Python 3.11+, FastAPI, uvicorn, Pydantic
- Docker multi-stage builds + Docker Compose
- Redis (cache/session)
- Deploy targets: Railway, Render, GCP Cloud Run

## Commands

```bash
# Run any section locally
cd <section>/develop && pip install -r requirements.txt && python app.py

# Docker build & run (from any section with Dockerfile)
docker build -t my-agent . && docker run -p 8000:8000 my-agent

# Docker Compose (06-lab-complete or 05/production)
docker compose up

# Production readiness check (06-lab-complete only)
python check_production_ready.py

# Deploy Railway (03-cloud-deployment/railway or 06-lab-complete)
npm i -g @railway/cli && railway login && railway init && railway up && railway domain

# Test endpoint
curl http://localhost:8000/health
curl -H "X-API-Key: $AGENT_API_KEY" -X POST http://localhost:8000/ask \
  -H "Content-Type: application/json" -d '{"question": "Hello"}'
```

## Architecture

```
01-localhost-vs-production/   Dev vs prod comparison (anti-patterns vs 12-factor)
02-docker/                    Dockerfile basics → multi-stage production
03-cloud-deployment/          Railway, Render, Cloud Run deploy configs
04-api-gateway/               API Key auth, JWT, rate limiting, cost guard
05-scaling-reliability/       Health checks, stateless, Redis, Nginx LB
06-lab-complete/              FINAL PROJECT — combines all above
utils/mock_llm.py             Mock LLM — shared across all sections, no API key needed
```

## Code Style

- Config via environment variables only (12-Factor), never hardcoded
- Each section has `develop/` (simple) and `production/` (full-featured) variants
- `06-lab-complete/app/` is the canonical entry point: `app.main:app`

## Testing

- No test suite — validate with `curl` endpoints and `check_production_ready.py`
- Health: `GET /health` | Ready: `GET /ready` | Chat: `POST /ask` (requires `X-API-Key`)

## Boundaries

Never:
- Commit `.env`, `.env.local`, or any file with secrets
- Modify `utils/mock_llm.py` — shared mock used by all sections
- Hardcode API keys, passwords, or secrets in any file
- Skip `.dockerignore` when adding new Dockerfiles

Ask first before:
- Changing `requirements.txt` versions (may break Docker builds)
- Modifying Docker health check commands
- Changing port from 8000
