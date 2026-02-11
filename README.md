# ProcessWire AI Docs

A collection of Agent Skills for [ProcessWire](https://processwire.com/) development with AI coding agents.

## What Are Agent Skills?

Agent Skills are an open standard for giving AI coding agents domain-specific knowledge and capabilities. They are supported by Claude Code, Cursor, VS Code (Copilot), Windsurf, Gemini CLI, and [many more](https://agentskills.io/integrate-skills).

Each skill is a folder containing a `SKILL.md` file with instructions and optional reference files that agents load on demand. See the [integration guide](https://agentskills.io/integrate-skills) for how to install skills in your AI tool.

## Available Skills

- **[processwire-ddev-cli](skills/processwire-ddev-cli/SKILL.md)** — PHP CLI via ddev: running scripts, bootstrapping the PW API, debugging with TracyDebugger, and direct database queries.
- **[processwire-page-classes](skills/processwire-page-classes/SKILL.md)** — Custom page classes: extending the Page class with template-specific logic, naming conventions, inheritance patterns, helper classes, and best practices.
- **[processwire-rockmigrations](skills/processwire-rockmigrations/SKILL.md)** — Schema migrations via RockMigrations: fields, templates, roles, permissions, MagicPages, and PageClass lifecycle hooks.

## Contributing Skills

Want to add a skill for a ProcessWire module or workflow? Two helper skills are included to streamline the process:

- **[create-pw-skill](skills/create-pw-skill/SKILL.md)** — Guided workflow for creating a new skill from source material (module docs, blog posts, API code).
- **[verify-pw-skill](skills/verify-pw-skill/SKILL.md)** — Verifies a skill's accuracy and best practices compliance against source material.

To contribute, fork this repo, use `/create-pw-skill` with your source material, then open a PR. Skills follow the [Agent Skills best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).
