# Advanced Features

## DefaultPage Class

`DefaultPage` is a fallback class for templates that don't have their own custom class. Create it at `/site/classes/DefaultPage.php`:

```php
<?php namespace ProcessWire;

class DefaultPage extends Page {
    public function getLastModified(): string {
        return wireRelativeTimeStr($this->modified) .
            ' by ' . $this->modifiedUser->name;
    }
}
```

Other custom page classes can extend `DefaultPage` instead of `Page` to inherit shared functionality:

```php
class HomePage extends DefaultPage {}
class ProductPage extends DefaultPage {}
```

## Hookable Methods

Make methods hookable by prefixing with `___` (three underscores) and documenting with `@method`:

```php
/**
 * @method string hello()
 */
class HelloPage extends Page {
    public function ___hello(): string {
        return 'hello';
    }
}
```

Now external code can hook before/after:

```php
$wire->addHookAfter('HelloPage::hello', function($e) {
    $e->return .= ' world';
});
```

### Hooking Page Events

Hook into page lifecycle events scoped to your class:

```php
$wire->addHookBefore('ProductPage::saveReady', function($e) {
    $product = $e->object; /** @var ProductPage $product */
    if(empty($product->sku)) {
        $product->sku = $product->generateSku();
    }
});
```

### Adding Methods via Hooks

Attach new methods to a class from outside:

```php
$wire->addHook('BlogPostPage::world', function($e) {
    $e->return = "it's a small world";
});
// Now $blogPost->world() works
```

## Helper Classes Pattern

For classes with heavy functionality, delegate to a shared single-instance helper to avoid duplicating objects across many pages:

```php
class ProductPageOrders extends Wire {
    public function getOrders(ProductPage $product): array {
        // Heavy logic here
    }
    public function addOrder(ProductPage $product, array $info): bool {
        // Order creation logic
    }
}

class ProductPage extends Page {
    static protected $orders = null;

    protected function orders(): ProductPageOrders {
        if(self::$orders === null) {
            self::$orders = new ProductPageOrders();
        }
        return self::$orders;
    }

    public function getOrders(): array {
        return $this->orders()->getOrders($this);
    }

    public function addOrder(array $info): bool {
        return $this->orders()->addOrder($this, $info);
    }
}
```

**Why:** If you have 100 product pages loaded, the `ProductPageOrders` instance is created only once (static), not 100 times. The helper extends `Wire` so it has access to the PW API.

## Field Value Classes

Document multi-value field types (like ProFields Combo) for IDE support:

```php
// /site/classes/fields/SeoValue.php
<?php namespace ProcessWire;

/**
 * @property string $browser_title
 * @property string $meta_description
 * @property bool|int $noindex
 */
class SeoValue extends ComboValue {}
```

Then reference in your page class:

```php
/**
 * @property SeoValue $seo
 */
class DestinationPage extends Page {}
```

Now `$page->seo->browser_title` gets full IDE autocomplete.

### Table Field Values

For ProFields Table, use `TableRow`:

```php
// /site/classes/fields/QuotesTableRow.php
<?php namespace ProcessWire;

/**
 * @property string $quote
 * @property string $cite
 * @property string $date
 */
class QuotesTableRow extends TableRow {}
```

Reference as array in page class:
```php
/**
 * @property QuotesTableRow[] $quotes
 * @property SeoValue $seo
 */
class DestinationPage extends Page {}
```

## Repeater Custom Classes

Add methods to repeater items:

```php
<?php namespace ProcessWire;

/**
 * @property string $quote
 * @property string $cite
 * @property string $source_url
 */
class QuotesRepeaterPage extends RepeaterPage {
    public function getCitation(): string {
        $cite = $this->cite;
        if($this->source_url) {
            $cite = "<a href='{$this->source_url}'>$cite</a>";
        }
        return $cite;
    }
}
```

For Repeater Matrix fields, extend `RepeaterMatrixPage` instead.
