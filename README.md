# PM Sprint Board

A lightweight product board for **SmartBot** and **ZenChat** (Zenoti). One HTML file, live-synced via Firebase Realtime Database, deployed on Netlify.

**Live:** [https://pm-board-avinash.netlify.app](https://pm-board-avinash.netlify.app)

## Features

### Workstreams
- Collapsible **SmartBot** and **ZenChat** sidebar groups
- Each workstream has its own sprint boards, roadmap, tasks, and settings focus
- Global **+ New** creates Sprint / Backlog / Parking boards with a project picker

### Boards
- **Project-scoped** boards (one column per workstream — not dual SmartBot|ZenChat)
- Board kinds: **Sprint**, **Backlog**, **Parking**
- **Sections** (Customer Requests, Internal, or custom) — create, rename, delete
- **Cards** with type, status, customer/reporter, developer, notes, and Jira ticket ID/link
- **Drag and drop** — reorder, move between sections, nest as subtasks
- **Select to Move** — bulk-move tasks to another board
- Status and developer filters on the board toolbar

### Roadmap
- Vertical month columns: **September (Q3)**, **October / November / December (Q4)**
- Add, edit, delete, and drag items per month
- Categories: Feature / Platform / Reporting, optional **LIVE** badge
- Separate roadmap data per workstream

### Other
- **Tasks / My Tasks** — shared personal task list (priority, due date, notes)
- **Settings** — global statuses; **per-project developer lists** (SmartBot vs ZenChat)
- **Access gate** — Avinash + PIN for edit; Other for view-only; Logout in the top bar
- **Notes hover** — preview notes on cards without opening edit
- **Live sync** — board state persists to Firebase; changes appear for everyone in real time

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

# Option B — Node (recommended if Python is unavailable)
npx --yes serve -l 5500 .

# Option C — Python
python -m http.server 5500
```

Then open `http://localhost:5500` (or `http://127.0.0.1:5500`).

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
| `boards` | Board list meta (`id`, `kind`, `project`, name, dates, goal) + active board |
| `board_<boardId>` | Board layout for one project key (`smartbot` or `zenchat` sections/cards) |
| `roadmap_smartbot` / `roadmap_zenchat` | Month-column roadmap items per workstream |
| `settings_statuses` | Custom status options |
| `settings_developers` | Per-project developer names `{ smartbot: [...], zenchat: [...] }` |
| `mytasks` | Personal My Tasks list |
| `notes_<cardId>` | Per-card notes fallback (also embedded in board state) |
| `ticket_<cardId>` | Ticket URL fallback (also embedded in board state) |

Legacy combined boards (both columns on one sprint) are split into project boards on load.

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
