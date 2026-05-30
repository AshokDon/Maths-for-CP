# The Math Behind Competitive Programming — Start Here

> **Blog 01 of the series.** This is the map. The tutorials that follow are the territory.
> Read this first — it explains *why* we learn the math, *how* it unlocks the hardest
> problems (especially DP), and the *one habit* that separates people who memorize
> formulas from people who solve problems they've never seen.

---

## Part 1 — Why math quietly decides who solves the problem

Walk into any competitive programming contest and watch what happens around the 1500–2000
rating mark. Two people have the same data-structures knowledge, the same coding speed, the
same template library. One of them solves the problem in 20 minutes. The other stares at it
for an hour.

The difference is almost never the code. It's that one of them **recognized the math** hiding
inside the problem statement.

CP problems rarely say "use the Chinese Remainder Theorem." They say *"find the number of
arrangements modulo 1,000,000,007."* They don't say "this is inclusion–exclusion." They say
*"count the integers from 1 to N divisible by none of these primes."* The hard part isn't
running the algorithm — it's **seeing that a piece of math applies at all.**

That recognition only comes when the math lives in your head as *ideas you understand*, not
*formulas you copied.* Which is the entire point of this series.

So, the honest framing:

- **Math is not one topic in CP.** It's the language the other topics are written in.
- **You don't need to be a "math person."** You need a small, well-understood toolkit and
  the habit of reaching for it.
- **Most CP math is shallow but wide** — dozens of small, sharp ideas, each learnable in an
  afternoon, each reusable forever.

---

## Part 2 — The part nobody tells beginners: math is what makes DP *click*

People learn basic dynamic programming (knapsack, longest common subsequence) and then hit a
wall. The intermediate and advanced DP problems suddenly feel impossible. Almost always, the
missing piece is **math**, not DP technique.

Here's the progression, and what unlocks each stage:

| You want to solve... | The math that unlocks it |
|----------------------|--------------------------|
| "Count the number of ways to..." DP | **Combinatorics** — permutations, `nCr`, stars and bars |
| Any counting DP with a huge answer | **Modular arithmetic** — answers mod 1e9+7, modular inverses |
| DP over subsets (bitmask DP) | **Bit manipulation + subset counting** |
| DP that's correct but *too slow* (N up to 10¹⁸) | **Matrix exponentiation** — turns an O(N) recurrence into O(log N) |
| "Count configurations avoiding X, Y, Z" | **Inclusion–Exclusion** layered on top of DP |
| "What is the expected number of steps until..." | **Probability & expectation** (expectation DP) |
| "Two players play optimally, who wins?" | **Game theory** — Grundy values *are* a DP over game states |

Look closely at that table. **Every "advanced DP" category is really a DP skeleton wrapped
around a math idea.** This is why we teach the math first. Once you understand *why* `nCr`
counts subsets, a counting DP stops being a mystery — it's just `nCr` reasoning applied state
by state. Once you understand that a linear recurrence is a matrix, "DP with N up to 10¹⁸"
stops being scary — it's the same DP, exponentiated.

The single biggest intermediate→advanced leap — **matrix exponentiation** — is pure linear
algebra. You cannot shortcut it with more DP practice. You have to learn the math.

That's the promise of this series: we're not learning math *instead of* algorithms. We're
learning the math that makes the algorithms finally make sense.

---

## Part 3 — Our one rule: First-Principles Thinking

Every tutorial in this series follows one rule:

> **We never hand you a formula. We build it in front of you.**

This is *first-principles thinking* — starting from things you already obviously believe, and
reasoning forward until the formula appears on its own. The opposite of "here is `n!`,
memorize it."

Why does this matter so much in CP? Because **you will face problems that don't match any
formula you memorized.** A memorized formula is a locked box: useful only when the problem is
shaped exactly like the box. A derived formula is a *method* — and methods stretch to fit new
problems.

### What it looks like in practice

Take the most basic fact in combinatorics: there are `n!` ways to arrange `n` items. A normal
tutorial states it. A first-principles tutorial *earns* it:

> You have `n` items and `n` empty slots to fill, left to right.
> - The **first** slot: any of the `n` items can go here. → `n` choices.
> - The **second** slot: one item is already used, so `n − 1` remain. → `n − 1` choices.
> - The **third** slot: `n − 2` choices. And so on, down to the last slot: `1` choice.
>
> Each independent choice *multiplies* the total, so:
> `n × (n − 1) × (n − 2) × … × 1 = n!`
>
> The formula didn't fall from the sky. It *fell out* of counting choices.

(If that reasoning feels familiar — it's exactly the "6 boxes" argument from the introductory
notes that kicked off this whole project. You were already doing first-principles thinking.
We're just making it the standard.)

Now watch the payoff. Because you *derived* `n!` instead of memorizing it, deriving `nCr`
takes thirty more seconds: to choose `r` items in order from `n` you get
`n × (n−1) × … × (n−r+1)` ways — but order didn't matter, and any group of `r` was counted
`r!` times (once per arrangement), so you divide it out:

```
nCr = [ n × (n−1) × … × (n−r+1) ] / r!  =  n! / ( r! · (n−r)! )
```

You didn't learn two formulas. You learned **one idea** (counting choices, then removing
overcounting) and the formulas are just that idea written down.

**Every concept in this series will be taught this way:** intuition → derivation → formula →
worked example → problems. Memorization is the fallback, never the goal.

---

## Part 4 — The Roadmap

We teach in **5 phases**, because the topics depend on each other. (Modular `nCr` needs both
number theory *and* combinatorics, so those two interleave rather than running in sequence.)

| Phase | Theme | Modules |
|-------|-------|---------|
| 1 | Make math *computable* | Module 0, start of Module 1 |
| 2 | Core counting (the daily bread of CP math) | Modules 1 + 2 |
| 3 | Reasoning under uncertainty | Module 3 |
| 4 | Adversarial reasoning | Module 4 |
| 5 | Speed-ups & heavy machinery | Modules 5 + 6 |

### Module 0 — Foundations
Make math turn into fast code.
*Complexity & Big-O · Bit manipulation · Binary (fast) exponentiation · GCD / Euclidean algorithm*

### Module 1 — Number Theory *(three tiers: essential → standard → advanced)*
- **Essential:** sieve of Eratosthenes · prime factorization · modular arithmetic · modular inverse · Fermat's & Euler's theorems
- **Standard:** extended Euclidean · linear Diophantine equations · Euler's totient (φ) · Chinese Remainder Theorem
- **Advanced:** Miller–Rabin · Pollard's rho · Möbius function & inversion · discrete logarithm · primitive roots

### Module 2 — Combinatorics
*Factorials & permutations · binomial coefficient `nCr` · binomial theorem · Pascal's triangle · computing `nCr mod p` · grid/lattice paths · stars and bars · inclusion–exclusion · derangements · Catalan numbers · Burnside's lemma · generating functions*

### Module 3 — Probability & Expectation
*Basic & conditional probability · expected value · **linearity of expectation** · probability/expectation DP · expectation with modular fractions*

### Module 4 — Game Theory
*Winning/losing positions (game DP) · Nim & Bouton's theorem · Grundy numbers (mex) · Sprague–Grundy theorem · misère games*

### Module 5 — Linear Algebra for CP
*Matrix multiplication · **matrix exponentiation** · fast linear recurrences · Gaussian elimination · determinant & rank · XOR / linear basis*

### Module 6 — Advanced / Polynomial *(optional, div-1 track)*
*FFT · NTT (mod p) · convolutions & applications*

---

## Part 5 — How this series works

Every topic gets its own tutorial blog, and every tutorial has the same four parts:

1. **Intuition** — the idea in plain language, first-principles style.
2. **Derivation** — we build the formula in front of you.
3. **Worked examples** — small numbers, fully traced.
4. **Problems** — graded from easy to hard, with links to judges.

### The 6 reference platforms we'll link throughout

You don't need paid courses. These six free resources cover almost everything:

- **CP-Algorithms** — the reference encyclopedia, code included → https://cp-algorithms.com/
- **USACO Guide** — structured, difficulty-tiered, with focus problems → https://usaco.guide/
- **Competitive Programmer's Handbook (CPH)** — free PDF book; chapters 21–25 are pure CP math → https://cses.fi/book/book.pdf
- **CSES Problem Set** — clean practice, organized by topic → https://cses.fi/problemset/
- **Codeforces EDU** — interactive video lessons → https://codeforces.com/edu/courses
- **AtCoder Educational DP Contest** — the gold standard for DP + expectation practice → https://atcoder.jp/contests/dp

For *pure math intuition and proofs* (not code): **Art of Problem Solving** → https://artofproblemsolving.com/

### A suggested 12-week pace

| Weeks | Focus |
|-------|-------|
| 1–2 | Module 0 + Number Theory (essentials) |
| 3–4 | Number Theory (standard) + Combinatorics (core) |
| 5–6 | Combinatorics (techniques) |
| 7 | Combinatorics (advanced: inclusion–exclusion, Catalan) |
| 8 | Probability & Expectation |
| 9 | Game Theory |
| 10 | Linear Algebra (matrix exponentiation) |
| 11 | Number Theory (advanced) |
| 12 | FFT/NTT *or* review + a mock contest |

Each week: **one concept lecture + one problem-solving session + a small problem set.**

---

### Next up

**Blog 02 — Foundations: Bit Manipulation & Fast Exponentiation**, where we'll do exactly what
we promised: start from "what does multiplying by 2 do in binary?" and *build our way up* to an
algorithm that computes `a^n` in `O(log n)` — the engine that every modular-arithmetic topic in
this series quietly runs on.

> *Don't memorize the map. Understand why each road connects to the next. That's first
> principles — and that's how you solve the problem you've never seen before.*
