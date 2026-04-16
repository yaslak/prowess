---
name: laravel-response
description: 'Defines API response conventions for the project. Trigger on: "return a response", "format response", "add message to response", "return 201", "paginate results". Always use this skill for any API response implementation.'
---

# Response

## Context
API response conventions guarantee a uniform structure for all responses — success, error, pagination.

## Conventions

- Always return a Resource
- Single → `Resource::make()`
- Collection → `Resource::collection()`
- Created → `Response::HTTP_CREATED`
- Messages → via `additional()` — always `trans()` / `__()` / `trans_choice()`
- Status codes → via `Response::HTTP_*` — never magic numbers
- Validation errors → automatic Laravel format via FormRequest
- Pagination → Laravel `paginate()` by default

## Do ✅ / Don't ❌

```php
// ✅ show
return ItemResource::make(
    $action->execute(resolve(StoreDto::class, $request->validated()))
);

// ✅ store 201
return ItemResource::make($result)
    ->additional(['message' => __('item.created')])
    ->response()
    ->setStatusCode(Response::HTTP_CREATED);

// ✅ collection 201 with plural message
return ItemResource::collection($result)
    ->additional([
        'message' => trans_choice('item.created', $result->count(), [
            'number' => $result->count(),
        ]),
    ])
    ->response()
    ->setStatusCode(Response::HTTP_CREATED);

// ✅ pagination
return ItemResource::collection(Item::paginate());

// ❌ magic number
->setStatusCode(201);

// ❌ hardcoded message
->additional(['message' => 'Item created']);

// ❌ response without Resource
return response()->json(['data' => $item]);
```
