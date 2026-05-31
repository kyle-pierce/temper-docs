Generate a workout program that I can import into the Temper mobile app.

Use Temper Program Import Format v1.0.
Return only valid JSON. Do not wrap the response in markdown fences or add commentary.

Use the official Temper import docs:
https://kyle-pierce.github.io/temper-docs/program-import/

Use the official Temper exercise catalog:
https://kyle-pierce.github.io/temper-docs/program-import/exercises.json

Every exercise must include a valid exerciseRef from the catalog.
Do not invent exerciseRef values.
If the exact exercise is not available, choose the closest appropriate exercise from the catalog.
Include exerciseName for readability, but use exerciseRef as the authoritative identity.

Program request:
[Describe the program here]