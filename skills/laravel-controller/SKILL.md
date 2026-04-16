---
name: laravel-controller
description: 'Generates a Laravel Controller following project conventions. Trigger on: "create a controller", "new controller", "controller for {Entity}", "add a method", "new endpoint". Always use this skill for any Controller creation or modification.'
---

# Controller

## Context
The Controller is the HTTP entry point — it receives the request, delegates to the Action, and returns a Resource with a message and HTTP status if needed. Never business logic.

## Conventions

- Thin — no business logic, no inline validation
- Flow: `resolve DTO` → `action->execute` → `Resource`
- Always `Gate::authorize()` per method — except public APIs without auth middleware
- Methods always named in CRUD — never business names (`activate`, `process`...)
- FormRequest for all validation
- Always use route model binding — never pass a raw ID
- Functional style — no unnecessary intermediate variables
- Always return a Resource
- Messages via `additional()` — always `trans()` / `__()` / `trans_choice()`
- Status codes via `Response::HTTP_*` — never magic numbers
- Location → `app/Http/Controllers/Api/V1/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Http/Controllers/Api/V1/{Resource}/`
- Feature test mandatory — 100% coverage → `tests/Feature/Api/V1/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅ invoke
public function __invoke(StoreRequest $request, StoreAction $action)
{
    Gate::authorize('store');
    return ItemResource::make(
        $action->execute(resolve(StoreDto::class, $request->validated()))
    );
}

// ✅ store — 201
public function store(StoreRequest $request, StoreAction $action)
{
    Gate::authorize('create', Item::class);
    return ItemResource::make(
        $action->execute(resolve(StoreDto::class, $request->validated()))
    )
        ->additional(['message' => __('item.created')])
        ->response()
        ->setStatusCode(Response::HTTP_CREATED);
}

// ✅ store collection — 201 plural message
public function store(StoreRequest $request, StoreAction $action)
{
    Gate::authorize('create', Item::class);
    $result = $action->execute(resolve(StoreDto::class, $request->validated()));
    return ItemResource::collection($result)
        ->additional([
            'message' => trans_choice('item.created', $result->count(), [
                'number' => $result->count(),
            ]),
        ])
        ->response()
        ->setStatusCode(Response::HTTP_CREATED);
}

// ✅ route model binding
public function show(ShowRequest $request, Item $item, ShowAction $action)
{
    Gate::authorize('view', $item);
    return ItemResource::make(
        $action->execute(resolve(ShowDto::class, $request->validated()))
    );
}

// ❌ unnecessary variable
$dto = resolve(StoreDto::class, $request->validated());
$result = $action->execute($dto);
return ItemResource::make($result);

// ❌ raw ID
public function show(Request $request, int $id) {}

// ❌ business method name
public function activate() {}

// ❌ business logic in controller
public function store(Request $request)
{
    $item = Item::create($request->all());
}

// ❌ inline validation
public function store(Request $request)
{
    $request->validate(['name' => 'required']);
}

// ❌ magic number
->setStatusCode(201);

// ❌ hardcoded message
->additional(['message' => 'Item created']);
```
