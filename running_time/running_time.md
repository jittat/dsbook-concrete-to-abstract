# Analyzing the running times

Different programs using different approaches sometimes solve the same problem
with different efficiency.  In our previous examples, we actually implement
different solutions and compare their running times.  If we want to find out
which method is the fastest to solve a particular problem, this may be the best
way.  However, there are cases where we can tell, even before implementing the
solutions, which solution should run faster, when the input is large.  The
ability to analyze (even roughly) the efficiency of the approaches before having
to spend time implementing the solutions is extremely useful.  In this chapter,
we will discuss the basic techniques to analyze solutions' running times.  In
later chapters, more techniques and examples will be also introduced to deal
with particular data structures and algorithms.

## Algorithms and their implementations

Let's start with easy examples.  We want to compute a product of two positive
integers $$a$$ and $$b$$.  We can write a loop that keeps adding $$a$$ for $$b$$
iterations, as described in `product1` below.

```
ALGORITHM product1(int a, int b)
  LET s = 0
  FOR i = 1,2,...,b
    LET s = s + a
  ENDFOR
  RETURN s
```

Or, we can just use multiplication operator `*` to simply get the answer.

```
ALGORITHM product2(int a, int b)
  RETURN a * b
```

Note that while both two solutions are written in a way that resemble computer
codes, they are not written in any particular programming languages.  However,
they provide enough information for anyone to translate them into actual
programs.  These clearly defined steps for solving tasks are usually referred to
as *algorithms*.  (Note that it is possible to express algorithms with real
codes, and it is common to express algorithms in codes in some programming
language but they get implemented in other programming languages.)

We can write both algorithms in Java codes as follow.

```java
static int product1(int a, int b) {
  int s = 0;
  for(int i = 0; i < b; i++) {
    s += a;
  }
  return s;
}

static int product2(int a, int b) {
  return a * b;
}
```

These actual codes are usually referred to as *implementations* of the
algorithms above.  Given the implementations in real programming languages, we
can conduct performance testings to measure their actual running times.

TODO: Do we have to write down algorithms before writing the implementations

## Best cases, worst cases, average cases

If the algorithms take input parameters, very often their running times depend
on these input parameters.  

As an example, both algorithms `product1` and `product2` runs at roughly the
same speed when $$b=1$$, because the for loop is executed only once.  This is
when `product1` runs the fastest.  If we want to analyze the running time but
only consider this easy case, the result seems not very useful and even
misleading.

Since the running time can vary a lot based on the input, we have to choose the
class of inputs to analyze carefully.  Choosing cases when the algorithm perform
fastest is referred to as the *best case analysis*.  On the other hand, if we
perform the analysis assuming the worst possible scenarios, this analysis is
referred to as the *worst case analysis*.  We want to be on the safe side when
applying the analysis result, so we usually prefer worst case analysis of
algorithm behaviors.

It is also possible to consider typical cases.  These type of analysis is called
*average case analysis*.  To get a precise result, we have to start with the
definition of typical inputs of the algorithm, usually modeled with probability
theory.  In this course, we will not perform average cases analysis that much.

## Factors affecting running times

TODO: write this section.

Examples of factors are:

* Factors that are unknown before implementing the algorithm
  * Programming languages
  * Programming style
* Factors that are unknown even when having the code that implements the algorithm
  * Computer hardwares
  * Other running processes in the computers

Therefore, if we want to analyze the algorithm as a mathematical object, we have
to accept that we cannot get accurate answers, but only partial answers.

## Running times as functions of the input sizes

As discussed in previous sections (TODO), considering only the algorithm is not
enough to accurately determine the actual running time for the set of inputs
that we are interested in.  However, the information in the algorithm itself is
still extremely useful.  Consider previous examples of product algorithms, that
given two positive integers $$a$$ and $$b$$, calculates the product $$a\cdot
b$$.

## Examples

### Multiplication algorithms

### Testing if an array is sorted

### `InsertableArray` operations

## Formal definitions

This section provides formal definitions for the $$O$$-notations.  Given
functions $$f(n)$$ and $$g(n)$$ over natural numbers, we say that $$f(n)$$
is $$O(g(n))$$ if there exists constants $$c$$ and $$n_0$$ such that

$$f(n) \leq c\cdot g(n),$$

when $$n \geq n_0$$.  Informally, this means that $$f$$ does not grow faster
than $$g$$ (or, $$f$$ can be upper bounded by $$g$$).

### Examples
