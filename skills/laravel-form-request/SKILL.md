---
name: laravel-form-request
description: 'Generates a Laravel FormRequest following project conventions. Trigger on: "create a form request", "new request", "validate {Entity}", "add validation". Always use this skill for any FormRequest creation or modification.'
---

# FormRequest

## Context
The FormRequest validates and authorizes the HTTP request before it reaches the Controller — it ensures data is clean and the user has the right to perform the action.

## Conventions

- `authorize()` → always `return true` — authorization is handled by the Policy in the Controller
- Override `messages()` if the default message is not explicit enough
- Always validate via Enum when the value is constrained
- Location → `app/Http/Requests/V1/{Resource}/`
- Unit test mandatory — 100% coverage → `tests/Unit/Http/Requests/V1/{Resource}/`

## Do ✅ / Don't ❌

```php
// ✅
class StoreItemRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'name'   => ['required', 'string'],
            'status' => ['required', Rule::enum(StatusEnum::class)],
        ];
    }

    public function messages(): array
    {
        return [
            'name.required' => 'The item name is required.',
        ];
    }
}

// ❌ authorize() with logic — delegate to Policy
public function authorize(): bool
{
    return $this->user()->isAdmin();
}

// ❌ inline validation in controller
$request->validate(['name' => 'required']);

// ❌ missing messages() when needed
public function rules(): array
{
    return ['email' => ['required', 'email']];
    // default message not explicit enough
}
```
