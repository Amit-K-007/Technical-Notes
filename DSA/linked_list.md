# Linked List

<br>

## Problems

<br>

### Detect loop in linked list

- Problem: Given head, the head of a linked list, determine if the linked list has a cycle in it.
- Link: https://leetcode.com/problems/linked-list-cycle/

---

- **Algorithm**: Slow fast pointer
- Start with `slow` pointer pointing to the head and fast pointer pointing to `head.next`.
- Move `slow` pointer by 1 step and `fast` pointer by 2 steps.
- If `fast` reaches the end, there is no loop.
- If there is a loop, eventually `slow` will be equal to `fast`.

---

- **Approach 2**: Use a `set` to keep track of visited nodes.
- While iterating if that node is present in the `set`, that means there is a loop.

---

- **Approach 3**: Traverse linked list by assigning each node INT_MAX value
- While iterating if any INT_MAX node present, then their is a cycle.

<br>

