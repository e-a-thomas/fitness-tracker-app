# Data Model

## Overview

The app uses a hierarchical data model to represent workout history:

```text
Session
└── SessionExercise
    ├── ExerciseDefinition
    └── Set
```

A **Session** represents one complete workout. 
A **SessionExercise** represents an exercise performed during that particular session. 
An **ExerciseDefinition** represents the exercise itself
A **Set** represents an individual set performed during a SessionExercise.

This is designed to allow me to have a logical flow of pieces when storing the data, but also to have a highly queriable and accessible store of data to draw insights from

---

## Entities

### Session

A `Session` represents one complete workout.

**Properties:**

* `id` — Unique identifier
* `name` — Name of the workout, such as "Upper" or "Lower"
* `date` — Date the workout took place
* `exercises` — The SessionExercises performed during the workout

**Relationship:**

```text
Session 1 ──── * SessionExercise
```

A Session can contain multiple SessionExercises.

Example:

```text
Session
Name: Push
Date: September 3, 2026

Exercises:
- Bench Press
- Incline Dumbbell Press
- Cable Fly
```

---

### ExerciseDefinition

An `ExerciseDefinition` represents a reusable definition of an exercise.

ExerciseDefinitions are like my library of exercises that I can do. Not specifically tied to any one Session. 

**Properties:**

* `id` — Unique identifier
* `name` — Exercise name
* `category` — Optional exercise category

**Relationship:**

```text
ExerciseDefinition 1 ──── * SessionExercise
```

The same ExerciseDefinition can be referenced by many SessionExercises across a user's workout history.

For example, Bench Press can be done many times. 

---

### SessionExercise

A `SessionExercise` represents a specific occurrence of an exercise within a Session.


**Properties:**

* `id` — Unique identifier
* `order` — Position of the exercise within the Session
* `session` — The Session in which the exercise was performed
* `exerciseDefinition` — The exercise being performed
* `sets` — Sets performed during this occurrence

**Relationships:**

```text
Session 1 ──── * SessionExercise
ExerciseDefinition 1 ──── * SessionExercise
SessionExercise 1 ──── * Set
```

For example:

```text
Session: Push — September 3, 2026

SessionExercise
ExerciseDefinition: Bench Press
Sets:
- 185 × 8
- 185 × 7
- 175 × 9
```

---

### Set

A `Set` represents an individual set performed during a SessionExercise.

**Properties:**

* `id` — Unique identifier
* `setNumber` — Position of the set within the exercise
* `weight` — Weight used
* `reps` — Number of repetitions

**Relationship:**

```text
SessionExercise 1 ──── * Set
```

Example:

```text
SessionExercise: Bench Press

Set 1: 185 lb × 8
Set 2: 185 lb × 7
Set 3: 175 lb × 9
```

---

## Relationships

The complete relationship structure is:

```text
                    ExerciseDefinition
                           │
                           │ 1
                           │
                           │ *
                           ▼
Session ─────── 1 → * SessionExercise
                         │
                         │ 1
                         │
                         │ *
                         ▼
                        Set
```

More explicitly:

```text
Session
  └── SessionExercise
        ├── ExerciseDefinition
        └── Set
```


---

## Example

A user performs the following workout:

```text
Push — September 3, 2026

Bench Press
    185 × 8
    185 × 7
    175 × 9

Incline Dumbbell Press
    60 × 10
    60 × 9
    55 × 10
```

The data is represented as:

```text
Session
├── name: "Push"
├── date: September 3, 2026
│
├── SessionExercise
│   ├── ExerciseDefinition → "Bench Press"
│   └── Sets
│       ├── 185 × 8
│       ├── 185 × 7
│       └── 175 × 9
│
└── SessionExercise
    ├── ExerciseDefinition → "Incline Dumbbell Press"
    └── Sets
        ├── 60 × 10
        ├── 60 × 9
        └── 55 × 10
```

If the user performs Bench Press again in a future Session, the app creates a new `SessionExercise` referencing the existing `Bench Press` `ExerciseDefinition`.

---

## Why Keep ExerciseDefinition and SessionExercise Separate

Separating these two concepts is intentional.

`ExerciseDefinition` answers:

> "What exercise is this?"

`SessionExercise` answers:

> "How did I perform this exercise during this particular session?"

For example, there should be one reusable:

```text
ExerciseDefinition
    Bench Press
```

but potentially many:

```text
SessionExercise
    Bench Press — September 3
    Bench Press — September 6
    Bench Press — September 10
    Bench Press — September 13
    ...
```

This structure will make it as easy as possible to query for insights and previous results. 

It also provides the foundation for features such as:

* Viewing the previous time an exercise was performed
* Comparing current sets to previous sets
* Tracking weight progression
* Calculating exercise volume
* Identifying personal records
* Creating exercise-specific progress charts

---

## Design Principles

### Dates are stored as Dates

Session dates should use Swift's `Date` type rather than strings. This allows the app to correctly sort, filter, group, and compare Sessions by date.

### Sets belong to SessionExercises

Sets represent actual performance and therefore belong to a specific occurrence of an exercise. They should not belong to the reusable ExerciseDefinition.

### ExerciseDefinitions are reusable

An ExerciseDefinition should represent one canonical exercise and be referenced by multiple SessionExercises.



