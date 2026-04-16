---
name: laravel-dto
description: 'Generates a Laravel DTO following project conventions. Trigger on: "create a dto", "new dto", "dto for {Entity}", "add a data transfer object". Always use this skill for any DTO creation or modification.'
---

# DTO

## Context
The DTO is the data contract that flows through the Pipeline — it carries input data and is enriched by each Pipe. It is the only object that circulates between layers.

## Conventions

- Always `final`
- Extend base `Dto` class
- Public properties → mutable (pipeline state)
- Getters/Setters → PHP 8.4 property hooks with backing property
- Init via `resolve(DtoClass::class, $request->validated())`
- `Dto::__construct()` automatically injects context into logs
- Location → `app/Dto/Api/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Dto/Api/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
final class StoreItemDto extends Dto
{
    public ?Item $item = null;

    public function __construct(
        public string $name,
        public int $price,
    ) {}
}

// ✅ property hook
private string $_email;
public string $email {
    get => $this->_email;
    set(string $value) {
        $this->_email = $value;
    }
}

// ✅ init
resolve(StoreItemDto::class, $request->validated())

// ❌ not final
class StoreItemDto extends Dto {}

// ❌ no base class
class StoreItemDto {}
```
