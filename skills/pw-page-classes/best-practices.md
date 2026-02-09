# Best Practices

## Do

- **Keep classes focused** — each class handles logic for its template type only
- **Use PHPDoc** — document all fields and virtual properties for IDE support
- **Access API via `$this->wire()->apivar`** — prefer over `$this->wire('apivar')` (IDE can resolve the class) and over `$this->pages` / `pages()` (not multi-instance safe)
- **Use `parent::get($key)`** as fallback in `get()` overrides
- **Use type hints** in method signatures (`BlogPostPage $post`) for safety
- **Extend `DefaultPage`** instead of `Page` when you have shared base functionality
- **Use helper classes** for heavy functionality shared across many page instances

## Don't

- **Don't add hooks inside custom page classes** — hooks in constructors or methods get added every time a page is loaded, causing duplicates. Add hooks in `/site/init.php` or `/site/ready.php` instead.
- **Don't put markup/HTML in page classes** — keep all rendering in `/site/templates/`. Page classes provide data and logic, templates handle presentation.
- **Don't use page classes as general function libraries** — methods should be specific to the page type, not generic utilities (exception: a class used by only one page can serve as its function library)
- **Don't assume single instance** — your class will be instantiated for every page of that template. Think of each instance individually, not as a singleton.
- **Don't put admin-only or front-end-only logic in the class** — custom page classes are used in both front-end and admin contexts, so be wary of building logic specific to only one.
- **Don't use traits when you need `instanceof`** — traits don't support type checking. Use interfaces or abstract classes instead.

## Common Pitfalls

### `$this->field` vs `$this->get('field')`

Inside page classes, `$this->property` accesses the property directly, bypassing `get()` and its lazy loading. For example, `$this->template` may return `null`, while `$page->template` (external) returns a `Template` object. Use `$this->get('property')` for reliable access.

### Hook Duplication

```php
// WRONG — hook added once per page load
class ProductPage extends Page {
    public function __construct() {
        parent::__construct();
        $this->addHookAfter('saveReady', ...); // Multiplied per instance!
    }
}
```

```php
// CORRECT — hook added once in init.php
// /site/init.php
$wire->addHookBefore('ProductPage::saveReady', function($e) { ... });
```

### Heavy Objects in Page Classes

If your page class creates expensive objects, use the static helper pattern from [advanced features](advanced-features.md) to avoid creating one per page instance.

### Forgetting the Namespace

Every file in `/site/classes/` must start with:
```php
<?php namespace ProcessWire;
```

Without it, ProcessWire won't recognize the class.

## When NOT to Use Custom Page Classes

Custom page classes are optional. Skip them when:
- The site is simple with minimal custom logic
- You're just getting started with ProcessWire
- The logic can live entirely in template files
- You're prototyping and not ready to commit to a class structure
