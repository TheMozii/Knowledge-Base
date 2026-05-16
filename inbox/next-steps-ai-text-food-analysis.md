# Next Steps: AI Text-Based Food Analysis

## Project

Smart Nutrition & Fitness Tracker

## Current State

The project already has a Food Logging module.

Current implemented functionality:
- food search by name
- barcode-based food lookup
- frontend Command pattern for food logging actions
- FastAPI backend route for food data lookup
- Open Food Facts API integration

## Next Step

The next planned feature is AI text-based food analysis.

The user should be able to type a natural meal description, for example:

"2 eggs, 1 slice of toast, and coffee with milk"

The system should return approximate nutrition values:
- calories
- protein
- carbs
- fats

## Why This Is Next

This step matches the MVP scope because:
- it improves speed of food logging
- it keeps the app simple
- it reuses the existing Food Logging feature
- it is easier than image-based food recognition
- it supports the project goal of AI-powered nutrition tracking

## Planned Implementation

1. Add a new frontend action for AI meal text analysis.
2. Keep the existing Food Logging Command pattern.
3. Add a backend endpoint:

POST /food/analyze-text

4. Use the OpenAI API to estimate nutrition values.
5. Return structured JSON with:
   - food name or meal description
   - calories
   - protein
   - carbs
   - fats
   - approximation message

6. Show a clear message that AI values are approximate.

## Restrictions

Do not add:
- image recognition yet
- custom machine learning models
- advanced analytics
- medical advice
- unnecessary microservices

## Open Questions

- Should AI results be editable before saving?
- Should the app combine Open Food Facts data with AI estimates?
- How should the app explain that AI nutrition values are approximate?
- Should this feature be implemented before authentication?

## Later Action

After implementation, convert this inbox note into an experiment file under:

experiments/

Possible file name:

experiments/EXP-ai-text-food-analysis.md

The experiment should record:
- hypothesis
- implementation attempt
- observations
- result
- lessons learned
- open questions

Writing rules:
- Keep the note simple and clear.
- Do not restructure the repository.
- Do not add unrelated features.
- Focus only on the next project steps.
