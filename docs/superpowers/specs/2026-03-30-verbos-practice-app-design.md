# Verbos Irregulares Practice App — Design Spec

**Date:** 2026-03-30
**Source material:** `verbos.pdf` — conjugation tables for 8 irregular Brazilian Portuguese verbs (SER, IR, ESTAR, TER, FAZER, SABER, QUERER, DAR), from the *Muito Prazer* textbook by Lexikos.

---

## Overview

A standalone single-file web app (`index.html`) for practicing irregular Portuguese verb conjugations. No server, no build step — open the file in a browser and it works. Progress is saved to `localStorage`.

---

## Screens

### 1. Session Setup

The user configures a practice session before starting:

- **Verb selection** — checkboxes for all 8 verbs (SER, IR, ESTAR, TER, FAZER, SABER, QUERER, DAR). All selected by default.
- **Tense selection** — checkboxes grouped by mood:
  - *Indicativo:* Presente, Pretérito Imperfeito, Pretérito Perfeito simples, Pretérito Perfeito composto, Pretérito mais-que-perfeito simples, Pretérito mais-que-perfeito composto, Futuro do Presente simples, Futuro do Presente composto, Futuro do Pretérito simples, Futuro do Pretérito composto
  - *Subjuntivo:* Presente, Pretérito Perfeito, Pretérito Imperfeito, Pretérito mais-que-perfeito, Futuro simples, Futuro composto
  - *Imperativo:* Afirmativo, Negativo
  - *Formas Nominais:* Infinitivo pessoal, Gerúndio, Particípio
- **Question count** — slider or number input (default: 20).
- **Start button** — disabled if no verbs or no tenses are selected.

Weak areas (from progress tracking) are visually flagged next to their tense/verb so the user can prioritize them.

### 2. Practice

Questions are drawn from the selected verbs × tenses, weighted by the user's error history (wrong answers appear more often). Each question is one of three randomly assigned modes:

**Mode A — Type the conjugation**
Prompt: `VERB · Tense · person`
User types the conjugated form. Press Enter or click Check. Diacritics are required (accented characters must match).

**Mode B — Identify the tense**
Prompt: `VERB · What tense is this?` + a display of 4–6 conjugated forms from a single tense.
User picks from 4 multiple-choice options (the correct tense + 3 distractors from the same verb or mood).

**Mode C — Fill the table**
Prompt: `VERB · Tense`
User fills in all person slots for that tense. Submitted all at once. Each slot scored individually.

After each answer (or table submission), immediate feedback is shown: correct forms are highlighted green, wrong answers show the correct form in red. A "Next" button advances to the next question.

Progress indicator shows `question N of total` throughout.

### 3. Results

After the final question:

- Score: `N / total` and percentage.
- **Needs work** list: tense+verb combinations with the most errors this session.
- Confirmation that progress has been saved to localStorage.
- Two actions: **Practice again** (same setup) or **New session** (back to Setup).

---

## Data Structure

All verb data is hardcoded in a JS object:

```js
const VERBS = {
  SER: {
    Indicativo: {
      Presente: ["sou", "és", "é", "somos", "sois", "são"],
      "Pretérito Imperfeito": ["era", "eras", "era", "éramos", "éreis", "eram"],
      // ...
    },
    Subjuntivo: { /* ... */ },
    Imperativo: {
      Afirmativo: ["—", "sê (tu)", "seja (você)", "sejamos (nós)", "sede (vós)", "sejam (vocês)"],
      Negativo: ["—", "não sejas (tu)", "não seja (você)", "não sejamos (nós)", "não sejais (vós)", "não sejam (vocês)"],
    },
    FormasNominais: {
      InfinitoPessoal: ["ser", "seres", "ser", "sermos", "serdes", "serem"],
      Gerundio: "sendo",
      Participio: "sido",
    },
  },
  IR: { /* ... */ },
  // ... all 8 verbs
}
```

Persons array index: `[0: eu, 1: tu, 2: ele/ela/você, 3: nós, 4: vós, 5: eles/vocês]`

Note: The PDF notes that `vós` (2nd person plural) is not used in Brazilian Portuguese conversation, and `tu` is only used in some regions. These forms are included in the data but can optionally be excluded from practice via a setup toggle.

---

## Progress Tracking

Stored in `localStorage` under key `verbos-progress` as a JSON object:

```js
{
  "SER|Indicativo|Presente|0": { correct: 5, incorrect: 1 },
  "SER|Indicativo|Presente|1": { correct: 3, incorrect: 4 },
  // key format: VERB|mood|tense|personIndex
}
```

**Weighting formula:** Items with a higher `incorrect / (correct + incorrect)` ratio are drawn more often. New items (unseen) get a neutral weight. This is a simple frequency-based system, not full spaced repetition.

---

## Architecture

Single `index.html` file with three sections:

- **`<style>`** — CSS for all screens. Clean, readable layout. No external stylesheets.
- **`<div id="app">`** — Container; JS swaps content between screens by replacing innerHTML or toggling visibility.
- **`<script>`** — All logic:
  - `VERBS` data object
  - `Progress` module (read/write localStorage)
  - `QuestionGenerator` — builds a weighted question queue from selected verbs/tenses
  - `UI` — renders each screen and handles events

No frameworks, no dependencies.

---

## Constraints & Notes

- Diacritics required in typed answers (é, ã, ô, etc.) — the app will show a hint about using the OS accent input if the user gets a wrong answer that differs only in accents.
- `vós` forms are included in data but a setup toggle can exclude them (default: excluded, matching Brazilian usage).
- The app is fully offline after first load.
