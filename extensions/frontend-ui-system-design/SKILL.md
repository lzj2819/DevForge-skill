---
name: frontend-ui-system-design
description: Domain extension for frontend UI system architecture. Use when the project involves React, Vue, Angular, or similar SPA/PWA frameworks with significant UI complexity. Loaded by devforge-architecture-design when PRD contains frontend tags.
---

# Frontend UI System Design Extension

## Overview

This extension augments the generic `devforge-architecture-design` skill with frontend-specific evaluation dimensions, component architecture, and performance targets. It ensures frontend projects receive proper design system, component library, and state management guidance.

## When to Load

- PRD mentions: React, Vue, Angular, SPA, PWA, design system, component library
- Project characteristic tags include: `ui-heavy`, `design-system`, `spa`, `pwa`, `micro-frontend`

## Overlay Rules

### Additional Dimensions

| Dimension | Weight | Description |
|-----------|--------|-------------|
| Component Reusability | 1.2x | How reusable are components across the application? |
| Bundle Size Impact | 1.1x | Impact on initial page load and time-to-interactive |
| State Complexity | 1.1x | Complexity of global state management requirements |
| Accessibility | 1.0x | WCAG compliance and inclusive design |
| Animation Performance | 1.0x | Frame rate and jank-free animation requirements |

### Mandatory Modules (Frontend Projects)

- `DesignSystem`: Color tokens, typography, spacing, iconography
- `ComponentLibrary`: Base components, generic components, business components (tiered)
- `StateStore`: Redux, Zustand, Pinia, Vuex, or Context API architecture
- `RoutingLayer`: Route guards, lazy loading, code splitting
- `APIService`: OpenAPI client generation, request interceptors, caching

### FrontendProfile XML Additions

When `architecture.xml` contains a Module with `type="frontend"`, the `FrontendProfile` node is populated:

```xml
<FrontendProfile>
  <Framework type="react" rendering="csr"/>
  <StateManagement type="zustand"/>
  <ComponentLibrary tiered="true"/>
  <PerformanceTargets fcp="1.8s" lcp="2.5s" cls="0.1" fid="100ms"/>
  <I18n strategy="dynamic-load" locales="en,zh,ja"/>
</FrontendProfile>
```

### Generated Artifacts

- `frontend/design-system/tokens.json` — Design tokens (color, font, spacing)
- `frontend/components/{tier}/{Component}.tsx` — Component skeletons (base/generic/business)
- `frontend/stores/{store}.ts` — State management skeleton
- `frontend/e2e/` — Playwright test templates
- `frontend/performance-budget.json` — Performance budget with LCP/FID/CLS targets

## References

- `references/component-hierarchy.md` — Component tiering strategy
- `references/performance-metrics.md` — Core Web Vitals and frontend metrics
- `references/storybook-integration.md` — Component documentation patterns
