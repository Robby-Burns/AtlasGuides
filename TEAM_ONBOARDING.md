# 👋 Team Onboarding — AI Agent Framework

**Version:** 1.5.0 | **Updated:** March 8, 2026  
**Status:** Production Ready ✅  
**Audience:** New team members joining a project that uses this framework  
**Time to read:** 15 minutes

---

## What Is This Framework?

This is a 10-part methodology for building AI agent systems. It ensures that no matter which AI coding tool you use (Cursor, Claude Code, Gemini/Antigravity, Windsurf), the AI assistant follows the same rules, cites the same documentation, and produces consistent, production-quality code.

The framework solves three problems:
1. **AI hallucinations:** Without rules, AI tools invent architectures, import random libraries, and forget decisions between sessions.
2. **Team inconsistency:** Without a shared kernel, each developer's AI behaves differently — leading to conflicting patterns in the same codebase.
3. **Silent decay:** Without scheduled audits, deployed agents rot — dependencies age, APIs deprecate, and nobody notices until something breaks.

---

## Your First 30 Minutes

### Step 1: Understand the Kernel (5 min)

Open `agent.md` in the project root. This is the **System Kernel** — the set of rules that every AI tool reads before responding. It enforces:
- **Citation Law:** The AI must cite a framework file for every architectural decision. No citations = no trust.
- **The 5-Phase Loop:** Read → Research → Act → Update → Recognize. Every task, every time.
- **Debate Protocol:** The AI debates itself at key moments to catch bad decisions early.
- **Skills Enforcement:** Before writing code, the AI checks if a reusable skill already exists.

You don't need to memorize it. The AI reads it automatically.

### Step 2: Set Up Your Tool (5 min)

The kernel needs to be in the right place for your AI tool to find it:

| Tool | What It Reads | How to Set Up |
|------|--------------|---------------|
| **Cursor** | `.cursorrules` | Already synced if `./scripts/sync-kernel.sh` has been run |
| **Claude Code** | `CLAUDE.md` | Already synced if `./scripts/sync-kernel.sh` has been run |
| **Antigravity** | `.agents/workflows/` + `.agents/skills/` | Already synced if `./scripts/sync-kernel.sh` has been run. Guides become workflows, skills get YAML frontmatter. |
| **Gemini (standalone)** | Custom Instructions | Copy `agent.md` content into your Gemini Custom Instructions |
| **Windsurf** | `.windsurfrules` | Already synced if `./scripts/sync-kernel.sh` has been run |

**Verify it works:** Open your AI tool and type: `/status`

The AI should read `.build-context.md` and give you a summary of the project's current state, active bugs, and upcoming audit. If it doesn't, the kernel isn't loaded — check the file exists and restart your tool.

### Step 3: Read the Memory Files (5 min)

- **`.build-context.md`** — The project's memory. Current state, recent changes, architectural decisions, audit history, and the Skills Registry. The AI reads this at the start of every session.
- **`.bugs_tracker.md`** — Active bugs and patterns. The AI reads this to avoid repeating past mistakes.
- **`AgentSpec.md`** — The specification of *what* we're building. Generated during Discovery using `MASTER_AGENT_DISCOVERY_PROMPT.md`. This is the source of truth for features, architecture, risk score, and guardrails. The AI references this to stay aligned with the project's goals — not just how to build, but what to build.

Skim these now to understand where the project is.

### Step 4: Know the 10 Guide Files (5 min)

You don't need to read all 10 guides today. The AI loads them on demand. But know what exists:

| File | One-Line Summary |
|------|-----------------|
| `00_START_HERE.md` | Entry point. 6 decisions to make. |
| `01_QUICK_REFERENCE.md` | Formulas, checklists, risk scoring. Pin this. |
| `02_COMPLETE_GUIDE.md` | Deep methodology. 8-step process. |
| `03_DEPENDENCY_MANAGEMENT.md` | Python deps, uv, reproducible builds. |
| `04_AI_ASSISTANT_INTEGRATION.md` | How to guide AI tools. Prompt patterns. |
| `05_BUILD_CONTEXT_AND_BUGS.md` | Memory file templates. |
| `06_INFRASTRUCTURE_AS_CODE.md` | Docker, Terraform, deployment. |
| `07_CONFIGURATION_CONTROL.md` | scale.yaml. Cost controls. |
| `08_AGNOSTIC_FACTORIES.md` | Swap any dependency with one config change. |
| `09_AUDIT_AND_MAINTENANCE.md` | Bi-annual audits. HITL sign-off. |

### Step 5: Learn the Commands (5 min)

These work in any AI tool:

| Command | When to Use |
|---------|------------|
| `/status` | Start of every session. "Where are we?" |
| `/new-agent [name] [risk]` | Building a new agent. |
| `/swap-component [from] [to]` | Changing a database, LLM, or orchestrator. |
| `/new-skill [pattern]` | Extracting a repeating pattern into a reusable skill. |
| `/debate [topic]` | When something doesn't feel right. Forces a multi-role debate. |
| `/run-audit` | Triggering the bi-annual maintenance audit. |
| `/phase-check` | "Are we ready to move to the next phase?" |

---

## How Debates Work

The framework uses a tiered debate system. You'll see these in the AI's responses:

**Tier 1 — Sanity Check (frequent, quick):**
A 3-line check that happens automatically when the AI adds a dependency, picks between approaches, or touches external services. Looks like:
```
⚖️ SANITY CHECK
Builder says: [approach]
Protector says: [risk check]
Verdict: Go / Adjust / Escalate
```

**Tier 2 — Full Council (at key moments):**
A multi-role debate with a structured verdict. Happens automatically for architecture decisions, high-risk code, component swaps, and phase transitions. The AI summons relevant roles (Builder, Protector, Scaler, Pragmatist, Skeptic) and requires a dissent + resolution before deciding.

**Tier 3 — Human-Triggered:**
You type `/debate [topic]` and the AI runs a full council that presents two genuinely different approaches, tries to break both, and asks you to confirm before proceeding.

**Your role in debates:** You don't need to do anything for Tier 1 and 2 — they happen automatically. For Tier 2, read the verdict and confirm or push back. For Tier 3, you're the judge.

---

## Daily Workflow

### Morning
```
You: "/status"
AI: [Reads .build-context.md, summarizes state, lists active bugs]
You: "Let's work on [feature]. The risk score is [X]."
```

### During Work
- The AI follows the 5-Phase Loop automatically.
- You'll see Tier 1 sanity checks inline.
- At key moments, you'll see Tier 2 debates. Read the verdict and confirm.
- If something feels off: `/debate [concern]`

### End of Session
```
You: "Update .build-context.md with what we accomplished today."
AI: [Updates Current State, Recent Changes, and Skills Registry]
```

---

## Rules for the Team

1. **One kernel, one truth.** All changes to AI rules go through `agent.md`. Never edit `.cursorrules` or `CLAUDE.md` directly — edit `agent.md` and run `./scripts/sync-kernel.sh`.

2. **Trust the Citation Law.** If the AI makes a claim without citing a framework file, ask: "Which file says that?" This single habit catches most hallucinations.

3. **Don't skip the Risk Score.** Every agent gets scored before any code is written. The score determines everything — guardrails, debate intensity, testing scope.

4. **Skills before code.** Before writing a new adapter, test scaffold, or factory, check the Skills Registry in `.build-context.md`. If a skill exists, use it. If you've written the same thing three times, extract it.

5. **Debates are not overhead.** They're insurance. A 30-second Tier 1 check is cheaper than a 3-hour debugging session caused by a bad dependency choice.

---

## Common Questions

**"What if I disagree with a debate verdict?"**
Override it. You're the human. But log your reasoning in `.build-context.md` under Architectural Decisions so the AI knows for next time.

**"What if I'm using a tool not listed here?"**
The kernel is plain Markdown. Any AI tool that supports a system prompt or project-level rules file can read it. Copy `agent.md` content into whatever config slot your tool provides.

**"How does Antigravity differ from the other tools?"**
Antigravity has its own native structure (`.agents/skills/` and `.agents/workflows/`). The sync script maps our framework to this structure automatically. Skills in Antigravity are loaded *semantically* — the AI reads the skill's description and decides if it's relevant to your prompt. This means our skills need precise YAML frontmatter descriptions. See `04_AI_ASSISTANT_INTEGRATION.md` for details.

**"Can I add custom commands?"**
Yes. Add them to the "Standardized Commands" section of `agent.md` and run `sync-kernel.sh`. Discuss with the team first.

**"What happens during the bi-annual audit?"**
The system scans dependencies, API contracts, framework guides, and skills. It generates a report. A human reviews and approves each item. Nothing is auto-applied. See `09_AUDIT_AND_MAINTENANCE.md`.

---

## 📌 Document Meta

**Version:** 1.5.0  
**Released:** March 8, 2026  
**Status:** Production Ready ✅  
**Part of:** 10-Part AI Agent Framework
