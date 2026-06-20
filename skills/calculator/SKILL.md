---
name: calculator
description: Perform exact arithmetic calculations using the command-line bc tool instead of mental math. Use this skill whenever the user asks for any arithmetic — addition, subtraction, multiplication, division, percentages, remainders, or chained calculations — even simple-looking ones like "what's 47 times 89" or "12.5% of 340". Mental arithmetic is error-prone; bc is exact. Always reach for this skill for numeric computation rather than computing in your head.
---

# calculator

Compute arithmetic with the `bc` command-line calculator. `bc` is exact and never makes the silent slips that mental math does, so use it for every arithmetic request.

## Core workflow

1. Translate the user's request into a `bc` expression.
2. Run it via bash, piping the expression into `bc`.
3. Report **both** the expression you used and the result.

## The scale gotcha (read this every time)

`bc` defaults to `scale=0`, which **truncates division to an integer**. `echo "10/3" | bc` returns `3`, not `3.333...`. This is the single most common mistake.

Whenever a calculation involves division (or could produce a non-integer result), set the scale explicitly at the start of the expression:

```bash
echo "scale=10; 10/3" | bc
# 3.3333333333
```

For calculations that are purely integer (addition, subtraction, multiplication of whole numbers), scale doesn't matter and you can omit it.

A scale of 10 is a sensible default for decimal results. Increase it if the user needs more precision, or round to fewer places in your written answer if that reads more naturally (e.g. report a money result to 2 decimals).

## Running expressions

Pipe the expression into `bc`:

```bash
echo "47 * 89" | bc
```

Operators: `+ - * /` for arithmetic, `%` for remainder (integer modulo — note `%` respects scale oddly, so for remainders keep scale at 0), `^` for integer exponentiation, parentheses for grouping.

### Percentages

There's no percent operator. Translate explicitly:
- "12.5% of 340" → `scale=10; 340 * 12.5 / 100`
- "what % is 45 of 180" → `scale=10; 45 / 180 * 100`

### Chained / multi-step calculations

Use semicolons to run several statements, or compute intermediate values in one expression with parentheses:

```bash
echo "scale=4; (1200 + 350) * 1.08" | bc
```

## Reporting the result

Show the expression and the answer together so the user can verify and reuse it. For example:

> `47 * 89` = **4183**

> `scale=10; 340 * 12.5 / 100` = **42.5** (12.5% of 340)

Keep it concise. Trim trailing zeros or round in the written answer when it aids readability, but don't misrepresent the computed value.

## Edge cases

- **Division by zero**: `bc` prints a runtime error to stderr. Catch it and tell the user the calculation is undefined.
- **Negative results**: handled natively, no special treatment.
- **Very large integers**: `bc` is arbitrary-precision, so big numbers are fine with no overflow.
- **Leading-zero / formatting**: `bc` prints `.5` rather than `0.5`. Add the leading zero in your written answer if it's clearer.