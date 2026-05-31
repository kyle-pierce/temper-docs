---
title: Temper Program Import Format v1.0
---

# Temper Program Import Format v1.0

The Program Import Format lets you generate a structured workout program with an AI tool and import it directly into the Temper mobile app.

---

## Import flow

1. Copy the [prompt template](prompt.md) into ChatGPT, Claude, Gemini, or another AI tool.
2. Describe the program you want.
3. The AI returns valid JSON.
4. Paste the JSON into Temper's import screen.
5. Review and edit the program in Temper.
6. Save it as a normal private program.

---

## Important rules

AI tools generating programs must follow these rules:

- Return **only valid JSON**. No markdown fences, no commentary.
- Set `format` to `"temper_program"`.
- Set `version` to `"1.0"`.
- Include an ordered `cycle` array that lists each workout name (or `"Rest"`) in the intended weekly pattern.
- Use only the supported set types: `work`, `warmup`, `drop`, `failure`.
- Each exercise must include either `exerciseId` or `exerciseName`. Use `exerciseName` when the Temper exercise ID is not known.

---

## Supported fields

### Top level

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `format` | `string` | Yes | Must be `"temper_program"` |
| `version` | `string` | Yes | Must be `"1.0"` |
| `program` | `object` | Yes | The program definition |

### `program`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | Yes | Display name |
| `workouts` | `array` | Yes | List of workouts |
| `description` | `string` | No | Short description |
| `context` | `string` | No | Free-form context |
| `guidelines` | `string` | No | Programming guidelines |
| `structuredGuidelines` | `string[]` | No | Structured guideline list |
| `cycle` | `string[]` | No | Ordered cycle slot names |

### Each workout

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | Yes | Display name (matches cycle slot) |
| `exercises` | `array` | Yes | List of exercises |
| `label` | `string` | No | Short label (e.g. `"A"`, `"Push"`) |
| `focus` | `string` | No | Focus description (e.g. `"Chest & Triceps"`) |

### Each exercise

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `sets` | `integer ≥ 1` | Yes | Number of sets |
| `repRange` | `object` | Yes | `{ min, max }` — target rep range |
| `exerciseId` | `string` | No | Temper catalog ID (preferred) |
| `exerciseName` | `string` | No | Human-readable name (fallback) |
| `restSeconds` | `integer ≥ 0` | No | Rest between sets in seconds |
| `setTypes` | `string[]` | No | Per-set type labels — see below |
| `programNotes` | `string` | No | Notes shown to the user |

### Set types

When provided, `setTypes` must be an array with one entry per set. Valid values:

| Value | Meaning |
|-------|---------|
| `work` | Standard working set (default) |
| `warmup` | Warm-up set — lighter, not counted in volume |
| `drop` | Drop set — reduce weight immediately after previous set |
| `failure` | Go to failure |

---

## Unsupported fields

Avoid these — they are not part of the v1 import format:

- Internal app IDs other than `exerciseId`
- Per-set weight or rpe targets
- Supersets or circuit groupings
- Cardio or mobility session types
- User profile or goal fields

---

## Full example

```json
{
  "format": "temper_program",
  "version": "1.0",
  "program": {
    "name": "Upper/Lower Split",
    "description": "4-day upper/lower program for intermediate lifters.",
    "cycle": ["Upper A", "Lower A", "Rest", "Upper B", "Lower B", "Rest", "Rest"],
    "workouts": [
      {
        "name": "Upper A",
        "focus": "Chest & Back",
        "exercises": [
          {
            "exerciseName": "Bench Press",
            "sets": 4,
            "repRange": { "min": 6, "max": 10 },
            "restSeconds": 120,
            "setTypes": ["warmup", "work", "work", "work"]
          },
          {
            "exerciseName": "Barbell Row",
            "sets": 4,
            "repRange": { "min": 6, "max": 10 },
            "restSeconds": 120
          }
        ]
      }
    ]
  }
}
```

See the [examples directory](examples/) for complete programs.

---

## Resources

- [JSON Schema](schema.json) — machine-readable schema for validation and AI tooling
- [Prompt Template](prompt.md) — copy-paste prompt for AI tools
- [Examples](examples/) — complete example programs
