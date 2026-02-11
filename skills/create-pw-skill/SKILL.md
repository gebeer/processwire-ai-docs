---
name: create-pw-skill
description: Guides the creation of a new ProcessWire agent skill from source material. Reads provided sources, proposes file structure and description, creates SKILL.md with reference files following best practices. Use when creating a new PW skill for this repository.
disable-model-invocation: true
user-invocable: true
argument-hint: "[source-paths-or-urls]"
---

# Create ProcessWire Skill

Workflow for creating a new ProcessWire agent skill from source material.

## Checklist

Copy and track progress:

```
- [ ] 1. Gather sources
- [ ] 2. Scope content with user
- [ ] 3. Read best practices
- [ ] 4. Propose file structure
- [ ] 5. Draft description
- [ ] 6. Create skill files
- [ ] 7. Output summary
```

## Workflow

### 1. Gather sources

Read all paths and URLs provided in $ARGUMENTS. Accept: file paths, directory paths, URLs (module docs, blog posts, API source code). If no arguments provided, ask the user for source material.

### 2. Scope content

Ask the user: extract all content from sources, or focus on specific parts? Wait for answer before proceeding.

### 3. Read best practices

Fetch and read the skill authoring best practices:
https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices

Focus on: description format, progressive disclosure, line limits, file structure.

### 4. Propose file structure

Study existing skills in `skills/` for conventions. Then propose a directory layout:

```
skills/processwire-[name]/
├── SKILL.md              # Overview + quick start + reference links
├── [topic-a].md          # Reference file per major topic
└── [topic-b].md          # ...
```

Present to user for approval. Do not proceed until approved.

### 5. Draft description

Draft the YAML `description` field:
- Third-person verb phrases (Creates, Manages, Generates...)
- Include specific trigger terms and keywords for discovery
- End with "Use when..." clause listing concrete scenarios
- Max 1024 characters

Show description to user for review before proceeding.

### 6. Create skill files

Create the skill directory and files under `skills/`.

Rules:
- Naming: `processwire-*` (lowercase, hyphens only)
- SKILL.md body: under 500 lines, aim for 50-70
- Reference files: one level deep from SKILL.md, descriptive filenames
- Table of contents required in files over 100 lines
- Code examples: always include `namespace ProcessWire`
- Do NOT include knowledge the assistant already has (basic PHP, general programming)

### 7. Output summary

When finished, output:

```
## Created Skill: processwire-[name]

### Sources
- [list each source path or URL used]

### Files
- skills/processwire-[name]/SKILL.md
- skills/processwire-[name]/[reference-files...]
```

This summary serves as input for `/verify-pw-skill`.
