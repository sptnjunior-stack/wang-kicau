# Wang-Kicau — shared expense tracker for the group

Wang-Kicau runs on **GitHub Pages** and saves the group's data to a **private GitHub repo**. Anyone with the link and the **group passphrase** can use it on any phone or laptop, with no Claude or GitHub account needed. Everyone sees the same ledger.

| Page | What it does |
|---|---|
| **Dashboard** | Total spending (tap *See details* for each person), still owed with who pays whom, weekly / monthly spending chart, by event, by category, biggest expenses. |
| **Expenses** | Every bill, grouped by date or event. Tap one to see each person's share and mark it *Transferred*. |
| **Split & settle** | Who transfers to whom (debts between two people are netted), *Mark transferred*, copy a summary for the group chat, balances per person. |
| **Events** | Trips, projects or occasions. Expenses in an event's dates are tagged automatically. |
| **Configuration** | Members (up to 10, with colours), categories, appearance (light / dark / auto, colour theme), sync & backup. |

The filters at the top (dates, event, category, person) apply to every page. Bills can be split equally, by custom amounts, or **by items** (price × quantity, tag who had what; service charge and PB1/PPN are shared at the bill's rate). **Scan bill** reads a photo of the receipt on the device and fills in the items.

---

## 1. Set up (one time, about 15 minutes)

You'll make two repositories on your GitHub account (`sptnjunior-stack`):

- `wang-kicau` — **public**, hosts the website (contains no expenses).
- `wang-kicau-data` — **private**, holds `data.json` (the ledger) and `photos/` (bill photos).

Only you (the owner) need a GitHub account and a token. Friends just use the link and the passphrase.

### Step 1 — Create the private data repo

1. github.com → **New repository** → name `wang-kicau-data` → **Private** → tick **Add a README** → **Create**.
2. **Add file → Upload files** → drag in everything from the **`wang-kicau-data`** folder (`data.json` and the `photos` folder) → **Commit**.
   This brings over the members, expenses and bill photo already in the Claude version. (If you'd rather start empty, skip this; the app creates `data.json` on the first save.)

### Step 2 — Create the app repo and turn on GitHub Pages

1. **New repository** → name `wang-kicau` → **Public** → **Create**.
2. **Add file → Upload files** → drag in everything from the **`wang-kicau`** folder: `index.html`, `setup.html`, `manifest.webmanifest`, `README.md`, and the `assets` and `tess` folders → **Commit**.
   (`tess/` is the on-device bill reader. Its files are 3–4 MB each, which is fine for GitHub's web upload.)
3. Repo **Settings → Pages** → Source: **Deploy from a branch** → Branch **main** / **(root)** → **Save**.
4. After a minute the site is live at **`https://sptnjunior-stack.github.io/wang-kicau/`**. It shows a "not connected yet" screen until Step 4.

### Step 3 — Create the access key (fine-grained token)

1. github.com → avatar → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
2. **Token name:** `wang-kicau`. **Resource owner:** your account. **Expiration:** e.g. 1 year (put a reminder in your calendar to renew it).
3. **Repository access:** *Only select repositories* → `wang-kicau-data`.
4. **Permissions → Repository permissions → Contents: Read and write.** (Metadata: Read-only is added automatically.) Nothing else.
5. **Generate** and copy the token (`github_pat_…`). Don't paste it anywhere public.

### Step 4 — Lock the key with the group passphrase

1. Open **`https://sptnjunior-stack.github.io/wang-kicau/setup.html`**.
2. Fill in owner `sptnjunior-stack`, repo `wang-kicau-data`, branch `main`, and paste the token → **Check access**. It confirms the repo is private and the token can write to it.
3. Choose the **group passphrase**: at least four random words, e.g. `kopi senja ojek pelangi`. Type it twice → **Create config.js**.
4. Copy the text it gives you. On GitHub, open `wang-kicau/assets/config.js` → pencil icon → replace everything with the copied text → **Commit**.

The token is now stored in the public repo **only in encrypted form**; it can be unlocked only with the passphrase.

### Step 5 — Open it and share

1. Open `https://sptnjunior-stack.github.io/wang-kicau/` (wait a minute after the commit), type the passphrase → **Unlock**. The pill under the logo turns green: *Synced just now*.
2. Send the group **the link and the passphrase separately and privately** (for example, the link in the group chat and the passphrase by direct message).
3. On phones: Safari → Share → **Add to Home Screen** (or Chrome → menu → *Add to home screen*). It opens full screen with the Wang-Kicau icon.

Each device asks for the passphrase once and then remembers it. **Configuration → Sync & backup → Lock this device** forgets it (use this on a borrowed phone).

---

## 2. Everyday use

- **Add expense** or **Scan bill** (top right). Scanning works best with a straight, well-lit photo; check the total and items before saving. Tag who had each item; untagged items are shared by everyone tagged on the bill.
- **Mark transfers** on *Split & settle* (whole pair at once) or on an expense (one person's share). The *Still owed* card on the Dashboard updates straight away.
- **Events:** create one for a trip, then filter by it to see that trip's total and settle it separately.

### How syncing works

- Every change is saved on the device immediately and pushed to `data.json` about 1 second later. Each save is a **commit** in `wang-kicau-data`, so the commit history is a full audit trail and backup.
- The app pulls the latest data when you open or return to it and every 30 seconds.
- If two people edit at the same time, changes are **merged record by record** (the newest edit to each expense wins; deletions are remembered), so nothing is overwritten.
- Offline? Keep going; it syncs when you're back online. The pill shows *Offline · will sync*.
- Bill photos are saved in `wang-kicau-data/photos/`.

### Backups

**Configuration → Sync & backup → Download backup** saves everything as a JSON file. **Restore backup** merges a file back in. You can also restore any older version of `data.json` from the data repo's commit history.

---

## 3. Renewing the key or changing the passphrase

When the token expires (or if the passphrase leaks):

1. Create a new fine-grained token (Step 3). Delete the old one.
2. Run `setup.html` again with the new token and a new passphrase, and replace `assets/config.js` (Step 4).
3. Tell the group the new passphrase. Each device unlocks once more.

## Security, in plain words

- Expenses live only in the **private** repo. The public repo has the app and an **encrypted** key.
- The key can only change files in `wang-kicau-data`; it can't touch your other repos or your account.
- Anyone with the passphrase can read and edit the ledger, which is the point for a friends' group. A long passphrase matters because the encrypted key is public: short ones can be guessed.
- Never commit the raw `github_pat_…` token to the public repo. If that happens, delete the token on GitHub immediately and make a new one.

## Troubleshooting

| Message | Fix |
|---|---|
| *This copy isn't connected to a data repo yet* | `assets/config.js` still has `lock: null`. Do Step 4. |
| *That passphrase doesn't match* | Check spelling, spaces and capital letters. If you changed it, everyone needs the new one. |
| *Access key was rejected or has expired* | Renew the token (section 3). |
| *Data repo wasn't found* | Check owner / repo in `config.js`, and that the token's repository access includes `wang-kicau-data`. |
| *GitHub's hourly limit was reached* | The shared key allows 5,000 requests an hour, plenty for 10 people; it clears within the hour and nothing is lost. |
| Bill reader doesn't start | Use a recent Chrome or Safari. You can still type the bill in or use *Paste text*. |
| Changes don't show on another phone | Wait 30 seconds or reopen the app; check the sync pill on both devices. |

## Files

```
index.html            the app
setup.html            owner-only tool that encrypts the access key with the group passphrase
manifest.webmanifest  "Add to Home Screen" name and icons
assets/config.js      repo settings + encrypted key (public, no secrets in plain text)
assets/icon*.png/svg  app icons
tess/                 on-device bill reader (Tesseract OCR + English language data)
```

No build step and no server: plain HTML, CSS and JavaScript. Fonts load from Google Fonts.
Differences from the Claude version: there's no Claude double-check when a scanned bill's numbers don't add up (you check those by hand), and Claude sharing is replaced by the link + passphrase.
