# Experiment: Weekly Nutrition Chart

## Context

The app already had daily nutrition totals. The next improvement was to show nutrition trends over a week.

## Hypothesis

Users can better understand progress if daily calorie totals are visualized across the week instead of only shown for the current day.

## Implementation

The implementation added:

- weekly totals support in the food logging module
- reducer/state updates for weekly nutrition data
- service/type updates for chart data
- a weekly calorie chart on the frontend home screen
- roadmap and weekly chart plan updates

## Observations

- Weekly visualization adds progress context without needing advanced analytics.
- Keeping the chart focused on calories avoids UI overload.
- The feature depends on clean saved nutrition data.

## Result

Initial result: implemented.

## Lessons Learned

Simple trend visualization is a good MVP feature because it improves feedback while keeping scope controlled.

## Open Questions

- Should macros also be charted weekly?
- Should the week start on Monday or Sunday?
- How should missing days be displayed?
