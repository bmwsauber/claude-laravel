# TypeScript in React

## Typing Props

```tsx
interface PostCardProps {
    post: Post
    onDelete?: (id: number) => void
}

export function PostCard({ post, onDelete }: PostCardProps) {
    // ...
}
```

- Prefer `interface` for props (extendable, better error messages); `type` for unions/utility types
- Optional props use `?:`, never `| undefined` unless there's a semantic difference

## Typing Events

```tsx
function handleChange(e: React.ChangeEvent<HTMLInputElement>): void {
    setValue(e.target.value)
}

function handleSubmit(e: React.FormEvent<HTMLFormElement>): void {
    e.preventDefault()
}
```

## Typing Children

```tsx
interface CardProps {
    children: React.ReactNode
}
```

## Generic Components

```tsx
interface ListProps<T> {
    items: T[]
    renderItem: (item: T) => React.ReactNode
}

export function List<T>({ items, renderItem }: ListProps<T>) {
    return <ul>{items.map((item, i) => <li key={i}>{renderItem(item)}</li>)}</ul>
}
```

## Shared Domain Types

Keep model types close to the module that owns them and import from there —
`Modules/Post/resources/js/types.ts` exporting `Post`, `Comment`, etc. Mirror the
PHP model's public shape (what the Controller actually passes to `Inertia::render()`).

## Strictness

- `tsconfig.json` should run with `strict: true`
- Never use `any` — use `unknown` and narrow, or a precise type
- Avoid non-null assertions (`!`) — prefer explicit checks or optional chaining
