# Private Sharing validation · 15 November 2026

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

Verification limits: the empty Supabase schema and anonymous denials are verified;
real OAuth callbacks, invitations and iPhone two-account sessions are still unconfigured. Browser UI assertions
use jsdom; no real Safari visual/device test was available in this environment.
The live backend remains an approval-gated setup step. Passing local tests does not
mean cloud configuration has been deployed or that sharing has been activated.

## Approved empty backend setup

Supabase project bgobhswgfxydjdyaxcwe was created in the selected Free organization after approval and a
$0/month quote. The checked migration is installed. Verified sharing_enabled=false,
all three scope flags=false, owner_count=0, summary_count=0, viewer_count=0 and
invitation_count=0. All three tables have RLS. Anonymous RPC privileges=0 and
public SECURITY DEFINER sharing functions=0. Actual anonymous HTTP checks for
role/status/view return 401; the private Data API schema returns 406.

Additional local PostgreSQL tests verify private implementations/public invoker
wrappers and rejection of revoked/expired authentication sessions.

Only INFO advisor notices remain for intentional default-deny RLS without table
policies: https://supabase.com/docs/guides/database/database-linter?lint=0008_rls_enabled_no_policy

GitHub OAuth is not yet enabled. Owner binding and real device/two-account OAuth
acceptance are still pending. No fitness data or invitations were created.
