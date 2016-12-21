---
layout: post
comments: true
categories: algorithms
title: Algorithms on ranges - part 1
---
Usually, what we concerns or what we process in a problem is single element. That is to say, elements in the input are
often treated as individual units. These problems could be solved with popular data structures like hash table, trees or graphs.
What if the problem is more concerned about a group of elements, specifically, a range of elements in the input, how should we
handle them with efficient solutions? This article will discuss some famous problems regarding to algorithms on ranges, and how
to efficiently solve them with existing known data structures and algorithms.

### Typical Problems on ranges
> Given an input array A[0...n], write a function to calculate the sum of a given range [i, j](both inclusive) in the array. For example,
> A = [0, 1, 2, 3, 4], range [i, j] = [1, 3], the sum will be s = 6. The function should support arbitrary query of any valid ranges
> for that input array.

A variant of the above problem is:
> What if the elements in the input array could be updated during the query. That means the array is not fixed. There is a update
> function update(i, val) could update the value of element in the position i in the input array. In this case, how to efficiently
> support arbitrary range queries.

Apart from the sum of a range, sometimes we might concern other properties of a range
> Given an input array A[0...n], write a function to support arbitrary query of the minimum value *min* of a range [i, j]. For example,
> A = [5, 3, 1, 4, 2], range [i, j] = [1, 4], then the minimum *min* = 1.

Instead of the minimum value of a range, *median* and *mode* might be the properties we need. These variants look similiary to 
each other, we might think that there might be a generalized solution for them. How do we efficiently solve these problems with 
a general methodology.

Let's look further. What if the input array is not a 1-dimension array, instead it is a 2-dimension or even n-dimension array, is
this problem the same with what we discuss above? The problem could be stated as:
> Given an 2-dimension array or matrix, A[n:m], write a function to support arbitrary query of the sum of a valid range. A valid 
> range is defined by the left top coordinator and the right bottom coordinator in the matrix. ![matrix](http://i.imgur.com/KPWQzMZ.png)

> The range in the picture is defined as [(2,1), (4,3)]. The sum is the total sum of elements in the red rectangle, which is 8.

### Next discussion to cover
1. Range sum
2. Range sum on 2-dimension array
3. Range minimum
4. Range mode and median

### Links to the solution to these problems
[Range query (data structures)](https://en.wikipedia.org/wiki/Range_query_(data_structures))

[Range minimum query](https://en.wikipedia.org/wiki/Range_minimum_query)

[Range Searching](https://www.cs.ucsb.edu/~suri/cs235/RangeSearching.pdf)

[Fenwick tree or binary indexed tree](https://en.wikipedia.org/wiki/Fenwick_tree)

[Segment tree](https://en.wikipedia.org/wiki/Segment_tree)

[RANGE MODE AND RANGE MEDIAN QUERIES ON LISTS AND TREES](http://cglab.ca/~morin/publications/ds/rmq-njc.pdf)

[B+ tree](https://en.wikipedia.org/wiki/B%2B_tree)




