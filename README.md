# Claude Prototypes

Self-contained, no-server web app prototypes. Each app is a single HTML file — open it directly in a browser (`file://` works) or use the GitHub Pages links below. No build step, and no account is ever required (PAPTrack offers an optional Google sign-in purely for cross-device sync).

- **ReturnRadar:** [eagleadams86.github.io/prototypes/returnradar.html](https://eagleadams86.github.io/prototypes/returnradar.html)
- **PAPTrack:** [eagleadams86.github.io/prototypes/cpap-tracker.html](https://eagleadams86.github.io/prototypes/cpap-tracker.html)

---

## Files

| File | Description |
|------|-------------|
| `returnradar.html` | Return-window & warranty deadline tracker — the entire app in one file |
| `cpap-tracker.html` | PAPTrack: CPAP supply cleaning, replacement & reorder tracker — one file |
| `favicon.ico` | Shared favicon (same one used across the dashboard and lottery repos) |

---

## 📡 ReturnRadar

Never miss a return window or warranty again. Americans forfeit billions every year in missed return windows and unclaimed warranties — the usual "solution" is a spreadsheet or a subscription app that demands an account and receipt scanning. ReturnRadar is the opposite: a free, private, works-offline tracker that answers one question — *how many days do I have left to act on this purchase?*

### Logging a purchase (~15 seconds)

- **Store presets auto-fill the return window** — type a store name and the typical return period fills in automatically. 20 common US retailers are built in (Amazon 30d, Target/Walmart/Costco/Home Depot/Lowe's 90d, Best Buy/GameStop 15d, Apple 14d, Kohl's 180d, IKEA/REI 365d, Nike 60d, and more); everything is editable per item
- **Quick-tap warranty chips** — common warranty lengths fill with one tap instead of typing
- **Categories** — 9 emoji-tagged categories (Electronics, Clothing & shoes, Home & kitchen, Tools & auto, Kids & toys, Sports & outdoors, Health & beauty, Gifts, Other)
- **Price and notes** — track what's at stake and where the receipt lives (order number, email, serial number, etc.)

### Urgency-first dashboard

- **Sorted by nearest deadline** — the item you most need to act on is always at the top; returned items sink to the bottom
- **Countdown badges colored by severity** — critical (≤3 days, red), serious (≤7 days, orange), warning (≤14 days, amber). Every badge pairs a colored dot + icon + text, so color is never the only signal
- **Stat tiles** — three at-a-glance numbers: total dollars **still returnable**, total dollars **under warranty**, and the **next deadline** with days remaining
- **Search** — live filter across item names, stores, and notes
- **Filter chips** — All active, ↩︎ Returnable, ⏰ Due soon, 🛡 Under warranty, Expired, ✓ Returned
- **Mark returned / un-mark** — one tap flips an item's state (with a small 🎉); returned items keep their history and can be un-marked

### Calendar reminders

- **One-tap `.ics` export per item** — any open return deadline downloads as a calendar event with a built-in alarm **2 days before** the deadline, so your phone nags you even when the app isn't open. Works with Apple Calendar, Google Calendar, Outlook — anything that opens `.ics`

### Data & privacy

- **100% private** — all data lives in the browser's `localStorage`. No account, no server, no analytics, nothing leaves the device
- **Works offline** — after the first load, no network is needed for anything
- **JSON backup export/import** — download all items as a JSON file and restore on any browser or device (this is also the migration path, since `localStorage` is per-browser)
- **Demo data** — the empty state offers 4 example items so you can explore the badges, filters, and stats before adding anything real

### Look & feel

- **Light & Midnight themes** — follows the system preference by default (`prefers-color-scheme`), with a 🌗 toggle in the header; choice is saved in `localStorage` and applied before first paint (no flash). The dark theme uses the same deep indigo/navy Midnight palette as the lottery pages
- **Mobile-first** — stat tiles reflow on narrow screens, filter chips scroll horizontally, safe-area insets handled for notched iPhones
- **iPhone home-screen support** — Apple web-app meta tags included, so "Add to Home Screen" gives a standalone app feel
- **Accessible** — ARIA labels/pressed states on the filter group and controls; urgency conveyed by icon + text, not color alone

---

## 🌬️ PAPTrack

Keeps CPAP supplies on schedule three ways at once: **cleaning** (daily/weekly reminders), **replacement** (countdowns per the standard DME schedule), and **inventory** (spares on hand with reorder flags). Built from a supplier's "clean & replace your equipment" flyer.

### Built-in supply presets

| Supply | Clean | Replace |
|--------|-------|---------|
| 😷 Mask | daily | every 3 months |
| 🪢 Headgear | weekly | every 6 months |
| 〰️ Tubing | weekly | every 3 months |
| 🌫️ Disposable Fine Filters | — (replace only) | every 2 weeks |
| 🛟 Full Face Cushions | daily | every month |
| 👃 Nasal Cushions & Pillows | daily | every 2 weeks |
| 💧 Humidifier Chamber | daily | every 6 months |

Every value is editable per item, and custom items are supported for anything else (chinstrap, SD card, wipes…).

### Features

- **One-tap setup** — "Load full-face kit" or "Load nasal kit" adds the six matching supplies at once; then set each item's real "last replaced" date via Edit
- **Replacement countdowns** — every item shows its next due date with urgency-colored badges (red when overdue, orange/amber as the deadline nears, otherwise green); the list sorts most-urgent first. Thresholds scale with each item's cycle (amber at ≤40%, orange at ≤20%, capped at 14/7 days), so a 2-week consumable isn't flagged "replace soon" the day it's replaced
- **Cleaning tracker** — items show "Cleaning due" or "Cleaned today/yesterday/N days ago"; one-tap **Mark cleaned**, plus a **Mark all due items cleaned** button for the daily routine
- **Last cleaned & last replaced** — every card shows how long ago the last replacement and cleaning happened (today / yesterday / N days ago; "Not cleaned yet" until the first one)
- **Inventory & reorder** — a spares counter with −/+ steppers on every card; items are flagged **Reorder** when spares fall to the item's threshold and a replacement is due within 30 days. **Replaced today** resets the countdown and consumes a spare automatically
- **Stat tiles** — items to clean today, next replacement (item + days), and how many supplies need reordering
- **Search & filters** — live search plus chips: All, 🧼 Clean due, ⏰ Replace due (due today or overdue), 📦 Reorder, ✓ All good
- **Calendar reminders** — one-tap `.ics` export per item with up to three events: next replacement (alarm 2 days before), a reorder reminder ahead of it (half the cycle for short-cycle consumables, at most 30 days — the typical insurance resupply window), and the next cleaning for items cleaned less often than daily
- **Resupply tip** — footer note about insurance PAP-adherence requirements and humidifier care (distilled water, empty every morning)
- **Private & offline** — all data in `localStorage`; JSON backup export/import; no account required
- **Cross-device sync (optional)** — a **☁️ Sign in to sync** button in the header signs in with Google and syncs via Firestore, so phone and computer share the same data live; signed out, the app is 100% local (see below)
- **7 themes, Midnight by default** — dropdown in the header with the same theme family as the lottery pages, listed alphabetically (Forest, Light, Midnight, Sepia, Slate, Solarized, Synthwave); choice saved in `localStorage` and applied before first paint

### Cross-device sync (Firebase, free tier)

Sync is **enabled** in this deployment, backed by the `paptrack-6c817` Firebase project — the `FIREBASE_CONFIG` object at the bottom `<script type="module">` block of `cpap-tracker.html` points at it. Setting that constant back to `null` returns the app to fully-local mode and hides all sync UI. To recreate the setup from scratch (e.g. in a fork):

1. At [console.firebase.google.com](https://console.firebase.google.com), create a project (Analytics not needed)
2. **Build → Authentication → Get started → Google** — enable the Google sign-in provider
3. **Authentication → Settings → Authorized domains** — add `eagleadams86.github.io`
4. **Build → Firestore Database → Create database** (production mode), then paste these **Rules**:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /paptrack/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
5. **Project settings → Your apps → Add app → Web** — copy the `firebaseConfig` object and paste it as the value of `FIREBASE_CONFIG` in `cpap-tracker.html`

The config object is not a secret (access is controlled by the rules above, which restrict each user to their own document). How sync behaves: `localStorage` stays the source of truth; on sign-in, whichever side changed most recently (`updatedAt`) wins; after that, edits push (debounced) and other devices update live. Signing out or losing connectivity just leaves the local copy in charge.

---

## Architecture

```
GitHub Pages (static hosting, this repo, main branch)
    ├── returnradar.html — single file, zero dependencies
    │       └── all state ──► browser localStorage (per device)
    └── cpap-tracker.html — single file; loads the Firebase SDK from gstatic.com
            ├── all state ──► browser localStorage (source of truth, works offline)
            └── signed in ──► Firestore doc paptrack/{uid} (last-write-wins by
                              updatedAt; live onSnapshot updates on other devices)

Both apps: backup/restore via JSON export & import.
```

There is no server of our own — the only backend is PAPTrack's optional Firebase (auth + one Firestore document per user, free tier). Each prototype must still work from a double-clicked file just as well as from GitHub Pages; PAPTrack degrades to fully-local mode when Firebase is unreachable or the user is signed out.

---

## Adding a prototype

1. Drop a new self-contained `*.html` file in the repo root (link `favicon.ico` in its `<head>`)
2. Add it to the list at the top of this README
3. Push — GitHub Pages serves it at `eagleadams86.github.io/prototypes/<file>.html`
