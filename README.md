# Ekhaya Technologies K53

A standalone Android learner's licence preparation application for South Africa.

## Product decisions

- Product: Ekhaya Technologies K53
- Platform for initial release: Android
- Application is independent of Inganathi Driving Academy.
- First three completed practice tests are free per learner account.
- Proposed Premium: R29.99/month for 20 completed tests per billing month.
- Proposed Premium Plus: R49.99/month for unlimited practice.
- Subscription prices and entitlements are configuration decisions to confirm before production.
- A test allowance is consumed only after a valid test submission is accepted by the backend.
- The app must not trust locally stored subscription or test-counter values as authoritative.

## Architecture

- `app/`: Android application.
- `web/`: reusable learner experience and K53 learning assets.
- `backend/`: server-side authentication, test submissions, subscription verification and learner progress.
- `docs/`: product, security, release and implementation documentation.

The app will package a versioned learning-content snapshot for responsive loading and offline study where permitted. Server-side services remain authoritative for account entitlements, completed-test counting and subscription status.

## Separation requirement

This repository is the Ekhaya K53 product repository. Do not add Inganathi branding, package IDs, endpoints, account systems or deployment configuration. Do not modify the Inganathi repository as part of this project.

## Status

Foundation stage. The initial project plan and subscription rules will be documented before implementation of learner-facing functionality.
