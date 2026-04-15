# Prowess

Personal dev toolkit — curated skills, agents and commands for building Laravel applications with Claude.

## Stack

- Laravel 13 + PHP 8.5
- Livewire 4
- Pest 4
- Tailwind CSS 4
- SQLite (local) / MySQL (production)

## Ecosystem

Microservices maintained by this toolkit:

| Service | Role | Domain |
|---|---|---|
| `brain` | Central observability & knowledge hub | brain.addlocal |
| `luna` | Primary client application | luna.addlocal |
| `alpha` | Internal tooling | alpha.addlocal |
| `fleet-auth` | Shared authentication service | fleet-auth.addlocal |
| `fleet-emailer` | Transactional email service | fleet-emailer.addlocal |
| `stay-sync` | Data synchronization service | stay-sync.addlocal |

All services are served locally by Laravel Herd.

## Skills

- `ui-designer` — Opinionated UI/UX decisions: color palettes, typography, component design. Activate when designing pages or components.

## Agents

- `architect` — Software architect for system design, schema proposals, service boundaries, and architectural trade-offs.

## Commands

- `/new-feature` — Scaffold a complete Laravel feature: model, migration, Livewire component, test, and route in one pass.

## Conventions

- Follow existing code conventions in each project — check sibling files first.
- Use descriptive names for all variables and methods.
- Prefer named routes and the `route()` helper for URL generation.
- Run `vendor/bin/pint --dirty --format agent` after modifying any PHP file.
