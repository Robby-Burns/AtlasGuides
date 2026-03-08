# 🤖 AI Assistant Integration - Tool-Agnostic Workflows

**Version:** 1.4.0 | **Updated:** March 1, 2026 | **Part:** 5/9  
**Status:** Production Ready ✅  
**Purpose:** How to configure and interact with AI coding assistants (Cursor, Windsurf, Claude Code) so they follow this framework.

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
- [Troubleshooting AI Hallucinations](#-troubleshooting-ai-hallucinations)

---

## 📜 The Universal Rules File (.cursorrules)

Most modern AI IDEs support a project-level rules file. Create a file named `agent.md` (or your IDE's equivalent) in the root of your project and paste this exactly:

```text
# 🤖 SYSTEM BEHAVIOR PROTOCOLS for [Project Name]

You are an expert AI Systems Architect building a production-grade AI agent system. You have finite context memory, so you MUST rely on your project memory files to avoid getting overwhelmed.

You must strictly follow this "Read -> Research -> Act -> Update" loop for every task:

1. **PHASE 1: READ (MANDATORY)** Before writing any code, you MUST silently read `.build-context.md` (formerly .claude-context) and `.bugs_tracker.md`. Use this to understand where we left off. Do not ask for setup information that is already in these files.

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

🛡️ THE "WORST-CASE" CODING STANDARD (Mandatory)

Rule 1: No Happy Paths.
Assume every API call will timeout, every database connection will fail, and every user input is malicious.

    ❌ Bad: response = api.get(url); return response.json()

    ✅ Good: try: response = api.get(url, timeout=5); response.raise_for_status(); ... except RequestException: handle_error()

Rule 2: The "KISS" Constraint (Keep It Simple, Stupid).
Before building a "Multi-Agent RAG System with Vector Memory," ask: Can this be a 5-line Python script?

    If a simple solution works, the complex one is forbidden.

    Architect Role: You are the gatekeeper. Reject over-engineered PRs immediately.

Rule 3: The 8-Step Debugging Protocol.
When a bug is found, you MUST follow this exact sequence. Do not skip steps:

    Find: Locate the exact line of failure.

    Reproduce: Create a script that forces the error to happen.

    Prove (Repro): Show the log output confirming the crash.

    Root Cause: Explain why it failed (don't guess).

    Fix: Write the code correction.

    Test: Run the reproduction script again.

    Regression Check: Run the full test suite to ensure nothing else broke.

    Prove (Fix): Show the log output confirming success.

🧠 Context Loading Strategy

Because AI context windows are finite, do not dump all framework files into every prompt.

The Optimal Context Loading Sequence:

    Always in Context: agent.md, .build-context.md, .bugs_tracker.md.

    On Project Start: Have the AI read 00_START_HERE.md and 01_QUICK_REFERENCE.md.

    When doing DevOps: @ or reference 06_INFRASTRUCTURE_AS_CODE.md specifically.

    When refactoring/swapping: @ or reference 08_AGNOSTIC_FACTORIES.md.

🚫 Enforcing the Risk Score

If the AI tries to write code without knowing the risk score, it will guess the guardrails (usually getting them wrong).

Your workflow when starting a new agent:

    You: "Let's build a new agent that reads customer emails and drafts refund approvals. The Risk Score is 13 (High). Read 01_QUICK_REFERENCE.md to see what guardrails are required, then propose the architecture."

If the AI starts coding immediately without guardrails, stop it:

    You: "Halt. You forgot the Risk Score 13 guardrails. Implement the Circuit Breaker and Human-in-the-loop HITL brake before writing the email parsing logic."

💬 Standardized Commands (Prompt Patterns)

Instead of typing long paragraphs, use these standardized prompt patterns.
/new-agent

    "I want to create a new agent named [Name]. The Risk Score is [X]. Please:

        Read .build-context.md.

        Define its interface using our factory pattern.

        Create a mock tool adapter for testing.

        Write the Pytest file with LLM-as-a-judge Eval checks.
        Do not write the implementation until we agree on the tests."

/swap-component

    "We need to swap our [Database/LLM/Orchestrator] from [Current] to [New].
    Please read 08_AGNOSTIC_FACTORIES.md. Write the new adapter class, add it to the factory, tell me what environment variable to update in scale.yaml, and update .build-context.md with this architectural decision."

🔧 Troubleshooting AI Hallucinations
Problem	AI Cause	Solution
AI hardcodes OpenAI API calls	Default training bias	Point it to 08_AGNOSTIC_FACTORIES.md and say "Use the LLM Factory."
AI forgets previous decisions	Context window pushed out	Say: "Read .build-context.md to refresh your memory."
AI writes monolithic code	Lazy generation	Say: "Refactor this into the Modular Monolith structure defined in 02_COMPLETE_GUIDE.md."
AI installs random libraries	Pip hallucination	Say: "Check pyproject.toml and use uv for dependency management."
📌 File Meta

Version: 1.4.0

Released: March 1, 2026

Status: Production Ready ✅

Part of: 9-Part AI Agent Framework

Next File: 05_BUILD_CONTEXT_AND_BUGS.md (Memory)


### Clarifying Questions to Complete Your Patch Pack:

1.  **File 06 (Infrastructure):** Your "Patch Pack" mentioned updating `06_INFRASTRUCTURE_AS_CODE.md` to include the **"Container-First" Deployment Strategy**. Do you want me to generate the full Markdown for that file now?
2.  **File 08 (Factories):** The patch also mentioned updating `08_AGNOSTIC_FACTORIES.md` to include the **Tech Radar -> Factory Pipeline**. Should I generate that file next?
3.  **Renaming Context:** I noticed in the new rules we refer to `.build-context.md`