# RockMigrations API Methods

## Table of Contents

- [Setup](#setup)
- [createField](#createfield)
- [createTemplate](#createtemplate)
- [createRole](#createrole)
- [createPermission](#createpermission)
- [createPage](#createpage)
- [migrate (Combined)](#migrate-combined)
- [Module Installation](#module-installation)
- [Other Useful Methods](#other-useful-methods)

---

For complex migrations, use the API in `site/modules/Site/Site.migrate.php`.

## Setup

```php
<?php namespace ProcessWire;

/** @var RockMigrations $rm */
$rm = $this->wire->modules->get('RockMigrations');
```

## createField

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

## createTemplate

```php
$rm->createTemplate('my-template', [
    'fields' => ['title', 'body', 'images'],
    'parentTemplates' => ['home'],
]);
```

## createRole

```php
$rm->createRole('editor', [
    'page-view',
    'page-edit',
    'page-add',
]);
```

## createPermission

```php
$rm->createPermission('my-custom-permission', 'Allows custom action');
```

## createPage

```php
$rm->createPage(
    template: 'my-template',
    parent: 1,
    name: 'page-name',
    title: 'Page Title',
    status: [Page::statusHidden],
    data: ['fieldname' => 'value'],
);
```

## migrate (Combined)

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

## Module Installation

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

## Other Useful Methods

```php
// Add/remove field from template
$rm->addFieldToTemplate('fieldname', 'templatename');
$rm->removeFieldFromTemplate('fieldname', 'templatename');
$rm->removeFieldsFromTemplate(['field1', 'field2'], 'templatename');

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
