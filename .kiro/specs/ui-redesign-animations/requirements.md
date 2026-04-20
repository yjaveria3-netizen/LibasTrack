# Requirements Document

## Introduction

LibasTrack is a business management web application for fashion/apparel brands, covering Dashboard, Orders, Products, Customers, Suppliers, Financial, Collections, Returns, Checklist, Drive Setup, and Brand Settings. The current UI uses a custom CSS design system with dark/light themes, a fixed sidebar, and Framer Motion (already installed). This feature is a complete UI redesign and animation overhaul to deliver a premium, highly-animated, visually advanced experience — while preserving all existing functionality and data flows.

The redesign targets: a richer visual hierarchy, fluid page transitions, micro-interactions on every interactive element, animated data visualisations, a collapsible/icon-only sidebar mode, a command palette, skeleton loading states, and a cohesive motion language across all pages.

---

## Glossary

- **UI**: The React frontend located in `frontend/src`.
- **Layout**: The `Layout.js` component that wraps all authenticated pages with the sidebar and main content area.
- **Sidebar**: The fixed left navigation panel containing brand identity, nav groups, theme toggle, and user pill.
- **Page**: Any top-level route component under `frontend/src/pages/`.
- **Motion_System**: The set of shared Framer Motion variants, transitions, and animation utilities used across the application.
- **Design_Token**: A CSS custom property defined in `index.css` that encodes colour, spacing, radius, shadow, or typography values.
- **Skeleton**: A placeholder shimmer element shown in place of content while data is loading.
- **Command_Palette**: A keyboard-triggered overlay (⌘K / Ctrl+K) for fast navigation and actions.
- **Micro_Interaction**: A small, purposeful animation on a single interactive element (button press, hover, focus, toggle).
- **Page_Transition**: An animated enter/exit sequence applied when navigating between routes.
- **Stat_Card**: A dashboard metric tile displaying a label, numeric value, and optional sub-text.
- **Toast**: A react-hot-toast notification.
- **Theme**: Either `dark` (default) or `light`, toggled via `ThemeContext`.
- **Glassmorphism**: A visual style using semi-transparent backgrounds with backdrop blur.
- **Stagger**: A Framer Motion technique where child elements animate in sequence with a delay offset.

---

## Requirements

### Requirement 1: Motion System Foundation

**User Story:** As a developer, I want a centralised motion system, so that all animations across the app are consistent, maintainable, and easy to apply.

#### Acceptance Criteria

1. THE Motion_System SHALL export a set of named Framer Motion variants covering at minimum: `fadeIn`, `fadeInUp`, `fadeInDown`, `scaleIn`, `slideInLeft`, `slideInRight`, `staggerContainer`, and `staggerItem`.
2. THE Motion_System SHALL export a shared `transition` object with `duration`, `ease`, and `type` values that can be overridden per-component.
3. WHEN a component imports Motion_System variants, THE component SHALL apply them without defining local animation values.
4. THE Motion_System SHALL respect the user's `prefers-reduced-motion` media query by substituting all motion with instant opacity transitions when reduced motion is preferred.
5. THE Motion_System SHALL be located in a single file at `frontend/src/utils/motion.js`.

---

### Requirement 2: Page Transitions

**User Story:** As a user, I want smooth animated transitions between pages, so that navigation feels fluid and premium rather than abrupt.

#### Acceptance Criteria

1. WHEN the user navigates to a new route, THE Layout SHALL animate the outgoing page out and the incoming page in using the `fadeInUp` variant with a duration of 0.3 seconds.
2. THE Page_Transition SHALL use Framer Motion's `AnimatePresence` component keyed on the current route pathname.
3. WHEN a page transition is in progress, THE Layout SHALL not show a flash of unstyled content or layout shift.
4. THE Page_Transition SHALL complete within 400 milliseconds to avoid perceived sluggishness.
5. WHERE the user has `prefers-reduced-motion` enabled, THE Layout SHALL skip the translate component of the transition and use opacity only.

---

### Requirement 3: Sidebar Redesign and Collapse

**User Story:** As a user, I want a collapsible sidebar with smooth animations, so that I can maximise screen space when needed while keeping navigation accessible.

#### Acceptance Criteria

1. THE Sidebar SHALL support two display modes: `expanded` (232 px wide, labels visible) and `collapsed` (60 px wide, icons only).
2. WHEN the user clicks the collapse toggle button, THE Sidebar SHALL animate between expanded and collapsed states using a width transition of 0.25 seconds with `cubic-bezier(0.4, 0, 0.2, 1)`.
3. WHEN the Sidebar is in collapsed mode, THE Sidebar SHALL display a tooltip with the nav item label on hover.
4. WHEN the Sidebar transitions to collapsed mode, THE main content area SHALL simultaneously animate its left margin to match the new sidebar width.
5. THE Sidebar SHALL persist the collapsed/expanded preference in `localStorage` under the key `sidebar_collapsed`.
6. WHEN a nav item is active, THE Sidebar SHALL display an animated left-border accent indicator using a `layoutId` Framer Motion shared element.
7. WHEN the user hovers over a nav item, THE nav item background SHALL animate in using a `layoutId`-based shared background highlight.
8. THE Sidebar brand icon SHALL display a subtle pulse glow animation on the accent colour with a 3-second loop.
9. WHEN the Sidebar is in collapsed mode on mobile (viewport width < 768 px), THE Sidebar SHALL behave as a full-width overlay drawer as it does currently.

---

### Requirement 4: Skeleton Loading States

**User Story:** As a user, I want skeleton placeholders while data loads, so that the interface feels responsive and I understand the layout before content appears.

#### Acceptance Criteria

1. WHEN a Page is fetching data from the API, THE Page SHALL display Skeleton components in place of Stat_Cards, tables, and list items.
2. THE Skeleton SHALL animate using a shimmer effect: a linear-gradient that sweeps from left to right over 1.5 seconds, looping infinitely.
3. THE Skeleton shimmer gradient SHALL use `var(--bg-layer2)` as the base colour and `var(--bg-layer3)` as the highlight colour so it respects the active Theme.
4. WHEN data has loaded, THE Page SHALL replace Skeleton components with real content using a `fadeIn` animation of 0.2 seconds.
5. THE Skeleton component SHALL accept `width`, `height`, and `borderRadius` props to match the shape of the content it replaces.
6. THE Dashboard Stat_Cards SHALL each display an individual Skeleton of matching dimensions while `loading` is true.

---

### Requirement 5: Stat Card Animations

**User Story:** As a user, I want dashboard stat cards to animate their values and respond to interaction, so that the data feels alive and engaging.

#### Acceptance Criteria

1. WHEN the Dashboard mounts and data has loaded, THE Stat_Cards SHALL animate in using a stagger sequence with 0.08 seconds between each card using the `staggerContainer` and `staggerItem` variants.
2. WHEN a Stat_Card value is a number, THE Stat_Card SHALL animate the numeric value counting up from 0 to the final value over 1 second using an easing curve.
3. WHEN the user hovers over a Stat_Card, THE Stat_Card SHALL elevate with a `translateY(-4px)` transform and an increased box-shadow, animated over 0.18 seconds.
4. WHEN the user hovers over a Stat_Card, THE Stat_Card SHALL reveal a subtle gradient overlay using the accent colour at 8% opacity.
5. WHEN the user clicks a Stat_Card, THE Stat_Card SHALL animate a brief scale-down to 0.97 before navigating.

---

### Requirement 6: Button and Interactive Element Micro-Interactions

**User Story:** As a user, I want every button and interactive element to respond with tactile animations, so that the interface feels polished and responsive.

#### Acceptance Criteria

1. WHEN the user hovers over a `.btn-primary` button, THE button SHALL animate `translateY(-2px)` and increase box-shadow over 0.14 seconds.
2. WHEN the user presses a `.btn-primary` button, THE button SHALL animate `scale(0.97)` over 0.08 seconds.
3. WHEN the user hovers over a `.btn-secondary` or `.btn-ghost` button, THE button SHALL animate a background colour transition over 0.14 seconds.
4. WHEN the user focuses a `.form-input`, `.form-select`, or `.form-textarea`, THE input SHALL animate a glow ring using `box-shadow: 0 0 0 3px var(--accent-glow)` over 0.15 seconds.
5. WHEN the user hovers over a table row in `tbody`, THE row SHALL animate a background highlight over 0.12 seconds.
6. WHEN a `.chip` filter is activated, THE chip SHALL animate from its inactive style to its active style using a background and border colour transition over 0.15 seconds.
7. WHEN a modal opens, THE `.modal` SHALL animate in using `scaleIn` (scale from 0.95 to 1) combined with `fadeIn` over 0.18 seconds with a spring easing.
8. WHEN a modal closes, THE `.modal` SHALL animate out using scale to 0.95 and opacity to 0 over 0.15 seconds.

---

### Requirement 7: Command Palette

**User Story:** As a user, I want a keyboard-triggered command palette, so that I can navigate to any page or trigger common actions without using the mouse.

#### Acceptance Criteria

1. WHEN the user presses `Ctrl+K` (Windows/Linux) or `Cmd+K` (macOS), THE Command_Palette SHALL open as a full-screen overlay.
2. WHEN the Command_Palette opens, THE Command_Palette SHALL animate in using a `scaleIn` + `fadeIn` combination over 0.2 seconds.
3. THE Command_Palette SHALL display a search input that is focused automatically on open.
4. WHEN the user types in the Command_Palette search input, THE Command_Palette SHALL filter the list of navigation items and quick actions in real time, showing results within 50 milliseconds.
5. THE Command_Palette SHALL include all sidebar navigation destinations and the six quick actions from the Dashboard.
6. WHEN the user presses `Enter` on a highlighted result, THE Command_Palette SHALL close and navigate to the selected destination.
7. WHEN the user presses `Escape`, THE Command_Palette SHALL close with a reverse `scaleIn` + `fadeOut` animation over 0.15 seconds.
8. WHEN the user clicks outside the Command_Palette panel, THE Command_Palette SHALL close.
9. THE Command_Palette SHALL be accessible via keyboard arrow keys to move between results.
10. THE Command_Palette overlay background SHALL use a `backdrop-filter: blur(12px)` glassmorphism effect.

---

### Requirement 8: Advanced Sidebar Visual Design

**User Story:** As a user, I want the sidebar to have a premium visual design with depth and subtle effects, so that the navigation area feels high-quality and distinct.

#### Acceptance Criteria

1. THE Sidebar SHALL display a vertical gradient accent stripe on its right edge using `var(--accent)` fading to transparent at top and bottom.
2. THE Sidebar background SHALL use a subtle noise texture or gradient overlay to add depth, without impacting readability.
3. WHEN the user scrolls the sidebar nav, THE Sidebar SHALL display a fade-out gradient at the top and bottom of the scrollable area to indicate overflow.
4. THE active nav item indicator SHALL use a Framer Motion `layoutId` shared element so it smoothly slides between items when the active route changes.
5. THE Sidebar footer user pill SHALL display the user avatar with a subtle ring animation using `var(--accent)` when the user is online.

---

### Requirement 9: Page Header Enhancements

**User Story:** As a user, I want page headers to feel dynamic and contextual, so that each section of the app has a distinct identity.

#### Acceptance Criteria

1. WHEN a Page mounts, THE page header title SHALL animate in using `fadeInDown` with a 0.25-second duration.
2. WHEN a Page mounts, THE page subtitle and action buttons SHALL animate in with a stagger delay of 0.05 seconds after the title.
3. THE page header SHALL display a subtle gradient background that transitions from `var(--bg-base)` to transparent at the bottom edge.
4. WHEN the user scrolls down past 40 px, THE page header SHALL increase its backdrop blur from 0 to 12px and add a bottom border, animated over 0.2 seconds.

---

### Requirement 10: Table and List Animations

**User Story:** As a user, I want table rows and list items to animate in when data loads, so that content appearance feels intentional rather than sudden.

#### Acceptance Criteria

1. WHEN a table's data loads, THE table rows SHALL animate in using a stagger sequence with 0.04 seconds between each row, using `fadeInUp` with a 12 px vertical offset.
2. WHEN a row is deleted, THE row SHALL animate out using a height collapse and opacity fade over 0.25 seconds before being removed from the DOM.
3. WHEN a new row is added (e.g. after creating a record), THE new row SHALL animate in at the top of the table using `fadeInDown` over 0.2 seconds.
4. WHEN the user applies a filter or search that changes the visible rows, THE table SHALL animate the transition using a cross-fade over 0.15 seconds.

---

### Requirement 11: Theme Toggle Animation

**User Story:** As a user, I want the dark/light theme toggle to animate smoothly, so that switching themes feels intentional and polished.

#### Acceptance Criteria

1. WHEN the user activates the theme toggle, THE toggle thumb SHALL animate its horizontal position using a spring transition with `stiffness: 400` and `damping: 30`.
2. WHEN the theme changes, THE background colours, text colours, and border colours across the entire UI SHALL transition over 0.3 seconds using CSS `transition` on all Design_Token-consuming properties.
3. WHEN the theme changes, THE toggle icon (☾ / ☀) SHALL animate a rotation of 180 degrees and opacity fade over 0.25 seconds.

---

### Requirement 12: Financial and Chart Animations

**User Story:** As a user, I want charts and financial data visualisations to animate on load, so that data presentation feels engaging and draws attention to key metrics.

#### Acceptance Criteria

1. WHEN the Financial page mounts and data has loaded, THE Recharts chart components SHALL animate their data series in from the baseline over 0.8 seconds.
2. WHEN the user hovers over a chart data point, THE tooltip SHALL animate in using `fadeIn` over 0.1 seconds.
3. WHEN the Financial page stat values load, THE values SHALL use the same count-up animation as Stat_Cards (Requirement 5.2).

---

### Requirement 13: Toast Notification Enhancements

**User Story:** As a user, I want toast notifications to animate with more personality, so that feedback feels immediate and satisfying.

#### Acceptance Criteria

1. WHEN a success Toast appears, THE Toast SHALL animate in from the bottom-right using a `slideInRight` + `fadeIn` combination over 0.25 seconds.
2. WHEN a Toast is dismissed, THE Toast SHALL animate out using `slideInRight` reversed and `fadeOut` over 0.2 seconds.
3. THE Toast SHALL display a subtle progress bar at the bottom indicating the remaining display duration.

---

### Requirement 14: Mobile Responsiveness Preservation

**User Story:** As a developer, I want all redesigned components to remain fully responsive, so that the app works correctly on mobile devices after the redesign.

#### Acceptance Criteria

1. THE Layout SHALL maintain the existing mobile breakpoint behaviour at viewport width < 768 px, with the sidebar as a full-width overlay drawer.
2. WHEN the mobile menu button is tapped, THE Sidebar overlay SHALL animate in using `slideInLeft` over 0.28 seconds.
3. WHEN the mobile overlay backdrop is tapped, THE Sidebar SHALL animate out using `slideInLeft` reversed over 0.22 seconds.
4. THE stats grid, quick actions grid, and all other grid layouts SHALL remain responsive using `auto-fit` / `minmax` CSS grid at all viewport widths.
5. IF a viewport width is less than 480 px, THEN THE Command_Palette SHALL display as a bottom sheet rather than a centred modal.

---

### Requirement 15: Accessibility and Performance

**User Story:** As a developer, I want all animations to be accessible and performant, so that the redesign does not harm users with motion sensitivity or degrade app performance.

#### Acceptance Criteria

1. THE Motion_System SHALL check `window.matchMedia('(prefers-reduced-motion: reduce)')` and set all animation durations to 0.01 seconds when reduced motion is preferred.
2. ALL Framer Motion animations SHALL use `transform` and `opacity` properties only, avoiding layout-triggering properties such as `width`, `height`, `top`, `left`, or `margin` in animated values (except for the sidebar collapse which uses width intentionally).
3. THE Skeleton shimmer animation SHALL use `transform: translateX()` rather than `background-position` to leverage GPU compositing.
4. WHEN the Command_Palette is closed, THE Command_Palette DOM node SHALL be unmounted to avoid background rendering cost.
5. ALL interactive elements with animations SHALL maintain a minimum touch target size of 44 × 44 px on mobile viewports.
6. THE count-up animation (Requirement 5.2) SHALL use `requestAnimationFrame` via a custom hook rather than `setInterval` to avoid dropped frames.
