# Experiment: Core Food Logging Tests

## Context

The MVP now includes authentication, food lookup, AI text analysis, PocketBase persistence, daily dashboard, and weekly calorie chart. Before adding more features, the project needed focused tests around core behavior.

## Hypothesis

Lightweight tests can protect important logic without adding unnecessary testing complexity.

## Implementation

The implementation added:

- frontend reducer tests for validation, commands, daily totals, delete behavior, and weekly totals
- backend mapper tests for valid food data, missing nutrition fields, and not-found data
- backend service tests for AI validation and missing OpenAI API key behavior
- backend API route tests for health, food search, not-found behavior, and AI error mapping
- `.gitignore` updates for database runtime files

## Observations

- TypeScript compilation plus Node execution kept frontend tests simple.
- Python `unittest` kept backend tests dependency-light.
- External API calls were excluded to keep tests fast and stable.

## Result

Initial result: implemented.

Test commands:

```sh
cd frontend && npm run test
cd backend && python3 -m unittest discover -s tests
```

## Lessons Learned

Focused tests are enough for this stage of the MVP and match the project rule of simplicity over complexity.

## Open Questions

- When should end-to-end tests be added?
- Should PocketBase integration tests be added after authentication stabilizes?
- Should pytest or Jest be introduced later if test complexity grows?
