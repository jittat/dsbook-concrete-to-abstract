# Sorting (Part 1)

We look at one of the most fundamental tasks that computers perform really well:
sorting objects.  While the task itself is extremely important, it is also a
very crucial preparation step for efficient searching algorithms which is the
foundation of database systems.

A standard task for sorting is to sort a sequence of integers.  You are given $$N$$ integers.

This chapter focuses on two simple quadratic-time sorting algorithms.  We will
take a short detour before presenting the typical implementation of both
algorithms by revisiting the concept of recursion.

## Recursive thinking

### Computing exponentiation

Let's start with a concrete example.  

> Given a number $$x$$, you want to compute $$x^{20}$$.

If you are good at writing loops, you can write the following solution in
minutes.

```java
  static double toPow20(double x) {
    double s = 1;
    for(int i = 1; i <= 20; i++) {
      s *= x;
    }
    return s;
  }
```

Let's think differently.  Suppose that we cannot use any loop.  How can we
compute $$x^{20}$$?  Let grant us some "magical" help: a method `toPow19` which
given $$x$$, computes $$x^{19}$$.  The previous method `toPow20` can be written
as follows.

```java
  static double toPow20(double x) {
    return x * toPow19(x);
  }
```

Let's try to get things done by implementing `toPow19`.  This looks easy as we
can use the same trick.

```java
  static double toPow19(double x) {
    return x * toPow18(x);
  }
```

How about `toPow18`?  OK, this will do:

```java
  static double toPow18(double x) {
    return x * toPow17(x);
  }
```

Following this trick, we can implement all `toPow?` methods to obtain `toPow20`.
However, we are restricted to computing $$x^{20}$$ (or smaller exponents).  Is
it possible to write a method, using this trick, to find the $$n$$-th power
of $$x$$, where $$n$$ is an input to the method, i.e, to write a method `pow(double x, int n)`.  We are tempting to write it like this:

```java
  static double toPow(double x, int n) {
    return x * toPow_n_minus_one(x);
  }
```

But this is clearly wrong.  From the previous attempt, we see this chain of
method calls:

> `toPow20` $$\leadsto$$  `toPow19` $$\leadsto$$  `toPow18` $$\leadsto$$  `toPow17` $$\leadsto\cdots\leadsto$$  `toPow2` $$\leadsto$$  `toPow1`

This guides us to the following implementation when $$n$$ is not in the method name.

```java
  static double toPow(double x, int n) {
    return x * toPow(x, n - 1);
  }
```

Since method `toPow` calls itself, this method is recursive.   To understand it,
we can first ignore the fact that it is recursive, but try to simulate how it
works.  Let's try following the execution of `toPow(2,3)`.

```
 toPow(2,3) calls toPow(2,2)
   toPow(2,2) calls toPow(2,1)
     toPow(2,1) calls toPow(2,0)
       toPow(2,0) calls toPow(2,-1)
         toPow(2,-1) calls toPow(2,-2)
           ..
             ..
               ..
```

The recursive calls never return (because we do not put any instructions for
returning).  To fix this problem, we need to make the recursive calls stop at
some point.  The obvious one is when `n` equals 0, because we know that the
answer is 1.  The correct implementation is the following.

```java
  static double toPow(double x, int n) {
    if(n == 0) {
      return 1;
    } else {
      return x * toPow(x, n - 1);
    }
  }
```

The case when `n = 1` is usually referred to as the *base case* or *basis*.

### List properties

We shall write methods that work with Java `List` as the next examples for
recursive thinking.  While we have implemented linked list data structures, in
this chapter, we focus on using the Java standard collection library. We shall
work mainly with `LinkedList` (a class that implement `List` interface) and
`ListIterator`.   You can review their interfaces at [this section](../lists/lists.md#list-java-collection).

Let's start with method `listSize` that takes a `ListIterator` and returns the
number of elements of the list after that iterator:

```java
  static int listSize(ListIterator li) {
    // ...
  }
```

We take a different direction this time.  Let's think about the base case first;
this is the case where the method can provide the answer easily.  For
`listSize`, we can see that since when the list is empty, the answer is 0, a
candidate for the base case is when `li.hasNext()` returns `false`.  The method
now deals with this case.

```java
  static int listSize(ListIterator li) {
    if(! li.hasNext()) {
      return 0;
    } else {
      // .. another case
    }
  }
```

What do we know about the other case?  We know that the list continues after the
location pointed to by the iterator `li`.  To solve a problem recursively, we
need to make a jump of faith: if we move the iterator to the next location and
find the size of the list after that, to obtain the size of the list after the
current location we can just add one to the result.  We therefore get this
method.

```java
  static int listSize(ListIterator li) {
    if(! li.hasNext()) {
      return 0;
    } else {
      li.next();
      return 1 + listSize(li);
    }
  }
```

Note that while we make the seemingly the same call to `listSize(li)`, the
iterator itself has moved to the next location.

We consider another related example. Suppose that we are given a list of
`Integer` and would like to find the sum of its elements.  We write the
following method:

```java
  static int listSum(List<Integer> list) {
    // ...
  }
```

In this form, the method argument does not allow us to make a recursive call
(unless we make some change to the list).  Let's create a helper function to
perform the task:

```java
  static int listSum(List<Integer> list) {
    return listSum(list, list.iterator());
  }

  static int listSum(List<Integer> list, Iterator<Integer> li) {
    // ...
  }
```

Let's follow the previous approach by considering the easiest case for the
method: when the iterator `li` points to the end of the list.  In that case,
`listSum` return `0`.

```java
  static int listSum(List<Integer> list, Iterator<Integer> li) {
    if(! li.hasNext()) {
      return 0;
    } else {
      // ...
    }
  }
```

For the other case, we note that we note that the summation of the list elements
after the current location equals the summation of the elements after the *next*
location plus the element at this location.  Thus, we have the following method.

```java
  static int listSum(List<Integer> list, Iterator<Integer> li) {
    if(! li.hasNext()) {
      return 0;
    } else {
      int val = li.next();
      return val + listSum(list, li);
    }
  }
```

Again, note that in the recursive call to `listSum(list, li)`, the iterator `li`
points to the next location.

In both examples, we first consider the base case, then look at how to deal with
the other case.  The case where we make recursive calls is usually referred to
as *the inductive case*.

### Two aspects of recursion

When we look at recursive programs, we see a program that calls itself. However,
if we ignore that fact about the caller and the callee names, the call usually
is a logical step to do.  If we look deeper inside how the results are computed,
we may need to see how all recursive calls are made.  We can see that there are
many levels of method calls before we reach the base case; and from that point
the final answer is reconstructed, bits by bits, from the calls.

These describe two aspects of recursive program:

* The high-level view that considers only the top-most level recursive call.
* The mechanical steps involving how recursive calls are made at many levels.

When designing a recursive method, we usually focus mostly on the high-level
vision that gives us the direction to go.  The top-most level call describes how
we obtain the desired solution from the solution of the other problem (usually a
smaller one).  Later, we can focus on the second step, which crucial when we
want to analyze the running time.  Paying too much attention to the mechanics of
recursion (the 2nd aspect) often make understanding recursive program very hard.

## Two different approaches for sorting

We are given a linked list of $$n$$ integers.  We want to sort them.   We will
derive two sorting algorithms by thinking recursively.  

We start by identifying the base case, i.e., the case where sorting is extremely
easy.  This is when the list is empty: if you are given an empty list, the
sorted result is also an empty list.  (You can also take the case when the list
has only one element, but if we want an algorithm that works for any input, we
have to deal with an empty list anyway.)

To start thinking recursively, we assume that we can solve the same problem with
smaller inputs and try to see if we can use that fact to solve the original
problem.

Thus, we ask the following question:

> Can we sort $$n$$ integers, if we know how to sort $$n-1$$ integers?

While there are many ways to do that, we consider only two natural ideas.

First, we can take any element $$x$$ out of the list, sort the remaining $$n-1$$
elements in the list, and try to put $$x$$ in the right location in the sorted
list.  See an illustrating example below.

```
Input: 10, 23, 14, 27, 7, 19, 3, 1, 15, 2
  1) Take 10 out.
     Remaining list: 23, 14, 27, 7, 19, 3, 1, 15, 2
  2) Sort the remaining list:
     Result: 1, 2, 3, 7, 14, 15, 19, 23, 27
  3) Put 10 in the right place.
     Result: 1, 2, 3, 7, [10], 14, 15, 19, 23, 27       
```

The previous approach focuses on working *after* the remaining list is sorted.
If we work harder to select an integer $$x$$ to be taken out, we can save a lot
of work to be done afterwards.  If we want put back $$x$$ easily, we can either
select the smallest or the largest integer.  The following illustrates the
point.

```
Input: 10, 23, 14, 27, 7, 19, 3, 1, 15, 2
  1) Take 1, the minimum integer out.
     Remaining list: 10, 23, 14, 27, 7, 19, 3, 15, 2
  2) Sort the remaining list:
     Result: 2, 3, 7, 10, 14, 15, 19, 23, 27
  3) Put 1 in the right place (the beginning of the list).
     Result: [1], 2, 3, 7, 10, 14, 15, 19, 23, 27       
```

The first approach leads to an insertion sort and the second approach gives a
selection sort.

Let's write the skeleton of both methods.

```java
public static LinkedList<Integer> sort(LinkedList<Integer> inList) {
  if(inList.size() == 0) {
    return new LinkedList<Integer>();
  }

  // find x to remove
  int x = ...

  LinkedList<Integer> outList = sort(inList);

  // put x into outList
  // ...

  return outList;
}
```

## Inserting sort

We will focus on two major steps left out from the skeleton.  It is easy to take
$$x$$ out of the list.  We simply take the first element out.

```java
  int x = inList.removeFirst();
```

To place $$x$$ to the right location requires some work.  Also, the list
iterator in Java is fairly limited, as you cannot get an element without having
to move.  In the code we have to move `next` and `previous` to return to the
right location.

```java
  if(outList.size() == 0) {
    outList.add(x);
    return outList;
  }

  ListIterator<Integer> i = outList.listIterator();
  while(i.hasNext()) {
    if(i.next() >= x) {
      i.previous();
      break;
    }
  }
  i.add(x);
  return outList;
```

Note that at the top-most level of the recursion, we only work with one element.
The rest are handled at the deeper levels of the recursion.

## Selection sort

We turn our focus to the selection sort.  Let's find the smallest
element $$x$$ and remove it.

```java
  int x = inList.getFirst();
  int minIndex = 0;

  ListIterator<Integer> i = inList.listIterator();
  while(i.hasNext()) {
    int y = i.next();
    if(y < x) {
      x = y;
      minIndex = i.previousIndex();
    }
  }
  inList.remove(minIndex);
```

Again note that because of the way Java iterator works, we have to use
`previousIndex` to get an index of the element $$y$$ recently obtained by
calling `next`.

After the recursive call, putting $$x$$ back to the list is easy.

```java
  outList.addFirst(x);
```

## Running times

### Insertion sort

To formally analyze the running time, we need to use a running time recurrence,
the topic that we shall cover in the next chapter.  For now, let's make an
informal argument.  We consider the worst-case running time.  From the code, to
sort $$n$$ integers, we have to

* take the first element $$x$$ out of the list,
* sort $$n-1$$ integers, and
* insert $$x$$ back into the sorted list of $$n-1$$ elements.

The first step clearly takes $$O(1)$$ time.  The last step iterates through a
list of $$n-1$$ elements.  In the worst-case, we have to look through the list
and that takes $$O(n)$$ time.

The conceptually puzzling step is the second one.  We will try to argue about it
by unrolling the recursive calls.  Let's unroll them for a few steps:

```
 sort(list with n elements):
    take the first element x1 out of the list of n elements
    [1st-level] recursive call to sort(list with n-1 elements):
       take the first element x2 out of the list of n-1 elements
       [2nd-level] recursive call to sort(list with n-2 elements):
          take the first element x3 out of the list of n-2 elements
          [3rd-level] recursive call to sort(list with n-3 elements):
             take the first element x4 out of the list of n-3 elements
             [4th-level] recursive call to sort(list with n-4 elements)
                ...
             insert x4 into the sorted list of x-4 elements
          insert x3 into the sorted list of x-3 elements
       insert x2 into the sorted list of x-2 elements
    insert x1 into the sorted list of x-1 elements
```

Using the $$O$$-notations in this case can easily cause an error, so let's work
without the $$O$$-notation.  Let's assume that the worst-case running time of
the first and the second steps is at most $$cn$$ units of time, for some
constant $$c$$, for an input list with $$n$$ elements.

In the first level of the recursive call, the input list has $$n-1$$ elements.
Thus the first and the third steps in that level run for at
most $$c(n-1)$$ time.  For the second level of the call,
the input list has $$n-2$$ elements;
thus the running time for the first and the third steps is at most $$c(n-2)$$.
This goes on for $$n-1$$ levels when the list eventually gets emptied.

If we sum up the running time across all levels we have that the algorithm runs
in time at most

$$cn + c(n-1) + c(n-2) + c(n-3) + \cdots + c(2) + c(1) = c\frac{n(n-1)}{2} = O(n^2)$$

### Selection sort

The recursive calls of the selection sort follows the same structure.  These are
3 main steps of the algorithm.

* take the minimum element $$x$$ out of the list,
* sort $$n-1$$ integers, and
* insert $$x$$ back to the beginning of the list.

The first step iterates through all the elements in the list; thus the running
time is $$O(n)$$ for an $$n$$-element list.  The third step takes $$O(1)$$ time
because we use a linked list.  Thus, the time outside the recursive calls when
the input list has $$n$$ elements is at most $$cn$$ for some constant $$c$$.

The same argument for the insertion sort works here as well.  Thus, the
selection sort runs in time $$O(n^2)$$.

## Iterative versions

The recursive versions of insertion sort and selection sort presented in the
previous sections are not standard ways to implement both algorithms

## Array implementations
