# Core Patterns

## Virtual Properties via `get()`

Override `get()` to expose computed values as properties:

```php
public function get($key) {
    if($key === 'numWords') return $this->numWords();
    if($key === 'authorName') return $this->getAuthorName();
    return parent::get($key);
}

public function numWords(): int {
    return str_word_count(strip_tags($this->body));
}
```

Now `$page->numWords` works like a native field. Always call `parent::get($key)` as the fallback.

## PHPDoc for IDE Support

Document fields and virtual properties for autocomplete and type safety:

```php
/**
 * Blog Post Page
 *
 * @property string $title
 * @property string $summary
 * @property string $body
 * @property string $date
 * @property User|NullPage $author
 * @property PageArray|CategoryPage[] $categories
 * @property-read string $authorName    Virtual, computed property
 * @property-read int $numWords         Virtual, computed property
 */
class BlogPostPage extends Page {
```

Use `@property` for real fields, `@property-read` for virtual/computed ones.

### PHPDoc in Template Files

In template files (e.g., `/site/templates/blog-post.php`), add type hints for `$page`:

```php
/** @var BlogPostPage $page */
```

For collections:
```php
/** @var BlogPostPage[] $posts */
```

## Output Formatting State — `of()`

Check whether the page is in output-formatted mode:

```php
public function thisAndThat(): string {
    if($this->of()) {
        return 'This &amp; That';  // Entity-encoded for output
    }
    return 'This & That';  // Raw for manipulation/saving
}
```

`$this->of()` returns `true` when the page is ready for output, or `false` when ready for manipulation and saving. It can also be used to set the state: `$this->of(true)` / `$this->of(false)`. Output formatting is typically enabled on the front-end and disabled in the admin.

## Admin Page List Label — `getPageListLabel()`

Customize how pages appear in the admin tree:

```php
public function getPageListLabel(): string {
    $title = $this->getFormatted('title');
    $date = $this->getFormatted('date');
    return "<strong>$title</strong> <small>($date)</small>";
}
```

This is the only method specifically intended for admin customization. It should return only inline HTML, and any text must be HTML entity-encoded.

## API Wire Access

Inside custom page classes, always access the API via `$this->wire()`:

```php
// Correct
$this->wire()->pages->find('template=product');
$this->wire()->sanitizer->text($input);
$this->wire()->config->urls->templates;

// Works, but not preferred — not guaranteed to use correct instance in multi-instance setups
$this->pages;
pages()->find('template=product');
```

## Type-Safe Function Parameters

Enforce page types in function signatures:

```php
function renderBlogCard(BlogPostPage $post): string {
    return "<h2>{$post->title}</h2><p>{$post->authorName}</p>";
}
```

This gives you runtime type enforcement and IDE autocomplete.
