# React Hooks

## useState

```tsx
const [count, setCount] = useState<number>(0)

setCount((prev) => prev + 1) // functional update when next value depends on previous
```

## useEffect

Runs after render; use for subscriptions, browser APIs, and anything that must not run during SSR/module scope.

```tsx
useEffect(() => {
    const handler = () => setWidth(window.innerWidth)
    window.addEventListener('resize', handler)
    return () => window.removeEventListener('resize', handler) // cleanup
}, [])
```

- Empty deps `[]` → run once on mount
- Omit deps → run every render (rarely correct)
- Always list every reactive value the effect reads

## useMemo / useCallback

Use only when a computation or function identity is expensive to recreate and is a dependency elsewhere — not as a default habit.

```tsx
const sorted = useMemo(() => [...posts].sort(byDate), [posts])
const handleSubmit = useCallback((id: number) => form.post(route('posts.update', id)), [form])
```

## useContext

For cross-component state within a page — pair with a small provider, not a global store:

```tsx
const PostContext = createContext<PostContextValue | null>(null)

export function usePostContext(): PostContextValue {
    const ctx = useContext(PostContext)
    if (!ctx) throw new Error('usePostContext must be used within PostProvider')
    return ctx
}
```

## Custom Hooks

Extract reactive logic with a `use*` prefix; a custom hook is just a function that calls other hooks.

```tsx
function usePagination(initial: number = 1) {
    const [page, setPage] = useState(initial)
    const next = useCallback(() => setPage((p) => p + 1), [])
    const prev = useCallback(() => setPage((p) => Math.max(1, p - 1)), [])
    return { page, next, prev }
}
```

## Rules of Hooks

- Call hooks only at the top level — never inside loops, conditions, or nested functions
- Call hooks only from React function components or custom hooks
- Keep dependency arrays exhaustive (`eslint-plugin-react-hooks` catches most mistakes)
