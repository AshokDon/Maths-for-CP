# Blog 02 — Foundations: Bit Manipulation & Fast Exponentiation

> Series template (every tutorial follows this): **Intuition → Derivation → Worked
> Examples → Practice (where the math is used) → Coding Problems.**
> Code is shown in **C++** (the contest language) and **Python** (the most readable for
> learning). Pick whichever you submit in.

This post covers the two ideas everything else in the series quietly stands on. By the end you'll
understand *why* binary exponentiation works — not just how to copy it.

---

# Topic A — Bit Manipulation

## Intuition: a number is just a row of switches

Forget that `13` is "thirteen" for a second. To a computer, `13` is a row of ON/OFF switches:

```
 value:  8   4   2   1
 bit:    1   1   0   1   →  8 + 4 + 0 + 1 = 13
```

Each switch (bit) is worth a power of two. A bit that's ON adds its value; OFF adds nothing.
That's the whole idea of binary. **Bit manipulation is just flipping, reading, and combining
these switches directly** — which is blazing fast because it's exactly how the hardware thinks.

## The operations, from first principles

Every bitwise operator works on switches *one position at a time*. Look at what each does to a
single pair of bits, and the whole operator follows:

| `a` | `b` | `a & b` (AND) | `a \| b` (OR) | `a ^ b` (XOR) |
|----|----|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 |

Read them as plain English and they stop being scary:
- **AND** → "ON only if *both* are ON." (a filter)
- **OR** → "ON if *either* is ON." (a switch-on)
- **XOR** → "ON if they *differ*." (a toggle — this is the star of game theory later)
- **NOT (`~`)** → flip every bit.
- **Left shift `x << k`** → slide all switches up `k` places = **multiply by 2ᵏ**.
- **Right shift `x >> k`** → slide down `k` places = **divide by 2ᵏ** (integer).

> *Why does `<< 1` multiply by 2?* Because shifting `1011` (=11) left gives `10110` (=22) —
> every switch moved to a slot worth twice as much. First principles, not memorization.

## The CP toolkit (the tricks you'll actually reuse)

These all fall out of the table above. `1 << i` is "a number with only bit `i` ON":

| Goal | Trick | Why it works |
|------|-------|--------------|
| Is bit `i` set? | `(x >> i) & 1` | shift bit `i` to the front, mask it |
| Set bit `i` | `x \| (1 << i)` | OR forces that switch ON |
| Clear bit `i` | `x & ~(1 << i)` | AND with all-ON-except-`i` forces it OFF |
| Toggle bit `i` | `x ^ (1 << i)` | XOR with a single ON bit flips it |
| Is `x` odd? | `x & 1` | the 1's bit is set only for odd numbers |
| Lowest set bit | `x & (-x)` | two's-complement isolates the rightmost ON bit |
| Drop lowest set bit | `x & (x - 1)` | handy for counting set bits |
| Count set bits | `__builtin_popcount(x)` (C++), `bin(x).count('1')` (Py) | |

## Worked example: a set as a single integer

This is *the* reason bit manipulation matters in CP. A subset of `{0,1,2,3,4}` becomes one number,
where bit `i` means "element `i` is in the set."

```
{0, 2, 3}   →   bits 0,2,3 ON   →   01101₂  =  13
```

Now set operations become bit operations:
- **Add element 1:**  `13 | (1 << 1)` = `01111` = `{0,1,2,3}`
- **Is 2 in the set?**  `(13 >> 2) & 1` = `1` → yes
- **Union of two sets:** `a | b`  ·  **Intersection:** `a & b`

A set with up to ~20 elements fits in one `int`, and you can loop over *all* `2ⁿ` subsets with a
single `for (int mask = 0; mask < (1<<n); mask++)`. That is the entire foundation of **bitmask DP**.

## 🎯 Where this math is used (practice — spot the pattern)

Before coding, train the *recognition*. Each scenario below is secretly bit manipulation:

1. "There are `N ≤ 20` cities; visit all of them once (Travelling Salesman)." → represent the
   set of visited cities as a bitmask. (**Bitmask DP**)
2. "Find the two numbers in an array that appear an odd number of times." → XOR cancels pairs.
3. "How many pairs `(i, j)` have `a[i] AND a[j] == 0`?" → reasoning over bits.
4. "Toggle every light in a range, then count how many are on." → XOR tricks.
5. "Generate every subset of a set of options." → loop `mask` from `0` to `2ⁿ − 1`.

## 💻 Coding problems

| Problem | Judge | Skill |
|---------|-------|-------|
| Apple Division | https://cses.fi/problemset/task/1623 | iterate all subsets via bitmask |
| Hamming Distance | https://cses.fi/problemset/task/2136 | XOR + popcount |
| Codeforces problems tagged `bitmasks` | https://codeforces.com/problemset?tags=bitmasks | mixed |

**Reference:** CP-Algorithms — Bit manipulation → https://cp-algorithms.com/algebra/bit-manipulation.html

---

# Topic B — Fast (Binary) Exponentiation

This is the single most reused algorithm in CP math. Modular inverses, counting DP, hashing, and
matrix exponentiation all run on it.

## The problem: the obvious way is too slow

Computing `aⁿ` by multiplying `a` by itself `n` times takes `n` multiplications. If `n = 10¹⁸`
(a normal CP constraint), that's a billion-billion operations. Your program will not finish before
the contest ends. We need something dramatically faster.

## Intuition: stop throwing away work

Multiplying one factor at a time wastes effort. Watch what squaring buys you:

```
a¹  →  a²  →  a⁴  →  a⁸  →  a¹⁶ ...
```

Each step is **one multiplication** (square the previous result), yet the exponent *doubles*.
So after `k` multiplications we've reached `a^(2ᵏ)`. The exponent grows exponentially while our
work grows linearly. That gap is the whole trick.

## Derivation from first principles

There are two equivalent ways to see it. Pick the one that clicks.

**Way 1 — split the exponent (recursive view).** For any `n`:

```
        ⎧ 1                       if n = 0
aⁿ  =   ⎨ ( a^(n/2) )²            if n is even
        ⎩ a · a^(n-1)            if n is odd
```

Each "even" step *halves* `n`. Halving a number down to 0 takes about `log₂ n` steps — so we do
~`log n` multiplications instead of `n`. For `n = 10¹⁸` that's about **60 operations**, not a
billion-billion.

**Way 2 — read the exponent in binary (iterative view).** Any exponent is a sum of powers of two
(that's what binary *is*). Example: `13 = 1101₂ = 8 + 4 + 1`, so:

```
a¹³ = a⁸ · a⁴ · a¹
```

So: walk through the bits of `n` from right to left. Keep a running `base` that you square every
step (giving `a¹, a², a⁴, a⁸, …`). Whenever the current bit is `1`, multiply that power into your
answer. This is the version we code.

## The algorithm

**C++**
```cpp
long long power(long long a, long long n) {
    long long result = 1;
    while (n > 0) {
        if (n & 1)          // current (lowest) bit is 1?
            result *= a;     //   include this power of a
        a *= a;              // square the base → next power of two
        n >>= 1;             // drop the bit we just handled
    }
    return result;
}
```

**Python**
```python
def power(a, n):
    result = 1
    while n > 0:
        if n & 1:            # current bit is 1?
            result *= a      #   include this power of a
        a *= a               # square the base
        n >>= 1              # move to the next bit
    return result
```

Notice the three toolkit tricks from Topic A doing real work: `n & 1` (read the bit),
`n >>= 1` (drop it). Bit manipulation wasn't a side quest — it's the engine here.

## Worked example: trace `3¹³` by hand

`13 = 1101₂`. Follow the loop:

| step | `n` (binary) | bit | action | `result` | `base` after squaring |
|-----|--------------|----|--------|----------|------------------------|
| start | 1101 | — | — | 1 | 3 |
| 1 | 1101 | 1 | `result = 1·3` | **3** | 3²=9 |
| 2 | 110 | 0 | skip | 3 | 9²=81 |
| 3 | 11 | 1 | `result = 3·81` | **243** | 81²=6561 |
| 4 | 1 | 1 | `result = 243·6561` | **1594323** | — |

Result: `3¹³ = 1594323`. ✓ (Check: `3¹⁰ = 59049`, times `3³ = 27`, gives `1594323`.) Four
multiplications instead of thirteen — and the gap explodes as `n` grows.

## The modular version (the one you'll actually use)

CP answers are almost always asked **modulo 1,000,000,007**, because true answers overflow 64-bit
integers. Powers blow up instantly (`2⁶⁴` already overflows), so we take the remainder *at every
step*. This is legal because mod distributes over multiplication:
`(x · y) mod m = ((x mod m) · (y mod m)) mod m`.

```cpp
const long long MOD = 1e9 + 7;
long long power(long long a, long long n, long long mod = MOD) {
    long long result = 1;
    a %= mod;
    while (n > 0) {
        if (n & 1) result = (result * a) % mod;
        a = (a * a) % mod;
        n >>= 1;
    }
    return result;
}
```

(In Python you can even just write `pow(a, n, mod)` — it does exactly this internally — but learn
the loop first so you understand what's happening.)

## 🎯 Where this math is used (practice — spot the pattern)

Fast exponentiation hides inside far more than "compute a power":

1. **Modular inverse via Fermat's Little Theorem:** `a⁻¹ ≡ a^(p−2) (mod p)`. This is *the* way to
   "divide" under a modulus — and dividing is needed for every `nCr mod p`. (We'll prove this in
   the Number Theory blog; it's literally one `power()` call.)
2. **Counting problems with huge answers:** "How many bit strings of length `n`?" → `2ⁿ mod p`.
3. **Linear recurrences in `O(log n)`:** the exact same square-and-multiply, but on *matrices*,
   computes Fibonacci(10¹⁸) in microseconds. (Module 5.)
4. **String hashing:** rolling polynomial hashes need fast powers of the base.
5. **Probability/expectation answers** reported as a modular fraction need modular inverses → fast
   power again.

If you remember one thing: **whenever you see "modulo 10⁹+7" in a problem, this function is
probably involved.**

## 💻 Coding problems

| Problem | Link | What it drills |
|---------|------|----------------|
| Exponentiation | https://cses.fi/problemset/task/1095 | the algorithm, exactly as-is |
| Bit Strings | https://cses.fi/problemset/task/1617 | recognize the answer is `2ⁿ mod p` |
| Exponentiation II | https://cses.fi/problemset/task/1712 | `a^(b^c)` — needs Fermat on the *exponent* (a great stretch problem) |
| Codeforces `number theory` tag | https://codeforces.com/problemset?tags=number+theory | mixed practice |

**References:**
CP-Algorithms — Binary exponentiation → https://cp-algorithms.com/algebra/binary-exp.html
USACO Guide — Modular arithmetic → https://usaco.guide/gold/modular

---

## Recap

- A number is a row of switches; bitwise operators flip/read/combine them, and a whole **set
  fits in one integer** (the seed of bitmask DP).
- **Binary exponentiation** computes `aⁿ` in `O(log n)` by squaring the base and reading the
  exponent's bits — and the **modular** version is the workhorse behind inverses, counting DP,
  hashing, and matrix exponentiation.

### Next up
**Blog 03 — Number Theory I: Primes, the Sieve, and GCD**, where we'll derive the Sieve of
Eratosthenes from the plain definition of a prime, and build the Euclidean algorithm from the
single observation that `gcd(a, b) = gcd(b, a mod b)` — first principles, all the way down.

> *You now own the engine (`power`) and the alphabet (bits). Everything modular from here is just
> these two ideas wearing different clothes.*
