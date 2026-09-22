# GreatLight PROJECT — SANCTUARY
## Part 1 Specification — Startup, Player Validation, Initial Session, Quit and Repetition Foundation
### Version 1.1

---

## 1. Purpose

Part 1 defines the program's initial lifecycle before the full SANCTUARY movement and combat rules are introduced.

Part 1 establishes:

- program startup;
- startup argument validation;
- game-type selection;
- player-name validation;
- case-sensitive player identity;
- initial game creation;
- initial command-line presentation;
- Player A starting the game;
- the quit command;
- quit-based victory;
- the foundation for Child-death termination;
- the foundation for repeated-state draw detection.

Part 1 is a specification and acceptance-test boundary. It does not define implementation code.

---

# 2. Program Startup

The program accepts three startup arguments:

```text
<game type> <player A name> <player B name>
```

The intended forms are:

```text
program 0 <Player A> <Player B>
program 1 <Player A> <Player B>
```

Game type:

| Value | Meaning |
|---:|---|
| `0` | NoGUI / CLI |
| `1` | GUI / Website |

The program must validate the startup arguments before creating or starting a game.

---

# 3. Startup Validation Order

Validation must occur in this order:

```text
1. Validate game type
2. Validate Player A
3. Validate Player B
4. Check that Player A and Player B are different
5. Create/start the game
```

If a validation step fails, the program prints the required error and terminates.

No welcome message, board, turn prompt, or game session may be produced after a failed startup validation.

---

# 4. Game Type Validation

The only valid game-type values are:

```text
0
1
```

Any other value is invalid.

Required error:

```text
ERROR: Game type number should be either 0 or 1, got <gotten argument>
```

Example:

Input:

```text
2 Name Name1
```

Output:

```text
ERROR: Game type number should be either 0 or 1, got 2
```

The program terminates immediately.

---

# 5. Player Name Validation

Player A is validated before Player B.

A player name must not be an integer and must not consist only of characters.

The current Part 1 rule is that a valid player name contains:

- at least one character;
- at least one integer/digit.

Examples of valid names:

```text
Name1
Player7
User2026
A1
```

Examples of invalid names:

```text
123
42
Name
Player
```

Required error:

```text
ERROR: Invalid player name
```

The same error is used whether Player A or Player B is invalid.

---

# 6. Player Name Comparison

Player names are case-sensitive.

Therefore:

```text
Name1
name1
```

are different names.

However:

```text
Name1
Name1
```

are the same name.

The comparison occurs only after both individual names have passed validation.

If the names are identical:

```text
ERROR: Player names should not be the same
```

The program terminates.

---

# 7. Successful Game Startup

When all startup arguments are valid, the program creates a new game.

The output begins exactly as:

```text
Welcome to a new game:
Player A: <Player A name>
Player B: <Player B name>
```

The initial board is then presented.

For the CLI session, the first player prompt is:

```text
Player A turn -> : PLAY
```

The exact board representation is defined by the board/rendering specification and is therefore represented by the board output in the final acceptance transcript once that representation is finalized.

---

# 8. Complete CLI Scenario Requirement

Part 1 tests are **complete CLI scenario tests**.

A test is not merely a test of one isolated function or message.

Each scenario represents:

```text
START PROGRAM
    ↓
READ ALL PROVIDED INPUT
    ↓
RUN PROGRAM
    ↓
CAPTURE EVERYTHING PRINTED
    ↓
PROGRAM TERMINATES
```

Therefore:

- `.input.txt` contains the complete input supplied to the program for that scenario.
- `.expected.txt` contains the complete command-line output from program start until termination.

The expected output must not omit earlier output simply because the scenario is testing a later event.

---

# 9. Quit Command

For Part 1, the command:

```text
q
```

means that the current player quits.

The current player's quit is a terminal event.

If Player A quits:

```text
Player A -> Quits
WIN GAME: Player B wins
```

If Player B quits:

```text
Player B -> Quits
WIN GAME: Player A wins
```

The program terminates immediately after the win result.

---

# 10. Canonical Player A Quit Transcript

Given:

```text
0 Name Name1
q
```

the complete CLI output is:

```text
Welcome to a new game:
Player A: Name
Player B: Name1
Player A turn -> : PLAY
Player A -> Quits
WIN GAME: Player B wins
```

This is the required transcript structure for a successful startup followed by Player A quitting.

---

# 11. Canonical Player B Quit Transcript

Player B can only receive a turn after a valid Player A game command has occurred.

The complete scenario therefore has the form:

```text
Welcome to a new game:
Player A: <name>
Player B: <name>
Player A turn -> : PLAY
<valid Player A command and resulting output>
Player B turn -> : PLAY
Player B -> Quits
WIN GAME: Player A wins
```

The exact Player A command is not specified by Part 1 and will be defined by the later game-command specification.

---

# 12. Game Termination

The project currently defines the following terminal outcomes:

### Win

A game ends with a win when:

1. the current player quits; or
2. the opposing Child dies.

The winning player is the other player.

### Draw

A game ends in a draw when the same complete game state occurs five times with the same player-to-move.

Required output:

```text
DRAW PLAY: Same game state 5 times.
```

---

# 13. Repetition Draw

The repetition rule is:

> If the same complete game state and the same player-to-move occur five times, the game is a draw.

The current player must therefore be part of the repeated-state identity.

Conceptually:

```text
Complete Game State + Current Player
```

is compared.

A matching board with a different player-to-move is not treated as the same repetition entry.

The exact contents of the complete game-state snapshot will be expanded as the later phases define movement, status, combat, counters, and other state.

---

# 14. Part 1 Test Organization

Test cases must be stored in separate input and output directories:

```text
PART1/
├── inputs/
│   ├── 01_....input.txt
│   ├── 02_....input.txt
│   └── ...
│
└── outputs/
    ├── 01_....expected.txt
    ├── 02_....expected.txt
    └── ...
```

The input and output filenames must correspond one-to-one.

Example:

```text
inputs/
└── 01_valid_cli_player_a_quits.input.txt

outputs/
└── 01_valid_cli_player_a_quits.expected.txt
```

---

# 15. Part 1 Acceptance Test Cases

The Part 1 acceptance suite currently contains:

| # | Scenario |
|---:|---|
| 01 | Valid CLI startup + Player A quits |
| 02 | Valid GUI startup + Player A quits |
| 03 | Game type `2` rejected |
| 04 | Non-numeric game type rejected |
| 05 | Player A integer rejected |
| 06 | Player B integer rejected |
| 07 | Player A letters-only name rejected |
| 08 | Player B letters-only name rejected |
| 09 | Identical player names rejected |
| 10 | Case-sensitive player names accepted |
| 11 | Valid alphanumeric player names |
| 12 | Invalid startup terminates before game |

Each expected-output file represents the complete command-line session for that scenario.

---

# 16. What Needs to Happen — Part 1 Implementation Contract

This section defines what the eventual implementation must satisfy.

1. The program must accept the defined startup arguments.
2. Game type must be validated before player names.
3. Only game types `0` and `1` are valid.
4. An invalid game type must produce the exact specified error.
5. The program must terminate after an invalid game type.
6. Player A must be validated before Player B.
7. An invalid player name must produce:
   `ERROR: Invalid player name`
8. Both player names must individually pass validation before they are compared.
9. Player names must be compared case-sensitively.
10. Identical names must produce:
    `ERROR: Player names should not be the same`
11. Failed startup validation must not start a game.
12. Failed startup validation must not print the welcome message.
13. A successful startup must create the initial game.
14. Player A must be the first player.
15. The successful startup output must contain the welcome message and both player names.
16. The initial game presentation must lead to:
    `Player A turn -> : PLAY`
17. `q` must be recognized as the current player's quit command.
18. A quitting player immediately loses the game.
19. The opposing player immediately wins.
20. Player A quitting must produce:
    `Player A -> Quits`
    followed by
    `WIN GAME: Player B wins`
21. Player B quitting must produce the corresponding Player B/A result.
22. The game must terminate after a quit victory.
23. Child death must eventually be a terminal win condition.
24. The game must maintain sufficient state information to support repetition detection.
25. Repetition identity must include the current player.
26. Five occurrences of the same complete game state + same player-to-move must produce:
    `DRAW PLAY: Same game state 5 times.`
27. The game must terminate after the repetition draw.
28. Every Part 1 acceptance test must compare the **complete CLI transcript**, from program startup through termination.
29. Input fixtures and expected-output fixtures must be stored separately under `inputs/` and `outputs/`.
30. Part 1 does not define the implementation of movement, combat, Substance, sickness, or the full board rules; those belong to subsequent parts/phases.
