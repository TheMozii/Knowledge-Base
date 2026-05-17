# Experiment: PocketBase Nutrition Persistence

## Context

The app already has:

- PocketBase authentication
- food search by name
- barcode lookup
- AI text-based food analysis
- local daily nutrition summary

The next step was to connect the daily nutrition summary to PocketBase so logged foods are saved for the authenticated user.

## Hypothesis

Saving nutrition records to PocketBase after authentication will make the daily summary more useful because logged meals can belong to a real user instead of only local state.

## Implementation

The implementation added:

- authenticated PocketBase user id and token passed into Food Logging
- a save command in the Food Logging command flow
- PocketBase `nutritions` record creation when the user taps "Add to Day"
- local daily summary update only after PocketBase confirms the save
- field-level PocketBase error parsing for save failures
- PocketBase migration for the correct `nutritions` collection

Expected PocketBase collection:

`nutritions`

Fields:

- `user` relation to `users`
- `name` text
- `source` select: `open_food_facts`, `ai_text`, `manual`
- `barcode` text optional
- `calories` number
- `protein` number
- `carbs` number
- `fats` number
- `loggedDate` date

Recommended access rules:

- List/Search: `user = @request.auth.id`
- View: `user = @request.auth.id`
- Create: `@request.auth.id != ""`
- Update: `user = @request.auth.id`
- Delete: `user = @request.auth.id`

## Verification

- TypeScript check passed with `npx tsc --noEmit`
- PocketBase migration was applied
- direct PocketBase save test passed
- a test nutrition record was created successfully

Test record id:

`krjqhbfrhduu3ju`

## Result

PocketBase nutrition persistence is implemented.

Now when a logged-in user adds a food to the day, the app saves the record to PocketBase before updating the local daily summary.

## Limitations

- Existing saved records are not loaded on app startup yet.
- Removing a food only updates local state; it does not delete the PocketBase record yet.
- A test record was added during verification.
- PocketBase database runtime files changed while the server was running.

## Lessons Learned

The Command pattern worked well for adding persistence because the reducer still describes side effects without directly calling APIs.

PocketBase requires the collection schema and access rules to match the frontend payload exactly.

Saving before updating local totals prevents unsaved records from appearing as saved.

## Open Questions

- Should the app load today's saved records immediately after login?
- Should removing a food delete the PocketBase record?
- Should logged foods support serving size editing before save?
- Should `source` be shown in the UI?
- Should daily totals be calculated in the frontend or cached in PocketBase later?
