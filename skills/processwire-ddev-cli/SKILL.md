---
name: processwire-ddev-cli
description: Runs PHP scripts and one-liners via ddev in ProcessWire projects, bootstraps the PW API for CLI usage, debugs with TracyDebugger, creates Tracy console snippets, and executes direct database queries. Use when running PHP through ddev, writing ProcessWire CLI scripts, building Tracy debugger snippets, or querying the database in a ddev environment.
---

# ProcessWire CLI via ddev

All PHP CLI commands must run through ddev to use the web container's PHP interpreter.

## Basic Commands

```bash
ddev php script.php          # Run PHP script
ddev php --version           # Check PHP version
ddev exec php script.php     # Execute in web container
ddev ssh                     # Interactive shell
```

## Bootstrapping ProcessWire

Include `./index.php` from project root to get the full PW API (`$pages`, `$page`, `$config`, `$sanitizer`, etc.).

**All CLI script files must be placed in `./cli_scripts/`.**

```bash
# Inline one-liner (functions API — no $ escaping needed)
ddev php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.'Count: '.pages()->count('template=product');"

# With PW API variables — must escape $ as \$
ddev php -r "namespace ProcessWire; include('./index.php'); echo \$pages->count('template=product');"

# Local variables also need escaping
ddev php -r "namespace ProcessWire; include('./index.php'); foreach(templates() as \$t) echo \$t->name.PHP_EOL;"
```

```bash
# Script file
ddev php cli_scripts/myscript.php
```

**One-liner rules:**
- Always use `ddev php -r`, not `ddev exec php -r` (exec adds an extra shell layer that can consume backslash escapes)
- Use functions API (`pages()`, `templates()`, `modules()`) to avoid bash `$` variable expansion
- When you must use `$variables`, escape them as `\$var`
- Prefix output with `PHP_EOL` to separate from RockMigrations log noise

## Reference Files

- [CLI scripts and one-liners](cli-scripts.md) — conventions, examples, script file patterns
- [TracyDebugger](tracy-debugger.md) — CLI debugging with `d()`, browser snippets with `bd()`
- [Database queries](database-queries.md) — direct SQL via `database()` PDO wrapper, prepared statements
