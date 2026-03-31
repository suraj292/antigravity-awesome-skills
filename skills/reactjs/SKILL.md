---
name: reactjs
description: "Dedicated React.js skill for building and maintaining modern React applications (React 17/18+) with clear component boundaries, hooks patterns, predictable state flow, performance-aware rendering, accessibility, and robust testing."
risk: safe
source: community
date_added: "2026-03-31"
---

# React.js

## Use This Skill When

- Building new React features or refactoring existing ones (React 17 / 18+)
- Designing component boundaries and state ownership
- Implementing async data handling, side-effect management, and data fetching
- Reviewing React code for correctness, accessibility, and performance
- Working with React Router, Context, Zustand, Redux Toolkit, or React Query / SWR
- Writing tests with React Testing Library and Vitest / Jest

## Do Not Use This Skill When

- The project is not React-based
- The request is specific to another framework (Vue, Angular, Svelte)
- The task concerns React Native mobile — use `react-native-architecture` skill instead

---

## Version Reference

| Feature | React 17 | React 18 |
|---|---|---|
| Concurrent features | ❌ | ✅ (`startTransition`, `useTransition`, `useDeferredValue`) |
| Automatic batching | Manual (`unstable_batchedUpdates`) | ✅ Automatic in all contexts |
| Suspense for data | Limited | ✅ Full support with frameworks |
| `useId()` hook | ❌ | ✅ |
| `useSyncExternalStore()` | ❌ | ✅ (for external store subscriptions) |
| Server Components | ❌ | ✅ (via Next.js App Router) |
| Root API | `ReactDOM.render()` | `createRoot()` |
| Strict Mode | Double renders in dev | Double renders + effects in dev |

> **Always confirm the React version before suggesting version-specific APIs.**

---

## Component Design Principles

- Keep components **small, composable, and single-responsibility** — split when a component exceeds ~150 lines or has more than one clear concern
- Prefer **function components** — never write new class components
- Express the UI as a function of state: `UI = f(state)`
- Co-locate state with the component that owns it — lift state only as far as needed
- Avoid **prop drilling** beyond 2 levels — use Context or a state manager instead
- Use **semantic HTML** (`<button>`, `<nav>`, `<main>`, `<section>`) for inherent accessibility

---

## Hooks Guidelines

### Core Hook Rules

- Only call hooks at the **top level** — never inside conditions, loops, or nested functions
- Only call hooks inside **React functions** (components or custom hooks)
- Prefix custom hooks with `use` — this is required for the linting rules to work correctly

### useState

- Model state as **minimal and normalized** — never duplicate values that can be derived
- Use functional updates `setState(prev => ...)` when new state depends on previous state
- Initialize expensive state with a **lazy initializer function**: `useState(() => computeExpensive())`

### useEffect

- Write effects to be **idempotent** and **cleanup-safe** — always return a cleanup function for subscriptions, intervals, and event listeners
- Prefer primitive values in dependency arrays over object references to avoid stale closures
- Never fetch data directly inside `useEffect` without abort handling:

```jsx
useEffect(() => {
  const controller = new AbortController()
  fetch(url, { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => { if (err.name !== 'AbortError') setError(err) })
  return () => controller.abort()
}, [url])
```

- Prefer **React Query / SWR** over manual `useEffect` data fetching for server state

### useMemo and useCallback

- Use `useMemo` for **expensive computations** that are confirmed bottlenecks — not by default
- Use `useCallback` to **stabilize callback references** passed to memoized children or dependency arrays
- Profile with React DevTools **Profiler** before adding memoization — premature memoization adds complexity without benefit

### useRef

- Use `useRef` to store **mutable values that do not trigger re-renders** (timers, previous values, DOM nodes)
- Never mutate `ref.current` during render
- Use `useImperativeHandle` + `forwardRef` to expose controlled imperative APIs from a component

### React 18 Hooks

- Use `useTransition()` to mark **non-urgent state updates** and keep the UI responsive during heavy re-renders
- Use `useDeferredValue()` to debounce expensive derived rendering tied to user input
- Use `useId()` for generating **stable, unique IDs** for accessibility attributes (`aria-labelledby`, `htmlFor`)
- Use `useSyncExternalStore()` for subscribing to external stores in a concurrent-safe way

---

## State Management

### Local State First

- Default to `useState` and `useReducer` — reach for external state management only when local state becomes genuinely insufficient
- Use `useReducer` when state has complex transitions with multiple sub-values or when the next state depends on the previous in non-trivial ways

### Context API

- Use React Context for **truly global, low-frequency** data (theme, locale, auth user)
- Never put high-frequency or large data in Context — it re-renders all consumers on every change
- Split context into smaller providers to minimize re-render surface

### Zustand (Lightweight Global State)

```js
import { create } from 'zustand'

const useUserStore = create((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null })
}))
```

- Prefer Zustand over Redux for most SPAs — simpler API, no boilerplate
- Use **selectors** to subscribe components to only the slice of store they need: `useUserStore(s => s.user)`

### Redux Toolkit (Complex Multi-Domain State)

- Use `createSlice` — never write reducers manually
- Use `createAsyncThunk` for async operations; prefer RTK Query for server state
- Normalize relational data with `createEntityAdapter`

### Server State (React Query / SWR)

- Use **React Query** (`@tanstack/react-query`) or **SWR** for async server data — not `useEffect` + `useState`
- These handle caching, deduplication, background refresh, loading/error states, and pagination

```jsx
const { data, isLoading, error } = useQuery({
  queryKey: ['user', userId],
  queryFn: () => fetchUser(userId)
})
```

---

## Performance Guidance

- Prevent unnecessary re-renders via **stable props** — extract static objects/functions outside the component or memoize
- Use `React.memo()` on components that receive stable props and render often
- **Split large UI trees** and lazy-load heavy boundaries:

```jsx
const HeavyComponent = React.lazy(() => import('./HeavyComponent'))
// Wrap in <Suspense fallback={<Loader />}>
```

- Use **efficient list rendering**: always provide a stable, unique `key` — never use array index when the list reorders
- Use `content-visibility: auto` CSS for very long off-screen lists
- Use `startTransition` (React 18) to keep input responsive during expensive renders
- **Profile before optimizing** — use React DevTools Profiler to confirm measured bottlenecks

---

## Data Fetching Patterns

| Pattern | When to Use |
|---|---|
| React Query / SWR | Server state (REST/GraphQL), caching, background sync |
| Server Components (Next.js) | Fetch at the server level, zero client JS |
| `useEffect` + fetch | Simple one-off fetches with no caching needs |
| `loader` (React Router 6.4+) | Route-level data loading before render |

---

## Routing (React Router v6)

- Use **`<Outlet />`** for nested routes — eliminates layout duplication
- Use **`loader`** and **`action`** functions (RR 6.4+) for data fetching and mutations at the route level
- Use **`useNavigate()`** over `<Redirect>` for programmatic navigation
- Use **`<Navigate />`** for declarative redirects in JSX
- Protect routes with a wrapper component or `loader` that redirects unauthenticated users
- Use `useParams()`, `useSearchParams()`, and `useLocation()` — never read from `window.location` directly

---

## Accessibility (a11y)

- Use semantic HTML elements — `<button>` not `<div onClick>`
- Every interactive element needs a visible or `aria-label` label
- Manage **focus** explicitly after dynamic content changes (modals, notifications)
- Use `useId()` (React 18) to link `<label htmlFor>` with form inputs reliably
- Test with keyboard navigation and a screen reader (NVDA / VoiceOver)
- Aim for WCAG 2.1 AA compliance minimally

---

## Testing

- Use **React Testing Library** + Vitest or Jest — never test implementation details
- Query elements the way users find them: `getByRole`, `getByLabelText`, `getByText`
- Avoid `getByTestId` except as a last resort
- Test: rendering, user interactions, conditional states, async loading/error, and form submission
- Use `userEvent` (from `@testing-library/user-event`) over `fireEvent` for realistic interaction simulation
- Use `msw` (Mock Service Worker) for mocking API calls in tests
- Use `waitFor()` or `findBy*` queries for async assertions

```jsx
it('loads and displays the user name', async () => {
  render(<UserProfile userId="1" />)
  expect(await screen.findByText('Jane Doe')).toBeInTheDocument()
})
```

---

## File and Folder Structure

```
src/
  components/        # Shared, reusable UI components
  features/          # Feature-specific components, hooks, and services
    user/
      UserProfile.jsx
      useUser.js
      userSlice.js
  hooks/             # Global custom hooks
  pages/ (or routes/)
  services/          # API clients, external integrations
  store/             # Global state setup (Zustand / Redux)
  utils/             # Pure helper functions
```

---

## Delivery Expectations

- Production-ready React code with predictable behavior and no stale closures
- Typed props with PropTypes or TypeScript interfaces
- Accessible markup with semantic HTML and ARIA where needed
- Tests for critical interactions, async states, and state transitions
- Brief tradeoff notes when multiple valid patterns exist (e.g. Context vs Zustand, useEffect vs React Query)
- No TODO comments in delivered code — resolve or document as a follow-up task
