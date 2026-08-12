# Customer Follow-up Tracker

A single-page tracker (SR No, Customer Name, Mobile No, Status, Last Follow-up,
Next Follow-up, Last Remark/Note) that reads its data from `data/entries.json`
in this repo and runs entirely in the browser — no server or database needed.

## How data works

- On load, the app fetches `data/entries.json` from the repo and merges it
  with anything cached in your browser's local storage, so it always shows
  the latest committed data plus any edits you haven't pushed yet.
- Rows with a blue left-edge stripe and a "not committed" tag are changes
  that only exist in your browser so far.
- **Download updated entries.json** exports the full current dataset
  (repo data + your local edits, merged — never a blind overwrite).
- Replace `data/entries.json` with the downloaded file and commit/push to
  make your changes permanent and visible to anyone else opening the site.
- **Sync from repo** re-fetches `data/entries.json`, useful after you or a
  teammate pushes new data.

This "manual commit" model was chosen deliberately: GitHub Pages is static
and can't accept writes on its own, and auto-committing from the browser
would require embedding a GitHub access token in the page, which anyone
viewing the site could extract. Downloading + committing keeps things
simple and secure.

## Deploy to GitHub Pages

1. Create a new **empty** repository on GitHub (no README/license), e.g.
   `customer-followup-tracker`.
2. From this folder, push it (already git-initialized with one commit on
   branch `master` — rename to `main` first if you want to match GitHub's
   default):
   ```bash
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages → Source → Deploy from a branch**, branch
   `main`, folder `/ (root)`, Save.
4. Your app will be live at:
   `https://<your-username>.github.io/<repo-name>/`

## Updating data going forward

1. Open the live site, add/edit/delete entries as needed.
2. Click **Download updated entries.json**.
3. In your repo, replace `data/entries.json` with the downloaded file
   (either via `git add/commit/push`, or GitHub's web UI: open the file →
   pencil icon → upload/paste new content → Commit).
4. Refresh the site (or click **Sync from repo**) — everyone now sees the
   updated data.

## File structure

```
customer-followup-tracker/
├── index.html         # the app
├── data/
│   └── entries.json   # the data (source of truth once committed)
└── README.md
```
