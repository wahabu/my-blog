# Strapi Admin UI Crash: "Cannot read properties of undefined (reading 'tours')"

## Problem Overview
During admin panel boot, the application crashes with the following error:
`Cannot read properties of undefined (reading 'tours')`

## Technical Root Cause
The Strapi Admin Guided Tour feature attempts to read state from an unmounted or uninitialized store slice (`admin_app.tours`). This is an admin panel hydration and store initialization failure, typically triggered by:
- Stale build artifacts inside `.cache` or `build`.
- Version drift across `@strapi/*` packages.
- Stale UI/tour state persisted in client-side storage.
- Custom admin extensions (`src/admin/app.js` / `app.tsx`) throwing early during bootstrap and halting reducer registration.

---

## Recovery & Troubleshooting Procedure

### Phase 1: Client-Side Cache Reset
1. Open DevTools (`Ctrl + Shift + I` / `F12`) -> **Application** -> **Storage**.
2. Select the Strapi origin (`http://localhost:1337`) and clear:
   - `STRAPI_GUIDED_TOUR`
   - `guided_tour_state`
   - LocalStorage and SessionStorage entirely.
3. Perform a hard refresh (`Ctrl + Shift + R`).

### Phase 2: Clean Rebuild Artifacts
Wipe transient build files and recompile the admin panel:

```bash
# Stop development server, then run:
rm -rf .cache build
npm run build
npm run develop
