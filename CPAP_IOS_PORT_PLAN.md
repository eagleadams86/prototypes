# iOS Port Plan — PAPTrack (CPAP Supply Tracker)

*Written 2026-07-19 by Claude (Fable 5) after a full review of `cpap-tracker.html` (998
lines, all app logic — no bundled libraries). This is the blueprint for the native iPhone
version. It's written to be executed by a future Claude session with no memory of this one;
the HTML file remains the source of truth for behavior wherever this plan is silent.
Companion doc: `IOS_PORT_PLAN.md` in the lottery repo follows the same structure.*

---

## 1. Why this app wants to be native

Of all the prototypes, PAPTrack gains the most from going native — the entire product is
*reminders*, and the web version can only nag you if you open it:

- **Local notifications** replace "remember to check the page": cleaning due this morning,
  replacement coming up, spares low. This is the killer feature the web version can't have.
- **EventKit** (Apple Calendar/Reminders integration) replaces the `.ics` download dance —
  no more exporting files; the app writes reminders directly, with permission.
- Everything else (list UI, countdowns, steppers, filters) translates one-to-one.

**App shape:** single-screen list app with a detail/edit sheet — structurally much simpler
than the lottery port. One `NavigationStack`, no tabs needed (search, filter chips, and
stat tiles all live on the main screen, same as the web).

New Xcode project, SwiftUI, in a **new repo folder `~/claude-paptrack-ios`** (one folder
per repo, never in iCloud-synced paths). The web app stays as-is.

### Tech choices

- **SwiftUI**, minimum **iOS 17** (`@Observable` models).
- **No charts needed** — this app has none.
- **UserNotifications** framework for reminders; **EventKit** only if calendar entries are
  wanted beyond notifications (start with notifications; EventKit is a stretch goal).
- Dependencies: **none**, unless the Firebase sync decision (§4) says otherwise.

---

## 2. Data model (port exactly)

The web app stores one array under `localStorage['pap-items']`. Item fields:

```swift
struct SupplyItem: Codable, Identifiable {
    var id: String          // uid(): base36 timestamp + random — keep format for sync compat
    var emoji: String
    var name: String
    var cleanDays: Int      // 0 = no cleaning schedule
    var replaceDays: Int    // 0 = "as needed" (no countdown; reorder driven by stock only)
    var lastReplaced: String?   // ISO date "yyyy-MM-dd"
    var lastCleaned: String?    // ISO date or nil ("Not cleaned yet")
    var stock: Int          // spares on hand
    var lowAt: Int          // reorder threshold
    var note: String
    var createdAt: Double   // ms since epoch (JS Date.now()) — keep unit for sync compat
}
```

**Keep field names and formats identical to the web app** (ISO date strings, ms epoch,
base36 ids). This is not cosmetic — it's what makes Firestore sync compatibility (§4)
possible without a migration layer.

### Presets and kits (transcribe from `PRESETS` / `KITS` constants, lines ~427–441)

8 supply presets (Mask 1/90, Headgear 7/180, Tubing 7/90, Filters 0/14, Full Face Cushions
1/30, Nasal Cushions 1/14, Humidifier 1/180, Distilled Water 0/0 — cleanDays/replaceDays)
plus a custom template. Two one-tap kits: **full** and **nasal** (six items each). Filters
seed `stock: 4` (multipacks); everything else 0. Each preset carries its user-facing note
("Cannot be washed — replace only", etc.) — copy the strings verbatim.

### Business rules (the part to test)

- **Urgency badges**: red overdue, orange/amber approaching, green otherwise. Thresholds
  *scale with the item's cycle*: amber at ≤40% of cycle remaining, orange at ≤20%, capped
  at 14/7 days — so a 2-week consumable isn't "due soon" the day it's replaced. Port the
  `severity`/`soonAt` functions faithfully; pin unit tests to web-app outputs.
- **Sort**: most urgent first; as-needed items (replaceDays 0) always last.
- **Reorder flag**: spares ≤ `lowAt` **and** replacement due within 30 days (as-needed
  items: spares alone drive it).
- **"Replaced today"**: resets countdown *and* decrements stock by one.
- **"Mark all due items cleaned"**: bulk action for the daily routine.
- Stat tiles: clean-today count, next replacement (item + days), reorder count.
- Search + filter chips: All / Clean due / Replace due / Reorder / All good (tap active
  chip again → back to All).

---

## 3. Notifications (new capability — design, don't port)

The web app's `.ics` export exists because a web page can't schedule anything. Replace the
concept, don't translate it:

1. **Daily cleaning digest** — one notification at a user-chosen time (default 8:00 AM):
   "3 items due for cleaning today: Mask, Cushions, Humidifier." Skipped when nothing is due.
2. **Replacement alerts** — per item, at 2 days before due and on the due date (mirrors the
   web's ICS alarm lead).
3. **Reorder alerts** — when an item newly crosses into reorder state, fire once (don't
   re-nag daily; badge count on the app icon can carry the standing state).

All local (`UNUserNotificationCenter`) — no server, no push certificates. Rebuild the
pending-notification schedule from scratch after every data mutation (simpler and more
reliable than diffing). Settings toggle per category. **EventKit calendar entries are a
stretch goal** only if notifications prove insufficient — likely they won't be.

---

## 4. The one big decision: sync (user call, decide at build time)

The web app has optional **Google sign-in + Firestore** sync (project `paptrack-6c817`,
doc per user at `paptrack/{uid}`, whole-document last-write-wins via a `pap-updated`
timestamp). Two honest options:

| Option | How | Trade-offs |
|---|---|---|
| **A. Keep Firestore** (recommended if the web version stays in use) | Firebase iOS SDK via Swift Package Manager + Google Sign-In. Read/write the *same* document with the same shape and timestamp rule | Web + computer + phone all share one dataset, existing data appears on day one. Cost: two real dependencies, a sign-in flow, and Firebase console config (add the iOS app to the project) |
| **B. Switch to iCloud (CloudKit or `NSUbiquitousKeyValueStore`)** | Data syncs across the user's Apple devices with zero sign-in UI | Simpler, dependency-free, invisible. But the web version stops sharing data — it becomes standalone or retired |

Recommendation: **A if the browser version (especially on the computer) remains part of the
routine; B if the iPhone app becomes the only daily surface.** Either way, phase 1–3 below
build the app fully local-first (the web app is local-first too — sync bolts on at the end,
exactly as it did on the web). A JSON import matching the web app's backup-export format is
the fallback bridge if B is chosen: export from the web page once, import into the app, done.

---

## 5. Themes

Same 7-palette family as the lottery apps — but note this app's set is **Forest, Light,
Midnight, Sepia, Slate, Solarized, Synthwave** (Slate instead of the lottery's Dark).
Transcribe the hex values from the `<style>` block of `cpap-tracker.html` (self-contained;
it does *not* link the lottery `theme.css`). Default **Midnight**, stored under `pap-theme`,
same `Theme` struct approach as the lottery plan §6. No system light/dark following —
themes are an explicit choice.

---

## 6. Architecture

```
PAPTrack/
├── App/
│   ├── PAPTrackApp.swift        @main, theme injection
│   └── Theme.swift              7 palettes
├── Engine/                      ← pure logic, unit-tested, no UI
│   ├── SupplyItem.swift         model + Codable (web-compatible shapes)
│   ├── Presets.swift            PRESETS + KITS transcription
│   ├── Schedule.swift           severity, soonAt, nextClean/Replace/Reorder, date math
│   └── Store.swift              load/save (UserDefaults key "pap-items"), JSON export/import
├── Notifications/
│   └── ReminderScheduler.swift  rebuild-all-pending on every mutation
├── Sync/                        ← phase 5 only, per the §4 decision
│   └── (FirestoreSync.swift | CloudKitSync.swift)
└── Views/
    ├── MainListView.swift       stat tiles, search, filter chips, sorted cards
    ├── ItemCardView.swift       badges, countdowns, steppers, Mark cleaned / Replaced today
    ├── EditItemSheet.swift      add/edit form, preset picker, As-needed chip
    └── SettingsSheet.swift      theme, notification times/toggles, backup, sync sign-in
```

## 7. Build order (each phase ends runnable)

1. **Scaffold + themes** — project, theme system, empty list shell.
2. **Engine + tests** — model, presets, all schedule math with tests pinned to web outputs
   (set up the same items in the web app, compare every badge/date/countdown).
3. **Main UI** — full list experience: kits, cards, steppers, search, chips, edit sheet,
   stat tiles. At the end of this phase the app has feature parity with the web *minus*
   ICS and sync. Runs entirely in the simulator.
4. **Notifications** — the digest + alerts of §3. Needs a real device to feel out timing.
5. **Sync** — implement §4's chosen option (or defer; the app is fully usable without it).
6. **Polish** — app icon (Midnight palette), badge counts, JSON backup import/export,
   footer resupply tip text.

## 8. Distribution + fidelity notes

Same distribution reality as the lottery plan §8: free sideload = 7-day expiry, $99/yr =
TestFlight/App Store. **App Review note:** PAPTrack tracks *supplies*, not health data —
don't integrate HealthKit, don't claim medical benefit, and review guideline 5.1.3 (health
data) stays out of scope. The footer insurance tip is informational and fine.

- The HTML is the spec; read `severity`, `nextClean`/`nextReplace`/`reorderNeeded`, and
  `applyPreset` before writing Swift.
- Dates: the web app compares ISO date *strings* and day-counts in local time — keep the
  same day-granularity semantics (`Calendar.current` startOfDay), no time-of-day math.
- "Ago" labels: today / yesterday / N days ago / "Not cleaned yet" — copy `agoLabel`.
- Drop from the port: the "Recent changes" GitHub-commits section (web-only feature).
- Keep engine types value-semantic and `Codable` so a future widget ("next replacement in
  N days") reuses them directly.
- Commit style in the new repo: plain-English subjects, same convention as this repo.
