---
description: Execute the implementation planning workflow using the plan template to generate design artifacts.
scripts:
  sh: scripts/bash/setup-plan.sh --json
  ps: scripts/powershell/setup-plan.ps1 -Json
agent_scripts:
  sh: scripts/bash/update-agent-context.sh __AGENT__
  ps: scripts/powershell/update-agent-context.ps1 -AgentType __AGENT__
---

## User Input
```text
$ARGUMENTS
```
You **MUST** consider the user input before proceeding (if not empty).

## Outline

1. **Knowledge Base Research via Anchor MCP**:
   - Query Anchor knowledge base for relevant patterns, best practices, and architectural decisions
   - Search for similar implementations or components related to this feature
   - Extract relevant technical standards, conventions, and lessons learned from Anchor
   - Identify established patterns for the proposed tech stack
   - Include findings to inform all subsequent technical planning decisions

2. **Setup**: Run `{SCRIPT}` from repo root and parse JSON for FEATURE_SPEC, IMPL_PLAN, SPECS_DIR, BRANCH. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot").

3. **Load context**: Read FEATURE_SPEC and `/memory/constitution.md`. Load IMPL_PLAN template (already copied).

4. **Execute plan workflow**: Follow the structure in IMPL_PLAN template to:
   - Fill Technical Context (incorporate Anchor findings, mark remaining unknowns as "NEEDS CLARIFICATION")
   - Fill Constitution Check section from constitution
   - Evaluate gates (ERROR if violations unjustified)
   - Phase 0: Generate research.md (resolve all NEEDS CLARIFICATION, include Anchor insights)
   - Phase 1: Generate data-model.md, contracts/, quickstart.md
   - Phase 1: Update agent context by running the agent script
   - Re-evaluate Constitution Check post-design

5. **Stop and report**: Command ends after Phase 2 planning. Report branch, IMPL_PLAN path, and generated artifacts.

## Phases

### Phase 0: Outline & Research

1. **Incorporate Anchor findings into research**:
   - Merge Anchor knowledge base findings from step 1
   - Document established patterns and standards from Anchor
   - Note any conflicts between Anchor recommendations and current approach

2. **Extract unknowns from Technical Context** (after Anchor consultation):
   - For each remaining NEEDS CLARIFICATION → research task
   - For each dependency not covered by Anchor → best practices task  
   - For each integration not documented in Anchor → patterns task

3. **Generate and dispatch research agents**:
```text
   For each unknown not resolved by Anchor:
     Task: "Research {unknown} for {feature context}"
   For each technology choice not covered in Anchor:
     Task: "Find best practices for {tech} in {domain}"
```

4. **Consolidate findings** in `research.md` using format:
   - Anchor Reference: [relevant patterns/standards from knowledge base]
   - Decision: [what was chosen]
   - Rationale: [why chosen, considering Anchor recommendations]
   - Alternatives considered: [what else evaluated]

**Output**: research.md with all NEEDS CLARIFICATION resolved and Anchor insights integrated

### Phase 1: Design & Contracts

**Prerequisites:** `research.md` complete with Anchor integration

1. **Extract entities from feature spec** → `data-model.md`:
   - Apply Anchor standards for entity design where applicable
   - Entity name, fields, relationships following Anchor conventions
   - Validation rules from requirements and Anchor best practices
   - State transitions if applicable, using Anchor patterns

2. **Generate API contracts** from functional requirements:
   - Follow Anchor API design standards where established
   - For each user action → endpoint
   - Use standard REST/GraphQL patterns from Anchor knowledge base
   - Output OpenAPI/GraphQL schema to `/contracts/`

3. **Agent context update**:
   - Run `{AGENT_SCRIPT}`
   - These scripts detect which AI agent is in use
   - Update the appropriate agent-specific context file
   - Add only new technology from current plan
   - Preserve manual additions between markers

**Output**: data-model.md, /contracts/*, quickstart.md, agent-specific file

## Key rules
- Use absolute paths
- ERROR on gate failures or unresolved clarifications
- Prioritize Anchor knowledge base findings in all technical decisions
- Document when deviating from Anchor recommendations with clear rationale