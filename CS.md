### Amortized Analysis 艾默儿
- Amortized analysis is a worst-case analysis of a a **sequence of operations** — to obtain a tighter bound on the overall or average cost per operation in the sequence than is obtained by separately analyzing each operation in the sequence.
- Set insertion operation has O(1) amortized run time because the time required to insert an element is O(1) **on average**, even though some elements trigger a O(n) rehashing of all the elements of the hash table, but array size grow geometrically (**doubling**). <= a sequence of n insert operations has overall time O(n)
- three main techniques
  - The aggregate method, where the total running time for a sequence of operations is analyzed.
  - The accounting (or banker's) method, where we impose an extra charge on inexpensive operations and use it to pay for expensive operations later on.
  - The potential (or physicist's) method, in which we derive a potential function characterizing the amount of extra work we can do in each step. This potential either increases or decreases with each successive operation, but cannot be negative.

