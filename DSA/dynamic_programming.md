# Dynamic Programming

<br>

## Algorithms

<br>

## Problems

<br>

### Climbing Stairs

- Problem: You are climbing a staircase. It takes n steps to reach the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
- Link: https://leetcode.com/problems/climbing-stairs/

---

**Memoization**: Recursively find ways to reach step `n`.
- From any step, there are two choices:
  - Climb 1 step.
  - Climb 2 steps.
- Store results of previously solved states in DP array.
- Reuse stored values to avoid repeated computations.
- Number of ways for a step depends on the previous two steps.

---

**Tabulation**: Build answer from smaller subproblems.
- Start with base cases for first two steps: `dp[1] = 1, dp[2] = 3`
- For every step from `3` to `n`:
  - Ways to reach current step = ways to reach previous step + ways to reach step before that.
- Continue filling DP array until step `n`.
- Return the value stored for the last step.


<br>
