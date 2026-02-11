---
name: processwire-rockmigrations
description: Creates and manages ProcessWire schema migrations via RockMigrations — fields, templates, roles, permissions, pages, and modules. Supports config migrations, direct API methods, MagicPages, and PageClass lifecycle hooks (onCreate, onAdded, onSaved). Use when creating or modifying fields, templates, roles, permissions, running migrations, setting up access control, or working with MagicPage and PageClass hooks in ProcessWire.
---

# RockMigrations Reference

RockMigrations provides version-controlled migrations for ProcessWire CMS. All migrations define fields, templates, roles, permissions, and pages via PHP code.

## File Locations

| Type | Location |
|------|----------|
| Config migrations | `site/RockMigrations/{fields,templates,roles,permissions}/[name].php` |
| Site migrations | `site/modules/Site/Site.migrate.php` |
| Module source | `site/modules/RockMigrations/RockMigrations.module.php` |

## Execution

```bash
# Run all migrations via ddev
ddev php site/modules/RockMigrations/migrate.php
```

Migrations also auto-run on module refresh in the ProcessWire backend.

## Quick Start — Config Field Migration

```php
// site/RockMigrations/fields/myfield.php
<?php namespace ProcessWire;

return [
    'type' => 'text',
    'label' => 'My Field Label',
    'tags' => 'mytag',
];
```

## Tips

1. **Prefer config migrations** over inline API calls for maintainability
2. **One file per asset** — easier to track changes in git
3. **Use tags** to organize fields/templates by module or feature
4. **Check existing patterns** in `site/RockMigrations/` for project conventions
5. **For undocumented methods**, explore `site/modules/RockMigrations/RockMigrations.module.php`

## Reference Files

- [Config migrations](config-migrations.md) — field types, template definitions, roles, permissions, and common patterns
- [Config migration hooks](config-migration-hooks.md) — execution order, circular dependencies, module dependency installation
- [API methods](api-methods.md) — direct API via `Site.migrate.php`: createField, createTemplate, createPage, combined migrations, module installation
- [MagicPages](magic-pages.md) — lifecycle hooks, Magic Methods, Magic Field Methods, Magic Assets, trait utilities
- [init() and ready()](init-ready.md) — attaching hooks directly in PageClasses instead of ready.php
