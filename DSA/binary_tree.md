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


### Iterative Postorder Traversal

- Problem: Given the `root` of a binary tree, return the postorder traversal of its nodes' values.
- Link: https://leetcode.com/problems/binary-tree-postorder-traversal/

---

- **Algorithm**: Perform modified preorder traversal: `Root → Right → Left`
- Store traversal order in result array
- Move right first instead of left
- Reverse final result to obtain postorder
- Stack helps simulate DFS traversal iteratively
- Why this works:
  - Normal preorder: `Root → Left → Right`
  - Swapping left/right gives:: `Root → Right → Left`
  - Reversing this sequence produces: `Left → Right → Root`
  - which is postorder traversal

---

**Approach 2**: Use a stack to simulate recursive postorder traversal (Similar to iter. preorder)
- Process current node first → add value to result
- Push left child before right child
- Stack is LIFO, so right subtree gets processed first
- Continue until all nodes are visited


<br>


### Binary Tree Level Order Traversal

- Problem: Given the `root` of a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level).
- Link: https://leetcode.com/problems/binary-tree-level-order-traversal

---

- **Algorithm**: BFS using Queue
- Since we need nodes level by level, use a queue so nodes are processed in the same order they are discovered
- Start by pushing the root into the queue
- At every iteration, the queue already contains all nodes of the current level
- Store current queue size and process exactly those many nodes to form one level
- While processing a node, push its left and right children into the queue so they become part of the next level
- Repeat until queue becomes empty


<br>


### Preorder, Inorder, and Postorder Traversal in one Traversal

- Problem: Given the root of a Binary Tree, return the preorder, inorder and postorder traversal sequence of the given tree by making just one traversal.
- Link: https://takeuforward.org/data-structure/preorder-inorder-postorder-traversals-in-one-traversal

---

- **Algorithm**: Stack + Traversal States
- Keep separate list to maintain each traversal
- Each node is processed in 3 stages:
  `preorder → inorder → postorder`
- so store node along with its current traversal state
- When node is popped with `state = 1`:
  - Add to preorder
  - push same node with `state = 2` to process inorder later
  - then move to left subtree
- When node is popped with `state = 2`:
  - Add to inorder
  - push same node with `state = 3` to process postorder later
  - then move to right subtree
- When node is popped with state = 3:
  - both subtrees are already processed
  - Add to postorder


<br>
