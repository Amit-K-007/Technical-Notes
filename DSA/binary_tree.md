# Binary Tree

<br>

## Algorithms

<br>

## Problems

<br>

### Iterative Preorder Traversal

- Problem: Given the `root` of a binary tree, return the preorder traversal of its nodes' values.
- Link: https://leetcode.com/problems/remove-outermost-parentheses/

---

- **Algorithm**: Use a stack to simulate recursive preorder traversal
- Process current node first → add value to result
- Push right child before left child
- Stack is LIFO, so left subtree gets processed first
- Continue until all nodes are visited


<br>


### Iterative Inorder Traversal

- Problem: Given the `root` of a binary tree, return the inorder traversal of its nodes' values.
- Link: https://leetcode.com/problems/binary-tree-inorder-traversal/

---

- **Algorithm**: Keep moving left and push nodes onto stack
- Leftmost node is processed first
- After reaching null, pop node and process it
- Then move to its right subtree
- Stack stores nodes whose left subtree is already explored
- Stack helps return to parent after fully exploring left subtree


<br>
