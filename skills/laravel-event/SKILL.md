---
name: laravel-event
description: 'Generates a Laravel Event following project conventions. Trigger on: "create an event", "new event", "event for {Entity}", "dispatch event". Always use this skill for any Event creation or modification.'
---

# Event

## Context
The Event signals that a business action has occurred — it decouples the main flow from side effects like notifications.

## Conventions

- Naming → `{Entity}{PastTense}` ex: `ItemCreated`, `UserSignedUp`
- Use `Dispatchable`, `SerializesModels`
- Dispatched from the Action — never from a Pipe or Controller
- Location → `app/Events/`
- Unit test mandatory — 100% coverage → `tests/Unit/Events/`

## Do ✅ / Don't ❌

```php
// ✅
class ItemCreated
{
    use Dispatchable, SerializesModels;

    public function __construct(
        public readonly Item $item,
    ) {}
}

// ✅ dispatch from Action
ItemCreated::dispatch($dto->item);

// ❌ dispatch from a Pipe
class StoreItemPipe
{
    public function __invoke(StoreItemDto $dto, Closure $next): mixed
    {
        $dto->item = Item::create([...]);
        ItemCreated::dispatch($dto->item); // ❌
        return $next($dto);
    }
}

// ❌ present tense naming
class CreateItem {} // should be ItemCreated
```
