# Stack

<br>

## Algorithms

<br>

## Problems

<br>


### Implement stack using queues

- Problem: Implement a last-in-first-out (LIFO) stack using only two queues. The implemented stack should support all the functions of a normal stack (push, top, pop, and empty).
- Link: https://leetcode.com/problems/implement-stack-using-queues/

---

- **Algorithm**: Standard queue
- Create 2 queues. At any point in time, only 1 queue will be filled.
- Push: Add element to filled queue.
- Pop: Remove element from filled queue and put it in empty queue. Do this `n-1` times. Return the `nth` element.
- Top: Same as Pop, but instead of returning, add that element back in the other queue and return its copy.
- Empty: Check if queue is empty.

---

- Using single queue.
- Push & Empty logic is same.
- For Pop and Top, instead of adding the removed element in the second queue, add it at the end of first queue. Do this again for `n-1` times. The `nth` element will be the top element.


<br>


### Implement Queue using Stacks

- Problem: Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (push, peek, pop, and empty).
- Link: https://leetcode.com/problems/implement-queue-using-stacks

---

- **Algorithm**: Using two Stacks where push operation is O(N)
- Push Operation:
  - Transfer all elements from stack1 to stack2.
  - Add the new element to stack1.
  - Transfer all elements back from stack2 to stack1.
  - This ensures the new element is always at the front for the next pop operation.
- Pop Operation: Remove and return the top element from stack1.
- Top Operation: Return the top element of stack1 without removing it.
- Size Operation: Return the size of stack1.

---

- **Approach 2**: Using Two Stacks Where Push Operation is O(1)
- Push Operation: Add the element to inputStack. This operation is efficient and always takes O(1) time.
- Pop Operation:
  - If the outputStack is empty, move all elements from inputStack to outputStack. This reversal of order ensures that the oldest element is on top of the outputStack.
  - Remove and return the top element from outputStack. This represents the oldest element in the queue.
- Top Operation:
  - If the outputStack is empty, move all elements from inputStack to outputStack to access the oldest element.
  - Return the top element of outputStack without removing it. This gives the element that has been in the queue the longest.
- Size Operation: Return the sum of the sizes of both stacks. This total gives the number of elements currently in the queue.


<br>


### Min Stack

- Problem: Design a stack that supports push, pop, top, and retrieving the minimum element in constant time for each O(1).
- Link: https://leetcode.com/problems/min-stack

---

- **Algorithm**: Maintain main stack + min stack
- Push to min stack only if new value ≤ current min
- On pop, remove from min stack only if it matches
- Top of min stack = current minimum
- Keeps only necessary minimum values

---

- **Approach 2**: Store (value, min_so_far) pair in stack
- On push → compute min with previous
- No separate data structure needed
- Top always contains current min info
- Pop automatically restores previous min

---

- **Approach 3**: One Stack + Encoding (space optimized)
- When pushing a new minimum, store encoded `value = 2*val - min`
- Encoded value is always less than new min, acts as a marker
- On pop, if top < min → it’s encoded → restore previous min
- `top()` returns `min` when encoded value is at top
- Why it works:
  - Encoding stores previous min information implicitly
  - Formula:`encoded = 2*new_min - old_min`
  - On pop: `old_min = 2*current_min - encoded`
  - This allows recovering past minimum without extra space

 
<br>

