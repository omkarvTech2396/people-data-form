# People Data Collection — Deployment Guide

Three parts:
1. **Code.gs** — backend (Google Apps Script) — storage + PDF generation
2. **index.html** — public form (share this link on WhatsApp)
3. **dashboard.html** — your private view/export screen (don't share this link publicly)

---

## Step 1: Set up the Google Sheet + Backend

1. Go to [sheets.google.com](https://sheets.google.com) → create a new blank Sheet. Name it e.g. `People Data`.
2. In the Sheet, go to **Extensions → Apps Script**.
3. Delete any starter code in the editor, then paste in the entire contents of **Code.gs**.
4. Click **Save** (disk icon), name the project e.g. `People Data Backend`.
5. Click **Deploy → New deployment**.
   - Click the gear icon next to "Select type" → choose **Web app**.
   - Description: anything, e.g. `v1`.
   - Execute as: **Me**.
   - Who has access: **Anyone** (this allows your public form and dashboard to reach it — the URL itself is long and unguessable, but see the security note at the bottom).
   - Click **Deploy**.
6. Authorize the permissions when prompted (click through the "unverified app" warning — this is expected since it's your own personal script).
7. Copy the **Web App URL** it gives you — it will look like:
   `https://script.google.com/macros/s/AKfycb.../exec`

Keep this URL — you need it in the next step.

---

## Step 2: Wire up the front-end pages

1. Open **index.html** and **dashboard.html** in a text editor.
2. In both files, find this line near the bottom:
   ```js
   const SCRIPT_URL = "PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE";
   ```
3. Replace the placeholder with the Web App URL you copied in Step 1. Save both files.

---

## Step 3: Host on GitHub Pages

1. Create a new GitHub repository (e.g. `people-data-form`).
2. Upload `index.html` and `dashboard.html` to the repo root.
3. Go to **Settings → Pages** → under "Build and deployment", choose **Deploy from a branch** → select `main` branch, `/root` folder → **Save**.
4. After a minute, GitHub will give you a live URL like:
   `https://yourusername.github.io/people-data-form/`
5. Your two live pages will be:
   - Form (share on WhatsApp): `https://yourusername.github.io/people-data-form/index.html`
   - Dashboard (keep private): `https://yourusername.github.io/people-data-form/dashboard.html`

---

## How it works

- **Form submissions** → sent via `fetch()` POST → Apps Script writes a new row into the `Responses` sheet tab (auto-created on first submission).
- **Dashboard** → loads all entries via `?action=list`, shows them in a list with checkboxes.
  - Click a name → view all fields in a popup.
  - Click **PDF** on a row → downloads a single-entry PDF.
  - Select multiple (or "Select all") → **Export Selected** → downloads one combined PDF with a page per person.
- **PDF format** matches what you asked for:
  ```
  First Name : ...
  Middle Name : ...
  Last Name : ...
  Date of Birth : ...
  Address : ...
  Place of Birth : ...
  ```
- **Weekly backup**: open the Google Sheet anytime → File → Download → Microsoft Excel (.xlsx).

---

## Security note (important)

Your dashboard link is **not password-protected** — anyone with the dashboard URL could view/export all collected data (names, addresses, DOB). Since GitHub Pages is fully public, this matters because you're handling other people's personal information. Two low-effort ways to reduce exposure:

1. **Don't publish the dashboard link anywhere public** — keep it bookmarked privately, don't commit it to a public README, don't share it in the same place as the form link.
2. **Add a simple password gate** to `dashboard.html` (a basic prompt() check against a fixed string) — this isn't real security, but stops casual/accidental access. I can add this if you'd like.

For anything beyond personal/informal use, consider making the GitHub repo **private** (GitHub Pages works on private repos with GitHub Pro, or you can host the dashboard differently) and adding real authentication.

---

## Optional next steps I can help with

- Add a duplicate-detection tag (e.g. matching on name + DOB) like we discussed earlier
- Add a password gate on the dashboard
- Add a consent checkbox + text to the form
- Add more fields later (e.g. your "life incident" field) — just add matching columns in Code.gs and index.html
