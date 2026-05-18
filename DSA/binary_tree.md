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


### Binary Tree Zigzag Level Order Traversal

- Problem: Given the `root` of a binary tree, return the zigzag level order traversal of its nodes' values. (i.e., from left to right, then right to left for the next level and alternate between).
- Link: https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal

---

- **Algorithm**: BFS traversal with alternating direction.
- Use a queue and process nodes level by level.
- Maintain a flag left_to_right for direction to follow at each level.
- For every level:
  - Create an array of size `level_size`.
  - If left_to_right, place node at index `i`.
  - Else place node at index `level_size - i - 1`.
- Push left and right child into queue while processing nodes.
- After every level, toggle direction flag and add current level to answer.


<br>


### Vertical order traversal

- Problem: You are given the root of a binary tree. Each node has a (row, col) position where left children go to (row+1, col–1) and right children to (row+1, col+1). You must return the vertical order traversal: group nodes by column from leftmost to rightmost, and within each column sort nodes first by row (top to bottom) and then by value if they share the same row and column.
- Link: https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/

---

- **Algorithm**: DFS traversal with node positions.
- Start traversal from `(row=0, col=0)`.
- For every node:
  - Store `(col, row, value)` in a list.
  - Move left → `(row+1, col-1)`
  - Move right → `(row+1, col+1)`
- Sort the list:
  - First by column
  - Then by row
  - Then by node value
- Iterate sorted nodes to get final vertical order traversal:
  - If column changes, create new column in answer.
  - Append node values to current column.

---

**Approach 2**: C++ approach using `Multiset`
- Multiset: Stores multiple values in sorted order, keeping duplicates also. `[2, 2, 4, 5]` 
- Use nested maps to group nodes by (column, row) and a multiset/sorted container to automatically keep nodes at the same position ordered by value, during insertion itself.


<br>


### Symmetric tree

- Problem: Given the root of a binary tree, check whether it is a mirror of itself (i.e., symmetric around its center).
- Link: https://leetcode.com/problems/symmetric-tree/

---

- **Algorithm**: Traverse 2 trees simultaneously
- Use a single recursive function to traverse 2 trees together.
- Trees 1: `root.left`. Tree 2: `root.right`.
- Both nodes should be absent or present with same value.
- If the above condition satisfies, got to left of node 1 and right of node 2. And then right of node 1 and left of node 2.
- Keep doing recursively until whole tree is traverse or the condition fails.


<br>


### Binary Tree Right Side View

- Problem: Given the root of a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.
- Link: https://leetcode.com/problems/binary-tree-right-side-view

---

- **Algorithm**: BFS Approach (Level Order Traversal)
- For every level:
  - Traverse all nodes of that level.
  - The last node processed is the rightmost node.
- Add the last node’s value of every level to answer.
- Push left and right child into queue while traversing.

---

**Approach 2**: DFS traversal prioritising right subtree.
- Traverse: `node -> right -> left`
- Maintain current depth/level.
- First node visited at a level is the visible rightmost node.
- If current level equals answer size:
  - Add node value to answer.
- Continue recursive traversal for right and left subtree.


<br>


### Lowest common ancestor

- Problem: Find the lowest common ancestor (LCA) of two nodes in a binary tree, where LCA is the lowest node that has both nodes as descendants (a node can be a descendant of itself).
- Link: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

---

- **Algorithm**: Return node or None
- Traverse the tree using postorder traversal (LRN).
- If current node is same as `p` or `q`, return that node.
- If we receive a node value from left subtree and right subtree, that means current node is the LCA. Return it.
- Otherwise if a value is received from left subtree, return it or right subtree or `None`.


<br>


### Maximum Width of Binary Tree

- Problem: Given a binary tree root, return its maximum width, defined as the largest number of nodes (including gaps) between the leftmost and rightmost non-null nodes at any level.
- Link: https://leetcode.com/problems/maximum-width-of-binary-tree/

---

- **Algorithm**: BFS traversal with complete binary tree indexing.
- Assign an index to every node: (idx is parent's index)
  - Left child → `2 * idx`
  - Right child → `2 * idx + 1
- For every level:
  - Width = `last_index - first_index + 1`
- Store `(node, index)` in queue during BFS traversal.
- Normalize indices at every level:
  - `idx -= first_index`
  - to avoid very large numbers for deep trees.
- Update maximum width for every level and return the answer.
```
        1(0)
      /      \
   3(0)      2(1)
   /            \
5(0)            9(3)
```


<br>


### All Nodes Distance K in Binary Tree

- Problem: Given the root of a binary tree, the value of a target node target, and an integer k, return an array of the values of all nodes that have a distance k from the target node.
- Link: https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree

---

- Algorithm: Parent Map + BFS
- Convert tree into graph using parent pointers, so that we can go left, right and up direction from any node.
- Traverse tree and store: `parent[child] = parent_node`
- Start BFS from target node.
- From every node, traverse:
  - left child
  - right child
  - parent node
- Use visited set to avoid revisiting nodes.
- When distance becomes `k`, add node value to answer.

---

- **Approach 2**: Path + Blocked Subtree Approach
- Find path from root to target and process ancestors one by one.
- Store path as: `(node, direction_to_block)` while returning from recursion.
- Starting from target node:
  - Run DFS to collect nodes at remaining distance.
  - Move upward through path and increase current distance.
- While processing an ancestor:
  - Block traversal towards subtree from which we came.
  - This avoids revisiting already processed nodes.
- Instead of permanently modifying tree:
  - Temporarily disconnect blocked branch using a temp variable.
  - Restore it after DFS call.
- Approach is made by me 🙂: https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/submissions/2004725936/


<br>



