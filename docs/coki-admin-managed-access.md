# Coki Driving Academy — Admin-Managed Learner Access

## Scope

Add a Coki administrator dashboard connected to the Ekhaya K53 learner application. Payments are offline for this first release. Coki staff confirm payment and manually provision or activate learner access. Do not add an online payment gateway in this phase.

This feature must be developed in the Ekhaya-k53 repository only. Do not modify or depend at runtime on the Inganathi Driving Academy project.

## Roles

- **Coki administrator:** Authenticated, allow-listed staff member. Can create and manage learner accounts, assign daily study allowance and access dates, reset credentials, suspend/reactivate accounts, and review activity.
- **Learner:** Can sign in to the K53 app and access learning features only while the account is active, within its access period, and with daily time remaining.

Never expose administrator functions to unauthenticated users or learners. Do not rely on a client-side role flag.

## Administrator workflow

1. Staff sign in to the protected admin dashboard.
2. Staff create a learner record using the learner's name and contact details.
3. The system provisions a unique username and a secure temporary credential (or sends a password setup/reset link; preferred where email delivery is available).
4. Staff mark offline payment as confirmed and assign access.
5. Staff set:
   - daily allowance in minutes (for example 60, 120, 180; configurable),
   - access start timestamp,
   - access expiry timestamp,
   - account status (active/suspended).
6. Learner signs in and sees the allocated allowance and time remaining.
7. Admin can amend the allocation, extend expiry, suspend/reactivate, or reset access. Every administrative change is recorded in an audit log.

Credentials must not be stored or displayed as plaintext after initial issuance. Prefer a one-time password setup link or generated temporary password requiring change at first sign-in. Never commit credentials, API keys, service-role keys, or secrets to Git.

## Daily study timer rules

- Store the entitlement and daily allowance in the backend; backend state is authoritative.
- Use Africa/Johannesburg (SAST) for the daily boundary. Reset at local midnight unless the product configuration explicitly changes this.
- Count active foreground study time only. Study content, practice tests, and answer review count. Backgrounded, idle, logged-out, or disconnected sessions pause or stop according to the session protocol.
- The app requests a server-authorized study session. The server returns a session identifier and authoritative remaining allowance.
- The client periodically reports activity/heartbeat; the backend validates session ownership and caps elapsed time. Do not trust client clock or client-submitted elapsed totals.
- Close/expire sessions on logout, background/idle timeout, or allowance exhaustion.
- Multiple devices/sessions must not spend the same remaining allowance concurrently. Enforce with transactional backend logic / row locking.
- When the allowance reaches zero, lock protected learning content and show the next reset time. At the next SAST day, replenish up to the assigned daily allowance if the account remains active and in its access window.
- Access expiry and suspension override remaining daily time.
- Admin adjustments must take effect on the next server authorization/check.

## Admin dashboard views

- Overview: active, suspended, and expired learners; today's active study; expiring access.
- Learners table: name, username, contact, status, daily minutes, remaining time today, access start/end, last login.
- Create/edit learner.
- Access allocation panel.
- Activity/audit history.
- Search and filters.
- Clear confirmation for suspension, expiry extension, and credential reset.

## Suggested data model

Use backend-managed authentication identities, plus relational records:

- `profiles`: auth user ID, display name, username, role, created timestamp.
- `learner_access`: learner ID, status, daily allowance minutes, start/end timestamps, offline payment confirmation metadata (no sensitive payment details).
- `study_sessions`: learner ID, server start/end, last heartbeat, session status, charged seconds, SAST study date.
- `daily_usage`: learner ID + SAST date unique key, used seconds, allowance snapshot; updated transactionally.
- `admin_audit_log`: actor ID, learner ID, action, before/after JSON, timestamp.
- Optional `learner_activity`: last login and learning progress summaries.

Apply row-level security. Learners can read only their own profile/access/usage and can never write entitlement, role, payment-confirmation, or usage totals directly. Admin writes must go through authenticated, server-side functions with role verification. Service credentials remain server-side.

## Acceptance criteria

- Admin-only dashboard routes are protected by backend role checks.
- Admin can provision learner account and set allowance/validity after offline payment.
- Learner can sign in and see correct assigned daily time and access expiry.
- Server enforces daily allowance; manipulating device time, browser storage, or client requests cannot grant extra time.
- Backgrounding/logout pauses charging; concurrent sessions cannot double-spend time.
- Usage resets at SAST midnight and does not reset on reinstall or device change.
- Suspended or expired accounts cannot start or continue study sessions.
- Admin changes and credential resets are auditable.
- No online payment processing is included in this phase.
- Existing K53 learning/test behavior is preserved; Inganathi remains untouched.

## Deployment notes

The repository holds application and backend migration/function source. A secure backend project (for example Supabase) must be provisioned and configured separately; GitHub Pages alone cannot provide private authentication, authoritative timers, or secure database enforcement. Document required environment variables by name only, and never commit their values.
