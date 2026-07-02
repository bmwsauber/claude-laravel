---
name: react-expert
description: >-
    Senior React specialist for building Inertia.js frontends with React 19,
    TypeScript, and function components + hooks. Activates when creating or
    modifying React components, hooks, JSX/TSX, Inertia React pages, forms
    (useForm), or Tailwind-styled UI.

    Trigger words — EN: React, React component, hooks, JSX, TSX, useState,
    useEffect, Inertia React page, function component.

    Українською: React, React компонент, хуки, JSX, TSX, Inertia React сторінка,
    функційний компонент, стан компонента.
---

# React Expert

## Core Rules

- **Function components + hooks only** — never class components
- **TypeScript by default** — every new file is `.tsx`, fully typed props/state
- **Composition over inheritance** — small, focused components; compose via children/props
- **State**: local `useState`, derived `useMemo`, cross-component `useContext` —
  no external store library unless state genuinely outlives a page (see `.claude/rules/inertia-react.md`)

## References

| File | Covers |
|------|--------|
| `references/hooks.md` | `useState`, `useEffect`, `useMemo`, `useCallback`, `useContext`, custom hooks |
| `references/components.md` | Component structure, props typing, composition patterns |
| `references/typescript.md` | Typing props, generics, event handlers, `FC` vs explicit return types |
| `references/inertia-integration.md` | `usePage`, `useForm`, `router`, deferred props, `WhenVisible` |

## Documentation

Use Context7 (`resolve-library-id` → `query-docs`) for current React/Inertia React API details —
training data may lag behind the latest React 19 conventions.

## When to Apply

Activate this skill when:
- Creating or modifying a React component, page, or hook
- Wiring an Inertia.js page (`usePage`, `useForm`, `router`)
- Reviewing component structure, prop typing, or accessibility
- Deciding where state should live (component vs. context vs. props)

## Related Skills

- **PHPUnit Testing** — testing Controllers/Services that feed these pages
- **Playwright Expert** — E2E testing of the resulting UI
