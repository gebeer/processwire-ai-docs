---
name: processwire-agenttools
description: Provides ProcessWire CLI and migration workflows for sites using the AgentTools module. Use when querying the ProcessWire API, making repeatable site changes, or transferring changes across environments.
---

# ProcessWire AgentTools

CLI access to the ProcessWire API and a file based migration system.

The module registers `$at` as a ProcessWire API variable (`wire('at')`).

Use the wrapper script as the default interface:

```bash
bash .agents/skills/processwire-agenttools/scripts/pw-at.sh ...
```

## Commands

Run from the ProcessWire root directory (where `index.php` lives).

The wrapper auto-detects DDEV and runs inside the web container when appropriate.
It preserves Ryan's original AgentTools command model while adapting runtime details.
Do not call `php index.php --at-*` directly unless debugging the wrapper itself.

| Command | Purpose |
|---------|---------|
| `bash .agents/skills/processwire-agenttools/scripts/pw-at.sh eval 'CODE'` | Evaluate a PHP expression |
| `cat <<'PHP' \| bash .agents/skills/processwire-agenttools/scripts/pw-at.sh stdin` | Evaluate multi-line PHP from stdin |
| `bash .agents/skills/processwire-agenttools/scripts/pw-at.sh cli` | Open interactive CLI session |
| `bash .agents/skills/processwire-agenttools/scripts/pw-at.sh migrations-apply` | Apply all pending migrations |
| `bash .agents/skills/processwire-agenttools/scripts/pw-at.sh migrations-list` | List migrations and their status |
| `bash .agents/skills/processwire-agenttools/scripts/pw-at.sh migrations-test` | Preview pending without applying |

## Available API variables

All CLI modes and migrations share the same variables:
`$pages`, `$templates`, `$fields`, `$fieldgroups`, `$modules`, `$config`, `$sanitizer`, `$users`, `$roles`, `$permissions`, `$session`, `$database`, `$cache`, `$log`, `$files`, `$at`

## When to use Migrations vs CLI

For one-off reads or changes, use the CLI. For environment-transferable changes, create a migration. Use `--at-cli` only for multi-step interactive work.

## Wrapper behavior

The wrapper solves the runtime-selection problem for the agent:

- If DDEV is active for the project, it runs AgentTools via `ddev exec php index.php ...`
- Otherwise it runs host-side `php index.php ...`
- It uses `ddev exec --raw --` for inline argument modes so code is not flattened into a shell string inside the container
- It uses normal `ddev exec` for stdin mode because that transports piped code correctly
- The launcher is strategy-based and can be extended for other environments without changing the agent-facing command names

The wrapper supports transport helpers `eval-b64` and `stdin-b64` for execution
environments that rewrite `$...` in command strings. These are compatibility
helpers in the skill, not part of the original AgentTools API. Prefer plain
`eval` and `stdin` unless you hit transport problems.

The wrapper also supports environment overrides:

- `PW_AT_RUNNER=auto|host|ddev`
- `PW_AT_PHP_CMD=php`

When unsure, use the type of operation as a guide:

| Operation | Default path | Reason |
|-----------|-------------|--------|
| Queries, listing, inspecting | CLI | No state change to transfer |
| Templates, fields, fieldgroups | Migration | Schema should match across environments |
| Pages | Ask the user | Could be seed data or environment-specific |
| Module config, roles, permissions | Migration | Config drift causes subtle bugs |
| Debugging, testing | CLI | Throwaway by nature |

- **Running PHP against the ProcessWire API** (queries, one-off changes, testing) — read [cli.md](cli.md)
- **Creating migrations** (repeatable, transferable changes across environments) — read [migrations.md](migrations.md)
- **Validating the wrapper layer** (only when debugging portability/transport issues) — read [testing.md](testing.md)
