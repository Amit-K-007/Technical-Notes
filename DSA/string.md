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




