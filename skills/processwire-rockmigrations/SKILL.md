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

---

## Config Migrations (Preferred)

Config migrations are individual PHP files that return arrays. They're automatically picked up by RockMigrations.

### Field Migration
```php
// site/RockMigrations/fields/myfield.php
<?php namespace ProcessWire;

return [
    'type' => 'text',           // or 'textarea', 'page', 'image', 'file', 'options', etc.
    'label' => 'My Field Label',
    'description' => 'Help text',
    'columnWidth' => 50,
    'tags' => 'mytag',
    // Field-specific options below
];
```

**Common field types and options:**

```php
// Text field
return ['type' => 'text', 'label' => 'Title', 'maxlength' => 255];

// Textarea field
return ['type' => 'textarea', 'label' => 'Description', 'rows' => 5];

// CKEditor field
return [
    'type' => 'textarea',
    'inputfieldClass' => 'InputfieldCKEditor',
    'contentType' => FieldtypeTextarea::contentTypeHTML,
];

// Page reference field
return [
    'type' => 'FieldtypePage',
    'inputfield' => 'InputfieldSelect',      // or InputfieldAsmSelect, InputfieldPageAutocomplete
    'template_id' => 'my-template',          // template name or ID
    'labelFieldName' => 'title',
    'derefAsPage' => FieldtypePage::derefAsPageArray,  // or derefAsPageOrFalse
];

// Image field
return [
    'type' => 'image',
    'maxFiles' => 1,
    'extensions' => 'jpg jpeg png gif svg',
    'outputFormat' => FieldtypeFile::outputFormatSingle,
];

// File field
return [
    'type' => 'file',
    'maxFiles' => 0,  // 0 = unlimited
    'extensions' => 'pdf doc docx',
];

// Options field
return [
    'type' => 'options',
    'options' => [
        1 => 'VALUE1|Label One',
        2 => 'VALUE2|Label Two',
        3 => 'VALUE3|Label Three',
    ],
];

// Checkbox field
return ['type' => 'checkbox', 'label' => 'Is Active'];

// Datetime field
return [
    'type' => 'datetime',
    'dateInputFormat' => 'j.n.Y',
    'datepicker' => InputfieldDatetime::datepickerFocus,
];
```

### Template Migration
```php
// site/RockMigrations/templates/my-template.php
<?php namespace ProcessWire;

return [
    'fields' => [
        'title',
        'myfield' => [
            'label' => 'Override label',    // field-in-context settings
            'columnWidth' => 50,
        ],
        'another_field',
    ],
    'parentTemplates' => ['parent-template'],
    'childTemplates' => ['child-template'],
    'noChildren' => 0,                      // 1 = no children allowed
    'noParents' => 0,                       // 1 = only one page with this template
    'sortfield' => 'title',                 // or '-created', 'sort', etc.
    'pageClass' => 'MyPageClass',           // custom PageClass
    'tags' => 'mytag',

    // Access control
    'useRoles' => 1,
    'roles' => ['admin', 'editor'],
    'editRoles' => ['admin', 'editor'],
    'createRoles' => ['admin'],
    'addRoles' => ['admin'],
];
```

### Role Migration
```php
// site/RockMigrations/roles/myrole.php
<?php namespace ProcessWire;

return [
    'page-view',
    'page-edit',
    'page-add',
    'page-delete',
    'page-sort',
    'profile-edit',
];
```

### Permission Migration
```php
// site/RockMigrations/permissions/my-permission.php
<?php namespace ProcessWire;

return 'Description of what this permission allows';
```

---

## Direct API (Site.migrate.php)

For complex migrations or operations not suited for config files, use the API in `site/modules/Site/Site.migrate.php`:

```php
<?php namespace ProcessWire;

/** @var RockMigrations $rm */
$rm = $this->wire->modules->get('RockMigrations');
```

### Create Field
```php
$rm->createField('fieldname', 'text', [
    'label' => 'My Field',
    'tags' => 'mytag',
]);

// Or with type in options
$rm->createField('fieldname', [
    'type' => 'textarea',
    'label' => 'My Field',
]);
```

### Create Template
```php
$rm->createTemplate('my-template', [
    'fields' => ['title', 'body', 'images'],
    'parentTemplates' => ['home'],
]);
```

### Create Role
```php
$rm->createRole('editor', [
    'page-view',
    'page-edit',
    'page-add',
]);
```

### Create Permission
```php
$rm->createPermission('my-custom-permission', 'Allows custom action');
```

### Create Page
```php
$rm->createPage(
    template: 'my-template',
    parent: 1,                              // parent ID or path like '/about/'
    name: 'page-name',                      // optional, auto-generated from title
    title: 'Page Title',
    status: [Page::statusHidden],           // optional
    data: ['fieldname' => 'value'],         // optional field values
);
```

### Combined Migration
```php
$rm->migrate([
    'fields' => [
        'myfield' => ['type' => 'text', 'label' => 'My Field'],
    ],
    'templates' => [
        'my-template' => [
            'fields' => ['title', 'myfield'],
        ],
    ],
    'roles' => [
        'myrole' => [
            'permissions' => ['page-view', 'page-edit'],
        ],
    ],
    'pages' => [
        [
            'template' => 'my-template',
            'parent' => 1,
            'title' => 'My Page',
        ],
    ],
]);
```

### Module Installation
```php
// Install from GitHub
$rm->installModule(
    "ModuleName",
    "https://github.com/user/repo/archive/refs/heads/main.zip"
);

// Install core module
$rm->installModule("PagePathHistory");

// Uninstall
$rm->uninstallModule("ModuleName");

// Delete module files
$rm->deleteModule("ModuleName");
```

### Other Useful Methods
```php
// Add/remove field from template
$rm->addFieldToTemplate('fieldname', 'templatename');
$rm->removeFieldFromTemplate('fieldname', 'templatename');

// Set field data
$rm->setFieldData('fieldname', ['label' => 'New Label']);

// Set template data
$rm->setTemplateData('templatename', ['sortfield' => 'title']);

// Delete field/template
$rm->deleteField('fieldname');
$rm->deleteTemplate('templatename');

// Add permission to role
$rm->addPermissionToRole('permission-name', 'rolename');
$rm->removePermissionFromRole('permission-name', 'rolename');
```

---

## MagicPages (PageClass Migrations)

Custom PageClasses can define their own migrations and lifecycle hooks.

**Available hooks** (require `use MagicPage` trait):
- `onCreate()` - Called when page object is created, before first save.
- `onAdded()` - Called after page is saved for the first time.
- `onSaved()` - Called every time the page is saved.
- `onSaveReady()` - Called before save, allows modifying data before it's written.

```php
// site/classes/ProductPage.php
<?php namespace ProcessWire;

use RockMigrations\MagicPage;

class ProductPage extends Page
{
    use MagicPage;

    const tpl = 'product';  // template name

    public function migrate()
    {
        $rm = $this->rockmigrations();
        $rm->migrate([
            'fields' => [
                'price' => ['type' => 'float', 'label' => 'Price'],
                'sku' => ['type' => 'text', 'label' => 'SKU'],
            ],
            'templates' => [
                self::tpl => [
                    'fields' => ['title', 'price', 'sku', 'body', 'images'],
                ],
            ],
        ]);
    }
}
```

---

## Tips

1. **Prefer config migrations** over inline API calls for maintainability
2. **One file per asset** - easier to track changes in git
3. **Use tags** to organize fields/templates by module or feature
4. **Check existing patterns** in `site/RockMigrations/` for project conventions
5. **For undocumented methods**, explore `site/modules/RockMigrations/RockMigrations.module.php`

---

## Common Patterns in This Project

Based on existing migrations:

```php
// Page reference field (site/RockMigrations/fields/sales_partner.php)
return [
    'type' => 'FieldtypePage',
    'inputfield' => 'InputfieldSelect',
    'template_id' => 'sales-partner',
    'labelFieldFormat' => '{uid} - {title}',
    'labelFieldName' => '.',
    'tags' => 'sales-partners',
];

// Template with roles (site/RockMigrations/templates/customer.php)
return [
    'fields' => [
        'title',
        'email' => [],
        'phone' => [],
    ],
    'parentTemplates' => ['customers'],
    'noChildren' => 1,
    'useRoles' => 1,
    'editRoles' => ['admin', 'customer'],
    'createRoles' => ['admin'],
];
```
