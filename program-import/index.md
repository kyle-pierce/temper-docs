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

- Return **only valid JSON**. No markdown fences, no commentary.
- Include all required fields: `name`, `context`, `description`, `workouts`, `cycle`.
- The `cycle` array is required. Rest days are objects — `{ "type": "rest" }` — not the string `"Rest"`.
- Each exercise must include `exerciseRef` — the Temper exercise catalog ID.
- `reps.min` must be less than or equal to `reps.max`.
- When `setTypes` is provided, its length must equal `sets`.
- Workout names must be unique. Strings in `cycle` must match a workout name.

---

## Supported fields

### Top level

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | Yes | Program display name |
| `context` | `string` | Yes | Context about the user or goals |
| `description` | `string` | Yes | Short description shown to the user |
| `workouts` | `array` | Yes | List of workouts (minimum 1) |
| `cycle` | `array` | Yes | Ordered cycle slots (minimum 1) |
| `trainingGuidelines` | `object` | No | Auto-progression, missed-target, and deload policies |

### Cycle slots

Each entry in `cycle` is one of:
- A **workout name string** — must match a workout's `name`
- A **rest day object** — `{ "type": "rest" }`

### Each workout

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | Yes | Display name — must be unique |
| `exercises` | `array` | Yes | List of exercises (minimum 1) |
| `focus` | `string` | No | Workout focus category — see valid values below |
| `supersets` | `string[][]` | No | Superset groups — see below |

Valid `focus` values: `"push"`, `"pull"`, `"legs"`, `"upper"`, `"lower"`, `"full_body"`, `"core"`

### Each exercise

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `exerciseRef` | `string` | Yes | Temper exercise catalog ID |
| `sets` | `integer ≥ 1` | Yes | Total number of sets |
| `reps` | `{ min, max }` | Yes | Target rep range |
| `rest` | `{ workSeconds, warmupSeconds? }` | Yes | Rest configuration |
| `setTypes` | `string[]` | No | Per-set type labels — length must equal `sets` |
| `notes` | `string` | No | Notes shown to the user |

`rest.workSeconds` — rest after work sets, in seconds (minimum 1).
`rest.warmupSeconds` — rest after warmup sets, in seconds (minimum 0, defaults to 0).

### Set types

| Value | Meaning |
|-------|---------|
| `work` | Standard working set (default when `setTypes` is omitted) |
| `warmup` | Warm-up set |
| `drop` | Drop set — reduce weight immediately after the previous set |
| `failure` | Go to failure |

### Supersets

`supersets` is an optional array of groups. Each group is an array of exercise names (minimum 2) that will be performed as a superset. The exercises in a group must be adjacent in the workout's `exercises` list and each exercise can only belong to one group.

### `trainingGuidelines`

Optional object controlling automatic progression. All three policies are required when the field is present.

**`progressionPolicy`** — how weight progresses between sessions:
- `{ "type": "manual" }` — user adjusts weight manually
- `{ "type": "linear", "upperBodyIncrement": 2.5, "lowerBodyIncrement": 5, "requireAllWorkSetsCompleted": true }` — add weight each session
- `{ "type": "double_progression", "defaultIncrement": 2.5 }` — progress reps before adding weight

**`missedTargetPolicy`** — what happens when the user misses the rep target:
- `{ "type": "manual" }` — user decides
- `{ "type": "repeat_then_reduce", "missesBeforeReduction": 2, "reductionPercent": 10 }` — deload after N misses

**`deloadPolicy`** — when and how to deload:
- `{ "type": "manual" }` — user initiates deload
- `{ "type": "every_n_weeks", "everyNWeeks": 4, "loadPercent": 70 }` — scheduled deload

---

## Unsupported fields

Avoid these — they are not part of the v1 import format:

- Per-set weight or RPE targets
- Cardio or mobility session types
- User profile or goal fields

---

## Full example

`exerciseRef` values reference the Temper exercise catalog. The catalog is synced separately — use the IDs provided by the catalog for production programs.

```json
{
  "name": "Upper/Lower Split",
  "context": "Intermediate lifter, training 4 days per week, focused on hypertrophy.",
  "description": "4-day upper/lower program alternating horizontal and vertical emphasis.",
  "cycle": [
    "Upper A",
    "Lower A",
    { "type": "rest" },
    "Upper B",
    "Lower B",
    { "type": "rest" },
    { "type": "rest" }
  ],
  "workouts": [
    {
      "name": "Upper A",
      "focus": "upper",
      "exercises": [
        {
          "exerciseRef": "ex_M8-whQhCC2l-pDKA",
          "sets": 4,
          "reps": { "min": 6, "max": 10 },
          "rest": { "workSeconds": 120, "warmupSeconds": 60 },
          "setTypes": ["warmup", "work", "work", "work"]
        },
        {
          "exerciseRef": "ex_jRszZ9t0mrLArO5j",
          "sets": 4,
          "reps": { "min": 6, "max": 10 },
          "rest": { "workSeconds": 120 }
        }
      ]
    },
    {
      "name": "Lower A",
      "focus": "legs",
      "exercises": [
        {
          "exerciseRef": "ex_68cMQ5G3jPG1CpRT",
          "sets": 4,
          "reps": { "min": 5, "max": 8 },
          "rest": { "workSeconds": 150, "warmupSeconds": 90 },
          "setTypes": ["warmup", "work", "work", "work"]
        },
        {
          "exerciseRef": "ex_mNDQcSGhYO_I-b6Y",
          "sets": 3,
          "reps": { "min": 10, "max": 12 },
          "rest": { "workSeconds": 120 }
        }
      ]
    },
    {
      "name": "Upper B",
      "focus": "upper",
      "exercises": [
        {
          "exerciseRef": "ex_6GdtGj4pdOKW4sam",
          "sets": 4,
          "reps": { "min": 6, "max": 10 },
          "rest": { "workSeconds": 120, "warmupSeconds": 60 },
          "setTypes": ["warmup", "work", "work", "work"]
        },
        {
          "exerciseRef": "ex_S7rjIBFoRGnHDuin",
          "sets": 4,
          "reps": { "min": 6, "max": 10 },
          "rest": { "workSeconds": 120 }
        }
      ]
    },
    {
      "name": "Lower B",
      "focus": "legs",
      "exercises": [
        {
          "exerciseRef": "ex_Kn9VQU_nzgSIK99W",
          "sets": 4,
          "reps": { "min": 4, "max": 6 },
          "rest": { "workSeconds": 180, "warmupSeconds": 90 },
          "setTypes": ["warmup", "work", "work", "work"]
        },
        {
          "exerciseRef": "ex_DioMiKJU4E8pNnb8",
          "sets": 3,
          "reps": { "min": 8, "max": 12 },
          "rest": { "workSeconds": 90 }
        }
      ]
    }
  ]
}
```

See the [examples directory](examples/) for complete programs.

---

## Resources

- [JSON Schema](schema.json) — machine-readable schema for validation and AI tooling
- [Prompt Template](prompt.md) — copy-paste prompt for AI tools
- [Examples](examples/) — complete example programs
