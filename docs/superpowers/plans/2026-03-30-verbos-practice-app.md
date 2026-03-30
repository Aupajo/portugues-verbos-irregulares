# Verbos Irregulares Practice App — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a standalone `index.html` app for practicing conjugations of 8 irregular Brazilian Portuguese verbs, with 3 quiz modes and localStorage progress tracking.

**Architecture:** Single HTML file — all CSS in `<style>`, all JS in `<script>`, no external dependencies. Pure logic (data, progress, question generation, answer checking) is testable via Node.js (`node tests/logic.test.js`). UI renders by replacing `innerHTML` of `<div id="app">` when switching screens.

**Tech Stack:** Vanilla HTML/CSS/JS, localStorage, Node.js (tests only)

---

## File Map

| File | Purpose |
|------|---------|
| `index.html` | Complete standalone app — all CSS + JS inline |
| `tests/logic.test.js` | Node.js tests for pure logic (no DOM) |

---

## Task 1: Verb data + scaffold

**Files:**
- Create: `index.html`
- Create: `tests/logic.test.js`

- [ ] **Step 1: Write the failing test for verb data**

Create `tests/logic.test.js`:

```js
// tests/logic.test.js
// Pure logic tests — run with: node tests/logic.test.js

function assert(condition, message) {
  if (!condition) { console.error(`FAIL: ${message}`); process.exitCode = 1; }
  else { console.log(`PASS: ${message}`); }
}

// ── Paste VERBS and PERSONS from index.html here for testing ──
// (kept in sync manually; tests run against the same data)

const PERSONS = ["eu", "tu", "ele / ela / você", "nós", "vós", "eles / elas / vocês"];

const TENSES_WITH_SIX_FORMS = [
  "Presente", "Pretérito Imperfeito", "Pretérito Perfeito simples",
  "Pretérito Perfeito composto", "Pretérito mais-que-perfeito simples",
  "Pretérito mais-que-perfeito composto", "Futuro do Presente simples",
  "Futuro do Presente composto", "Futuro do Pretérito simples",
  "Futuro do Pretérito composto",
  // Subjuntivo
  "Presente_subj", "Pretérito Perfeito_subj", "Pretérito Imperfeito_subj",
  "Pretérito mais-que-perfeito_subj", "Futuro simples_subj", "Futuro composto_subj",
  // Imperativo
  "Afirmativo", "Negativo",
  // Formas Nominais
  "Infinito pessoal",
];

// VERBS will be pasted here in Step 3
// For now just test that the file can be required after data is added

console.log("Test file created — add VERBS data to complete tests");
```

- [ ] **Step 2: Run test to confirm it runs**

```
node tests/logic.test.js
```
Expected: prints "Test file created — add VERBS data to complete tests"

- [ ] **Step 3: Create index.html with full VERBS data**

Create `index.html`:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Verbos Irregulares</title>
  <style>/* CSS added in Task 4 */</style>
</head>
<body>
  <div id="app"></div>
  <script>
(function () {

const PERSONS = ["eu", "tu", "ele / ela / você", "nós", "vós", "eles / elas / vocês"];

// Each verb: { Indicativo, Subjuntivo, Imperativo, FormasNominais }
// Arrays of 6 strings: [eu, tu, ele/ela/você, nós, vós, eles/elas/vocês]
// Imperativo eu slot is "—" (no imperative for 1st person singular)
// Formas Nominais: Gerundio and Participio are strings; Infinito pessoal is array of 6

const VERBS = {
  SER: {
    Indicativo: {
      "Presente":                         ["sou","és","é","somos","sois","são"],
      "Pretérito Imperfeito":             ["era","eras","era","éramos","éreis","eram"],
      "Pretérito Perfeito simples":       ["fui","foste","foi","fomos","fostes","foram"],
      "Pretérito Perfeito composto":      ["tenho sido","tens sido","tem sido","temos sido","tendes sido","têm sido"],
      "Pretérito mais-que-perfeito simples":  ["fora","foras","fora","fôramos","fôreis","foram"],
      "Pretérito mais-que-perfeito composto": ["tinha sido","tinhas sido","tinha sido","tínhamos sido","tínheis sido","tinham sido"],
      "Futuro do Presente simples":       ["serei","serás","será","seremos","sereis","serão"],
      "Futuro do Presente composto":      ["terei sido","terás sido","terá sido","teremos sido","tereis sido","terão sido"],
      "Futuro do Pretérito simples":      ["seria","serias","seria","seríamos","seríeis","seriam"],
      "Futuro do Pretérito composto":     ["teria sido","terias sido","teria sido","teríamos sido","teríeis sido","teriam sido"],
    },
    Subjuntivo: {
      "Presente":                         ["seja","sejas","seja","sejamos","sejais","sejam"],
      "Pretérito Perfeito":               ["tenha sido","tenhas sido","tenha sido","tenhamos sido","tenhais sido","tenham sido"],
      "Pretérito Imperfeito":             ["fosse","fosses","fosse","fôssemos","fôsseis","fossem"],
      "Pretérito mais-que-perfeito":      ["tivesse sido","tivesses sido","tivesse sido","tivéssemos sido","tivésseis sido","tivessem sido"],
      "Futuro simples":                   ["for","fores","for","formos","fordes","forem"],
      "Futuro composto":                  ["tiver sido","tiveres sido","tiver sido","tivermos sido","tiverdes sido","tiverem sido"],
    },
    Imperativo: {
      "Afirmativo": ["—","sê (tu)","seja (você)","sejamos (nós)","sede (vós)","sejam (vocês)"],
      "Negativo":   ["—","não sejas (tu)","não seja (você)","não sejamos (nós)","não sejais (vós)","não sejam (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["ser","seres","ser","sermos","serdes","serem"],
      "Gerúndio":  "sendo",
      "Particípio": "sido",
    },
  },

  IR: {
    Indicativo: {
      "Presente":                         ["vou","vais","vai","vamos","ides","vão"],
      "Pretérito Imperfeito":             ["ia","ias","ia","íamos","íeis","iam"],
      "Pretérito Perfeito simples":       ["fui","foste","foi","fomos","fostes","foram"],
      "Pretérito Perfeito composto":      ["tenho ido","tens ido","tem ido","temos ido","tendes ido","têm ido"],
      "Pretérito mais-que-perfeito simples":  ["fora","foras","fora","fôramos","fôreis","foram"],
      "Pretérito mais-que-perfeito composto": ["tinha ido","tinhas ido","tinha ido","tínhamos ido","tínheis ido","tinham ido"],
      "Futuro do Presente simples":       ["irei","irás","irá","iremos","ireis","irão"],
      "Futuro do Presente composto":      ["terei ido","terás ido","terá ido","teremos ido","tereis ido","terão ido"],
      "Futuro do Pretérito simples":      ["iria","irias","iria","iríamos","iríeis","iriam"],
      "Futuro do Pretérito composto":     ["teria ido","terias ido","teria ido","teríamos ido","teríeis ido","teriam ido"],
    },
    Subjuntivo: {
      "Presente":                         ["vá","vás","vá","vamos","vades","vão"],
      "Pretérito Perfeito":               ["tenha ido","tenhas ido","tenha ido","tenhamos ido","tenhais ido","tenham ido"],
      "Pretérito Imperfeito":             ["fosse","fosses","fosse","fôssemos","fôsseis","fossem"],
      "Pretérito mais-que-perfeito":      ["tivesse ido","tivesses ido","tivesse ido","tivéssemos ido","tivésseis ido","tivessem ido"],
      "Futuro simples":                   ["for","fores","for","formos","fordes","forem"],
      "Futuro composto":                  ["tiver ido","tiveres ido","tiver ido","tivermos ido","tiverdes ido","tiverem ido"],
    },
    Imperativo: {
      "Afirmativo": ["—","vai (tu)","vá (você)","vamos (nós)","ide (vós)","vão (vocês)"],
      "Negativo":   ["—","não vás (tu)","não vá (você)","não vamos (nós)","não vades (vós)","não vão (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["ir","ires","ir","irmos","irdes","irem"],
      "Gerúndio":  "indo",
      "Particípio": "ido",
    },
  },

  ESTAR: {
    Indicativo: {
      "Presente":                         ["estou","estás","está","estamos","estais","estão"],
      "Pretérito Imperfeito":             ["estava","estavas","estava","estávamos","estáveis","estavam"],
      "Pretérito Perfeito simples":       ["estive","estiveste","esteve","estivemos","estivestes","estiveram"],
      "Pretérito Perfeito composto":      ["tenho estado","tens estado","tem estado","temos estado","tendes estado","têm estado"],
      "Pretérito mais-que-perfeito simples":  ["estivera","estiveras","estivera","estivéramos","estivéreis","estiveram"],
      "Pretérito mais-que-perfeito composto": ["tinha estado","tinhas estado","tinha estado","tínhamos estado","tínheis estado","tinham estado"],
      "Futuro do Presente simples":       ["estarei","estarás","estará","estaremos","estareis","estarão"],
      "Futuro do Presente composto":      ["terei estado","terás estado","terá estado","teremos estado","tereis estado","terão estado"],
      "Futuro do Pretérito simples":      ["estaria","estarias","estaria","estaríamos","estaríeis","estariam"],
      "Futuro do Pretérito composto":     ["teria estado","terias estado","teria estado","teríamos estado","teríeis estado","teriam estado"],
    },
    Subjuntivo: {
      "Presente":                         ["esteja","estejas","esteja","estejamos","estejais","estejam"],
      "Pretérito Perfeito":               ["tenha estado","tenhas estado","tenha estado","tenhamos estado","tenhais estado","tenham estado"],
      "Pretérito Imperfeito":             ["estivesse","estivesses","estivesse","estivéssemos","estivésseis","estivessem"],
      "Pretérito mais-que-perfeito":      ["tivesse estado","tivesses estado","tivesse estado","tivéssemos estado","tivésseis estado","tivessem estado"],
      "Futuro simples":                   ["estiver","estiveres","estiver","estivermos","estiverdes","estiverem"],
      "Futuro composto":                  ["tiver estado","tiveres estado","tiver estado","tivermos estado","tiverdes estado","tiverem estado"],
    },
    Imperativo: {
      "Afirmativo": ["—","está (tu)","esteja (você)","estejamos (nós)","estai (vós)","estejam (vocês)"],
      "Negativo":   ["—","não estejas (tu)","não esteja (você)","não estejamos (nós)","não estejais (vós)","não estejam (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["estar","estares","estar","estarmos","estardes","estarem"],
      "Gerúndio":  "estando",
      "Particípio": "estado",
    },
  },

  TER: {
    Indicativo: {
      "Presente":                         ["tenho","tens","tem","temos","tendes","têm"],
      "Pretérito Imperfeito":             ["tinha","tinhas","tinha","tínhamos","tínheis","tinham"],
      "Pretérito Perfeito simples":       ["tive","tiveste","teve","tivemos","tivestes","tiveram"],
      "Pretérito Perfeito composto":      ["tenho tido","tens tido","tem tido","temos tido","tendes tido","têm tido"],
      "Pretérito mais-que-perfeito simples":  ["tivera","tiveras","tivera","tivéramos","tivéreis","tiveram"],
      "Pretérito mais-que-perfeito composto": ["tinha tido","tinhas tido","tinha tido","tínhamos tido","tínheis tido","tinham tido"],
      "Futuro do Presente simples":       ["terei","terás","terá","teremos","tereis","terão"],
      "Futuro do Presente composto":      ["terei tido","terás tido","terá tido","teremos tido","tereis tido","terão tido"],
      "Futuro do Pretérito simples":      ["teria","terias","teria","teríamos","teríeis","teriam"],
      "Futuro do Pretérito composto":     ["teria tido","terias tido","teria tido","teríamos tido","teríeis tido","teriam tido"],
    },
    Subjuntivo: {
      "Presente":                         ["tenha","tenhas","tenha","tenhamos","tenhais","tenham"],
      "Pretérito Perfeito":               ["tenha tido","tenhas tido","tenha tido","tenhamos tido","tenhais tido","tenham tido"],
      "Pretérito Imperfeito":             ["tivesse","tivesses","tivesse","tivéssemos","tivésseis","tivessem"],
      "Pretérito mais-que-perfeito":      ["tivesse tido","tivesses tido","tivesse tido","tivéssemos tido","tivésseis tido","tivessem tido"],
      "Futuro simples":                   ["tiver","tiveres","tiver","tivermos","tiverdes","tiverem"],
      "Futuro composto":                  ["tiver tido","tiveres tido","tiver tido","tivermos tido","tiverdes tido","tiverem tido"],
    },
    Imperativo: {
      "Afirmativo": ["—","tem (tu)","tenha (você)","tenhamos (nós)","tende (vós)","tenham (vocês)"],
      "Negativo":   ["—","não tenhas (tu)","não tenha (você)","não tenhamos (nós)","não tenhais (vós)","não tenham (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["ter","teres","ter","termos","terdes","terem"],
      "Gerúndio":  "tendo",
      "Particípio": "tido",
    },
  },

  FAZER: {
    Indicativo: {
      "Presente":                         ["faço","fazes","faz","fazemos","fazeis","fazem"],
      "Pretérito Imperfeito":             ["fazia","fazias","fazia","fazíamos","fazíeis","faziam"],
      "Pretérito Perfeito simples":       ["fiz","fizeste","fez","fizemos","fizestes","fizeram"],
      "Pretérito Perfeito composto":      ["tenho feito","tens feito","tem feito","temos feito","tendes feito","têm feito"],
      "Pretérito mais-que-perfeito simples":  ["fizera","fizeras","fizera","fizéramos","fizéreis","fizeram"],
      "Pretérito mais-que-perfeito composto": ["tinha feito","tinhas feito","tinha feito","tínhamos feito","tínheis feito","tinham feito"],
      "Futuro do Presente simples":       ["farei","farás","fará","faremos","fareis","farão"],
      "Futuro do Presente composto":      ["terei feito","terás feito","terá feito","teremos feito","tereis feito","terão feito"],
      "Futuro do Pretérito simples":      ["faria","farias","faria","faríamos","faríeis","fariam"],
      "Futuro do Pretérito composto":     ["teria feito","terias feito","teria feito","teríamos feito","teríeis feito","teriam feito"],
    },
    Subjuntivo: {
      "Presente":                         ["faça","faças","faça","façamos","façais","façam"],
      "Pretérito Perfeito":               ["tenha feito","tenhas feito","tenha feito","tenhamos feito","tenhais feito","tenham feito"],
      "Pretérito Imperfeito":             ["fizesse","fizesses","fizesse","fizéssemos","fizésseis","fizessem"],
      "Pretérito mais-que-perfeito":      ["tivesse feito","tivesses feito","tivesse feito","tivéssemos feito","tivésseis feito","tivessem feito"],
      "Futuro simples":                   ["fizer","fizeres","fizer","fizermos","fizerdes","fizerem"],
      "Futuro composto":                  ["tiver feito","tiveres feito","tiver feito","tivermos feito","tiverdes feito","tiverem feito"],
    },
    Imperativo: {
      "Afirmativo": ["—","faz / faze (tu)","faça (você)","façamos (nós)","fazei (vós)","façam (vocês)"],
      "Negativo":   ["—","não faças (tu)","não faça (você)","não façamos (nós)","não façais (vós)","não façam (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["fazer","fazeres","fazer","fazermos","fazerdes","fazerem"],
      "Gerúndio":  "fazendo",
      "Particípio": "feito",
    },
  },

  SABER: {
    Indicativo: {
      "Presente":                         ["sei","sabes","sabe","sabemos","sabeis","sabem"],
      "Pretérito Imperfeito":             ["sabia","sabias","sabia","sabíamos","sabíeis","sabiam"],
      "Pretérito Perfeito simples":       ["soube","soubeste","soube","soubemos","soubestes","souberam"],
      "Pretérito Perfeito composto":      ["tenho sabido","tens sabido","tem sabido","temos sabido","tendes sabido","têm sabido"],
      "Pretérito mais-que-perfeito simples":  ["soubera","souberas","soubera","soubéramos","soubéreis","souberam"],
      "Pretérito mais-que-perfeito composto": ["tinha sabido","tinhas sabido","tinha sabido","tínhamos sabido","tínheis sabido","tinham sabido"],
      "Futuro do Presente simples":       ["saberei","saberás","saberá","saberemos","sabereis","saberão"],
      "Futuro do Presente composto":      ["terei sabido","terás sabido","terá sabido","teremos sabido","tereis sabido","terão sabido"],
      "Futuro do Pretérito simples":      ["saberia","saberias","saberia","saberíamos","saberíeis","saberiam"],
      "Futuro do Pretérito composto":     ["teria sabido","terias sabido","teria sabido","teríamos sabido","teríeis sabido","teriam sabido"],
    },
    Subjuntivo: {
      "Presente":                         ["saiba","saibas","saiba","saibamos","saibais","saibam"],
      "Pretérito Perfeito":               ["tenha sabido","tenhas sabido","tenha sabido","tenhamos sabido","tenhais sabido","tenham sabido"],
      "Pretérito Imperfeito":             ["soubesse","soubesses","soubesse","soubéssemos","soubésseis","soubessem"],
      "Pretérito mais-que-perfeito":      ["tivesse sabido","tivesses sabido","tivesse sabido","tivéssemos sabido","tivésseis sabido","tivessem sabido"],
      "Futuro simples":                   ["souber","souberes","souber","soubermos","souberdes","souberem"],
      "Futuro composto":                  ["tiver sabido","tiveres sabido","tiver sabido","tivermos sabido","tiverdes sabido","tiverem sabido"],
    },
    Imperativo: {
      "Afirmativo": ["—","sabe (tu)","saiba (você)","saibamos (nós)","sabei (vós)","saibam (vocês)"],
      "Negativo":   ["—","não saibas (tu)","não saiba (você)","não saibamos (nós)","não saibais (vós)","não saibam (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["saber","saberes","saber","sabermos","saberdes","saberem"],
      "Gerúndio":  "sabendo",
      "Particípio": "sabido",
    },
  },

  QUERER: {
    Indicativo: {
      "Presente":                         ["quero","queres","quer","queremos","quereis","querem"],
      "Pretérito Imperfeito":             ["queria","querias","queria","queríamos","queríeis","queriam"],
      "Pretérito Perfeito simples":       ["quis","quiseste","quis","quisemos","quisestes","quiseram"],
      "Pretérito Perfeito composto":      ["tenho querido","tens querido","tem querido","temos querido","tendes querido","têm querido"],
      "Pretérito mais-que-perfeito simples":  ["quisera","quiseras","quisera","quiséramos","quiséreis","quiseram"],
      "Pretérito mais-que-perfeito composto": ["tinha querido","tinhas querido","tinha querido","tínhamos querido","tínheis querido","tinham querido"],
      "Futuro do Presente simples":       ["quererei","quererás","quererá","quereremos","querereis","quererão"],
      "Futuro do Presente composto":      ["terei querido","terás querido","terá querido","teremos querido","tereis querido","terão querido"],
      "Futuro do Pretérito simples":      ["quereria","quererias","quereria","quereríamos","quereríeis","quereriam"],
      "Futuro do Pretérito composto":     ["teria querido","terias querido","teria querido","teríamos querido","teríeis querido","teriam querido"],
    },
    Subjuntivo: {
      "Presente":                         ["queira","queiras","queira","queiramos","queirais","queiram"],
      "Pretérito Perfeito":               ["tenha querido","tenhas querido","tenha querido","tenhamos querido","tenhais querido","tenham querido"],
      "Pretérito Imperfeito":             ["quisesse","quisesses","quisesse","quiséssemos","quisésseis","quisessem"],
      "Pretérito mais-que-perfeito":      ["tivesse querido","tivesses querido","tivesse querido","tivéssemos querido","tivésseis querido","tivessem querido"],
      "Futuro simples":                   ["quiser","quiseres","quiser","quisermos","quiserdes","quiserem"],
      "Futuro composto":                  ["tiver querido","tiveres querido","tiver querido","tivermos querido","tiverdes querido","tiverem querido"],
    },
    Imperativo: {
      "Afirmativo": ["—","quer / quere (tu)","queira (você)","queiramos (nós)","querei (vós)","queiram (vocês)"],
      "Negativo":   ["—","não queiras (tu)","não queira (você)","não queiramos (nós)","não queirais (vós)","não queiram (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["querer","quereres","querer","querermos","quererdes","quererem"],
      "Gerúndio":  "querendo",
      "Particípio": "querido",
    },
  },

  DAR: {
    Indicativo: {
      "Presente":                         ["dou","dás","dá","damos","dais","dão"],
      "Pretérito Imperfeito":             ["dava","davas","dava","dávamos","dáveis","davam"],
      "Pretérito Perfeito simples":       ["dei","deste","deu","demos","destes","deram"],
      "Pretérito Perfeito composto":      ["tenho dado","tens dado","tem dado","temos dado","tendes dado","têm dado"],
      "Pretérito mais-que-perfeito simples":  ["dera","deras","dera","déramos","déreis","deram"],
      "Pretérito mais-que-perfeito composto": ["tinha dado","tinhas dado","tinha dado","tínhamos dado","tínheis dado","tinham dado"],
      "Futuro do Presente simples":       ["darei","darás","dará","daremos","dareis","darão"],
      "Futuro do Presente composto":      ["terei dado","terás dado","terá dado","teremos dado","tereis dado","terão dado"],
      "Futuro do Pretérito simples":      ["daria","darias","daria","daríamos","daríeis","dariam"],
      "Futuro do Pretérito composto":     ["teria dado","terias dado","teria dado","teríamos dado","teríeis dado","teriam dado"],
    },
    Subjuntivo: {
      "Presente":                         ["dê","dês","dê","demos","deis","deem"],
      "Pretérito Perfeito":               ["tenha dado","tenhas dado","tenha dado","tenhamos dado","tenhais dado","tenham dado"],
      "Pretérito Imperfeito":             ["desse","desses","desse","déssemos","désseis","dessem"],
      "Pretérito mais-que-perfeito":      ["tivesse dado","tivesses dado","tivesse dado","tivéssemos dado","tivésseis dado","tivessem dado"],
      "Futuro simples":                   ["der","deres","der","dermos","derdes","derem"],
      "Futuro composto":                  ["tiver dado","tiveres dado","tiver dado","tivermos dado","tiverdes dado","tiverem dado"],
    },
    Imperativo: {
      "Afirmativo": ["—","dá (tu)","dê (você)","demos (nós)","dai (vós)","deem (vocês)"],
      "Negativo":   ["—","não dês (tu)","não dê (você)","não demos (nós)","não deis (vós)","não deem (vocês)"],
    },
    FormasNominais: {
      "Infinito pessoal": ["dar","dares","dar","darmos","dardes","darem"],
      "Gerúndio":  "dando",
      "Particípio": "dado",
    },
  },
};

// App placeholder — screens added in later tasks
document.getElementById('app').textContent = 'Loading…';

})();
  </script>
</body>
</html>
```

- [ ] **Step 4: Update test file to validate VERBS structure**

Replace `tests/logic.test.js` with:

```js
// tests/logic.test.js — run with: node tests/logic.test.js

function assert(condition, message) {
  if (!condition) { console.error(`FAIL: ${message}`); process.exitCode = 1; }
  else { console.log(`PASS: ${message}`); }
}

// ── Copy VERBS and PERSONS from index.html ──
const PERSONS = ["eu", "tu", "ele / ela / você", "nós", "vós", "eles / elas / vocês"];

// [paste VERBS constant here — keep in sync with index.html]
// For CI purposes, require the verbs from a shared file is not needed;
// the test validates structure manually pasted.
// (In practice: copy the VERBS block from index.html here before running)

const VERB_NAMES = ["SER","IR","ESTAR","TER","FAZER","SABER","QUERER","DAR"];
const INDICATIVO_TENSES = [
  "Presente","Pretérito Imperfeito","Pretérito Perfeito simples",
  "Pretérito Perfeito composto","Pretérito mais-que-perfeito simples",
  "Pretérito mais-que-perfeito composto","Futuro do Presente simples",
  "Futuro do Presente composto","Futuro do Pretérito simples","Futuro do Pretérito composto",
];
const SUBJUNTIVO_TENSES = [
  "Presente","Pretérito Perfeito","Pretérito Imperfeito",
  "Pretérito mais-que-perfeito","Futuro simples","Futuro composto",
];

// Validate all 8 verbs have complete data
VERB_NAMES.forEach(v => {
  assert(VERBS[v] !== undefined, `${v} exists`);
  INDICATIVO_TENSES.forEach(t => {
    const forms = VERBS[v].Indicativo[t];
    assert(Array.isArray(forms) && forms.length === 6,
      `${v} Indicativo "${t}" has 6 forms`);
    forms.forEach((f, i) => assert(typeof f === 'string' && f.length > 0,
      `${v} Indicativo "${t}" [${i}] is non-empty string`));
  });
  SUBJUNTIVO_TENSES.forEach(t => {
    const forms = VERBS[v].Subjuntivo[t];
    assert(Array.isArray(forms) && forms.length === 6,
      `${v} Subjuntivo "${t}" has 6 forms`);
  });
  assert(Array.isArray(VERBS[v].Imperativo["Afirmativo"]) && VERBS[v].Imperativo["Afirmativo"].length === 6,
    `${v} Imperativo Afirmativo has 6 forms`);
  assert(Array.isArray(VERBS[v].Imperativo["Negativo"]) && VERBS[v].Imperativo["Negativo"].length === 6,
    `${v} Imperativo Negativo has 6 forms`);
  assert(Array.isArray(VERBS[v].FormasNominais["Infinito pessoal"]) && VERBS[v].FormasNominais["Infinito pessoal"].length === 6,
    `${v} FormasNominais "Infinito pessoal" has 6 forms`);
  assert(typeof VERBS[v].FormasNominais["Gerúndio"] === 'string',
    `${v} FormasNominais Gerúndio is string`);
  assert(typeof VERBS[v].FormasNominais["Particípio"] === 'string',
    `${v} FormasNominais Particípio is string`);
});

console.log('\nDone.');
```

- [ ] **Step 5: Copy VERBS block into test file, then run**

Open `tests/logic.test.js`, paste the `VERBS` constant (copied verbatim from `index.html`) right after the `PERSONS` definition. Then:

```
node tests/logic.test.js
```

Expected: all PASS lines (128+ checks), no FAIL

- [ ] **Step 6: Commit**

```bash
git add index.html tests/logic.test.js
git commit -m "feat: add verb data for all 8 irregular verbs + structure tests"
```

---

## Task 2: Progress module

**Files:**
- Modify: `index.html` — add Progress module inside the IIFE
- Modify: `tests/logic.test.js` — add progress tests

- [ ] **Step 1: Write failing tests for Progress**

Add to the bottom of `tests/logic.test.js` (before `console.log('\nDone.')`) :

```js
// ── Progress module tests ──
// Paste Progress object here from index.html after implementing it

// Stub localStorage for Node.js
const _store = {};
const localStorage = {
  getItem: k => _store[k] ?? null,
  setItem: (k, v) => { _store[k] = v; },
};

// [paste Progress here after Task 2 Step 3]

// Placeholder — will fail until Progress is added
assert(typeof Progress !== 'undefined', 'Progress module exists');
assert(typeof Progress.record === 'function', 'Progress.record is function');
assert(typeof Progress.getWeight === 'function', 'Progress.getWeight is function');
assert(typeof Progress.getAll === 'function', 'Progress.getAll is function');

Progress.record('SER|Indicativo|Presente', true);
Progress.record('SER|Indicativo|Presente', true);
Progress.record('SER|Indicativo|Presente', false);
const w1 = Progress.getWeight('SER|Indicativo|Presente');
assert(w1 > 1, 'Weight increases after incorrect answers');
assert(w1 < 2, 'Weight stays below 2 after 1 of 3 wrong');

Progress.record('IR|Indicativo|Presente', true);
Progress.record('IR|Indicativo|Presente', true);
const w2 = Progress.getWeight('IR|Indicativo|Presente');
assert(w2 < w1, 'All-correct item has lower weight than mixed item');

const unseen = Progress.getWeight('DAR|Subjuntivo|Presente');
assert(unseen === 1, 'Unseen item has weight 1');
```

- [ ] **Step 2: Run test to confirm it fails**

```
node tests/logic.test.js
```
Expected: `FAIL: Progress module exists` (and subsequent failures)

- [ ] **Step 3: Implement Progress module in index.html**

Add after the `VERBS` constant and before `document.getElementById`:

```js
// Progress: track correct/incorrect per (verb|mood|tense) key
const Progress = (() => {
  const STORAGE_KEY = 'verbos-progress';

  function load() {
    try { return JSON.parse(localStorage.getItem(STORAGE_KEY)) || {}; }
    catch { return {}; }
  }

  function save(data) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
  }

  function record(key, correct) {
    const data = load();
    if (!data[key]) data[key] = { correct: 0, incorrect: 0 };
    if (correct) data[key].correct++; else data[key].incorrect++;
    save(data);
  }

  function getWeight(key) {
    const data = load();
    const p = data[key];
    if (!p) return 1;
    const total = p.correct + p.incorrect;
    if (total === 0) return 1;
    return 1 + (p.incorrect / total) * 2; // range: 1.0 – 3.0
  }

  function getAll() {
    return load();
  }

  return { record, getWeight, getAll };
})();
```

- [ ] **Step 4: Paste Progress into test file and run**

Copy the `Progress` object (the `const Progress = (() => { ... })();` block) from `index.html` and paste it after the `localStorage` stub in `tests/logic.test.js`. Then:

```
node tests/logic.test.js
```
Expected: all PASS

- [ ] **Step 5: Commit**

```bash
git add index.html tests/logic.test.js
git commit -m "feat: add Progress module with localStorage tracking and weight scoring"
```

---

## Task 3: Question generator + answer checker

**Files:**
- Modify: `index.html` — add `buildQueue()` and `checkAnswer()`
- Modify: `tests/logic.test.js` — add queue + answer tests

- [ ] **Step 1: Write failing tests**

Add to `tests/logic.test.js` before the final `console.log`:

```js
// ── Question generator tests ──
// [paste buildQueue and checkAnswer after implementing in index.html]

assert(typeof buildQueue === 'function', 'buildQueue exists');
assert(typeof checkAnswer === 'function', 'checkAnswer exists');

// Answer checker
assert(checkAnswer('sou', 'sou') === 'correct', 'exact match is correct');
assert(checkAnswer('  sou  ', 'sou') === 'correct', 'whitespace trimmed');
assert(checkAnswer('SOu', 'sou') === 'correct', 'case insensitive');
assert(checkAnswer('sao', 'são') === 'accent-only', 'missing accent flagged');
assert(checkAnswer('ser', 'sou') === 'wrong', 'wrong answer is wrong');

// Queue builder
const selectedVerbs = ['SER', 'IR'];
const selectedTenses = {
  Indicativo: ['Presente', 'Pretérito Perfeito simples'],
  Subjuntivo: ['Presente'],
  Imperativo: [],
  FormasNominais: [],
};
const queue = buildQueue(selectedVerbs, selectedTenses, 10, false);
assert(Array.isArray(queue), 'buildQueue returns array');
assert(queue.length === 10, 'queue has requested count');
queue.forEach((q, i) => {
  assert(['A','B','C'].includes(q.mode), `q[${i}] has valid mode`);
  assert(selectedVerbs.includes(q.verb), `q[${i}] verb is in selection`);
  assert(typeof q.mood === 'string', `q[${i}] has mood`);
  assert(typeof q.tense === 'string', `q[${i}] has tense`);
  assert(Array.isArray(q.forms), `q[${i}] has forms array`);
});
```

- [ ] **Step 2: Run test to confirm failures**

```
node tests/logic.test.js
```
Expected: `FAIL: buildQueue exists`, `FAIL: checkAnswer exists`

- [ ] **Step 3: Implement answer checker in index.html**

Add after `Progress`:

```js
function normalize(s) { return s.trim().toLowerCase(); }

function stripDiacritics(s) {
  return s.normalize('NFD').replace(/[\u0300-\u036f]/g, '').toLowerCase().trim();
}

// Returns 'correct' | 'accent-only' | 'wrong'
function checkAnswer(input, expected) {
  if (normalize(input) === normalize(expected)) return 'correct';
  if (stripDiacritics(input) === stripDiacritics(expected)) return 'accent-only';
  return 'wrong';
}
```

- [ ] **Step 4: Implement buildQueue in index.html**

Add after `checkAnswer`:

```js
// Returns array of question objects, length = count
// excludeVos: if true, skip person index 4 (vós) for all modes
function buildQueue(selectedVerbs, selectedTenses, count, excludeVos) {
  // Build pool of (verb, mood, tense) combos
  const pool = [];
  selectedVerbs.forEach(verb => {
    ['Indicativo','Subjuntivo','Imperativo','FormasNominais'].forEach(mood => {
      const tenses = selectedTenses[mood] || [];
      tenses.forEach(tense => {
        const forms = mood === 'FormasNominais'
          ? VERBS[verb].FormasNominais[tense]
          : VERBS[verb][mood][tense];
        if (!forms) return;
        const key = `${verb}|${mood}|${tense}`;
        pool.push({ verb, mood, tense, forms, weight: Progress.getWeight(key) });
      });
    });
  });

  if (pool.length === 0) return [];

  // Weighted random selection (with replacement)
  const totalWeight = pool.reduce((s, p) => s + p.weight, 0);
  const questions = [];

  // Need at least 4 pool items for mode B distractors
  const canUseB = pool.length >= 4;

  for (let i = 0; i < count; i++) {
    // Pick item by weight
    let r = Math.random() * totalWeight;
    let item = pool[pool.length - 1];
    for (const p of pool) { r -= p.weight; if (r <= 0) { item = p; break; } }

    const { verb, mood, tense, forms } = item;
    const isSingleForm = typeof forms === 'string';

    // Assign mode
    let mode;
    if (isSingleForm) {
      mode = 'A';
    } else {
      const roll = Math.random();
      if (canUseB && roll < 0.33) mode = 'B';
      else if (roll < 0.66) mode = 'C';
      else mode = 'A';
    }

    // For mode A: pick a random person slot
    let personIndex = null;
    if (mode === 'A' && !isSingleForm) {
      const slots = [0,1,2,3,4,5].filter(i => !(excludeVos && i === 4) && forms[i] !== '—');
      personIndex = slots[Math.floor(Math.random() * slots.length)];
    }

    // For mode B: build 4 choices (correct tense + 3 distractors)
    let choices = null;
    if (mode === 'B') {
      const distractors = pool
        .filter(p => !(p.verb === verb && p.mood === mood && p.tense === tense) && typeof p.forms !== 'string')
        .sort(() => Math.random() - 0.5)
        .slice(0, 3)
        .map(p => ({ verb: p.verb, mood: p.mood, tense: p.tense }));
      choices = [
        { verb, mood, tense, correct: true },
        ...distractors.map(d => ({ ...d, correct: false })),
      ].sort(() => Math.random() - 0.5);
    }

    questions.push({ verb, mood, tense, forms, mode, personIndex, choices });
  }

  return questions;
}
```

- [ ] **Step 5: Paste buildQueue and checkAnswer into test file and run**

Copy both functions from `index.html` into `tests/logic.test.js` (after the Progress block). Then:

```
node tests/logic.test.js
```
Expected: all PASS

- [ ] **Step 6: Commit**

```bash
git add index.html tests/logic.test.js
git commit -m "feat: add question generator (weighted) and answer checker (diacritic-aware)"
```

---

## Task 4: CSS

**Files:**
- Modify: `index.html` — replace `/* CSS added in Task 4 */` with full styles

- [ ] **Step 1: Add CSS**

Replace `<style>/* CSS added in Task 4 */</style>` with:

```html
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: #f9fafb;
    color: #111827;
    min-height: 100vh;
    padding: 24px 16px;
  }

  #app {
    max-width: 640px;
    margin: 0 auto;
  }

  h1 { font-size: 1.5rem; font-weight: 700; margin-bottom: 4px; }
  h2 { font-size: 1.25rem; font-weight: 600; margin-bottom: 16px; }
  h3 { font-size: 1rem; font-weight: 600; margin-bottom: 8px; }

  .subtitle { color: #6b7280; font-size: 0.875rem; margin-bottom: 20px; }

  .card {
    background: #fff;
    border: 1px solid #e5e7eb;
    border-radius: 10px;
    padding: 20px;
    margin-bottom: 16px;
  }

  /* Verb / tense checkboxes */
  .chip-group { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 12px; }
  .chip {
    display: flex; align-items: center; gap: 6px;
    background: #f3f4f6; border: 1px solid #d1d5db;
    border-radius: 20px; padding: 4px 12px;
    cursor: pointer; font-size: 0.875rem; user-select: none;
    transition: background 0.15s, border-color 0.15s;
  }
  .chip.selected { background: #ede9fe; border-color: #7c3aed; color: #5b21b6; font-weight: 600; }
  .chip input { display: none; }

  .tense-group { margin-bottom: 14px; }
  .tense-group-label { font-size: 0.7rem; font-weight: 700; text-transform: uppercase;
    letter-spacing: 0.08em; color: #9ca3af; margin-bottom: 6px; }

  /* Number input */
  .count-row { display: flex; align-items: center; gap: 12px; margin-bottom: 20px; }
  .count-row label { font-size: 0.875rem; color: #374151; }
  .count-row input[type=number] {
    width: 70px; padding: 6px 10px; border: 1px solid #d1d5db; border-radius: 6px;
    font-size: 1rem; text-align: center;
  }

  /* Buttons */
  .btn {
    display: inline-flex; align-items: center; justify-content: center;
    padding: 10px 20px; border-radius: 8px; font-size: 0.95rem; font-weight: 600;
    cursor: pointer; border: none; transition: opacity 0.15s;
  }
  .btn:disabled { opacity: 0.4; cursor: not-allowed; }
  .btn-primary { background: #7c3aed; color: #fff; }
  .btn-primary:hover:not(:disabled) { background: #6d28d9; }
  .btn-secondary { background: #f3f4f6; color: #374151; border: 1px solid #d1d5db; }
  .btn-secondary:hover { background: #e5e7eb; }
  .btn-full { width: 100%; }

  /* Progress bar */
  .progress-bar-wrap { background: #e5e7eb; border-radius: 99px; height: 6px; margin-bottom: 24px; }
  .progress-bar-fill { background: #7c3aed; border-radius: 99px; height: 6px; transition: width 0.3s; }

  /* Question card */
  .question-label { font-size: 0.8rem; font-weight: 700; text-transform: uppercase;
    letter-spacing: 0.06em; color: #7c3aed; margin-bottom: 6px; }
  .question-prompt { font-size: 1.35rem; font-weight: 700; margin-bottom: 20px; }

  /* Text input for mode A */
  .answer-input {
    width: 100%; padding: 12px 16px; font-size: 1.1rem;
    border: 2px solid #d1d5db; border-radius: 8px; outline: none;
    transition: border-color 0.15s;
  }
  .answer-input:focus { border-color: #7c3aed; }
  .answer-input.correct { border-color: #16a34a; background: #f0fdf4; }
  .answer-input.wrong   { border-color: #dc2626; background: #fef2f2; }
  .answer-input.accent  { border-color: #d97706; background: #fffbeb; }

  /* Feedback row */
  .feedback { font-size: 0.9rem; margin-top: 10px; min-height: 20px; }
  .feedback.correct { color: #16a34a; }
  .feedback.wrong   { color: #dc2626; }
  .feedback.accent  { color: #d97706; }

  /* Mode B choices */
  .choices { display: flex; flex-direction: column; gap: 10px; margin-bottom: 16px; }
  .choice-btn {
    text-align: left; padding: 12px 16px; border-radius: 8px;
    border: 2px solid #e5e7eb; background: #fff; cursor: pointer;
    font-size: 0.9rem; transition: border-color 0.15s, background 0.15s;
  }
  .choice-btn:hover:not(:disabled) { border-color: #7c3aed; background: #ede9fe; }
  .choice-btn.selected { border-color: #7c3aed; background: #ede9fe; }
  .choice-btn.correct-reveal { border-color: #16a34a; background: #f0fdf4; }
  .choice-btn.wrong-reveal   { border-color: #dc2626; background: #fef2f2; }

  /* Mode C table */
  .fill-table { width: 100%; border-collapse: collapse; margin-bottom: 16px; }
  .fill-table td { padding: 4px 6px; }
  .fill-table .person-cell { width: 120px; font-size: 0.875rem; color: #6b7280; }
  .fill-table input {
    width: 100%; padding: 7px 10px; border: 1.5px solid #d1d5db; border-radius: 6px;
    font-size: 0.9rem;
  }
  .fill-table input.correct { border-color: #16a34a; background: #f0fdf4; }
  .fill-table input.wrong   { border-color: #dc2626; background: #fef2f2; }
  .fill-table input.accent  { border-color: #d97706; background: #fffbeb; }

  /* Results */
  .score-big { font-size: 3rem; font-weight: 800; color: #7c3aed; text-align: center; }
  .score-pct { text-align: center; color: #6b7280; margin-bottom: 24px; }
  .weak-list { list-style: none; }
  .weak-list li {
    padding: 8px 12px; background: #fef2f2; border-radius: 6px;
    font-size: 0.875rem; color: #991b1b; margin-bottom: 6px;
  }
  .action-row { display: flex; gap: 12px; margin-top: 20px; }
  .action-row .btn { flex: 1; }

  /* Vós toggle */
  .toggle-row { display: flex; align-items: center; gap: 10px; margin-bottom: 16px; font-size: 0.875rem; }
  .toggle-row input[type=checkbox] { width: 16px; height: 16px; cursor: pointer; }
</style>
```

- [ ] **Step 2: Open index.html in browser, confirm blank page loads without errors**

Open `index.html` directly in a browser (File → Open). The page should show "Loading…" with no console errors.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add full CSS styles"
```

---

## Task 5: Setup screen

**Files:**
- Modify: `index.html` — add `renderSetup()` and call it from init

- [ ] **Step 1: Add renderSetup and wire to init**

Replace `document.getElementById('app').textContent = 'Loading…';` with:

```js
// ── Session state ──
let currentQueue = [];
let currentIndex = 0;
let sessionResults = [];
let sessionSetup = null;

const TENSE_GROUPS = {
  Indicativo: [
    "Presente","Pretérito Imperfeito","Pretérito Perfeito simples",
    "Pretérito Perfeito composto","Pretérito mais-que-perfeito simples",
    "Pretérito mais-que-perfeito composto","Futuro do Presente simples",
    "Futuro do Presente composto","Futuro do Pretérito simples","Futuro do Pretérito composto",
  ],
  Subjuntivo: [
    "Presente","Pretérito Perfeito","Pretérito Imperfeito",
    "Pretérito mais-que-perfeito","Futuro simples","Futuro composto",
  ],
  Imperativo: ["Afirmativo","Negativo"],
  FormasNominais: ["Infinito pessoal","Gerúndio","Particípio"],
};

function getWeakTenses() {
  const all = Progress.getAll();
  return Object.entries(all)
    .filter(([, v]) => v.incorrect > 0)
    .sort((a, b) => b[1].incorrect - a[1].incorrect)
    .slice(0, 5)
    .map(([key]) => key);
}

function renderSetup() {
  const weak = getWeakTenses();
  const app = document.getElementById('app');
  app.innerHTML = `
    <h1>Verbos Irregulares</h1>
    <p class="subtitle">Configure your practice session</p>

    <div class="card">
      <h3>Verbs</h3>
      <div class="chip-group" id="verb-chips">
        ${Object.keys(VERBS).map(v => `
          <label class="chip selected" id="chip-${v}">
            <input type="checkbox" checked data-verb="${v}" onchange="updateChip(this)">
            ${v}
          </label>`).join('')}
      </div>
    </div>

    <div class="card">
      <h3>Tenses</h3>
      ${Object.entries(TENSE_GROUPS).map(([mood, tenses]) => `
        <div class="tense-group">
          <div class="tense-group-label">${mood}</div>
          <div class="chip-group">
            ${tenses.map(t => {
              const key = Object.keys(VERBS)[0] + '|' + mood + '|' + t;
              const isWeak = weak.some(w => w.endsWith('|' + t));
              const defaultOn = mood === 'Indicativo' && [
                'Presente','Pretérito Perfeito simples','Pretérito Imperfeito'
              ].includes(t);
              return `<label class="chip${defaultOn ? ' selected' : ''}" id="chip-${mood}-${t.replace(/ /g,'_')}">
                <input type="checkbox" ${defaultOn ? 'checked' : ''} data-mood="${mood}" data-tense="${t}" onchange="updateChip(this)">
                ${t}${isWeak ? ' ⚠️' : ''}
              </label>`;
            }).join('')}
          </div>
        </div>`).join('')}
    </div>

    <div class="card">
      <div class="toggle-row">
        <input type="checkbox" id="exclude-vos" checked>
        <label for="exclude-vos">Skip <em>vós</em> forms (not used in Brazilian Portuguese conversation)</label>
      </div>
      <div class="count-row">
        <label for="q-count">Questions:</label>
        <input type="number" id="q-count" value="20" min="5" max="100">
      </div>
      <button class="btn btn-primary btn-full" id="start-btn" onclick="startSession()">Start session</button>
    </div>
  `;
  updateStartButton();
}

function updateChip(el) {
  const label = el.closest('.chip');
  if (el.checked) label.classList.add('selected');
  else label.classList.remove('selected');
  updateStartButton();
}

function updateStartButton() {
  const anyVerb = [...document.querySelectorAll('[data-verb]')].some(el => el.checked);
  const anyTense = [...document.querySelectorAll('[data-tense]')].some(el => el.checked);
  document.getElementById('start-btn').disabled = !(anyVerb && anyTense);
}

function startSession() {
  const selectedVerbs = [...document.querySelectorAll('[data-verb]:checked')].map(el => el.dataset.verb);
  const selectedTenses = { Indicativo: [], Subjuntivo: [], Imperativo: [], FormasNominais: [] };
  document.querySelectorAll('[data-tense]:checked').forEach(el => {
    selectedTenses[el.dataset.mood].push(el.dataset.tense);
  });
  const count = Math.max(5, Math.min(100, parseInt(document.getElementById('q-count').value) || 20));
  const excludeVos = document.getElementById('exclude-vos').checked;

  sessionSetup = { selectedVerbs, selectedTenses, count, excludeVos };
  currentQueue = buildQueue(selectedVerbs, selectedTenses, count, excludeVos);
  currentIndex = 0;
  sessionResults = [];
  renderPractice();
}

renderSetup();
```

- [ ] **Step 2: Open index.html in browser**

Open `index.html`. You should see:
- "Verbos Irregulares" heading
- Verb chips (all selected, purple)
- Tense checkboxes grouped by mood
- Vós toggle + question count + Start button
- Clicking chips toggles them purple/grey
- Start button is disabled if no verbs or tenses are selected

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: setup screen with verb/tense selection and session config"
```

---

## Task 6: Practice screen — all 3 modes

**Files:**
- Modify: `index.html` — add `renderPractice()`, `renderQuestion()`, mode renderers, and answer handlers

- [ ] **Step 1: Add renderPractice and mode renderers**

Add the following functions before `renderSetup()`:

```js
function renderPractice() {
  if (currentIndex >= currentQueue.length) { renderResults(); return; }
  const q = currentQueue[currentIndex];
  const app = document.getElementById('app');

  const pct = Math.round((currentIndex / currentQueue.length) * 100);
  app.innerHTML = `
    <div class="progress-bar-wrap">
      <div class="progress-bar-fill" style="width:${pct}%"></div>
    </div>
    <div class="card" id="question-card">
      ${renderQuestion(q)}
    </div>
  `;

  // Focus input if mode A
  if (q.mode === 'A') {
    const inp = document.getElementById('answer-input');
    if (inp) { inp.focus(); inp.addEventListener('keydown', e => { if (e.key === 'Enter') submitModeA(); }); }
  }
}

function moodLabel(mood) {
  return { Indicativo: 'Indicativo', Subjuntivo: 'Subjuntivo',
    Imperativo: 'Imperativo', FormasNominais: 'Formas Nominais' }[mood] || mood;
}

function renderQuestion(q) {
  const counter = `<div style="font-size:0.8rem;color:#9ca3af;margin-bottom:8px;">${currentIndex + 1} / ${currentQueue.length}</div>`;
  if (q.mode === 'A') return counter + renderModeA(q);
  if (q.mode === 'B') return counter + renderModeB(q);
  return counter + renderModeC(q);
}

function renderModeA(q) {
  const isSingle = typeof q.forms === 'string';
  const person = isSingle ? '' : ` · ${PERSONS[q.personIndex]}`;
  return `
    <div class="question-label">${moodLabel(q.mood)} · ${q.tense}</div>
    <div class="question-prompt">${q.verb}${person}</div>
    <input class="answer-input" id="answer-input" type="text" autocomplete="off" autocorrect="off" spellcheck="false" placeholder="type conjugation…">
    <div class="feedback" id="feedback"></div>
    <button class="btn btn-primary" style="margin-top:14px;" onclick="submitModeA()">Check</button>
  `;
}

function renderModeB(q) {
  // Show up to 6 forms from the correct tense
  const sample = Array.isArray(q.forms) ? q.forms.slice(0,4).join(' · ') : q.forms;
  return `
    <div class="question-label">${q.verb} · What tense is this? (${moodLabel(q.mood)})</div>
    <div class="question-prompt" style="font-size:1.1rem;">${sample}</div>
    <div class="choices" id="choices">
      ${q.choices.map((c, i) => `
        <button class="choice-btn" id="choice-${i}" onclick="submitModeB(${i})" data-correct="${c.correct}">
          ${c.tense} <span style="color:#9ca3af;font-size:0.8rem;">(${c.mood})</span>
        </button>`).join('')}
    </div>
    <div class="feedback" id="feedback"></div>
  `;
}

function renderModeC(q) {
  const persons = PERSONS.map((p, i) => ({ p, i }))
    .filter(({ i }) => !(sessionSetup.excludeVos && i === 4));
  return `
    <div class="question-label">${moodLabel(q.mood)}</div>
    <div class="question-prompt">${q.verb} · ${q.tense}</div>
    <table class="fill-table">
      ${persons.map(({ p, i }) => `
        <tr>
          <td class="person-cell">${p}</td>
          <td><input class="fill-input" id="fill-${i}" data-idx="${i}" type="text"
            autocomplete="off" autocorrect="off" spellcheck="false"
            onkeydown="if(event.key==='Enter')submitModeC()"></td>
        </tr>`).join('')}
    </table>
    <div class="feedback" id="feedback"></div>
    <button class="btn btn-primary" onclick="submitModeC()">Check all</button>
  `;
}
```

- [ ] **Step 2: Add answer submission handlers**

Add these functions immediately after the renderers:

```js
function nextQuestion(correct, key) {
  Progress.record(key, correct);
  sessionResults.push({ ...currentQueue[currentIndex], correct });
  currentIndex++;
  setTimeout(renderPractice, correct ? 800 : 1400);
}

function submitModeA() {
  const q = currentQueue[currentIndex];
  const input = document.getElementById('answer-input');
  const feedback = document.getElementById('feedback');
  if (!input || input.disabled) return;

  const expected = typeof q.forms === 'string' ? q.forms : q.forms[q.personIndex];
  const result = checkAnswer(input.value, expected);
  const key = `${q.verb}|${q.mood}|${q.tense}`;

  input.disabled = true;
  document.querySelector('[onclick="submitModeA()"]').disabled = true;

  if (result === 'correct') {
    input.classList.add('correct');
    feedback.className = 'feedback correct';
    feedback.textContent = '✓ Correct!';
    nextQuestion(true, key);
  } else if (result === 'accent-only') {
    input.classList.add('accent');
    feedback.className = 'feedback accent';
    feedback.innerHTML = `⚠ Check accents — answer: <strong>${expected}</strong>`;
    nextQuestion(false, key);
  } else {
    input.classList.add('wrong');
    feedback.className = 'feedback wrong';
    feedback.innerHTML = `✗ Correct answer: <strong>${expected}</strong>`;
    nextQuestion(false, key);
  }
}

function submitModeB(choiceIndex) {
  const q = currentQueue[currentIndex];
  const feedback = document.getElementById('feedback');
  const buttons = document.querySelectorAll('.choice-btn');
  buttons.forEach(b => b.disabled = true);

  const choice = q.choices[choiceIndex];
  const correct = choice.correct;
  const key = `${q.verb}|${q.mood}|${q.tense}`;

  buttons.forEach((b, i) => {
    if (q.choices[i].correct) b.classList.add('correct-reveal');
    else if (i === choiceIndex && !correct) b.classList.add('wrong-reveal');
  });

  feedback.className = correct ? 'feedback correct' : 'feedback wrong';
  feedback.textContent = correct ? '✓ Correct!' : `✗ That was: ${q.tense} (${q.mood})`;
  nextQuestion(correct, key);
}

function submitModeC() {
  const q = currentQueue[currentIndex];
  const feedback = document.getElementById('feedback');
  const inputs = document.querySelectorAll('.fill-input');
  let allCorrect = true;
  const key = `${q.verb}|${q.mood}|${q.tense}`;

  inputs.forEach(inp => {
    inp.disabled = true;
    const idx = parseInt(inp.dataset.idx);
    const expected = q.forms[idx];
    const result = checkAnswer(inp.value, expected);
    if (result === 'correct') {
      inp.classList.add('correct');
    } else if (result === 'accent-only') {
      inp.classList.add('accent');
      inp.title = `Correct: ${expected}`;
      allCorrect = false;
    } else {
      inp.classList.add('wrong');
      inp.title = `Correct: ${expected}`;
      allCorrect = false;
    }
  });

  document.querySelector('[onclick="submitModeC()"]').disabled = true;
  feedback.className = allCorrect ? 'feedback correct' : 'feedback wrong';
  feedback.textContent = allCorrect ? '✓ All correct!' : '✗ See highlighted cells (hover for correct form)';
  nextQuestion(allCorrect, key);
}
```

- [ ] **Step 3: Open index.html and run through a session manually**

1. Open `index.html`, select a few verbs + tenses, click Start
2. Verify all 3 modes appear (may take a few questions to see all)
3. Mode A: type correct and wrong answers, verify green/red feedback
4. Mode B: click correct and wrong choices, verify correct answer is revealed
5. Mode C: fill some correct and wrong, verify per-cell colors + hover titles
6. Verify progress bar advances with each question

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: practice screen with all 3 quiz modes and answer feedback"
```

---

## Task 7: Results screen

**Files:**
- Modify: `index.html` — add `renderResults()`

- [ ] **Step 1: Add renderResults**

Add before `renderSetup()`:

```js
function renderResults() {
  const total = sessionResults.length;
  const correct = sessionResults.filter(r => r.correct).length;
  const pct = total > 0 ? Math.round((correct / total) * 100) : 0;

  // Collect weak combos (wrong this session)
  const weakMap = {};
  sessionResults.filter(r => !r.correct).forEach(r => {
    const key = `${r.verb} · ${r.tense} (${moodLabel(r.mood)})`;
    weakMap[key] = (weakMap[key] || 0) + 1;
  });
  const weakItems = Object.entries(weakMap)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 6);

  const app = document.getElementById('app');
  app.innerHTML = `
    <div class="card" style="text-align:center;padding:32px 20px;">
      <div class="score-big">${correct} / ${total}</div>
      <div class="score-pct">${pct}% correct</div>
    </div>

    ${weakItems.length > 0 ? `
      <div class="card">
        <h3>Needs work</h3>
        <ul class="weak-list">
          ${weakItems.map(([label, count]) => `<li>${label} <span style="color:#9ca3af">(${count}×)</span></li>`).join('')}
        </ul>
        <p style="font-size:0.8rem;color:#9ca3af;margin-top:10px;">
          Progress saved — weak tenses will appear more often next session.
        </p>
      </div>` : `
      <div class="card" style="text-align:center;color:#16a34a;font-weight:600;">
        Perfect session! 🎉
      </div>`}

    <div class="action-row">
      <button class="btn btn-secondary" onclick="practiceAgain()">Practice again</button>
      <button class="btn btn-primary" onclick="renderSetup()">New session</button>
    </div>
  `;
}

function practiceAgain() {
  const { selectedVerbs, selectedTenses, count, excludeVos } = sessionSetup;
  currentQueue = buildQueue(selectedVerbs, selectedTenses, count, excludeVos);
  currentIndex = 0;
  sessionResults = [];
  renderPractice();
}
```

- [ ] **Step 2: Open index.html, complete a session, verify results screen**

1. Start a session with 5 questions (change count input to 5)
2. Answer some right, some wrong
3. After question 5, results screen should appear with score and weak list
4. Click "Practice again" — new session with same settings starts
5. Click "New session" — setup screen appears

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: results screen with score, weak tenses list, and repeat/new actions"
```

---

## Task 8: Final integration check

**Files:**
- Modify: `index.html` — add page title h1 to results/practice, verify Gerúndio/Particípio in Mode A

- [ ] **Step 1: Verify Gerúndio and Particípio work in Mode A**

In the browser:
1. Setup: select any verb, select only "FormasNominais → Gerúndio"
2. Start session (5 questions)
3. All questions should be mode A with prompt like "SER · Gerúndio"
4. Correct answer is the single string (e.g., "sendo")
5. Verify answer checking works

If "Gerúndio" or "Particípio" don't appear in tense checkboxes, confirm they are listed in `TENSE_GROUPS.FormasNominais` in `renderSetup` (they should be from Task 5).

- [ ] **Step 2: Verify progress persistence across page reloads**

1. Complete a session with some wrong answers
2. Note which tenses appeared in "Needs work"
3. Reload the page (F5)
4. Start a new session with all verbs/tenses
5. The weak tenses from before should show ⚠️ in the tense selection chips

(The ⚠️ in `renderSetup` is driven by `getWeakTenses()` which reads localStorage — verify it still works.)

- [ ] **Step 3: Verify mobile layout**

Open browser DevTools → responsive mode → set to 375px width. Verify:
- All chips wrap cleanly
- Inputs are large enough to tap
- Buttons are full-width and tappable
- No horizontal overflow

- [ ] **Step 4: Run logic tests one final time**

```
node tests/logic.test.js
```
Expected: all PASS

- [ ] **Step 5: Final commit**

```bash
git add index.html tests/logic.test.js
git commit -m "feat: complete verbos practice app — all screens, 3 quiz modes, progress tracking"
```

---

## Spec Coverage Check

| Spec requirement | Task |
|---|---|
| 8 verbs: SER IR ESTAR TER FAZER SABER QUERER DAR | Task 1 |
| All tenses from PDF | Task 1 |
| Mode A — type conjugation | Task 6 |
| Mode B — identify tense (multiple choice) | Task 6 |
| Mode C — fill the table | Task 6 |
| Choose tenses per session | Task 5 |
| Progress saved to localStorage | Task 2 |
| Weak areas weighted higher | Tasks 2 & 3 |
| Weak areas flagged in setup | Task 5 |
| Results screen with score + weak list | Task 7 |
| Practice again / New session | Task 7 |
| Vós exclusion toggle | Task 5 |
| Diacritic-aware answer checking | Task 3 |
| Standalone single HTML file | All tasks |
