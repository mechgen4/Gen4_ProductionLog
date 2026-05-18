# Gen4 Portal – Migration Progress

## Task
Migrate `gen4_daily_log.html` from **localStorage** to **Firebase Firestore** — same setup as `index.html`.

## Context
- `index.html` = main production app, already on Firebase (Flyer Frame + Draw Frame)
- `gen4_daily_log.html` = older version, Flyer Frame only, still using localStorage
- Both files are in `D:\Bharath\`

## Firebase Config (from index.html)
```js
const firebaseConfig = {
  apiKey:            "AIzaSyDcTMmXR05nBxD1k3o9SGW6T3nGfQHDN7o",
  authDomain:        "gen4-productionlog.firebaseapp.com",
  projectId:         "gen4-productionlog",
  storageBucket:     "gen4-productionlog.firebasestorage.app",
  messagingSenderId: "754999209069",
  appId:             "1:754999209069:web:e9a80b061b6a427eb3a364"
};
```
- SDK version: `firebase 10.12.0`
- Flyer collection: `flyer_logs`
- Draw collection: `draw_logs`

## Migration Steps for gen4_daily_log.html
1. Change `<script>` → `<script type="module">`
2. Add Firebase imports (app + firestore)
3. Init Firebase app + Firestore + `flyerCol = collection(db, 'flyer_logs')`
4. Replace `loadEntries()` / `saveEntries()` with in-memory `flyerEntries[]` array
5. `DOMContentLoaded` → start `onSnapshot` real-time listener
6. `saveEntry()` → async `addDoc(flyerCol, {...})`
7. `deleteEntry(id)` → async `deleteDoc(doc(db,'flyer_logs',id))`
8. `clearAll()` → async batch `getDocs` + `deleteDoc`
9. `updateDashboard()` → reads from `flyerEntries[]` (in-memory, kept fresh by snapshot)
10. All event-handler functions exposed via `window.funcName = ...`

## Status
- [x] Migration of gen4_daily_log.html — DONE (2026-05-18)

## What was changed in gen4_daily_log.html
- `<script>` → `<script type="module">`
- Added Firebase 10.12.0 imports (app + firestore)
- Replaced localStorage STORE_KEY with Firestore `flyer_logs` collection + real-time `onSnapshot`
- All event-handler functions now on `window.*` (required for inline onclick in module scripts)
- `saveEntry()` → async `addDoc`
- `deleteEntry(id)` → async `deleteDoc` (id is now a Firestore string, not a numeric timestamp)
- `clearAll()` → async batch getDocs + deleteDoc
- `updateDashboard()` reads from in-memory `flyerEntries[]` array (kept live by snapshot)

## Live App
https://mechgen4.github.io/Gen4_ProductionLog/?v=2
GitHub repo: https://github.com/mechgen4/Gen4_ProductionLog

## Next Steps (if any)
- Verify it syncs correctly with index.html (both use same flyer_logs collection)
- Consider adding Draw Frame section to gen4_daily_log.html if needed
