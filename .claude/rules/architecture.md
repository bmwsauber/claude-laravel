# Architecture Patterns

## Business Logic

- **Controllers** — HTTP entry point; thin, delegate to Service classes
- **Service Layer**: business logic lives in dedicated Service classes (`Modules/{Name}/Services`)
- **Repository Pattern**: not used — rely on Eloquent models directly

## Modular Architecture

- **`nwidart/laravel-modules`** — each domain is a self-contained module under `Modules/{Name}/`
- Namespace has **no** `App` segment: `Modules\Post\Http\Controllers`, `Modules\Post\Services`, `Modules\Post\Models`
- Each module owns its Controllers, Services, Models, Form Requests, Policies, Jobs, Observers,
  migrations, routes, and Inertia React pages (`Modules/{Name}/resources/js/Pages/`)
- Generate via `php artisan module:make {Name}` and `module:make-*` commands
- Register the module's Policies in its own `ServiceProvider::boot()`, not in `AppServiceProvider`

## Frontend

- **Inertia.js** with React — frontend built as SPA via server-driven routing
- **TypeScript** by default (`.tsx`)
- **Domain Organization**: React pages/components live inside their owning module

## Database

- Every DB structure change → new migration
- Every DB data change → update seeder + factory
- Prefer Eloquent models over raw queries (`DB::` facade)
- Prefer Eloquent relationships over manual joins
- Prefer Eloquent eager loading over lazy loading (N+1 prevention)
- Prefer Eloquent pagination, scopes, soft deletes over raw alternatives

## Performance

- **Laravel Octane** with FrankenPHP — high-performance application server
- **Redis** — caching, sessions, queue management
- **PostgreSQL** with proper indexing
- Image optimization tools included in Docker setup

## Development Tools

- **Telescope** — debugging assistant (enabled in testing)
- **Log Viewer** — web-based log viewing
- **IDE Helpers** — auto-generated (`php artisan ide-helper:generate`)
- **Xdebug** — available in Docker development environment
