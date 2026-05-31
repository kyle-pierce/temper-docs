Generate a workout program that I can import into the Temper mobile app.

Use Temper Program Import Format v1.0.
Return only valid JSON. Do not wrap the response in markdown fences or add commentary.

Use the official Temper import docs:
https://kyle-pierce.github.io/temper-docs/program-import/

Use the official Temper exercise catalog:
https://kyle-pierce.github.io/temper-docs/program-import/exercises.json

Every exercise must include a valid exerciseRef from the catalog.
exerciseRef values are opaque IDs like "ex_M8-whQhCC2l-pDKA" — they are NOT readable slugs like "bench_press" or "squat".
You must fetch the catalog and look up the correct ID for each exercise.
Do not invent exerciseRef values.
If the exact exercise is not available, choose the closest appropriate exercise from the catalog.

Critical field requirements — these are common mistakes:
- Required top-level fields: name, context, description, workouts, cycle. No others (e.g. no version, author, schedule).
- reps must be an object: { "min": 5, "max": 5 } — not a plain number.
- rest must be an object: { "workSeconds": 180 } — not a plain number.
- cycle is required. It is an array of workout name strings and/or rest day objects ({ "type": "rest" }). Strings must exactly match a workout name.

Program request:
[Describe the program here]