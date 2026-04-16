---
name: laravel-support
description: 'Generates a Laravel Support object following project conventions. Trigger on: "create a support object", "new support", "support for {Entity}", "typed json object". Always use this skill for any Support object creation or modification.'
---

# Support

## Context
The Support object is the typed representation of a JSON column — it replaces raw arrays with an object that has typed properties.

## Conventions

- Always `final`
- Typed constructor properties
- `toArray()` method mandatory
- Location → `app/Support/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
final class Address
{
    public function __construct(
        public ?string $county   = null,
        public ?string $line_one = null,
        public ?string $line_two = null,
        public ?string $city     = null,
        public ?string $zip      = null,
    ) {}

    public function toArray(): array
    {
        return [
            'county'   => $this->county,
            'line_one' => $this->line_one,
            'line_two' => $this->line_two,
            'city'     => $this->city,
            'zip'      => $this->zip,
        ];
    }
}

// ❌ not final
class Address {}

// ❌ missing toArray()
final class Address
{
    public function __construct(
        public ?string $city = null,
    ) {}
}
```
