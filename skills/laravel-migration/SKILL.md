---
name: laravel-migration
description: 'Generates a Laravel migration following project conventions. Trigger on: "create a migration", "add a column", "new table", "migration for {Model}", "modify the table", "add a field". Always use this skill for any table or column creation or modification.'
---

# Migration

## Context
A migration describes the database structure — creating, modifying or dropping tables and columns. It versions the DB structure alongside the code. Every change is traceable, reversible and shareable across environments.

## Conventions

- Default Laravel structure
- Table naming → Model name in plural (ex: `users`, `companies`)
- Pivot table → combination of both table names in singular alphabetical order (ex: `company_user`, `plan_role`)
- All columns first, foreign keys just before `timestamps()`
- `foreignIdFor(Model::class)` — never `foreignId('...')`
- Always `->index()->constrained()` — let Laravel guess the name automatically
- Never `cascadeOnDelete()` / `cascadeOnUpdate()`
- New column → ask if DB fresh is possible :
  - Yes → modify the existing migration
  - No → create a new migration

## Do ✅

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('body');
    $table->boolean('active')->default(true);
    $table->foreignIdFor(User::class)->index()->constrained();
    $table->foreignIdFor(Company::class)->index()->constrained();
    $table->timestamps();
});

// Pivot table — alphabetical order
Schema::create('company_user', function (Blueprint $table) {
    $table->foreignIdFor(Company::class)->index()->constrained();
    $table->foreignIdFor(User::class)->index()->constrained();
    $table->timestamps();
});
```

## Don't ❌

```php
// ❌ foreignId instead of foreignIdFor
$table->foreignId('user_id')->constrained();

// ❌ cascade
$table->foreignIdFor(User::class)->index()->constrained()->cascadeOnDelete();

// ❌ pivot table badly named
Schema::create('user_company', ...);

// ❌ foreign keys after timestamps
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->timestamps();
    $table->foreignIdFor(User::class)->index()->constrained();
});
```
