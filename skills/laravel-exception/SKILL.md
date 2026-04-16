---
name: laravel-exception
description: 'Generates a Laravel custom Exception following project conventions. Trigger on: "create an exception", "new exception", "exception for {Entity}", "handle error". Always use this skill for any Exception creation or modification.'
---

# Exception

## Context
Custom exceptions represent precise business error cases — they carry their HTTP status and bubble up to the Handler for a uniform JSON response.

## Conventions

- All exceptions extend `\Exception`
- The exception carries a mutable public `status` (default `500`)
- Thrown in Pipes — without status
- If the exception does not exist yet → create it
- Caught in the Action — set status via `Response::HTTP_*` → re-throw
- Handler → single entry → JSON response
- Location → `app/Exceptions/`

## Do ✅ / Don't ❌

```php
// ✅ custom exception
class ItemNotFoundException extends \Exception
{
    public int $status = Response::HTTP_NOT_FOUND;
}

// ✅ in Pipe — throw_if / throw_unless with class string + optional message
/**
 * @throws ItemNotFoundException|Throwable
 */
throw_if(
    condition: $someCondition,
    exception: ItemNotFoundException::class
);

// with message:
throw_unless(
    $response->offsetExists('data'),
    ItemNotFoundException::class,
    'Item not found in response'
);

// ✅ in Action — set status via constant
catch (ItemNotFoundException $e) {
    $e->status = Response::HTTP_NOT_FOUND;
    throw $e;
}

// ✅ Handler
$this->renderable(function (\Exception $e) {
    return response()->json(['message' => $e->getMessage()], $e->status);
});

// ❌ if block pour throw
if ($someCondition) {
    throw new ItemNotFoundException();
}

// ❌ catch without re-throw
catch (ItemNotFoundException $e) {
    // silently ignored
}

// ❌ magic number in status
$e->status = 404;
```
