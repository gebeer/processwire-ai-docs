---
name: verify-pw-skill
description: Verifies an existing ProcessWire agent skill for accuracy and best practices compliance against source material. Runs parallel checks per reference file, produces an issue table with severity levels, and applies only user-approved fixes. Use when verifying or auditing a PW skill in this repository.
disable-model-invocation: true
user-invocable: true
argument-hint: "[skill-path] [source-paths-or-urls]"
---

# Verify ProcessWire Skill

Verify an existing ProcessWire skill's accuracy and best practices compliance against source material.

## Input

Accepts either:
1. **Same session**: output from `/create-pw-skill` (sources + file structure already in context)
2. **Manual**: skill path + source paths/URLs via $ARGUMENTS

If neither available, ask the user for the skill path and source material.

## Checklist

Copy and track progress:

```
- [ ] 1. Identify inputs
- [ ] 2. Read best practices
- [ ] 3. Run parallel verification
- [ ] 4. Compile issue table
- [ ] 5. Present to user
- [ ] 6. Apply approved fixes
```

## Workflow

### 1. Identify inputs

Determine the skill directory and list its files (SKILL.md + reference files). Confirm all source material (paths, URLs) with the user.

### 2. Read best practices

Fetch and read:
https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

Focus on: description format, progressive disclosure, line limits, file structure, naming conventions.

### 3. Run parallel verification

Launch one subagent per reference file. Each subagent reads its file and the relevant source material, checking three dimensions:

**Accuracy**
- Method signatures match source (argument names, order, types)
- Property names and values are correct
- Code syntax is valid (`namespace ProcessWire`, correct API patterns)
- No contradictions with source

**Coverage**
- Important features present in source but missing from skill
- Missing common patterns or pitfalls documented in source

**Best practices**
- SKILL.md is lean overview with progressive disclosure to reference files
- Description: third-person verbs, trigger terms, "Use when..." clause
- SKILL.md body under 500 lines
- Table of contents in files over 100 lines
- Reference files one level deep, descriptive filenames
- No info the assistant already knows (basic PHP, etc.)

Also verify SKILL.md frontmatter (name matches directory, description under 1024 chars).

### 4. Compile issue table

Merge all subagent results into a single table sorted by severity:

| File | Issue | Severity | Details |
|------|-------|----------|---------|
| ... | ... | inaccuracy | ... |
| ... | ... | coverage-gap | ... |
| ... | ... | style | ... |

### 5. Present to user

Display the issue table. **Stop here.** Do not make any changes. Wait for the user to review and specify which issues to address.

### 6. Apply approved fixes

Fix only the issues the user approves. Do not make any other changes.

## Rules

- Always verify method signatures against actual source code, not just documentation
- Use parallel subagents (one per reference file) for efficiency
- Never auto-fix without explicit user approval
- If a skill has no reference files (only SKILL.md), run a single verification pass
