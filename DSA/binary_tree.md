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


### Maximum depth of binary tree

- Problem: Given the root of a binary tree, return its maximum depth. A binary tree's maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
- Link: https://leetcode.com/problems/maximum-depth-of-binary-tree/

---

- **Algorithm**: Max of left or right
- If `root` is nil, return `0`.
- Max depth of current node is max of (left subtree depth or right subtree depth) + 1.
- Recursively perform above logic from the root and return the answer.


<br>


### Balanced Binary Tree

- Problem: Check if for every node, the height difference of left and right subtree ≤ 1
- Link: https://leetcode.com/problems/balanced-binary-tree/

---

- **Algorithm**: Combine height + balance check in one DFS
- For each node, recursively get height of left subtree
  - If it returns `-1`, stop immediately → subtree already unbalanced
- Do the same for right subtree
- After getting both heights, check whether subtree is unbalanced, and if unbalanced, return `-1`
- If balanced, return actual height


<br>


### Binary Tree Maximum Path Sum

- Problem: A path in a binary tree is a sequence of connected nodes with no repeats and does not need to include the root. The path sum is the total of its node values. Given a binary tree root, return the maximum path sum of any non-empty path.
- Link: https://leetcode.com/problems/binary-tree-maximum-path-sum/

---

- **Algorithm**: Postorder traversal with path contribution
- Traverse the tree bottom-up so that at each node you already know the best contributions from left and right subtrees.
- Compute valid contributions from children. For each node:
```py
left = max(dfs(node.left), 0)
right = max(dfs(node.right), 0)

# (Ignore negative paths by taking 0 (they reduce total sum).)
```
- At each node, compute: `current = node.val + left + right`
- This represents the best path passing through this node. Maintain a global variable to store the maximum of all such values.
- Return only one side (to maintain valid path): `return node.val + max(left, right)`
- After traversal completes, return the global maximum path sum.


<br>


### Same tree

- Problem: Given the roots of two binary trees p and q, write a function to check if they are the same or not. Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.
- Link: https://leetcode.com/problems/same-tree/

---

- **Algorithm**: Bool of left and right
- For a pair of nodes to be identical:
    - Either both should be `nil`.
    - Or both should be not `nil` and should have the same value.
- Check if a pair of nodes is identical. If it is, recursively check the left and right pair.
- Also implement early stoping. If a pair is not identical, return `false` from there, don't evaluate other pairs.


<br>
