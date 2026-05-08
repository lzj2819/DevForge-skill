# State Updater

## Purpose

Standardize the STATE.md update pattern across all DevForge skills.

## Template

```markdown
## State Update

Update `PROJECT_SCAFFOLD/docs/architecture/system/STATE.md`:

1. Append to **Completed Steps**: `[YYYY-MM-DD HH:MM] {skill_name}: {summary}`
2. Update **Current State**: `phase: {new_phase}`, DIVE `Design: {status}`, `Implement: {status}`, `Verify: {status}`, `Evolve: {status}`
3. Update **Module Registry** (if applicable): append `{id: module_id, status: {status}, path: {path}}`
4. Append any discovered risks to **Known Pitfalls**
5. Update **Artifact Index** with new/modified artifacts
```

## Phase Transition Map

| From Phase | Skill | To Phase |
|------------|-------|----------|
| initial | requirement-analysis | `requirement_analysis_completed` |
| requirement_analysis_completed | architecture-design | `architecture_design_completed` |
| architecture_design_completed | architecture-validation | `architecture_validated` |
| architecture_design_completed | design-review | `design_review_completed` |
| architecture_validated + design_review_completed | project-scaffolding | `scaffolding_completed` |
| scaffolding_completed | module-design | `module_design_completed` (when all modules done) |
| module_design_completed | test-execution | `test_execution_completed` |
| test_execution_completed | iteration-planning | `iteration_planning_completed` |
| iteration_planning_completed | architecture-validation | `architecture_validated` (loop back) |

## Usage

Each skill fills in:
- `{skill_name}`: the current skill identifier
- `{summary}`: what was accomplished (include counts, key decisions)
- `{new_phase}`: the phase to transition to (or keep current if not a transition point)
- `{status}`: `completed`, `in_progress`, `pending`, or `not_applicable`
- `{module_id}`: the module identifier (for module-design only)
- `{path}`: relative path to module artifacts
