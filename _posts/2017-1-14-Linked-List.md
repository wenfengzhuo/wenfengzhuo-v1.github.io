---
layout: post
comments: true
categories: algorithm
title: Depth of Linked List
---
   
<h2><center>Linked List vs Array List</center></h2>

### Implementation

Both of Linked List and Array List serve as collections for storing a sequence of elements. The difference between these two lists is their internal implementation of the ways of storing objects.
For linked list, every objects stored in it are connected or linked together by maintaining pointers that point its predecessor and successor, so the objects will be represented as a node internally, which holds the information of the object itself, the predecessor pointer and the successor pointer. Adding a new element is as simple as adding a node at the tail of the list.
For array list, all objects will be stored in a pre-allocated array. The object will be directly added in the array without wrapping it as a node. The internal array could grow as more objects are added.

### Performance

With knowing their implementation, we could derive their performance clearly from operation perspective.

**Add**

For adding new object at the tail of the list, both of linked list and array list will have O(1) time for this operation. A difference is that if all slots in array list are exhausted, the list will grow to hold more objects, then a extra copy from the old array to the new array will be executed.  This will cost extra time compared with linked list, although this cost will be amortized to O(1).
For adding  new object in a position other than the tail of the list, the linked list will cost O(n/2) in worst case because it will first iterate the list to reach the node in the target position and then insert the added object by linking it. For array list, finding the position to insert the added object cost O(1) time, but there is an extra O(n) time in worst case to shift all object (to the right of the target position) to the right by one position.

**Deletion**

Linked list will cost O(n/2) time in worst case for delete a object if the target is in the middle of the list. In linked list, the deletion could be achieved by first finding that target node and then unlinking it from its predecessor and successor. Finding the object will cost O(n/2) time, and the unlinking will cost O(1) time. 
Array list will only cost O(1) time to delete a target object, but it still requires extra time to shift all objects (to the right of the deleted object) to the left. If we delete the first object of the list in array list, then a copy of the rest of objects will be performed and this will nearly cost O(n) time (n is the length of the array). In total, the deletion will cost O(n) time for array list in worst case.

**Retrieve by index**

For linked list, retrieving an object by its index in the list will cost O(n/2) time as we need to iterate as most as half of the list to find the object if it resides in the middle of the list.
For array list, retrieving an object is as simple as retrieving the object in its internal array, so this will guarantee that it only costs O(1) time.

**Space**

### Application

With their differences in implementation and performance, linked list and array list will be used in different scenarios. 

**Random Access**

If we want to random access to the objects in the list, array list will be a better choice because each retrieval operation will only cost O(1) time. A typical example is that when we want to use quick-sort to sort the objects in the memory, storing those objects in an array list will achieve high performance.
Even for sequential access, the array list will generally perform better than linked list thanks to the good locality of reference. A simple explanation of locality of reference is that the memory near the already accessed memory is tended to be cached so retrieving it will be faster than retrieving those in RAM.

**Density of Data**

If the data that is stored in a grid-way, or there are multiple dimensions to access the data, and the data is sparse distributed, then it is better to use linked list. A typical example is sparse matrix. Using array list would consume too much unnecessary memory in this case. Another example I could think up is the representation of a polynomial equation.

**Manipulating data**

If we want to insert or delete data during the iterator of the list, linked list will outperform than array list. This is because array list will cost extra time to move data when adding or deleting an element in a specific. 

**access order**

A common requirement in practice is that we might need to maintain the access order of the objects, for example a LRU cache. Using linked list to implement this cache is an easy task. The recently accessed object could be put in the tail of the list, and the front object of the list will be the one which is least recently used, and if the cache reaches its capacity, we could evict the front objects. Both of those operations will cost only O(1) time. In Java, it is very quick to implement a LRU cache by inheriting LinkedHashMap and overriding its removeEldestEntry method.

**memory**

As the overhead of memory to store objects in linked list, it is always better to choose array list to store large amount of data in memory. 

**Basic data structure**

Linked list usually is the choice to be used as the underlying data structures of queues and stacks thanks to its O(1) operations of adding and removing object at the head or tail of the list.
 
 
<h2><center>Union Iterators</center></h2>

> **Input**:
>	    M iterators
>	    N tuples in each iterator
>	    K keys in each tuple
>
> **Output**:
>      The ordered union of all tuples from each iterators.
>
> **Assumption**:
> 1. Tuples are different to each other in the same iterator
> 2. Each iterator has exact the same number of tuples

The question above could be simplified as: *merge M iterators into a single stream and keep those tuples sorted in the final stream*. As tuples in a single iterator are already in order, what we need to take care is to maintain the relative order among the different iterators during the merging process. With this objective, I have two solutions in mind.

### Priority Queue

**Algorithm**

Why does a priority queue help? The thinking process is pretty straightforward. As we want to make the final single stream as sorted, during the merging process if we always pick the smallest elements from those M iterators and put it to our final stream, then we will finally get a sorted single stream. To pick the smallest elements efficiently, priority queue (or heap) might be the most appropriate choice. We could maintain a min-heap, which has the smallest element in the root. The retrieval of smallest element and the addition of a element to the queue will both cost O(log(n)) time, n is the number of elements in the queue. 

**Pseudo Code**

The following pseudo code elaborates the algorithm above. To make it simple, the code assumes that the final stream r (a list) will have a *add()* function, the function will append an element in this tail and also it will ignore those elements who are the same with the tail so that the final stream does not contain duplicates. For the priority queue, we will assume that it could compare the first element of a iterator.

```
union(Iterator[] A):
  r = []
  q = priority queue
  for i in A
    if i has next
      q.offer(i)  ====> (1) O(Klog(M)) * M
  while q has next
    i = q.poll()
    r.add(i.next())
    if i has next
      q.offer(i)  ====> (2) O(Klog(M)) * (N*M - M)
  return r
```

**Complexity**

In the code above, we could see that we first add those iterators who have elements into the priority queue. Then we will retrieve the smallest element every time we poll the root from the priority queue. After we add the smallest element in our final stream, we also need to make sure that we will add the iterator back if it still has elements in it.
In line (1), we can see the program will execute M times if we have M iterators, for each addition into the queue, it will be O(log(M)) comparisons as of the property of priority queue. We have to notice that, for each comparison, it will cost O(K) time to compare a tuple since there are K keys in each tuple. So in total, the line (1) will contribute O(Klog(M)) \* M to the time complexity. In line (2), we know that there are still (N\*M - M) elements that are needed to be offered in the queue, so in total, it will be O(Klog(M))\*(N\*M - M). All in all, the total time complexity of this algorithm will be O(N * K * M * log(M)) in worst case. 
As for the space complexity, we know that during the merging process, there will always be M elements if all iterators still have remaining elements. So in worst case, it will be O(M) elements in queue, and each elements has O(K) keys, so in total, the space complexity will be O(M * K).
In conclusion:

====> Time Complexity: O(N * K * M * log(M))

====> Space Complexity: O(M * K)

### Divide-and-Conquer

**Algorithm**

Another way to think this problem is to find whether there is an approach to simplify the problem or convert the problem to an existing solved problem. We could start with a very simple case: if there are only two iterators to be merged. In this case, the situation is just like a the merging phase in merge-sort, so we could use the same logic to solve the problem in this case. If there are more iterators, we could always divide those iterators into halves, and solve it recursively until there are only two iterators. This logic is pretty much the same with merge-sort.

**Pseudo Code**

*mergeIterator* is a function that merges two sorted iterators into a single iterable object. The function assumes that the iterator has a *peek* function that returns the first elements in the iterator. 

```
mergeIterator(Iterator l, Iterator r)
  r = []
  while l has next && r has next
    kl = l.peek()
    kr = r.peek()
    if kl <= kr ====> O(K)
      r.add(l.next())
    else
      r.add(r.next())
  while l has next
    r.add(l.next())
  while r has next
    r.add(r.next())
  return r
```
*union* is the major function in this algorithm. It adopts the same idea with merge-sort. It divides the iterator array into halves recursively, and then use *mergeIterator* function to merge the iterators that are returned by the two recursive call.

```
union(Iterator[] A)
  len = len of A
  if len == 0
    return []
  if len == 1
    return [](A[0])
  mid = len / 2
  l = union(A[:mid]) ====> (1) f(M/2)
  r = union(A[mid+1:]) ====> (2) f(M/2)
  return mergeIterator(l, r) ====> (3) O(N * M * K) => O(N * k * M)
```
**Complexity**

As we can see in the code, the time complexity follows the formula of a typical divide-and-conquer solution:
> T(n) = 2*T(n/2) + f(n)

In the line (3), we can see it calls *mergeIterator* function. The *mergeIterator* function has time complexity proportional to the size of the two iterators, because there will be the same number of comparisons with the number of elements in these two iterators. As a result, line (3) indicates that f(n) = O(N*M\*K)=O(NK * M). According to the master theorem, the final time complexity of this algorithm will be O(NKM\*log(M)).
For space complexity, as you can see from the code, there will be no extra space needed if we do not count the stack space for recursive call. With the space for recursive call considered, the space complexity will be O(log(M)).

====> Time Complexity: O(N * K / 2 * M * log (M))

====> Space Complexity: O(log(M))

### Comparison between queue and divide-and-conquer

**Time Complexity**

From the time complexity perspective, both of the two functions will have the same time complexity, which means that theoretically, both of the two algorithms will have same running time for the same input. 

**Space Complexity**

As we could tell clear from the descriptions above, the divide-and-conquer solution will requires much less space than the priority queue solution. In practice, if we adopt divide-and-conquer solution, it means we only need few extra memory to achieve the same time complexity with the priority queue solution. In fact, in practice more extra memory might have impact on the total running time. When request more memory, new memory will be allocated, and this process will cost some time. What is more, if the data is too large to fit in memory, then virtual memory will be involved, which has significantly lower performance than the RAM memory. In this perspective, I will prefer divide-and-conquer solution.

**Parallel capability**

By using divide-and-conquer method, we know that the second approach could parallelize very well. To simply put, we could run the two halves of the iterator arrays at the same time, and this applies to every recursive call. By using parallelism, in assumption that we have enough processors, the time complexity will be reduced to O(N*M\*K). Further discussion will be covered in next section.
For priority queue solution, since we need to get the smallest tuples every time we poll from the queue, it is hard for us to parallelize the algorithm.

### Further Optimization

**Parallelism**

As we already mentioned in previous section, we could parallelize divide-and-conquer solution in a very natural way. The following pseudo code explains how could achieve parallelism:

```
union(Iterator[] A)
  len = len of A
  if len == 0
    return []
  if len == 1
    return [](A[0])
  mid = len / 2
  fork l = union(A[:mid]) ====> (1)
  r = union(A[mid+1:])
  join                    ====> (2)
  return mergeIterator(l, r)
```

In line (1), we create a new thread (or process) to run the recursive call for the left half of the array in parallel. In line(2), we will wait the completion of the two processes. So the time complexity could be calculated with the formula:
> T(M) = T(M/2) + O(M * N * K)

As a result, the final time complexity will be O(N * K * M).

**Tuple Comparison**

For a tuple, assign a hash value for it. The hash value is determined by the keys in each tuple. The large tuple will have large hash value. Then for most of keys, just compare their hash value, we will know their order. For the tuples who have the same hash value, we compare them by comparing the keys in each tuple. The following picture shows a way of hashing a tuple.

![hash](http://i.imgur.com/LJs9a5U.png)

Let's assume the range of keys in those tuples is 0~1023, and there are 3 keys in each tuples. We divide the whole range with 2^3 = 8 parts, each part will be labeled as 0, 1, 2, ..., 7. The following code explains how we compute the hash for a tuple: t (k1, k2, k3).

```
hash(tuple)
	h = 0
    i = 0
    while i < 3
	    if tuple[i] > 512
		    h |= 1 << (2-i)
		i ++
	return h
```

Let's assume each tuple now has a function *hashCode* which returns a pre-computed hash. If luckily, all the tuples in those iterators have different hash value, then we could directly reduce the time complexity of our algorithm to O(N*M\*log(M)). In reality, some tuples might have the same hash value. So we need to do a amortized analysis of the algorithm. If all the tuples distributed normally in the picture above, then we could calculate the how many tuples might end up with having the same hash values. For two tuples, the possibility to have the same hash value will be 1 / (2 ^ k). We know that there will N\*M comparisons in total, so for those comparisons which need full comparisons of each keys, the total number will be (1/(2^k)) * (N\*M). Let's assume there is **c** percentage of comparison which could directly use hash values. So the average time for each comparison will be
> ((1/(2^k)*(N\*M)) * K + cN\*M)/(M\*N) = K/(2^K) + c

As c is lower than 1 (because not all comparisons could directly use hash values), so the average time for each comparison will less than 2. The time complexity now will be from O(N*K\*M\*log(M)) to O(N\*M\*log(M) because the average time for comparison becomes constant time.
