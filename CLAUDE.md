# ACT Study Lab

A single-page ACT study app (`index.html`: HTML, CSS and JavaScript in one file, no build step).
It was first built in claude.ai as an artifact. We're now continuing it here in Claude Code and
publishing it as a public website.

## What the app does
- **Plan tab:** predicts how many questions each topic gets on the enhanced ACT (format since
  spring 2025: English 50 Qs/35 min, Math 45/50, Reading 36/40, optional Science 40/40, 4 answer
  choices everywhere). Topic sizes start from ACT's published blueprint (`BP` object) and blend in
  the topics of official questions the user logs once there are 10+ in a section. A priority list
  ranks topics by frequency × the user's miss rate.
- **Practice tab:** Smart mix (weighted by priority), Mixed (all sections, random), single
  sections; difficulty filter (easy/medium/hard); 5/10/20 questions; optional real-ACT timer.
  Right answer → 1–2 sentence explanation (`x`). Wrong answer → 3–5 sentence explanation
  (`xl`, or the `XL` map for older built-ins).
- **Add tab:** user types in official questions (section, topic, difficulty, test date,
  passage, 4 choices, explanations). A "Patterns across real tests" table shows topic counts by
  test date.
- **Store tab:** coins buy color themes, avatars and avatar frames; display name.
- **Progress tab:** coins, questions done, best streak, daily-streak card, badges (correct-answer
  totals, answer streaks, daily streaks), accuracy by topic, recent sessions.
- **Coins:** +10 correct, +1 per attempt; streak bonus every 3 in a row that doubles
  (5, 10, 20 … capped at 640).
- **Daily streak:** counts consecutive local days the app was opened.
- **STEM tab:** Math + Science practice (the enhanced ACT's STEM score), weak spots or full mix.
- 200 built-in ORIGINAL questions, 50 per section (`BUILTIN`, passages in `PASS`). English is
  passage-based (essays with [[underlined]] portions, NO CHANGE first). Never add real ACT test
  questions to the built-in bank. They're copyrighted; users may add their own copies only to
  their own private bank.
- **Solved questions are replaced (per user):** a correct answer retires the question (`P.retired`);
  Math/Science get a new template question (`MATH_T`, `SCI_T`, stored in `P.gen`); English/Reading
  come back when fewer than `REFILL_AT` remain. `AI_ENDPOINT` (empty) is the hook for a future
  server that writes questions. Never put an API key in the page.
- **ACT-style checker (`actCheck`):** every generated/AI question must pass it; built-ins were
  screened with it. Open `index.html#check` for a report of the whole bank.

## Things that only worked inside claude.ai (must be replaced here)
1. **`claude.use("db")` / `claude.use("user")`:** cloud saving of the profile
   (`data/users/<id>/profile`) and the custom question collection. Outside claude.ai these
   resolve to nothing, and the app already falls back to `localStorage` (`localLoad`/`localSave`).
   If we want accounts and cross-device sync, add a real backend (e.g. Firebase or Supabase).
2. **`claude.use("sample")`:** used by "Get more questions" (`generate()`) and by detailed
   explanations for user-added questions (`fetchLong()`). To keep these, add a small server or
   serverless function that calls the Claude API. NEVER put an API key in the browser code or the
   public site.

## Working rules
- Keep it a static site that runs by opening `index.html` (or `python3 -m http.server 8000`)
  unless we deliberately add a backend.
- Use git and commit after each feature the user approves.
- The user is a student who previews changes in the browser at http://localhost:8000. After each
  change, say what to refresh and what to look for.
