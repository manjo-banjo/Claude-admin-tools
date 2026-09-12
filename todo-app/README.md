# Waypoint — to-do list

Source for the published Claude Artifact: `waypoint.html`.

Live app: https://claude.ai/code/artifact/29c2e2c2-2389-4e0f-bd63-4396c50866b7

A single-file HTML app, published as a Claude Artifact, so it runs as a normal
responsive web page on desktop and mobile with no separate hosting.

## What it does

- **Tabs** — Work / Personal / Health / Admin, plus an "All" view. Each tab
  shows a live `done/total` count.
- **Progress** — an overall progress bar, plus "done today", "done this
  week" and a day streak, so effort is visible rather than buried in a long
  list.
- **Recently completed feed** — a short log of what was just finished.
- **Manual editing** — add, check off, edit (category/priority/due
  date/notes) and delete tasks directly in the UI.
- **Chat button** — a built-in "Ask Claude" panel (bottom-right) lets you
  update the list in plain English (e.g. "mark dentist done", "add call mom
  to personal, due Friday"). It calls Claude directly from the page (the
  viewer's own Claude usage) with tools to add/complete/reopen/edit/delete
  tasks — no separate chat session needed.

## How it's built

- Data lives in the artifact's `db` capability (a shared, realtime document
  store per artifact) — the `tasks` collection, one document per task
  (`title`, `tab`, `priority`, `due`, `notes`, `done`, `createdAt`,
  `completedAt`). Every open tab of the page stays in sync live.
- The chat panel uses the `sample` capability to ask Claude, passing the
  current task list as context and a small set of page-defined tools
  (`add_task`, `complete_task`, `reopen_task`, `update_task`, `delete_task`)
  that write straight to the same `db` collection the manual UI uses — so
  both update paths are the same data.
- No backend, no build step — publishing the HTML file *is* the deploy.

## Editing

Update `waypoint.html` and republish it as an Artifact to the same URL
(`Artifact` tool, passing `url` to update in place) to ship changes.
