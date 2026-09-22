# SANCTUARY — Part 1 Acceptance Package v1.1

This package contains the Part 1 specification and complete CLI scenario test fixtures.

## Structure

- `PART1_SPECIFICATION_v1_1.md` — authoritative Part 1 specification
- `inputs/` — complete input supplied to each scenario
- `outputs/` — complete expected CLI transcript for each scenario
- `manifest.json` — test inventory

## Test rule

Each input/output pair represents one complete program session.

The expected output starts at program startup and continues until the program terminates.

The output is therefore not a fragment containing only the condition being tested.

## Placeholder note

No later-phase game commands are invented here. Part 1 tests that require a future valid move are deferred until that command is specified.
