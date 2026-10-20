# IronPath

A strength & resistance personal trainer for Android. It writes a four week training block for
your experience level, tells you exactly what to lift each session, logs your sets, and adjusts
the next block from how those sets actually felt.

Everything runs on the phone: no account, no network calls, no analytics.

## What it does

**Onboarding** — pick experience (Beginner / Intermediate / Advanced), goal (Strength / Muscle /
General fitness), days per week (2–6) and kit (full gym / dumbbells / bodyweight). The generator
picks the split, exercises, sets, reps, loads and rest times from that.

| Days | Beginner | Intermediate / Advanced |
|------|----------|--------------------------|
| 2 | Full Body A/B | Full Body A/B |
| 3 | Full Body A/B/C | Push / Pull / Legs |
| 4 | Upper A / Lower A / Upper B / Lower B | same |
| 5 | PPL + Upper / Lower | PPL + Upper / Lower |
| 6 | PPL ×2 | PPL ×2 |

**Programming model**
- Four week block: three loading weeks, then a planned deload (lighter loads, same movements).
- Strength: 3–6 sets of 3–5 reps on compounds at RPE 8–9, 8–10 reps on accessories.
- Muscle: 4×10 → 4×8 with double progression; General fitness: 3×12 → 3×10.
- Loads are percentages of a working max per lift, rounded to what plates allow (2.5 kg above
  20 kg, 1 kg below, per-hand dumbbell loads scaled down from the barbell reference).
- Exercises are filtered by level and equipment, never repeat inside a session, and rotate
  across the week so upper/lower days differ.

**Autoregulation** — finishing a workout raises, holds or backs off each lift's working max by up
to 3% per block, based on whether you hit the rep targets and the RPE you logged. Grinding sets
do not get loaded up; missed reps while grinding cause a 3% back-off.

**Logging** — tap a set, adjust weight/reps, pick an RPE, and a rest timer starts. Sessions
survive app restarts. Finishing shows volume, duration and any new estimated 1RMs (Epley).

**Progress** — sessions, weekly volume bars for the last eight weeks, week streak, total volume,
best estimated 1RM per lift, and recent session history.

## Screens

- **Train** — today's session, week ring, exercise preview, week checklist.
- **Workout** — per-set logging, rest countdown, live volume.
- **Progress** — stats, weekly volume chart, records, history.
- **Plan** — block overview, setup, rebuild block from logs, delete all data.

## Project layout

```
app/src/main/java/com/ironpath/app/
  engine/          pure Kotlin, no Android classes — unit tested
    Models.kt          levels, goals, exercise/prescription/session/program types
    ExerciseLibrary.kt 50+ lifts with level, equipment, cue and load factor
    ProgramGenerator.kt splits, set/rep/intensity schemes, exercise selection
    Formulas.kt        Epley 1RM, plate rounding, formatting
    Training.kt        app state, autoregulated progression, PRs, stats
    Codec.kt           line based save format
  ui/              programmatic Views (no Compose, no AndroidX)
    SetupScreen, HomeScreen, WorkoutScreen, ProgressScreen, PlanScreen, Ui
  MainActivity.kt  single activity + tab bar
  Store.kt         file persistence in app-private storage
```

## Build

```bash
# local.properties needs sdk.dir=/path/to/android-sdk
./gradlew assembleDebug
./gradlew installDebug
```

`minSdk 24`, `targetSdk 34`, Kotlin 1.9 / AGP 8.2, no third-party runtime dependencies.

## Tests

```bash
./gradlew testDebugUnitTest
```

20 JUnit tests cover: frequency/level combinations, split selection, equipment and level
filtering, no duplicate lifts per session, week-over-week load progression and deload,
bodyweight programmes, Epley maths and plate rounding, autoregulation up/hold/back-off,
session finishing and history, block rebuilding from updated maxes, PR detection, stats, and
save/load round-trips (including an in-progress session).

## Notes

- Training advice here is generic programming logic, not medical advice. Check with a
  professional before starting a new programme, especially after injury.
- Bodyweight movements are prescribed by reps only and progress through reps and range of
  motion rather than load.
# IronPath
