# 🧠 Build Context & Bug Tracking - AI Project Memory

**Version:** 1.6.0 | **Updated:** March 13, 2026 | **Part:** 6/10  
**Status:** Production Ready ✅  
**Purpose:** Give AI Coding Assistants project memory to prevent repeated bugs and architecture drift.

---

## 📍 Purpose

AI Coding Assistants (Cursor, Windsurf, Claude Code, Antigravity) have **zero memory between sessions**. 

To solve this, we use two files that the **AI is responsible for reading and updating**:
1. **`.build-context.md`** — Project state, recent changes, architecture, audit history.
2. **`.bugs_tracker.md`** — Active bugs, patterns, root causes, and deploy session logs.

**Core Rule:** The AI must manage its own memory through a strict "Read -> Act -> Update" loop (enforced via `agent.md`).

---

## 🗺️ Quick Navigation

- [The AI-First Bookkeeping Workflow](#-the-ai-first-bookkeeping-workflow)
- [File 1: .build-context.md Template](#-file-1-build-contextmd)
- [File 2: .bugs_tracker.md Template](#-file-2-bugs_trackermd)
- [Troubleshooting Memory Loss](#-troubleshooting)

---

## 🤖 The AI-First Bookkeeping Workflow

You do not need to manually update these files. You enforce that the AI does it.

### Step 1: Session Start (READ)
The AI reads `.build-context.md` automatically via the `agent.md` rules.
**You say:** "Let's continue building the Research Agent. Read project memory."

### Step 2: Coding (ACT)
The AI follows the "Tech Radar -> Factory" pipeline, writes the code, and runs tests.

### Step 3: Session End (UPDATE)
**You say:** "Great, that works. Update `.build-context.md` with what we built today and any new architectural decisions. If we hit any bugs, log them in `.bugs_tracker.md`."

---

## 📄 File 1: `.build-context.md`

### Template (Create this in your root folder)

```markdown
# AI Project Memory - [PROJECT NAME]

**Last Updated:** [DATE/TIME]  
**Project Phase:** [Discovery/Build/Test/Deploy/Maintain]  
**Risk Score:** [0-17]  

---

## 📍 Current State

### What I'm Working On Right Now
- **Feature:** [Current feature name]
- **Status:** [Started/In Progress/Testing/Blocked]
- **Blocker:** [What's blocking progress]

### Recent Changes (AI-UPDATED)
- **[DATE]:** Built [feature/component]
  - Files modified: `app/agents/researcher.py`
  - Architectural Decision: Decided to use LangGraph for orchestration.

---

## 🏗️ Project Structure
[Provide a brief tree of your /app, /infra, and /tests folders here]

---

## ⚙️ Configuration & Factories
- **LLM Engine:** [e.g., Anthropic via scale.yaml]
- **Orchestration:** [e.g., LangGraph]
- **Database:** [e.g., PostgreSQL]
- **Environment:** Hybrid MCP + Local

---

## 🛠️ Important Architectural Decisions
*(AI: Never delete these entries. Add to them when making technical choices.)*
*(AI: Audit findings that result in approved changes are architectural decisions. Log them here with the audit date and the rationale the human gave for approving.)*

**Decision:** Use hybrid tooling  
**Why:** DB queries need speed (local); file access needs safety (MCP)

**Decision:** Agent Orchestration  
**Why:** Decided to use LangGraph to manage cyclic agent routing instead of linear scripts.

---

## 🔍 Audit History
*(AI: Add an entry each time an audit is completed or an item is applied)*

**Audit [DATE]:**
  Completed: [DATE]
  Items approved: [N]
  Items deferred: [N]
  Items rejected: [N]
  Applied changes: [list]
  Test result: PASS / FAIL + rollback note if applicable
  Sign-off: [human name or initials]

**Between-audit CVE alerts:**
  [DATE]: [package] [CVE-ID] — [action taken]

---

## 🧩 Skills Registry
*(AI: Register every reusable skill here. Check this list before writing a new pattern — if a skill already exists, use it.)*

| Skill Name | Location | Trigger Condition | Created | Last Audited |
|------------|----------|-------------------|---------|--------------|
| [e.g., test-scaffold] | `/skills/test-scaffold/` | "Create a Pytest file with LLM-as-a-judge for any new agent" | [DATE] | [DATE] |
| [e.g., factory-generator] | `/skills/factory-generator/` | "Create interface + adapter + factory for a new external service" | [DATE] | [DATE] |
```

---

## 📄 File 2: `.bugs_tracker.md`

### Template (Create this in your root folder)

```markdown
# Bug Tracker & Pattern Memory

**Last Updated:** [DATE]  
**Active Bugs:** [COUNT]  

---

## 🚨 Active Bugs

### BUGS-001: MCP Connection Timeout
**Status:** Active | **Severity:** Major  
**Description:** Agent fails to connect to `mcp-filesystem` on initial startup.
**Fix Attempts:** Tried sleep(5). Didn't work reliably. Next step: Add Tenacity retry backoff.

---

## 🔍 Bug Patterns Identified (AI-UPDATED)
*(AI: Read this before writing new integration code to avoid repeating mistakes)*

### Pattern 1: Sidecar Race Conditions
**Occurrences:** 2 times (MCP filesystem + Database)  
**Root cause:** Containers starting before services are ready  
**Solution:** Always use exponential backoff decorators on connection logic.

### Pattern 2: API Rate Limiting
**Occurrences:** 1 time (External Search Tool)
**Root cause:** Parallel agent execution triggered 429 errors.
**Solution:** Implemented the Token Bucket algorithm in the `RateLimiter` middleware.

---

## 🚀 Deploy Session Log
*(AI: After every Deploy Error Protocol run, add an entry here. This is the memory of what was found, what was fixed, and what to watch for next time.)*

### DEPLOY-[DATE]: [brief description e.g. "prod crash on migration"]

**Trigger:** [What caused the scan — deploy failure / pre-deploy scan / health check]  
**Environment:** [staging / prod]

**Issue Map (what was found):**
| ID | Layer | Description | Severity | Root cause or symptom? |
|----|-------|-------------|----------|------------------------|
| C1 | Config | DATABASE_URL missing in prod | BLOCKING | Root cause |
| M1 | Migration | Migration 004 failed — no DB connection | BLOCKING | Symptom of C1 |

**Fix order applied:** C1 → M1  
**Human approved:** [YES / NO — if NO, explain why it was skipped]

**Files touched this session:**
- `config/.env.prod`
- `alembic/versions/004_add_sessions_table.py`

**Skeptic check result:** PASS ✅ / FAIL ❌  
**Regressions found:** [None / describe if any]  
**Test suite result:** [PASS / FAIL]

**Recurring pattern?**
*(AI: If this is the 2nd+ time this type of issue appeared in deploy scans, flag it here.)*
- [ ] Yes — consider automating this check in CI (see `agent.md` Phase 5: Recognize & Propose Skills)
- [ ] No — first occurrence

**Notes for next session:**
[Anything the next session should know — e.g., "Layer 1 scan will show a warning about REDIS_URL until ticket #42 is resolved — expected, not blocking"]
```

---

## 📌 File Meta

**Version:** 1.6.0  
**Released:** March 13, 2026  
**Status:** Production Ready ✅  
**Part of:** 10-Part AI Agent Framework  

**Next File:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (Deployment)
