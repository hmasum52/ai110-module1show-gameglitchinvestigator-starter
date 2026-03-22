# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start
  (for example: "the hints were backwards").

When I first ran the game, the hints were backwards i.e. guessing too high said "Go HIGHER!" and too low said "Go LOWER!", which was the opposite of correct. The difficulty ranges were also swapped: Hard only went 1–50 while Normal went 1–100, making Hard easier than Normal. On top of that, attempts started at 1 instead of 0, so players always got one fewer attempt than intended. The "New Game" button didn't reset score, status, or history, so state leaked between games.

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

I used Claude (Claude Code) throughout this project. One correct suggestion was to move the `attempts += 1` increment to after input validation — Claude pointed out that invalid guesses were consuming attempts, and I verified the fix by submitting a non-numeric guess and confirming the attempt counter stayed the same. One misleading direction was around the `show_hint` / hint persistence issue: Claude initially suggested just calling `st.warning(message)` inline, which disappeared on rerun; I had to push further and use `st.session_state.last_message` to persist the hint across reruns, which I verified by submitting a guess and seeing the hint still displayed after the page refreshed.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

I decided a bug was fixed by running the game manually and confirming the specific bad behavior was gone. I also ran the pytest suite in `tests/test_game_logic.py` — the tests initially failed because they compared `check_guess(...)` directly to a string, but `check_guess` returns a tuple `(outcome, message)`. Updating the tests to unpack `outcome, _ = check_guess(...)` made all three tests pass and confirmed the hint logic was correct. Claude helped me understand why the tests were failing by spotting the tuple mismatch immediately.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Every time a user interacts with a Streamlit widget (clicking a button, typing in a box) the entire Python script runs again from top to bottom. This means any regular variable you set is lost on the next interaction. `st.session_state` is a dictionary that persists across these reruns, so it's the only way to remember things like the secret number, the attempt count, or the last hint message between clicks.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

I want to keep the habit of annotating bugs before fixing them (like commit `b946336`) i.e. writing down what's wrong and why forces me to understand the bug before blindly patching it. Next time I'd verify AI suggestions on a smaller, isolated example before applying them to the full codebase, rather than trusting the first answer. This project made me realize that AI-generated code can be a great starting point, but it often needs careful review and testing to ensure it fits the specific context and doesn't introduce new issues.