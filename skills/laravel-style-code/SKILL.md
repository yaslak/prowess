---
name: laravel-style-code
description: 'Defines code style conventions for the project. Always apply these rules when writing any PHP code. Trigger on any code generation task.'
---

# Code Style

## Context
Functional style and Laravel helpers keep code expressive, readable and consistent — fewer intermediate variables, clearer intent.

## Conventions

- `declare(strict_types=1)` mandatory in all files
- Strict typing everywhere
- Functional style — chain calls, no unnecessary intermediate variables
- `Arr::` instead of `array_*`
- `Str::` instead of `str_*`
- `collect()` instead of manipulating arrays manually
- Laravel facades instead of global PHP functions
- `is_null()` instead of `=== null`
- `->count()` sur Collection — pas `count($collection)`
- `if + throw new` quand un log précède l'exception — pas `throw_if`

## Do ✅ / Don't ❌

```php
// ✅ chained
return User::active()
    ->get()
    ->filter(fn($u) => $u->verified)
    ->map(fn($u) => $u->name);

// ❌ intermediate variables
$users = User::active()->get();
$filtered = $users->filter(fn($u) => $u->verified);
$mapped = $filtered->map(fn($u) => $u->name);
return $mapped;

// ✅ Laravel helpers
Arr::map($items, fn($i) => $i->name);
Str::slug($title);
collect($items)->pluck('name');

// ❌ native PHP functions
array_map(fn($i) => $i->name, $items);
str_replace(' ', '-', strtolower($title));

// ✅ helper
is_null($value)

// ❌ native comparison
$value === null

// ✅ count sur Collection
$dto->products->count()

// ❌ count natif
count($dto->products)

// ✅ log avant exception
if ($condition) {
    logger()->warning('Insufficient balance');
    throw new InsufficientBalanceException();
}

// ❌ throw_if — logger() après est unreachable
throw_if($condition, InsufficientBalanceException::class);
logger()->warning('...');
```

## Eloquent vs Raw SQL

- Jamais `DB::` — toujours `Model::query()`
- Jamais `selectRaw`, `whereRaw`, `orderByRaw` — utiliser les méthodes Eloquent/Collection

```php
// ✅ Eloquent + Collection
Stat::whereIn('post_id', $ids)
    ->get()
    ->pipe(fn ($stats) => (object) [
        'impressions' => $stats->sum('impressions'),
        'views'       => $stats->sum('views'),
    ]);

// ❌ raw SQL
Stat::whereIn('post_id', $ids)
    ->selectRaw('COALESCE(SUM(impressions), 0) as impressions')
    ->first();

// ❌ DB facade
DB::table('stats')->where('post_id', $id)->first();
```

## Named Arguments

- Named arguments obligatoires quand il y a plus d'un paramètre
- Alignement vertical obligatoire

```php
// ✅
Context::add(
    key  : 'account',
    value: Account::firstOrCreate(
        ['external_company_id' => auth('alpha')->user()->company->id],
        ['status' => AccountStatusEnum::Inactive]
    )
);

// ✅
resolve(
    name      : StoreDto::class,
    parameters: $request->validated()
);

// ❌
Context::add('account', Account::firstOrCreate(...));

// ❌
Context::add(
    key: 'account',
    value: Account::firstOrCreate(...)
);
```
