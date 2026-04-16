---
name: laravel-model
description: 'Generates a Laravel Eloquent Model following project conventions. Trigger on: "create a model", "new model", "model for {Entity}", "add a relation", "add a scope", "add a cast". Always use this skill for any Model creation or modification.'
---

# Model

## Context
The Model is the Eloquent representation of a DB table — it contains only relations, casts and scopes. Never business logic methods.

## Conventions

- Always `$fillable` — never `$guarded`
- Each Model must have its Migration and Factory — no exception
- Declare all relations on both sides
- Policy linked via `#[UsePolicy(PolicyClass::class)]`
- JSON columns → always cast via a dedicated `Support` object
  - Cast → `App\Casts\{Entity}\{Field}Cast` (implements `Castable`)
  - Support object → `App\Support\{Entity}\{Field}` (final class)
- Simple JSON columns (flat arrays) → `'agreements' => 'array'`
- Avoid `query()` — call Model directly
- Laravel automatically generates scopes from column names (ex: `whereCompanyId()` from `company_id`) — never write this scope manually
- Custom scopes → always `scope{Name}()` — never `#[Scope]` attribute
- Unit test mandatory — 100% coverage → `tests/Unit/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅ fillable
protected $fillable = [
    'name',
    'email',
    'address',
    'phones',
    'company_id',
    'agreements',
];

// ❌ guarded
protected $guarded = [];

// ✅ cast with Support object
protected $casts = [
    'address' => AddressCast::class,
    'phones'  => PhoneCast::class,
];

// ✅ cast flat array
protected $casts = [
    'agreements' => 'array',
];

// ✅ custom scope
public function scopeActive(Builder $query): Builder
{
    return $query->where('active', true);
}

// ❌ #[Scope] — prevents direct call without query()
#[Scope]
public function active(Builder $query): Builder
{
    return $query->where('active', true);
}

// ✅ custom scope call
User::active()->get();

// ✅ automatic Laravel scope from column
User::whereCompanyId($company->id)->get();

// ❌ raw where
User::where('active', true)->get();
User::where('company_id', $company->id)->get();

// ❌ query()
User::query()->get();

// ✅ Policy
#[UsePolicy(UserPolicy::class)]
class User extends Model {}

// ✅ relations on both sides
// User
public function company(): BelongsTo
{
    return $this->belongsTo(Company::class);
}

// Company
public function users(): HasMany
{
    return $this->hasMany(User::class);
}

// ❌ business logic in Model
public function processPayment(): void {}
```
