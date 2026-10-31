# Waypoint — weekly planner

Source for the published Claude Artifact: `waypoint.html`.

Live app: https://claude.ai/artifact/6A6gGG7TcVkxwX5caAL6AJ

A single-file HTML app, published as a Claude Artifact, so it runs as a normal
responsive web page on desktop and mobile with no separate hosting.

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
- **Ask Claude** — the 💬 button lets you change the planner in plain English
  ("move dentist to Friday", "add renew passport to admin on 5 Dec",
  "plan switching gas provider: switch name, meter change, new tariff, then
  move provider" — which creates a group with those steps in order).

## How it's built

- Data lives in the artifact's `db` capability:
  - `tasks` — one document per task: `title`, `tab` (category id),
    `priority` (`med`|`high`), `due` (`YYYY-MM-DD`, empty = Backlog), `notes`,
    `group` (group doc id, empty = none), `step` (sort order within the group),
    `done`, `createdAt`, `completedAt`.
  - `groups` — one document per group: `name`, `createdAt`.
  Every open copy of the page stays in sync live.
- The chat panel uses the `sample` capability with page-defined tools
  (`add_task`, `complete_task`, `reopen_task`, `update_task`, `delete_task`)
  that write to the same `tasks` collection as the manual UI.
- No backend, no build step — publishing the HTML file *is* the deploy.

## Editing

Update `waypoint.html` and republish it as an Artifact to the same URL
(`Artifact` tool, passing `url` to update in place) to ship changes.
