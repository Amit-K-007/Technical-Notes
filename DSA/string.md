# String

<br>

## Algorithms

<br>

## Problems

<br>

### Remove outermost parentheses

- Problem: Given a valid parentheses string s, split it into its primitive components. For each primitive part, remove its outermost parentheses, then return the resulting string.
- Link: https://leetcode.com/problems/remove-outermost-parentheses/

---

- **Algorithm**: Depth counter
- Maintain a counter `depth` that indicates how deep we are in a nested parentheses.
- While iterating over the string `s`, ignore `(` at depth 1 and `)` at depth 0.
- For rest of the parentheses, add them to a new string and return it.


<br>


