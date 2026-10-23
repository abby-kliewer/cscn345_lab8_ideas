# Snake — Two-Player Edition: Lab 8 Build Guide

> **Important:** This guide gives you the *design*, *structure*, and *pseudocode*, not finished source code. The lab forbids copied code, so you and your partner should write every line yourselves. Small snippets here only show a Python technique, not a game.

**Difficulty:** ★★★ · **Interface:** `pygame` (recommended) or Graphics.py · **Build time:** 3–4 days

Classic Snake is single-player, so this version puts **two snakes on one board**: Player 1 uses **WASD** and Player 2 uses the **arrow keys**. Both chase food, and crashing into a wall, yourself, or the other snake ends your round.

> **Library note:** Graphics.py's `win.checkKey()` only remembers the *last* key pressed, so with two players mashing keys at once, inputs get dropped. **`pygame` handles simultaneous keys properly** and counts as a "reasonable external library." Install it with `pip install pygame`. If you must use Graphics.py, make the game turn-based instead (Player 1 plays a round, then Player 2, and the higher score wins).

## Data structures
- Grid-based movement: the board is `GRID_WIDTH × GRID_HEIGHT` cells of `CELL_SIZE` pixels
- Each snake is a **list of `(col, row)` tuples**, with the head at index 0
- `direction`: a tuple like `(1, 0)` for right or `(0, -1)` for up
- `food`: one `(col, row)` tuple
- `scores`: a dict, plus a per-round `food_eaten` count
- Optionally a small `Snake` class holding `body`, `direction`, `color`, `alive`, and `name` (a clean way to show off organization)

## Functions / methods to write
| Function | Job |
|---|---|
| `handle_input(events, snake1, snake2)` | Map keys to directions; **block 180° reversals** |
| `move_snake(snake)` | Insert a new head at `head + direction`; pop the tail unless food was eaten |
| `check_collisions(snake, other)` | Wall, self, and other snake → mark dead |
| `spawn_food(snakes)` | Pick a random cell not occupied by either snake (loop until valid) |
| `draw(screen, snakes, food, scores)` | Render the grid, snakes, food, and HUD |
| `play_round()` | The real-time loop; returns the round winner |

## Game loop pseudocode
```
initialize pygame, window, clock
create both snakes at opposite sides, facing each other's direction
spawn food
while round running:
    for each event:
        if window closed -> quit cleanly
        if key pressed -> update the correct snake's NEXT direction
    move both snakes
    if a snake's head is on food -> grow it, add point, spawn new food
    check collisions for both snakes
    if one or both dead -> decide round result, break
    draw everything
    clock.tick(SPEED)   # controls game speed
```

## Tricky logic worth explaining in your walkthrough
- **Reversal bug:** if a snake moving right gets "left," it would run into itself instantly. Ignore a new direction that's the exact opposite of the current one.
- **Head-on collision:** if both heads land on the same cell in the same frame, call it a draw.
- **Fair simultaneous movement:** move *both* snakes first, *then* check collisions, so Player 1 doesn't get an advantage from going first.
- **Growing:** "don't pop the tail this frame" is all growing really is.

## Scoring idea
- +1 per food eaten during the round
- +5 bonus for being the survivor
- The first player to 30 total points (or best of 5 rounds) wins the match

## Error handling to include
- `try/except ImportError` around `import pygame` with a helpful "run pip install pygame" message
- Food spawn never landing on a snake
- Closing the window mid-game exits cleanly (`pygame.quit()` in a `finally` block)
- Menu input such as "number of rounds" validated before the game starts

## Twists
- Speed increases as total food eaten goes up
- Power-ups: a "shrink opponent" item or a brief speed boost
- Wrap-around walls as a selectable mode

## Testing checklist
- [ ] Holding/mashing keys for both players works at once
- [ ] Instant reversal is blocked
- [ ] Head-on, head-to-body, wall, and self collisions all resolve correctly
- [ ] Food never spawns inside a snake

---

## Requirements Checklist

| Requirement | What graders look for |
|---|---|
| Loops | A main game loop (`while`), plus loops for boards, rounds, and input retries |
| Conditionals | Win/lose/draw checks, turn switching, input validation |
| Variable naming | `snake_case`, descriptive names (`current_player`, not `cp`), CONSTANTS in caps |
| Comments | A header docstring, a docstring on every function, and comments on tricky logic |
| Two-player | Both players actively play (hot-seat or simultaneous keys) |
| Scoring + winner | Scores tracked across rounds; a winner is announced at the end |
| Error handling | `try/except`, range checks, re-prompting until input is valid |

### Shared building blocks

**1. File header.** Put this at the top of every game file:
```python
"""
Game: <name>
Authors: <you>, <partner>
Course: CSIS ___ — Lab 8
Description: <one or two sentences on how the game works>
"""
```

**2. Constants.** Keep "magic numbers" out of your logic:
```python
BOARD_SIZE = 3
ROUNDS_TO_WIN = 3
```

**3. A reusable safe-input pattern.** Write your own version of this idea:
```
function get_int_in_range(prompt, low, high):
    loop forever:
        read text from user
        try converting to int
            if conversion fails -> print friendly error, continue
        if number outside low..high -> print error, continue
        return number
```

**4. Score tracking:**
```python
scores = {"Player 1": 0, "Player 2": 0}
```

**5. Play-again loop.** This wraps the whole game so scores carry across rounds:
```
while players want to keep playing:
    play one round -> returns winner (or None for draw)
    update scores
    show scoreboard
    ask "Play again? (y/n)" — validate y/n only
announce overall winner
```

**6. Recommended file structure:**
```
main()                 # entry point; play-again loop
play_round()           # one full round of the game
display_board()/draw   # output only
get_player_move()      # input + validation only
check_winner()         # logic only, returns a result
```
Keeping input, logic, and display in separate functions makes the code walkthrough much easier to present.

**7. Run guard** at the bottom of the file:
```python
if __name__ == "__main__":
    main()
```

---

## Presentation Prep

1. **Purpose & function (1 min):** rules in plain language, and how to win.
2. **Live demo (2–3 min):** play a short round, and *deliberately type bad input* to show the error handling.
3. **Code walkthrough (3–4 min):** follow the flow `main()` → `play_round()` → input/logic/display functions. Each partner presents the part they wrote.
4. **Take notes during feedback:** you're required to incorporate some changes before submitting, so keep a short list of the suggestions you implemented. It's worth mentioning in your submission.

## Suggested Week Plan

| Day | Goal |
|---|---|
| Fri–Sat | Pick a game, split the functions, and write the main loop skeleton |
| Sun–Mon | Core gameplay working end-to-end (ugly is fine) |
| Tue | Scoring, the play-again loop, and all the error handling |
| Wed | Comments, cleanup, and both partners testing each other's sections |
| Thu | Rehearse the presentation, and break your own game on purpose |
| Fri | Present, then apply the feedback before posting |
