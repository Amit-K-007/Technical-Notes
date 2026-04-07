# Sliding Window

<br>

## Algorithms

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

