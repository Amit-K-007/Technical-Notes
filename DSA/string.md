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


### Reverse the order of words
- Problem:
    ```
    input = "a good   example"
    output = "example good a"
    ```

---

- **Algorithm**: Reverse of reverse is the original.
- Reverse the whole string while handling the spaces.
- Reverse individual words.


<br>


### Rotate string

- Problem: Given two strings s and goal, return true if and only if s can become goal after some number of shifts on s. A shift on s consists of moving the leftmost character of s to the rightmost position.
- Link: https://leetcode.com/problems/rotate-string/

---

- **Algorithm**: Rotated string
- When searching in a rotated string or array, do `double = s+s`.
- Iterate over `double`, the rotated part starting from index `i` will be `double[i:i+n]`.


<br>


### Sort characters by frequency

- Problem: Given a string s, sort it in decreasing order based on the frequency of the characters. Return the sorted string. If there are multiple answers, return any of them.
- Link: https://leetcode.com/problems/sort-characters-by-frequency/

---

- **Algorithm**: String + hashmap
- Calculate frquency of characters using `Counter(s)` in python OR hashmap.
- Create an array of `(-freq, key)` and sort the array.
- Iterate through the sorted array and construct the output.


<br>


### Roman number to integer

- Problem: Convert roman number string to its representative integer. The string only contains `I(1), V(5), X(10), L(50), C(100), D(500), M(1000))`.
```py
input = "MCMXCIV"
output = 1994
```

---

- **Algorithm**: Simple string traversal.
- Create a mapping of Roman symbols to integers.
- Traverse the string from left to right.
- For each character:
  - If its value is less than the next character, subtract it.
  - Otherwise, add it.
- Sum all values to get the final integer.


<br>


### Longest Palindromic Substring

- Problem: Given a string `s`, return the longest palindromic substring in `s`
- Link: https://leetcode.com/problems/longest-palindromic-substring/description/

---

- **Algorithm**: Check palinedromes by considering all 2n + 1 centers
- At each step, find the longest palindromic substring with centre as `s[i]`. Odd length palindromic substring.
- Also, find the longest palindromic substring with centre as `s[i], s[i+1]`. Even length palindromic substring.
- Keep track of longest palindromic substring and return it.

---

- **Approach 2**: Use a 2D DP table to store whether a substring `s[i…j]` is a palindrome.
- Traverse the string using gap strategy `(j - i)`:
    - If `i == j` → single character → palindrome.
    - If `j - i == 1` → two characters → palindrome if both equal.
    - For length ≥ 3 → palindrome if:
        `s[i] == s[j] AND dp[i+1][j-1] == True`

---

- **Approach 3**: Manacher’s Algorithm
- Use symmetry and previously computed palindrome information to find the longest palindromic substring in linear time.
- Transform string by adding separators (`#`) to handle even & odd palindromes uniformly.
- Maintain:
    - `P[i]` → radius of palindrome centered at index `i`
    - `C` → center of current palindrome
    - `R` → right boundary of current palindrome
- For each index i:
    - Find mirror index: `mirror = 2*C - i`
    - If `i < R`, initialize:
        `P[i] = min(R - i, P[mirror])`
    - Expand palindrome centered at `i` while characters match
    - If palindrome expands beyond `R`, update `C` and `R`
- After processing all indices, find the maximum value in `P[]` and map it back to the original string.


<br>
