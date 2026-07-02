---
name: phpunit-testing
description: >-
    Tests applications using PHPUnit. Activates when writing tests, creating
    unit or feature tests, adding assertions, debugging test failures, working
    with data providers or mocking; or when the user mentions test, spec, TDD,
    assertion, coverage, or needs to verify functionality works.

    Українською: тестування PHPUnit, написати тест, створи тест, юніт тест, фіча тест, assertions, data provider, мок, покриття, мутаційне тестування, TDD, додай тест, перевірка функціональності
---

# PHPUnit Testing

## When to Apply

Activate this skill when:

- Creating new tests (unit or feature)
- Modifying existing tests
- Debugging test failures
- Writing architecture tests

## Documentation

Use `search-docs` for detailed PHPUnit / Laravel testing patterns.

## Basic Usage

### Creating Tests

Use `php artisan make:test {Name}Test` (add `--unit` for `tests/Unit`).

### Test Organization

- Unit/Feature tests: `tests/Feature` and `tests/Unit` directories.
- Module-specific tests may live alongside the module: `Modules/{Name}/tests/`.
- Do NOT remove tests without approval — these are core application code.

### Models Testing Policy

- DO NOT create unit tests for Laravel Eloquent models.
- Rationale:
    - Laravel's Eloquent ORM is extensively tested by the Laravel team
    - Testing basic CRUD operations, relationships, and standard functionality
      provides no value
    - Models are excluded from code coverage metrics (see `phpunit.xml`)
- What NOT to test:
    - Basic relationships (hasOne, hasMany, belongsTo, etc.)
    - Simple CRUD operations (create, update, delete, find)
    - Standard Eloquent functionality
    - Factory creation without custom logic
    - Basic fillable/guarded attributes
    - Standard casting functionality
- Exceptions — What TO test:
    - Custom business logic methods
    - Complex accessors/mutators with business rules
    - Custom scopes with specific logic
    - Observer behavior and side effects
    - Mass assignment protection (if critical)
- Where to test model functionality instead:
    - Feature tests via HTTP endpoints and workflows
    - Integration tests for model interactions
    - Observer tests for event handlers
    - Controller/Service tests for business logic

### Basic Test Structure

<code-snippet name="Basic PHPUnit Test Example" lang="php">

final class ExampleTest extends TestCase
{
    public function test_it_is_true(): void
    {
        $this->assertTrue(true);
    }
}

</code-snippet>

### Running Tests

- Run a single test with filter before finalizing: `php artisan test --filter=test_name`.
- Run all tests: `php artisan test`.
- Run file: `php artisan test tests/Feature/ExampleTest.php`.

## Assertions

Use specific assertions (`assertSuccessful()`, `assertNotFound()`) instead of
`assertStatus()`:

<code-snippet name="PHPUnit Response Assertion" lang="php">

public function test_it_returns_all(): void
{
    $this->postJson('/api/docs', [])->assertSuccessful();
}

</code-snippet>

| Use                  | Instead of          |
| -------------------- | -------------------- |
| `assertSuccessful()` | `assertStatus(200)` |
| `assertNotFound()`   | `assertStatus(404)` |
| `assertForbidden()`  | `assertStatus(403)` |

## Mocking

Use Laravel's built-in facades (`Mockery`/`$this->mock()`) or `Event::fake()`, `Queue::fake()`, `Notification::fake()`.

## Data Providers

Use PHPUnit data providers for repetitive tests (validation rules, etc.):

<code-snippet name="PHPUnit Data Provider Example" lang="php">

#[DataProvider('emailProvider')]
public function test_it_has_emails(string $email): void
{
    $this->assertNotEmpty($email);
}

public static function emailProvider(): array
{
    return [
        'james'  => ['james@laravel.com'],
        'taylor' => ['taylor@laravel.com'],
    ];
}

</code-snippet>

## Mutation Testing

Use **Infection** for mutation testing on top of PHPUnit:

```bash
docker compose exec app ./vendor/bin/infection --threads=max --min-msi=100
```

- Minimum score: 100% for covered code.
- Fix any surviving mutants by improving assertions, not implementation.

## Architecture Testing

Enforce code conventions with `ta-tikoma/phpunit-architecture-test`:

<code-snippet name="Architecture Test Example" lang="php">

public function test_controllers_have_suffix(): void
{
    Architecture::rule()
        ->classes()
        ->that()->resideInNamespace('Modules\Post\Http\Controllers')
        ->should()->haveNameMatching('*Controller');
}

</code-snippet>

## Common Pitfalls

- Using `assertStatus(200)` instead of `assertSuccessful()`
- Forgetting data providers for repetitive validation tests
- Deleting tests without approval
- Testing implementation details instead of behavior

## Related Skills

- **Test Master** - Testing strategies
