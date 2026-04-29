# EXP-14.04.25-pure-function

## Prompt been provided

I am creating an food nutrition ai based application. Bellow i attached first feature of description about that application the user story, 3 criteria and flow based on sequence diagram. Write the logic for this feature as a Pure Function. It must have no side effects (stateless) and must return a predictable output. For frontend use React Native (Expo) TypeScript Chart libraries (Recharts / Victory) for backend FastAPI (Python).

Feature Food Logging

User story

As a user of the Smart Nutrition & Fitness Tracker application, I want to enter the name of a food or scan its barcode, so that I can receive information about its nutritional values.

Acceptance Criteria (AC)

AC1 Search food by name
Given the user on the food logging screen.
When the user enters the name of a food and submits the request.
Then the system shows nutritionsl information about that food.

AC2 Search food by barcode
Given the user on the food logging screen and camera available and wait for scaning.
When the user scans barcode on the food package.
Then the system shows nutritionsl information about that food.

AC3 Food not found
Given the user on the food logging screen.
When the user scans barcode on the food package or enters name of the food than doesnt exist in the system.
Then the system shows message that information about this food could't not be found.

Flow

sequenceDiagram
    actor User
    participant App
    participant Backend
    participant FoodAPI as Food Database / API

    User->>App: Open food logging screen
    User->>App: Enter food name or scan barcode
    App->>Backend: Send food query
    Backend->>FoodAPI: Request food/product data

    alt Food data found
        FoodAPI-->>Backend: Return nutritional information
        Backend-->>App: Return food details
        App-->>User: Display nutritional information
    else Food data not found
        FoodAPI-->>Backend: No matching result
        Backend-->>App: Return not found response
        App-->>User: Show error message
    end

# AI respond

AI did a good job after the first try, sheme wasnt adjusted for getting the right answer. AI provided a plan of the clean way to model this feature as a pure function. In 13 steps Such as
1. Core idea

For your feature, the pure logic should handle:

user types food name
user scans barcode
user submits search
backend result comes back with food data
backend result comes back with not found
backend result comes back with error

So the architecture is:

Pure function = decides next state
Impure layer = does API request / barcode scan / camera access
UI = renders state

2. Domain model

* State

```
type SearchMode = 'idle' | 'name' | 'barcode';
type Status = 'idle' | 'editing' | 'loading' | 'success' | 'not_found' | 'error';

type NutritionInfo = {
  name: string;
  barcode?: string;
  calories: number;
  protein: number;
  carbs: number;
  fats: number;
};

type FoodLoggingState = {
  status: Status;
  mode: SearchMode;
  query: string;
  food: NutritionInfo | null;
  message: string | null;
};
```
* Events

```
type FoodLoggingEvent =
  | { type: 'TYPE_NAME'; value: string }
  | { type: 'SCAN_BARCODE'; value: string }
  | { type: 'SUBMIT_SEARCH' }
  | { type: 'SEARCH_SUCCESS'; food: NutritionInfo }
  | { type: 'SEARCH_NOT_FOUND' }
  | { type: 'SEARCH_ERROR'; message?: string }
  | { type: 'RESET' };
```

* Commands
Pure functions should not fetch themselves, so they can return a command:

```
type FoodLoggingCommand =
  | { type: 'NONE' }
  | { type: 'FETCH_FOOD_BY_NAME'; name: string }
  | { type: 'FETCH_FOOD_BY_BARCODE'; barcode: string };
```

3. Initial state

```
export const initialFoodLoggingState: FoodLoggingState = {
  status: 'idle',
  mode: 'idle',
  query: '',
  food: null,
  message: null,
};
```

4. Pure function in TypeScript

This is the main pure logic for Expo React Native.

```
type FoodLoggingResult = {
  state: FoodLoggingState;
  command: FoodLoggingCommand;
};

export function foodLoggingReducer(
  state: FoodLoggingState,
  event: FoodLoggingEvent
): FoodLoggingResult {
  switch (event.type) {
    case 'TYPE_NAME': {
      const query = event.value.trimStart();

      return {
        state: {
          ...state,
          status: query ? 'editing' : 'idle',
          mode: 'name',
          query,
          food: null,
          message: null,
        },
        command: { type: 'NONE' },
      };
    }

    case 'SCAN_BARCODE': {
      const barcode = event.value.trim();

      return {
        state: {
          ...state,
          status: barcode ? 'editing' : 'idle',
          mode: 'barcode',
          query: barcode,
          food: null,
          message: null,
        },
        command: { type: 'NONE' },
      };
    }

    case 'SUBMIT_SEARCH': {
      const query = state.query.trim();

      if (!query) {
        return {
          state: {
            ...state,
            status: 'error',
            message: 'Please enter a food name or scan a barcode.',
            food: null,
          },
          command: { type: 'NONE' },
        };
      }

      if (state.mode === 'barcode') {
        return {
          state: {
            ...state,
            status: 'loading',
            food: null,
            message: null,
          },
          command: {
            type: 'FETCH_FOOD_BY_BARCODE',
            barcode: query,
          },
        };
      }

      return {
        state: {
          ...state,
          status: 'loading',
          food: null,
          message: null,
        },
        command: {
          type: 'FETCH_FOOD_BY_NAME',
          name: query,
        },
      };
    }

    case 'SEARCH_SUCCESS': {
      return {
        state: {
          ...state,
          status: 'success',
          food: event.food,
          message: null,
        },
        command: { type: 'NONE' },
      };
    }

    case 'SEARCH_NOT_FOUND': {
      return {
        state: {
          ...state,
          status: 'not_found',
          food: null,
          message: "Information about this food couldn't be found.",
        },
        command: { type: 'NONE' },
      };
    }

    case 'SEARCH_ERROR': {
      return {
        state: {
          ...state,
          status: 'error',
          food: null,
          message: event.message ?? 'Something went wrong while searching for food.',
        },
        command: { type: 'NONE' },
      };
    }

    case 'RESET': {
      return {
        state: initialFoodLoggingState,
        command: { type: 'NONE' },
      };
    }

    default: {
      return {
        state,
        command: { type: 'NONE' },
      };
    }
  }
}
```

5. Why this is pure

This function is pure because:

it does not call API
it does not read camera
it does not mutate external variables
same input always gives same output

Example:

```
foodLoggingReducer(initialFoodLoggingState, {
  type: 'TYPE_NAME',
  value: 'Banana',
});
```
It will always return the same result.

6. How it maps to your acceptance criteria

AC1 Search food by name

Flow:

- user types "banana"
- event = TYPE_NAME
- user presses search
- event = SUBMIT_SEARCH
- pure function returns command:
```
{ type: 'FETCH_FOOD_BY_NAME', name: 'banana' }
```
- external layer calls backend
- if backend returns food:
SEARCH_SUCCESS
- state becomes success and UI shows nutrition

AC2 Search food by barcode

Flow:

- barcode scanner reads code
- event = SCAN_BARCODE
- event = SUBMIT_SEARCH
- pure function returns command:
```
{ type: 'FETCH_FOOD_BY_BARCODE', barcode: '1234567890' }
```
- backend response becomes SEARCH_SUCCESS
- UI shows nutrition

AC3 Food not found

- If backend does not find food:
```
{ type: 'SEARCH_NOT_FOUND' }
```
- Then the state becomes:
```
{
  status: 'not_found',
  food: null,
  message: "Information about this food couldn't be found."
}
```

7. React Native Expo usage

In React Native, use the pure function inside useReducer-style logic or a custom state machine.

Example:
```
import React, { useState } from 'react';

const [domain, setDomain] = useState<FoodLoggingResult>({
  state: initialFoodLoggingState,
  command: { type: 'NONE' },
});

function dispatch(event: FoodLoggingEvent) {
  const next = foodLoggingReducer(domain.state, event);
  setDomain(next);

  handleCommand(next.command);
}

async function handleCommand(command: FoodLoggingCommand) {
  try {
    switch (command.type) {
      case 'FETCH_FOOD_BY_NAME': {
        const response = await fetch(`/api/food/search?name=${encodeURIComponent(command.name)}`);
        if (response.status === 404) {
          dispatch({ type: 'SEARCH_NOT_FOUND' });
          return;
        }

        const food = await response.json();
        dispatch({ type: 'SEARCH_SUCCESS', food });
        return;
      }

      case 'FETCH_FOOD_BY_BARCODE': {
        const response = await fetch(`/api/food/barcode/${encodeURIComponent(command.barcode)}`);
        if (response.status === 404) {
          dispatch({ type: 'SEARCH_NOT_FOUND' });
          return;
        }

        const food = await response.json();
        dispatch({ type: 'SEARCH_SUCCESS', food });
        return;
      }

      case 'NONE':
      default:
        return;
    }
  } catch {
    dispatch({ type: 'SEARCH_ERROR' });
  }
}
```
Important detail:
handleCommand is not pure. That is correct.
Only the domain reducer is pure.

8. Backend pure logic in FastAPI/Python

FastAPI endpoints themselves are not pure because they do I/O.
But the backend can still have a pure domain function that transforms API result into a stable response format.

* Python domain types
```
from dataclasses import dataclass
from typing import Optional, Literal, TypedDict


Status = Literal["success", "not_found", "error"]


@dataclass(frozen=True)
class NutritionInfo:
    name: str
    barcode: Optional[str]
    calories: float
    protein: float
    carbs: float
    fats: float


@dataclass(frozen=True)
class FoodLookupResult:
    status: Status
    food: Optional[NutritionInfo]
    message: Optional[str]
```

* Pure Python mapper
```
from typing import Optional, Dict, Any


def map_food_api_response(api_data: Optional[Dict[str, Any]]) -> FoodLookupResult:
    if not api_data:
        return FoodLookupResult(
            status="not_found",
            food=None,
            message="Information about this food couldn't be found."
        )

    nutriments = api_data.get("nutriments", {})

    return FoodLookupResult(
        status="success",
        food=NutritionInfo(
            name=api_data.get("name", "Unknown food"),
            barcode=api_data.get("barcode"),
            calories=float(nutriments.get("calories", 0)),
            protein=float(nutriments.get("protein", 0)),
            carbs=float(nutriments.get("carbs", 0)),
            fats=float(nutriments.get("fats", 0)),
        ),
        message=None
    )
```
This is also pure because:

no HTTP calls
no DB access
no mutation
deterministic output

9. FastAPI route example

The route is impure, but it uses the pure mapper.
```
from fastapi import APIRouter, HTTPException

router = APIRouter()


@router.get("/food/search")
async def search_food_by_name(name: str):
    api_data = await external_food_service.search_by_name(name)  # impure
    result = map_food_api_response(api_data)  # pure

    if result.status == "not_found":
        raise HTTPException(status_code=404, detail=result.message)

    return {
        "name": result.food.name,
        "barcode": result.food.barcode,
        "calories": result.food.calories,
        "protein": result.food.protein,
        "carbs": result.food.carbs,
        "fats": result.food.fats,
    }
```

10. Best structure for your project

Use this separation:

- Frontend
pure reducer for feature logic
scanner hook for barcode
API service for backend calls
UI components for rendering nutrition data
- Backend
FastAPI endpoint for HTTP
service for Food API integration
pure mapper/validator for response normalization
response schema for frontend

11. Optional chart preparation as pure function

Since you mentioned Recharts / Victory, if you want to display macro split charts, prepare chart data with a pure function too.
```
type MacroChartItem = {
  x: string;
  y: number;
};

export function buildMacroChartData(food: NutritionInfo): MacroChartItem[] {
  return [
    { x: 'Protein', y: food.protein },
    { x: 'Carbs', y: food.carbs },
    { x: 'Fats', y: food.fats },
  ];
}
```
This is also deterministic and works well for Victory or Recharts.

12. Final pure function logic description for documentation
You can place this in your project docs:
Pure Function Logic for Food Logging Feature

Input:
- current feature state
- user event
- backend result event

Output:
- next feature state
- command describing required external action

Rules:
    1. If the user types a food name, store it as query and set mode to "name".
    2. If the user scans a barcode, store it as query and set mode to "barcode".
    3. If the user submits an empty query, return error state with validation message.
    4. If the user submits a name query, return loading state and command to fetch food by name.
    5. If the user submits a barcode query, return loading state and command to fetch food by barcode.
    6. If food data is returned, return success state with nutrition information.
    7. If no food is found, return not_found state with appropriate message.
    8. If request fails, return error state with generic error message.

This logic is stateless, side-effect free, and deterministic.

13. Recommendation

For this feature, the strongest implementation is:

Frontend: reducer + command pattern in TypeScript
Backend: service layer + pure mapper in Python
Shared principle: pure logic handles state transitions, impure code handles API/camera/network

This is better than putting everything into one function, because camera scanning and API requests can never be truly pure.

If you want, I can turn this into a formal functional specification for your documentation, or write the full Expo TypeScript implementation with reducer, service, and screen component.
