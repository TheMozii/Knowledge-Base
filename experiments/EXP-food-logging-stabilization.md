# Experiment: Food Logging Stabilization

## Context

The deployed and local app had several food logging issues during testing:

- Search results sometimes failed when the backend or Open Food Facts did not return a food.
- The deployed frontend sometimes created API URLs with double slashes.
- Demo users were logged out after refreshing the browser.
- Foods with missing or zero protein/fats/carbs could not always be added to the daily summary.
- After adding a food, the search field stayed filled instead of clearing for the next input.

This was stabilization work for the MVP food logging flow, not a new major feature.

## Hypothesis

The MVP food logging flow becomes easier to test and more reliable if the app handles missing external data, browser refreshes, incomplete nutrition values, and repeated food entry more gracefully.

## Implementation

The stabilization added:

- Browser localStorage demo authentication.
- Demo session persistence after refresh.
- localStorage persistence for demo nutrition records.
- A local demo food fallback list for common foods.
- API base URL normalization to remove trailing slashes.
- A Nutella demo fallback result.
- Frontend nutrition number normalization before saving.
- PocketBase migration to allow empty or zero nutrition number values.
- Search field clearing after a food is successfully added to the daily summary.
- Reducer test coverage for clearing search after save.

AI and demo nutrition values are approximate and should be treated as MVP support data, not medically accurate nutrition guidance.

## Observations

- PocketBase required number fields reject zero values, so nutrition macro fields should not be required.
- Some real food API records can have missing macro values.
- For an MVP, missing macros should safely default to 0 instead of blocking the user.
- The deployed demo should not fully depend on external APIs for every food search.
- Browser localStorage is useful for demo persistence but is not a production database.
- Clearing the search after save improves repeated food entry speed.

## Result

Initial result: implemented.

Verification:

- Frontend tests passed with:
  `cd frontend && npm run test`

- Backend tests passed with:
  `cd backend && python3 -m unittest discover -s tests`

- PocketBase migration was added:
  `pb_migrations/1779989712_allow_empty_nutrition_numbers.js`

## Lessons Learned

Food logging needs defensive handling because external nutrition data is inconsistent. Missing or zero macro values should not break the core user flow.

For deployed demos, local fallback data and localStorage can improve reliability while keeping the real architecture available for future production work.

## Open Questions

- Should missing nutrition values be visually marked as unknown instead of shown as 0?
- Should demo fallback foods be expanded?
- Should the backend also include fallback foods?
- Should PocketBase be replaced by Supabase for production deployment?
- Should the app show whether nutrition data came from Open Food Facts, AI, or demo data?
