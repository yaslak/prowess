---
name: laravel-logs
description: 'Defines logging conventions for the project. Trigger on: "add a log", "log this", "add logging", "log error", "log event". Always use this skill for any logging implementation.'
---

# Logs

## Context
Logs allow tracing and debugging every operation — context is injected automatically via ServiceProviders, never manually.

## Conventions

- `logger()->info()` → normal action
- `logger()->warning()` → suspicious but non-blocking case
- `logger()->error()` → business error
- `logger()->critical()` → urgent — triggers Nightwatch notification
- `logger()->emergency()` → immediate action required
- Context injected automatically — never manually
- Context enrichment possible via `Log::withContext([...])`
- Log in Pipes and anywhere as needed

## Do ✅ / Don't ❌

```php
// ✅
logger()->info('Item stored');
logger()->error('Payment failed');
logger()->critical('Stripe down');

// ✅ context enrichment
Log::withContext(['order_id' => $dto->order_id]);

// ❌ manual context — already injected automatically
logger()->info('Item stored', [
    'user_id' => auth()->id(),
    'ip'      => request()->ip(),
]);
```

## Log avant exception

Quand un log doit précéder une exception, utiliser `if + throw new` — pas `throw_if`.

```php
// ✅ log avant exception
if ($condition) {
    logger()->warning('Insufficient balance');
    throw new InsufficientBalanceException();
}

// ❌ throw_if — logger() après throw est inatteignable (unreachable code)
throw_if($condition, InsufficientBalanceException::class);
logger()->warning('Insufficient balance');
```
