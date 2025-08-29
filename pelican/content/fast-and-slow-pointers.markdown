Title: Algorithm patterns: Fast and Slow Pointers
Date: 2025-08-29
Category: Blog
Tags: algorithms

The **fast and slow pointers** pattern is a versatile and efficient technique for traversing linear data structures, such as arrays, linked lists, or even graphs, using two pointers that move at different speeds. This approach is often used to identify cycles or find a specific target. The speeds of the pointers can be adjusted according to the problem statement. The two pointers pattern focuses on comparing data values, whereas the fast and slow pointers method is typically used to analyze the structure or properties of the data.

---

## About the pattern

Similar to the two pointers pattern, the fast and slow pointers pattern uses two pointers to traverse an iterable data structure, but at different speeds, often to identify cycles or find a specific target. The speeds of the pointers can be adjusted according to the problem statement. The two pointers pattern focuses on comparing data values, whereas the fast and slow pointers method is typically used to analyze the structure or properties of the data.

The key idea is that the pointers start at the same location and then start moving at different speeds. The slow pointer moves one step at a time, while the fast pointer moves by two steps. Due to the different speeds of the pointers, this pattern is also commonly known as the Hare and Tortoise algorithm, where the Hare is the fast pointer while Tortoise is the slow pointer. If a cycle exists, the two pointers will eventually meet during traversal. This approach enables the algorithm to detect specific properties within the data structure, such as cycles, midpoints, or intersections.

To visualize this, imagine two runners on a circular track starting at the same point. With different running speeds, the faster runner will eventually overtake the slower one, illustrating how this method works to detect cycles.

Here’s a basic pseudocode template that you can adapt to your specific needs:

```
FUNCTION fastAndSlow(dataStructure):
  # initialize pointers (or indices)
  fastPointer = dataStructure.start   # or 0 if the data structure is an array
  slowPointer = dataStructure.start   # or 0 if the data structure is an array
  
  WHILE fastPointer != null AND fastPointer.next != null: 
    # For arrays: WHILE fastPointer < dataStructure.length AND (fastPointer + 1) < dataStructure.length:
    
    slowPointer = slowPointer.next            
    # For arrays: slowPointer = slowPointer + 1
    
    fastPointer = fastPointer.next.next       
    # For arrays: fastPointer = fastPointer + 2
    
    IF fastPointer != null AND someCondition(fastPointer, slowPointer):
      # For arrays: use someCondition(dataStructure[fastPointer], dataStructure[slowPointer]) if needed
      handleCondition(fastPointer, slowPointer)
      BREAK

  # process the result
  processResult(slowPointer)
  # For arrays: processResult(slowPointer) might process dataStructure[slowPointer]
```

In the template above, the fastPointer moves at twice the speed of the slowPointer, allowing efficient traversal and detection of specific conditions. 

The fastPointer advances two steps at a time (fastPointer.next.next for linked lists or fastPointer + 2 for arrays), while the slowPointer moves one step at a time (slowPointer.next or slowPointer + 1).

The loop continues until the fastPointer reaches the end of the structure or a condition (someCondition) is met, triggering handleCondition.

Finally, the result is processed using the slowPointer, which often points to a meaningful position, such as the middle of the structure or the start of a cycle.

---

## Examples

The following examples illustrate some problems that can be solved with this approach:

1. **Middle of the linked list:** Given the head of a singly linked list, return the middle node of the linked list.

The idea is to iterate the linked list using fast and slow pointers with slow pointer moving one step whereas the fast pointer moving two steps. When the fast pointer reaches the last node or becomes NULL, the slow pointer will be pointing to the middle of the linked list.

2. **Detect cycle in an array:** Given an array of non-negative integers where elements are indexes pointing to the next element, determine if there is a cycle in the array.

To detect a cycle in the given array, we'll use the fast and slow pointers. The slow pointer moves forward one step at a time, while the fast pointer moves two steps forward. If there is a cycle, the fast pointer will eventually meet the slow pointer.

---

## Real-world problems

Many problems in the real world use the fast and slow pointers pattern. Let’s look at some examples.

- **Symlink verification:** Imagine you’re an IT administrator responsible for maintaining a large server. In one of the directories, multiple files and symbolic links (symlinks) are scattered around, each pointing to various files or even to other symlinks. Occasionally, a well-intentioned script or misconfiguration might create a loop—say, a symlink A points to B, and somehow B eventually links back to A. This creates a cycle where following those symlinks never ends. In a symlink verification utility, the fast and slow pointer approach helps detect loops where symlinks endlessly reference each other. One pointer (the “tortoise”) follows links one step at a time, while the other (the “hare”) jumps two steps. If both ever land on the same file or link again, it confirms a cycle.

- **Compiling an object-oriented program:** During compilation, each object-oriented module may depend on others, sometimes creating a loop (A depends on B, and B depends on A). With fast and slow pointers, you track dependencies step by step: the “tortoise” moves one module at a time, and the “hare” jumps two modules at a time. If they meet, there’s a cyclic dependency. This quick, space-efficient check helps catch and break these loops, ensuring a clean compilation sequence.

---

## Does your problem match this pattern?

Yes, if the following condition is fulfilled:

- **Linear data structure:** The input data can be traversed in a linear fashion, such as an array, linked list, or string.

In addition, if either of these conditions is fulfilled:

- **Cycle or intersection detection:** The problem involves detecting a loop within a linked list or an array or involves finding an intersection between two linked lists or arrays.

- **Find the starting element of the second quantile:** This means identifying the element where the second part of a divided dataset begins—like the second half, second third (tertile), or second quarter (quartile). For example, the task might ask you to find the middle element of an array or a linked list, which marks the start.
