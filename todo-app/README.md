# Waypoint — weekly planner

**Open it:** https://manjo-banjo.github.io/Claude-admin-tools/todo-app/

A single-file web app (`index.html`), hosted on GitHub Pages. No build step, no
server: GitHub serves the file, and your tasks are saved to a JSON file in a
separate **private** repo through GitHub's API. Works on desktop and phone
(add it to your home screen for an app-like icon).

## First-time setup (once per device)

1. Create a **private** repo for the data, e.g. `waypoint-data` (empty is fine).
2. Create a fine-grained token at
   https://github.com/settings/personal-access-tokens/new —
   *Repository access*: Only select repositories → the data repo;
   *Permissions*: Contents → **Read and write**. Pick an expiry you're happy
   with; when it expires the app shows "GitHub access lost" and you paste a new one.
3. Open the app, enter `owner/waypoint-data` and the token, press **Connect**.
   The token is kept in that browser only. The app creates `waypoint.json` if
   it doesn't exist.

"Use this device only" skips GitHub and keeps tasks in that browser instead
(no sync between devices). Click the status pill next to the title to connect
or disconnect later.

## What it does

- **Week view (front page)** — a planner grid: one row per category (Work,
  Social, Health, Admin, General), one column for the undated Backlog, then
  Mon–Sun. Today's column is highlighted. On phones the grid becomes a
  scrolling agenda (Backlog first, then each day).
- **List view** — every task grouped by category, sorted by date.
- **Groups view** — free-form, long-running projects (e.g. "Switch gas
  provider") made of ordered steps. Each card shows a progress bar, done/total
  and %, steps left, when it started and last activity; the first unfinished
  step is marked **Next**. Reorder with ↑↓, add steps at the bottom of a card,
  rename or delete a group (its steps stay as tasks). Groups where every step
  is done move to a collapsed "Finished" section.
- **Quick add** — type a task and press Enter; it lands in General · Backlog ·
  Medium. Optional shortcuts at the end of the text:
  - category: `#work`, `#social`, `#health`, `#admin`, `#general` (prefixes work, e.g. `#soc`)
  - group: `@switch-gas` (hyphens for spaces). Matches an existing group by
    name or word prefix (`@gas` finds "Switch gas provider"), otherwise creates a
    new group. Typing `@` suggests existing groups. A step added this way takes
    the group's usual category unless you also give `#category`.
  - important: `!`
  - dates: `today`, `tomorrow`, `fri`, `next fri`, `next week`, `next month`,
    `in 3 days|weeks|months`, `5 dec`, `dec 5th`, `5/12` (day/month),
    `31/01/2027`, `the 15th`, `2026-12-05`
  A preview under the box shows where the task will land before you add it.
- **Calendar picker** — a month calendar (with dots on days that already have
  tasks) from the add box, from "Other date…" in the task editor, and from the
  week label to jump to any week.
- **Moving tasks** — drag between cells on desktop, or tap a task to change
  category, day, importance and notes.
- **Progress** — week progress bar, per-day done/total, done today, day streak,
  backlog size.
- **Updating with Claude** — ask Claude (in a session with the data repo
  attached) to add, move or complete tasks; it edits `waypoint.json` and the
  app picks the change up within ~30 seconds. (The in-app 💬 chat button only
  appears when the page runs as a Claude artifact.)

## How it's built

- **Storage.** Everything is one JSON file, `waypoint.json`, at the root of the
  private data repo:

  ```jsonc
  {
    "format": "waypoint/1",
    "updatedAt": "2026-11-02T09:00:00.000Z",
    "tasks": {
      "<id>": {
        "title": "Book meter change",
        "tab": "admin",              // work | social | health | admin | general
        "priority": "med",           // med | high (shown as "!")
        "due": "2026-11-06",         // YYYY-MM-DD, or "" for the Backlog
        "notes": "",
        "group": "<group id>",       // "" for none
        "step": 1793455422000,       // order within the group (lower = earlier), or null
        "done": false,
        "createdAt": "2026-11-02T09:00:00.000Z",
        "completedAt": null          // ISO time when ticked off
      }
    },
    "groups": {
      "<id>": { "name": "Switch gas provider", "createdAt": "2026-11-02T09:00:00.000Z" }
    }
  }
  ```

  Ids are any unique string. Anything (you, Claude, a script) can edit this file
  directly; keep it valid JSON.
- **Sync.** Edits show instantly and are committed to the data repo about a
  second later (several quick edits become one commit). The app re-reads the
  file every 30 seconds and when you return to the tab, so changes made on
  another device or by Claude appear on their own. If the file changed since
  the app last read it, the app fetches the latest version and re-applies its
  own edits on top, so nothing is lost.
- **Status pill** next to the title: Synced / Saving… / Offline — retrying /
  GitHub access lost — reconnect / This device only.
- **Inside a Claude artifact** the same file instead uses the artifact's
  built-in database and shows the 💬 chat button; that copy keeps its own,
  separate data.

## Editing the app

Edit `index.html` and push. GitHub Pages republishes within a minute or two.
