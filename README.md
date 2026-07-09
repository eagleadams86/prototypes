# Claude Prototypes

Self-contained, no-server web app prototypes. Each app is a single HTML file — open it directly in a browser (`file://` works) or use the GitHub Pages links below. No build step, no dependencies, no accounts.

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
- **Replacement countdowns** — every item shows its next due date with urgency-colored badges (overdue red, ≤7 days orange, ≤14 days amber, otherwise green); the list sorts most-urgent first
- **Cleaning tracker** — items show "Cleaning due" or "Cleaned today/yesterday/N days ago"; one-tap **Mark cleaned**, plus a **Mark all due items cleaned** button for the daily routine
- **Inventory & reorder** — a spares counter with −/+ steppers on every card; items are flagged **Reorder** when spares fall to the item's threshold and a replacement is due within 30 days. **Replaced today** resets the countdown and consumes a spare automatically
- **Stat tiles** — items to clean today, next replacement (item + days), and how many supplies need reordering
- **Search & filters** — live search plus chips: All, 🧼 Clean due, ⏰ Replace soon, 📦 Reorder, ✓ All good
- **Calendar reminders** — one-tap `.ics` export of the next replacement date with an alarm 2 days before
- **Resupply tip** — footer note about insurance PAP-adherence requirements and humidifier care (distilled water, empty every morning)
- **Private & offline** — all data in `localStorage`; JSON backup export/import; no account, no server
- **Midnight theme by default** — the shared deep indigo/navy palette, with a 🌗 toggle to a light theme; choice applied before first paint

---

## Architecture

```
GitHub Pages (static hosting, this repo, main branch)
    ├── returnradar.html ─┐
    └── cpap-tracker.html ─┴─ single file each: HTML + CSS + JS, zero dependencies
            └── all state ──► browser localStorage (per device)
                              backup/restore via JSON export & import
```

There is intentionally no backend. Each prototype must run from a double-clicked file just as well as from GitHub Pages.

---

## Adding a prototype

1. Drop a new self-contained `*.html` file in the repo root (link `favicon.ico` in its `<head>`)
2. Add it to the list at the top of this README
3. Push — GitHub Pages serves it at `eagleadams86.github.io/prototypes/<file>.html`
