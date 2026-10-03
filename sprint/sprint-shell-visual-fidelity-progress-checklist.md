# Sprint shell visual fidelity — progress checklist

**Sprint id:** `sprint-shell-visual-fidelity` (**closed** 2026-10-03)  
**Archives:** `client/archive/sprint-shell-visual-fidelity.yaml`, `server/archive/sprint-shell-visual-fidelity.yaml`  
**Goal:** Match dashboard sidebar, header, nav badges, and theme shortcut to canonical mockups.  
**Scope:** 240px sidebar, workspace row, collapse/expand, ⌘K search affordance, help + notifications, avatar, overview queue → nav counts, overview heading typography.  
**Non-goals:** Functional ⌘K command palette, workspace switcher, pipeline nav route, settings rail (212px), per-screen content fidelity beyond overview heading.

| Area | Done | Total | % |
|------|------|-------|---|
| Data / DB | 1 | 1 | 100% |
| Server | 1 | 1 | 100% |
| Client | 4 | 4 | 100% |
| Docs | 1 | 1 | 100% |
| **Overall** | **7** | **7** | **100%** |

## Data / DB

- [x] No migration — shell reuses existing overview metrics API

## Server

- [x] Document no DB change (`task-shell-no-db`)

## Client

- [x] Sidebar + header match mockup dimensions and chrome
- [x] Nav attention badges from overview queues when API data exists
- [x] Client unit tests — nav attention mapping
- [x] Playwright — overview shell smoke (⌘K, page title, login flow)

## Docs

- [x] design-system-shell fidelity notes + sprint YAML aligned
