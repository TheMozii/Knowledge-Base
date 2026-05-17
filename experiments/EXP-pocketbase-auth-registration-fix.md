# Experiment: PocketBase Authentication Registration Fix

## Context

The Smart Nutrition & Fitness Tracker authentication flow was changed to use PocketBase.

PocketBase is running locally at:

`http://127.0.0.1:8090`

The frontend uses:

`EXPO_PUBLIC_POCKETBASE_URL=http://127.0.0.1:8090`

or defaults to that URL if the environment variable is missing.

This was an authentication integration fix, not an unrelated product feature.

## Problem

The app showed this error during registration:

`Failed to create record.`

The error came from PocketBase when trying to create a new user record.

## Cause

The frontend registration request only sent:

- email
- password
- passwordConfirm

PocketBase user creation also needed a valid username value for the current collection setup.

The app also showed only the generic PocketBase error message, so the real field-level cause was unclear.

## Implementation

The frontend auth service was updated.

File changed:

`frontend/src/features/auth/service.ts`

Changes made:

- added automatic username generation from email
- sent `username` when creating a PocketBase user
- improved PocketBase error parsing
- field-level PocketBase errors are now shown instead of only generic messages

Registration endpoint:

`POST /api/collections/users/records`

Login endpoint:

`POST /api/collections/users/auth-with-password`

## Verification

Direct PocketBase test succeeded:

- registered test user: `r@gmail.com`
- logged in with the same user successfully
- PocketBase returned an auth token
- TypeScript check passed with `npx tsc --noEmit`

## Result

PocketBase registration and login now work with the frontend auth flow.

Important note:

The test account `r@gmail.com` already exists in PocketBase after testing. Future registration tests should use a different email or sign in with the existing account.

## Lessons Learned

PocketBase errors can be generic at the top level.

For better developer experience, the frontend should parse field-level errors from the PocketBase response.

Authentication integration should be tested directly against the backend service before debugging the UI.

## Open Questions

- Should usernames be visible to users or generated silently?
- Should the app use email only for identity and hide username completely?
- Should auth tokens be persisted after refresh?
- Should the backend validate PocketBase tokens before saving nutrition records?
- Should the `nutritions` collection be connected to authenticated users next?
