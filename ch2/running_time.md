# Analyzing the running times

Different programs using different approaches sometimes solve the same problem
with different efficiency.  In our previous examples, we actually implement
different solutions and compare their running times.  If we want to find out
which method is the fastest to solve a particular problem, this may be the best
way.  However, there are cases where we can tell, even before implementing the
solutions, which solution should run faster, when the input is large.  The
ability to analyze (even roughly) the efficiency of the approaches before having
to spend time implementing the solutions is extremely useful.  In this section,
we will discuss the techniques to analyze solutions' running times.

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

## Factors affecting running times

## Running times as functions of the input sizes

As discussed in previous sections (TODO), considering only the algorithm is not
enough to accurately determine the actual running time for the set of inputs
that we are interested in.  However, the information in the algorithm itself is
still extremely useful.  Consider previous examples of product algorithms, that
given two positive integers $$a$$ and $$b$$, calculates the product $$a\cdot
b$$.



## Formal definitions
