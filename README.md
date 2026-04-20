# CS211 Spelling Bee — Interactive Word Game & Solvers

NYT-style Spelling Bee: hive letters, required letter, play mode, brute-force solver, and prefix-pruned recursive binary-search solver with pangram scoring.

## Overview

This program implements a **Spelling Bee**–style game: you get a **hive** of unique letters and one **required** letter. Valid words use only hive letters, meet a minimum length (4+), and must include the required letter. The project supports **play mode** (you find words interactively), then runs a **solver** that lists all valid dictionary words with **scores** and **pangram** detection.

It was built for **CS 211** to practice **dynamic memory**, **string algorithms**, and **comparing two solution strategies**—a full dictionary scan versus search guided by a sorted dictionary.

## Key Features

- **Dictionary loading** into a growable `WordList` (dynamic array of heap-allocated strings).
- **Hive construction** from user input: letters inserted in **sorted order**, duplicates removed.
- **Random hive mode**: choose a “fit” dictionary word whose **unique letter count** matches hive size, then derive the hive from that word.
- **Validation**: words may use only hive letters and must contain the **required** letter; **pangram** and **perfect pangram** detection via unique-letter counts.
- **Scoring and display**: 4-letter words = 1 point; longer words = length in points; pangram bonus; formatted word list with totals.
- **Two solvers**:
  - **Brute force**: scan every dictionary word and filter with `isValidWord`.
  - **Optimized**: recursive `findAllMatches` with **binary search** returning an **exact match**, a **prefix** of some dictionary word (keep extending), or **no valid continuation** (dial/backtrack), pruning dead branches.

## Tech Stack

| Category | Details |
|----------|---------|
| Language | C (`C99`-style: `stdbool`, etc.) |
| Standard library | `stdio`, `stdlib`, `string`, `ctype`, `time` |
| Build | `makefile` |
| Profiling | gprof outputs in-repo (`gprof_bruteforce.txt`, `gprof_hive.txt`, `gprof_optimized.txt`) for comparing solver behavior |
| Test data | `dictionary.txt`, `sampleDict.txt`, `simple4.txt`, `simple5.txt`, `newsamp.txt` |

## How It Was Built (Timeline)

1. **Data structures** — `WordList`, `appendWord` with doubling realloc, `buildDictionary`, `freeWordList`.
2. **Hive** — `findLetter`, `buildHive` (sorted insertion, no duplicates).
3. **Random setup** — `countUniqueLetters`, `findAllFitWords` for random hive generation.
4. **Game rules & UI** — validation, pangram helpers, `printHive`, `printList` with scoring.
5. **Baseline solver** — `bruteForceSolve`.
6. **Optimized solver** — `isPrefix`, tri-state recursive `findWord` (exact match / prefix / no match), then `findAllMatches` with extension via `hive[0]` and letter dialing when a branch cannot prefix any dictionary word.
7. **`main`** — CLI (`-r`, `-d`, `-p`, `-o`, `-s`), optional play loop, solvers, cleanup.

## Challenges & Lessons Learned

- **Memory ownership** — Clear rules for when strings are copied vs. referenced; `freeWordList` must free each word and the pointer array; refactors can easily introduce leaks or double-frees.
- **Hive letter order** — The optimized solver’s extension strategy is tied to how hive strings are built; ordering affects the recursive search pattern.
- **Tri-state binary search** — Separating “prefix of some word” from “not in dictionary at all” is required for correct pruning; wrong cases drop valid words or waste work.
- **Pangram vs. perfect pangram** — Scoring and markers depend on whether all hive letters appear and whether length equals hive size.
- **Performance** — Brute force is simple but costly on large dictionaries; the optimized solver is harder to debug but aligns with profiling comparisons (e.g. gprof).

## Build & Run

From this directory:

```bash
make build
./spellingBee.exe
```

Useful `makefile` targets: `run`, `run_simp`, `run_play`, `run_sample`, `valgrind`, `gprof_brute`, `gprof_opt`, `gprof_all`, `clean`.

**CLI flags:** `-p` play mode, `-o` optimized solver (omit for brute force), `-r <n>` random hive with `n` letters (2–12), `-d <file>` dictionary path, `-s <seed>` fixed RNG seed.

---

*Course: CS 211 — project author’s implementation of the Spelling Bee assignment.*
