Generate a workout program that I can import into the Temper mobile app.

Use Temper Program Import Format v1.0.
Return only valid JSON. Do not wrap the response in markdown fences or add commentary.

Use the official Temper import docs:
https://kyle-pierce.github.io/temper-docs/program-import/

Use the official Temper exercise catalog:
https://kyle-pierce.github.io/temper-docs/program-import/exercises.json

Every exercise must include a valid exerciseRef from the catalog.
exerciseRef values are opaque IDs like "ex_Xg4kR2mPqL" — they are NOT readable slugs like "bench_press" or "squat".
You must fetch the catalog and look up the correct ID and trackingType for each exercise.
Do not invent exerciseRef values.
If the exact exercise is not available, choose the closest appropriate exercise from the catalog.
If no suitable exercise ref can be found, report an error to the user instead of generating an invalid program.

Critical field requirements — these are common mistakes:
- Required top-level fields: name, context, description. For a single-phase program also include workouts and cycle. For a periodized program use phases (array of 2 or more) instead of workouts and cycle — never include both.
- Each phase must have: name, cycleRepetitions (integer ≥ 1), workouts, and cycle. Phase cycle strings must match workout names within that same phase only.
- For catalog exercises with trackingType "reps", use a rep target object, not a plain number.
- For catalog exercises with trackingType "duration", use durationSeconds (number) instead of a rep target.
- Do not include trackingType in the generated program JSON; it comes from the exercise catalog.
- rest must be an object: { "workSeconds": 180 } — not a plain number.
- cycle (top-level or per-phase) is an array of workout name strings and/or rest day objects ({ "type": "rest" }). Strings must exactly match a workout name in the same scope.

Program request:
[Describe the program here]