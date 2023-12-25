---
title: 42Singapore - How To Solve 'push_swap' Problem
author: Frank Nguyen
pubDatetime: 2023-12-25T12:00:00+08:00
postSlug: how-to-solve-push-swap
featured: false
draft: false
tags:
  - how-to
  - 42singapore
description: Guide to solve the 'push_swap' problem from 42 Singapore programme
---

![Push Swap Machine](@assets/push-swap.jpeg)

[push_swap](https://github.com/frankng-sg/42singapore/blob/main/cursus/pdf/push_swap/push_swap.pdf) is a project that requires you to implement efficient sort algorithm on a circular linked list using unconventional operations a.k.a push, swap, rotate, and reverse rotate.

## Table of contents

## Introduction

Traditional sorting algorithms operate on arrays that allow to swap any two numbers. In this project, you are required to sort a circular linked list which you can only swap the first two numbers. Therefore, traditional sorting algorithms such as quick sort and merge sort are not suitable for this project. Also, for small sequences contain 3 to 5 numbers, you should produce optimal sorting stategies. To make computation easier to work with, you should represent each user-input value by its index within its sorted form. For example, the list {999, -13, 45} would be stored as {2, 0, 1}. In this way, you will always work with a list of numbers from 0 to N - 1.

## Sort a stack of 2 numbers

This requires at most 1 operation. If the first element is greater than the second element, you can swap the first two numbers. Otherwise, you don't need to do anything.

## Sort a stack of 3 numbers

This requires at most 2 operations. Let us look at all possible cases:

- 0 1 2: no operation is required

- 0 2 1: swap the first two numbers + then rotate the list

- 1 0 2: swap the first two numbers

- 1 2 0: reverse rotate the list

- 2 0 1: rotate the list

- 2 1 0: swap the first two numbers + then reverse rotate the list

## Sort a stack of 4 numbers
There are 24 combinations of 4 numbers so you may feel lazy to precompute solutions for all possible cases. With the help of an additional stack, you can sort a stack of 4 numbers with at most 7 moves. Here is how it works:

Step 1: Push 3 to stack B (Max 3 moves)

Step 2: Sort stack A (Max 2 moves)

Step 3: Push 3 to stack A (1 move)

Step 4: Rotate stack A (1 move)

Maximum number of moves = 7 moves

## Sort a stack of 5 numbers

There are 120 combinations of 5 numbers so it is unwise to precompute solutions for all possible cases. You can come up with a sorting strategy and test your stategy with all possible combinations to find out the maximum moves in the worse case scenario. In this project, the limit is 12 moves to sort a sequence of 5 numbers. To achieve this, you can use `3/2 strategy`. Here is how it works:

Step 1: Push 3 to stack B (Max 3 moves)

Step 2: Push 4 to stack B (Max 3 moves)

Step 3: Sort stack A (Max 2 moves)

Step 4: Push 4 and 3 to stack A (2 moves)

Step 5: Rotate stack A twice (2 moves)

Maximum number of moves = 12 moves

## Sort a stack of more than 5 numbers

You can look through the [list of sorting algorithms](https://en.wikipedia.org/wiki/Sorting_algorithm) and pick the most suitable one for this project. I have experimented with bubble sort, insertion sort, and radix sort.

### Bubble Sort

This algorithm will keep checking the stack is in "sorted state". The stack in "sorted state" means you can return sorted stack just by rotating moves, for example, {3 4 0 1 2} is called in "sorted state" because you bring the stack to {0 1 2 3 4} by rotating 2 times. If it can't, it will find the location of two adjacent numbers that can be swapped. It will rotate the stack until the two numbers are at the top of the stack and then swap them. It will repeat this process until the stack is in "sorted state". After that, it will rotate the stack until 0 is at the top of the stack.

```
bubble_sort(stack_a) {
  N = number of elements in the stack_a
  while true {
    loc = find_swappable_location(stack_a)
    if loc < 0
      break
    if loc > 0 {
      if loc > N / 2
        reverse rotate stack_a (N - loc) times
      else
        rotate stack_a (loc) times
    }
    swap the first two numbers of stack_a
  }

  loc = find_location_of_key(stack_a, 0)
  if loc == 0
    return stack_a
  if loc > N / 2
    reverse rotate stack_a (N - loc) times
  else
    rotate stack_a (loc) times
  return stack_a
}
```

### Insertion Sort

This algorithm will push stack_a 's numbers from smallest to largest to stack_b. Hence, stack_b will be sorted in descending order. After that, it will push stack_b 's numbers back to stack_a and stack_a will be sorted in ascending order.

```
insertion_sort(stack_a) {
  N = number of elements in the stack_a
  for i = 0 to N - 1 {
    loc = find_location_of_key(stack_a, i)
    if loc > (N - i) / 2
      reverse rotate stack_a (N - loc) times
    else
      rotate stack_a (loc) times
    push stack_a to stack_b
  }

  while stack_b is not empty {
    push stack_b to stack_a
  }
}
```

### Radix Sort

Since all integers can be represented with 0's and 1's, you can go through each bit from the least significant bit (LSB) to the most significant bit (MSB), i.e. from bit 0th to nth. At bit ith, you check each number in the stack, if bit ith of the number is 0, you push the number to stack_b, otherwise, you rotate the stack_a. After going through all the numbers in the stack_a, you will push back numbers from stack_b to stack_a. You will repeat this process until you go through all the bits.

For example, a sequence of {2, 3, 0, 1} can be represented as {"10", "11", "00", "01"}

**Starting from bit 0th (LSB)**

stack_a = {"10", "11", "00", "01"}, stack_b = {} <br/>
--- bit 0th of "10" is 0, so push "10" to stack_b

stack_a = {"11", "00", "01"}, stack_b = {"10"} <br/>
--- bit 0th of "11" is 1, so rotate the stack_a

stack_a = {"00", "01", "11"}, stack_b = {"10"} <br/>
--- bit 0th of "00" is 0, so push "00" to stack_b

stack_a = {"01", "11"}, stack_b = {"00", "10"} <br/>
--- bit 0th of "01" is 1, so rotate the stack_a


stack_a = {"11", "01"}, stack_b = {"00", "10"} <br/>
--- you have checked all numbers in stack_a, so now push back numbers from stack_b to stack_a

stack_a = {"10", "00", "11", "01"}, stack_b = {} <br/>
--- this is the result after going through bit 0th

**Continue with bit 1th (MSB)**

stack_a = {"10", "00", "11", "01"}, stack_b = {} <br/>
--- bit 1th of "10" is 1, so rotate the stack_a

stack_a = {"00", "11", "01", "10"}, stack_b = {} <br/>
--- bit 1th of "00" is 0, so push "00" to stack_b

stack_a = {"11", "01", "10"}, stack_b = {"00"} <br/>
--- bit 1th of "11" is 1, so rotate the stack_a

stack_a = {"01", "10", "11"}, stack_b = {"00"} <br/>
--- bit 1th of "01" is 0, so push "01" to stack_b

stack_a = {"10", "11"}, stack_b = {"01", "00"} <br/>
--- you have checked all numbers in stack_a, so now push back numbers from stack_b to stack_a

stack_a = {"00", "01", "10", "11"}, stack_b = {} <br/>
--- this is the result after going through bit 1th

Since you have reached the MSB, the stack_a is sorted.<br/>
stack_a = {"00", "01", "10", "11"} is also {0, 1, 2, 3}

**Conclusion**
The radix sort algorithm is simple to implement and its performance is reasonable. You may not get the perfect score with radix sort but you will definitely pass the project.

## Testing your algorithm

I understand that you are eager to benchmark your algorithms with other students so I wrote [Push Swap Checker](https://github.com/frankng-sg/42singapore/tree/main/cursus/push_swap_checker) just for that. I hope you enjoy push_swapping!

![Push Swap Checker Preview](@assets/push_swap_checker.gif)
