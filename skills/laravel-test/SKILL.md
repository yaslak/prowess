---
name: laravel-test
description: 'Generates Laravel tests following project conventions. Trigger on: "create a test", "new test", "test for {Entity}", "add unit test", "add feature test". Always use this skill for any test creation or modification.'
---

# Test

## Context
Tests guarantee 100% coverage of every artifact — Unit for isolated logic, Feature for the complete HTTP flow.

## Conventions

- Framework → Pest only
- Unit test → follows the source file path, replacing `app/` with `tests/Unit/` and removing `Api/`
  ex: `app/Http/Controllers/Api/V1/{Resource}/` → `tests/Unit/Http/V1/Controllers/{Resource}/`
- Feature test → `tests/Feature/Api/V1/{Resource}/`
- Always use fluent syntax `expect()->toBe()->and()`
- Use Factories for test data — never manual data
- Always use route names — never URI directly
- 100% coverage mandatory

## Do ✅ / Don't ❌

```php
// ✅ fluent syntax
expect($item->name)->toBe('Test')
    ->and($item->status)->toBe(StatusEnum::ACTIVE);

// ✅ feature test — route name, no variables
it('stores an item', function () {
    actingAs(User::factory()->create())
        ->postJson(route('items.store'), [
            'name' => 'Test Item',
        ])
        ->assertCreated()
        ->assertJsonPath('data.name', 'Test Item');
});

// ❌ PHPUnit syntax
$this->assertEquals('Test', $item->name);

// ❌ URI directly
->postJson('/api/v1/domains/1/items', [...]);

// ❌ manual data in tests
$item = Item::create(['name' => 'Test', 'company_id' => 1]);

// ❌ unnecessary variables
$response = actingAs($user)->postJson(route('items.store'), [...]);
$response->assertCreated();
```
