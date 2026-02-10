# TracyDebugger

## CLI Usage

In CLI scripts, use `d()` for debug output:

```php
<?php namespace ProcessWire;
include(__DIR__ . '/../index.php');

$page = wire()->pages->get('/');
d($page, 'Home page');                          // dumps to terminal
d($page->getFields()->each('name'), 'Fields');  // dumps array to terminal
```

**Works in CLI:**
- `d($var, $title)` — dumps to terminal using `print_r()` for arrays/objects
- `TD::dump()` / `TD::dumpBig()` — same behavior

**Does NOT work in CLI:**
- `bd()` / `barDump()` — requires the browser debug bar

## Browser Snippets

PHP snippets placed in `site/templates/TracyDebugger/snippets/` can be executed from Tracy's Console panel in the browser. Full PW API is available without bootstrap.

**Conventions:**
- Always start with `<?php` and `namespace ProcessWire;`
- Use `bd($var, $title)` for output (displays in Tracy dump bar)
- Filename should describe the action (e.g., `importColors.php`, `setPassword.php`)

**Example** (`site/templates/TracyDebugger/snippets/listProducts.php`):
```php
<?php namespace ProcessWire;

$products = wire()->pages->find('template=product, limit=10');
foreach ($products as $p) {
    bd($p->title, "Product {$p->id}");
}
```
