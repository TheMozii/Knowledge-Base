# Belief: Deployed Demos Need Local Fallbacks

## Context

The deployed demo depends on the backend, Open Food Facts, browser state, and saved nutrition records. During testing, food search and refresh behavior could fail or interrupt the demo experience.

## Assumption

A deployed MVP demo should still show the main food logging flow even when an external API does not return useful data.

## Risk

Fallback data can make the app look more complete than it really is. Demo data must stay clearly scoped and should not be treated as a production food database.

## How to validate

Test the deployed demo with common foods, failed searches, and browser refreshes. Check whether the user can still complete a basic daily food logging session.
