+++
tags = []
categories = []
draft = false
image = ""
weight = 5
writer = "Lukas Herman"
title = "Greedy Algorithm (Lecture)"
date = "2018-01-23T19:24:27-04:00"
+++


Lecture by James Wang (jzwang@clemson.edu)

>Taking the maximum benefit without considering what will happen next; greedy algorithms just need to maximize the local cost.


Greedy algorithms can be parallelized well. Each CPU can be used to calculate the local min/max. **But**, you might not find the best solution using greedy algorithms.


Steps to finding efficient greedy algorithm:

1. Start by finding a dynamic programming style solution
2. Each recursive step need to solve the local min/max (find the greedy choice)
3. Find the recursive solution using the greedy choice
4. Convert to an iterative algorithm if possible

**More generally**, taking the direct approach:

1. Show the problem is reduced to a sub problem via a greedy choice
2. Prove there is an optimal solution containing the greedy choice
3. Prove that combining the greedy choice with an optimal solution of the remaining sub problem yields an optimal solution

Problems that can work well with being greedy:

1. **Greedy-choice property**: A global optimum can be arrived at by selecting a local optimum
2. **Optimal substructure**: An optimal solution to the problem contains an optimal solution to subproblems

## Example


![Screenshot-from-2018-01-23-08-32-44](/content/images/2018/01/Screenshot-from-2018-01-23-08-32-44.png)
*Note: the picture is taken from my algorithm course's lecture*



Properties of optimal solution:

- \\# of pennies <= 4
  - Proof: Replace 5 pennies with 1 nickel
- \\# of nickels <= 1
- \\# of quarters <= 3
- \\# of nickels + # of dimes <= 2
  - Proof:
    - Replace 3 dimes and 0 nickels with 1 quarter and 1 nickel
    - Replace 2 dimes and 1 nickel with 1 quarter
    - Recall: at most 1 nickel


![Screenshot-from-2018-01-23-08-47-19](/content/images/2018/01/Screenshot-from-2018-01-23-08-47-19.png)
*Note: the picture is taken from my algorithm course's lecture*


