# Motivating examples

This chapter presents two familiar examples.  Note that each example
is accompanied with a programming task.

## Nearby items

Let's start by thinking about the PokemonGo.  You walk around and some
monster near you shows up so that you can try to capture it.  In an
actual app, the app on your phone should receive a set of monsters
close to your location and decides (locally) which monsters to show to
you.  Since every player shares the same set of appearing monsters,
the server has to figure out, for each player, this set of nearby
monsters.

Finding nearby monsters for every user is a classic example of a
nearest neighbor search problem.  In this problem you are given two
types of objects, monsters and players, and a radius $$R$$ and you
want to find, for a given player, all monsters which are within
distance $$R$$ from this player.

**Related task:** [Nearby Search](ch1_tasks.md#tasks-ch1-nearby-search)

To encourage the reader to think about the problems, try out various
solutions, and experiment with the running times, we usually provide a
programming task formulated from a problem like this one.  Head to the
task [Nearby Search](ch1_tasks.md#tasks-ch1-nearby-search) to read the
task statement and try to implement a solution before you read
further.

### First attempt

A simple solution for this problem is the following.

> For a given player $$p$$, you can iterate through all active
  monsters, compute the distances, and report all monsters within the
  good radius.

This looks fairly simple.  But if you have a lot of active users and a
lot of monsters, it may require a lot of work.  Let's write a simple
program to test the limits of this straight-forward idea.

We shall assume that we only know how to use arrays.  Therefore, in
this example to simplify the code, we will only count the number of
nearby monsters for a player $$p$$.

```java
	private static int countNearbyMonsters(Player p, Monster [] monsters, double radius) {
		int count = 0;
		for(Monster m : monsters) {
			if(p.distanceTo(m) < radius) {
				count++;
			}
		}
		return count;
	}
```

### How to test the performance

We have a simple method implementing our first idea for the nearby
monster search algorithm.  The next step is to test its performance.
We will do that by running the algorithm against a set of test data
and measure the running times.

We still have to think about the kind of test data we want to use and
how to obtain them.  Since we want test data to represent real-world
usage, if we can collect them in actual usages of the algorithm that
would be best.  Since this is not possible, we have to settle with
synthetically generated data.  Here, we will generate monsters and
players randomly, assuming that they are likely to appear anywhere on
the map.

Also, if we look at the method, there is another parameter we have to
think about: the radius $$r$$.  Since the running time of this
algorithm does not depend on $$r$$, we can use any number.

Let's try to put everything into places.  (Note that our main program
reads the number of players and monsters from the command-line
arguments.)

```java
	private static Monster[] randomMonster(int m) {
		Monster [] monsters = new Monster[m];
		for(int i=0; i<m; i++) {
			monsters[i] = new Monster(Math.random() * GAME_WIDTH,
			                          Math.random() * GAME_WIDTH);
		}
		return monsters;
	}

	public static void main(String [] args) {
		int m,n;
		
		n = Integer.parseInt(args[0]);
		m = Integer.parseInt(args[1]);
		
		Monster [] monsters = randomMonster(m);
		double r = GAME_WIDTH * 0.01;
		
		int totalCount = 0;
		
		for(int i=0; i<n; i++) {
			Player p = new Player(Math.random() * GAME_WIDTH, 
			                      Math.random() * GAME_WIDTH);
			totalCount += countNearbyMonsters(p, monsters, r);
		}
		System.out.println(totalCount);		
	}

```

The following table summarizes the running time when for various
values of $$n$$ (assuming that $$m=n$$).

| $$n$$        | Running time |
| ------------ | ------------ |
| 10           |              |
| 100          |              |
| 1000         |              |
| 10000        |              |
| 20000        |              |
| 30000        |              |

We can plot the running times agaist $n$, which is essentially the
size of the problem.

TODO: graph + discussion on the quadratic running time

### The limits

### Can we do better?

Data partitioning is the answer.

## Storing comments

The second problem we shall consider is about how to display comments
in an on-line forum (or a Facebook status).  Modern forum software
allows users to post comments to comments.  While many systems, like
Facebook, do not allow you to post comments to comments of a comments
(i.e., the level of comments is only two), we consider a more general
commenting structure, where any comments can have comments.  In this
case, you end up with a situation like forums like Reddit or Hacker
News, where users keep posting comments and you have to display these
comments nicely, showing clearly the "nested" structure of the
comments.

Let's start with an example.  Suppose we have 7 comments, each comment
has a unique ID.

| ID     | Reply to ID  | Messages   |
|--------|--------------|--------------------|
| 1      | status | hello world |
| 2 | status | this is another comment |
| 3 | 1 | first reply |
| 4 | 2 | good morning thailand |
| 5 | 1 | second one |
| 6 | 1 | third one |
| 7 | 5 | when it is ok |

When two comments reply to the same comment, you want to show the
later one (with higher ID) later.  The following is a possible way to
display the comments (showing the nested structure).

```
- 1 hello world
  - 3 first reply
  - 5 second one
    - 7 when it is ok
  - 6 third one
- 2 this is another comment
  - 4 good morning thailand
```

This problem looks easy at first, but it take a lot of thinking to get
everything right.  We will try to tackle it using many approaches.
But before that, it will be very useful if the reader would stop for a
few minutes to think about the problem.  Also, it would be great to
try to solve it before moving on to see the solutions.  Check out the
task: [Status comments](ch1_tasks.md#tasks-ch1-comments).

**Related task:** [Status comments](ch1_tasks.md#tasks-ch1-comments)

In what follows, we shall discuss three approaches for solving this
task.  Your answer may fall into one of these approaches. The first
two use only basic iterative techniques.

### First solution: direct printing (very complicated)
#### Nested comments: diving in

The first solution uses a direct approach.  Let's begin by looking at
the example.  Note that when you want to print comment 1, you need to
pull every comment that replies to comment 1; in this case comments
3,5, and 6.  Furthermore, when you want to print comment 5, you need
to pull comment 7 that replies to it.  (This is true for comment 3,
but it has no replies.)

We shall start by writing a method for looking up the $$j$$-th comment
that replies to the $$i$$-th comment, or return `null` if it does not
exist.  For example the 1-st comment that replies to comment 5, is
comment 7.  

```java
	private static Comment findReplyingComment(int i, int j, int n, Comment[] comments) {
		int c = 0;
		for(int k=0; k<n; k++) {
			if(comments[k].getParentId() == i) {
				c++;
				if(c==j) {
					return comments[k];
				}
			}
		}
		return null;
	}
```

Note that this is enough to get us started.  I.e., if we start
by following the chain of replying comments, we can start the comment
report, as shown in the following code.

```java
	Comment currentComment = null;
	currentComment = findReplyingComment(0, 1, n, comments);
	while(currentComment != null) {
		printComment(currentComment);
		Comment replyingComment = findReplyingComment(currentComment.getId(), 1,
		                                              n, comments);
		currentComment = replyingComment;
	}
```

However, when we cannot find deeper comments, we need to be able to
"back off".  For example, we may start by printing comment 1, then
follow its replies to get comment 3.  But for the next step, when
`findReplyingComment(3,1,...)` returns `null`, you have to back off,
and return to finding the 2nd replying comment to comment 1.

#### Going back

To be able to do so, we need a lot of book keepings.  Let's think for
a moment.  When we finish calling

```java
    findReplyingComment(3,1,...)
```

the next call would be

```java
    findReplyingComment(1,2,...)
```

I.e., we need to know the comment $$p$$, later referred to as a parent
comment, on the upper level (to get back to) and $$p$$'s next reply
number.  The quick and dirty solution would be as follows.

```java
		currentComment = findReplyingComment(0, 1, n, comments);
		while(currentComment != null) {
			printComment(currentComment);
			Comment replyingComment = findReplyingComment(currentComment.getId(), 1,
			                                              n, comments);
			
			if(replyingComment != null) {
				parentComment = currentComment;
				parentReplyNumber = 1;
				
				currentComment = replyingComment;
			} else {
				currentComment = findReplyingComment(parentComment.getId(),
				                                     parentReplyNumber,
				                                     n, comments);
				parentReplyNumber += 1;
			}
		}

```

But this does not work correctly when you have to back off for many
levels.  Think of the case when we finish printing comment 7.  Knowing
that keeping only one level of parent comments is not enough, we have
to maintain parents for all levels.  This is clearly something that we
should be happy to do because we will know, as a by product, the
indent level of each comment as well.  Thus, we keep

```java
		Comment [] parentComments = new Comment[n];
		int parentReplyNumbers = new int[n];
		int parentCount = 0;
```

and every time, we descend to print any replies of comment $$i$$, we
save comment $$i$$ to `parentComments`, the reply number to
`parentReplyNumbers`, and update `parentCount`.  We also have to make
sure that our `while` loop handle descending and backing off procedure
correctly.

TODO: complete this section


### Second solution: Build before print {#ch1-status-sol2}

The reason why the previous approach is fairly difficult is because we
have to make sure that everything is printed in the right order.  It
can be much easier to have some extra freedom to work on the output
before printing.  We keep the output in an array.

```java
		Comment[] sortedComments = new Comment[n];
		int sortedCount = 0;
```

TODO: work out this section

By inserting comments into `sortedComments` in the correct order, we
can prepare the output array for easy printing.  However, it is still
hard to find out the right place to put a particular comment.  

We use another observation in the code below that only prints all the
comments in the correct order with no indent.  TODO: MORE HERE

The general structure of the method (with some step left out) is as follows.

```java
	private static void printComment(int n, Comment[] comments) {
		Comment[] sortedComments = new Comment[n];
		int sortedCount = 0;
		
		for(int p=0; p<n; p++) {
			for(int i=0; i<n; i++) {
				if(comments[i].getParentId() == p) {
					// put comments[i] in sortedComments in the right order
				}
			}
		}
		
		// print all comments in sortedComments
	}
```

Let's fill out all the code.

TODO: explain this code. make sure to note that when p=0, lastPos is intentionally -1

```java
	private static void printComment(int n, Comment[] comments) {
		Comment[] sortedComments = new Comment[n];
		int sortedCount = 0;
		
		for(int p=0; p<n; p++) {
			int lastPos = -1;
			boolean isFirstChild = true;
			
			for(int i=0; i<n; i++) {
				if(comments[i].getParentId() == p) {
					if(isFirstChild) {
						for(int j=0; j<sortedCount; j++) {
							if(sortedComments[j].getId() == p) {
								lastPos = j;
								break;
							}
						}
					}
					
					int pos = lastPos + 1;
					for(int j=sortedCount - 1; j>=pos; j--) {
						sortedComments[j+1] = sortedComments[j];
					}
					sortedComments[pos] = comments[i];
					sortedCount++;
					
					lastPos = pos;
					isFirstChild = false;
				}
			}
		}
		
		for(int i=0; i<n; i++) {
			System.out.println("- " + sortedComments[i].getId() + 
			                   " " + sortedComments[i].getMsg());
		}
	}
```

Printing with correct indentation is left as an exercise to the reader.

### The third solution: a recursive approach

The last solution is probably the simplest and the most elegant.  The
idea is also simple and natural.  But if you have bad experience with
recursion before, please try to forget that for a moment.

We start by asking this question: what do we have to do to print
comment $$i$$?

The answer is: we have to print a line with the body of comment $$i$$
and then print all comments $$j$$ replying to comment $$i$$.

This gives a fairly straight-forward implementation of `printComment`.

```java
	private static void printComment(Comment comment, 
	                                 int n, Comment[] comments) {
		System.out.println("- " + comment.getId() +
		                   " " + comment.getMsg());
                                   
		for(int i=0; i<n; j++) {
			if(comments[j].getParentId() == comment.getId()) {
				printComment(comments[j], n, comments);
			}
		}
	}
```

Please try to read the code carefully to understand what the code
intends to do.  Basically, it works exactly as we said: it prints a
line with the body of `comment` and then finds and prints all
comments $$j$$ replying to `comment`.

Method `printComment` looks like a typical Java method, with one
exception: it calls itself.  See below:

```java
	private static void printComment(Comment comment, 
	                                 int n, Comment[] comments) {
		// ...
		// ...     {
			// ...     {
				printComment(comments[j], n, comments);
			// }
		// }
	}
```

This naturally comes out from our description of how to print a
comment: To **print a comment**, we print it's body and **print all of
its replying comments**.

This type of methods (or functions, in general) is called recursive.
It arrives naturally when you have definitions that are recursive,
i.e., you define something in terms of itself.  As you can see, it can
greatly reduce the complexity of the code.

We are, though, missing another piece to run the code. We need a main
caller that calls `printComment` on all top-level comments (whose
`parentId` is zero).  Here it is.

```java
	private static void printAllComments(int n, Comment[] comments) {
		for(int i=0; i<n; i++) {
			if(comments[i].getParentId() == 0) {
				printComment(i, n, comments);
			}
		}
	}
```

Finally, we need to add indentation.  This can be done simply by
passing another parameter `level` to the method.  Note that in
`printComment`, when it calls itself, it tells the recursive call to
increase the indentation by passing in `level + 1`.

```java
	private static void printAllComments(int n, Comment[] comments) {
		for(int i=0; i<n; i++) {
			if(comments[i].getParentId() == 0) {
				printComment(comments[i], 0, n, comments);
			}
		}
	}

	private static void printComment(Comment comment, int level,
	                                 int n, Comment[] comments) {
		for(int j=0; j<level; j++) {
			System.out.print("  ");
		}
		System.out.println("- " + comment.getId() +
		                   " " + comment.getMsg());
		for(int j=0; j<n; j++) {
			if(comments[j].getParentId() == comments[i].getId()) {
				printComment(comments[j], level + 1, n, comments);
			}
		}
	}
```

You will see a lot of usage for recursion in this book and in computer
science, in general.  We would like to note that while recursion looks
pretty subtle if you try to peek inside the box to see the details, if
you look at that with the right level of details, it can be pleasingly
straight-forward.  We will discuss more about recursion, when we deals
with data structures like binary trees and recursive algorithms such
as Quick Sort and Merge Sort.

