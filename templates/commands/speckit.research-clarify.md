---
description: Clarification and research session prior to implementation (PRPs TestAgent/VoiceAgent/Orchestrator)
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --include-tasks
  ps: scripts/powershell/check-prerequisites.ps1 -Json -IncludeTasks
---

## User Input

```text
$ARGUMENTS  
First line (required): Path to the target PRP to update (e.g., specs/001-voice-agent-testing/prps/orchestrator-prp.md)
Following lines (optional): Additional user context. The assistant will automatically generate clarification questions.
```

You **MUST** consider the user input before proceeding (if not empty).

## Setup

**Setup**: Run `.specify/scripts/powershell/check-prerequisites.ps1 -Json` from the repo root **once**, and parse minimal JSON payload fields:
- `FEATURE_DIR`
- `AVAILABLE_DOCS` (list of available documents in the feature directory)
- If JSON parsing fails, abort and instruct the user to re-run `/speckit.specify` or verify the feature branch environment.
- For single quotes in args like "I'm Groot", use escape syntax: e.g. 'I'\''m Groot (or use double-quotes: "I'm Groot").

## Objective
Detect and reduce ambiguities or missing decisions that impact architecture, contracts, tests, and evaluation. The session produces concrete answers that are **integrated directly into the PRP specified in `$ARGUMENTS`** (adding/updating minimal, testable sections).

## Scope & Rules (based on speckit.clarify)
- The assistant (me) **proactively generates questions** needed to create/adjust the target PRP, based on the current spec and research via Anchor MCP. User input is optional.
- Maximum of 5 questions per session, one at a time.
- Each question must be resolved with:
  - Multiple choice (2–5 mutually exclusive options), or
  - Short answer (<= 5 words).
- Always show an explicit recommendation before listing options.
- Record each accepted answer in the **target PRP** under `## Clarifications > ### Session YYYY-MM-DD` (create if missing) and update the affected section of the PRP in a minimal, testable way.
- Do not parallelize: resolve in order of highest impact (Impact × Uncertainty).

## Operating Mode
- Start with a high‑impact question (priority T019–T023) following the workflow format.
- After your answer, **I immediately integrate it into the target PRP** (file provided in `$ARGUMENTS`) and adjust relevant sections as needed.
- Repeat up to 5 questions, or until you indicate it's sufficient.

## Coverage Taxonomy (scan guide)
- Functional scope and success criteria
- Domain and data model
- Interaction flow and UX (turns, stages)
- Non-functional attributes (latency, reliability, observability)
- External integrations and dependencies (LiveKit, Langfuse, MCP)
- Edge cases and failure handling (retries, timeouts, mind change)
- Constraints and trade-offs
- Terminology and consistency
- Exit signals and DoD

## Question Queue (candidates)
Questions are activated one at a time. Below are initial candidates by pending research area (T019–T023).

### T019 — Langfuse evaluators and scoring
Question C1: What is the primary evaluation level?

**Recommended:** Option B — Conversation-level evaluation with per-turn events  
Reason: Balances granularity with simplicity; enables global KPIs and attaching per‑turn excerpts/flags.

| Option | Description |
|--------|-------------|
| A | Turn-only (each exchange produces an independent score) |
| B | Conversation-level + per-turn events (global KPIs with turn-by-turn annotations) |
| C | Mixed with configurable weights (more complexity than MVP) |
| Short | Customize (<=5 words) |

Response format: "A", "B", "C" or short.

### T020 — LiveKit multi‑agent coordination
Question C2: Turn detection in MVP?

**Recommended:** Option B — Manual (orchestrator controls)  
Reason: Reduces initial uncertainty; we can later move to semantic/multilingual detector.

| Option | Description |
|--------|-------------|
| A | Automatic VAD/turn detection (Multilingual/Semantic) |
| B | Manual (VoiceAgent greets; orchestrator advances stages) |
| C | Hybrid (manual with VAD fallback) |
| Short | Other (<=5 words) |

### T022 — Pytest + sequential + retry/backoff
Question C3: Retry scope in MVP?

**Recommended:** Option B — Retries on critical I/O operations (LiveKit room, trace finalize)  
Reason: Higher benefit with less noise; avoids hiding logical bugs.

| Option | Description |
|--------|-------------|
| A | Broad retries (almost all operations) |
| B | Critical I/O only (create room, join, finalize trace) |
| C | No retries (fail fast) |
| Short | Customize (<=5 words) |

### T023 — Hybrid evaluation + LLM‑as‑a‑Judge
Question C4: Minimum general metric set for MVP?

**Recommended:** Option B — 3 metrics: task_success (binary), professionalism (1–5), latency_ms (numeric)  
Reason: Covers outcome, quality, and performance; simple to report/visualize.

| Option | Description |
|--------|-------------|
| A | task_success only (binary) |
| B | task_success + professionalism + latency_ms |
| C | Extended package (7+ metrics) |
| Short | Other (<=5 words) |

## Integration Plan after each answer
1. Add one line in the **target PRP** under `## Clarifications > ### Session YYYY-MM-DD`:
   - `- Q: <question> → A: <answer>`
2. Update the **target PRP** affected section (technical spec, success criteria, blueprint, examples) keeping the change minimal and testable.
3. If applicable, reflect notes in other artifacts (e.g., `Specs/Readme_langfuse_specs.md` or contracts), but the source of truth for this session is the **specified PRP**.
4. Save and mark T019/T020/T022/T023 as ✅ when appropriate.

## Anchor MCP — Query Plan
- Langfuse evaluators: "langfuse evaluator api dataset scoring", "langfuse score config examples"
- LiveKit coordination: "AgentSession multi participant turn management", "RoomIO examples"
- Pytest async: "pytest asyncio fixtures sequential", "exponential backoff pattern"
- LLM‑as‑a‑Judge: "langfuse llm judge rubric", "hybrid evaluation metrics"

## Expected session outcome
- 3–5 accepted answers reducing MVP uncertainty.
- Update `research.md` and notes in PRPs.
- Clear signal to start implementation (or if another clarification round is needed).
