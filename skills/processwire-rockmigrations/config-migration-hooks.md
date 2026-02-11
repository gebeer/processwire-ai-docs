# Config Migration Hooks

RockMigrations executes migrations in a specific order. Hook files let you run custom code at different stages — essential for handling circular dependencies and module installation order.

## Execution Order

1. `beforeAssets.php` — before any fields/templates are created
2. **First run: create assets** (fields, templates exist but without settings)
3. `afterAssets.php` — assets exist but settings not yet applied
4. `beforeData.php` — before data migrations apply settings
5. **Second run: migrate data** (settings applied to fields/templates)
6. `afterData.php` — after all migrations complete

## Hook File Location

Place hook files directly in the `RockMigrations` directory:

```
site/RockMigrations/beforeAssets.php
site/RockMigrations/afterAssets.php
site/RockMigrations/beforeData.php
site/RockMigrations/afterData.php
```

Also works in module directories: `site/modules/[ModuleName]/RockMigrations/`.

## Circular Dependencies

When a page reference field needs a parent page whose template is created in the same migration:

1. Template migration file creates the template
2. `afterAssets.php` creates the parent page (template exists at this stage)
3. Field migration file creates the page reference field pointing to that parent

```php
// site/RockMigrations/afterAssets.php
<?php namespace ProcessWire;

$rm = rockmigrations();
$rm->createPage(
    template: 'my-template',
    parent: 1,
    name: 'my-parent-page',
    title: 'My Parent Page',
);
```

## Module Dependencies

Install fieldtype/inputfield modules before creating fields of that type:

```php
// site/RockMigrations/beforeAssets.php
<?php namespace ProcessWire;

$rm = rockmigrations();

// Disable config migrations to prevent endless loop
$rm->configMigrations(false);
wire()->modules->install('FieldtypeMyCustom');
wire()->modules->install('InputfieldMyCustom');
$rm->configMigrations(true);
```

Important: Wrap module installation in `configMigrations(false)` / `configMigrations(true)` to prevent recursive migration loops.
