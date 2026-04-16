---
name: laravel-factory
description: 'Generates a Laravel Factory following project conventions. Trigger on: "create a factory", "new factory", "factory for {Entity}", "add factory state". Always use this skill for any Factory creation or modification.'
---

# Factory

## Context
The Factory generates Model instances for tests — it encapsulates test data creation in a reusable and expressive way.

## Conventions

- Every Model must have its Factory — no exception
- Always use `for()` for `belongsTo` relations
- Always use `has()` for `hasMany`/`hasOne` relations
- Both accept an existing instance or a Factory
- Always use named methods (states) instead of `create([...])`
- Location → `database/factories/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Database/Factories/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅ belongsTo
Item::factory()->for($company)->create();
Item::factory()->for(Company::factory())->create();

// ✅ hasMany
Item::factory()->has(Tag::factory()->count(3))->create();
Item::factory()->has($tag)->create();

// ✅ named state
Item::factory()->active()->create();

// ❌ manual data
Item::factory()->create(['status' => 'active', 'company_id' => $company->id]);

// ❌ without named state
Item::factory()->create(['active' => true]);
```
