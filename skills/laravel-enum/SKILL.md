---
name: laravel-enum
description: 'Generates a Laravel Enum following project conventions. Trigger on: "create an enum", "new enum", "enum for {Entity}", "add status", "add type". Always use this skill for any Enum creation or modification.'
---

# Enum

## Context
The Enum replaces PHP constants for fixed values — it guarantees consistency of values across code, validations and the DB.

## Conventions

- Always use an Enum instead of PHP `const`
- Used in FormRequest for validation via `Rule::enum()`
- Used to protect DB column values (cast in Model)
- Location → `app/Enums/`
- Unit test mandatory — 100% coverage → `tests/Unit/Enums/`

## Do ✅ / Don't ❌

```php
// ✅
enum StatusEnum: string
{
    case ACTIVE   = 'active';
    case INACTIVE = 'inactive';
    case PENDING  = 'pending';
}

// ✅ in FormRequest
'status' => ['required', Rule::enum(StatusEnum::class)],

// ✅ in Model cast
protected $casts = [
    'status' => StatusEnum::class,
];

// ❌ PHP const
const STATUS_ACTIVE   = 'active';
const STATUS_INACTIVE = 'inactive';

// ❌ raw string validation
'status' => ['required', 'in:active,inactive,pending'],
```
