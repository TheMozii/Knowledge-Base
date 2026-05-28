# Belief: Food Logging Speed Matters More Than Perfect Accuracy For MVP

## Context

The Smart Nutrition & Fitness Tracker MVP is focused on making food logging easy to test and use. During stabilization, missing nutrition values, external API failures, and repeated food entry friction affected the core flow.

## Assumption

Users are more likely to keep using the MVP if adding food is fast and reliable, even when nutrition values are approximate.

## Risk

Approximate AI or demo nutrition values may be misunderstood as exact. Showing missing macros as 0 may also hide uncertainty.

## How to validate

Test whether users can log several foods quickly without getting blocked, then ask if they understand that AI and demo nutrition values are approximate and not medical guidance.
