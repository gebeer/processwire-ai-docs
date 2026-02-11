# Setup and Naming Conventions

## Enabling Custom Page Classes

In `/site/config.php`:
```php
$config->usePageClasses = true;
```

Without this, ProcessWire ignores all custom page class files.

## Directory

All custom page classes live in `/site/classes/`.

## Namespace

Every file must declare the ProcessWire namespace:
```php
<?php namespace ProcessWire;
```

## Naming Convention

Template name is converted to PascalCase, then `Page` is appended.

| Template Name | Class Name | Filename |
|---------------|------------|----------|
| `home` | `HomePage` | `HomePage.php` |
| `product` | `ProductPage` | `ProductPage.php` |
| `blog-post` | `BlogPostPage` | `BlogPostPage.php` |
| `basic-page` | `BasicPagePage` | `BasicPagePage.php` |

**Hyphenated templates:** each segment is capitalized. `blog-post` becomes `BlogPost`, then add `Page` = `BlogPostPage`.

## Built-in System Classes

You can also extend built-in system page types. These use specific base classes:

| Template | Class Name | Extends | Filename |
|----------|------------|---------|----------|
| `user` | `UserPage` | `User` | `UserPage.php` |
| `permission` | `PermissionPage` | `Permission` | `PermissionPage.php` |
| `role` | `RolePage` | `Role` | `RolePage.php` |
| `language` | `LanguagePage` | `Language` | `LanguagePage.php` |

```php
<?php namespace ProcessWire;
class UserPage extends User {
    public function getFullName(): string {
        return "$this->first_name $this->last_name";
    }
}
```

## Repeater and Fieldset Classes

Special field types have their own base classes:

| Type | Base Class | Naming Example |
|------|------------|----------------|
| Repeater | `RepeaterPage` | `QuotesRepeaterPage` |
| Repeater Matrix | `RepeaterMatrixPage` | `BlocksRepeaterMatrixPage` |
| Fieldset Page | `FieldsetPage` | `SeoFieldsetPage` |

```php
<?php namespace ProcessWire;
class QuotesRepeaterPage extends RepeaterPage {
    // Custom methods for repeater items
}
```
