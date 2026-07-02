# Docker Environment Commands

**All commands MUST run inside the Docker container.**

## PHP / Artisan

```bash
docker compose exec app php artisan module:make ModuleName
docker compose exec app php artisan module:make-controller ControllerName ModuleName
docker compose exec app php artisan module:make-request RequestName ModuleName
docker compose exec app php artisan module:make-model ModelName ModuleName -m
docker compose exec app php artisan module:make-migration create_table_name_table ModuleName
docker compose exec app php artisan module:make-job JobName ModuleName
docker compose exec app php artisan module:make-policy PolicyName ModuleName
docker compose exec app php artisan migrate
docker compose exec app php artisan migrate:rollback
docker compose exec app php artisan optimize:clear
docker compose exec app php artisan ide-helper:generate
```

## Code Quality

```bash
docker compose exec app ./vendor/bin/pint --dirty
docker compose exec app ./vendor/bin/pint
docker compose exec app ./vendor/bin/phpstan analyse
docker compose exec app ./vendor/bin/rector process --dry-run
docker compose exec app ./vendor/bin/rector process
```

## Testing

```bash
docker compose exec app php artisan test
docker compose exec app php artisan test --coverage
docker compose exec app ./vendor/bin/infection --threads=max --min-msi=100
docker compose exec app php artisan test tests/Unit/ExampleTest.php
```

## Composer

```bash
docker compose exec app composer install
docker compose exec app composer require vendor/package
docker compose exec app composer run dev
docker compose exec app composer run pint:fix
docker compose exec app composer run rector:fix
```

## Frontend (also in Docker)

```bash
docker compose exec app yarn dev
docker compose exec app yarn build
docker compose exec app yarn install
```

> **NEVER run commands outside Docker** — all dependencies exist only in the container.
> **Place domain code inside its module** — `Modules/{Name}/`, never in a flat `app/` directory.
