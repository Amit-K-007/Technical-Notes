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


### Copy List with Random Pointer

- Problem: Create and return a deep copy of a linked list where each node has both a next and a random pointer (to any node), ensuring all pointers in the new list point only to newly created nodes.
- Link: https://leetcode.com/problems/copy-list-with-random-pointer

---

- **Algorithm**: Interweaving Nodes Method
- For each original node, create its copy and insert it right after the original node.
`(A → B → C becomes A → A' → B → B' → C → C')`
- For each original node, assign:
`copy.random = original.random.next (if random exists).`
- Restore the original list and extract the copied list into a separate linked list.

---

- **Approach 2**: Traverse the original list and create a copy node for each original node.
- Store mapping: `old_node → new_node` in a dictionary.
- Traverse the list again and set:
```py
new_node.next = old_to_new[old_node.next]`
new_node.random = old_to_new[old_node.random]
(if they exist)
```


<br>


