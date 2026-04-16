# MOC — Frontend & UI

> Error patterns for web frontends, mobile apps, and UI frameworks. Frontend errors are unique because many are silent (wrong visual output, no error message) and debugging requires browser DevTools.

---

## [[React]]

### Common Errors
- `Cannot update a component ('X') while rendering a different component ('Y')` → [[State & Lifecycle Errors]]: State update during render phase.
  - **Fix**: Move state update to `useEffect` or event handler. Don't call `setState` during render.
- `Too many re-renders. React limits the number of renders to prevent an infinite loop` → [[Resource Exhaustion Errors]]: Infinite state update loop.
  - **Common cause**: `setState` called unconditionally in component body or `useEffect` without proper deps.
  - **Fix**: Check `useEffect` dependency array. Don't call `setState` in render. Use `useCallback` for event handlers.
- `Each child in a list should have a unique "key" prop` → [[Offset & Boundary Errors]]: Missing or non-unique keys.
  - **Fix**: Use stable, unique IDs as keys. Don't use array index as key (causes bugs on reorder/delete).
- `Cannot read properties of undefined (reading 'X')` → [[Null & Undefined Reference Errors]]: Data not loaded yet.
  - **Fix**: Add loading states, optional chaining (`data?.property`), default values.
- `Cannot read properties of null (reading 'X')` → [[Null & Undefined Reference Errors]]: Ref not attached or element not rendered.
- `Rendered fewer hooks than expected` → [[State & Lifecycle Errors]]: Hook called conditionally.
  - **Fix**: Hooks must be called in the same order every render. No hooks inside `if`, loops, or early returns.
- `Rendered more hooks than during the previous render` → [[State & Lifecycle Errors]]: Same as above.
- `Invalid hook call. Hooks can only be called inside of the body of a function component` → [[State & Lifecycle Errors]]: Hook called outside component, in class component, or multiple React copies.
- `Can't perform a React state update on an unmounted component` → [[State & Lifecycle Errors]]: Async operation completed after unmount.
  - **Fix**: Use AbortController in useEffect cleanup. Check mounted state. Use React Query/SWR for data fetching.
- `Warning: Can't call setState on a component that is not yet mounted` → [[State & Lifecycle Errors]]: setState before mount.
- `Objects are not valid as a React child` → [[Type & Casting Errors]]: Rendering an object instead of string/number/element.
  - **Fix**: Use `JSON.stringify()` for debugging, or access specific properties.
- `Functions are not valid as a React child` → [[Type & Casting Errors]]: Rendering a function instead of calling it.
  - **Fix**: Call the function: `{myFunc()}` not `{myFunc}`.
- `Maximum update depth exceeded` → [[Resource Exhaustion Errors]]: Infinite useEffect loop.
  - **Fix**: Check useEffect dependency array. Ensure deps don't change every render (use useMemo/useCallback).
- `Hydration failed because the initial UI does not match what was rendered on the server` → [[The Environment Delta]]: SSR/SSG mismatch.
  - **Common causes**: `Date.now()`, `Math.random()`, `window` access, browser-only APIs, conditional rendering based on client state.
  - **Fix**: Use `useEffect` for client-only code. Use `suppressHydrationWarning` for intentional mismatches.
- `Error: Minified React error #XXX` → Production build error. Look up error code at reactjs.org/docs/error-decoder.html.
- `Warning: Each child in an array or iterator should have a unique "key" prop` → Same as key prop error above.
- `Warning: Encountered two children with the same key` → [[Offset & Boundary Errors]]: Duplicate keys in list.
- `Error: Suspense boundary received an update before it finished hydrating` → [[State & Lifecycle Errors]]: State update during hydration.

### React Traps
- **Stale closures in hooks**: `useEffect`/`useCallback` capture variables at creation time. If deps are wrong, you see stale values.
  - **Fix**: Include all used variables in dependency array. Use `useRef` for mutable values that shouldn't trigger re-render.
- **useEffect cleanup timing**: Cleanup runs before the next effect, not on unmount only. Can cause unexpected behavior.
- **Context re-render cascade**: All consumers re-render when context value changes. Even if they only use part of the context.
  - **Fix**: Split context into smaller pieces. Use `useMemo` on context value. Consider Zustand/Jotai for fine-grained state.
- **Prop drilling**: Passing props through many levels. Makes code fragile.
  - **Fix**: Use Context, state management library, or component composition.
- **useEffect as lifecycle method**: Don't think of useEffect as componentDidMount/componentDidUpdate. Think of it as synchronization.
- **Missing dependency array**: `useEffect(() => {...})` runs every render. `useEffect(() => {...}, [])` runs once. Missing deps = stale data.
- **Object/array in dependency array**: Objects and arrays are compared by reference. New object every render = effect runs every render.
  - **Fix**: Use `useMemo` to memoize objects/arrays, or destructure to primitive deps.
- **Batching behavior**: React 18 batches all state updates (including async). React 17 only batches in event handlers.

### React Debug Tools
- React DevTools — component tree, props, state, hooks, profiler.
- `React.StrictMode` — highlights potential problems (double-renders in dev).
- `why-did-you-render` — logs unnecessary re-renders.
- React Profiler — measure render performance.
- Error Boundaries — catch render errors (`componentDidCatch`, `getDerivedStateFromError`).

---

## [[Next.js]]

### Common Errors
- `Hydration failed because the initial UI does not match what was rendered on the server` → [[The Environment Delta]]: Server/client mismatch.
- `Error: Dynamic server usage: X` → [[Configuration & Environment Errors]]: Using dynamic features (cookies, headers) in static page.
  - **Fix**: Use `export const dynamic = 'force-dynamic'` or move to server component.
- `Module not found: Can't resolve 'X'` in server components → [[Dependency & Import Errors]]: Client-only library in server component.
  - **Fix**: Move to client component (`'use client'`), or use dynamic import with `ssr: false`.
- `Error: Invariant: headers() expects to have requestAsyncStorage` → [[State & Lifecycle Errors]]: Using server-only API outside request context.
- `Error: NEXT_REDIRECT` → Not an error. Next.js uses thrown errors for redirects internally.
- `Error: Objects are not valid as a React child (found: [object Promise])` → [[Type & Casting Errors]]: Rendering a Promise. Need `await` in server component or `use()` in client component.
- `Error: Cannot find module 'X'` → [[Dependency & Import Errors]]: Module resolution failure.
- `Error: Unsupported Server Component type: undefined` → [[Type & Casting Errors]]: Default export missing or wrong.
- `Warning: Extra attributes from the server: class, style` → [[The Environment Delta]]: Browser extensions adding attributes.

### Next.js Traps
- **Server vs Client component boundary**: Server components can't use hooks, event handlers, or browser APIs. Client components can't be async.
- **Caching behavior**: Next.js aggressively caches. `fetch` in server components is cached by default. Use `cache: 'no-store'` or `revalidate`.
- **Middleware edge runtime**: Middleware runs on Edge Runtime. Limited Node.js API support. No `fs`, limited `crypto`.
- **`'use client'` boundary**: Only marks the entry point. Imported components are also client components.
- **Route handlers vs API routes**: App Router uses Route Handlers (`route.ts`). Pages Router uses API Routes (`pages/api`). Don't mix.
- **Parallel routes and intercepting routes**: Complex routing patterns that can cause unexpected behavior.

---

## [[Vue]]

### Common Errors
- `Cannot read properties of undefined (reading 'X')` → [[Null & Undefined Reference Errors]]: Reactive data not initialized.
- `Avoid mutating a prop directly` → [[State & Lifecycle Errors]]: Props are one-way data flow. Emit events instead.
- `Maximum recursive updates exceeded` → [[Resource Exhaustion Errors]]: Watcher triggering itself.
- `Property "X" was accessed during render but is not defined on instance` → [[Null & Undefined Reference Errors]]: Variable not declared in data/setup.
- `Component is missing template or render function` → [[Configuration & Environment Errors]]: Component has no template.
- `Unknown custom element: <X>` → [[Dependency & Import Errors]]: Component not registered.
- `Avoid using non-primitive value as key` → [[Type & Casting Errors]]: Using object as v-for key.
- `Invalid prop: type check failed for prop "X"` → [[Type & Casting Errors]]: Prop type validation failed.

### Vue Traps
- **Reactivity caveats (Vue 2)**: Adding new properties to objects isn't reactive. Use `Vue.set()` or `this.$set()`.
- **Reactivity caveats (Vue 3)**: `ref` vs `reactive`. Destructuring `reactive` loses reactivity. Use `toRefs()`.
- **`v-if` vs `v-show`**: `v-if` removes from DOM (lifecycle hooks fire). `v-show` hides with CSS (always in DOM).
- **`key` on `v-for`**: Always use unique key. Without key, Vue reuses elements (can cause bugs with stateful components).
- **Composition API vs Options API**: Don't mix in same component. Choose one pattern.

---

## [[Angular]]

### Common Errors
- `ExpressionChangedAfterItHasBeenCheckedError` → [[State & Lifecycle Errors]]: Value changed during change detection cycle.
  - **Fix**: Use `ChangeDetectorRef.detectChanges()`, move logic to `ngAfterViewInit`, or use `setTimeout`.
- `NullInjectorError: No provider for X` → [[Dependency & Import Errors]]: Service not provided in module/component.
  - **Fix**: Add to `providers` array in module or use `@Injectable({ providedIn: 'root' })`.
- `Circular dependency detected` → [[Dependency & Import Errors]]: Services depending on each other.
  - **Fix**: Use `forwardRef()`, restructure dependencies, or use an intermediary service.
- `Can't bind to 'X' since it isn't a known property of 'Y'` → [[Dependency & Import Errors]]: Module not imported or component not declared.
- `Template parse errors: 'X' is not a known element` → [[Dependency & Import Errors]]: Component not declared in module.
- `Error: NG0100: ExpressionChangedAfterItHasBeenCheckedError` → Same as above, Angular 14+ error code format.
- `Error: NG0200: Circular dependency in DI detected` → [[Dependency & Import Errors]].
- `Error: NG0300: Multiple components match node with tagname 'X'` → [[Dependency & Import Errors]]: Ambiguous component selector.
- `Error: NG0301: Export of name 'X' not found` → [[Dependency & Import Errors]]: Template reference variable export not found.

### Angular Traps
- **Change detection**: Angular checks all components by default. Use `OnPush` strategy for performance.
- **Zone.js**: Angular uses Zone.js to detect async operations. Some async operations (WebSocket, third-party libs) may not trigger change detection.
- **RxJS memory leaks**: Subscriptions not unsubscribed. Use `async` pipe, `takeUntil`, or `takeUntilDestroyed`.
- **Module system complexity**: NgModules, standalone components, lazy loading — multiple patterns coexist.
- **AOT vs JIT compilation**: AOT (production) is stricter than JIT (development). Code that works in dev may fail in prod.

---

## CSS / Layout Errors

### Common Issues
- **Element not visible** → Check (in order):
  1. `display: none` or `visibility: hidden`
  2. `opacity: 0`
  3. `height: 0` or `width: 0` with `overflow: hidden`
  4. `z-index` — element behind another element
  5. `position: absolute/fixed` — element positioned off-screen
  6. `clip-path` or `clip` hiding element
  7. `transform: scale(0)` or `transform: translateX(-9999px)`
  8. Parent has `overflow: hidden` and element is outside bounds
  9. `color` same as `background-color`
  10. `pointer-events: none` (visible but not interactive)

- **Layout shift (CLS)** → [[State & Lifecycle Errors]]: Content loading changes layout.
  - **Fix**: Set explicit dimensions on images/videos (`width`/`height` or `aspect-ratio`). Use skeleton screens. Avoid inserting content above existing content.

- **Flexbox issues**:
  - Items not wrapping → Add `flex-wrap: wrap`.
  - Items not growing → Check `flex-grow`, `flex-shrink`, `flex-basis`.
  - Items overflowing → Check `min-width: 0` (flex items have `min-width: auto` by default).
  - Centering not working → Check both `justify-content` AND `align-items`. Check flex direction.

- **Grid issues**:
  - Items not placed correctly → Check `grid-template-columns`/`grid-template-rows`. Use DevTools grid inspector.
  - Implicit grid → Items placed outside explicit grid create implicit tracks.
  - `fr` unit not working as expected → `fr` distributes remaining space after fixed sizes.

- **Responsive issues**:
  - Media queries not working → Check viewport meta tag: `<meta name="viewport" content="width=device-width, initial-scale=1">`.
  - Mobile layout broken → Test with device emulation. Check for fixed widths, horizontal overflow.
  - Font size too small on mobile → Use relative units (`rem`, `em`, `vw`). Minimum 16px for body text.

- **CSS specificity wars** → [[Concurrency & Race Condition Errors]]: Multiple rules targeting same element.
  - **Fix**: Use BEM naming, CSS Modules, CSS-in-JS, or Tailwind to avoid specificity conflicts.
  - **Debug**: DevTools shows which rules are applied and which are overridden.

- **CSS not loading** → [[Configuration & Environment Errors]]: Wrong path, MIME type error, or build tool not processing CSS.

### CSS Debug
- Browser DevTools Elements panel — inspect computed styles, box model, applied rules.
- DevTools Grid/Flexbox inspector — visual layout debugging.
- `outline: 1px solid red` — quick visual debugging (doesn't affect layout like `border`).
- `* { outline: 1px solid red; }` — see all element boundaries.

---

## Browser Errors

### Console Errors
- `CORS error` → [[Permission & Authorization Errors]]: See [[MOC — Security & Auth]] CORS section.
- `Mixed Content: The page was loaded over HTTPS, but requested an insecure resource` → [[Configuration & Environment Errors]]: HTTPS page loading HTTP resource.
  - **Fix**: Change all URLs to HTTPS. Use protocol-relative URLs (`//`) or always HTTPS.
- `SecurityError: Blocked a frame with origin "X" from accessing a cross-origin frame` → [[Permission & Authorization Errors]]: iframe cross-origin restriction.
- `QuotaExceededError: The quota has been exceeded` → [[Resource Exhaustion Errors]]: localStorage/IndexedDB/Cache API full.
  - **Fix**: Clean up old data. Use IndexedDB for large data (larger quota than localStorage).
- `NotAllowedError: play() failed because the user didn't interact with the document first` → [[Permission & Authorization Errors]]: Autoplay policy.
  - **Fix**: Wait for user interaction before playing audio/video. Use `muted` attribute for autoplay.
- `DOMException: Failed to execute 'querySelector' on 'Document': 'X' is not a valid selector` → [[Syntax & Parse Errors]]: Invalid CSS selector.
- `Uncaught (in promise)` → [[State & Lifecycle Errors]]: Unhandled Promise rejection. Add `.catch()` or try/catch with await.
- `ResizeObserver loop limit exceeded` → [[Resource Exhaustion Errors]]: ResizeObserver callback causing layout changes that trigger more observations.
  - **Fix**: Debounce the callback. Usually safe to ignore this error.
- `Script error.` (no details) → [[Permission & Authorization Errors]]: Cross-origin script error. Add `crossorigin` attribute to script tag and CORS headers on script server.

### Performance Issues
- **Slow initial load** → Bundle too large.
  - **Fix**: Code splitting, lazy loading, tree shaking, compression (gzip/brotli), CDN.
  - **Measure**: Lighthouse, WebPageTest, Chrome DevTools Performance tab.
- **Janky scrolling** → Main thread blocked.
  - **Fix**: Virtualize long lists (react-window, react-virtuoso). Debounce scroll handlers. Use `will-change` CSS property. Use `requestAnimationFrame`.
- **Memory leak** → [[Resource Exhaustion Errors]]: Event listeners not removed, intervals not cleared, detached DOM nodes, closures holding references.
  - **Debug**: Chrome DevTools Memory tab. Take heap snapshots, compare, find growing objects.
- **Layout thrashing** → Reading layout properties (offsetHeight) then writing (style changes) in a loop forces synchronous layout.
  - **Fix**: Batch reads and writes. Use `requestAnimationFrame`. Use CSS transforms instead of layout properties.

---

## Mobile ([[React Native]] / [[Flutter]])

### React Native Errors
- `Red screen of death` → Unhandled JS exception. Read the error message in Metro bundler.
- `Invariant Violation: "X" has not been registered` → [[Configuration & Environment Errors]]: App name mismatch between JS and native.
- `Unable to resolve module X` → [[Dependency & Import Errors]]: Module not found. Clear Metro cache: `npx react-native start --reset-cache`.
- `Native module X tried to override Y` → [[Dependency & Import Errors]]: Duplicate native module registration.
- `Bridge communication error` → [[Serialization & Encoding Errors]]: Non-serializable data crossing JS/Native bridge.
- `VirtualizedList: You have a large list that is slow to update` → [[Resource Exhaustion Errors]]: Use `FlatList` with proper `keyExtractor` and `getItemLayout`.
- `Require cycle` → [[Dependency & Import Errors]]: Circular dependency. Restructure imports.

### Flutter Errors
- `RenderFlex overflowed by X pixels` → [[Offset & Boundary Errors]]: Widget exceeds available space.
  - **Fix**: Use `Flexible`, `Expanded`, `SingleChildScrollView`, or `Wrap`.
- `setState() called after dispose()` → [[State & Lifecycle Errors]]: Async operation completed after widget disposed.
  - **Fix**: Check `mounted` before calling `setState()`.
- `A RenderFlex overflowed` → [[Offset & Boundary Errors]]: Same as above.
- `Null check operator used on a null value` → [[Null & Undefined Reference Errors]]: Using `!` on null.
- `type 'Null' is not a subtype of type 'X'` → [[Type & Casting Errors]]: Null where non-null expected.
- `MissingPluginException` → [[Dependency & Import Errors]]: Native plugin not installed. Run `flutter pub get` and rebuild.
- `Unhandled Exception: PlatformException` → [[Connection & Network Errors]] or [[Permission & Authorization Errors]]: Platform-specific error.

### Mobile-Specific Traps
- **Platform differences**: iOS and Android behave differently. Test on both.
- **Hot reload state**: Hot reload preserves state. Full restart (`R`) resets state. Stale state can cause confusing bugs.
- **Native module linking**: React Native native modules need linking. Auto-linking (RN 0.60+) usually works, but sometimes needs manual setup.
- **App Store/Play Store rejections**: Not a code error but a deployment error. Check guidelines before submission.
- **Deep linking**: URL scheme and universal links configuration is platform-specific and error-prone.
- **Push notification setup**: Complex setup involving certificates (iOS), Firebase (Android), and server-side integration.
- **Permissions**: Camera, location, storage permissions must be requested at runtime. Handle denial gracefully.
