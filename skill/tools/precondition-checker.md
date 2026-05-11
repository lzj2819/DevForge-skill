# Precondition Checker

## Purpose

Standardize the precondition check pattern across all DevForge skills.

## Template

```markdown
## Precondition Check

Before starting, read `PROJECT_SCAFFOLD/docs/architecture/system/STATE.md`.
- Acceptable phases: `{phase_list}`
- If phase is earlier than `{minimum_phase}`, stop and instruct the user to complete `{prerequisite_skill}` first
- If `{required_artifact}` is missing, stop and instruct the user to complete `{prerequisite_skill}` first
```

## Usage

Each skill copies this template and fills in:
- `{phase_list}`: comma-separated list of acceptable phases
- `{minimum_phase}`: the earliest acceptable phase
- `{prerequisite_skill}`: the skill to recommend if preconditions are not met
- `{required_artifact}`: the artifact that must exist (e.g., `architecture.xml`)

## Standard Phase Flow

| Skill | Acceptable Phases | Minimum Phase | Required Artifact |
|-------|-------------------|---------------|-------------------|
| requirement-analysis | `initial` | `initial` | none |
| architecture-design | `requirement_analysis_completed`, `evolution_completed` | `requirement_analysis_completed` | `PRD.md` |
| architecture-validation | `architecture_design_completed` | `architecture_design_completed` | `architecture.xml` |
| design-review | `architecture_validated`, `architecture_design_completed` | `architecture_design_completed` | `architecture.xml` |
| project-scaffolding | `architecture_design_completed`, `architecture_validated`, `design_review_completed` | `architecture_design_completed` | `architecture.xml` |
| module-design | `scaffolding_completed`, `module_design_completed` | `scaffolding_completed` | `architecture.xml` |
| test-execution | `module_design_completed` | `module_design_completed` | module designs |
| iteration-planning | `module_design_completed`, `iteration_planning_completed` | `module_design_completed` | `STATE.md` |
| visualization | `architecture_design_completed`+ | `architecture_design_completed` | `architecture.xml` |
| ops-ready | `test_execution_completed` | `test_execution_completed` | `STATE.md` |
| debug-assistant | `scaffolding_completed`+ | `scaffolding_completed` | `STATE.md` |
| sdlc-security-audit | `design_review_completed` | `design_review_completed` | `architecture.xml` |
| devforge-threat-modeling | `design_review_completed`, `architecture_validated` | `design_review_completed` | `architecture.xml` |
| devforge-data-pipeline | `architecture_design_completed`, `scaffolding_completed` | `architecture_design_completed` | `architecture.xml` |

## Phase List

The following phases are defined by the skill chain:

- `initial` — before any skill runs
- `requirement_analysis_completed` — after `devforge-requirement-analysis` finishes
- `architecture_design_completed` — after `devforge-architecture-design` finishes
- `architecture_validated` — after `devforge-architecture-validation` finishes
- `design_review_completed` — after `devforge-design-review` finishes
- `security_audit_completed` — after `devforge-security-audit` finishes
- `threat_modeling_completed` — after `devforge-threat-modeling` finishes
- `data_pipeline_completed` — after `devforge-data-pipeline` finishes
- `scaffolding_completed` — after `devforge-project-scaffolding` finishes
- `module_design_completed` — after `devforge-module-design` finishes
- `test_execution_completed` — after `devforge-test-execution` finishes
- `iteration_planning_completed` — after `devforge-iteration-planning` finishes
- `visualization_completed` — after `devforge-visualization` finishes
- `ops_ready_completed` — after `devforge-ops-ready` finishes
- `evolution_completed` — after `devforge-iteration-planning` finishes with breaking changes
