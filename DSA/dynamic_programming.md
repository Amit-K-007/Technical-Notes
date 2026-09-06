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


### Minimum Path Sum

- Problem: Find a path from the top-left to the bottom-right of a grid such that the sum of all visited cells is minimum, moving only right or down.
- Link: https://leetcode.com/problems/minimum-path-sum/

---

**Algorithm**: Instead of counting paths, DP stores the minimum cost to reach each cell.
- Transition becomes: `current_cell + min(top, left)` (or `min(right, down)` in memoization).


<br>


### Triangle

- Problem: Find the minimum path sum from the top to the bottom of a triangle, where from each element you can move only to the same index or the next index in the row below.
- Link: https://leetcode.com/problems/triangle/

---

**Memoization**: Recursively find the minimum path sum from each position to the bottom.
- State represents the current position `(row, col)`.
- If the last row is reached, return its value.
- Store the minimum cost for each state to avoid recomputation.
- From every position, choose the minimum of:
  - moving to the same index,
  - moving to the next index.
 
---

**Tabulation**: Build the DP table from the bottom row upwards.
- Initialize the last row as the base case.
- For every cell moving upwards:
  - Add the current value to the minimum of its two reachable children.
- Continue until reaching the top.
- The top element stores the minimum path sum.
- **Space Optimized DP**: Use a single DP array initialized with the last row.


<br>


### Partition Equal Subset Sum

- Problem: Given an integer array nums, return true if you can partition the array into two subsets such that the sum of the elements in both subsets is equal or false otherwise.
- Link: https://leetcode.com/problems/partition-equal-subset-sum/

---

**Key Observation**:
- If the total sum is odd, equal partition is impossible.
- Otherwise, the problem reduces to finding a subset with sum: `total_sum / 2`

---

**Memoization**: Recursively decide whether to include or exclude each element.
- State consists of:
  - current index,
  - remaining target sum.
- If target becomes `0`, a valid subset is found.
- If index reaches the end or target becomes negative, return `False`.
- Store results of previously solved states to avoid recomputation.

---

**Tabulation**: Build DP table for subset sum: (n * target)
- Let `dp[i][j]` denote whether sum `j` can be formed using the first `i` elements.
- Initialize sum `0` as always possible.
- For every element:
  - Exclude it.
  - Include it if it does not exceed the current target.
- The final answer is whether `target = total_sum / 2` is achievable.

---

**Space optimized tabulation**: Use prev & curr table.
- `prev` stores achievable sums using previous elements.
- For every element, build `curr` using:
  - excluding the current element (`prev`),
  - including the current element (if possible).
- After processing each element, update `prev = curr`.
- Return whether the target sum is achievable.


<br>


### Cherry Pickup II

- Problem: Two robots start from the top-left and top-right corners of the grid. Find the maximum cherries both can collect while moving to the bottom row, where each robot can move diagonally left, down, or diagonally right.
- Link: https://leetcode.com/problems/cherry-pickup-ii

---

**Memoization**: Use recursion with state `(row, col1, col2)` representing the current row and positions of both robots.
- If either robot goes out of bounds, return a very small value (invalid path).
- At each state, collect cherries from both positions:
  - If `col1 == col2`, count cherries only once.
  - Otherwise, add cherries from both cells.
- Try all 9 possible movement combinations for the next row.
- Store the result for each state to avoid recomputation.

---

**Tabulation**: Build a 3D DP table bottom-up.
- Let `dp[row][col1][col2]` represent the maximum cherries collectable from that state.
- Initialize the last row with the cherries collected by both robots.
- For each row from bottom to top, check all 9 next moves and choose the maximum.
- Add the current row's cherries to the best next-state value.


<br>


### Coin Change

- Problem: Given coins of different denominations and an infinite supply of each, find the fewest number of coins needed to make up a given amount. Return `-1` if it cannot be made.
- Link: https://leetcode.com/problems/coin-change/

---

**Key Observation**:
- Unlike 0/1 knapsack style problems, each coin can be reused any number of times (unbounded).
- The problem reduces to: minimum number of coins summing exactly to `amount`.

---

**Memoization**: Recursively find minimum coins needed for a given remaining amount.
- State represents the remaining amount.
- If remaining amount becomes `0`, no more coins are needed (base case = 0).
- If remaining amount becomes negative, return infinity (invalid path).
- For every coin:
  - Try using it and recurse on `remaining - coin` (coin can be reused, so index doesn't decrease).
  - Take the minimum across all coin choices, adding 1 for the current coin used.
- Store results of previously solved states in DP to avoid recomputation.

---

**Tabulation**: Build answer from smaller subproblems.
- Let `dp[i]` represent the minimum coins needed to make amount `i`.
- Initialize `dp[0] = 0` (base case) and rest as infinity.
- For every amount from `1` to `amount`:
  - For every coin:
    - If coin value ≤ current amount, update `dp[i] = min(dp[i], dp[i - coin] + 1)`.
- If `dp[amount]` is still infinity, return `-1`; otherwise return `dp[amount]`.


<br>
