# 🧠 Claude Context & Bug Tracking - Project Memory System

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 6/8  
**Status:** Production Ready ✅  
**Purpose:** Give Claude Code/Cursor project memory to prevent 80% of bugs

---

## 📍 Purpose

This file teaches you to use **two files** that give Claude Code/Cursor project memory:

1. **`.claude-context.md`** — Project state, recent changes, architecture
2. **`.bugs_tracker.md`** — Active bugs, patterns, root causes

**Without these:** Claude forgets context, asks same questions, repeats bugs  
**With these:** Claude remembers everything, continues from where you left off, prevents mistakes

**When to use:** Start of every project, update daily.

---

## 🗺️ Quick Navigation

- [The Problem](#-the-problem-claude-has-no-memory)
- [The Solution](#-the-solution-two-files)
- [File 1: .claude-context.md](#-file-1-claude-contextmd)
- [File 2: .bugs_tracker.md](#-file-2-bugs_trackermd)
- [Automation: update_context.py](#-automation-python-script)
- [Daily Workflow](#-daily-workflow)
- [Troubleshooting](#-troubleshooting)

---

## 🔗 Related Files

**Before this:** [04_AI_ASSISTANT_INTEGRATION.md](./04_AI_ASSISTANT_INTEGRATION.md) (Claude setup)  
**This file:** [05_CLAUDE_CONTEXT_AND_BUGS.md](./05_CLAUDE_CONTEXT_AND_BUGS.md) (You are here)  
**After this:** [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) (Deep methodology)  
**Reference:** [01_QUICK_REFERENCE.md](./01_QUICK_REFERENCE.md) (Formulas)

---

## ✅ What You'll Learn

- [ ] Why project memory matters
- [ ] How to create `.claude-context.md` template
- [ ] How to create `.bugs_tracker.md` template
- [ ] How to automate context updates with Python script
- [ ] Daily workflow for keeping context updated
- [ ] How to prevent bugs from repeating

---

## 🚨 The Problem: Claude Has No Memory

Claude Code/Cursor has **zero memory between sessions**:

- ❌ Forgets what you built yesterday
- ❌ Doesn't understand current project state
- ❌ Can't track why bugs happened before
- ❌ Repeats same mistakes week after week
- ❌ Loses architectural context (e.g., "Did we use MCP bridges here?")
- ❌ Asks same setup questions repeatedly

**Impact:** 
- +40% development time (repeating work)
- +60% bugs (no pattern recognition)
- +30% frustration (context loss)

---

## ✅ The Solution: Two Files

**File 1: `.claude-context.md`**
- **What:** Project state, recent changes, architecture decisions
- **When:** Updated after major work sessions
- **Who updates:** You manually + automated script
- **Claude reads:** At start of every session

**File 2: `.bugs_tracker.md`**
- **What:** Active bugs, root causes, patterns, blockers
- **When:** Updated when bugs are found
- **Who updates:** You manually
- **Claude reads:** When troubleshooting

**Together they prevent:**
- ✓ Context loss between sessions
- ✓ Repeated mistakes (bug patterns visible)
- ✓ Architectural drift (decisions documented)
- ✓ Lost knowledge (reasoning in comments)

---

## 📄 File 1: `.claude-context.md`

### Purpose

Tracks your project state so Claude understands:
- What was built recently
- Current file structure
- Active features being developed
- Architectural decisions
- Known issues and workarounds

### Template (Copy This)

```markdown
# Claude Context - [PROJECT NAME]

**Last Updated:** [DATE/TIME]  
**Project Phase:** [Discovery/Build/Test/Deploy/Maintain]  
**Current Sprint:** [What you're working on this week]  
**Risk Score:** [0-17, from 01_QUICK_REFERENCE.md]  

---

## 📍 Current State

### What I'm Working On Right Now

- **Feature:** [Current feature name]
- **Files modified:** [List files: app/agents/foo.py, infra/main.tf, etc]
- **Status:** [Started/In Progress/Testing/Blocked]
- **Blocker (if any):** [What's blocking progress]
- **Expected completion:** [When?]

### What Was Built in Last Session

- **[DATE]:** Built [feature/component]
  - Files: `app/agents/researcher.py`, `app/adapters/mcp_bridge.py`
  - What works: [Specific accomplishment]
  - Known issue: [Any gotchas?] (Link to `.bugs_tracker.md` if tracked)

- **[PREVIOUS DATE]:** Built [other feature]
  - Files: [affected files]
  - What works: [Result]

---

## 🏗️ Project Structure

### Current Architecture

```
your-project/
├── /docs/                    # Framework docs (read first)
│   ├── 00_START_HERE.md
│   ├── 01_QUICK_REFERENCE.md
│   └── ... (all 8 files)
│
├── /src/app/                 # Application code
│   ├── /agents/              # Agent implementations
│   │   ├── researcher.py     # WORKING: Takes queries, searches web
│   │   └── writer.py         # IN PROGRESS: Drafts content
│   │
│   ├── /adapters/            # Hybrid Tooling (Local vs MCP)
│   │   ├── local_db.py       # FAST: Direct SQL via SQLAlchemy
│   │   ├── mcp_bridge.py     # SAFE: Sandboxed MCP connections
│   │   └── interfaces/
│   │       └── database.py   # Abstract base class
│   │
│   ├── /guardrails/          # Security (risk-based)
│   │   ├── injection_check.py
│   │   ├── output_validation.py
│   │   └── audit_log.py
│   │
│   └── main.py               # Entry point
│
├── /infra/                   # Infrastructure as Code
│   ├── /modules/
│   │   ├── agent_service/    # Container app
│   │   ├── database/         # Postgres/Redis
│   │   └── monitoring/       # Jaeger/OTEL
│   └── main.tf              # Terraform root module
│
├── /tests/                   # Test suite
│   ├── test_researcher.py   # 85% coverage ✓
│   ├── test_guardrails.py   # 70% coverage
│   └── ... (more tests)
│
├── .claude-context.md        # 👈 THIS FILE (you are here)
├── .bugs_tracker.md          # 🐛 Bug tracking file
├── requirements.txt          # Flexible versions (>=)
├── requirements-lock.txt     # Exact versions (==)
├── docker-compose.yml        # Local dev + sidecars
├── .env.example              # Environment template
└── README.md                 # Project overview
```

### Key Files & Their Status

| File | Status | Last Modified | Notes |
|------|--------|---------------|-------|
| `app/agents/researcher.py` | ✅ Working | [DATE] | Uses MCP bridge for web search |
| `app/adapters/mcp_bridge.py` | 🔧 Testing | [DATE] | Connection retries added |
| `infra/main.tf` | ⏳ Pending | [DATE] | Needs Azure credentials |
| `tests/test_researcher.py` | ✅ Working | [DATE] | 85% coverage, all passing |

---

## ⚙️ Configuration & Dependencies

### Current LLM Setup

```python
# config/llm.py
MODEL = "gpt-4"          # OpenAI (can swap to Claude: see 08_AGNOSTIC_FACTORIES.md)
TIMEOUT = 60             # seconds
```

Can be swapped to Claude with 1-line change (see 08_AGNOSTIC_FACTORIES.md).

### Environment Variables Required

```bash
# .env (keep out of Git)
OPENAI_API_KEY=sk-...           # ✅ Set
DATABASE_URL=postgresql://...   # ✅ Set
USE_MCP=true                    # ✅ Set (Hybrid mode)
MCP_FILESYSTEM_URL=http://...   # ⚠️ NOT SET YET
```

### Infrastructure State

- **Local:** Docker Compose running ✅
- **Production:** Terraform plan created ⏳ (waiting for approval)

---

## 🎯 Active Features & Status

### In Progress

- [ ] **MCP Bridge for GitHub** (40% done)
  - ✅ Interface defined
  - ✅ Local adapter working
  - ⏳ Needs: MCP client implementation
  - Blocked by: Need MCP SDK docs

### Completed

- [x] **Researcher agent** (100%)
  - Works end-to-end
  - Has 85% test coverage
  - Uses local adapters for speed

- [x] **Database adapter pattern** (100%)
  - Can swap SQL ↔ Vector DB with env var
  - See 08_AGNOSTIC_FACTORIES.md

- [x] **Infrastructure basics** (100%)
  - Docker Compose set up
  - Terraform structure created

---

## 🛠️ Important Architectural Decisions

### Why We Use Hybrid Tooling (Local + MCP)

**Decision:** Use local adapters for speed, MCP for safety  
**Date:** [DATE]  
**Why:** DB queries need millisecond latency (local); file access needs sandboxing (MCP)

```python
def get_file_access():
    if os.getenv("USE_MCP"):
        return MCPFilesystemAdapter()  # Sandboxed
    else:
        return LocalFilesystemAdapter()  # Fast but less safe
```

**Impact:** `app/factory.py` switches implementation based on env var

**Tradeoff:** MCP adds ~50ms latency but prevents file escapes

---

### Why Infrastructure as Code

**Decision:** Terraform for all infrastructure, no cloud console clicks  
**Date:** [DATE]  
**Why:** Need reproducible, versionable, auditable infrastructure

**Impact:** All infra changes go through `/infra/main.tf`  
**Tradeoff:** Learning curve on Terraform, but prevents drift

---

### Why Hybrid Observability

**Decision:** Console logging locally, Jaeger in production  
**Date:** [DATE]  
**Why:** Risk score is 10 (MEDIUM), so observability required

**Impact:** Add OpenTelemetry deps, Jaeger service in docker-compose  
**Tradeoff:** Extra 50ms per request for tracing, worth it for debugging

---

## 🔗 Integration Points

### Current External APIs & Sidecars

```
OpenAI API
├─ Used by: All agents for LLM inference
├─ Rate limit: 10K tokens/min
└─ Cost: ~$0.15/M tokens (with Haiku)

MCP Filesystem Server (Sidecar)
├─ Used by: Researcher agent (file search)
├─ URL: http://mcp-filesystem:8000
├─ Status: Running in Docker Compose ✅
└─ Latency: ~50ms per call

PostgreSQL Database
├─ Used by: All agents for persistence
├─ Connection string: See .env
├─ Status: Running locally ✅
└─ Backups: Manual (TODO: automate)
```

---

## 🧪 Testing & Coverage

### Current Test Coverage

```
Overall:        78%  (Target: 80%+)
  /agents:      85%  (Good! 🟢)
  /adapters:    70%  (Needs work 🟡)
  /guardrails:  65%  (Needs work 🟡)
  /infra:       N/A  (Validate via: terraform plan)
```

### Running Tests

```bash
# Full suite
pytest

# With coverage report
pytest --cov=app --cov-report=html

# Specific module
pytest tests/test_researcher.py -v

# Watch mode (auto-rerun)
pytest-watch
```

---

## 🔐 Security & Guardrails

### Implemented (Risk: 10 = MEDIUM)

- ✅ Input validation (basic)
- ✅ Prompt injection detection (separate LLM call)
- ✅ Output validation (no PII leaks)
- ✅ Rate limiting (Redis, 10 req/min per user)
- ✅ Audit logging (file-based)
- ⚠️ MCP sandboxing (Filesystem restricted to /data)

### TODO (Before Production)

- [ ] Encryption at rest (DB)
- [ ] Multi-factor verification
- [ ] Cost tracking per request
- [ ] Real-time alerting

---

## 📌 Known Issues & Workarounds

### ISSUE-1: MCP Sidecar Connection Timeout

**Impact:** High  
**Status:** Active  
**Workaround:** Restart docker-compose if agent fails on startup  
**Permanent fix:** Add healthcheck to docker-compose services  
**Related bug:** See `.bugs_tracker.md` BUGS-001  

---

### ISSUE-2: Terraform State Lock

**Impact:** Medium  
**Status:** Active  
**Workaround:** `terraform force-unlock [LOCK_ID]`  
**Permanent fix:** Use remote state backend (S3/Azure Blob)  
**When fixed:** Will allow parallel CI/CD runs  

---

---

## 📄 File 2: `.bugs_tracker.md`

### Purpose

Tracks all bugs to:
- Prevent the same bug from happening twice
- Identify patterns (e.g., "MCP connections always timeout at startup")
- Track resolution progress
- Learn from mistakes

### Template (Copy This)

```markdown
# Bug Tracker - [PROJECT NAME]

**Last Updated:** [DATE]  
**Active Bugs:** [COUNT]  
**Resolved This Week:** [COUNT]  

---

## 🚨 Active Bugs (PRIORITY ORDER)

### BUGS-001: MCP Connection Timeout

**Status:** Active  
**Priority:** High  
**Severity:** Major (Agent can't function)  
**Found:** [DATE]  
**Affects:** `app/adapters/mcp_bridge.py`  

**Description:**
Agent fails to connect to `mcp-filesystem` sidecar on initial startup.
Error appears 30 seconds after agent starts, before any requests come in.

**Error Message:**
```
ConnectionRefusedError: [Errno 111] Connection refused
Context: Trying to connect to http://mcp-filesystem:8000
```

**Root Cause Analysis:**
1. Agent container starts faster than MCP sidecar container
2. No retry logic in `mcp_bridge.py`
3. Docker doesn't wait for service readiness, just container startup
4. Agent tries to initialize MCP connection at import time (bad pattern)

**Why It Matters:**
- Blocks all agent operations
- Makes local development frustrating (have to restart)
- Would fail in production (no manual restarts)

**Fix Attempts:**
1. **[DATE] Tried:** Adding `depends_on` in docker-compose
   - **Result:** Failed. Docker waits for container start, not app readiness
   - **Learning:** Need application-level retry, not orchestration-level

2. **[DATE] Tried:** Adding sleep(5) before connection
   - **Result:** Works sometimes, but fragile
   - **Learning:** Hard-coded waits are bad, need exponential backoff

**Next Steps:**
- [ ] Implement `tenacity` retry decorator on connection
- [ ] Add Docker healthcheck for MCP service
- [ ] Move MCP connection from import-time to first-use
- [ ] Add logging to understand timing

**Related Code:**
```python
# app/adapters/mcp_bridge.py (line 15)
# TODO: Add @retry decorator with exponential backoff
```

---

## ✅ Resolved Bugs

### BUGS-R001: Terraform Azure Provider Version Mismatch

**Status:** Resolved  
**Fixed:** [DATE]  

**Problem:**
`terraform plan` failed with "Provider version incompatibility"
Error: "azurerm provider requires version 3.70.0, installed 3.60.0"

**Root Cause:**
Provider version wasn't pinned in Terraform code. Different machines had different versions installed.

**Solution:**
Locked provider version in `infra/versions.tf`:
```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.90.0"  # ← Lock exact version
    }
  }
}
```

**Learning:**
Always pin exact versions for infrastructure providers. Environment-specific differences lead to "works on my machine" problems.

---

## 🔍 Bug Patterns Identified

### Pattern 1: Sidecar Race Conditions

**Occurrences:** 2 times (MCP filesystem + Database)  
**Root cause:** Containers starting before services are ready  
**Solution:** Add Docker healthchecks + exponential backoff in code  
**Prevention:** Test startup sequence before committing infrastructure changes  

### Pattern 2: Manual Infrastructure Drift

**Occurrences:** 1 time (Azure Portal changes)  
**Root cause:** Someone clicked Azure Portal instead of using Terraform  
**Solution:** NEVER change resources manually. Always use `terraform apply`  
**Prevention:** Lock down cloud IAM permissions (read-only for console, full for Terraform)  

### Pattern 3: Repeated Test Failures

**Occurrences:** Multiple (async test timing)  
**Root cause:** Tests depend on exact timing, not state  
**Solution:** Use explicit waits, not `time.sleep()`  
**Prevention:** Use pytest fixtures with proper async setup  

---

## 🧬 Bug Prevention Checklist

Before committing code:

- [ ] Does this integrate with external services? → Add retry logic
- [ ] Does this require timing? → Use explicit waits, not sleep()
- [ ] Does this change infrastructure? → Update Terraform, commit code
- [ ] Is this a known pattern bug? → Check patterns above first
- [ ] Would Claude make this mistake? → Add to `.claude-context.md`

---
```

---

## ⚙️ Automation: Python Script

### Why Automate?

Updating `.claude-context.md` manually is tedious. A script can:
- Auto-detect which files changed
- Generate recent changes section
- Remind you to add manual notes

**Time saved:** 5 minutes per session × 250 sessions/year = 20+ hours/year

---

### Script: `scripts/update_context.py`

Create a file `scripts/update_context.py`:

```python
#!/usr/bin/env python3
"""
Context Automation Script v1.2.0
Analyzes git diff and updates .claude-context.md automatically.

Usage:
  python scripts/update_context.py          # Show preview
  python scripts/update_context.py --apply  # Apply changes
"""

import subprocess
import datetime
import sys
from pathlib import Path

CONTEXT_FILE = ".claude-context.md"
DOCS_FOLDER = "docs"

def get_git_diff():
    """Get list of changed files since last commit."""
    try:
        result = subprocess.run(
            ['git', 'status', '--porcelain'],
            capture_output=True,
            text=True,
            check=True
        )
        return result.stdout
    except subprocess.CalledProcessError:
        print("❌ Not a Git repository or git not installed")
        return None

def get_last_session_date():
    """Extract last update date from .claude-context.md."""
    if not Path(CONTEXT_FILE).exists():
        return None
    
    with open(CONTEXT_FILE) as f:
        for line in f:
            if "Last Updated:" in line:
                return line.split(":", 1)[1].strip()
    return None

def update_context(apply=False):
    """Detect changes and suggest update."""
    
    changes = get_git_diff()
    if not changes:
        print("✅ No changes detected")
        return
    
    timestamp = datetime.datetime.now().strftime("%B %d, %Y at %H:%M")
    last_session = get_last_session_date()
    
    # Parse changes
    modified_files = []
    for line in changes.strip().split('\n'):
        if line.startswith('M '):  # Modified
            modified_files.append(line[3:])
        elif line.startswith('A '):  # Added
            modified_files.append(line[3:] + " (NEW)")
    
    if not modified_files:
        print("✅ Only untracked files or deletions")
        return
    
    # Generate update text
    update_text = f"""
### {timestamp}

**Auto-Detected Changes:**
```
{chr(10).join(modified_files)}
```

**Manual Notes (Add the WHY):**
- [What did you accomplish?]
- [Why did you make this change?]
- [Any gotchas or blockers?]
- [Next steps?]
"""
    
    print("📝 PROPOSED UPDATE:\n")
    print(update_text)
    print("\n---\n")
    
    if apply:
        # Insert after "Recent Changes" header
        with open(CONTEXT_FILE, 'r') as f:
            content = f.read()
        
        marker = "## 🔧 Recent Changes"
        if marker in content:
            new_content = content.replace(marker, marker + update_text)
            
            with open(CONTEXT_FILE, 'w') as f:
                f.write(new_content)
            
            print(f"✅ Updated {CONTEXT_FILE}")
        else:
            print(f"⚠️  Couldn't find '{marker}' section in {CONTEXT_FILE}")
    else:
        print("📋 Preview mode. Run with --apply to update")

if __name__ == "__main__":
    apply_mode = "--apply" in sys.argv
    
    if apply_mode:
        print("🔄 Updating context automatically...\n")
    else:
        print("👀 Preview mode (not updating)\n")
    
    update_context(apply=apply_mode)
```

### How to Use

```bash
# See what changed (preview)
python scripts/update_context.py

# Actually update .claude-context.md
python scripts/update_context.py --apply

# Then manually add notes
vim .claude-context.md

# Commit
git add .claude-context.md .bugs_tracker.md
git commit -m "EOD: Updated context and bugs"
```

---

## 📋 Daily Workflow

### Morning (Start of Day)

```bash
# 1. Claude reads project state
"Read .claude-context.md and tell me where we left off. What should we work on?"

# 2. Claude checks for active bugs
"Any active bugs in .bugs_tracker.md we should know about?"

# 3. You're ready to start
"Let's continue with [feature]. Reference the framework if needed."
```

### During Work

- Make code changes
- Document what you're doing in your head (you'll need it later)
- If you find a bug: Add to `.bugs_tracker.md` immediately

### End of Day

```bash
# 1. Auto-update context (detects file changes)
python scripts/update_context.py --apply

# 2. Add manual notes (the WHY)
vim .claude-context.md
# Write: What you accomplished, why, any blockers

# 3. Commit
git add .claude-context.md .bugs_tracker.md
git commit -m "EOD: Updated context $(date +%Y-%m-%d)"
```
## 🧠 Automating Compliance: The "Hard Code" Files

**Don't want to remind Claude to read these files every time?** You can "hard code" these instructions into the AI's system prompt using project rules. This forces every session to start with context awareness and bug checks.

### For Cursor Users (`.cursorrules`)
Create a `.cursorrules` file in your project root with this content:

```
# 🤖 SYSTEM BEHAVIOR PROTOCOLS

1. **CONTEXT FIRST**: At the start of EVERY session, you MUST read `.claude-context.md` to understand the current project state, architectural decisions, and active features.
2. **BUG PREVENTION**: Before writing code, read `.bugs_tracker.md`. If a requested feature matches a known bug pattern, apply the documented fix immediately.
3. **TOOLING CHECK**: Always check `config/scale.yaml` before deciding between Local Adapters or MCP Bridges.
4. **DOCUMENTATION**: If you fix a bug, you MUST ask the user to update `.bugs_tracker.md`.
---

## 🔧 Troubleshooting

### Problem: Claude Keeps Asking Same Questions

**Symptom:**
```
Claude: "What's your database setup?"
You: "I put it in .claude-context.md"
Claude: "Can you tell me again?"
```

**Fix:**
```
Tell Claude: "Please read .claude-context.md first. It has all project state."
```

Then Claude will:
- Read the file
- Understand your project
- Stop asking basic questions

---

### Problem: Same Bug Happens Twice

**Symptom:** Bug fixed on Wednesday, same bug on Friday

**Fix:**
1. Check `.bugs_tracker.md` for similar bugs
2. Add to bug tracker when found (prevent repetition)
3. Reference in `.claude-context.md` Known Issues section

---

### Problem: Context File Growing Too Large

**Symptom:** `.claude-context.md` is now 50KB

**Fix:**
1. Archive old sections (move to `.claude-context-archive.md`)
2. Keep only last 2 weeks of changes
3. Keep all architectural decisions (don't delete those)

---

## ✅ Setup Checklist

Before starting any project:

### Initial Setup
- [ ] Create `.claude-context.md` from template (above)
- [ ] Create `.bugs_tracker.md` from template (above)
- [ ] Create `scripts/update_context.py` file
- [ ] Create `scripts/` folder if it doesn't exist
- [ ] Make script executable: `chmod +x scripts/update_context.py`

### Per-Session Setup
- [ ] Morning: Tell Claude "Read .claude-context.md first"
- [ ] Work: Make changes, document progress
- [ ] Evening: `python scripts/update_context.py --apply`
- [ ] Evening: Add manual notes to `.claude-context.md`
- [ ] Evening: Update `.bugs_tracker.md` if bugs found
- [ ] Evening: Commit both files

### Best Practices
- [ ] Update context after each coding session
- [ ] Never delete architectural decisions (keep forever)
- [ ] Bug tracker is for learning (understand WHY bugs happen)
- [ ] Run automation script, then manually add "the why"
- [ ] Commit context files with code changes

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework  

**Next File:** [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) (Deep methodology)

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

**These two files are your insurance policy against context loss and repeated bugs.**  
**Set them up once, maintain them daily, reap the benefits forever.** 🧠