---
name: vuejs
description: "Dedicated Vue.js skill covering both Vue 2 (Options API) and Vue 3 (Composition API / script setup). Includes component patterns, reactivity, state management, routing, performance, and testing for both versions."
risk: safe
source: community
date_added: "2026-03-31"
---

# Vue.js

## Use This Skill When

- Building features in Vue 2 or Vue 3 applications
- Creating or refactoring components, composables, or mixins
- Designing state management, routing, and async data flows
- Migrating from Vue 2 (Options API) to Vue 3 (Composition API)
- Improving Vue app performance and maintainability
- Working with Vuex (Vue 2/3) or Pinia (Vue 3)

## Do Not Use This Skill When

- The frontend stack is not Vue
- The task is exclusively backend or framework-agnostic

---

## Version Reference

| Feature | Vue 2 | Vue 3 |
|---|---|---|
| Primary API | Options API | Composition API + `<script setup>` |
| Reactivity system | `Vue.observable()`, `Object.defineProperty` | `ref()`, `reactive()`, Proxy-based |
| State management | Vuex 3/4 | Pinia (recommended) or Vuex 4 |
| Router | Vue Router 3 | Vue Router 4 |
| Lifecycle hooks | `created`, `mounted` etc. in `options` | `onMounted()`, `onCreated()` etc. as functions |
| Global API | `Vue.use()`, `Vue.component()` | `createApp()`, `app.use()`, `app.component()` |
| Teleport | ❌ | `<Teleport to="body">` |
| Fragments | ❌ (single root) | ✅ (multiple root nodes) |
| TypeScript | Limited | First-class support |
| `v-model` | `.sync` modifier | Multiple `v-model` bindings |

> **Always confirm the project's Vue version before applying version-specific patterns.**

---

## Vue 2 Guidelines (Options API)

### Component Structure

```vue
<template>
  <div><!-- single root required --></div>
</template>

<script>
export default {
  name: 'MyComponent',
  props: {
    title: { type: String, required: true },
    count: { type: Number, default: 0 }
  },
  data() {
    return {
      localState: ''
    }
  },
  computed: {
    derivedValue() {
      return this.count * 2
    }
  },
  watch: {
    count(newVal, oldVal) { /* respond to changes */ }
  },
  methods: {
    handleClick() { /* ... */ }
  },
  mounted() { /* lifecycle */ },
  beforeDestroy() { /* cleanup */ }
}
</script>
```

### Vue 2 Rules

- Declare all reactive properties in `data()` upfront — Vue 2 cannot detect dynamically added root properties
- Use `Vue.set(obj, key, value)` or `this.$set()` when adding properties to reactive objects at runtime
- Use `Vue.delete()` / `this.$delete()` when removing reactive properties
- Use `$emit` with documented event names; prefer `kebab-case` for custom events
- Use **mixins** sparingly — extract shared logic, but prefer composable utilities in Vue 3
- Use `beforeDestroy` (not `unmounted`) for cleanup in Vue 2
- Use **Vuex** with strict modules for state management — define `state`, `getters`, `mutations`, `actions`

### Vuex 3 / 4 Patterns (Vue 2)

```js
// store/modules/user.js
export default {
  namespaced: true,
  state: () => ({ user: null }),
  getters: { isLoggedIn: state => !!state.user },
  mutations: { SET_USER(state, user) { state.user = user } },
  actions: {
    async fetchUser({ commit }, id) {
      const user = await api.getUser(id)
      commit('SET_USER', user)
    }
  }
}
```

---

## Vue 3 Guidelines (Composition API)

### Preferred Component Structure (`<script setup>`)

```vue
<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const props = defineProps({
  title: { type: String, required: true },
  count: { type: Number, default: 0 }
})

const emit = defineEmits(['update:count', 'submit'])

const localState = ref('')
const derivedValue = computed(() => props.count * 2)

watch(() => props.count, (newVal) => { /* respond */ })

onMounted(() => { /* setup */ })
</script>

<template>
  <div>
    <slot />
  </div>
</template>
```

### Reactivity Rules

- Use `ref()` for primitive values and single values; use `reactive()` for objects/structs
- **Never destructure** `reactive()` objects directly — use `toRefs()` to preserve reactivity
- Prefer `computed()` for derived values over duplicating state or using watchers
- Keep watchers intentional — use `watchEffect()` for automatic dependency tracking, `watch()` for explicit source control
- Always clean up subscriptions, intervals, and event listeners in `onUnmounted()`

### Composables

- Extract reusable reactive logic into **composables** (`use` prefix convention: `useUser`, `useFetch`, `useDebounce`)
- Composables must be called at the top level of `setup()` or `<script setup>` — never inside conditions or loops
- Return only what the consuming component needs from a composable

```js
// composables/useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const double = computed(() => count.value * 2)
  const increment = () => count.value++
  return { count, double, increment }
}
```

### Pinia (Vue 3 Recommended State Management)

```js
// stores/user.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  const user = ref(null)
  const isLoggedIn = computed(() => !!user.value)
  async function fetchUser(id) {
    user.value = await api.getUser(id)
  }
  return { user, isLoggedIn, fetchUser }
})
```

- Prefer **setup stores** (function syntax) over options stores for consistency with Composition API
- Use `storeToRefs()` when destructuring store state to preserve reactivity
- Use Pinia `$patch()` for batching multiple state updates

---

## Shared Principles (Vue 2 and Vue 3)

### Component Design

- Keep components **focused and single-purpose** — split large components when they exceed ~200 lines
- Define **explicit props contracts** with types, validators, and defaults
- Use `$emit` / `defineEmits` for all child-to-parent communication
- Avoid direct parent manipulation from a child (`$parent`) — use events or provide/inject
- Use `provide` / `inject` for deep prop passing instead of prop drilling
- Co-locate component-specific logic (CSS, tests) with the component file

### Template Practices

- Always use `:key` on `v-for` with a stable, unique ID — never use array index as key when the list can reorder
- Avoid `v-if` and `v-for` on the same element — wrap with `<template v-if>` instead
- Use `v-show` for frequent toggle visibility, `v-if` for conditional mounting
- Escape untrusted HTML — avoid `v-html` with user-supplied content

### Performance

- Lazy-load route-level components with dynamic imports: `component: () => import('./MyPage.vue')`
- Use `<KeepAlive>` for expensive components that are toggled but should retain state
- Avoid unnecessary reactive depth for large data structures — use `shallowRef()` / `shallowReactive()` where deep reactivity is not needed
- Handle **loading**, **empty**, and **error** states explicitly in every async operation
- Use `defineAsyncComponent()` for heavy async components

### Vue Router (3 for Vue 2, 4 for Vue 3)

- Use **named routes** — always navigate by name, not path string
- Define route-level **meta** fields for auth guards and page titles
- Use **Navigation Guards** (`beforeEach`) for auth checks and redirects
- Prefer **lazy-loaded routes** for all non-critical pages

---

## Testing

- Use **Vue Test Utils** (v1 for Vue 2, v2 for Vue 3) + **Vitest** or **Jest**
- Test component behavior from the user's perspective — not internal implementation details
- Use `mount()` for integration behavior, `shallowMount()` for unit isolation
- Test: prop rendering, emitted events, conditional visibility, slot content, and async data states
- Use `flushPromises()` to resolve async operations in tests

---

## Migration: Vue 2 → Vue 3

- Use the official **Vue Migration Build** as an intermediate step for large codebases
- Replace **mixins** with composables
- Replace `Vue.observable()` with `reactive()`
- Replace `Vue.set()` / `Vue.delete()` calls (no longer needed with Proxy-based reactivity)
- Replace `beforeDestroy` → `onBeforeUnmount`, `destroyed` → `onUnmounted`
- Replace `.sync` modifier → `v-model:propName`
- Replace Vuex → Pinia for new stores (Vuex 4 still works but Pinia is the standard)
- Replace `Vue.use()` → `app.use()` on the app instance

---

## Delivery Expectations

- Clearly label code as Vue 2 or Vue 3 when both could apply
- Production-ready components with typed props, emits, and explicit reactivity
- Readable templates and composables with strong naming conventions
- Actionable notes for follow-up improvements (e.g. migration candidates) when relevant
