# Implementation Plan: UI Redesign & Animations

## Overview

Layer a cohesive, premium motion system on top of the existing LibasTrack design system. The approach is additive — introduce shared utilities, wrap existing components with Framer Motion primitives, and add new components (Skeleton, CommandPalette, useCountUp) without touching data flows or backend contracts.

## Tasks

- [x] 1. Install fast-check and set up motion system foundation
  - Run `npm install --save-dev fast-check` in `frontend/`
  - Create `frontend/src/utils/motion.js` exporting all 8 required named variants: `fadeIn`, `fadeInUp`, `fadeInDown`, `scaleIn`, `slideInLeft`, `slideInRight`, `staggerContainer`, `staggerItem`
  - Export a shared `transition` object with `duration: 0.3`, `ease: [0.4, 0, 0.2, 1]`, `type: 'tween'`
  - Implement `prefersReducedMotion()` guard: when active, all variant `y`/`x`/`scale` values become `0` and `transition.duration` becomes `0.01`
  - All variants must use only `opacity`, `x`, `y`, `scale`, `rotate` — never layout-triggering properties
  - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 15.1, 15.2_

  - [x] 1.1 Write property test — P1: All required variants exported
    - **Property 1: All required motion variants are exported**
    - Use `fc.constantFrom(...variantNames)` to verify each name is a non-null exported object
    - Tag: `// Feature: ui-redesign-animations, Property 1`
    - **Validates: Requirements 1.1**

  - [x] 1.2 Write property test — P2: Reduced-motion collapses durations
    - **Property 2: Reduced-motion collapses all animation durations**
    - Mock `window.matchMedia` to return `matches: true`, verify effective duration ≤ 0.01 and translate/scale values are 0
    - Tag: `// Feature: ui-redesign-animations, Property 2`
    - **Validates: Requirements 1.4, 15.1**

  - [x] 1.3 Write property test — P3: GPU-composited properties only
    - **Property 3: Motion variants use only GPU-composited properties**
    - For each variant, assert no animated key is `width`, `height`, `top`, `left`, or `margin`
    - Tag: `// Feature: ui-redesign-animations, Property 3`
    - **Validates: Requirements 15.2**

- [x] 2. Implement `useCountUp` hook
  - Create `frontend/src/hooks/useCountUp.js`
  - Signature: `useCountUp(target: number, duration: number = 1000): number`
  - Use `requestAnimationFrame` exclusively — no `setInterval`
  - Return current animated value; start at 0, monotonically increase to `target`
  - Cancel rAF loop on unmount via `useEffect` cleanup
  - When `prefersReducedMotion()` is true, return `target` immediately
  - _Requirements: 5.2, 12.3, 15.6_

  - [x] 2.1 Write property test — P4: Count-up produces values 0→target
    - **Property 4: Count-up hook produces values from 0 to target**
    - Use `fc.integer({ min: 1, max: 1_000_000 })` to verify monotonic increase ending at target
    - Tag: `// Feature: ui-redesign-animations, Property 4`
    - **Validates: Requirements 5.2, 12.3**

  - [x] 2.2 Write property test — P5: Count-up uses requestAnimationFrame
    - **Property 5: Count-up hook uses requestAnimationFrame**
    - Mock `requestAnimationFrame` and `setInterval`; verify rAF is called and setInterval is not
    - Use `fc.integer({ min: 1, max: 9999 })`
    - Tag: `// Feature: ui-redesign-animations, Property 5`
    - **Validates: Requirements 15.6**

- [x] 3. Implement `Skeleton` component
  - Create `frontend/src/components/Skeleton.js`
  - Accept props: `width`, `height`, `borderRadius`, `className`
  - Apply props as inline styles on the rendered `div`
  - Add shimmer CSS class; shimmer uses `::after` pseudo-element with `transform: translateX(-100%)` → `translateX(100%)` keyframe (GPU-composited)
  - Add `@keyframes shimmer` and `.skeleton` / `.skeleton::after` rules to `frontend/src/index.css`
  - Shimmer gradient uses `var(--bg-layer2)` base and `var(--bg-layer3)` highlight, 1.5s loop
  - _Requirements: 4.2, 4.3, 4.5, 15.3_

  - [x] 3.1 Write property test — P6: Skeleton applies dimension props
    - **Property 6: Skeleton accepts and applies arbitrary dimension props**
    - Use `fc.integer` and `fc.string` arbitraries for CSS values; verify rendered element styles match
    - Tag: `// Feature: ui-redesign-animations, Property 6`
    - **Validates: Requirements 4.5**

- [x] 4. Extract shared nav data and create `navItems.js`
  - Create `frontend/src/utils/navItems.js` exporting `NAV_ITEMS` array (all sidebar nav destinations) and `QUICK_ACTIONS` array (six dashboard quick actions)
  - Update `frontend/src/components/Layout.js` to import `NAV_ITEMS` from `navItems.js` instead of the local `NAV` constant
  - Update `frontend/src/pages/Dashboard.js` to import `QUICK_ACTIONS` from `navItems.js`
  - _Requirements: 7.5_

- [~] 5. Implement `CommandPalette` component
  - Create `frontend/src/components/CommandPalette.js`
  - Props: `isOpen: boolean`, `onClose: () => void`
  - Portal-render via `ReactDOM.createPortal` into `document.body`
  - Full-screen overlay backdrop with `backdrop-filter: blur(12px)` glassmorphism; close on backdrop click
  - Centred panel with glassmorphism styling; bottom-sheet positioning when viewport width < 480px
  - Auto-focus search input on open
  - Filter `NAV_ITEMS` + `QUICK_ACTIONS` by case-insensitive `includes` on label/description in real time
  - Keyboard navigation: `ArrowUp`/`ArrowDown` to move between results, `Enter` to select and navigate, `Escape` to close
  - Animate open: `scaleIn` + `fadeIn` over 0.2s; animate close: reverse `scaleIn` + `fadeOut` over 0.15s using `AnimatePresence`
  - Mount only when `isOpen` is true — DOM node absent when closed
  - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7.9, 7.10, 14.5, 15.4_

  - [~] 5.1 Write property test — P8: Command palette filters correctly
    - **Property 8: Command palette filters results correctly**
    - Use `fc.string()` for search queries; verify every displayed result contains the query string (case-insensitive)
    - Tag: `// Feature: ui-redesign-animations, Property 8`
    - **Validates: Requirements 7.4**

  - [~] 5.2 Write property test — P9: Command palette contains all nav items
    - **Property 9: Command palette contains all navigation destinations**
    - Use `fc.constantFrom(...NAV_ITEMS)` to verify each item is findable in the unfiltered list
    - Tag: `// Feature: ui-redesign-animations, Property 9`
    - **Validates: Requirements 7.5**

  - [~] 5.3 Write property test — P10: Keyboard navigation cycles through all results
    - **Property 10: Command palette keyboard navigation cycles through all results**
    - Use `fc.integer({ min: 1, max: 20 })` for result count; verify ArrowDown wraps and ArrowUp reverses
    - Tag: `// Feature: ui-redesign-animations, Property 10`
    - **Validates: Requirements 7.9**

  - [~] 5.4 Write property test — P11: Command palette DOM absent when closed
    - **Property 11: Command palette DOM node is absent when closed**
    - Use `fc.constantFrom('escape', 'outside', 'enter')` for close actions; verify DOM node is unmounted after close
    - Tag: `// Feature: ui-redesign-animations, Property 11`
    - **Validates: Requirements 15.4**

  - [~] 5.5 Write property test — P17: Bottom sheet on narrow viewports
    - **Property 17: Command palette displays as bottom sheet on narrow viewports**
    - Use `fc.integer({ min: 320, max: 479 })` for viewport width; verify bottom-sheet positioning class/style is applied
    - Tag: `// Feature: ui-redesign-animations, Property 17`
    - **Validates: Requirements 14.5**

- [~] 6. Checkpoint — Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [~] 7. Update `Layout.js` — collapsible sidebar, page transitions, CommandPalette integration
  - Add `collapsed` state initialised from `localStorage.getItem('sidebar_collapsed')` with `try/catch` fallback to `false`
  - Persist `collapsed` to `localStorage` on every toggle
  - Animate sidebar width between 232px (expanded) and 60px (collapsed) using Framer Motion `animate` prop with `0.25s cubic-bezier(0.4,0,0.2,1)` transition
  - Animate `main-content` `marginLeft` in sync with sidebar width
  - Add collapse toggle button to sidebar
  - Show nav item labels only when expanded; show tooltips on hover when collapsed
  - Add `layoutId="active-nav-indicator"` shared element for the active nav item left-border accent
  - Add `layoutId="nav-hover-bg"` shared background highlight on nav item hover
  - Add brand icon pulse glow animation (3s loop on `var(--accent)`)
  - Wrap `<Outlet />` in `AnimatePresence` keyed on `location.pathname`; wrap page content in `motion.div` with `fadeInUp` variant (0.3s)
  - Add `Ctrl+K` / `Cmd+K` keydown listener; render `<CommandPalette>` with `isOpen` state
  - Preserve mobile overlay drawer behaviour at `< 768px`
  - Add vertical gradient accent stripe on sidebar right edge
  - Add scroll fade-out gradients at top/bottom of sidebar nav overflow area
  - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3, 3.4, 3.5, 3.6, 3.7, 3.8, 3.9, 7.1, 8.1, 8.2, 8.3, 8.4, 14.1, 14.2, 14.3_

  - [~] 7.1 Write property test — P12: Sidebar collapse persists to localStorage
    - **Property 12: Sidebar collapse state persists to localStorage**
    - Use `fc.array(fc.boolean())` for toggle sequences; verify `localStorage['sidebar_collapsed']` matches current state after each toggle
    - Tag: `// Feature: ui-redesign-animations, Property 12`
    - **Validates: Requirements 3.5**

  - [~] 7.2 Write property test — P13: Collapsed sidebar shows tooltips for all nav items
    - **Property 13: Collapsed sidebar shows tooltips for all nav items**
    - Use `fc.constantFrom(...NAV_ITEMS)` to verify tooltip text matches item label when sidebar is collapsed
    - Tag: `// Feature: ui-redesign-animations, Property 13`
    - **Validates: Requirements 3.3**

- [~] 8. Update `index.css` — CSS transitions on design tokens, skeleton keyframes, theme toggle spring
  - Add `transition` declarations to all design-token-consuming properties (background, color, border-color) across `.card`, `.stat-card`, `.nav-item`, `.form-input`, `.form-select`, `.form-textarea`, `.btn-primary`, `.btn-secondary`, `.btn-ghost`, `.chip`, `.modal`, table rows — 0.3s for theme changes
  - Add `@keyframes shimmer` for the skeleton sweep animation using `transform: translateX`
  - Add `.skeleton` and `.skeleton::after` CSS rules
  - Add `.toggle-thumb` spring transition using CSS `transition: transform 0.2s cubic-bezier(0.34,1.56,0.64,1)` (approximating spring stiffness 400 / damping 30)
  - Add `.theme-icon` rotation animation (180deg + opacity fade, 0.25s) for the ☾/☀ icon
  - Add `.page-header--scrolled` class with `backdrop-filter: blur(12px)` and bottom border
  - Add `.sidebar-collapsed` width and icon-only styles
  - Add `.cmd-palette-overlay` and `.cmd-palette-panel` glassmorphism styles
  - _Requirements: 4.2, 4.3, 6.1, 6.2, 6.3, 6.4, 6.5, 6.6, 11.1, 11.2, 11.3, 15.3_

- [~] 9. Implement page header enhancements (shared pattern)
  - Create a `PageHeader` component inline pattern (or small component) using `motion.h1` with `fadeInDown` (0.25s) for the title and `motion.div` with `fadeInUp` + 0.05s delay for subtitle/actions
  - Add `useEffect` scroll listener that adds `.page-header--scrolled` class when `scrollY > 40px`
  - Apply the `PageHeader` pattern to `Dashboard.js`, `Financial.js`, `Orders.js`, `Products.js`, `Customers.js`, `Suppliers.js`, `Returns.js`, `Checklist.js`
  - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [~] 9.1 Write property test — P14: Page header blur responds to scroll position
    - **Property 14: Page header blur responds to scroll position**
    - Use `fc.integer({ min: 0, max: 2000 })` for scroll position; verify blur class applied above 40px and absent at or below
    - Tag: `// Feature: ui-redesign-animations, Property 14`
    - **Validates: Requirements 9.4**

- [~] 10. Checkpoint — Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [~] 11. Update `Dashboard.js` — skeleton loading, stat card stagger, count-up values
  - Replace the loading spinner with `Skeleton` components matching each stat card's dimensions while `loading` is true
  - Wrap the stats grid in `motion.div` with `staggerContainer` variant; wrap each `stat-card` in `motion.div` with `staggerItem` (0.08s stagger)
  - Apply `useCountUp` to each numeric stat value; pass raw number to hook and format the result for display
  - Add `whileHover={{ y: -4 }}` and `whileTap={{ scale: 0.97 }}` to each stat card
  - Add hover gradient overlay reveal (accent at 8% opacity) via CSS `::before` already in place — ensure `motion` wrapper doesn't break it
  - Apply `PageHeader` pattern to the Dashboard page header
  - _Requirements: 4.1, 4.4, 4.6, 5.1, 5.2, 5.3, 5.4, 5.5, 9.1, 9.2_

  - [~] 11.1 Write property test — P7: Pages show skeletons while loading
    - **Property 7: Pages show skeletons while loading and content after**
    - Use `fc.constantFrom(...pageComponents)` including Dashboard; verify skeleton present when `loading=true` and absent when `loading=false`
    - Tag: `// Feature: ui-redesign-animations, Property 7`
    - **Validates: Requirements 4.1, 4.4**

- [~] 12. Update `Financial.js` — Recharts animation, count-up stats, skeleton loading
  - Replace loading spinner with `Skeleton` components for chart and stat card areas
  - Set `isAnimationActive={true}` and `animationDuration={800}` on all Recharts chart series components
  - Apply `useCountUp` to all numeric stat values on the Financial page
  - Apply `PageHeader` pattern to the Financial page header
  - _Requirements: 4.1, 4.4, 9.1, 9.2, 12.1, 12.2, 12.3_

- [~] 13. Update all remaining page components — skeleton loading and page header enhancements
  - For each of `Orders.js`, `Products.js`, `Customers.js`, `Suppliers.js`, `Returns.js`, `Checklist.js`:
    - Replace loading spinner with `Skeleton` components matching table row dimensions while `loading` is true
    - Wrap `tbody` rows in `motion.tr` with `staggerItem` variant (0.04s stagger) inside a `staggerContainer` `motion.tbody`
    - Add `AnimatePresence` around `tbody` to handle row add/remove animations
    - Row exit animation: `height: 0, opacity: 0` over 0.25s
    - New row entrance: `fadeInDown` over 0.2s
    - Apply `PageHeader` pattern to each page header
  - _Requirements: 4.1, 4.4, 9.1, 9.2, 10.1, 10.2, 10.3, 10.4_

  - [~] 13.1 Write property test — P15: Table row deletion triggers exit animation
    - **Property 15: Table row deletion triggers exit animation**
    - Use `fc.array(fc.record({ id: fc.string(), label: fc.string() }))` for table data; verify exit animation plays before DOM removal
    - Tag: `// Feature: ui-redesign-animations, Property 15`
    - **Validates: Requirements 10.2**

  - [~] 13.2 Write property test — P16: New table rows animate in on addition
    - **Property 16: New table rows animate in on addition**
    - Use `fc.record(...)` for new row data; verify `fadeInDown` entrance animation is applied on first render
    - Tag: `// Feature: ui-redesign-animations, Property 16`
    - **Validates: Requirements 10.3**

- [~] 14. Add toast enhancements — progress bar and slide-in animation
  - Extend the `ToasterWithTheme` component in `App.js` to customise toast enter/exit animations using react-hot-toast's `toastOptions`
  - Add a `ToastProgressBar` sub-component that renders a progress bar `div` inside each toast, animating its width from 100% to 0 over the toast duration
  - Apply `slideInRight` + `fadeIn` entrance (0.25s) and reversed exit (0.2s) via react-hot-toast custom render or CSS
  - _Requirements: 13.1, 13.2, 13.3_

  - [~] 14.1 Write property test — P18: Toast progress bar present for any timed toast
    - **Property 18: Toast progress bar is present for any timed toast**
    - Use `fc.integer({ min: 1000, max: 10000 })` for duration; verify progress bar element is present and its width decreases over time
    - Tag: `// Feature: ui-redesign-animations, Property 18`
    - **Validates: Requirements 13.3**

- [~] 15. Wire `AnimatePresence` page transitions in `App.js`
  - Import `AnimatePresence` from `framer-motion` and `useLocation` from `react-router-dom` in `App.js`
  - Wrap `<Routes>` in `<AnimatePresence mode="wait">` keyed on `location.pathname`
  - Ensure page transitions complete within 400ms; use `fadeInUp` variant with 0.3s duration
  - When `prefers-reduced-motion` is active, skip translate and use opacity only
  - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_

- [~] 16. Final checkpoint — Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- fast-check PBT tests run a minimum of 100 iterations each
- All animations use `transform` and `opacity` only (GPU-composited), except the intentional sidebar width transition
- `prefers-reduced-motion` is respected throughout — all durations collapse to 0.01s when active
- The `useCountUp` hook must receive raw numeric values; pages format the animated result for display
