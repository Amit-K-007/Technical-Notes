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
