# Battleship Lite: Lab 8 Build Guide

> **Important:** This guide gives you the *design*, *structure*, and *pseudocode*, not finished source code. The lab forbids copied code, so you and your partner should write every line yourselves. Small snippets here only show a Python technique, not a game.

**Difficulty:** ★★☆ · **Interface:** Terminal (Graphics.py optional) · **Build time:** 2–3 days

## Concept
A 6×6 grid with three ships per player (lengths 2, 3, and 3). Each player secretly places their ships, then they take turns firing at coordinates like `B4`. A hit scores points, sinking a ship scores a bonus, and sinking all enemy ships wins the round.

## Data structures
- `BOARD_SIZE = 6`, `SHIP_LENGTHS = [2, 3, 3]`
- Per player:
  - `ships`: a list of ships, where each ship is a list of `(row, col)` cells (or a set of remaining cells)
  - `shots_received`: a set of `(row, col)` already fired at
- Symbols: `"~"` water, `"X"` hit, `"o"` miss, `"S"` your own ship (shown only to its owner)
- Suggested structure: a dict per player, e.g. `{"name": ..., "ships": [...], "shots_received": set(), "score": 0}`

## Functions to write
| Function | Job |
|---|---|
| `parse_coordinate(text)` | Turn `"B4"` into `(1, 3)`; raise or return `None` if invalid |
| `get_coordinate(prompt)` | Loop until `parse_coordinate` succeeds |
| `get_orientation()` | Accept only `H` or `V` |
| `ship_fits(cells, existing_ships)` | Check that cells are in bounds and don't overlap other ships |
| `place_ships(player)` | Loop over `SHIP_LENGTHS`, asking start + orientation and re-prompting if invalid |
| `display_board(player, reveal_ships)` | Print the grid with row/column labels; hide ships for the opponent's view |
| `fire(attacker, defender, coord)` | Return `"miss"`, `"hit"`, or `"sunk"` and update state |
| `all_sunk(player)` | True if every ship has been fully hit |
| `pass_device(next_player)` | Clear the screen and wait for "press Enter" so players can't see each other's boards |

## Round pseudocode
```
for each player:
    pass device, then place ships (validated)
current = player 1, opponent = player 2
loop:
    pass device to current
    show opponent's board (ships hidden) and current's own board
    coord = get valid, not-already-fired coordinate
    result = fire(current, opponent, coord)
    update score based on result, print feedback
    if all_sunk(opponent): current wins round, break
    swap current and opponent
```

## Error handling to include
- Bad coordinate formats: `"Z9"`, `"44"`, `"B"`, `"b10"`, empty input
- Lowercase letters (normalize with `.upper()`)
- Ships extending off the board or overlapping
- Firing at a coordinate already fired at (warn and don't use up the turn)
- Orientation input other than H or V

## Scoring idea
- Hit = +1, sink = +3 bonus, winning the round = +5
- Best of 3 rounds or highest total score wins the match. Because points come from hits, a player who loses a round can still lead overall.

## Twists
- **Sonar:** once per game, reveal whether a 2×2 area contains any ship
- **Random placement option:** let a player type `R` to auto-place ships (`random` module + `ship_fits` in a loop)
- **Salvo:** the number of shots per turn equals the number of ships you have left

## Testing checklist
- [ ] Every edge and corner coordinate parses correctly
- [ ] Overlapping or out-of-bounds ships are rejected
- [ ] A "sunk" message appears only when the last cell of a ship is hit
- [ ] The opponent's ships are never revealed on their board

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
