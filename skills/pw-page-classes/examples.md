# Complete Examples

## Blog Post Page

```php
<?php namespace ProcessWire;

/**
 * Blog Post Page — /site/classes/BlogPostPage.php
 * Template: blog-post
 *
 * @property string $title
 * @property string $summary
 * @property string $body
 * @property string $date
 * @property User|NullPage $author
 * @property PageArray|CategoryPage[] $categories
 * @property-read string $authorName
 * @property-read int $numWords
 */
class BlogPostPage extends Page {

    public function get($key) {
        if($key === 'authorName') return $this->getAuthorName();
        if($key === 'numWords') return $this->numWords();
        return parent::get($key);
    }

    public function getAuthorName(): string {
        $u = $this->createdUser;
        return "$u->first_name $u->last_name";
    }

    public function numWords(): int {
        return str_word_count(strip_tags($this->body));
    }

    public function getExcerpt(): string {
        $excerpt = $this->summary;
        if(empty($excerpt)) {
            $excerpt = $this->wire()->sanitizer->truncate($this->body);
        }
        return $excerpt;
    }

    public function getRelatedPosts(int $limit = 3): PageArray {
        return $this->wire()->pages->find([
            'template' => 'blog-post',
            'categories' => $this->categories,
            'sort' => '-date',
            'id!=' => $this->id,
            'limit' => $limit
        ]);
    }

    public function getPageListLabel(): string {
        $title = $this->getFormatted('title');
        $date = $this->getFormatted('date');
        return "<strong>$title</strong> <small>$date</small>";
    }
}
```

## Product Page with Helper Class

```php
<?php namespace ProcessWire;

/**
 * Product Page Orders Helper — /site/classes/ProductPageOrders.php
 */
class ProductPageOrders extends Wire {

    public function getOrders(ProductPage $product): array {
        return $this->wire()->database->query(
            "SELECT * FROM orders WHERE product_id=:id",
            [':id' => $product->id]
        )->fetchAll();
    }

    public function getOrderCount(ProductPage $product): int {
        return count($this->getOrders($product));
    }
}

/**
 * Product Page — /site/classes/ProductPage.php
 * Template: product
 *
 * @property string $title
 * @property float $price
 * @property string $sku
 * @property int $stock
 * @property PageArray|CategoryPage[] $categories
 * @property-read int $orderCount
 */
class ProductPage extends Page {

    static protected $orders = null;

    public function get($key) {
        if($key === 'orderCount') return $this->orders()->getOrderCount($this);
        return parent::get($key);
    }

    protected function orders(): ProductPageOrders {
        if(self::$orders === null) {
            self::$orders = new ProductPageOrders();
        }
        return self::$orders;
    }

    public function getOrders(): array {
        return $this->orders()->getOrders($this);
    }

    public function isInStock(): bool {
        return $this->stock > 0;
    }

    public function getFormattedPrice(): string {
        return '$' . number_format($this->price, 2);
    }
}
```

## Abstract Content Base with Implementations

```php
<?php namespace ProcessWire;

/**
 * Abstract Content Page — /site/classes/ContentPage.php
 * Not tied to a template. Base for all content types.
 *
 * @property string $title
 * @property string $body
 */
abstract class ContentPage extends Page {

    abstract public function getExcerpt(): string;

    public function getReadingTime(): int {
        return (int) ceil(str_word_count(strip_tags($this->body)) / 200);
    }

    public function getPageListLabel(): string {
        $title = $this->getFormatted('title');
        $time = $this->getReadingTime();
        return "<strong>$title</strong> <small>{$time} min read</small>";
    }
}
```

```php
<?php namespace ProcessWire;

/**
 * Article Page — /site/classes/ArticlePage.php
 * Template: article
 *
 * @property string $subtitle
 */
class ArticlePage extends ContentPage {
    public function getExcerpt(): string {
        return $this->wire()->sanitizer->truncate($this->body, 200);
    }
}
```

```php
<?php namespace ProcessWire;

/**
 * News Page — /site/classes/NewsPage.php
 * Template: news
 *
 * @property string $source_url
 */
class NewsPage extends ContentPage {
    public function getExcerpt(): string {
        return $this->summary ?: $this->wire()->sanitizer->truncate($this->body, 150);
    }
}
```

## Interface Example — Tour Pages

```php
<?php namespace ProcessWire;

interface TourPage {
    public function getDepartures(int $month, int $year): array;
    public function getTourType(): string;
}
```

```php
<?php namespace ProcessWire;

/**
 * @property string $title
 * @property string $body
 * @property float $price_per_person
 */
class BoatTourPage extends Page implements TourPage {
    public function getDepartures(int $month, int $year): array {
        return $this->wire()->pages->find([
            'template' => 'departure',
            'parent' => $this,
            'month' => $month,
            'year' => $year
        ])->getArray();
    }

    public function getTourType(): string {
        return 'boat';
    }
}
```

## Custom User Page

```php
<?php namespace ProcessWire;

/**
 * User Page — /site/classes/UserPage.php
 * Extends the built-in User class (not Page).
 *
 * @property string $first_name
 * @property string $last_name
 * @property string $company
 */
class UserPage extends User {

    public function getFullName(): string {
        $name = trim("$this->first_name $this->last_name");
        return $name ?: $this->name;
    }

    public function getDisplayName(): string {
        $full = $this->getFullName();
        if($this->company) {
            return "$full ($this->company)";
        }
        return $full;
    }
}
```

## DefaultPage with Shared Utility

```php
<?php namespace ProcessWire;

/**
 * Default Page — /site/classes/DefaultPage.php
 * Fallback for all templates without a custom class.
 */
class DefaultPage extends Page {

    public function getLastModified(): string {
        return wireRelativeTimeStr($this->modified) .
            ' by ' . $this->modifiedUser->name;
    }

    public function getBreadcrumbs(): PageArray {
        return $this->parents();
    }
}
```

Then all other page classes inherit these methods:
```php
class HomePage extends DefaultPage {}
class ContactPage extends DefaultPage {}
```
