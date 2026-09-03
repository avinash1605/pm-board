# PM Sprint Board

A lightweight sprint board for **SmartBot** and **ZenChat** (Zenoti). One HTML file, live-synced across users via Firebase Realtime Database, deployed on Netlify.

**Live:** [https://pm-board-avinash.netlify.app](https://pm-board-avinash.netlify.app)

## Features

- **Sprint board** with SmartBot and ZenChat columns
- **Sections** (Customer Requests, Internal, or custom) — create, rename, delete
- **Cards** with type, status, customer/reporter, notes, and ticket URL
- **Drag and drop** — reorder, move between sections, nest as subtasks
- **Select to Move** — bulk-move tasks to another sprint
- **My Tasks** — personal tasks with priority, due date, and notes (not sprint-linked)
- **Settings** — custom status options and default sprint duration
- **Live sync** — all board state persists to Firebase; changes appear for everyone in real time

## Tech stack

| Piece | Detail |
| --- | --- |
| App | Single `index.html` (HTML / CSS / JS) |
| Drag & drop | [SortableJS](https://sortablejs.github.io/Sortable/) via cdnjs |
| Sync | Firebase Realtime Database (REST + Server-Sent Events) |
| Hosting | Netlify (auto-deploys from this repo) |

No build step, no npm dependencies.

## Local preview

Serve over HTTP (not `file://`) so Firebase sync works:

```bash
# Option A — VS Code / Cursor Live Server
# Right-click index.html → Open with Live Server
# → http://127.0.0.1:5500

# Option B — Python
python -m http.server 5500

# Option C — Node
npx --yes serve .
```

Then open `http://127.0.0.1:5500`.

## Deploy

Push to `main` on GitHub. Netlify rebuilds automatically (~30 seconds).

```bash
git add .
git commit -m "your message"
git push origin main
```

## Firebase

Board state is stored at:

`https://pm-board-e3d30-default-rtdb.firebaseio.com`

Keys used by the app (colons become underscores in Firebase paths):

| Key | Purpose |
| --- | --- |
| `board_<sprintId>` | Full board layout, cards, nesting, statuses, ticket URLs |
| `mytasks` | Personal My Tasks list |
| `notes_<cardId>` | Per-card notes (also embedded in board state) |
| `ticket_<cardId>` | Ticket URL fallback (also embedded in board state) |

## Project structure

```
pm-board/
├── index.html   # Entire application
└── README.md
```

## Editing guidelines

- Keep everything in `index.html` — do not split into modules
- Persist board changes with `saveBoardState()` via the `store` wrapper
- Prefer inline two-step confirms over `confirm()` (blocked in some sandboxes)
- Keep the UI monochrome with a single accent (`#3b5bdb`)
