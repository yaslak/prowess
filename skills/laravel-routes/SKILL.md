---
name: laravel-routes
description: 'Generates Laravel routes following project conventions. Trigger on: "create a route", "new route", "add endpoint", "register route for {Entity}". Always use this skill for any route creation or modification.'
---

# Routes

## Context
Files that define the HTTP entry points of the API — URI, method, associated controller. One file per entity keeps the project navigable and maintainable.

## Conventions

- `api.php` / `web.php` → mapping only + version prefix (`v1`)
- Middleware → only in child files
- Naming → `{domain}.{entity}.{action}`
- `{domain}` = tenant domain ID (multi-tenant) — not a business module
- Route model binding mandatory — never pass a raw ID
- Always prefer `Route::resource()->only()` instead of declaring each route manually
- One file per entity → `routes/v1/{resource}.php`

## Do ✅ / Don't ❌

```php
// ✅ api.php
Route::prefix('v1')->group(function () {
    require base_path('routes/v1/items.php');
    require base_path('routes/v1/users.php');
});

// ✅ routes/v1/items.php
Route::prefix('domains/{domain}/items')
    ->middleware(['auth:api'])
    ->group(function () {
        Route::resource('items', ItemController::class)
            ->only(['index', 'store', 'show', 'update', 'destroy']);
    });

// ❌ declaring each route manually
Route::get('/', [ItemController::class, 'index'])->name('items.index');
Route::post('/', [ItemController::class, 'store'])->name('items.store');

// ❌ raw ID
Route::get('/{id}', [ItemController::class, 'show']);

// ❌ middleware in api.php
Route::middleware(['auth:api'])->prefix('v1')->group(function () {});

// ❌ all routes in api.php
Route::get('/items', [ItemController::class, 'index']);
```
