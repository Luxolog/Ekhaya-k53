# Ekhaya K53 — Implementation Plan

## 1. Product scope

Build an independent Android application for South African learner's licence preparation under the Ekhaya Technologies brand. Reuse the existing K53 learning flow and original/appropriately licensed learning assets from the Inganathi K53 project as source material, but do not depend on or modify the Inganathi live website or repository at runtime.

The first release focuses on learner-licence practice. Additional Ekhaya education products are out of scope for the MVP.

## 2. Product rules

- New learner accounts receive three free completed practice tests.
- Starting, opening, abandoning or crashing during a test does not consume an allowance.
- A test is counted only after the backend validates and records its final submission.
- Free allowance is associated with the learner account, not the device, and cannot be reset by reinstalling the app.
- Proposed Premium: R29.99 per month, 20 completed tests per billing period.
- Proposed Premium Plus: R49.99 per month, unlimited completed tests.
- Prices and plan quotas are proposed launch configuration, not final until Play Console product setup and commercial review.
- Allowance resets according to the subscription billing period; never reset simply because the calendar month changes unless that is the configured product rule.
- Subscription entitlements are verified server-side using Google Play purchase/subscription data.
- Restore purchases and account/device changes must not erase valid entitlements or progress.
- Clearly disclose auto-renewal, price, billing period, cancellation and trial/offer terms before purchase.

## 3. Architecture

### Android client
- Unique package ID under Ekhaya Technologies namespace (select final reverse-domain identifier before first release).
- Learner onboarding, sign-in, dashboard, study mode, timed test player, results, mistake review, progress, plan screen and account settings.
- Capacitor is the initial candidate if the reusable K53 experience is web-based and compatible. Validate before committing; retain the option to use native Kotlin modules for billing and device features.
- Keep learning UI/content available locally where licensing and entitlement rules permit.
- Never treat local counters or client-side premium flags as authoritative.

### Backend
- Account authentication and identity.
- Authoritative test-session creation and submission.
- Atomic entitlement checks and completed-test accounting.
- Subscription purchase-token verification and lifecycle updates.
- Learner history, progress and content version management.
- Administrative question/content management.
- Audit trail for purchases, entitlement changes and test submissions.

### Data entities
- users
- question_sets / content_versions
- questions / answer_options / explanations / media
- test_sessions
- test_submissions
- test_answers
- subscription_products
- subscription_entitlements
- billing_events
- learner_progress

Use stable IDs for questions and content versions. Never rely on question array position as an identifier.

## 4. Test accounting and anti-abuse requirements

1. Authenticate the learner before issuing a test session.
2. The backend checks available entitlement and creates a time-limited test session.
3. A session has a unique ID and server-controlled status: issued, in_progress, submitted, expired or voided.
4. Submission must be idempotent. A retry for the same session cannot consume another test.
5. Backend validates session ownership, question set/version, answers, timing rules and session state.
6. The backend records one completed test only on the first valid submission.
7. Abandoned or expired sessions do not consume an allowance; suspicious repeated session creation may be rate-limited.
8. Free-test count and paid quota are computed from authoritative records.
9. Concurrent submissions must not overspend the remaining quota.
10. Provide support/admin correction with an immutable audit event.

## 5. Subscription lifecycle

- Use Google Play Billing for in-app digital subscriptions in the Play-distributed Android app, subject to applicable Play policies and any valid regional programme/exception.
- Verify purchase tokens with a trusted backend using Google Play Developer API.
- Validate product ID, package name, purchase state, acknowledgement state, expiry and linked account.
- Acknowledge/confirm purchases according to Play requirements.
- Process Real-time Developer Notifications (RTDN) or equivalent purchase lifecycle notifications.
- Handle active, grace period, account hold, paused, canceled-but-active, expired, revoked, refunded and pending states.
- Restore purchases and reconcile after sign-in/device change.
- Do not unlock premium solely from a client callback.
- Keep secrets and service-account credentials out of the Android app and Git repository.
- Test billing using Play licence testers and test tracks before production.

## 6. Content reuse and quality assurance

- Inspect source HTML, JavaScript, CSS, question JSON, road-sign data and image assets before porting.
- Identify the actual assessment structure and ensure all exam sections and diagrams are supported.
- Preserve answer keys and explanations only after validating the source data.
- Build an import/normalisation step that produces versioned content JSON.
- Check duplicate IDs, missing answers, missing diagram references, invalid answer indices and empty explanations.
- Keep content tests separate from application UI tests.
- Verify results screen shows correct and incorrect answers and the correct explanation for each submitted answer.
- Confirm the app does not display another driving school's identity or branding.

## 7. Delivery milestones

### M1 — Repository foundation and source audit
- Establish repository structure, project rules and implementation documentation.
- Inspect the source K53 repository without modifying it.
- Confirm reusable web runtime, data formats, media assets, dependencies and assessment logic.
- Recommend exact Android wrapper/toolchain and package ID.

### M2 — Content migration and learner MVP
- Create Ekhaya-branded learner experience.
- Import versioned question bank and media.
- Implement study mode, timed tests, scoring, answer review and history.
- Package local learning content and graceful connectivity behaviour.

### M3 — Backend and identity
- Choose and provision backend.
- Implement secure sign-up/sign-in.
- Implement test session and idempotent server-side submission.
- Implement three-free-test entitlement and progress sync.
- Add admin content update workflow.

### M4 — Google Play monetisation
- Configure subscription products and prices in Play Console.
- Implement Google Play Billing.
- Add backend purchase verification, notifications, restore and lifecycle handling.
- Enforce proposed monthly quotas server-side.
- Add transparent plan and subscription disclosures.

### M5 — QA and release readiness
- Functional tests across supported Android versions/devices.
- Test timers, session expiry, interrupted network, duplicate submissions and concurrent requests.
- Test purchase, restore, cancel, grace period, expiry and refund scenarios.
- Security/privacy review, accessibility and performance checks.
- Generate signed Android App Bundle; protect signing key.
- Prepare privacy policy, Data safety disclosures, store listing and support contact.
- Complete required Play testing and production access process.

## 8. Acceptance criteria for first learner MVP

- App installs and launches under Ekhaya K53 branding and unique package ID.
- No Inganathi name, logo, package ID, URLs or runtime dependencies appear in the Ekhaya app.
- Learner can navigate study content and complete a full test.
- Score and answer review are correct against a verified answer key.
- A submitted test is counted once, not more than once.
- First three completed tests are free for the authenticated account.
- Abandoning a test does not consume a test credit.
- Learner can see remaining free tests and past results.
- Inganathi repository and deployments remain unchanged.

## 9. Open technical decisions

- Backend provider and deployment environment.
- Final Android application ID and signing ownership.
- Exact source-content licence/permission and question provenance.
- Final question/test formats and assessment timing.
- Subscription product IDs and final commercial prices.
- Whether Premium quotas are 20 per monthly billing period and Premium Plus is unlimited, as proposed.
- Privacy policy URL, support email and app contact details.
