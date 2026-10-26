# Hangman (Head-to-Head)

A two-player, terminal-based Hangman game written in Python. One player types a secret word or phrase, and the other tries to guess it one letter at a time before the hangman is fully drawn. Then the players swap roles.

---

## How To

### How to Run

**Requirements**

- Python **3.10 or newer** (the game uses a `match` statement)
- A terminal (macOS Terminal, Linux shell, or the VS Code terminal)
- No extra libraries; the game only uses Python's built-in `os` module

**Steps**

1. Open a terminal in the folder that contains `hangman.py`.
2. Check your Python version:
   ```bash
   python3 --version
   ```
3. Start the game:
   ```bash
   python3 hangman.py
   ```
   On macOS or Linux you can also make the file executable and run it directly:
   ```bash
   chmod +x hangman.py
   ./hangman.py
   ```

> **Windows note:** The game clears the screen with the `clear` command, which Windows Command Prompt and PowerShell don't recognize. Run it in Git Bash or WSL, or change `os.system('clear')` to `os.system('cls')` in `clear_screen()`.

### How to Play

The game is played in **two rounds** at the same keyboard.

| Round | Sets the secret phrase | Guesses |
|---|---|---|
| 1 | Player 1 | Player 2 |
| 2 | Player 2 | Player 1 |

**Each round:**

1. **The setter enters a secret word or phrase.** The guesser should look away while it's typed. The screen clears as soon as the setter presses Enter, so the phrase doesn't stay visible.
2. **The board appears.** You'll see the gallows, a row of blanks (`_`) for each character in the phrase, and a list of wrong guesses. Spaces between words are already filled in.
3. **The guesser types one letter and presses Enter.**
   - If the letter is in the phrase, every spot where it appears is revealed.
   - If it isn't, a body part is added to the hangman and the letter is added to the **Mistakes** list.
4. **Keep guessing** until one of these happens:
   - **You win** if every blank is filled in. The game prints *"Great job, Player X!"*
   - **You lose** after **6 mistakes** (head, body, two arms, two legs). The game prints *"Good try, Player X"* and reveals the phrase.

After both rounds are played, the game ends.

### Example Board

```
   +-----+ 	 Mistakes: E T
   |     O
   |     |
   |
   |

   _ A N G M A N
```

---

## Good to Know

- **Guesses aren't case-sensitive.** Everything is converted to uppercase.
- **Only the first character counts.** Typing `abc` guesses `A`.
- **Punctuation and numbers in the phrase must be guessed too.** If the secret is `R2-D2`, the guesser has to guess `2` and `-` as well, so setters should stick to letters and spaces.
- **Repeating a wrong letter costs another mistake.** Check the Mistakes list before you guess.
- **Pressing Enter with no letter** counts as a mistake if the phrase has no spaces.
- **There's no running score.** Each round is won or lost on its own.

---

## How the Code Is Organized

| Function | What it does |
|---|---|
| `get_phrase(player)` | Asks the setter for the secret phrase and builds the blank `revealed` list |
| `get_guess(guesser)` | Reads one character from the guesser and uppercases it |
| `check_guess(phrase, guess, revealed)` | Reveals every matching letter and returns whether the guess was correct |
| `check_word(phrase, revealed)` | Returns `True` once the whole phrase is revealed |
| `show_hangman(mistakes, wrong_guesses)` | Clears the screen and draws the gallows for the current mistake count |
| `show_revealed(revealed)` | Prints the phrase with unguessed letters as `_` |
| `clear_screen()` | Clears the terminal |
| `take_turn(player)` | Runs one full round with `player` as the setter; returns `True` if the guesser won |

The bottom of the file calls `take_turn(1)` and then `take_turn(2)` to play both rounds.
