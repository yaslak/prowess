---
name: laravel-action
description: 'Generates a Laravel Action following project conventions. Trigger on: "create an action", "new action", "action for {Entity}", "add a flow", "orchestrate {Entity}". Always use this skill for any Action creation or modification.'
---

# Action

## Context
The Action orchestrates the business flow via a Pipeline of Pipes — it contains no logic, only the order of operations, event dispatching and exception handling.

## Conventions

- Orchestration only — never business logic
- Always returns the injected DTO
- `DB::transaction()` mandatory — automatic rollback on any exception
- Try/catch for custom exceptions — set status → re-throw
- Pipes declared directly inside the Pipeline — no separate `$pipes` array
- All DB queries go through a Pipe — never `get()`, `find()`, `first()` in the Action
- Location → `app/Http/Actions/V1/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Http/Actions/V1/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
public function execute(StoreItemDto $dto): StoreItemDto
{
    return DB::transaction(function () use ($dto) {
        try {
            return Pipeline::send($dto)
                ->through([
                    ValidateItemPipe::class,
                    StoreItemPipe::class,
                    SendNotificationPipe::class,
                ])
                ->thenReturn();
        } catch (ItemException $e) {
            $e->status = 422;
            throw $e;
        }
    });
}

// ❌ separate $pipes array
class StoreItemAction
{
    public array $pipes = [
        ValidateItemPipe::class,
        StoreItemPipe::class,
    ];
}

// ❌ business logic in Action
public function execute(StoreItemDto $dto): StoreItemDto
{
    $dto->item = Item::create(['name' => $dto->name]);
    return $dto;
}

// ❌ DB query in Action
public function execute(StoreItemDto $dto): StoreItemDto
{
    $dto->item = Item::find($dto->id);
    return Pipeline::send($dto)...
}

// ✅ DB query in a dedicated Pipe
public function __invoke(StoreItemDto $dto, Closure $next): mixed
{
    $dto->item = Item::find($dto->id);
    return $next($dto);
}

// ❌ no DB::transaction()
return Pipeline::send($dto)
    ->through([...])
    ->thenReturn();

// ❌ catch without re-throw
catch (ItemException $e) {
    // silently ignored
}
```
