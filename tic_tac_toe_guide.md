# Tic-Tac-Toe: Lab 8 Build Guide

> **Important:** This guide gives you the *design*, *structure*, and *pseudocode*, not finished source code. The lab forbids copied code, so you and your partner should write every line yourselves. Small snippets here only show a Python technique, not a game.

**Difficulty:** ★☆☆ · **Interface:** Terminal or Graphics.py · **Build time:** 1–2 days

> **Heads-up:** Your instructor gave you `TTT.py` as a sample. If you build Tic-Tac-Toe, your version has to be clearly *different* from the sample. Use it only to understand ideas, and add at least one twist from the list below so the game stands on its own.

## Concept
Players alternate placing X and O on a 3×3 grid. Three in a row wins the round, and the first player to win 3 rounds wins the match.

## Data structures
- `board`: a list of 9 strings (`" "`, `"X"`, `"O"`) **or** a 3×3 list of lists
- `current_player`: `"X"` or `"O"`
- `scores`: a dict keyed by player name
- `WIN_LINES`: a constant list of all 8 winning index combinations

## Functions to write
| Function | Job |
|---|---|
| `create_board()` | Return a fresh empty board |
| `display_board(board)` | Print the grid with position numbers 1–9 in empty spots |
| `get_move(board, player)` | Ask for 1–9 and validate it; return an index |
| `check_winner(board)` | Loop through `WIN_LINES`; return `"X"`, `"O"`, `"Draw"`, or `None` |
| `switch_player(player)` | Return the other symbol |
| `play_round()` | Loop until there's a winner or draw; return the result |

## Round pseudocode
```
board = new empty board
current_player = starting player (alternate who starts each round)
loop:
    show board
    move = get valid move from current_player
    place symbol on board
    result = check winner
    if result is not None: return result
    switch player
```

## Error handling to include
- Non-numeric input such as `"abc"` → `ValueError`
- Number outside 1–9
- Square already taken
- Empty input (just pressing Enter)

## Twists to make it your own
- **Bigger board:** a 5×5 grid where 4 in a row wins (the win check must use loops, not a hardcoded list)
- **Ultimate mode:** each player gets one "erase" move per match to clear an opponent's mark
- **Disappearing marks:** each player can only have 3 marks on the board, so placing a 4th removes their oldest one
- Graphics.py: click detection maps a mouse click to a grid cell (`col = int(x // cell_size)`)

## Testing checklist
- [ ] All 8 win lines are detected (test each one)
- [ ] A full board with no winner gives a draw
- [ ] Bad input never crashes the game
- [ ] Scores persist across rounds, and the starting player alternates

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
