# Sliding Window

<br>

## Algorithms

<br>

### Prefix Sum + HashMap

- A technique where we maintain a **running cumulative sum (prefix sum)** and use a **hashmap to store frequencies of previous sums**, so we can efficiently find subarrays that satisfy a target condition by checking if a required previous sum exists.

**Core Logic**
- Keep a running `prefix_sum`
- At each index, check how many times `(prefix_sum - k)` has appeared
- Add that frequency to answer and update current prefix in hashmap

**Reason (Why it works)**
- A subarray sum is:
  `prefix[j] - prefix[i] = k`
- Rearranged:
  `prefix[i] = prefix[j] - k`
- So for current position j, any previous prefix equal to (current - k) forms a valid subarray
- Hashmap ensures we count **all such previous occurrences efficiently**

**Key Properties**
- Time Complexity: O(n)
- Space Complexity: O(n) (to store prefix sums)
- Supports negative numbers (unlike sliding window which usually requires non-negative numbers).

**Reference Problems**
- Subarray Sum Equals K → Classic exact sum problem
- Count Number of Nice Subarrays (alt approach) → Can also be done with prefix (odd → 1, even → 0)
- Maximum Size Subarray Sum Equals K → Store first occurrence of prefix
- Contiguous Array (Equal 0s and 1s) → Convert 0 → -1, reduce to sum = 0
- Subarrays Divisible by K → Use prefix mod k


<br>


### Sliding Window + AtMost Trick

- A technique where we use a **two-pointer sliding window to count subarrays satisfying a “≤ k” condition**, and compute exact results by using the relation: **exact(k) = atMost(k) − atMost(k−1)**, leveraging the monotonic nature of non-negative arrays.

**Core Logic**
- Count subarrays with sum ≤ k using sliding window
- Do the same for ≤ (k - 1)
- Subtract:
  `exact(k) = atMost(k) - atMost(k - 1)`

**Reason (Why it works)**
- Sliding window can efficiently count **all subarrays with sum ≤ k**
- But exact `k` is hard to track directly
- So:
  - `atMost(k)` includes all valid + extra (sum < k)
  - `atMost(k-1)` includes only extra
- Subtracting removes extras → leaves only **exact k**
- Works because array is **non-negative**, so window expands/shrinks predictably

**Key Properties**
- Time Complexity: O(n)
- Space Complexity: O(1) (no extra data structures)
- Works only for non-negative arrays (binary / positive)

**Reference Problems**
- Binary Subarrays With Sum → Direct application of atMost trick
- Count Number of Nice Subarrays → Count subarrays with k odd numbers
- Subarrays with K Different Integers → Exactly k distinct → atMost(k) - atMost(k-1)
- Max Consecutive Ones III (variation) → At most k zeros → sliding window
- Fruit Into Baskets → At most 2 distinct elements

<br>

## Problems

<br>

### Longest Substring Without Repeating Characters

- Problem: Find the length of the longest substring without duplicate characters
- Link: https://leetcode.com/problems/longest-substring-without-repeating-characters/

---

- **Algorithm**: Variable length sliding window
- Maintain a window `[left, right]` with unique characters
- Expand `right` to include new characters
- If duplicate found → shrink from `left` until valid
- Use a `set` to store current window characters

---

- **Approach 2**: Sliding Window + HashMap
- Use a sliding window with two pointers
- Store last seen index of each character
- On duplicate → jump left pointer by (last seen index + 1) directly instead of shrinking step-by-step
- And update the last seen index of that character


<br>

### Max Consecutive Ones III

- Problem: Find maximum number of consecutive 1's in the array if you can flip at most k 0's.
- Link: https://leetcode.com/problems/max-consecutive-ones-iii/

---

- **Algorithm**: Variable length sliding window
- Maintain a window `[left, right]`
- Expand `right` pointer
- Count number of zeros in the window
- If zeros exceed `k`, shrink window from `left`

--- 

- **Approach 2**: Queue Based window
- Use `queue` data structure for `O(1)` shrinking.
- Store indices of zeros in a queue
- When zeros exceed `k`, remove the oldest zero (front of queue)


<br>


### Longest Repeating Character Replacement

- Problem: Find the length of the longest substring where you can make all characters the same using at most k replacements of any characters.
- Link: https://leetcode.com/problems/longest-repeating-character-replacement/

---

- **Algorithm**: Variable length sliding window
- Keep track of frequency of characters and the max occurring character
- Window is valid if: `window_size - max_freq ≤ k`
- Expand window greedily, shrink when invalid


<br>


###  Maximum Points You Can Obtain from Cards

- Problem: Given an array cardPoints and an integer k, pick exactly k cards from either the start or end of the array to maximize your total score.
- Link: https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/

---

- **Algorithm**: Sliding Window on Ends
- Start by taking all k cards from the front
- Gradually shift selection: remove one from front, add one from back
- This keeps total cards = k, but changes front vs back balance
- Track the maximum score during the process


<br>


### Binary subarrays with sum

- Problem: Given a binary array nums and an integer goal, return the number of non-empty subarrays with a sum goal.
- Link: https://leetcode.com/problems/binary-subarrays-with-sum

---

- **Algorithm**: Prefix Sum + HashMap
- Maintain a running prefix sum while traversing the array.
- For each index, check if `(prefix_sum - goal)` exists in a hashmap.
- If it exists, it means a subarray ending here has `sum = goal`.
- Store frequency of each prefix sum to handle multiple occurrences.
- Initialize hashmap with `{0:1}` to handle subarrays starting from index 0.

---

- **Approach 2**: Sliding Window (At Most Trick)
- Use identity:
  exact(goal) = atMost(goal) - atMost(goal - 1)
- Build helper to count subarrays with sum ≤ k using sliding window.
- Expand window by moving right pointer; shrink when sum exceeds `k`.
- For each position, add `(right - left + 1)` valid subarrays.
- Works because array is binary (non-negative) → window never breaks unpredictably.


<br>


### Count Number of Nice Subarrays

- Problem: Given an array of integers nums and an integer k. A continuous subarray is called nice if there are k odd numbers on it. Return the number of nice sub-arrays.
- Link: https://leetcode.com/problems/count-number-of-nice-subarrays

---

- Both approaches are similar to above question **Binary subarrays with sum**
- Just use count of odd numbers instead of the sum of numbers


<br>


### Number of Substrings Containing All Three Characters

- Problem: Given a string s consisting only of characters a, b and c. Return the number of substrings containing at least one occurrence of all these characters a, b and c.
- Link: https://leetcode.com/problems/number-of-substrings-containing-all-three-characters

---

- **Algorithm**: Variable sliding window + Counting at each step.
- Use 2 pointers `i & j`.
- Increment `j` till you find the first window containing `a,b,c`.
- This window and all the windows to the right (by increasing `j`) are valid. Add this count to `total`.
- Now decrement `i`. If the window is valid, add the same above count to `total`.
- Continue decrement of `i` till the window becomes invalid.
- Again start from 2nd step.


<br>


### Fruit Into Baskets
- Problem: Find the length of the longest contiguous subarray that contains at most 2 distinct fruit types.
- Link: https://leetcode.com/problems/fruit-into-baskets/

---

- **Algorithm**: Initialize two variables to store the two recent fruit types seen and their last positions.
- Traverse the array while expanding the window to include current fruits as long as they match the two types.
- If a third fruit type appears, update the window to start just after the last occurrence of one of the older fruit types.
- Track the maximum length of valid windows throughout the traversal.

---

- **Approach 2**: Hashmap + sliding window
- Initialize two pointers for the window: start and end.
- Use a hash map to store the frequency of each fruit type within the current window.
- Iterate through the array using the end pointer.
- Add the current fruit to the map and update its count.
- If the map size exceeds 2 (more than 2 fruit types in window), shrink the window from the start pointer until the map becomes valid (size ≤ 2).
- At each step, track the maximum length of the valid window.


<br>
