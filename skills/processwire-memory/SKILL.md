---
name: processwire-memory
description: Stores and retrieves ProcessWire API patterns, gotchas, and conventions via memvid hybrid search. Use when starting PW tasks, hitting PW errors, or when user asks to remember/recall ProcessWire knowledge. Persistent cross-project memory at ~/.memvid/processwire.mv2
---

# memvid -- ProcessWire Cross-Project Memory

## Installation

```bash
npm install -g memvid-cli
```

If the memory file doesn't exist yet:
```bash
mkdir -p ~/.memvid && memvid create ~/.memvid/processwire.mv2
```

## When to Store vs Retrieve

### STORE proactively when you discover:
- Non-obvious PW API pattern, gotcha, or workaround
- Debugging insight involving ProcessWire internals
- PW convention or architectural decision worth preserving
- Correction to previously stored PW knowledge
- User requests storing a PW pattern

### RETRIEVE when:
- Starting any ProcessWire task
- Encountering a PW error or familiar pattern
- User asks "do we know..." or "have we seen..." about PW
- Before PW architectural decisions

### DO NOT store:
- Project-specific config (belongs in project CLAUDE.md)
- Transient debugging state (one-off errors)
- Non-PW knowledge (general PHP, CSS, JS patterns)

## URI Convention

```
mv2://processwire/topic/subtopic
```

| Topic | Example URIs |
|-------|-------------|
| api/pages | `mv2://processwire/api/pages` -- $pages API, PageArray, find/get |
| api/fields | `mv2://processwire/api/fields` -- field types, options, config |
| api/templates | `mv2://processwire/api/templates` -- template API, families |
| api/hooks | `mv2://processwire/api/hooks` -- hook system, priorities, events |
| api/modules | `mv2://processwire/api/modules` -- module API, autoload, config |
| selectors | `mv2://processwire/selectors` -- selector syntax, operators |
| templating | `mv2://processwire/templating` -- template files, rendering, regions |
| multilingual | `mv2://processwire/multilingual` -- LanguageSupport, translations |
| permissions | `mv2://processwire/permissions` -- access control, roles, pages |
| debugging | `mv2://processwire/debugging` -- common errors, diagnostics |
| patterns | `mv2://processwire/patterns` -- architectural patterns, conventions |
| migrations | `mv2://processwire/migrations` -- field/template setup, deployment |

Use `--scope` on retrieval to filter by URI prefix.

## Quick Reference

All commands use `$MV` for `~/.memvid/processwire.mv2` below. In actual commands, use the full path.

| Operation | Command |
|-----------|---------|
| **Store a learning** | `echo "content" \| memvid put $MV --embedding --uri mv2://processwire/topic --title "Short title" --tag source=project-name` |
| **Store from file** | `memvid put $MV --embedding --input file.md --uri mv2://processwire/topic --title "Title"` |
| **Bulk ingest (JSON)** | `cat batch.json \| memvid put-many $MV --dedup` (note: `put-many` does NOT support `--embedding`; use individual `put --embedding` per file for vector search, or pre-compute embeddings and include in batch JSON) |
| **Correct a mistake** | `memvid correct $MV "The correct statement" --topics "processwire,topic"` |
| **Search** | `memvid find $MV --query "search terms"` |
| **View memory cards** | `memvid memories $MV` |
| **Statistics** | `memvid stats $MV` |

### Search

Use `memvid find` for all queries — it runs true hybrid search (BM25 + vector).

**Do not use `ask`**: broken — ignores `--mode hybrid/sem`, falls back to pure Tantivy lexical with poor semantic recall.

## Storing Content

**Always pass `--embedding` on every `put` call** — required for hybrid search to work.

### Anatomy of a good memory entry

```bash
echo 'ProcessWire $pages->find() returns a PageArray even for zero results.
Use $pages->count() for existence checks instead of empty() on the result,
because PageArray is never falsy. Discovered debugging blank template output
where empty($results) was always false.' | \
memvid put ~/.memvid/processwire.mv2 \
  --embedding \
  --uri mv2://processwire/api/pages \
  --title "PageArray is never falsy - use count() for existence" \
  --tag source=walz-relaunch \
  --tag type=gotcha
```

Key qualities:
- **What**: the fact or pattern (PageArray never falsy)
- **Why it matters**: consequence of getting it wrong (blank output)
- **Context**: how it was discovered (debugging blank template)
- **URI**: scoped for retrieval (`mv2://processwire/api/pages`)
- **Tags**: enable filtering (`source`, `type`)

### Useful tag conventions

| Tag | Values |
|-----|--------|
| `type` | `gotcha`, `pattern`, `convention`, `fix`, `architecture`, `api` |
| `source` | project name where learned |
| `confidence` | `high`, `medium`, `low` |

### Storing corrections

When you discover previous PW knowledge was wrong:

```bash
memvid correct ~/.memvid/processwire.mv2 \
  "ProcessWire hook priority 100 runs AFTER default (0), not before. Higher number = later execution." \
  --topics "processwire,hooks,priority"
```

Corrections get a retrieval boost so they surface above the wrong information.

## Retrieving Content

### At session start in a PW project

```bash
memvid find ~/.memvid/processwire.mv2 --query "processwire" --top-k 5
```

### Scoped to a topic

```bash
memvid find ~/.memvid/processwire.mv2 --query "hooks" --scope mv2://processwire/api/hooks/
```

### When hitting a specific error

```bash
memvid find ~/.memvid/processwire.mv2 --query "SQLSTATE[HY000] template not found"
```

### Conceptual queries

Use keyword terms, not natural language — `find` is BM25-driven:

```bash
memvid find ~/.memvid/processwire.mv2 --query "multilingual page field language value"
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Storing without URI | Always pass `--uri` — unscoped content is hard to retrieve |
| Overly broad URI (`mv2://processwire/`) | Be specific: `mv2://processwire/api/pages` |
| Storing project-specific config | Belongs in project CLAUDE.md |
| Not searching before starting work | Retrieve at start of every PW task |
| Using `ask` | Broken — use `find` instead |
| Storing non-PW knowledge | PW-only memory. General PHP/CSS/JS doesn't belong |
| Storing without `--embedding` | Always pass `--embedding` on `put` (see `put-many` caveat in Quick Reference) |
