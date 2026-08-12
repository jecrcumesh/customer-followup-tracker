# Customer Follow-up Tracker

A single-page tracker (SR No, Customer Name, Mobile No, Status, Last Follow-up,
Next Follow-up, Last Remark/Note) hosted on GitHub Pages, gated by a GitHub
token so the actual data stays private.

## Why two repos

Free GitHub Pages only works on **public** repos — private-repo Pages needs
GitHub Pro/Team/Enterprise. To keep data private without paying for that,
this app is split in two:

1. **`customer-followup-tracker`** (this repo, public) — just hosts
   `index.html`. It's a login screen + empty shell with no real data baked
   in, so being public is harmless.
2. **`customer-followup-tracker-data`** (private, sibling folder in the zip
   you got) — holds only `entries.json`. The app reads/writes it through
   GitHub's authenticated Contents API, so it's only visible to someone who
   supplies a valid token with access to *that* repo.

## How it works

- `index.html` opens on a login screen asking for a GitHub Personal Access
  Token (PAT).
- The token is checked by calling the data repo's Contents API for
  `entries.json`. Valid + has access → tracker unlocks. Otherwise, an error.
- Every add/edit/delete commits straight back to `entries.json` in the data
  repo via the API — changes are visible to anyone else with a valid token
  immediately, no manual download/upload step.
- The token lives only in the visitor's own browser (`sessionStorage` by
  default, `localStorage` if "Remember this token" is checked) and is never
  sent anywhere except `api.github.com`.

## 1. Create both repos on GitHub

- `customer-followup-tracker` → **public**
- `customer-followup-tracker-data` → **private**

## 2. Config (already set)

In `index.html`:

```js
const GITHUB_OWNER     = "jecrcumesh";
const GITHUB_DATA_REPO = "customer-followup-tracker-data";
const GITHUB_BRANCH    = "main";
const DATA_PATH        = "entries.json";
```

## 3. Push both repos

From the unzipped folder, each subfolder is its own git repo, already
committed on branch `main`:

```bash
# public app repo
cd customer-followup-tracker
git remote add origin https://github.com/jecrcumesh/customer-followup-tracker.git
git push -u origin main

# private data repo
cd ../customer-followup-tracker-data
git remote add origin https://github.com/jecrcumesh/customer-followup-tracker-data.git
git push -u origin main
```

## 4. Enable Pages on the public repo only

**Settings → Pages → Source → Deploy from a branch → main / (root)** on
`customer-followup-tracker`. Site will be live at
`https://jecrcumesh.github.io/customer-followup-tracker/`.

Do **not** enable Pages on the data repo — it doesn't need it.

## 5. Create a token

1. GitHub → **Settings → Developer settings → Personal access tokens →
   Fine-grained tokens → Generate new token**.
2. **Repository access:** Only select repositories →
   `customer-followup-tracker-data` (the data repo — not the public one).
3. **Permissions:** Repository permissions → **Contents: Read and write**.
4. Set an expiration, generate, and copy the token (shown once).
5. Share it only with people who should see/edit the tracker. Anyone with
   the token can read and modify the data — treat it like a password and
   revoke/rotate it in GitHub if it's ever exposed.

## Notes & limits

- The token lives in browser storage, so anyone with access to that browser
  profile could read it — the standard trade-off for a fully static (no
  backend) private app.
- If two people save at nearly the same time, the second save detects the
  conflict, refreshes to the latest data, and asks that person to redo their
  change — nothing is silently overwritten.
- **Log out** clears the token from the browser; **Refresh** re-pulls the
  latest data.

## File structure (as zipped)

```
customer-followup-tracker/          # public — Pages hosts this
├── index.html
└── README.md

customer-followup-tracker-data/     # private — API-only access
├── entries.json
└── README.md
```
