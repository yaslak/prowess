---
description: Scaffold a complete Laravel feature — model, migration, Livewire component, test and route in one pass.
---

Scaffold a complete Laravel feature in one pass.

## Usage

```
/new-feature <FeatureName> [--api] [--livewire] [--policy]
```

## What it does

Given a feature name (e.g. `UserProfile`, `InvoiceExport`), scaffold:

1. **Model** — `php artisan make:model <Name> --factory --migration`
2. **Migration** — infer columns from the feature name and context; ask only if ambiguous
3. **Livewire component** (default or `--livewire`) — `php artisan make:livewire <Name>`
4. **Policy** (`--policy`) — `php artisan make:policy <Name>Policy --model=<Name>`
5. **Feature test** — `php artisan make:test --pest <Name>Test`
6. **Route** — add to `routes/web.php` or `routes/api.php` (`--api`)

## Rules

- Pass `--no-interaction` to all artisan commands
- Check sibling models for existing naming conventions before creating
- Use constructor property promotion in all generated PHP classes
- Add explicit return types to all methods
- Run `vendor/bin/pint --dirty --format agent` after all PHP files are created
- Run `php artisan test --compact --filter=<Name>` at the end to confirm tests pass

## Steps

1. Confirm the feature name and options with the user if not provided
2. Run the artisan commands in order
3. Fill in the migration with sensible columns based on context
4. Write the Livewire component with basic structure (mount, render)
5. Write a passing feature test (at minimum: model creation + route access)
6. Add the route with the correct middleware
7. Format with Pint
8. Run the test — report result
