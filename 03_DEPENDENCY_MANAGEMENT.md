# 📦 Dependency Management - Python Setup & Reproducible Builds

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 4/8  
**Status:** Production Ready ✅  
**Purpose:** Ensure reproducible builds across Dev, Staging, and Production environments

---

## 📍 Purpose

This file teaches you to manage Python dependencies correctly so:
- Development is flexible (easy to experiment)
- Production is frozen (exact, reproducible versions)
- No "works on my machine but not production" surprises
- Security updates happen automatically in dev
- Production never breaks unexpectedly

**Golden Rule:** "Development is Flexible. Production is Frozen."

---

## 🗺️ Quick Navigation

- [The Golden Rule](#-the-golden-rule)
- [File Structure](#-file-structure)
- [The 2026 Standard: uv](#-the-2026-standard-using-uv)
- [Essential Dependencies](#-essential-dependencies-for-2026)
- [Docker Integration](#-docker-integration)
- [pyproject.toml Configuration](#-projecttoml-configuration)
- [Workflow Examples](#-workflow-examples)
- [Security & Vulnerabilities](#-security-vulnerabilities)
- [Troubleshooting](#-troubleshooting)

---

## 🔗 Related Files

**Before this:** [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) (Deep methodology)  
**This file:** [03_DEPENDENCY_MANAGEMENT.md](./03_DEPENDENCY_MANAGEMENT.md) (You are here)  
**After this:** [04_AI_ASSISTANT_INTEGRATION.md](./04_AI_ASSISTANT_INTEGRATION.md) (Claude setup)  
**For production:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (Terraform + Docker)

---

## ✅ What You'll Learn

- [ ] The golden rule (flexible dev, frozen prod)
- [ ] How to use `uv` for fast, reliable installs
- [ ] Difference between requirements.txt and requirements-lock.txt
- [ ] Essential packages for AI agents (LLMs, frameworks, DB)
- [ ] Docker best practices for Python
- [ ] How to update dependencies safely
- [ ] How to check for security vulnerabilities
- [ ] How to configure pyproject.toml

---

## ⚡ The Golden Rule

> **"Development is Flexible. Production is Frozen."**

### What This Means

```
Requirements.txt:        requirements-lock.txt:
langchain>=0.1.0         langchain==0.1.15
openai>=1.0.0            openai==1.3.7
fastapi>=0.109.0         fastapi==0.109.2

Dev: Uses latest patches  Prod: Exact versions only
Allows security updates   No surprises in production
```

### Why It Matters

**Without locking:**
```
Day 1:  uv pip install -r requirements.txt
        → Installs langchain 0.1.15

Day 30: New dev (same requirements.txt)
        → Installs langchain 0.1.28 (newer)
        → Breaks something
        → "But it worked for me!"
```

**With locking:**
```
requirements-lock.txt specifies EXACT versions
→ Every developer, every server gets identical setup
→ No surprises, no "works on my machine" issues
```

---

## 📁 File Structure

### Your Dependency Files

```
project-root/
├── requirements.txt           ← For development (flexible >=)
├── requirements-lock.txt      ← For production (exact ==)
├── pyproject.toml            ← Project metadata
├── .env.example              ← Environment variables
├── Dockerfile                ← Container definition
└── docker-compose.yml        ← Local dev setup
```

---

## 🛠️ The 2026 Standard: Using `uv`

### Why `uv` Over pip?

| Feature | pip | uv | Winner |
|---------|-----|----|----|
| **Speed** | Slow (Python) | ⚡ 10-100x faster (Rust) | uv |
| **Resolution** | Sometimes errors | Smart resolver | uv |
| **Lock file** | Requires pip-tools | Built-in | uv |
| **Standard tool** | Yes | New standard | pip (for now) |

**Recommendation:** Use `uv` locally, keep pip as fallback.

### Installation

```bash
# MacOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# Verify
uv --version  # Should show version
```

### Basic Workflow

```bash
# 1. Create virtual environment
uv venv

# 2. Activate it
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows

# 3. Install from flexible requirements
uv pip install -r requirements.txt

# 4. Generate lock file (exact versions)
uv pip compile requirements.txt -o requirements-lock.txt

# 5. Production: use locked versions
uv pip install -r requirements-lock.txt
```

---

## 📋 Essential Dependencies for 2026

### Core Application

```txt
# Web framework
fastapi>=0.104.0
uvicorn[standard]>=0.24.0

# Data validation
pydantic>=2.0.0
python-dotenv>=1.0.0

# Database
sqlalchemy>=2.0.0
psycopg2-binary>=2.9.0    # PostgreSQL

# Utilities
requests>=2.31.0
aiohttp>=3.9.0            # Async HTTP
```

### AI & LLM Providers (Choose 1-2)

```txt
# Anthropic (Claude)
anthropic>=0.18.0

# OpenAI
openai>=1.0.0

# Google
google-generativeai>=0.3.0

# Or: LangChain for abstraction
langchain>=0.1.0,<2.0
langchain-anthropic>=0.1.0
langchain-openai>=0.0.5
```

### Observability (Required for Production)

```txt
# OpenTelemetry - Distributed Tracing
opentelemetry-api>=1.20.0
opentelemetry-sdk>=1.20.0
opentelemetry-exporter-otlp>=1.20.0

# Framework instrumentation
opentelemetry-instrumentation-fastapi>=0.41b0
opentelemetry-instrumentation-requests>=0.41b0
opentelemetry-instrumentation-redis>=0.41b0
```

### Multi-Container & Async (If scaling)

```txt
# Task queue
redis>=5.0.0
celery>=5.3.0

# Async HTTP
httpx>=0.24.0
aiohttp>=3.9.0
```

### Security & Validation

```txt
# Input validation
pydantic>=2.0.0

# Secrets
python-dotenv>=1.0.0

# Authentication
pyjwt>=2.8.0
passlib>=1.7.4
```

### Development Only

```txt
# Testing
pytest>=7.0
pytest-cov>=4.0
pytest-asyncio>=0.21.0
pytest-mock>=3.12.0

# Linting & Formatting
ruff>=0.1.0
black>=23.0.0
isort>=5.12.0

# Type checking
mypy>=1.7.0
types-redis>=4.6.0
types-requests>=2.31.0

# Development tools
ipython>=8.17.0
watchdog>=3.0.0
```

---

## 📄 requirements.txt Template

### Development Setup

Create `requirements.txt` (flexible versions):

```txt
# Web Framework
fastapi>=0.104.0
uvicorn[standard]>=0.24.0

# LLM & AI
anthropic>=0.18.0
langchain>=0.1.0,<2.0
openai>=1.0.0

# Database
sqlalchemy>=2.0.0
psycopg2-binary>=2.9.0
redis>=5.0.0

# Data Validation
pydantic>=2.0.0
python-dotenv>=1.0.0

# Observability
opentelemetry-api>=1.20.0
opentelemetry-sdk>=1.20.0
opentelemetry-exporter-otlp>=1.20.0
opentelemetry-instrumentation-fastapi>=0.41b0

# Utilities
requests>=2.31.0
aiohttp>=3.9.0

# Testing (dev only)
pytest>=7.0
pytest-cov>=4.0
pytest-asyncio>=0.21.0

# Linting (dev only)
ruff>=0.1.0
black>=23.0.0
mypy>=1.7.0
```

### Generate Lock File

```bash
uv pip compile requirements.txt -o requirements-lock.txt
```

This creates `requirements-lock.txt` with exact pinned versions.

---

## 🐳 Docker Integration

### Multi-Stage Build (Optimized)

Create `Dockerfile`:

```dockerfile
# Stage 1: Builder
FROM python:3.11-slim AS builder

WORKDIR /app

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# Copy lock file (not requirements.txt - we want exact versions)
COPY requirements-lock.txt .

# Install packages in virtual environment
RUN uv venv && uv pip install -r requirements-lock.txt

# Stage 2: Runtime
FROM python:3.11-slim

WORKDIR /app

# Copy virtual environment from builder
COPY --from=builder /app/.venv /app/.venv

# Make sure we use the venv
ENV PATH="/app/.venv/bin:$PATH"

# Copy application code
COPY . .

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Build & Run

```bash
# Build image
docker build -t my-agent:latest .

# Run container
docker run -p 8000:8000 \
  -e OPENAI_API_KEY=$OPENAI_API_KEY \
  my-agent:latest
```

### Docker Compose (Local Development)

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@postgres:5432/agent
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    volumes:
      - .:/app  # Mount source for hot reload

  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=agent
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  postgres_data:
```

### Run Locally

```bash
docker-compose up
# Access at http://localhost:8000
```

---

## 🔧 pyproject.toml Configuration

### Modern Python Project Setup

Create `pyproject.toml`:

```toml
[project]
name = "my-ai-agent"
version = "1.0.0"
description = "Production AI Agent Framework"
requires-python = ">=3.11"

dependencies = [
    "fastapi>=0.104.0",
    "uvicorn[standard]>=0.24.0",
    "anthropic>=0.18.0",
    "sqlalchemy>=2.0.0",
    "psycopg2-binary>=2.9.0",
    "redis>=5.0.0",
    "pydantic>=2.0.0",
    "python-dotenv>=1.0.0",
    "opentelemetry-api>=1.20.0",
    "opentelemetry-sdk>=1.20.0",
    "opentelemetry-exporter-otlp>=1.20.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "pytest-cov>=4.0",
    "pytest-asyncio>=0.21.0",
    "ruff>=0.1.0",
    "black>=23.0.0",
    "mypy>=1.7.0",
]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
addopts = "-v --cov=app --cov-report=term-missing:skip-covered"

[tool.ruff]
line-length = 100
target-version = "py311"
select = ["E", "F", "W"]

[tool.black]
line-length = 100
target-version = ['py311']

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
```

---

## 📊 Version Pinning Strategy

### When to Use What

| Use Case | Example | Why |
|----------|---------|-----|
| **Flexible (dev)** | `langchain>=0.1.0` | Allows security patches |
| **Capped (dev)** | `langchain>=0.1.0,<2.0` | Blocks breaking changes |
| **Exact (prod)** | `langchain==0.1.15` | Reproducible, no surprises |
| **Range (rare)** | `langchain>=0.1.0,<0.2.0` | Very specific needs |

### Decision Tree

```
Is this for production?
├─ YES → Use == (exact version)
│       Example: langchain==0.1.15
│
└─ NO (development) → Use >= with optional cap
        ├─ Want latest patches? → >=1.0
        │   Example: fastapi>=0.104.0
        │
        └─ Want to block breaking? → >=1.0,<2.0
            Example: langchain>=0.1.0,<2.0
```

---

## 🔄 Workflow Examples

### Development Setup (First Time)

```bash
# 1. Create virtual environment
uv venv

# 2. Activate
source .venv/bin/activate

# 3. Install flexible versions
uv pip install -r requirements.txt

# 4. Generate lock file
uv pip compile requirements.txt -o requirements-lock.txt

# 5. Verify lock file
git add requirements-lock.txt
git commit -m "lock: Initial dependency lock"
```

### Production Deployment

```bash
# Use only lock file (no updates)
uv pip install -r requirements-lock.txt

# Everything is exact, reproducible
```

### Update Dependencies (Safely)

```bash
# 1. Update requirements.txt with new versions
vim requirements.txt
# Change: langchain>=0.1.0 → langchain>=0.2.0

# 2. Install locally (test first!)
uv pip install -r requirements.txt

# 3. Run tests (make sure nothing breaks)
pytest

# 4. If tests pass, generate new lock
uv pip compile requirements.txt -o requirements-lock.txt

# 5. Commit both files
git add requirements.txt requirements-lock.txt
git commit -m "deps: Update to langchain 0.2.0"
```

### Check for Security Issues

```bash
# Install pip-audit
pip install pip-audit

# Check for vulnerabilities
pip-audit

# Should show: "No known security vulnerabilities found"

# Or use safety
pip install safety
safety check
```

---

## 🤖 For Claude Code/Cursor (Explicit Instructions)

When Robert works with dependencies:

### Always Do

- ✅ Keep `requirements.txt` and `requirements-lock.txt` in sync
- ✅ Use `uv` (fast, reliable, modern standard)
- ✅ Test locally before updating lock file
- ✅ Commit lock file to Git (reproducibility)
- ✅ Check for security vulnerabilities

### When Robert Says "Update dependencies"

```
You should:
1. Ask: "Which packages? All or specific ones?"
2. Update requirements.txt (flexible versions)
3. Test: pytest
4. Generate: uv pip compile requirements.txt -o requirements-lock.txt
5. Suggest: "Commit both files together"
```

### When Docker Build Fails

```
Check:
1. Is Dockerfile using requirements-lock.txt? (Should be!)
2. Are lock files up-to-date?
3. Run: docker build --no-cache (force rebuild)
```

---

## 🐛 Troubleshooting

### Problem: "Package version conflict"

**Symptom:** `uv pip install` fails with version conflict

**Fix:**
```bash
# Check what's conflicting
uv pip install -r requirements.txt --dry-run

# Option 1: Loosen constraints in requirements.txt
vim requirements.txt
# Change: langchain>=0.1.0,<0.1.5
# To:     langchain>=0.1.0,<1.0

# Option 2: Downgrade conflicting package
uv pip install fastapi==0.100.0 langchain>=0.1.0
```

---

### Problem: "Works locally but fails in production"

**Symptom:** App works on dev, breaks on Docker/production

**Fix:**
1. Ensure Dockerfile uses `requirements-lock.txt` (not `requirements.txt`)
2. Make sure lock file is up-to-date
3. Use same Python version everywhere (3.11+)

---

### Problem: "New developer has different versions"

**Symptom:** "Works on my machine" syndrome

**Fix:**
1. Commit `requirements-lock.txt` to Git
2. Tell new dev: `uv pip install -r requirements-lock.txt`
3. Everyone gets EXACT same versions

---

### Problem: "pip vs uv - which should I use?"

**Answer:**
- **Locally:** Use `uv` (faster, better)
- **Production/CI:** `pip` works fine (it's in Docker anyway)
- **Best practice:** `uv` everywhere (but pip fallback OK)

---

## ✅ Checklist

### Before First Deployment

- [ ] `requirements.txt` with flexible versions (>=)
- [ ] `requirements-lock.txt` generated and committed
- [ ] Dockerfile uses `requirements-lock.txt`
- [ ] `python-dotenv` installed (for .env handling)
- [ ] Security audit passed: `pip-audit`
- [ ] Tests pass: `pytest --cov`

### For Every Dependency Change

- [ ] Update `requirements.txt` (flexible)
- [ ] Test locally: `pytest`
- [ ] Lock: `uv pip compile requirements.txt -o requirements-lock.txt`
- [ ] Commit both files
- [ ] Update `.claude-context.md`

### Docker Setup

- [ ] Multi-stage Dockerfile (builder + runtime)
- [ ] Uses `requirements-lock.txt` (exact versions)
- [ ] Python 3.11+ base image
- [ ] Environment variables via .env (not hardcoded)

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework  

**Next File:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (Terraform deployment)

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

**The golden rule: Dev is flexible, Prod is frozen.**  
**Master this, and you'll never have "works on my machine" problems again.** 📦