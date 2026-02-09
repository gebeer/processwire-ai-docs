# Inheritance and Code Reuse

## Basic Inheritance

A custom page class can extend another custom page class instead of `Page`:

```php
class ArticlePage extends Page {
    public function getExcerpt(): string {
        return $this->wire()->sanitizer->truncate($this->body);
    }
}

class BlogPostPage extends ArticlePage {
    // Inherits getExcerpt() and can add more
    public function getRelatedPosts(): PageArray {
        return $this->wire()->pages->find([
            'template' => 'blog-post',
            'id!=' => $this->id
        ]);
    }
}
```

## Abstract Base Classes

Use abstract classes when multiple templates share behavior but you don't want the base instantiated directly:

```php
abstract class ContentPage extends Page {
    abstract public function getExcerpt(): string;

    public function getReadingTime(): int {
        return (int) ceil(str_word_count(strip_tags($this->body)) / 200);
    }
}

class ArticlePage extends ContentPage {
    public function getExcerpt(): string {
        return $this->wire()->sanitizer->truncate($this->body);
    }
}

class BlogPostPage extends ContentPage {
    public function getExcerpt(): string {
        return $this->summary ?: $this->wire()->sanitizer->truncate($this->body);
    }
}
```

## Interfaces

Define contracts that page classes must fulfill:

```php
interface TourPage {
    public function getDepartures(int $month, int $year): array;
    public function getTourType(): string;
}

class BoatTourPage extends Page implements TourPage {
    public function getDepartures(int $month, int $year): array {
        // Implementation
    }
    public function getTourType(): string {
        return 'boat';
    }
}
```

Interfaces support `instanceof` checks, which is useful for polymorphic code:

```php
if($page instanceof TourPage) {
    $departures = $page->getDepartures(6, 2025);
}
```

## Traits

Share method implementations across unrelated page classes:

```php
trait ExcerptTrait {
    public function getExcerpt(): string {
        return $this->wire()->sanitizer->truncate($this->body);
    }
}

class ArticlePage extends Page {
    use ExcerptTrait;
}

class NewsPage extends Page {
    use ExcerptTrait;
}
```

**Caveat:** Traits do NOT support `instanceof` checks. Prefer interfaces or abstract classes when you need type checking. Use traits only for shared implementation without type identity.

## DRY Strategy Selection

| Need | Use |
|------|-----|
| Shared behavior + type identity | Abstract base class |
| Contract enforcement | Interface |
| Shared implementation, no type identity needed | Trait |
| Few classes, simple shared logic | Base class inheritance |
| Runtime extension | Hooks (see [advanced features](advanced-features.md)) |

## Hooks as Alternative (Less Preferred)

You can attach the same method to multiple classes via hooks, but this is less self-documenting:

```php
$wire->addHookMethod(
    'ArticlePage::getExcerpt, BlogPostPage::getExcerpt',
    function($e) {
        $page = $e->object;
        $e->return = wire('sanitizer')->truncate($page->body);
    }
);
```

Prefer inheritance/interfaces/traits over hooks for shared behavior — hooks are harder to discover and don't provide IDE autocomplete.
