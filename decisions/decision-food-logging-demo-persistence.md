# Decision: Use Browser localStorage For Demo Food Logging Persistence

## Context

Demo users were logged out after refreshing the browser, and demo nutrition records did not reliably survive refreshes. This made the deployed MVP harder to test.

## Options

- Use browser localStorage for demo authentication and demo nutrition records.
- Require PocketBase authentication for every deployed demo session.
- Add a production database replacement before continuing MVP testing.

## Decision

Use browser localStorage for demo authentication and demo nutrition record persistence.

## Reason

localStorage is fast to implement, works in the deployed browser demo, and makes the MVP easier to test without changing the full backend architecture.

## Trade-offs

- localStorage is not a production database.
- Data is tied to the browser.
- It improves demo reliability but does not replace proper production persistence.
