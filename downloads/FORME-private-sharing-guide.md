# FORME · Private Sharing

## Current setup: empty free backend ready; sharing OFF

Following Owner approval, a dedicated **forme-private-sharing** project was created
in **your selected Free organization**, with a verified quote of **$0/month**, in Mumbai (ap-south-1).
The migration is installed. Sharing and all scopes are disabled; the Owner binding,
Viewer binding, invitations and shared summaries are empty. No fitness records,
profile JSON or tracking history were uploaded.

The API URL is `https://bgobhswgfxydjdyaxcwe.supabase.co`. Only the intentionally
public publishable connection key is included in the app. Connection form values
are prefilled for convenience but not saved automatically. No sign-in or fitness
upload happens just by loading the app or previewing selections.

**Remaining:** configure GitHub OAuth, have the Owner sign in, verify and bind the
sole Owner UUID, and complete synthetic two-account/iPhone acceptance checks.
Enabling real sharing and the first fitness upload still require separate approval.
Your full record remains in the existing on-device IndexedDB.

## Snapshot versus live sharing

|                     | Manually sent snapshot                                           | Authenticated live viewer                                            |
| ------------------- | ---------------------------------------------------------------- | -------------------------------------------------------------------- |
| Cost                | Can be completely free                                           | Designed for Supabase Free + existing free GitHub Pages              |
| Updates             | Fixed copy; send another to update                               | Owner uploads selected summaries while the app is open               |
| Privacy             | Encrypt before sending; recipient controls their downloaded copy | HTTPS, verified sign-in, backend authorization and RLS               |
| Revocation          | Cannot recall a downloaded/decrypted copy                        | Disables future backend reads and deletes the current shared summary |
| Backend permissions | No backend exists for a file                                     | Backend enforces the sole Owner and one read-only Viewer             |
| Offline             | Recipient can keep a local copy                                  | Viewer must be online to verify permission                           |

A snapshot cannot meet the requested revocation and backend-permission guarantees,
so this feature implements the live route and explains the snapshot alternative.
It does not expose a public fitness snapshot or a public JSON export URL. Your full
backup remains an Owner-only local download and is never an invitation artifact.

Revocation cannot prevent screenshots or recall information that has already been
seen. A request already authorized before revocation can complete. Subsequent reads
are denied. Viewer pages hide data when backgrounded, offline, or when a refresh
fails; they poll permission every 15 seconds. Authenticated responses use no-store.
Viewer health data is kept in memory, not IndexedDB/localStorage/Cache Storage.

## Secure free-tier architecture

- Existing GitHub Pages serves only application code, assets, and safe source ZIPs.
  No fitness JSON or Owner profile is published in that repository.
- A **dedicated Supabase Free project** supplies Auth and PostgreSQL/PostgREST RPCs.
  The currently listed plan is $0, with 500 MB of database storage and 50,000 monthly
  active users; free projects may pause after one week of inactivity. Select Free,
  do not add billing information, do not upgrade, and stop if the provider requires
  a paid plan. Recheck the current terms at https://supabase.com/pricing.
- Owner and Viewer use Supabase Auth's GitHub OAuth provider. Each needs a free
  personal GitHub account. This avoids relying on a paid SMTP provider or the
  restricted default email sender. The OAuth app requests identity access only;
  no repository collaborator, GitHub organization role, or Supabase dashboard
  access is granted to the Viewer.
- The Owner is an immutable singleton row bound by project-admin SQL to the exact
  verified Supabase Auth UUID. The API has **no create/change Owner function**.
  Signing up, changing user metadata or visiting the Owner UI cannot promote a
  Viewer to Owner. You keep the cloud project's administrator login yourself.
- A singleton access record permits exactly one Viewer binding. Replacing the
  invitation immediately removes the previous Viewer. Invitations expire in 24
  hours, contain 256 bits of random entropy, and are single use. The backend stores
  only the SHA-256 digest and requires the invited person's exact GitHub numeric
  provider identity, verified in auth.identities, not user-editable metadata.
- The invitation URL contains only public connection configuration and the opaque
  invitation token in the URL fragment. It contains **no fitness information**.
  Opening the link alone grants no read access. The app removes the fragment,
  retains the pending invitation in sessionStorage only to finish sign-in, then
  deletes the pending token after successful acceptance.
- Only HTTPS Supabase project URLs and publishable/anon keys are accepted. Those
  keys are designed to be public; they are not permissions. Never put service-role
  keys, secret keys, database passwords or GitHub OAuth client secrets in the app,
  repository, invitation or chat.
- All tables live in a non-exposed `forme_private` schema. Direct access is revoked
  from public/anon/authenticated, and RLS is enabled with no permissive table policy.
  Narrow public SECURITY INVOKER RPC wrappers delegate to checked SECURITY DEFINER
  implementations in the unexposed private schema. They check permissions inside
  PostgreSQL with locked rows and a fixed search_path. Every operation verifies
  the authenticated session against auth.sessions, so revoked/expired sessions
  cannot rely on an otherwise unexpired JWT. Anonymous RPCs are denied.
- The API validates and reconstructs every allowed field. Unknown fields, including
  notes or weights smuggled into nested records, are rejected. An unapproved scope
  cannot be published even if the client is modified.
- Upload approval is bound locally to the precise project, Owner identity and
  selected scopes. A backend response cannot broaden that local consent. Viewer
  connection settings cannot replace the Owner's upload destination.
- Revocation clears the Viewer identity, invitation and shared summary atomically.
  Disabling deletes the summary and denies reads; re-enabling can resume the same
  previously accepted Viewer. **Revoke** removes the binding entirely and requires
  a fresh invitation, even if sharing is subsequently re-enabled.

Supabase's project administrator and the provider infrastructure necessarily have
access to plaintext selected summaries; this is authenticated sharing, not
end-to-end encryption. Only approved fitness summaries go there. Full Owner data
is never migrated into the backend. OAuth identity data is processed by GitHub and
Supabase when the user explicitly signs in.

Official references:

- https://supabase.com/docs/guides/auth/social-login/auth-github
- https://supabase.com/docs/guides/database/postgres/row-level-security
- https://supabase.com/pricing

## Exact visible fields

Every scope starts off. Selected workout titles and exercise names are shared text,
so review the exact preview and avoid putting private notes in those names.

**Workout schedule:** weekday, title, start time, duration, exercise names, number
of sets and target reps. No load, personal notes, profile or class notes.

**Completed workouts:** last 100 completed sessions, each with date, workout title,
rounded training minutes and completed-set count. Active/unfinished sessions,
private session IDs, notes, exercise loads and individual weight/reps histories are
excluded.

**Activity charts:** last 28 local calendar dates, each with number of completed
workouts, total training minutes and completed-set count. No strength-load, body
weight, nutrition/protein, hydration, measurement, class or sleep charts are shared.
Charts are derived only from actual completed sessions; no demo history is seeded.

**Never uploaded by this feature:** profile/name/height/starting weight, dietary
preferences, nutrition, hydration, body measurements, sleep, private notes, active
workout state, workout loads, backups and full IndexedDB state.

## Safe setup plan — do not activate sharing yet

Steps 1–4 are completed for this project. OAuth, Owner binding and live acceptance are still pending.
This remains optional; the rest of FORME works without any cloud project.

1. **Approve the architecture first.** Confirm that selected summaries may be stored
   with Supabase Free and that both people can use a free GitHub login. No health
   upload or invitation is needed just to review this proposal.
2. On the iPhone, export a fresh **More → Backup → Export JSON backup** and save it
   in Files. Keep using the same `https://lujainaljabri.github.io/forme/` address and
   Home Screen installation. No IndexedDB version change or local migration is
   required; existing history is untouched.
3. After approval, create a dedicated Supabase organization/project on **Free**.
   Keep its dashboard/admin credentials private. Do not invite the Viewer into it.
   Do not attach a card or select a paid plan. Never disable security to make setup
   easier.
4. In that project’s SQL Editor run **backend/sharing.sql** once, in the dedicated
   empty project. Do **not** run test-bootstrap.sql or permissions.test.sql there;
   those are only for disposable synthetic local tests. The migration creates no
   personal fitness data and starts sharing disabled. This migration is already
   installed in the approved project; do not run it a second time.
5. Create a GitHub OAuth App under your own GitHub developer settings, using the
   callback URL shown by the project's GitHub Auth provider:
   `https://bgobhswgfxydjdyaxcwe.supabase.co/auth/v1/callback`.
   Configure its client ID and client secret in **Supabase Auth → Providers →
   GitHub**, never in FORME. Use identity access only, not repository permissions.
6. In Supabase Auth URL configuration set Site URL to
   `https://lujainaljabri.github.io/forme/` and add these exact redirect URLs:
   `https://lujainaljabri.github.io/forme/?sharing=1` and
   `https://lujainaljabri.github.io/forme/?view=1`.
   Keep `forme_private` outside the exposed API schemas (public only is sufficient).
7. In **More → Private Sharing**, enter only the project HTTPS URL and its
   publishable/anon key, then choose **Save approved connection**. This permits
   sign-in; it does not upload any fitness records. Sign in with your GitHub account.
8. Before Owner bootstrap, the page correctly says **No Owner permission** and
   displays your Auth user UUID. Verify that UUID against the corresponding user
   in Supabase Auth's Users page. With project-admin SQL, bind only that user:

   ```sql
   insert into forme_private.owner(singleton,user_id)
   values(true,'YOUR-VERIFIED-AUTH-USER-UUID');
   ```

   Run this once. Refresh the sharing page. Its role must say **Sole Owner**.
   Do not insert another Owner, edit ownership, or accept a Viewer as an admin.

9. Select only the desired scopes. Open **Preview exact shared fields** and review
   every record. No network upload occurs from ticking a checkbox or previewing.
10. Only when ready, choose **Approve & enable selected sharing** and confirm the
    upload dialog. The first upload creates a **separate summary**. IndexedDB stays
    authoritative, and it is never replaced, cleared or transformed.
11. Enter your trusted person’s GitHub username. The app resolves it to a stable
    numeric identity and asks you to confirm that exact account. Choose **Create
    private invitation**, copy the link and send it yourself. It expires after 24
    hours. The agent does not send messages to your trusted person.
12. The person opens the link in Safari, signs into that exact GitHub account and
    gets only the read-only Viewer Dashboard. Sign-in is required even with the
    link; no health data can be read by anonymous visitors.
13. Complete the acceptance checks below before relying on live sharing. You can
    deselect scopes and **Apply permissions & sync**, **Disable sharing**, or
    **Revoke access & delete shared copy** at any time while online.

## iPhone sign-in acceptance check

OAuth redirects must return to the same browser storage context that began sign-in.
iOS can open external sign-in pages in Safari separately from an installed Home
Screen app. The real iPhone flow is an explicit acceptance test, not something this
release claims to have verified. If sign-in returns to a different context or fails
the PKCE exchange, stop and keep sharing off; do not clear website data or move your
full history to fix it. We must verify sign-in in the intended Safari/Home Screen
context with synthetic records before uploading real summaries. Owner data is never
automatically copied between those contexts, and the existing PWA remains usable.

## Sync, offline behavior, and rollback

The Owner's full records stay local. After explicit consent, only selected summaries
are periodically published while the Owner app is open (every 30 seconds), on
returning to the foreground, or via **Sync now**. iPhone background suspension
means this is not a continuously running server-to-server sync. The Viewer polls
when open and shows the latest successful upload time. If the Owner app is closed
or offline, the backend retains its last selected summary until a successful sync,
disable, or revoke.

Scope changes clear the previous summary on the backend before publishing the new
one. Changing the upload project requires new on-device consent. Device changes or
cleared localStorage require reconnecting and re-approving that device. Importing
an Owner backup does not import credentials or upload consent.

Revocation requires a successful network request. A failed/offline revoke is not a
successful revoke; the interface reports failure and backend permissions remain
unchanged until retried. For an emergency, the Owner can disable the project or
run `select public.forme_revoke();` with the Owner's authenticated session (or
project-admin SQL can clear access/delete the summary directly).

Deleting local app data, signing out, clearing browser storage or removing the Home
Screen icon is **not** backend revocation. Revoke first if you want the shared copy
removed. For rollback, revoke access and remove the optional backend connection;
continue using the original local tracking features. No cloud import is necessary.
Do not restore an old static build while active sharing remains enabled: revoke
before reverting so an old backend summary does not remain available.

## Verification and acceptance

Run locally:

```sh
npm ci
npm test
npm run test:backend  # needs local Docker; uses isolated PostgreSQL 17 and synthetic data
npm run build
```

The backend test runner creates and removes its own network-isolated disposable
PostgreSQL container. It exercises actual SQL permissions and RLS, including:

- authenticated Viewer denial for publish/configure/invite/revoke/Owner status;
- all direct table reads/writes denied;
- anonymous denial for every RPC;
- wrong identity/token, forged user metadata, consumed invitations and revoked
  tokens cannot claim access;
- only one Viewer can be bound, and replacing one denies the previous Viewer;
- unselected scopes and private/extra nested fields cannot be uploaded/read;
- narrowing scopes clears stale data;
- disable/revoke blocks subsequent reads and deletes shared data;
- RLS still denies rows even with accidentally granted table-level privileges.

Front-end tests cover default-off/no-network behavior, every scope combination,
private-data stripping, exact-project/identity/scope consent, separated Viewer
configuration, a read-only Viewer without Owner IndexedDB access, and fail-closed
behavior after denied refresh. Existing tracker/backup/reopen tests still run.

These tests do not impersonate a real Supabase OAuth service and do not replace a
live two-account acceptance test. The empty live project and its default-deny API have been verified. Real OAuth
callbacks, invitations and device sharing remain unconfigured and unverified.
After approval/configuration, verify with synthetic non-private records first:

1. Anonymous access is denied, including direct RPC calls with just the anon key.
2. A second uninvited GitHub account and a forwarded invitation fail.
3. The invited account sees only selected schedule/activity fields and no edit UI.
4. Viewer attempts to call Owner RPCs fail at the backend.
5. Removing a scope makes it unavailable on the next read.
6. Disabling and revoking block new reads; revoked tokens cannot be reused.
7. Reopen the Owner's original Home Screen app and verify its existing history,
   active session, meals and sleep are unchanged. Export another local backup.

Do not turn on sharing or upload real data until these checks and the Owner's
explicit approval are complete.

## Verified backend checks

Live anonymous RPC calls to forme_role, forme_owner_status and forme_view return 401. Requests for the private schema through the Data API return 406 (unexposed).
All three tables have RLS enabled, no anonymous sharing RPC has EXECUTE, and no
public sharing function is SECURITY DEFINER. The only advisor notices are INFO
for RLS enabled without policies: direct table access is intentionally default-deny;
checked private implementations are the only data access path.

Advisor explanation: https://supabase.com/docs/guides/database/database-linter?lint=0008_rls_enabled_no_policy
