# init() and ready() in MagicPages

ProcessWire does not natively trigger `init()` and `ready()` on custom page classes. Without RockMigrations, hooks related to a page class must go in `/site/ready.php` or a custom module — scattering logically connected code across files.

With the `MagicPage` trait, you can define `init()` and `ready()` directly in the page class.

## Example

```php
// site/classes/EventPage.php
<?php namespace ProcessWire;

use RockMigrations\MagicPage;

class EventPage extends Page
{
    use MagicPage;

    public function init()
    {
        // attach hooks, register behaviors
    }

    public function ready()
    {
        // runs after all modules are ready
    }
}
```

## Key Behavior

- `init()` and `ready()` run **once per page class**, not once per page instance
- Behind the scenes, RockMigrations creates a runtime page (with `$this->id === 0`) for each MagicPage class
- This ensures hooks fire even if no pages of that type exist yet (e.g., a fresh blog module with zero posts)
- Inside these methods, `$this` is available but `$this->id` is always `0`
