# Form Requests & Authorization

## Form Request

All form submissions require a Form Request. Never validate in the Controller or Service body.

```php
<?php

declare(strict_types=1);

namespace Modules\Post\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

final class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true; // Auth check done via Policy in the Controller
    }

    /** @return array<string, array<string>> */
    public function rules(): array
    {
        return [
            'title'       => ['required', 'string', 'max:255'],
            'description' => ['required', 'string', 'max:5000'],
        ];
    }
}
```

## Authorization in Controllers

Call `$this->authorize()` in the Controller for Policy checks, then delegate to the Service:

```php
final class UpdatePostController extends Controller
{
    public function __construct(private readonly PostService $posts) {}

    public function __invoke(UpdatePostRequest $request, Post $post): RedirectResponse
    {
        $this->authorize('update', $post);

        $this->posts->update($post, $request->validated());

        return redirect()->route('posts.index');
    }
}
```

## Policy Pattern

```php
<?php

declare(strict_types=1);

namespace Modules\Post\Policies;

use Modules\Post\Models\Post;
use App\Models\User;

final class PostPolicy
{
    public function update(User $user, Post $post): bool
    {
        return $user->getKey() === $post->author_id;
    }

    public function delete(User $user, Post $post): bool
    {
        return $user->getKey() === $post->author_id;
    }
}
```

Register in the module's `ServiceProvider::boot()`:
```php
Gate::policy(Post::class, PostPolicy::class);
```

## Validation Error Flow

Laravel Form Request errors → `$page.props.errors` in Inertia → `useForm().errors.field` in React.
