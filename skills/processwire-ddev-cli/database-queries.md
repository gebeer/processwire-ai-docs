# Direct Database Queries

Use `database()` (returns `WireDatabasePDO`, a PDO wrapper) for raw SQL queries.

## Prepared Statements

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$query = database()->prepare("SELECT * FROM pages WHERE template = :tpl LIMIT 5");
$query->execute(['tpl' => 'product']);
$rows = $query->fetchAll(\PDO::FETCH_ASSOC);
```

## Simple Queries

```php
$result = database()->query("SELECT COUNT(*) FROM pages");
echo $result->fetchColumn();
```

## Key Methods

| Method | Purpose |
|--------|---------|
| `database()->prepare($sql)` | Prepared statement, use `:param` placeholders |
| `database()->query($sql)` | Direct query (no params) |
| `$query->execute(['param' => $value])` | Bind and execute |
| `$query->fetch(\PDO::FETCH_ASSOC)` | Single row |
| `$query->fetchAll(\PDO::FETCH_ASSOC)` | All rows |
| `$query->fetchColumn()` | Single value |

## Example: Query Module Data

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$query = database()->prepare("SELECT data FROM modules WHERE class = :class");
$query->execute(['class' => 'ProcessPageListerPro']);
$row = $query->fetch(\PDO::FETCH_ASSOC);
print_r(json_decode($row['data'], true));
```

## ddev Database Access

```bash
ddev mysql                                    # MySQL client
ddev exec -s db <cmd>                         # Run in database container
ddev exec --dir /var/www/html/site <cmd>      # Run from specific directory
```
