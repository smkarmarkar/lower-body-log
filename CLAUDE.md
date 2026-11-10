# Strength Log (lower-body-log)

A phone-first guided strength workout app for Sandeep, served by GitHub Pages at https://smkarmarkar.github.io/lower-body-log/ from `main`. Pushing to `main` deploys it, usually within a minute.

## Files

- `index.html`: the whole app (HTML, CSS and JS in one file, no build step, no dependencies). Edit it directly.
- `manifest.webmanifest`, `icon-180.png`: home-screen install.

Inside `index.html`:

- `/* THEME */` holds the colour and type tokens, and `/* VIEW */` holds the render functions. Keep restyling to those two.
- `PROGRAM` holds the workout: Coach Deba's Phase 1 Hypertrophy, 4 sessions with keys `u1` Upper 1, `d1` Lower 1, `u2` Upper 2, `d2` Lower 2. `DAYS` sets their order.
  - Keep the existing exercise `id`s, because past workouts and the "Last time" weights refer to them.
  - `d1` and `d2` are the original Lower 1 and Lower 2 keys.
- Superset notation: the same number with letters (`6A`, `6B`, `6C`) means the exercises run back to back, then the first one's `rest`. The coach's A1/A2 groups map to these numbers.
- Bump `PROGRAM_REV` whenever `PROGRAM` changes. In-app edits (stored as `lbl.program` and `program.json`) only apply while their `rev` matches.
- Bump `APP_VERSION` on every release.

## Data

- Workouts live in the phone's localStorage. They sync to a secret Gist described as "Lower Body Log private data", using a token that exists only on the phone.
- Never ask for the token, and never put the Gist ID in this public repo. Ask Sandeep for the ID when you need it. Anyone can read the Gist with the ID at `https://api.github.com/gists/<id>`.
- Gist files: `history.json` and `program.json` (only after in-app edits). `feedback.json` is old and no longer used.
- Merge is by `id`, and the newer `updated` wins.

## Feedback ("fix feedback for strength-log")

Notes come from the shared Bugbox widget (https://github.com/smkarmarkar/bugbox), with app id `strength-log`. The app loads it at the end of `index.html` and passes the current screen as context. The steps for handling approved notes are in Bugbox's `CLAUDE.md`. The old notes in the Gist's `feedback.json` were copied into Bugbox on 2026-11-08, and the app no longer reads or writes that file.

## New phase from a PDF ("build new phase")

Sandeep uploads a coach PDF in Settings → New phase, with an optional note. The app saves the PDF to the Gist as `phase-request-<id>.pdf.b64` (a data URL) and adds an entry to `phase-requests.json`: `{id, at, updated, file, name, size, note, status: pending|built|needs-info, builtAs, reply}`.

1. Read the Gist (ask Sandeep for the ID). Take each `pending` request, decode its `.pdf.b64`, and read the PDF together with the `note`. The note wins over the PDF.
2. Build the sessions exactly like Phase 1: new `PROGRAM` keys (`p2s1`, `p2s2`, …), blocks with superset numbers (A1/A2 → `7A`/`7B`), sets, reps, tempo, RIR, rest, warm-up text, and hold timers.
3. Reuse an existing exercise `id` when it is the same exercise, so "Last time" weights carry over. New exercises get new ids.
4. Every exercise needs a video: search YouTube for a short vertical demo with high views, and check that it plays (oEmbed).
5. Append `{id:'p2', name:'Phase 2', focus:'…', days:[…]}` to `PHASES`. The last phase is the current one on Home; earlier phases stay reachable through the phase switcher. Bump `PROGRAM_REV` and `APP_VERSION`, test at phone size, then push.
6. Set the request to `status:'built'`, `builtAs:'Phase 2'`, `updated:<now>` in `phase-requests.json`. If something is unclear, set `status:'needs-info'` with a `reply` question instead; the app shows it under New phase.

Test at phone size (393x793) before pushing. Adding `#fast` to the URL shortens rests to 5 seconds and holds to 3 seconds.
