**Outline**  
Generate a focused PRP (Project Requirements & Planning) document for a specific component identified from task breakdown and implementation plan.

**sh**: scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks  
**ps**: scripts/powershell/check-prerequisites.ps1 -Json -RequireTasks -IncludeTasks

**Setup**: Run {SCRIPT} from repo root and parse FEATURE_DIR and AVAILABLE_DOCS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'''m Groot' (or double-quote if possible: "I'm Groot").

**Load design documents**: Read from FEATURE_DIR:

- Required: plan.md (tech stack, architecture, file structure), tasks.md (task breakdown with phases), spec.md (user stories)
- Optional: data-model.md (entities), contracts/ (API endpoints), research.md (technical decisions), quickstart.md (integration scenarios)
- Note: Generate PRP based on what's available. Not all projects have all documents.

**Execute PRP generation workflow**:

1. Load tasks.md and identify component focus from user input or suggest components based on task analysis
2. Load plan.md and extract relevant technical context for the component
3. Load spec.md and map component to relevant user stories
4. If data-model.md exists: Extract entities related to component
5. If contracts/ exists: Map relevant endpoints to component
6. If research.md exists: Extract technical decisions affecting component
7. Analyze component scope and boundaries within overall system
8. Generate focused PRP document for the specific component
9. Validate PRP completeness (clear scope, mapped tasks, success criteria, implementation approach)

**Generate prp-[component-name].md**: Use .specify/templates/prp-template.md as structure, fill with:

- Component name derived from user input or task analysis
- Mapped tasks from tasks.md that relate to this component
- Technical specifications from plan.md relevant to component
- Success criteria based on mapped tasks and user stories
- Implementation blueprint with phases
- Dependencies on other components
- Integration points and validation approach

**Report**: Output path to generated PRP and summary:

- Component name and type
- Number of tasks mapped from tasks.md
- Dependencies identified
- Integration points with other components
- Confidence score for implementation readiness

**Context for PRP generation**: $ARGUMENTS

The PRP should be immediately implementable - focused enough to develop independently while maintaining integration with the overall system architecture.

**PRP Generation Rules**

**CRITICAL**: PRP MUST be focused on a single, implementable component to enable independent development.

**Component Identification (REQUIRED)** From user input or task analysis, identify ONE specific component:

- From tasks.md patterns: Setup/Infrastructure, Core Logic, API/Interface, Testing/Validation, Integration
- From plan.md patterns: Services, Data Layer, Frontend, External Integrations
- From user input: Specific component name or domain focus

**PRP Structure (REQUIRED)** Every PRP MUST follow this structure:

```markdown
# ##PRP: [Component Name] - [Brief Description]

---
name: "[Component Name]"
type: "[component|service|agent|integration]"
framework: "[framework from plan.md]"
dependencies: ["dep1", "dep2"]
priority: "[high|medium|low]"
complexity: "[1-10]"
estimated_effort: "[hours/days]"
tasks_mapped: ["T001", "T005", "T012"]
---

## Goal
[Specific, measurable objective extracted from mapped tasks]

## Why  
[Business value and technical rationale from plan.md/spec.md context]

## What
### Technical Specification
[Component details from plan.md relevant to this component]

### Task Mapping
[List specific tasks from tasks.md that this PRP encompasses]

## Success Criteria
[Testable requirements derived from mapped tasks and user stories]

## Implementation Blueprint
[Phase-by-phase approach based on task structure]

## Context References
[References to specific sections in plan.md, tasks.md, spec.md]
```

**Task Mapping Rules**

- Extract tasks from tasks.md that relate specifically to this component
- Preserve task IDs and dependencies from original tasks.md
- Map tasks to implementation phases within the PRP
- Identify which user stories this component serves

**Technical Context Rules**

- Extract relevant architecture from plan.md
- Include only tech stack elements this component uses
- Specify file paths and structure relevant to component
- Include integration points with other components

**Scope Validation** PRP scope is correct when:

- Component is implementable independently (within dependencies)
- Task mapping covers complete component functionality
- Success criteria are specific and testable
- Integration points are clearly defined
- Implementation approach aligns with plan.md architecture

The goal is to create a focused, implementable specification for a single component that enables parallel development while maintaining system integration.