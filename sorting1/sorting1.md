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

## Inserting sort

## Inserting sort: iterative version

## Array implementation

## Selection sort

## Selection sort: iterative version

## Array implementation
