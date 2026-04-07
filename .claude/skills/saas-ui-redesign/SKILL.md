---
name: saas-ui-redesign
description: Use this skill when the user asks to "redesign the UI", "add a sidebar", "switch to sidebar navigation", "convert to SaaS layout", or "modernize the interface". Redesigns a Vue 3 app from a horizontal top-nav layout to a vertical sidebar SaaS-style interface.
version: 0.1.0
---

# SaaS UI Redesign

Redesign the inventory management app's layout from a horizontal top-nav bar to a modern SaaS-style vertical sidebar navigation. All `.vue` file modifications MUST be delegated to the `vue-expert` agent per CLAUDE.md.

---

## Target Layout

```
┌──────────┬──────────────────────────────────┐
│ Sidebar  │  FilterBar  (sticky, top: 0)     │
│  240px   ├──────────────────────────────────┤
│          │                                  │
│ [Logo]   │  <router-view />                 │
│          │  padding: 1.5rem 2rem            │
│ [Nav     │                                  │
│  Links]  │                                  │
│          │                                  │
│ [User /  │                                  │
│  Lang]   │                                  │
└──────────┴──────────────────────────────────┘
```

---

## Files to Modify

| File | What Changes |
|------|-------------|
| `client/src/App.vue` | Full layout restructure — add sidebar, remove top-nav |
| `client/src/components/FilterBar.vue` | Update sticky offset from `top: 70px` → `top: 0` |

---

## Step 1 — Delegate to vue-expert

Instruct the `vue-expert` agent to make both file changes in a single task. Provide the full spec below.

---

## App.vue Restructure Spec

### Root layout change
```css
/* Before */
.app { display: flex; flex-direction: column; min-height: 100vh; }

/* After */
.app { display: flex; flex-direction: row; height: 100vh; overflow: hidden; }
```

### Remove the entire `.top-nav` / `<header>` block
Delete the `<header class="top-nav">` element and all its CSS. The sidebar replaces it.

### Add sidebar markup (inside `.app`, before `<FilterBar>` and `<main>`)
```html
<aside class="sidebar">
  <div class="sidebar-logo">
    <h1>FactoryOS</h1>
    <span class="sidebar-subtitle">Inventory</span>
  </div>

  <nav class="sidebar-nav">
    <router-link to="/" class="sidebar-link" active-class="active" exact>
      <svg><!-- grid/dashboard icon --></svg>
      <span>Overview</span>
    </router-link>
    <router-link to="/inventory" class="sidebar-link" active-class="active">
      <svg><!-- box/cube icon --></svg>
      <span>Inventory</span>
    </router-link>
    <router-link to="/orders" class="sidebar-link" active-class="active">
      <svg><!-- clipboard/list icon --></svg>
      <span>Orders</span>
    </router-link>
    <router-link to="/spending" class="sidebar-link" active-class="active">
      <svg><!-- chart/dollar icon --></svg>
      <span>Finance</span>
    </router-link>
    <router-link to="/demand" class="sidebar-link" active-class="active">
      <svg><!-- trending-up icon --></svg>
      <span>Demand Forecast</span>
    </router-link>
    <router-link to="/reports" class="sidebar-link" active-class="active">
      <svg><!-- bar-chart icon --></svg>
      <span>Reports</span>
    </router-link>
  </nav>

  <div class="sidebar-footer">
    <LanguageSwitcher />
    <ProfileMenu />
  </div>
</aside>
```

Use simple inline SVG paths for icons — no icon library needed. Source clean 20×20 icons from heroicons paths (stroke-based, `stroke-width="1.5"`).

### Add sidebar CSS
```css
.sidebar {
  width: 240px;
  min-width: 240px;
  height: 100vh;
  background: #0f172a;
  display: flex;
  flex-direction: column;
  position: sticky;
  top: 0;
  z-index: 100;
  overflow-y: auto;
}

.sidebar-logo {
  padding: 1.5rem 1.25rem 1rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
}

.sidebar-logo h1 {
  font-size: 1.1rem;
  font-weight: 700;
  color: #f1f5f9;
  letter-spacing: -0.02em;
}

.sidebar-subtitle {
  font-size: 0.7rem;
  color: #475569;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  font-weight: 600;
}

.sidebar-nav {
  flex: 1;
  padding: 0.75rem 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.sidebar-link {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  border-radius: 6px;
  color: #94a3b8;
  font-size: 0.875rem;
  font-weight: 500;
  text-decoration: none;
  transition: all 0.15s ease;
  border-left: 3px solid transparent;
}

.sidebar-link svg {
  width: 18px;
  height: 18px;
  flex-shrink: 0;
  stroke: currentColor;
  fill: none;
}

.sidebar-link:hover {
  background: rgba(255, 255, 255, 0.06);
  color: #f1f5f9;
}

.sidebar-link.active {
  background: rgba(59, 130, 246, 0.12);
  color: #60a5fa;
  border-left-color: #3b82f6;
}

.sidebar-footer {
  padding: 1rem 0.75rem;
  border-top: 1px solid rgba(255, 255, 255, 0.06);
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
```

### Update `.main-content`
```css
.main-content {
  flex: 1;
  height: 100vh;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  min-width: 0; /* prevent flex overflow */
}
```

Remove `max-width`, `margin: 0 auto`, and top-level `padding` — those belong on the inner content wrapper that `<router-view>` renders, not here. The `<router-view>` itself sits inside `.main-content` after the FilterBar.

### Template structure after changes
```html
<div class="app">
  <aside class="sidebar"> ... </aside>

  <div class="main-content">
    <FilterBar />
    <main class="page-content">
      <router-view />
    </main>
  </div>

  <!-- modals stay here -->
</div>
```

Add `.page-content` wrapper:
```css
.page-content {
  flex: 1;
  padding: 1.5rem 2rem;
  max-width: 1600px;
  width: 100%;
}
```

---

## FilterBar.vue Change

One CSS change only — update the sticky top offset:

```css
/* Before */
.filters-bar {
  position: sticky;
  top: 70px;   /* offset for old 70px top-nav */
  z-index: 90;
}

/* After */
.filters-bar {
  position: sticky;
  top: 0;       /* sidebar is on the left now, no top offset needed */
  z-index: 90;
}
```

---

## Verification

After vue-expert completes both file changes:

1. Use `mcp__playwright__navigate` to open `http://localhost:3000`
2. Take a screenshot with `mcp__playwright__screenshot` and confirm:
   - Sidebar is visible on the left (dark background, 240px)
   - No top nav bar remains
   - Nav links are vertical and route correctly
   - FilterBar is sticky at the top of the content column
   - ProfileMenu and LanguageSwitcher appear in the sidebar footer
3. Click each nav link and verify the active state highlights correctly

---

## Design Tokens Reference

| Token | Value |
|-------|-------|
| Sidebar bg | `#0f172a` |
| Sidebar text | `#94a3b8` |
| Sidebar active text | `#60a5fa` |
| Sidebar active bg | `rgba(59,130,246,0.12)` |
| Sidebar active border | `#3b82f6` |
| Sidebar hover bg | `rgba(255,255,255,0.06)` |
| Sidebar width | `240px` |
| Content bg | `#f8fafc` (unchanged) |
| Content padding | `1.5rem 2rem` |
