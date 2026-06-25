# en-IE Go-Live — Action Plan

Live page: **https://nielsmolenaar-hp.github.io/enie-go-live-plan/**

Interactive plan for the Helloprint Ireland (en-IE) go-live (target: **3 July**). A single self-contained `index.html` — no build step, no server.

## What's in it

Four tabs:

- **Action plan** — every action grouped by workstream, each with Owner · Depends on · Timeline · Critical to go live · If not done. Filter by day, tick a **Done** checkbox, and leave per-field feedback (✎).
- **Timeline** — the day-by-day schedule (25 Jun → 3 Jul + fast-follow). Completed actions show a green ✓; a day turns green when everything in it is done.
- **What ifs?** — contingency scenarios with a take on each, plus a feedback box.
- **Technical requirements** — the launch-critical technical gates (pulled from the PDP/PLP/FLP specs), with deferred items behind a toggle. Set Required / Nice-to-have / Deferred / Context per item.

Use **Copy my feedback** (top bar) to export all your notes and decisions as text.

## How to update it

1. Edit `index.html` (or get a new version).
2. In this repo, open `index.html` → click the pencil ✏️ (or drag-drop the new file onto the repo) → **Commit changes** to `main`.
3. GitHub Pages redeploys automatically in ~1 minute.

## Notes

- **Feedback, checkboxes and filters are stored per-viewer** in the browser (localStorage) — your ticks and a colleague's don't sync. Shared/synced state would need a backend.
- The page carries a `noindex` tag, but the repo is **public** (required for free GitHub Pages), so anyone with the link can view it.
