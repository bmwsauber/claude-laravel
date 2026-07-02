# Component Structure & Composition

## Basic Component

```tsx
interface ButtonProps {
    label: string
    onClick: () => void
    variant?: 'primary' | 'secondary'
}

export function Button({ label, onClick, variant = 'primary' }: ButtonProps) {
    return (
        <button
            onClick={onClick}
            className={variant === 'primary' ? 'bg-blue-600 text-white' : 'bg-gray-200'}
        >
            {label}
        </button>
    )
}
```

- Prefer named function declarations over `const X: FC<Props> = (...) => {}` — clearer stack traces, no implicit `children`
- Destructure props in the signature; default values inline

## Composition Over Configuration

Prefer children/slots over large prop objects for layout flexibility:

```tsx
<Card>
    <Card.Header>{post.title}</Card.Header>
    <Card.Body>{post.excerpt}</Card.Body>
</Card>
```

## Directory Convention (per module)

```
Modules/Post/resources/js/
├── Pages/          # Inertia pages — receive props from the Controller
├── Components/     # reusable, prop-driven, emit callbacks via props
│   └── UI/          # generic UI primitives (Button, Modal, Input)
└── Hooks/           # use* custom hooks
```

## Events

React uses callback props, not emitted events:

```tsx
interface CommentFormProps {
    onSubmit: (body: string) => void
}

export function CommentForm({ onSubmit }: CommentFormProps) {
    const [body, setBody] = useState('')
    return (
        <form onSubmit={(e) => { e.preventDefault(); onSubmit(body) }}>
            <textarea value={body} onChange={(e) => setBody(e.target.value)} />
        </form>
    )
}
```

## Lists & Keys

```tsx
{posts.map((post) => (
    <PostCard key={post.id} post={post} />
))}
```

Never use array index as `key` when the list can reorder, filter, or grow.
