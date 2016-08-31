# Lists

Lists are everywhere.  Comments directly replying the same status can be kept in
a list.  A list of monsters around you (within a particular radius) in a game is
a list.  When you have a collection of objects, the simplest way to deal with
them is to put them in a list.

What can you do with a list?

Below is an example of a list of top Women's World Cup goal scorers.

```
Marta
Birgit Prinz
Somying Jaiyen
Abby Wambach
Michelle Akers
Sun Wen
Bettina Wiegmann
Ann Kristin Aarones
Heidi Mohr
```

It seems that there is a mistake.  `Somying Jaiyen` should not be in the list.
So let's remove it.  This is the result

```
Marta
Birgit Prinz
Abby Wambach
Michelle Akers
Sun Wen
Bettina Wiegmann
Ann Kristin Aarones
Heidi Mohr
```

Now, suppose that in year 2019 another player, `Mary Playgood` has just scored
12 goals.  With this many goals, she has to be inserted into the list after
`Akers`. The list becomes:

```
Marta
Birgit Prinz
Abby Wambach
Michelle Akers
Mary Playgood
Sun Wen
Bettina Wiegmann
Ann Kristin Aarones
Heidi Mohr
```

You have just heard the name `Orathai Srimanee`, who is a famous Thai player.
You may want to check if she is in the list.  To do so, you have to look at
every entry in the list, and find out that she is not in the list.  You want to
know how many additional goals she has to score to be in the list.  With the
current information on the list, you cannot answer the question.  You look up
[wikipedia](https://en.wikipedia.org/wiki/List_of_FIFA_Women%27s_World_Cup_goalscorers) and update the list with goal information.

```
Marta, 15
Birgit Prinz, 14
Abby Wambach, 14
Michelle Akers, 12
Mary Playgood, 12
Sun Wen, 11
Bettina Wiegmann, 11
Ann Kristin Aarones, 10
Heidi Mohr, 10
```

You look at the last entry and see that `Heidi Mohr` scored 10 goals.  So
`Orathai Srimanee` needs to score 8 more goals to have a chance to be in the
list.

You can see that there is a lot of operations you can do with a list.  And also,
note that an entry in a list can keep fair amount of information.

This chapter discuss how we can implement a list and also an abstract data type
for a list.  

We can use the most common data structure, **arrays**, to deal with lists.
Arrays are good for keeping collections of data.  However, array manipulations
are usually inefficient.  Since every element in an array is stored
contiguously, insertion and deletion of array elements usually take linear time.
In this chapter, we shall look at a commonly used abstract data type for linear
collection called a list.  You can implement a list using an array, but the
another common implementation is a linked list, which allows efficient
manipulation of list items.

## A list of colored balls

Let's start with a concrete problem.  Consider a rather popular casual game
called Zuma.  In this game, there is a list of enemy colored balls lining up to
reach a secret gate.  You want to prevent that by shooting another set of
colored balls to the list.  Because consecutive three balls of the same color
explode, you can potentially destroy all the enemy balls.

We will look at the simpler version of the game, where we can shoot the balls,
but the balls remain in the list.  In this tasks, there are $$n$$ enemy colored
balls and you want to shoot $$m$$ colored balls.  To be precise, the enemy balls
are referred to as ball $$1$$ to ball $$n$$, and the balls you will shoot are
referred to as ball $$n+1$$ to ball $$n+m$$.  For each of your $$i$$-th ball,
for $$1\leq i\leq m$$ you shoot into the list, it will land right after
ball $$p[i]$$.  (Note that $$i$$ here is not referring to the ball numbers, as
your $$i$$-th ball is numbered $$n+i$$.) You want to know the final list of
balls.

Consider the following example.

TODO: add example

**Related task:** [Zooma 1](ch3_tasks.md#tasks-ch3-zooma-1)

One can deal with this task by just simulating the system and output the final
result.  Starting with a list of $$n$$ numbers from $$1$$ to $$n$$.  We iterate
the array $$p[i]$$ and insert ball number $$n+i$$ into the sequence right after
$$p[i]$$.  To do so, we need to look up where $$p[i]$$ is in the sequence and
insert $$i+n$$ right after it.

### Array implementation

Using a `InsertableArray` from last chapter, we can implement a solution to the
task as follows.  Note that `p[i]` here is a 0-indexed array; thus, $$p[1]$$ in
the task is stored in `p[0]`, and so on.

```java
InsertableArray<Integer> outArray = new InsertableArray<Integer>(n + m);

for(int i=0; i<n; i++) {
  outArray.insert(i, i+1);
}

for(int i=0; i<m; i++) {
  int ballNumber = n + i + 1;
  int pred = p[i];

  int pos = outArray.findIndex((Integer num) -> (num == pred));
  outArray.insert(pos + 1, ballNumber);
}

for(int i=0; i<n+m; i++) {
  System.out.println(outArray.get(i));
}
```

The solution works fine.  Next, we need to analyze its running time to see how
the program deals with the cases with large $$n$$ and $$m$$.  To simplify our
analysis, assume that $$n=m$$.  Clearly, the first for loop runs in
time $$O(n)$$ and the second for loop runs in time $$O(n+m)=O(n)$$.  The main
working loop for this solution is the middle for loop.  Although the loop itself
runs for only $m$ iterations, we also have to consider the running times of
operations inside the loop.  From the last chapter, we knows that both `findIndex`
and `insert` runs in time at most linear on the number of elements in the array;
in this case, they both run in time $$O(n+m)=O(n)$$.  Thus, the main loop runs
in time $$O(n^2)$$, which dominates the total running time of the algorithm.

In this chapter, we will learn a new data structure that can speed up the
solution to run in time $$O(n)$$.

## Linked lists

### Class `ListNode`

```java
public class ListNode {
	public int val;
	public ListNode next = null;

	public ListNode(int val) {
		this.val = val;
		this.next = null;
	}

	public ListNode() {
		this(0);
	}
}
```

### "Raw" implementation

#### A slow version

```java
ListNode head = null;
ListNode tail = null;

for(int i=0; i<n; i++) {
  ListNode newNode = new ListNode(i+1);
  nodes[i+1] = newNode;

  if(tail != null) {
    tail.next = newNode;
    tail = newNode;
  } else {
    head = tail = newNode;
  }
}

for(int i=0; i<m; i++) {
  int ballNumber = n + i + 1;
  int pred = p[i];

  ListNode newNode = new ListNode(ballNumber);

  ListNode currentNode = head;
  while(currentNode != null) {
    if(currentNode.val == pred) {
      break;
    }
    currentNode = currentNode.next;
  }

  newNode.next = currentNode.next;
  currentNode.next = newNode;
}

ListNode currentNode = head;
while(currentNode != null) {
  System.out.println(currentNode.val);
  currentNode = currentNode.next;
}
```

#### An efficient implementation

```java
ListNode[] nodes = new ListNode[n + m + 1];

for(int i=0; i<n; i++) {
  ListNode newNode = new ListNode(i+1);
  nodes[i+1] = newNode;
  // ...
}

for(int i=0; i<m; i++) {
  // ...
  ListNode currentNode = nodes[pred];

  newNode.next = currentNode.next;
  currentNode.next = newNode;
}
// ...
```

## List ADT

## Linked List Implementation

## Doubly Linked Lists
