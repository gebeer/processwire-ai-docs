---
name: processwire-page-classes
description: Creates and manages ProcessWire custom page classes that extend Page with template-specific methods, properties, and logic. Covers naming conventions, get() overrides, API access patterns, inheritance, traits, and common pitfalls. Use when creating or editing custom page classes, adding template-specific methods to pages, or working with typed pages in ProcessWire.
---

# ProcessWire Custom Page Classes

Custom page classes let you extend ProcessWire's `Page` with template-specific methods and properties. Every page using a given template becomes an instance of your custom class.

## Quick Setup

1. Enable in `/site/config.php`:
```php
$config->usePageClasses = true;
```

2. Create class in `/site/classes/` matching the template name:

| Template | Class | File |
|----------|-------|------|
| `home` | `HomePage` | `HomePage.php` |
| `blog-post` | `BlogPostPage` | `BlogPostPage.php` |
| `basic-page` | `BasicPagePage` | `BasicPagePage.php` |

**Rule:** PascalCase the template name + append `Page`.

3. Minimal class:
```php
<?php namespace ProcessWire;
class HomePage extends Page {}
```

## Essential Pattern — Custom Properties via `get()`

```php
public function get($key) {
    if($key === 'authorName') return $this->getAuthorName();
    return parent::get($key);
}
```

This makes `$page->authorName` work as a virtual property.

## API Access Inside Classes

```php
// Correct — always use wire()
$this->wire()->pages->find('template=blog-post');
$this->wire()->sanitizer->text($value);

// Works but NOT preferred — not multi-instance safe
$this->pages;
pages()->find('template=product');
```

## Property Access Gotcha

Inside the class, `$this->property` accesses the property directly, bypassing the `get()` method's lazy loading. For example, `$this->template` may return `null`, while `$page->template` (external) returns a `Template` object. Use `$this->get('property')` for consistent behavior.

## Reference Files

- [Setup and naming conventions](setup-and-naming.md) — config options, naming rules, directory structure
- [Core patterns](core-patterns.md) — methods, PHPDoc, output formatting, admin labels
- [Inheritance and code reuse](inheritance-and-reuse.md) — inheritance, abstract classes, interfaces, traits
- [Advanced features](advanced-features.md) — helper classes, hookable methods, repeaters, DefaultPage, field classes
- [Best practices](best-practices.md) — do's, don'ts, common pitfalls
- [Complete examples](examples.md) — full working class implementations
