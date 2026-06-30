+++
date = '2026-06-03T03:20:00+00:00'
draft = true
title = 'Heavy Light Decomposition - Explained'
author = "Prayag Jain"
hideFromRSS = true
+++

# Introduction
This is my first post where I explain algorithms and data structures that I find cool. I found out about heavy light decomposition when I was trying to solve the CSES problem [Path Queries II](https://cses.fi/problemset/task/2134). Most implementations of HLD you'll see will focus on writing code quickly, as they're often meant for competitive programmers. Obviously, you don't want to waste time writing clean code in a coding contest, but when you're learning, I'd say writing clean code is quite fun. So this is my attempt to explain what heavy light decomposition is, why we need it, and how it works. I'll be using C++ for this tutorial, but the concept remains universal.

# Segment Trees
Before understanding HLD, we must first look at what Segment Trees are. If you already know what they are and how they work, feel free to skip this section. Let us suppose that you have an array of integers `[4,5,1,2,8,5,7,6]`. If I ask you the sum of all integers starting at index `l` and ending at index `r`, your first instinct would be to use a simple for loop like this:
```cpp
void getSum(const vector<int> &arr, const int l, const int r) {
    int sum = 0;

    for (size_t pos = l; pos <= r; pos++) {
        sum += arr[pos];
    }

    return sum;
}
```

To answer this query, we just performed $r - l + 1$ operations. In the worst case, when `l` is beginning of the array and `r` is the end, we'd need to perform a total of $N$ operations. Here, $N$ is the size of the array. This is fine if the sum needs to be calculated only once. Consider a situation where the array has approximately $10^5$ elements. To answer one query of this type, we'd need to perform $10^5$ operations in the worst case. If we have, say, $10^4$ queries, we'd need to perform over $10^9$ operations. This could become really slow. 

