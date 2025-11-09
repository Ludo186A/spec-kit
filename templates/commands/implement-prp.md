---
description: Execute focused implementation of a specific component defined in a PRP (Project Requirements & Planning) document
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks --include-prp
  ps: scripts/powershell/check-prerequisites.ps1 -Json -RequireTasks -IncludeTasks -IncludePrp
---



## User Input



```text

$ARGUMENTS

```



You \*\*MUST\*\* consider the user input to identify the target PRP component (if not empty).



\## Outline



1\. Run `{SCRIPT}` from repo root and parse FEATURE\_DIR, AVAILABLE\_DOCS list, and AVAILABLE\_PRPS list. All paths must be absolute. For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\\''m Groot' (or double-quote if possible: "I'm Groot").



2\. \*\*PRP Component Selection\*\*:

&nbsp;  - \*\*If user specified component\*\*: Look for `prp-\[component-name].md` in FEATURE\_DIR

&nbsp;  - \*\*If no component specified\*\*: 

&nbsp;    - List all available PRP files from AVAILABLE\_PRPS

&nbsp;    - If only one PRP exists, select it automatically

&nbsp;    - If multiple PRPs exist, ask user to choose which component to implement

&nbsp;    - If no PRPs exist, \*\*STOP\*\* and suggest running `/speckit.prp \[component-name]` first



3\. \*\*Load target PRP document\*\*:

&nbsp;  - \*\*REQUIRED\*\*: Read the selected `prp-\[component-name].md` for focused implementation scope

&nbsp;  - Extract component metadata: name, type, framework, dependencies, mapped tasks

&nbsp;  - Extract implementation blueprint and success criteria

&nbsp;  - Extract task mapping from the PRP (which tasks from tasks.md this PRP covers)



4\. \*\*Check component-specific checklists\*\* (if FEATURE\_DIR/checklists/ exists):

&nbsp;  - Look for checklist files that match the component name or type

&nbsp;  - Scan relevant checklist files for the component being implemented

&nbsp;  - For each relevant checklist, count:

&nbsp;    - Total items: All lines matching `- \[ ]` or `- \[X]` or `- \[x]`

&nbsp;    - Completed items: Lines matching `- \[X]` or `- \[x]`

&nbsp;    - Incomplete items: Lines matching `- \[ ]`

&nbsp;  - Create a status table:



&nbsp;    ```text

&nbsp;    | Checklist | Total | Completed | Incomplete | Status |

&nbsp;    |-----------|-------|-----------|------------|--------|

&nbsp;    | api.md    | 8     | 8         | 0          | ✓ PASS |

&nbsp;    | security.md | 6   | 4         | 2          | ✗ FAIL |

&nbsp;    ```



&nbsp;  - \*\*If any relevant checklist is incomplete\*\*:

&nbsp;    - Display the table with incomplete item counts

&nbsp;    - \*\*STOP\*\* and ask: "Component-specific checklists are incomplete. Do you want to proceed with implementation anyway? (yes/no)"

&nbsp;    - Wait for user response before continuing

&nbsp;    - If user says "no" or "wait" or "stop", halt execution

&nbsp;    - If user says "yes" or "proceed" or "continue", proceed to step 5



&nbsp;  - \*\*If all relevant checklists are complete\*\*:

&nbsp;    - Display the table showing all checklists passed

&nbsp;    - Automatically proceed to step 5



5\. Load and analyze the broader implementation context:

&nbsp;  - \*\*REQUIRED\*\*: Read tasks.md for the complete task list (to understand dependencies)

&nbsp;  - \*\*REQUIRED\*\*: Read plan.md for tech stack, architecture, and file structure

&nbsp;  - \*\*IF EXISTS\*\*: Read data-model.md for entities related to this component

&nbsp;  - \*\*IF EXISTS\*\*: Read contracts/ for API specifications relevant to this component

&nbsp;  - \*\*IF EXISTS\*\*: Read research.md for technical decisions affecting this component

&nbsp;  - \*\*IF EXISTS\*\*: Read quickstart.md for integration scenarios involving this component



6\. \*\*Component-Specific Project Setup\*\*:

&nbsp;  - \*\*Component Dependencies\*\*: Verify that component dependencies (from PRP metadata) are available

&nbsp;  - \*\*Technology-Specific Setup\*\*: Create/verify ignore files only for technologies used by this component

&nbsp;  - \*\*Integration Points\*\*: Ensure integration points with other components are ready



&nbsp;  \*\*Detection \& Creation Logic\*\* (same as main implement but component-focused):

&nbsp;  - Check if the following command succeeds to determine if the repository is a git repo:



&nbsp;    ```sh

&nbsp;    git rev-parse --git-dir 2>/dev/null

&nbsp;    ```



&nbsp;  - Apply technology-specific patterns only for the component's tech stack

&nbsp;  - Focus on patterns relevant to the component being implemented



7\. \*\*Extract component-specific tasks\*\*:

&nbsp;  - Parse the PRP's task mapping section

&nbsp;  - Extract only the tasks from tasks.md that are mapped to this component (e.g., "T005", "T012", "T018")

&nbsp;  - Identify task dependencies within the component scope

&nbsp;  - Determine execution order for component tasks

&nbsp;  - Check for external dependencies (tasks not in this component that must complete first)



8\. \*\*Component Implementation Execution\*\*:

&nbsp;  - \*\*Pre-execution validation\*\*: Ensure all external dependencies are satisfied

&nbsp;  - \*\*Focused execution\*\*: Run only the tasks mapped to this component

&nbsp;  - \*\*Phase-by-phase approach\*\*: Follow the implementation blueprint from the PRP

&nbsp;  - \*\*Respect component boundaries\*\*: Avoid modifications outside component scope

&nbsp;  - \*\*Integration awareness\*\*: Consider integration points with other components



9\. Component execution rules:

&nbsp;  - \*\*Component setup first\*\*: Initialize component-specific structure and dependencies

&nbsp;  - \*\*Component tests\*\*: Implement tests for this component's functionality

&nbsp;  - \*\*Core component logic\*\*: Implement the component's primary functionality

&nbsp;  - \*\*Integration interfaces\*\*: Implement the component's integration points

&nbsp;  - \*\*Component validation\*\*: Test component independently and integration points



10\. \*\*Component-focused progress tracking\*\*:

&nbsp;   - Report progress for each completed component task

&nbsp;   - Track completion against PRP success criteria

&nbsp;   - Halt execution if critical component tasks fail

&nbsp;   - For parallel component tasks \[P], continue with successful tasks, report failed ones

&nbsp;   - \*\*IMPORTANT\*\*: Mark completed tasks as \[X] in both tasks.md AND track completion in PRP

&nbsp;   - Provide component-specific error messages and debugging context



11\. \*\*Component completion validation\*\*:

&nbsp;   - Verify all component tasks from PRP mapping are completed

&nbsp;   - Check that implemented component matches PRP specification

&nbsp;   - Validate component tests pass and meet PRP success criteria

&nbsp;   - Confirm component integration points work correctly

&nbsp;   - Test component independently before declaring complete

&nbsp;   - Report component status and readiness for integration



\## PRP Implementation Rules



\*\*CRITICAL\*\*: This command implements ONLY the component defined in the selected PRP.



\*\*Component Scope Enforcement\*\*:

\- Execute ONLY tasks mapped in the PRP's task mapping section

\- Do NOT implement tasks outside the component scope

\- Respect component boundaries defined in the PRP

\- Focus on component-specific files and functionality



\*\*Integration Awareness\*\*:

\- Be aware of integration points with other components

\- Implement component interfaces as specified in PRP

\- Ensure component can be integrated when other components are ready

\- Do NOT implement other components' functionality



\*\*Success Criteria Focus\*\*:

\- Validate implementation against PRP success criteria (not full feature criteria)

\- Test component functionality as defined in PRP

\- Ensure component meets its specific requirements

\- Component should be deployable/testable independently when possible



\*\*Progress Reporting\*\*:

\- Report completion as "Component \[name] implementation complete"

\- List which tasks from tasks.md were completed

\- Reference PRP success criteria that were met

\- Identify any remaining integration work needed



Note: This command requires a PRP document to exist. If no PRP exists for the desired component, run `/speckit.prp \[component-name]` first to generate the focused implementation plan.



\## Error Handling



\*\*Missing PRP\*\*: If target PRP doesn't exist, suggest creating it:

```

ERROR: PRP file 'prp-\[component].md' not found in \[FEATURE\_DIR]

Run /speckit.prp \[component-name] first to generate the component implementation plan.

```



\*\*Invalid Component\*\*: If specified component doesn't match any available PRP:

```

ERROR: No PRP found for component '\[component-name]'

Available PRPs: \[list available PRP files]

Use /speckit.implement-prp \[available-component] or create new PRP with /speckit.prp \[component-name]

```



\*\*External Dependencies\*\*: If component depends on incomplete external tasks:

```

WARNING: Component has external dependencies that are not complete:

\- T003 (User model) - Required by this component

\- T008 (Database setup) - Required by this component



Recommend completing these tasks first, or proceed with component implementation and handle integration later.

Continue? (yes/no)

```

