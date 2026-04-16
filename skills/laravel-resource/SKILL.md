---
name: laravel-resource
description: 'Generates a Laravel Resource following project conventions. Trigger on: "create a resource", "new resource", "resource for {Entity}", "format response". Always use this skill for any Resource creation or modification.'
---

# Resource

## Context
The Resource transforms the DTO or Model into a structured JSON response — it formats data for the client without any business logic.

## Conventions

- Extend `JsonResource`
- `@property {Model} $resource` docblock mandatory — without it the IDE does not know the exact type of `$resource` and autocompletion breaks
- Always `$this->resource->` — never `$this->`
- `created_at` / `updated_at` always present
- Foreign IDs always included
- Relations → `mergeWhen` + `relationLoaded()` — never load if not eager loaded
- Cast objects → `new {Cast}Resource($this->resource->field)->resolve()`
- Single relation → `Resource::make()`
- Collection → `Resource::collection()`
- Location → `app/Http/Resources/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Http/Resources/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
/** @property Item $resource */
class ItemResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'         => $this->resource->id,
            'name'       => $this->resource->name,
            'company_id' => $this->resource->company_id,
            $this->mergeWhen(
                $this->resource->relationLoaded('company'),
                fn () => ['company' => CompanyResource::make($this->resource->company)]
            ),
            'created_at' => $this->resource->created_at,
            'updated_at' => $this->resource->updated_at,
        ];
    }
}

// ❌ $this-> instead of $this->resource->
'name' => $this->name,

// ❌ loading a non eager loaded relation
'company' => CompanyResource::make($this->resource->company()->first()),

// ❌ missing @property docblock — IDE loses type awareness
class ItemResource extends JsonResource
{
    public function toArray(Request $request): array {}
}
```
