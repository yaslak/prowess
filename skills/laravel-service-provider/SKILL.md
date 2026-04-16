---
name: laravel-service-provider
description: 'Generates a Laravel ServiceProvider following project conventions. Trigger on: "create a service provider", "new provider", "register service", "bootstrap service". Always use this skill for any ServiceProvider creation or modification.'
---

# ServiceProvider

## Context
The ServiceProvider registers and configures application services at startup — one ServiceProvider = one single role.

## Conventions

- One ServiceProvider = one single role
- Location → `app/Providers/`

### Log ServiceProviders
- `LogHttpContextServiceProvider` → HTTP context via `RequestHandled`
- `LogJobContextServiceProvider` → Job context via `Queue::before/after/failing`
- `LogCommandContextServiceProvider` → Command context via `CommandFinished`

## Do ✅ / Don't ❌

```php
// ✅ single role
class LogHttpContextServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        $this->app['events']->listen(RequestHandled::class, function () {
            Log::withContext([...]);
        });
    }
}

// ❌ multiple roles in one ServiceProvider
class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Log::withContext([...]);
        Queue::before(fn() => ...);
        $this->app['events']->listen(CommandFinished::class, fn() => ...);
    }
}
```
