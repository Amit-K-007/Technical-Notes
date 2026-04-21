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


