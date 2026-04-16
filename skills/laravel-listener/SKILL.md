---
name: laravel-listener
description: 'Generates a Laravel Listener following project conventions. Trigger on: "create a listener", "new listener", "listener for {Event}", "react to event". Always use this skill for any Listener creation or modification.'
---

# Listener

## Context
The Listener reacts to an Event — it executes an isolated side effect, synchronously or asynchronously as needed.

## Conventions

- Naming → `{Action}{Entity}Listener` ex: `SendWelcomeEmailListener`
- Implement `ShouldQueue` if async processing is needed
- One Listener = one side effect
- Location → `app/Listeners/`
- Unit test mandatory — 100% coverage → `tests/Unit/Listeners/`

## Do ✅ / Don't ❌

```php
// ✅ sync
class SendWelcomeEmailListener
{
    public function handle(UserSignedUp $event): void
    {
        Mail::to($event->user)->send(new WelcomeMail($event->user));
    }
}

// ✅ async
class PublishItemToKafkaListener implements ShouldQueue
{
    public function handle(ItemCreated $event): void
    {
        KafkaClient::publish($event->item->toArray());
    }
}

// ❌ multiple side effects in one Listener
public function handle(ItemCreated $event): void
{
    Mail::send(...);
    Slack::notify(...);
    Cache::flush(...);
}
```
