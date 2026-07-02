# Migrations & Queue Jobs

## Migration Conventions

- **Naming**: `create_posts_table`, `add_slug_to_posts_table`, `drop_legacy_field_from_users_table`
- Every migration must implement `down()` for reversibility
- Never modify existing migration files — always create a new one
- Migrations live inside the owning module: `Modules/{Name}/database/migrations/`,
  generated with `php artisan module:make-migration create_posts_table {Name}`

```php
<?php

declare(strict_types=1);

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::table('posts', function (Blueprint $table): void {
            $table->string('slug')->unique()->after('title');
        });
    }

    public function down(): void
    {
        Schema::table('posts', function (Blueprint $table): void {
            $table->dropColumn('slug');
        });
    }
};
```

## Queue Jobs

Standard `ShouldQueue` Job classes, generated with `php artisan module:make-job`:

```php
<?php

declare(strict_types=1);

namespace Modules\Post\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Queue\Queueable as QueueableTrait;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Modules\Post\Models\Post;

final class ProcessPostAnalytics implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    public int $tries = 3;
    public array $backoff = [30, 60, 120]; // exponential backoff (seconds)
    public int $timeout = 120;

    public function __construct(private readonly Post $post) {}

    public function handle(): void
    {
        // must be idempotent
    }
}

// Dispatch:
ProcessPostAnalytics::dispatch($post);
ProcessPostAnalytics::dispatch($post)->delay(now()->addMinutes(5));
```

## Idempotency

Jobs must produce the same result when run multiple times:

```php
public function handle(): void
{
    PostAnalytics::query()->updateOrCreate(
        ['post_id' => $this->post->getKey()],
        ['processed_at' => now()],
    );
}
```

## Unique Jobs

Prevent duplicate jobs for the same resource:

```php
use Illuminate\Contracts\Queue\ShouldBeUnique;

final class ProcessPostAnalytics implements ShouldQueue, ShouldBeUnique
{
    use InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(private readonly Post $post) {}

    public function uniqueId(): string
    {
        return (string) $this->post->getKey();
    }
}
```
