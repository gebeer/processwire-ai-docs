---
name: pw-ddev-cli
description: ProcessWire CLI usage via ddev — running PHP scripts, bootstrapping the PW API, debugging with Tracy, and direct database queries. Use when executing PHP in a ProcessWire ddev project, writing CLI scripts, or querying the database directly.
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
# Inline one-liner
ddev exec php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.'Count: '.pages()->count('template=product');"
```

```bash
# Script file
ddev php cli_scripts/myscript.php
```

**One-liner rules:**
- Use functions API (`pages()`, `templates()`, `modules()`) to avoid bash `$` variable expansion
- Escape local variables with `\$var`
- Prefix output with `PHP_EOL` to separate from RockMigrations log noise

## Reference Files

- [CLI scripts and one-liners](cli-scripts.md) — conventions, examples, script file patterns
- [TracyDebugger](tracy-debugger.md) — CLI debugging with `d()`, browser snippets with `bd()`
- [Database queries](database-queries.md) — direct SQL via `database()` PDO wrapper, prepared statements
