# Testing Rules

## Models Testing Policy

**DO NOT** create unit tests for Laravel Eloquent models.

Rationale:
- Laravel's Eloquent ORM is extensively tested by the Laravel team
- Testing basic CRUD, relationships, and standard functionality provides no value
- Models are excluded from code coverage metrics (see phpunit.xml)

What NOT to test:
- Basic relationships (hasOne, hasMany, belongsTo, etc.)
- Simple CRUD operations
- Standard Eloquent functionality
- Factory creation without custom logic
- Basic fillable/guarded attributes, standard casting

Exceptions — What TO test:
- Custom business logic methods
- Complex accessors/mutators with business rules
- Custom scopes with specific logic
- Observer behavior and side effects

Where to test model functionality instead:
- Feature tests via HTTP endpoints and workflows
- Integration tests for model interactions
- Observer tests for event handlers
- Controller/Service tests for business logic

## Framework & Tools

- **PHPUnit** — `extends TestCase`, `public function test_*(): void`, `$this->assertX()`
- **Mutation Testing** with Infection — `--threads=max --min-msi=100`
- **Architectural Testing** — enforced via `tests/Unit/ArchTest.php` (`ta-tikoma/phpunit-architecture-test`)

## Test Structure

```
tests/
├── Feature/          # Integration tests (Auth, Posts, Pages)
├── Unit/             # Unit tests (Services, Models, Observers, Support)
└── TestCase.php      # Base test case
```

Module-specific tests may also live alongside the module: `Modules/{Name}/tests/`.

## Running Tests

All tests run in Docker. Feature tests do not need to mutate.

```bash
docker compose exec app php artisan test                                       # all tests
docker compose exec app php artisan test --coverage                            # with coverage
docker compose exec app ./vendor/bin/infection --threads=max --min-msi=100     # mutation
docker compose exec app php artisan test tests/Unit/ExampleTest.php            # specific file
```

## Test Configuration

- **Database**: RefreshDatabase trait for clean state
- **Environment**: phpunit.xml with testing-specific settings
- **Coverage**: reports in `coverage/` directory
- **Memory Limit**: 512M

## Writing Tests

```php
<?php

declare(strict_types=1);

namespace Tests\Unit;

use Tests\TestCase;

final class YourClassTest extends TestCase
{
    protected function setUp(): void
    {
        parent::setUp();

        $this->user = User::factory()->create();
    }

    public function test_it_describes_what_it_tests(): void
    {
        $result = someFunction();

        $this->assertSame('expected_value', $result);
    }
}
```

### Testing Controllers/Services

```php
public function test_it_updates_the_post(): void
{
    $post = Post::factory()->create();

    $response = $this->actingAs($post->author)
        ->put(route('posts.update', $post), ['title' => 'New title']);

    $response->assertRedirect(route('posts.index'));
    $this->assertSame('New title', $post->refresh()->title);
}
```

## Architectural Testing

Enforced rules (`tests/Unit/ArchTest.php`):
- No debugging functions in production code
- Models must extend Eloquent Model
- Controllers must have 'Controller' suffix
- Enums must be proper enum classes
