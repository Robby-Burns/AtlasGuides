# 🎯 Quick Reference - AI Agent Development Framework

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 2/8  
**Status:** Production Ready ✅  
**Purpose:** Fast lookup for formulas, checklists, matrices, and decision trees

---

## 📍 Purpose

This file is your **reference desk** for quick answers. No long explanations, just:
- **7-step process** (memorize this)
- **Risk scoring formula** (0-17 scale)
- **Guardrails by risk level** (what to enable)
- **Deployment matrix** (where to run it)
- **Essential checklists** (what to verify)
- **Easy swapping patterns** (code examples)

**When to use:** During design decisions, implementation, deployment. Pinned in your browser.

---

## 🗺️ Quick Navigation

- [Super Quick Start](#-super-quick-start-5-min)
- [The 7-Step Process](#-the-7-step-process-memorize-this)
- [Risk Scoring Formula](#-risk-scoring-formula-0-17-scale)
- [Auto-Enabled Guardrails](#-auto-enabled-guardrails-by-risk-level)
- [Deployment Platform Matrix](#-deployment-platform-matrix-february-2026)
- [Architecture Decision Matrix](#-architecture-decision-matrix)
- [Easy Swapping Patterns](#-easy-swapping-patterns-no-rewrites)
- [Testing Requirements](#-testing-requirements)
- [Deployment Checklist](#-deployment-checklist)
- [Troubleshooting Quick Fixes](#-troubleshooting-quick-fixes)
- [Success Indicators](#-success-indicators)

---

## 🔗 Related Files

**Before this:** [00_START_HERE.md](./00_START_HERE.md) (Choose your path)  
**After this:** [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) (Deep dive on any section)  
**For Claude setup:** [04_AI_ASSISTANT_INTEGRATION.md](./04_AI_ASSISTANT_INTEGRATION.md)  
**For daily workflow:** [05_CLAUDE_CONTEXT_AND_BUGS.md](./05_CLAUDE_CONTEXT_AND_BUGS.md)

---

## ⚡ Super Quick Start (5 min)

```bash
1. Calculate your risk score (see formula below)
2. Choose your guardrails (see table below)
3. Choose your platform (see matrix below)
4. Reference this file when making decisions
5. Read 00_START_HERE.md for context
```

---

## 🎯 The 7-Step Process (Memorize This)

**Use this sequence for EVERY project:**

```
┌─────────────────────────────────────────────┐
│ Step 1: DISCOVERY                           │
├─────────────────────────────────────────────┤
│ □ What problem are we solving?              │
│ □ Who uses it? How?                         │
│ □ What does success look like?              │
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 2: RISK SCORING (0-17)                 │
├─────────────────────────────────────────────┤
│ □ Input Risk (0-5): How dangerous is input? │
│ □ Output Risk (0-5): Impact of wrong result?│
│ □ Data Sensitivity (0-4): PII/Financial?    │
│ □ Model Risk (0-3): LLM autonomy?           │
│ □ TOTAL = Sum (determines everything next) │
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 3: GUARDRAILS                          │
├─────────────────────────────────────────────┤
│ □ 0-4 (LOW): Basic validation               │
│ □ 5-10 (MEDIUM): + Rate limiting            │
│ □ 11-17 (HIGH): + Human approval            │
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 4: ARCHITECTURE                        │
├─────────────────────────────────────────────┤
│ □ <1K users: Monolith                       │
│ □ 1K-10K: Modular monolith                  │
│ □ 10K+: Multi-container microservices       │
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 5: TOOLING STRATEGY                    │
├─────────────────────────────────────────────┤
│ □ Internal: Local Adapters (fast)           │
│ □ External: MCP Bridges (safe)              │
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 6: IMPLEMENTATION                      │
├─────────────────────────────────────────────┤
│ □ Phase 1: Core + Unit tests (60%)          │
│ □ Phase 2: Integration + Security (20%)     │
│ □ Phase 3: Observability + OpenTelemetry (20%)│
└─────────────────────────────────────────────┘
           ↓
┌─────────────────────────────────────────────┐
│ Step 7: DEPLOY & MONITOR                    │
├─────────────────────────────────────────────┤
│ □ Infrastructure (Terraform)                │
│ □ CI/CD Pipeline                            │
│ □ Observability (OTEL traces)               │
│ □ Iterate based on data                     │
└─────────────────────────────────────────────┘
```

---

## 📊 Risk Scoring Formula (0-17 Scale)

### Calculate Your Score

```
TOTAL RISK = Input Risk + Output Risk + Data Sensitivity + Model Risk

Min: 0 (no risk)
Max: 17 (extreme risk)
```

### Input Risk (0-5)

| Score | Scenario | Example |
|-------|----------|---------|
| **0** | No input | Batch jobs, read-only systems |
| **1** | Dropdown selection | Predefined categories |
| **2** | Structured text | Forms with validation |
| **3** | Open-ended input | User questions, feedback |
| **4** | Code/commands | Script generation, shell access |
| **5** | Direct API/DB access | Raw SQL queries, unfiltered database calls |

### Output Risk (0-5)

| Score | Scenario | Example |
|-------|----------|---------|
| **0** | Read-only | Logs, dashboards, reports |
| **1** | Informational | Summaries, explanations |
| **2** | Recommendations | Suggestions, rankings |
| **3** | Decisions | Status changes, approvals |
| **4** | Automated actions | Data updates, API calls |
| **5** | Critical | Financial transactions, medical decisions |

### Data Sensitivity (0-4)

| Score | Data Type | Examples |
|-------|-----------|----------|
| **0** | Public | News, Wikipedia, public repos |
| **1** | Internal | Company docs, internal wikis |
| **2** | PII | Names, emails, addresses |
| **3** | Sensitive PII | Phone, SSN, ID numbers |
| **4** | Extreme | Medical records, financial accounts |

### Model Risk (0-3) — NEW in v1.2.0

| Score | LLM Type | Autonomy Level |
|-------|----------|----------------|
| **0** | None | No LLM, deterministic only |
| **1** | Constrained | Classification, simple generation |
| **2** | Complex | Reasoning chains, code generation |
| **3** | Autonomous | Uses tools, takes actions, multi-step |

### Risk Score Thresholds

```
0-4:   LOW      → Basic guardrails
5-10:  MEDIUM   → Standard guardrails
11-17: HIGH     → Comprehensive guardrails + isolation
```

### Example Calculations

**Example 1: Research Agent**
```
Input:     3 (user questions)
Output:    2 (summaries)
Data:      0 (public sources)
Model:     2 (reasoning)
───────────────────
TOTAL: 7 (MEDIUM) ← Rate limit, injection checks
```

**Example 2: Autonomous Code Generator**
```
Input:     4 (open specs)
Output:    4 (production code)
Data:      1 (internal examples)
Model:     3 (autonomous, tools)
───────────────────
TOTAL: 12 (HIGH) ← Human review, audit, microVM
```

**Example 3: Customer Support Bot**
```
Input:     4 (open customer input)
Output:    3 (affects customers)
Data:      2 (customer data)
Model:     1 (constrained)
───────────────────
TOTAL: 10 (MEDIUM) ← Standard guardrails
```

---

## 🛡️ Auto-Enabled Guardrails by Risk Level

### LOW Risk (0-4)

```
✓ Input validation (basic)
✓ Output validation (basic)
✓ Logging (info level)
✓ Error handling
```

**Setup:** 30 minutes (just add basic checks)

---

### MEDIUM Risk (5-10)

```
✓ Everything from LOW, plus:
✓ Prompt injection detection (check with separate LLM call)
✓ Content filtering (output PII scan)
✓ Rate limiting (per user/IP)
✓ Audit logging (who, what, when)
✓ Input sanitization
```

**Setup:** 4-8 hours (add security layer)

---

### HIGH Risk (11-17)

```
✓ Everything from MEDIUM, plus:
✓ Human approval for critical actions
✓ Comprehensive audit logging (immutable)
✓ Encryption (at rest + in transit)
✓ Multi-factor verification
✓ Rollback capabilities
✓ MicroVM isolation (Kata Containers/gVisor)
✓ Distributed tracing (OpenTelemetry)
✓ Cost tracking per request
✓ Real-time alerting
```

**Setup:** 2-4 weeks (enterprise-grade)

---

## 🌍 Deployment Platform Matrix (February 2026)

### Decision Guide

| Platform | Best For | Resume Value | Cost | Setup Time | Tier |
|----------|----------|--------------|------|-----------|------|
| **Railway** | Ship MVP this week | ⭐⭐ | $5-50/mo | 30 min | Option A |
| **Google Cloud Run** | Get hired at startups | ⭐⭐⭐⭐⭐ | $20-200/mo | 1 hour | Option B |
| **Azure Container Apps** | Get hired at enterprise | ⭐⭐⭐⭐⭐ | $50-300/mo | 2 hours | Option B |
| **Fly.io** | Global latency + real-time | ⭐⭐⭐ | $20-150/mo | 1 hour | Option A/B |
| **Northflank** | Maximum security (microVM) | ⭐⭐⭐⭐ | $100-500/mo | 3 hours | Option B |

### Decision Rules

- **Speed:** Railway (Option A)
- **Career:** Azure/GCP + Terraform (Option B)
- **Security:** Northflank (Option B)
- **Global:** Fly.io (Option A/B)

---

## 🏗️ Architecture Decision Matrix

### By Scale

| Scale | Users | Pattern | Setup | Cost | Complexity |
|-------|-------|---------|-------|------|------------|
| **Tiny** | <100 | Single Lambda + DB | 1 hour | <$10/mo | ⭐ |
| **Small** | 100-1K | Monolith + DB | 4 hours | $20-50/mo | ⭐⭐ |
| **Medium** | 1K-10K | Modular monolith + cache | 1 day | $50-200/mo | ⭐⭐⭐ |
| **Large** | 10K+ | Multi-container + orchestration | 1 week | $500+/mo | ⭐⭐⭐⭐⭐ |

### By Problem Type

| Problem | Architecture | Tools | Risk |
|---------|--------------|-------|------|
| Research/Analysis | Monolith | Local adapters only | LOW-MEDIUM |
| Data Processing | Monolith or Modular | Local DB, cache | MEDIUM |
| Real-time Chat | Monolith | WebSockets, local state | MEDIUM |
| Code Generation | Monolith or Multi-container | Sandbox isolation, MCP | HIGH |
| Multi-step Workflows | Modular monolith | Worker queue, state machine | MEDIUM-HIGH |

---

## 🛠️ Easy Swapping Patterns (No Rewrites!)

### Pattern 1: Swap LLM Models (1 line)

```python
# config/llm.py
llm = ChatOpenAI(model="gpt-4")              # OpenAI
llm = ChatAnthropic(model="claude-3-opus")   # Claude
llm = ChatGoogle(model="gemini-pro")         # Google
```

---

### Pattern 2: Swap Tools (Hybrid Pattern)

```python
# app/factory.py
def get_database():
    if os.getenv("USE_MCP") == "true":
        return MCPDatabaseAdapter()     # Sandboxed, safe
    else:
        return LocalPostgresAdapter()   # Fast, local
```

---

### Pattern 3: Swap Databases (1 env var)

```bash
# .env
DATABASE_TYPE=postgresql    # PostgreSQL
DATABASE_TYPE=qdrant        # Vector DB
DATABASE_TYPE=sqlite        # Local dev
```

---

### Pattern 4: Swap Workers (Local → Distributed)

```python
# config/scale.yaml
workers:
  enabled: false            # Run locally
  # Change to: true         # Run distributed (Celery)
```

---

## 🧪 Testing Requirements

### Coverage Targets

```
Unit Tests:        60-70%  (isolated, all mocked)
Integration Tests: 20-30%  (real components, mocked externals)
E2E Tests:         5-10%   (everything real)

TARGET: 80%+ total coverage
```

### Must Include

- ✓ Security tests (injection, auth, PII)
- ✓ Integration tests (components work together)
- ✓ Tooling tests (verify MCP/Local switching)
- ✓ Performance tests (guardrails don't choke)
- ✓ Error handling (graceful degradation)

### Command Reference

```bash
# Run all tests
pytest

# With coverage
pytest --cov=app --cov-report=html

# Watch mode (auto-rerun on change)
pytest-watch

# Only integration tests
pytest tests/integration/
```

---

## 🚀 Deployment Checklist

**Before going to production:**

- [ ] **Tests:** 80%+ coverage (Unit + Integration)
- [ ] **Infra:** Defined in Terraform ([06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md))
- [ ] **Tools:** MCP sidecars configured (if using)
- [ ] **Observability:** OpenTelemetry tracing enabled
- [ ] **Context:** `.claude-context.md` up to date
- [ ] **Security:** All guardrails enabled per risk score
- [ ] **Secrets:** Using environment variables, not hardcoded
- [ ] **Database:** Migrations tested, rollback plan
- [ ] **Monitoring:** Alerts configured for failures
- [ ] **Documentation:** README + runbook for ops team

---

## 🔍 Troubleshooting Quick Fixes

| Problem | Solution | Reference |
|---------|----------|-----------|
| **Connecting external tools** | Use MCP Bridge (hybrid pattern) | See "Easy Swapping" above |
| **Version conflicts** | Use flexible versions (>=) | [03_DEPENDENCY_MANAGEMENT.md](./03_DEPENDENCY_MANAGEMENT.md) |
| **Claude losing context** | Update `.claude-context.md` daily | [05_CLAUDE_CONTEXT_AND_BUGS.md](./05_CLAUDE_CONTEXT_AND_BUGS.md) |
| **Deployment errors** | Check Terraform state | [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) |
| **Agent stuck in loop** | Check OpenTelemetry traces | [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) Section 5.6 |
| **Cost growing too fast** | Adjust `scale.yaml` settings | [07_CONFIGURATION_CONTROL.md](./07_CONFIGURATION_CONTROL.md) |
| **Bugs repeating** | Track in `.bugs_tracker.md` | [05_CLAUDE_CONTEXT_AND_BUGS.md](./05_CLAUDE_CONTEXT_AND_BUGS.md) |

---

## ✅ Success Indicators

You're using this framework correctly when:

✅ Every project starts with 7-step process (Discovery → Deploy)

✅ Risk scores calculated BEFORE architecture decisions

✅ `.claude-context.md` updated after every session

✅ Guardrails enabled appropriate to risk level

✅ Infrastructure defined in Terraform (never manual clicks)

✅ Tests at 80%+ coverage from day 1

✅ Observability (OTEL) included in production agents

✅ Claude Code/Cursor asks about risk score first

✅ Deployment platform chosen from matrix (not random)

---

## 🤖 For Claude Code/Cursor (Explicit Instructions)

When Robert asks "Let's build [thing]":

### Always Do First

1. **Ask:** "What's your risk score? (0-17)"
   - Help calculate using Risk Scoring Formula above
   - This determines EVERYTHING that follows

2. **Ask:** "What platform?" 
   - Reference Platform Matrix above
   - Default: Railway (MVP) or Azure/GCP (Production)

3. **Ask:** "Architecture?"
   - Reference Architecture Decision Matrix above
   - Match to scale + problem type

### Always Include

- ✅ Risk score in analysis (drives guardrails)
- ✅ Appropriate guardrails (from table above)
- ✅ Test coverage strategy (80%+ target)
- ✅ Infrastructure reference ([06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md))

### When You See Patterns

- Code swapping needed? → Show "Easy Swapping" patterns
- Performance issues? → Suggest deployment from matrix
- Test coverage low? → Reference Testing Requirements above
- Unsure about architecture? → Use Architecture Decision Matrix

---

## 📋 Quick Reference Cards

### Print These & Pin Them

**Card 1: Risk Scoring (Pocket Size)**
```
RISK = Input (0-5) + Output (0-5) + Data (0-4) + Model (0-3)
0-4:   LOW      (Basic validation)
5-10:  MEDIUM   (+ Rate limiting)
11-17: HIGH     (+ Human approval)
```

**Card 2: 7-Step Process**
```
1. DISCOVERY (What problem?)
2. RISK SCORING (0-17)
3. GUARDRAILS (What to enable?)
4. ARCHITECTURE (Monolith? Multi?)
5. TOOLING (Local vs MCP?)
6. IMPLEMENTATION (Build + test)
7. DEPLOY & MONITOR (Terraform + OTEL)
```

**Card 3: Platform Picker**
```
Ship fast? → Railway
Get hired? → GCP/Azure
Secure? → Northflank
Global? → Fly.io
```

---

## 🔄 When to Reference This File

- Before architecture decisions (use matrices)
- During implementation (use formulas)
- Before testing (check coverage targets)
- Before deploying (use deployment checklist)
- When debugging (use troubleshooting table)
- When Claude asks "What should we do?" (use 7-step process)

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework  

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

**Use this. Reference it daily. The best tools are the ones you actually use.** 🎯