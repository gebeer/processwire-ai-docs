# Config Migrations Reference

Config migrations are individual PHP files that return arrays. They are automatically picked up by RockMigrations from the `site/RockMigrations/` directory.

## Table of Contents

- [Field Migrations](#field-migrations)
  - [Basic Structure](#basic-structure)
  - [Field Type Examples](#field-type-examples)
- [Template Migrations](#template-migrations)
- [Role Migrations](#role-migrations)
- [Permission Migrations](#permission-migrations)
- [Common Patterns](#common-patterns)

---

## Field Migrations

### Basic Structure

```php
// site/RockMigrations/fields/myfield.php
<?php namespace ProcessWire;

return [
    'type' => 'text',
    'label' => 'My Field Label',
    'description' => 'Help text',
    'columnWidth' => 50,
    'tags' => 'mytag',
];
```

### Field Type Examples

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
    'inputfield' => 'InputfieldSelect',
    'template_id' => 'my-template',
    'labelFieldName' => 'title',
    'derefAsPage' => FieldtypePage::derefAsPageArray,
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
    'maxFiles' => 0,
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

---

## Template Migrations

```php
// site/RockMigrations/templates/my-template.php
<?php namespace ProcessWire;

return [
    'fields' => [
        'title',
        'myfield' => [
            'label' => 'Override label',
            'columnWidth' => 50,
        ],
        'another_field',
    ],
    'parentTemplates' => ['parent-template'],
    'childTemplates' => ['child-template'],
    'noChildren' => 0,
    'noParents' => 0,
    'sortfield' => 'title',
    'pageClass' => 'MyPageClass',
    'tags' => 'mytag',
    'useRoles' => 1,
    'roles' => ['admin', 'editor'],
    'editRoles' => ['admin', 'editor'],
    'createRoles' => ['admin'],
    'addRoles' => ['admin'],
];
```

Fields listed as plain strings use default settings. Fields with array values get field-in-context overrides (label, columnWidth, etc.).

---

## Role Migrations

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

The filename becomes the role name. The returned array lists permissions assigned to the role.

---

## Permission Migrations

```php
// site/RockMigrations/permissions/my-permission.php
<?php namespace ProcessWire;

return 'Description of what this permission allows';
```

The filename becomes the permission name. The returned string is the permission description.

---

## Common Patterns

### Page Reference with Custom Label Format

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
```

### Template with Role-Based Access

```php
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
