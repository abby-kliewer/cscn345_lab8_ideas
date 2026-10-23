# Hangman (Head-to-Head): Lab 8 Build Guide

> **Important:** This guide gives you the *design*, *structure*, and *pseudocode*, not finished source code. The lab forbids copied code, so you and your partner should write every line yourselves. Small snippets here only show a Python technique, not a game.

**Difficulty:** ★☆☆ · **Interface:** Terminal · **Build time:** 1–2 days

## Concept
Players take turns as the word-setter and the guesser. The setter types a secret word, which is hidden with `getpass`, and the guesser has 6 lives. The guesser earns points for solving it, more if they use fewer misses. After N rounds each, the highest score wins.

## Data structures
- `secret_word`: a string, lowercase
- `guessed_letters`: a `set` (fast membership checks, no duplicates)
- `lives_remaining`: an int, starting at `MAX_LIVES = 6`
- `scores`: a dict

## Functions to write
| Function | Job |
|---|---|
| `get_secret_word(setter)` | Use `getpass.getpass()` so the word isn't shown; validate that it's letters only and 3–12 characters long |
| `clear_screen()` | Print blank lines (or use `os.system`) so the guesser can't scroll up |
| `display_progress(word, guessed)` | Build `"_ a _ _ m a _"` with a loop |
| `draw_hangman(lives)` | Print ASCII art. Design your own stages! |
| `get_guess(guessed)` | Validate exactly one letter that hasn't already been guessed |
| `is_solved(word, guessed)` | True if every letter in the word has been guessed |
| `calculate_points(lives_remaining)` | e.g. `10 + 5 * lives_remaining` |
| `play_round(setter, guesser)` | Run the guessing loop and return points earned |

## Round pseudocode
```
word = setter enters secret word (hidden)
clear screen
guessed = empty set, lives = MAX_LIVES
while lives > 0 and word not solved:
    show hangman + progress + guessed letters
    letter = get valid guess
    add to guessed
    if letter in word: "Hit!"
    else: lives -= 1, "Miss!"
if solved: award points to guesser
else: reveal word; optionally award setter a point for stumping them
```

## Error handling to include
- Secret word containing numbers, spaces, or symbols (use `.isalpha()`)
- A guess longer than one character or empty
- Repeated guesses (don't charge a life; just warn)
- Uppercase input (normalize with `.lower()`)
- `KeyboardInterrupt` (Ctrl+C) caught gracefully at the top level with a goodbye message

## Twists
- **Full-word guess:** the guesser can guess the whole word once for bonus points, but a wrong guess costs 2 lives
- **Hint system:** spend 5 points to reveal a letter
- **Category mode:** the setter also types a category ("animal"), which is shown to the guesser
- Steal round: if the guesser fails, the setter's points go up

## Testing checklist
- [ ] Words with repeated letters (e.g. "balloon") reveal all positions at once
- [ ] Losing at exactly 0 lives works
- [ ] Roles swap correctly every round
- [ ] The final winner and ties are both handled

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
