# Binary Search Tree

<br>

## Problems

<br>

### BST Iterator

- Problem: Design an iterator for a binary search tree that traverses the nodes in in-order sequence.
- Link: https://leetcode.com/problems/binary-search-tree-iterator/

- Algorithm: Inorder traversal using stack
- Use a stack to store current node and it's left lines on nodes in constructor.
- If len(stack) > 0, next element is present. Next element is the top element of the stack.
- Pop the top element, insert left line of nodes of it's right child in the stack and return popped value.
