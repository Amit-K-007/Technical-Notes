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


### House Robber II

- Problem: Given money in houses arranged in a circle, find the maximum amount that can be robbed without robbing two adjacent houses. Since the first and last houses are also adjacent, they cannot both be robbed.
- Link: https://leetcode.com/problems/house-robber-ii/

---

**Memoization**: Recursively decide whether to rob or skip the current house.
- State consists of:
  - Current house index.
  - Whether the first house was robbed.
- If first house is robbed, last house cannot be considered.
- Store results of previously solved states in DP.
- For every house:
  - Rob it and move two houses ahead.
  - Skip it and move to next house.
- Return the better of the two choices.

---

**Tabulation**: Solve House Robber I for both valid ranges.
- Define: `dp[i] = maximum money that can be robbed till house i`
- For every house:
  - Either rob current house and add best till `i-2`.
  - Or skip current house and keep best till `i-1`.
- Fill DP array from left to right.
- Compute answer separately for both ranges and return the maximum.

---

**Space Optimized**: Same DP transition as tabulation.
- Observe that current state depends only on previous two states.
- Maintain:
  - Best answer till previous house.
  - Best answer till house before previous.
- Update these values while traversing houses.
- Solve for both valid ranges and return the maximum.


<br>

