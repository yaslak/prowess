---
name: laravel-cast
description: 'Generates a Laravel Cast following project conventions. Trigger on: "create a cast", "new cast", "cast for {Entity}", "cast json column". Always use this skill for any Cast creation or modification.'
---

# Cast

## Context
The Cast transforms a JSON column into a typed Support object — it ensures JSON data is always manipulated via a structured object rather than a raw array.

## Conventions

- Implements `Castable`
- Always paired with a dedicated `Support` object
- Location → `app/Casts/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
class AddressCast implements Castable
{
    public static function castUsing(array $arguments): CastsAttributes
    {
        return new class () implements CastsAttributes {
            public function get(Model $model, string $key, mixed $value, array $attributes): Address
            {
                $data = json_decode($value, true);
                return new Address(
                    county  : $data['county'],
                    line_one: $data['line_one'],
                    city    : $data['city'],
                    zip     : $data['zip'],
                );
            }

            public function set(Model $model, string $key, mixed $value, array $attributes): string
            {
                if (!$value instanceof Address) {
                    throw new InvalidArgumentException('Expected Address instance.');
                }
                return json_encode($value->toArray());
            }
        };
    }
}

// ❌ cast to raw array — loses typing
protected $casts = [
    'address' => 'array',
];
```
