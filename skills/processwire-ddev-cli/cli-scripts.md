# CLI Scripts and One-Liners

## Script File Convention

All CLI scripts go in `./cli_scripts/`. Bootstrap PW with a relative include:

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

// PW API now available
$products = $pages->find('template=product');
foreach ($products as $p) {
    echo "{$p->id}: {$p->title}\n";
}
```

Run with:
```bash
ddev php cli_scripts/myscript.php
```

## One-Liners

Always use `ddev php -r` (not `ddev exec php -r` — exec adds an extra shell layer that can consume backslash escapes). Use the functions API to avoid bash `$` variable expansion issues:

```bash
# Count pages by template (functions API — no escaping needed)
ddev php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.'Products: '.pages()->count('template=product');"

# Check module status
ddev php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.(modules()->isInstalled('ProcessShop') ? 'yes' : 'no');"

# With PW API variables — must escape $ as \$
ddev php -r "namespace ProcessWire; include('./index.php'); echo \$pages->count('template=product');"

# List all templates (local var \$t also needs escaping)
ddev php -r "namespace ProcessWire; include('./index.php'); foreach(templates() as \$t) echo \$t->name.PHP_EOL;"
```

**Rules:**
- Always use `ddev php -r`, never `ddev exec php -r`
- `pages()`, `templates()`, `modules()` — safe, no `$` to escape
- PW API variables (`$pages`, `$config`, etc.) and local variables (`$t`) must be escaped as `\$` in bash
- Prefix output with `PHP_EOL` to separate from RockMigrations log noise

## Script Example: Inspect Fields

```php
// cli_scripts/inspect_fields.php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$p = pages()->get('/');
print_r($p->getFields()->each('name'));
```
```bash
ddev php cli_scripts/inspect_fields.php
```

## When to Use Scripts vs One-Liners

| Use case | Approach |
|----------|----------|
| Quick check (count, status) | One-liner |
| Complex queries, loops, output formatting | Script file |
| Reusable inspection/debugging | Script file |
