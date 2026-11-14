# Private Sharing validation · 14 November 2026

- Production TypeScript check and Vite/PWA build: passed.
- Front-end/data suite: 19 tests passed, including all original tracker tests.
- PostgreSQL 17 permission/privacy assertions: passed in a disposable,
  network-isolated Docker container with synthetic Auth users and fitness records.
- Original src/data.ts compared byte-for-byte against the previous release:
  unchanged. No IndexedDB schema, database name, record key or version migration.
- Default-off UI and exact selected-data preview issue no network requests.
- Public source/app archives exclude the Owner private profile/setup JSON.
- Viewer endpoint is an isolated app entry that does not read Owner IndexedDB.
- Viewer UI has no fitness mutation controls, clears data after denial/offline,
  and uses authenticated backend calls with cache disabled.
- Owner upload approval is tied to project, identity and scope; Viewer connection
  settings cannot overwrite Owner upload settings.
- Backend tests exercise RPC denials, direct access denials and RLS, unselected
  scopes, nested-field rejection, invitation identity/expiry/one-time use,
  forged metadata, disable, revoke and one-Viewer replacement.

Verification limits: no live Supabase project, real OAuth callback, real invitation
or iPhone two-account session has been configured or tested. Browser UI assertions
use jsdom; no real Safari visual/device test was available in this environment.
The live backend remains an approval-gated setup step. Passing local tests does not
mean cloud configuration has been deployed or that sharing has been activated.
