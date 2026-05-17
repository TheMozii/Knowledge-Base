# Experiment: User-Owned Nutrition Records

## Context

The app uses PocketBase for authentication and nutrition persistence.

The app already had:

- PocketBase users
- `nutritions` collection
- food save flow
- saved food loading flow

The next issue was making sure food records belong to the correct authenticated user and that one user cannot load another user's foods.

## Problem

Nutrition records needed a proper relation to the authenticated user.

The app also needed to load only the foods entered by the signed-in user.

## Implementation

A PocketBase migration was added:

`pb_migrations/1779027600_enforce_user_owned_nutritions.js`

The `nutritions` collection uses:

`user` relation -> `users`

The frontend save flow already sends:

`user: userId`

The frontend load query was updated to filter by the authenticated user:

`user = "{userId}" && loggedDate >= ...`

PocketBase access rules were updated:

List/Search:

`user = @request.auth.id`

View:

`user = @request.auth.id`

Create:

`@request.auth.id != "" && user = @request.auth.id`

Update:

`user = @request.auth.id`

Delete:

`user = @request.auth.id`

## Verification

- Migration was applied.
- PocketBase restarted successfully.
- Database rules were confirmed with SQLite.
- TypeScript check passed with `npx tsc --noEmit`.
- Backend Python compile check passed.

## Result

Nutrition records are now user-owned.

Each authenticated user should only save and load their own food records.

## Notes

Direct login verification with old test users failed because the test accounts or passwords likely changed in PocketBase admin.

The schema and rules are now correct, but app-level testing should be done with a valid current PocketBase user.

## Lessons Learned

Food persistence should always be tied to authenticated user identity.

PocketBase relation fields and access rules must be configured together.

Frontend filtering is useful, but database access rules are the real protection.

## Open Questions

- Should deleting food from the daily summary also delete the PocketBase record?
- Should the app show user-owned food history by date?
- Should nutrition records include serving size?
- Should records store whether they came from AI or Open Food Facts more visibly?
- Should test users and seed data be managed through migrations or manual admin setup?
