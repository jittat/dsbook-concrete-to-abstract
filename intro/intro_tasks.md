## Chapter 1: Programming Tasks

### 1.1 Nearby search {#tasks-intro-nearby-search}

You are given a locations of players, monsters, and the radius. Each player only sees monsters within the specified radius.  Output the total number of monsters seen by any players.

**Input format:**

* First line: $$n$$ $$m$$ $$r$$ (where $$n$$ is the number of players, $$m$$ is the number of monsters, and $$r$$ is a radius)
* The next $$n$$ lines, for each line the co-ordinate for each player: $$x$$ $$y$$
* The next $$m$$ lines, for each line the co-ordinate for each monster: $$x$$ $$y$$

**Test data:** [Download here](http://theory.cpe.ku.ac.th/~jittat/courses/01204212/tasks/nearby/)

**Some answers:**

* `n100.in`: `5`
* `n1000.in`: `311`
* `n5000.in`: `7803`
* `n10000.in`: `31072`

### 1.2 Status comments {#tasks-intro-comments}

You are given an information on how comments are posted. You want to display them in a nicely form.

**Input**

* First line: integer $$n$$ (the number of comments)
* The next $$n$$ lines, each line specify a comment. Line $$i + 1$$ for $$1\leq i\leq n$$ specifies the $$i$$-th comment. In this line, there is an integer $$p$$ ($$p \lt i$$) and a string $$s$$ (consiting of alphabets `a` - `z`, `A` - `Z`, and an underline (`_`) whose length is at most 30). This comment replies to the $$p$$-th comment. If $$p=0$$, this comment replies to the status itself. The string $$s$$ is the comment text.

**Input example**

```
7
0 hello_world
0 this_is_another_comment
1 first_reply
2 good_morning_thailand
1 second_one
1 third_one
5 when_it_is_ok
```

**Output example**

For this input, the expected report would be (see the description of the output at the end):

```
- 1 hello_world
  - 3 first_reply
  - 5 second_one
    - 7 when_it_is_ok
  - 6 third_one
- 2 this_is_another_comment
  - 4 good_morning_thailand
```
