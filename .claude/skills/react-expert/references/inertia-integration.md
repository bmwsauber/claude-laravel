# Inertia React Integration

Full conventions live in `.claude/rules/inertia-react.md` — this is the quick-reference companion.

## Page Component

```tsx
import type { PageProps } from '@/types'

interface Props extends PageProps {
    post: Post
}

export default function ShowPost({ post }: Props) {
    return <article>{post.title}</article>
}
```

## usePage — Shared Props

```tsx
import { usePage } from '@inertiajs/react'

const { props } = usePage<{ auth: { user: User } }>()
const user = props.auth.user
```

## useForm

```tsx
import { useForm } from '@inertiajs/react'

const form = useForm({ title: '', body: '' })

function submit(e: React.FormEvent) {
    e.preventDefault()
    form.post(route('posts.store'), { onSuccess: () => form.reset() })
}

// form.errors.title, form.processing, form.data
```

## Links & Navigation

```tsx
import { Link, router } from '@inertiajs/react'

<Link href={route('posts.index')}>Back to posts</Link>

router.visit(route('posts.show', post.id))
router.reload({ only: ['posts'] }) // partial reload
```

## Deferred Props & WhenVisible

```tsx
import { Deferred, WhenVisible } from '@inertiajs/react'

<Deferred data="stats" fallback={<Spinner />}>
    <StatsCard stats={stats} />
</Deferred>

<WhenVisible data="comments" fallback={<Spinner />}>
    <CommentsList comments={comments} />
</WhenVisible>
```

## Routing

Always use `route()` from Ziggy for named routes — never hardcode URLs:

```tsx
import { route } from 'ziggy-js'

route('posts.show', post.id)
```
