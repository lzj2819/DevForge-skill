# Validation Engine

## Purpose

Standardize the self-validation pattern across all DevForge skills.

## Template

```markdown
## Self-validation: {validation_name}

Before finalizing, verify {artifacts} with automated checks:
- **Check 1**: {description} -> {pass_criteria}
- **Check 2**: {description} -> {pass_criteria}
- ...

If any check fails, fix the artifacts before proceeding.
```

## Common Checks Library

| Check ID | Description | Applicable Skills |
|----------|-------------|-------------------|
| SCHEMA-COMPLIANCE | XML contains all required nodes | architecture-design, module-design |
| TRACEABILITY | Every output traces back to PRD requirement | All skills |
| SYNTAX-VALID | Generated code passes syntax check | project-scaffolding, module-design |
| INTERFACE-HONOR | Module-level interfaces match system-level interfaces | module-design |
| STATE-LIFECYCLE | Every state entry has location, owner, consumer, lifecycle | architecture-design, module-design |
| CROSS-MODULE-COMPAT | Output schemas match downstream input schemas | module-design (batch mode) |
| PRD-TRACEABILITY | Every user story cites at least one system-level requirement | requirement-analysis, module-design |
| NO-VAGUE-INTERFACE | No vague phrases like "returns data" or "handles errors" | architecture-design, module-design |
| ARTIFACT-INDEX | All generated artifacts are listed in INDEX.md | All skills |

## Usage

Each skill references checks from the Common Checks Library that apply to it, then adds any skill-specific checks. Do not repeat the full check description — reference by Check ID and add skill-specific details.
