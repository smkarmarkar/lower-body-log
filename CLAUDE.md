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

- Workouts and notes live in the phone's localStorage. They sync to a secret Gist described as "Lower Body Log private data", using a token that exists only on the phone.
- Never ask for the token, and never put the Gist ID in this public repo. Ask Sandeep for the ID when you need it. Anyone can read the Gist with the ID at `https://api.github.com/gists/<id>`.
- Gist files: `history.json`, `feedback.json`, and `program.json` (only after in-app edits).
- Merge is by `id`, and the newer `updated` wins.

## "fix feedback"

Sandeep leaves notes with the in-app bug button and approves them on the phone. When he says "fix feedback":

1. Read `feedback.json` from the Gist. Act only on notes with `status: "approved"`.
2. Fix them in `index.html`, bump `APP_VERSION`, and push to `main`. Check that the live site serves the new version.
3. Update each handled note in the Gist:
   - set `status: "fixed"` and `fixedIn: "<version>"`;
   - push `{by: "claude", at: <ms>, text: "what changed"}` onto `thread`;
   - set `updated` to now.
4. Writing to the Gist needs Sandeep's GitHub session or token. If you can't write, list the replies for him instead. The app picks up changes on its next sync.
5. Tell him to tap Refresh in the app.

Test at phone size (393x793) before pushing. Adding `#fast` to the URL shortens rests to 5 seconds and holds to 3 seconds.
