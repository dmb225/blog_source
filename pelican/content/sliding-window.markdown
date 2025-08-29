Title: Algorithm Patterns: Sliding Window
Date: 2025-08-29
Category: Blog
Tags: algorithms

The **sliding window** pattern is a technique for efficiently processing sequential data, such as arrays and strings. It involves maintaining a dynamic window that slides across the data, allowing you to examine a fixed portion at a time. Instead of repeatedly scanning the entire dataset, this approach adjusts the window’s boundaries as needed to track relevant elements. It is especially useful for solving problems involving contiguous subarrays or substrings, such as finding the maximum sum of a subarray or detecting repeated patterns in a string. This pattern can be viewed as a variation of the two-pointer approach, where the pointers define the window’s start and end.

---

## About the pattern

Imagine you have a tray of 10 cookies and want to find the most chocolate chips in any 3 cookies next to each other. Using a naive approach, you’d stand at each cookie and count its chocolate chips along with those of its immediate left and right neighbors to form every possible group of 3. This means repeating the counting process for each cookie, which quickly becomes inefficient as the number of cookies grows.

We can avoid this hassle by using a smarter approach. Instead of recounting the chips for each group from scratch, you start by counting the chips in the first 3 cookies. Then, as you move to the next group, you simply subtract the chips from the cookie you leave behind and add the chips from the new cookie you include. 

Consider the following steps:

Step 1: Count the chips in the first three cookies. This is your starting total—and your initial “best so far.”

Step 2: Slide the window one cookie to the right:
- Subtract the chips from the cookie that just slipped out of the window.
- Add the chips from the fresh cookie that slid into view.

Step 3: If this new sum tops your current record, replace it with the initial “best so far”.

Step 4: Repeat the above steps for each group of three cookies, all the way to the last one.

By updating the total in constant time with each slide, you find the group of neighboring cookies with maximum chocolate chips without ever recounting the entire window.

The sliding window can be of a fixed size or dynamic.
- The fixed-size window is used when the window size is given or constant. For example, find the largest sum of any three consecutive numbers.
- The dynamic window is used when the window size changes based on conditions. For example, find the smallest subarray whose sum exceeds a target.

How is the sliding window implemented?  
The sliding window technique uses two pointers representing the window’s boundaries. These pointers move through an array or a string to examine a portion of the data at a time. The window is then updated by adding new elements and removing old ones as the pointers move.

In a fixed-size window, both pointers move together, keeping the window size constant. For example, if you need to find the largest sum of three consecutive numbers, the window size stays at three while sliding across the array.

In a dynamic window, the window size can grow or shrink based on a condition. One pointer (usually the right one) expands the window, while the other (left) contracts it when the condition is met, like finding the smallest subarray with a sum greater than a target. This two pointer strategy allows the sliding window to process data in linear time, making it a powerful tool for problems involving continuous data sections.

Here is the template for the sliding window, where we maintain a dynamic window that adjusts as we traverse the array.

```
FUNCTION slidingWindow(arr, k, processWindow):
  # Initialize the window result (sum, product, count, etc.)
  windowResult = INITIAL_VALUE
  
  # Compute the initial window's result
  FOR i FROM 0 TO k - 1:
    UPDATE windowResult WITH arr[i]

  # Process the first window
  processWindow(windowResult)

  # Slide the window across the array
  FOR i FROM k TO length of arr - 1:
    UPDATE windowResult BY ADDING arr[i]  # Add a new element to the window
    UPDATE windowResult BY REMOVING arr[i - k]  # Remove outgoing element
    processWindow(windowResult)  # Operation on the updated window
```

We start by computing the result for the first k elements and storing it in windowResult. This represents the initial window in the array. Next, we slide the window across arr by moving one step at a time. For each step:

1. Process the current window’s result using the function processWindow(windowResult). This step can involve updating a maximum, computing an average, counting occurrences, or any other required calculation.
2. Add the new incoming element (arr[i]) to the window, updating windowResult accordingly.
3. Remove the outgoing element (arr[i—k]) from the window to maintain the correct size.
4. Repeat the process until the entire array is covered, ensuring we efficiently compute the desired result for every possible k-sized subarray.

---

## How does the sliding window technique reduce time complexity?

We have learned that the sliding window helps process sequential data like arrays or strings efficiently. Let’s quickly see why that is the case.

- Avoids nested loops: Without a sliding window, many problems require checking all subarrays using two or more loops, leading to O(n2) time complexity or more. The sliding window allows us to update the window by adjusting pointers, reducing the complexity of traversing and processing the entire array to linear, O(n).
- Reuses computation: Instead of recalculating values for each window from scratch, the sliding window approach reuses previous calculations by adding new elements and removing old ones. In the example we discussed above, where we find the most chocolate chips in any 3 cookies, we only update the sum by adding the next cookie and removing the leftmost one.

---

## Examples

The following examples illustrate some problems that can be solved with this approach:

1. Maximum sum subarray of size K: Given an array of integers and a positive integer k, find the maximum sum of any contiguous subarray of size k.
2. Longest substring without repeating characters: Given a string, find the length of the longest substring without repeating characters.

---

## Real-world problems

Many problems in the real world use the sliding window pattern. Let’s look at some examples.

- Telecommunications: Find the maximum number of users connected to a cellular network’s base station in every k-millisecond sliding window.
- Video streaming: Given a stream of numbers representing the number of buffering events in a given user session, calculate the median number of buffering events in each one-minute interval.
- Social media content mining: Given the lists of topics that two users have posted about, find the shortest sequence of posts by one user that includes all the topics that the other user has posted about.

---

## Does your problem match this pattern?

Yes, if all of these conditions are fulfilled:

- Contiguous data: The input data is stored in a contiguous manner, such as an array or string.
- Processing subsets of elements: The problem requires repeated computations on a contiguous subset of data elements (a subarray or a substring), such that the window moves across the input array from one end to the other. The size of the window may be fixed or variable, depending on the requirements of the problem. 
- Efficient computation time complexity: The computations performed every time the window moves take constant or very small time.
