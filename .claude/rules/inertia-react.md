# Inertia v2 + React 19 Conventions

## Stack

- **Inertia.js v2** — server-driven SPA routing (`@inertiajs/react`)
- **React 19** — function components + hooks; never class components
- **TypeScript** — default for all files (`.tsx`)
- **Inertia shared props + React hooks** — `usePage()` for shared state, `useState`/`useContext` for local/cross-component state (no Redux/Zustand)
- **Ziggy** — `route()` helper for named routes

## Component Structure

```tsx
import { usePage, useForm } from '@inertiajs/react'
import { route } from 'ziggy-js'
import type { FC } from 'react'

interface PostPageProps {
    post: Post
}

const PostPage: FC<PostPageProps> = ({ post }) => {
    return <article>{post.title}</article>
}

export default PostPage
```

## Shared Props

Access shared props (from `HandleInertiaRequests`) via `usePage()` — never re-pass them as component props:

```tsx
interface SharedProps {
    auth: { user: User }
    flash: Flash
}

const { props } = usePage<SharedProps>()
const user = props.auth.user
```

## useForm + Form Request

```tsx
const form = useForm({ title: '', body: '' })

function submit(e: React.FormEvent): void {
    e.preventDefault()
    form.post(route('posts.store'), {
        onSuccess: () => form.reset(),
    })
}
```

Form Request validation errors map automatically to `form.errors.field`.

## Deferred Props (v2)

```php
return Inertia::render('Dashboard', [
    'user'  => $user,
    'stats' => Inertia::defer(fn () => $stats),
]);
```

```tsx
import { Deferred } from '@inertiajs/react'

<Deferred data="stats" fallback={<div className="animate-pulse h-20 bg-gray-200 rounded" />}>
    <StatsCard stats={stats} />
</Deferred>
```

## Partial Reloads

```tsx
import { router } from '@inertiajs/react'

router.reload({ only: ['posts'] })
```

## WhenVisible (Lazy Loading)

```tsx
import { WhenVisible } from '@inertiajs/react'

<WhenVisible data="comments" fallback={<LoadingSpinner />}>
    <CommentsList comments={comments} />
</WhenVisible>
```

## Redirects

```php
return redirect()->route('posts.index');           // Inertia redirect
return Inertia::location(route('posts.index'));     // external/full redirect
```

## State: Props + Hooks, Not a Store Library

Prefer Inertia shared/page props and local component state over a global store:

- **Page-level state**: props passed by the Controller via `Inertia::render()`
- **Cross-component state within a page**: `useContext` + `useReducer`/`useState`
- **Component-local state**: `useState`, `useMemo`, `useCallback`
- Reach for a store library only when state genuinely outlives a single page — discuss with `ddd-architect` first

## SSR Caveats

- No `window`/`document`/`localStorage` in the component body — wrap in `useEffect`
- Any browser-only initialization belongs inside `useEffect`, never at module scope
