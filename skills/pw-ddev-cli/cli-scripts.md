# CLI Scripts and One-Liners

## Script File Convention

All CLI scripts go in `./cli_scripts/`. Bootstrap PW with a relative include:

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

// PW API now available
$products = wire()->pages->find('template=product');
foreach ($products as $p) {
    echo "{$p->id}: {$p->title}\n";
}
```

Run with:
```bash
ddev php cli_scripts/myscript.php
```

## One-Liners

Use `ddev php -r` with `wire()->...` to avoid bash `$` variable expansion issues:

```bash
# Count pages by template
ddev php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.'Products: '.wire()->pages->count('template=product');"

# Check module status
ddev php -r "namespace ProcessWire; include('./index.php'); echo PHP_EOL.(wire()->modules->isInstalled('ProcessShop') ? 'yes' : 'no');"

# List all templates (note \$t escaping for local var)
ddev php -r "namespace ProcessWire; include('./index.php'); foreach(wire()->templates as \$t) echo \$t->name.PHP_EOL;"
```

**Rules:**
- `wire()->pages`, `wire()->templates`, `wire()->modules` — safe, no `$` to escape, works when functions API is disabled
- Local variables like `$t` must be escaped as `\$t` in bash
- Prefix output with `PHP_EOL` to separate from RockMigrations log noise

## Script Example: Inspect Fields

```php
// cli_scripts/inspect_fields.php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$p = wire()->pages->get('/');
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
