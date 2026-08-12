# Customer Follow-up Tracker

A single-page tracker (SR No, Customer Name, Mobile No, Status, Last Follow-up,
Next Follow-up, Last Remark/Note) hosted on GitHub Pages. The page itself is
public (that's how free Pages works), but it shows nothing until the visitor
enters a valid GitHub token — the data lives in a **private** repo and is
only readable/writable through GitHub's authenticated API.

## How it works

- `index.html` opens on a login screen asking for a GitHub Personal Access
  Token (PAT).
- The token is checked by calling GitHub's Contents API for
  `data/entries.json`. If it's valid and has access to the repo, the tracker
  unlocks; otherwise an error is shown.
- Every add/edit/delete commits straight back to `data/entries.json` in the
  repo via the API (no manual download/upload step) — changes are visible to
  anyone else with a valid token immediately.
- The token is stored only in the visitor's own browser (`sessionStorage` by
  default, or `localStorage` if "Remember this token" is checked) — it's
  never sent anywhere except `api.github.com`.

## 1. Make the repo private

Data privacy only holds if the repo is private. On GitHub: **Settings →
General → Danger Zone → Change visibility → Private** (or create it private
from the start).

> Note: GitHub Pages built from a private repo publishes the page's *HTML/JS*
> publicly regardless (that's expected — it's just the empty shell/login
> screen). What stays protected is `data/entries.json`, since it's fetched
> through the authenticated API, not as a static file.

## 2. Configure the app

Already set in `index.html`:

```js
const GITHUB_OWNER  = "jecrcumesh";
const GITHUB_REPO   = "customer-followup-tracker";
const GITHUB_BRANCH = "main";
const DATA_PATH     = "data/entries.json";
```

## 3. Push to GitHub

Already `git init`'d locally with one commit on branch `main`:

```bash
git remote add origin https://github.com/jecrcumesh/customer-followup-tracker.git
git push -u origin main
```

Then: **Settings → Pages → Source → Deploy from a branch → main / (root)**.
Site will be live at `https://jecrcumesh.github.io/customer-followup-tracker/`.

## 4. Create a token for yourself (and anyone else who should have access)

1. GitHub → **Settings → Developer settings → Personal access tokens →
   Fine-grained tokens → Generate new token**.
2. **Repository access:** Only select repositories → pick this repo.
3. **Permissions:** Repository permissions → **Contents: Read and write**.
   (Nothing else is needed.)
4. Set an expiration and generate. Copy the token (`github_pat_...`) — GitHub
   only shows it once.
5. Share it only with people who should be able to see/edit the tracker.
   Anyone with the token can read and modify the data, so treat it like a
   password and rotate/revoke it in GitHub if it's ever exposed.

## Notes & limits

- Because the token lives in browser storage, anyone with physical/malware
  access to that browser profile could read it — this is the standard
  trade-off for a fully static (no backend) private app. For stronger
  security you'd need a small server component to hold the token instead.
- If two people save changes at nearly the same time, the second save will
  detect the conflict, automatically refresh to the latest data, and ask
  that person to redo their change (no data is silently overwritten).
- **Log out** clears the token from the browser; **Refresh** re-pulls the
  latest data from the repo.

## File structure

```
customer-followup-tracker/
├── index.html         # public shell + login gate + app
├── data/
│   └── entries.json   # private data, accessed only via GitHub API
└── README.md
```
