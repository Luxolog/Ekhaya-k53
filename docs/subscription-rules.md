# Ekhaya K53 — Subscription and Test Allowance Rules

## Proposed plans

| Plan | Price | Completed tests |
| --- | ---: | ---: |
| Free | R0 | 3 once-off free tests per account |
| Premium | R29.99/month | 20 per subscription billing period |
| Premium Plus | R49.99/month | Unlimited |

These are proposed launch values. Configure products and prices in Google Play Console only after commercial approval and price validation.

## Definition of a completed test

A test is completed when the learner submits a valid, final answer set and the backend accepts and records that submission. Merely launching a test, viewing questions, leaving a test unfinished, or losing connectivity must not consume a test credit.

## Free entitlement

- Three accepted completed submissions per learner account, once-off.
- The count does not reset monthly.
- The count survives app deletion, reinstall, device replacement and logout/login.
- Multiple devices signed into the same account share the same allowance.
- Do not use device identifiers as the primary identity or entitlement key.

## Paid entitlements

- Premium quota: 20 accepted completed submissions during the active subscription billing period.
- Premium Plus: no monthly completed-test cap while the subscription entitlement is active.
- Usage periods follow verified subscription period boundaries from Google Play, not an assumed calendar month.
- Cancellation generally leaves access available until the verified paid-through/expiry time.
- Grace period, account hold, pause, refund and revocation follow verified Play subscription state and configured product policy.
- On expiry or revocation, paid entitlements are removed; completed history remains available to the learner.

## Backend enforcement

The backend is authoritative. Client-side counters are display hints only.

For each accepted submission, perform entitlement validation, quota check, unique session/idempotency check, scoring and usage increment as one transaction. Concurrent requests must not allow more submissions than the remaining quota.

Store an immutable event for each accepted submission and each entitlement transition. A support adjustment must be auditable and must not silently delete historical use.

## User-facing disclosures

Before purchase, disclose:
- product name,
- price and billing frequency,
- number of tests included (or unlimited),
- automatic renewal,
- how to cancel,
- when paid access ends after cancellation,
- any trial/introductory offer terms.

The app must provide a way to manage or restore purchases where appropriate.

## Abuse controls

- Require account authentication before free-test entitlement is allocated.
- Apply rate limits to account creation, login, test-session creation and submissions.
- Use server-generated session IDs and server-side time/expiry.
- Make final submission idempotent.
- Detect repeated suspicious account creation without invasive device fingerprinting.
- Provide a user-friendly recovery path for network failures and duplicate submission attempts.
