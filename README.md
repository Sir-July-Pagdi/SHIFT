# Shift — Ideas in Motion

Offline-first presentation editor that combines a spatial canvas (Prezi-style
camera paths) with conventional slides, on one shared document model.

```bash
pnpm install
pnpm dev            # http://localhost:5173
pnpm build && pnpm preview   # production build + service worker, http://127.0.0.1:4173
pnpm typecheck
pnpm test           # unit tests (document-model, engine, storage)
pnpm e2e            # Playwright, builds and serves the app itself
```

Playwright needs a Chromium. Either `pnpm exec playwright install chromium`, or
set `CHROMIUM_PATH=/path/to/chrome`.

## Layout

```
packages/document-model   pure data: types, factory, validator, prototype migration, templates
packages/engine           pure logic: camera, history, scene ops, paths, slides, geometry
apps/web                  React UI, Zustand store, Dexie storage, PWA
tests/e2e                 Playwright
docs/architecture-review.md   what held up, what changed, what is not verified
```

Rule: `document-model` and `engine` never import React, the DOM or Dexie. UI
components never edit document data directly; they call store actions, which
call engine functions, which return new immutable documents.

## Status

| Area | State |
|---|---|
| Infinite canvas, pan/zoom (wheel, buttons, pinch, Space-drag) | Done |
| Text, card, shapes, circle, image | Done (plain text only) |
| Select, multi-select (Shift), move, resize, rotate, delete, duplicate, z-order, nudge | Done |
| Frames with real containment; nested frames | Done |
| Presentation paths (multiple), reorder, per-step duration/easing | Done |
| Camera-tween presenting, keyboard/click navigation, fullscreen request, reduced motion | Done (fullscreen unverified in headless) |
| Slide mode: slides, thumbnails, reorder, duplicate, delete, size, speaker notes | Done |
| Frame ↔ slide conversion | Done (documented limits) |
| Undo/redo (one step per gesture) | Done |
| Autosave, snapshots, recovery prompt, storage status | Done |
| Export/import backup (`.shift.json`), import Shift 0.1/0.2 prototype files | Done |
| PWA, offline reload | Done (verified with network off) |
| Templates (6, real objects, paths/slides) | Done |
| Responsive: desktop, tablet, phone drawers | Done (touch verified synthetically only) |
| Grouping, rich text, lines | Not done |
| Object animations, slide transitions, layouts/themes/placeholders | Not done |
| PDF export | Not done |
| Cloud sync, collaboration, AI, MP4/PPTX | Out of scope |

## Shortcuts

`Ctrl/Cmd+Z` undo · `Ctrl+Y` / `Ctrl+Shift+Z` redo · `Ctrl+D` duplicate ·
`Ctrl+A` select all · `Delete` remove · arrows nudge (Shift = 10) ·
`Space`+drag pan · double-click text to edit (`Ctrl+Enter` or click away to
finish, `Esc` cancels) · presenting: `←/→`, `Space`, `Home/End`, `N` notes, `Esc` exit.

## Data and privacy

Everything is stored in IndexedDB on this device. Nothing is sent anywhere.
Browsers can evict site data; use **☰ → Export backup** regularly. Open the app
with `?debug` to expose the store as `window.__shift` for testing.

## Migrating from the 0.2 prototype

Use **☰ → Import file…** with a `.shift.json` exported from 0.1/0.2 (or the JSON
from its localStorage key `shift-prototype`). Frame objects become the single
source of truth; the path order comes from the old `frames[]`; images become
assets. Known limitation: 0.2 stamped slide ids onto every object the first
time Slide mode was opened, so such files import with their content on slide 1.
