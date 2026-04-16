---
name: laravel-policy
description: 'Generates a Laravel Policy following project conventions. Trigger on: "create a policy", "new policy", "policy for {Entity}", "add authorization". Always use this skill for any Policy creation or modification.'
---

# Policy

## Context
The Policy centralizes authorization rules for a Model — it determines if a user can perform an action on a resource.

## Conventions

- Linked to Model via `#[UsePolicy(PolicyClass::class)]`
- Called via `Gate::authorize()` in each Controller method
- Exception: public APIs without auth middleware
- Location → `app/Policies/`
- Unit test mandatory — 100% coverage → `tests/Unit/Policies/`

## Do ✅ / Don't ❌

```php
// ✅ Policy
class ItemPolicy
{
    public function view(User $user, Item $item): bool
    {
        return $user->company_id === $item->company_id;
    }

    public function create(User $user): bool
    {
        return $user->hasPermission('items.create');
    }
}

// ✅ in Controller
Gate::authorize('view', $item);
Gate::authorize('create', Item::class);

// ❌ authorization in FormRequest
public function authorize(): bool
{
    return $this->user()->isAdmin();
}

// ❌ $this->authorize() — Laravel 12 uses Gate::authorize()
$this->authorize('view', $item);
```
