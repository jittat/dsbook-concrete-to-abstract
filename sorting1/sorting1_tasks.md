## Chapter 5: Programming tasks

### 5.1 Integer sorting

You are given $$N$$ integers (whose values are between -1,000,000,000 to 1,000,000,000).  You want to sort them ascendingly.

#### Input

* First line: an integer $$N$$ ($$1\leq N\leq 100,000$$)
* The next $$N$$ lines: each line contains one integer

#### Output

Output $$N$$ lines of $$N$$ integers in sorted order from the smallest to the largest.

#### Example

**Input**

```
5
10
2
300
25
7
```

**Output**

```
2
7
10
25
300
```

#### Test data

Download [them here](http://theory.cpe.ku.ac.th/~jittat/courses/01204212/tasks/sortint/)

Correct answer for `n10.in`.

```
22
172
189
245
266
332
399
597
858
984
```
Correct answer for `n1000.in` (first 10 lines):

```
38
58
213
220
298
320
440
462
526
576
```

Correct answer for `n10000.in` (first 10 lines)  (Notes: if you use recursion,
you might not be able to run on this big input):

```
71
83
131
188
261
265
368
399
464
495
```
