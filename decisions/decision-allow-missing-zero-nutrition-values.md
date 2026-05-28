# Decision: Allow Missing Or Zero Nutrition Macro Values

## Context

Some Open Food Facts records have missing macro values, and PocketBase required number fields rejected zero values. This blocked users from adding some foods to the daily summary.

## Options

- Block foods when protein, fats, or carbs are missing.
- Require users to manually fill every missing macro before saving.
- Allow empty or zero macro values and normalize frontend nutrition numbers before saving.

## Decision

Allow missing or zero nutrition macro values and normalize nutrition numbers before saving.

## Reason

Food logging should not fail just because external nutrition data is incomplete. For the MVP, it is better to keep the logging flow working and treat missing macros defensively.

## Trade-offs

- Missing values shown as 0 may reduce clarity.
- Approximate AI and demo values are not medically accurate.
- The app may later need a visual difference between real zero values and unknown values.
