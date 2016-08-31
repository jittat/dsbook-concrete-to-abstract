## Chapter 3: Programming Tasks

### 3.1 Zooma 1 {#tasks-ch3-zooma-1}

This task is motivated by [Zuma](https://en.wikipedia.org/wiki/Zuma_%28video_game%29), a video game by PopCap Games.

In this version of the game, there is a sequence of $$n$$ colored balls that
moves toward an exit.   You can shoot another $$m$$ colored balls into the
sequence.  Balls do not disappear in this version.

Find out the final sequence of the balls.

The balls in the original sequence are numbered from $$1$$ to $$n$$.  
The balls that you shoot are numbered from $$n+1$$ to $$n+m$$.

#### Game play example

Consider the case where $$n=5$$ and $$m=4$$.  The original sequence has balls with these colors:

         1     2     3     4     5
         G     B     G     Y     Y

You have 4 balls numbered as this:

         6     7     8     9
         R     G     B     G

If you shoot your first ball to the location after ball 3, the sequence becomes

         1     2     3     *6*     4     5
         G     B     G     *R*     Y     Y

If you shoot your second ball to the location after ball 1, the sequence becomes

         1     *7*     2     3     6     4     5
         G     *G*     B     G     R     Y     Y

If you shoot your third ball to the location after ball 6, the sequence becomes

         1     7     2     3     6     *8*     4     5
         G     G     B     G     R     *B*     Y     Y

If you shoot your forth ball to the location after ball 5, the sequence becomes

         1     7     2     3     6     8     4     5     *9*
         G     G     B     G     R     B     Y     Y     *G*

and this is the final sequence.

**Note:** In this version, the colors of the balls are not relevant because
balls do not disappear.  In next versions of the task (see [Zooma 2](#tasks-ch3-zooma-2)), there are rules about the disappearance of
consecutive balls with the same color.

### Input

* First line: $$n$$ and $$m$$
* Next $$n$$ lines: for $$1 \leq i\leq n$$, line $$1 + i$$ specifies one integer `c[i]` the color of ball $$i$$.
* Next $$m$$ lines: for $$1 \leq j \leq m$$, line $$1 + n + j$$ specifies two integers `d[j]` and `p[j]`.  `d[j]` is the color of ball $$n+j$$ (this is your $$j$$-th ball), and `p[j]` is the number of the ball right after which you shoot this ball into the sequence.  Note that `p[j]` $$<$$ $$n + j$$.

### Output

Your program should print $$n+m$$ integers which are the ball numbers in the final sequence.

### Example

This is the same example with colors: G = 1, B = 2, Y = 3, R = 4.

#### Input

```
5 4
1
2
1
3
3
4 3
1 1
2 6
1 5
```

#### Output

```
1
7
2
3
6
8
4
5
9
```

### Test data

Download at: [http://theory.cpe.ku.ac.th/~jittat/courses/01204212/tasks/zooma1/](http://theory.cpe.ku.ac.th/~jittat/courses/01204212/tasks/zooma1/)

Expected output for n10-3.in:

```
1
2
14
18
20
3
11
13
4
5
17
6
7
8
15
12
19
9
10
16
```
Expected output for n50-3.in (first 10 lines):

```
1
61
2
97
52
3
81
4
71
67
5
```


### 3.2 Zooma 2 {#tasks-ch3-zooma-2}

### 3.3 Zooma 3 {#tasks-ch3-zooma-3}
