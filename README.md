# Commission Tracker

A single-file HTML commission tracker built for artists and creators. Track the status of your commissions publicly while keeping client notes, checklists, and private details encrypted and admin-only. Live data syncs across all browsers via JSONBin.

**Built by [ohshinakai](https://ohshinakai.github.io)**
# Commission Tracker

A single-file HTML commission tracker built for artists and creators. Track the status of your commissions publicly while keeping client notes, checklists, and private details encrypted and admin-only. Live data syncs across all browsers via JSONBin.

**Built by [ohshinakai](https://ohshinakai.github.io)**

---

## Features

### Public View
- Live commission table with ID, type, commission name, status, payment, due date, franchise, and race/species
- Clickable stat chips (Active, In Progress, Unpaid, Completed, Total) that filter the table instantly
- Search by commission name, franchise, and race
- Filter by type, contact method, and status
- Sort any column by clicking its header
- "Show Done" toggle to include or hide completed commissions
- Dark / light mode toggle
- Hover over any commission name to see a preview image, franchise, and race
- Fully responsive — works on desktop and mobile

### Admin View (password-protected)
- All public features, plus:
- Client username and contact method columns
- Add, edit, and delete commissions
- Per-commission notes and requests field
- Drag-to-reorder process checklist with custom items
- Preview image per commission (URL paste or file upload)
- Export data as a `.json` backup
- Import from a `.json` backup
- Clear all commissions
- Editable tracker name (click the logo)
- JSONBin live sync configuration
- Password change via setup URL

---

## Tech Stack

- Pure HTML, CSS, and JavaScript — no frameworks, no build step
- **AES-GCM 256-bit encryption** (Web Crypto API) for all private fields
- **PBKDF2** key derivation with 200,000 iterations
- **JSONBin.io** for live public data sync
- All data stored in `localStorage` and JSONBin — no server required

---

## Setup Guide

### Step 1 — JSONBin Account

1. Create a free account at [jsonbin.io](https://jsonbin.io)
2. Go to **Bins → Create a Bin** and paste this as the initial content:
   ```json
   {"commissions": [], "nextId": 1}
   ```
3. Set the bin to **Public** (privacy toggle in the bin settings)
4. Copy your **Bin ID** from the URL
5. Go to **API Keys** and copy your **Master Key**

### Step 2 — Bake in Your Bin ID

Open `index.html` in a text editor and find this line near the top of the `<script>` block:

```js
const BAKED_BIN_ID = ''; // Add your JSONBin Bin ID here after setup
```

Replace it with your Bin ID:

```js
const BAKED_BIN_ID = 'your-bin-id-here';
```

This allows any visitor (any browser, any device) to read your live commission data without needing to configure anything.

### Step 3 — Set Your Setup Secret

The admin password is initialised via a secret URL parameter. Choose a secret phrase, generate its SHA-256 hash at [emn178.github.io/online-tools/sha256.html](https://emn178.github.io/online-tools/sha256.html), then replace the placeholder in `index.html`:

```js
const SETUP_SECRET_HASH = ''; // Paste your SHA-256 hash here
```

Becomes:

```js
const SETUP_SECRET_HASH = 'your-sha256-hash-here';
```

Keep your original secret phrase private — this is what protects the admin setup flow.

### Step 4 — Upload to GitHub Pages (or any static host)

Commit `index.html` to your repository and enable GitHub Pages under **Settings → Pages**. Netlify, Vercel, and Cloudflare Pages also work with no additional configuration.

### Step 5 — First-Time Admin Setup

1. Visit your live site with the setup parameter:
   ```
   https://yourusername.github.io/your-repo/index.html?setup=YOUR_SECRET_PHRASE
   ```
2. Click the lock icon — a **"Set Admin Password"** modal appears
3. Before setting a password, click **⚙ Configure JSONBin First**
4. Enter your **Master Key** and **Bin ID**, click **Test Connection**, then **Save & Sync**
5. The JSONBin modal closes and the password modal reopens automatically
6. Enter your chosen admin password and click **Set Password**

Your admin password is now stored as an encrypted verify token on JSONBin. It is never stored in plain text anywhere.

> **Note:** You must configure JSONBin before setting your password. If you try to set a password without JSONBin configured, the tracker will show an error and display the **⚙ Configure JSONBin First** button automatically.

### Step 6 — Import Your Data (optional)

If you have an existing `.json` export, unlock admin mode, click **↑ Import**, and select the file. Your commission data will load and sync to JSONBin automatically.

---

## Daily Workflow

**Managing commissions (any browser):**
1. Visit your live site URL
2. Click the lock icon and enter your password
3. Make changes — status updates, new entries, notes, images
4. Changes sync to JSONBin automatically — all visitors see live data immediately

**Updating the tracker itself (new features, design changes):**
1. Edit `index.html` locally
2. Test in your browser
3. Upload to GitHub Pages — your JSONBin data and password are unaffected

---

## Admin Features In Detail

### Adding a Commission
Click **+ New** in the toolbar. Fill in the name, type, client username, contact, status, due date, franchise, race, and optionally a preview image. Click **Create**.

### Editing a Commission
Click any row in the table to open the detail panel. All fields are editable. Click **Save Changes** to update and sync.

### Process Checklist
Each commission has a 10-item default checklist:
- Commission folder created
- Initial prompt created
- Background
- Expression / pose variant
- Editing required
- Post-processing complete
- Backstory written
- Revisions done
- Delivered to client
- Posted to website

Tick items as you complete them. Drag the ⠿ handle to reorder. Add custom items with the text field at the bottom.

### Preview Images
In the detail panel, paste a direct image URL or upload a file. For best results with Imgur, use `i.imgur.com/IMAGEID.jpg` — the tracker auto-converts standard Imgur page URLs to direct links. Uploaded files are stored as base64 locally; URL-based images sync via JSONBin.

### Export & Import
Use **↓ Export** to download a `.json` backup of all your data. Use **↑ Import** to restore from a backup. Keep regular exports as a safety net.

### Changing Your Admin Password
Visit your site with the setup URL:
```
https://yourusername.github.io/your-repo/index.html?setup=YOUR_SECRET_PHRASE
```
A **Reset Password** section appears below the normal login. Enter your current password and your new password, then click **↺ Confirm Reset**. Your current password is verified before the reset is allowed.

---

## Security Model

| Scenario | Outcome |
|----------|---------|
| Public visitor | Sees commissions, statuses, franchises, races, and images only |
| Someone downloads the HTML | Cannot unlock admin — no valid verify token exists in their browser |
| Someone finds the setup URL | Cannot set or reset a password without knowing the current admin password |
| Someone brute-forces the password | Infeasible — PBKDF2 with 200,000 iterations per attempt |
| Admin notes in JSONBin | Never written there — encrypted and stored in localStorage only |

Private fields (notes, checklists, contact details) are encrypted with AES-GCM before being stored locally and are never sent to JSONBin.

---

## Switching Devices

On a new browser or device:
1. Visit the live site and click the lock icon
2. Enter your admin password — the verify token is fetched from JSONBin and verified
3. Click **⚙ JSONBin** in the admin toolbar and enter your Master Key and Bin ID
4. You're in — private notes and checklists stored locally will be empty on the new device until you import a backup

---

## Hosting Options

The tracker is a single HTML file and works on any static host:

| Host | Notes |
|------|-------|
| **GitHub Pages** | Free, connects to your repo, auto-deploys on push |
| **Netlify** | Free tier, drag-and-drop deploy at app.netlify.com/drop |
| **Vercel** | Free tier, connects to GitHub for auto-deploys |
| **Cloudflare Pages** | Free, fast global CDN, connects to GitHub |
| **itch.io** | Good option for the art/adoptables community |

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Features

### Public View
- Live commission table with ID, type, commission name, status, payment, due date, franchise, and race/species
- Clickable stat chips (Active, In Progress, Unpaid, Completed, Total) that filter the table instantly
- Search by commission name, franchise, and race
- Filter by type, contact method, and status
- Sort any column by clicking its header
- "Show Done" toggle to include or hide completed commissions
- Dark / light mode toggle
- Hover over any commission name to see a preview image, franchise, and race
- Fully responsive — works on desktop and mobile

### Admin View (password-protected)
- All public features, plus:
- Client username and contact method columns
- Add, edit, and delete commissions
- Per-commission notes and requests field
- Drag-to-reorder process checklist with custom items
- Preview image per commission (URL paste or file upload)
- Export data as a `.json` backup
- Import from a `.json` backup
- Clear all commissions
- Editable tracker name (click the logo)
- JSONBin live sync configuration
- Password change via setup URL

---

## Tech Stack

- Pure HTML, CSS, and JavaScript — no frameworks, no build step
- **AES-GCM 256-bit encryption** (Web Crypto API) for all private fields
- **PBKDF2** key derivation with 200,000 iterations
- **JSONBin.io** for live public data sync
- All data stored in `localStorage` and JSONBin — no server required

---

## Setup Guide

### 1. JSONBin Account

1. Create a free account at [jsonbin.io](https://jsonbin.io)
2. Go to **Bins → Create a Bin** and paste this as the initial content:
   ```json
   {"commissions": [], "nextId": 1}
   ```
3. Set the bin to **Public** (privacy toggle in the bin settings)
4. Copy your **Bin ID** from the URL
5. Go to **API Keys** and copy your **Master Key**

### 2. Bake in Your Bin ID

Open `index.html` in a text editor and find this line near the top of the `<script>` block:

```js
const BAKED_BIN_ID = '';
```

Replace it with your Bin ID:

```js
const BAKED_BIN_ID = 'your-bin-id-here';
```

This allows any visitor (any browser, any device) to read your live commission data without needing to configure anything.

### 3. Set Your Setup Secret

The admin password is initialised via a secret URL parameter. Choose a secret phrase, generate its SHA-256 hash at [emn178.github.io/online-tools/sha256.html](https://emn178.github.io/online-tools/sha256.html), then replace the hash in `index.html`:

```js
const SETUP_SECRET_HASH = 'your-sha256-hash-here';
```

Keep your secret phrase private — this is what protects the admin setup flow.

### 4. Upload to GitHub Pages (or any static host)

Commit `index.html` to your repository and enable GitHub Pages under **Settings → Pages**. Netlify, Vercel, and Cloudflare Pages also work with no additional configuration.

### 5. First-Time Admin Setup

1. Visit your live site with the setup parameter:
   ```
   https://yourusername.github.io/your-repo/?setup=YOUR_SECRET_PHRASE
   ```
2. Click the lock icon — a "Set Admin Password" prompt appears
3. Enter your chosen password and click **Set Password**
4. Click **⚙ JSONBin** in the admin toolbar and enter your Master Key and Bin ID
5. Click **Test Connection**, then **Save & Sync**

Your admin password is now stored as an encrypted verify token on JSONBin. No password is ever stored in plain text.

### 6. Import Your Data (optional)

If you have an existing `.json` export, unlock admin mode, click **↑ Import**, and select the file. Your commission data will load and sync to JSONBin automatically.

---

## Daily Workflow

**Managing commissions (any browser):**
1. Visit your live site URL
2. Click the lock icon and enter your password
3. Make changes — status updates, new entries, notes, images
4. Changes sync to JSONBin automatically — all visitors see live data immediately

**Updating the tracker itself (new features, design changes):**
1. Edit `index.html` locally
2. Test in your browser
3. Upload to GitHub Pages — your JSONBin data and password are unaffected

---

## Admin Features In Detail

### Adding a Commission
Click **+ New** in the toolbar. Fill in the name, type, client username, contact, status, due date, franchise, race, and optionally a preview image. Click **Create**.

### Editing a Commission
Click any row in the table to open the detail panel. All fields are editable. Click **Save Changes** to update and sync.

### Process Checklist
Each commission has a 10-item default checklist:
- Commission folder created
- Initial prompt created
- Background
- Expression / pose variant
- Editing required
- Post-processing complete
- Backstory written
- Revisions done
- Delivered to client
- Posted to website

Tick items as you complete them. Drag the ⠿ handle to reorder. Add custom items with the text field at the bottom.

### Preview Images
In the detail panel, paste a direct image URL or upload a file. For best results with Imgur, use `i.imgur.com/IMAGEID.jpg` — the tracker auto-converts standard Imgur page URLs to direct links. Uploaded files are stored as base64 locally; URL-based images sync via JSONBin.

### Export & Import
Use **↓ Export** to download a `.json` backup of all your data. Use **↑ Import** to restore from a backup. Keep regular exports as a safety net.

### Changing Your Admin Password
Visit your site with the setup URL:
```
https://yourusername.github.io/your-repo/?setup=YOUR_SECRET_PHRASE
```
A **Reset Password** section appears below the normal login. Enter your current password and your new password, then click **↺ Confirm Reset**. Your current password is verified before the reset is allowed.

---

## Security Model

| Scenario | Outcome |
|----------|---------|
| Public visitor | Sees commissions, statuses, franchises, races, images only |
| Someone downloads the HTML | Cannot unlock admin — no valid verify token in their browser |
| Someone finds the setup URL | Cannot reset without knowing the current admin password |
| Someone brute-forces the password | Infeasible — PBKDF2 with 200,000 iterations per attempt |
| Admin notes in JSONBin | Never written there — encrypted and localStorage-only |

Private fields (notes, checklists, contact details) are encrypted with AES-GCM before being stored locally and are never sent to JSONBin.

---

## Switching Devices

On a new browser or device:
1. Visit the live site and click the lock icon
2. Enter your admin password — the verify token is fetched from JSONBin
3. Click **⚙ JSONBin** and enter your Master Key and Bin ID
4. You're in — private notes and checklists stored locally will be empty on the new device until you import a backup

---

## License

MIT License — see [LICENSE](LICENSE) for details.
