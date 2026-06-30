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


### Unique Paths

- Problem: Find the total number of unique paths from the top-left to the bottom-right of an m × n grid, moving only right or down.
- Link: https://leetcode.com/problems/unique-paths

---

**Brute Force (Recursion)**: Explore all possible paths recursively.
- From every cell, try both possible moves:
  - Move right.
  - Move down.
- If the path goes outside the grid, discard it.
- If the destination is reached, count it as one valid path.
- Sum all valid paths obtained from both choices.

---

**Memoization**: Store the number of paths from each cell to avoid recomputation.
- State represents the current cell `(row, col)`.
- If a cell's answer is already computed, reuse it.
- Otherwise, compute it recursively using right and down moves.
- Store the result before returning.

---

**Tabulation**: Build the solution from smaller subproblems.
- Let `dp[i][j]` represent the number of ways to reach cell `(i, j)`.
- First row and first column have only one possible path.
- Every other cell can be reached either from:
  - the top cell, or
  - the left cell.
- Fill the DP table row by row and return the last cell.

---

**Space Optimized DP**: Use only one row of DP instead of the entire grid.
- Observe that each cell depends only on:
  - current row's previous column, and
  - previous row's same column.
- Update the DP array while traversing each row.
- The last element stores the total number of unique paths.


<br>


### Unique Paths II

- Problem: Find the total number of unique paths from the top-left to the bottom-right of a grid containing obstacles, moving only right or down. Cells marked 1 are blocked and cannot be visited.
- Link: https://leetcode.com/problems/unique-paths-ii/

---

**Algorithm**: Similar to previous problem
- Obstacle Handling: Treat obstacle cells as having 0 paths. During recursion/DP, immediately return or store 0 for blocked cells so they do not contribute to any valid path.


<br>
