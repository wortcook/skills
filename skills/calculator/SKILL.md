---
name: calculator
description: Performs math calculations. Use when it is necessary to peform a numeric calculation.
compatibility: Requires echo,bash,bc
allowed-tools: Bash(echo:*),Bash(bc:*)
---

## Calculator Skill
invoke bc skill to find math calculation using bc.

The expression precedence is as follows: (lowest to highest)

|| operator, left associative
&& operator, left associative
! operator, nonassociative
Relational operators, left associative
Assignment operator, right associative
+ and - operators, left associative
*, / and % operators, left associative
^ operator, right associative
unary - operator, nonassociative
++ and -- operators, nonassociative

Use parenthesis when unsure of precedence to ensure accuracy of answer. 1+2 * 3 should give an answer of 7. make clear by using 1+(2 * 3) to avoid errors
Parenthesis must be balanced, for every ( there must be a ) 

## General pattern
```bash
echo "<EQUATION>" | bc -l
```

## Examples
what one plus one or 1 + 1
```bash
echo "1+1"|bc -l
```

what 2 * (3+5)
```bash
echo "2*(3+5)"|bc -l
```

what two to the 8th
```bash
echo "2^8"|bc -l
```