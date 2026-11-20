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
- **Score check (Plan tab):** Quick (15) or Thorough (30) mixed quiz in ACT order, optional Science
  (+5/+10). No feedback until the end. `assessScore()` weights hard questions more, maps weighted
  % correct to 1–36 with `SCORE_CURVE` (approximate, shaped like typical ACT tables), and reports
  a likely range; Composite = avg of E/M/R, STEM = avg of M/S. History in `P.assess`. Doesn't
  retire questions. It's an estimate, never presented as an official score.
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
- **Coins:** +5 per correct answer, 0 for wrong. The streak counts from the 1st correct; from the
  3rd in a row each correct answer pays double the previous one (10, 20, 40 …), capped at 2,560 per question; a wrong answer
  resets the streak (`coinsFor`). Score check: flat 5 per correct.
- **Secret vault (`renderVault`):** name "Gold Rush" → tap the header coin counter 5× in 4 s →
  code 2560 → button adds 500 coins per tap. Only affects that browser's coins.
- **Daily streak:** counts consecutive local days the app was opened.
- **STEM tab:** Math + Science practice (the enhanced ACT's STEM score), weak spots or full mix.
- 204 built-in ORIGINAL questions (`BUILTIN`, passages in `PASS`): English 50, Math 50, Science 50,
  Reading 54 (6 passages x 9 questions, like the real test). English is passage-based (essays with
  [[underlined]] portions, NO CHANGE first, plus a few "NOT/LEAST acceptable" items). Reading passages
  are ~550-700 words, start with a `~ ` source heading marking them as original, and one has a data
  table. Passages can draw graphs: `{{line Figure 1; x=..; y=..; Name: x,y x,y}}` or
  `{{bar Figure 1; x=..; y=..; Label: value; ...}}` (rendered by `figure()`). Topic mixes match ACT's
  published category ranges (see the `BP` comment). Never add real ACT test
  questions to the built-in bank. They're copyrighted; users may add their own copies only to
  their own private bank.
- **Solved questions are replaced (per user):** a correct answer retires the question (`P.retired`);
  Math/Science get a new template question (`MATH_T`, `SCI_T`, stored in `P.gen`); English/Reading
  come back when fewer than `REFILL_AT` remain. `AI_ENDPOINT` (empty) is the hook for a future
  server that writes questions. Never put an API key in the page.
- **Quality of life:** answer eliminator (✕ next to each choice), keyboard shortcuts (A–D / 1–4,
  Enter / N / →), the current session survives reloads (`actlab.session`), practice settings are
  remembered (`actlab.ui`), "Redo the N I missed" on the results screen, passage expand/jump on
  phones and side-by-side passage + question on screens ≥1000px, auto-scroll to the explanation,
  and a backup code (`ACTLAB1:` + base64 JSON) to move progress between devices (Progress tab).
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
