# 🤖 AI Assistant Integration - Tool-Agnostic Workflows

**Version:** 1.5.0 | **Updated:** March 8, 2026 | **Part:** 5/10  
**Status:** Production Ready ✅  
**Purpose:** How to configure and interact with AI coding assistants (Cursor, Windsurf, Claude Code, Antigravity) so they follow this framework.

---

## 📍 Purpose

AI coding assistants change constantly. This file teaches you the **principles** of guiding any AI assistant so it doesn't hallucinate architectures, forget your context, or write insecure code.

**The Golden Rule of AI Assistants:** "The AI is a junior developer with infinite typing speed but zero object permanence. You must force it to read the rules and update the project memory every single time."

---

## 🗺️ Quick Navigation

- [The Universal Rules File](#-the-universal-rules-file)
- [The Worst-Case Coding Standard (Mandatory)](#-the-worst-case-coding-standard-mandatory)
- [Context Loading Strategy](#-context-loading-strategy)
- [Enforcing the Risk Score](#-enforcing-the-risk-score)
- [Standardized Commands (Prompt Patterns)](#-standardized-commands-prompt-patterns)
- [Skill Creation & Reuse](#-skill-creation--reuse)
- [Troubleshooting AI Hallucinations](#-troubleshooting-ai-hallucinations)

---

## 📜 The Universal Rules File (.cursorrules)

Most modern AI IDEs support a project-level rules file. Create a file named `agent.md` (or your IDE's equivalent) in the root of your project and paste this exactly:

```text
# 🤖 SYSTEM BEHAVIOR PROTOCOLS for [Project Name]

You are an expert AI Systems Architect building a production-grade AI agent system. You have finite context memory, so you MUST rely on your project memory files to avoid getting overwhelmed.

You must strictly follow this "Read -> Research -> Act -> Update" loop for every task:

1. **PHASE 1: READ (MANDATORY)** Before writing any code, you MUST silently read `.build-context.md` and `.bugs_tracker.md`. Use this to understand where we left off. Do not ask for setup information that is already in these files.

2. **PHASE 2: RESEARCH (TECH RADAR)** Before importing ANY new library, you must trigger the `tech-radar-skill` to validate it is the current SOTA (State of the Art) for [CURRENT_YEAR]. 
   - Do NOT use training-data defaults (like `requests` or `pandas`) blindly.
   - Validate the library is maintained and fits the Risk Score.

3. **PHASE 3: ACT (THE RISK SCORE LOCK & FAULT TOLERANCE)**
   You are FORBIDDEN from generating agent code or architecture until you know the 0-17 Risk Score (defined in `docs/guides/01_QUICK_REFERENCE.md`). 
   - Use the Agnostic Factory pattern (`docs/guides/08_AGNOSTIC_FACTORIES.md`) for all external dependencies.
   - **ASSUME NOTHING. GUARD EVERYTHING.** Wrap all external network and database I/O in `try/except` blocks with graceful degradation.
   - **WRITE DEFENSIVELY FOR CONTAINERS.** Do not rely on local ephemeral filesystems (e.g., `/tmp/`). Use BytesIO buffers or save directly to a persistent database.
   - **ENFORCE STRICT TIMEOUTS.** Do not block the main UI thread with synchronous background tasks.

4. **PHASE 4: UPDATE (MANDATORY BOOKKEEPING)**
   Immediately after making a change, fixing a bug, or making an architectural decision, YOU must update the memory files:
   - Fixed a bug? Add the root cause and solution to `.bugs_tracker.md`.
   - Built a feature or changed a file? Update the "Current State" and "Recent Changes" sections in `.build-context.md`.
   - Approved an audit item? Log it in the "Audit History" section of `.build-context.md`.

5. **PHASE 5: RECOGNIZE & PROPOSE SKILLS (CONTINUOUS)**
   As you build, you must watch for repeating patterns that should become reusable skills. A pattern qualifies as a skill candidate when:
   - You've written the same adapter/factory/scaffold pattern **3+ times** across features or projects.
   - A workflow step (e.g., "validate API response", "generate test scaffold", "create migration script") is manually repeated every time.
   - An audit finding keeps recurring across cycles (same type of fix applied twice = skill candidate).
   
   When you identify a candidate, **propose it immediately:**
   "I've noticed we repeat [pattern] in [locations]. This should be extracted into a reusable skill. Shall I create it now?"
   
   Skills are stored in `/skills/` and registered in the "Skills Registry" section of `.build-context.md`.
```

---

## 🛡️ THE "WORST-CASE" CODING STANDARD (Mandatory)

**Rule 1: No Happy Paths.**
Assume every API call will timeout, every database connection will fail, and every user input is malicious.

- ❌ Bad: `response = api.get(url); return response.json()`
- ✅ Good: `try: response = api.get(url, timeout=5); response.raise_for_status(); ... except RequestException: handle_error()`

**Rule 2: The "KISS" Constraint (Keep It Simple, Stupid).**
Before building a "Multi-Agent RAG System with Vector Memory," ask: Can this be a 5-line Python script?

- If a simple solution works, the complex one is forbidden.
- Architect Role: You are the gatekeeper. Reject over-engineered PRs immediately.

**Rule 3: The 8-Step Debugging Protocol.**
When a bug is found, you MUST follow this exact sequence. Do not skip steps:

1. **Find:** Locate the exact line of failure.
2. **Reproduce:** Create a script that forces the error to happen.
3. **Prove (Repro):** Show the log output confirming the crash.
4. **Root Cause:** Explain why it failed (don't guess).
5. **Fix:** Write the code correction.
6. **Test:** Run the reproduction script again.
7. **Regression Check:** Run the full test suite to ensure nothing else broke.
8. **Prove (Fix):** Show the log output confirming success.

---

## 🧠 Context Loading Strategy

Because AI context windows are finite, do not dump all framework files into every prompt.

**The Optimal Context Loading Sequence:**

- **Always in Context:** `agent.md`, `.build-context.md`, `.bugs_tracker.md`.
- **On Project Start:** Have the AI read `00_START_HERE.md` and `01_QUICK_REFERENCE.md`.
- **When doing DevOps:** `@` or reference `06_INFRASTRUCTURE_AS_CODE.md` specifically.
- **When refactoring/swapping:** `@` or reference `08_AGNOSTIC_FACTORIES.md`.
- **When doing maintenance/audit:** `@` or reference `09_AUDIT_AND_MAINTENANCE.md`.

---

## 🚫 Enforcing the Risk Score

If the AI tries to write code without knowing the risk score, it will guess the guardrails (usually getting them wrong).

**Your workflow when starting a new agent:**

> You: "Let's build a new agent that reads customer emails and drafts refund approvals. The Risk Score is 13 (High). Read `01_QUICK_REFERENCE.md` to see what guardrails are required, then propose the architecture."

If the AI starts coding immediately without guardrails, stop it:

> You: "Halt. You forgot the Risk Score 13 guardrails. Implement the Circuit Breaker and Human-in-the-loop HITL brake before writing the email parsing logic."

---

## 💬 Standardized Commands (Prompt Patterns)

Instead of typing long paragraphs, use these standardized prompt patterns.

### `/new-agent`

> "I want to create a new agent named [Name]. The Risk Score is [X]. Please:
> 1. Read `.build-context.md`.
> 2. Define its interface using our factory pattern.
> 3. Create a mock tool adapter for testing.
> 4. Write the Pytest file with LLM-as-a-judge Eval checks.
> Do not write the implementation until we agree on the tests."

### `/swap-component`

> "We need to swap our [Database/LLM/Orchestrator] from [Current] to [New].
> Please read `08_AGNOSTIC_FACTORIES.md`. Write the new adapter class, add it to the factory, tell me what environment variable to update in `scale.yaml`, and update `.build-context.md` with this architectural decision."

### `/run-audit`

> "Run the bi-annual audit process. Read `09_AUDIT_AND_MAINTENANCE.md` for the full procedure. Check dependencies, API contracts, and framework guide recommendations. Generate the report at `docs/audits/AUDIT_REPORT_[DATE].md` and notify via the configured channel."

### `/new-skill`

> "I've identified a repeating pattern: [describe the pattern and where it repeats].
> Please:
> 1. Read `.build-context.md` to check if a similar skill already exists in the Skills Registry.
> 2. Define the skill's interface (what it takes in, what it produces).
> 3. Write the skill implementation in `/skills/[skill-name]/`.
> 4. Include a `SKILL.md` with usage instructions and trigger conditions.
> 5. Add it to the Skills Registry in `.build-context.md`.
> 6. Write a test that validates the skill works in isolation."

---

## 🧩 Skill Creation & Reuse

### When to Create a Skill

Skills are reusable, self-contained automation patterns that the AI assistant can invoke by name. They prevent the AI from reinventing the same solution every session.

**The "Rule of 3" trigger:** If you or the AI have written the same pattern three or more times — across features, projects, or audit cycles — it must be extracted into a skill.

**Common skill candidates:**
- Test scaffold generators (e.g., "create a Pytest file with LLM-as-a-judge for this agent")
- Factory boilerplate (e.g., "create interface + adapter + factory for a new external service")
- Migration scripts (e.g., "generate an Alembic migration for this schema change")
- Deployment validators (e.g., "check that all env vars in scale.yaml exist in the secret manager")
- Audit sub-checks (e.g., "scan pyproject.toml for unmaintained packages")

### Skill File Structure

Each skill lives in `/skills/[skill-name]/` and contains:

```text
/skills/
  /test-scaffold/
    SKILL.md          # Usage instructions, trigger conditions, examples
    skill.py          # The implementation
    test_skill.py     # Validates the skill in isolation
  /factory-generator/
    SKILL.md
    skill.py
    test_skill.py
```

### Skill Lifecycle

1. **Identify:** AI proposes during Phase 5 of the Read → Research → Act → Update → Recognize loop.
2. **Create:** Use the `/new-skill` prompt pattern. AI writes the skill, test, and `SKILL.md`.
3. **Register:** AI adds the skill to the Skills Registry in `.build-context.md`.
4. **Use:** AI invokes the skill by name in future sessions instead of rewriting the pattern.
5. **Audit:** During bi-annual audits, skills are reviewed for staleness (see `09_AUDIT_AND_MAINTENANCE.md`).

---

## 🔧 Troubleshooting AI Hallucinations

| Problem | AI Cause | Solution |
|---------|----------|----------|
| AI hardcodes OpenAI API calls | Default training bias | Point it to `08_AGNOSTIC_FACTORIES.md` and say "Use the LLM Factory." |
| AI forgets previous decisions | Context window pushed out | Say: "Read `.build-context.md` to refresh your memory." |
| AI writes monolithic code | Lazy generation | Say: "Refactor this into the Modular Monolith structure defined in `02_COMPLETE_GUIDE.md`." |
| AI installs random libraries | Pip hallucination | Say: "Check `pyproject.toml` and use `uv` for dependency management." |
| AI skips audit maintenance | No awareness of Step 8 | Say: "Read `09_AUDIT_AND_MAINTENANCE.md` and follow the audit checklist." |
| AI rebuilds the same pattern repeatedly | No skill awareness | Say: "We've built this 3 times. Use `/new-skill` to extract it into a reusable skill." |

---

## 📌 File Meta

**Version:** 1.5.0  
**Released:** March 8, 2026  
**Status:** Production Ready ✅  
**Part of:** 10-Part AI Agent Framework  

**Next File:** [05_BUILD_CONTEXT_AND_BUGS.md](./05_BUILD_CONTEXT_AND_BUGS.md) (Memory)
