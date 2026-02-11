# MagicPages Reference

Custom PageClasses can define their own migrations and lifecycle hooks.

## Table of Contents

- [Lifecycle Hooks](#lifecycle-hooks)
- [Magic Methods](#magic-methods)
- [Magic Field Methods](#magic-field-methods)
- [Magic Assets](#magic-assets)
- [Trait Utility Methods](#trait-utility-methods)
- [Example](#example)

---

## Lifecycle Hooks

Require `use MagicPage` trait on the PageClass.

- `onCreate()` — after `Pages::saveReady` when page ID is 0. Called when page is created, before first save.
- `onAdded()` — after `Pages::added`. Called after page is saved for the first time.
- `onSaved()` — after `Pages::saved`. Called every time the page is saved.
- `onSaveReady()` — after `Pages::saveReady`. Called before save, allows modifying data before write.

---

## Magic Methods

Additional hooks that fire only for pages matching the PageClass template. Define them as public methods on your class — no manual hook wiring needed.

Can be disabled globally: `$config->noMagicMethods = true;`

| Method | Hook | Purpose |
|--------|------|---------|
| `editForm($form)` | `ProcessPageEdit::buildForm` | Modify the page edit form |
| `editFormContent($form)` | `ProcessPageEdit::buildFormContent` | Modify the content tab |
| `editFormSettings($form)` | `ProcessPageEdit::buildFormSettings` | Modify the settings tab |
| `onChanged()` | `Page::changed` | React to field value changes |
| `onProcessInput()` | `InputfieldForm::processInput` | Process form input |
| `onTrashed()` | `Pages::trashed` | React to page being trashed |
| `pageListLabel()` | `ProcessPageListRender::getPageLabel` | Customize page list label |
| `setPageName()` | `Pages::saved(id>0)` | Set page name from callback |

---

## Magic Field Methods

Access fields with long prefixed names via shortened method calls.

Can be disabled globally: `$config->noMagicFieldMethods = true;`

```php
// Field named "event_start_date" can be accessed as:
$page->date();  // returns value of event_start_date
```

Useful when fields use module/feature prefixes to avoid name collisions.

---

## Magic Assets

MagicPage classes auto-load matching CSS/JS files in the ProcessWire backend when editing pages of that type.

```
site/classes/EventPage.php   ← page class
site/classes/EventPage.css   ← auto-loaded in admin
site/classes/EventPage.js    ← auto-loaded in admin
```

---

## Trait Utility Methods

Methods available on any class using the `MagicPage` trait:

| Method | Purpose |
|--------|---------|
| `rockmigrations()` | Get RockMigrations instance |
| `rockfrontend()` | Get RockFrontend instance |
| `site()` | Get Site module instance |
| `createOnTop()` | Create pages on top of page list |
| `setPageNameFromTitle()` | Auto-set page name from title |
| `setPageNameFromField($fields)` | Auto-set page name from specified field(s) |
| `removeSaveButton($form)` | Remove submit button from edit screen (use inside `editForm()`) |
| `pageListBadge($str, $style)` | Render styled badge in page list |
| `getTplName()` | Get template name from `const tpl` or `$this->template` |

---

## Example

```php
// site/classes/ProductPage.php
<?php namespace ProcessWire;

use RockMigrations\MagicPage;

class ProductPage extends Page
{
    use MagicPage;

    const tpl = 'product';  // template name

    public function migrate()
    {
        $rm = $this->rockmigrations();
        $rm->migrate([
            'fields' => [
                'price' => ['type' => 'float', 'label' => 'Price'],
                'sku' => ['type' => 'text', 'label' => 'SKU'],
            ],
            'templates' => [
                self::tpl => [
                    'fields' => ['title', 'price', 'sku', 'body', 'images'],
                ],
            ],
        ]);
    }
}
```
