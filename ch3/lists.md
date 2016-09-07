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
analysis, assume that $$n=m$$.  

There are 3 for loops in this code.  Clearly, the third for loop runs in time $$O(n+m)=O(n)$$.  
The first for loop is fairly interesting.  From the previous
chapter, we know that `insert` in `InsertableArray` runs in
worst-case $$O(n)$$ time when there are $$n$$ elements in the array.
However, the way we use `insert` here is actually its best case, i.e.,
we only `insert` new element as the last element in the array;
therefore, the running time in this case
is $$O(1)$$.  The first loop then runs in term $$O(n)$$.

The main working loop for this solution is the middle for loop.  Let's consider
it in details.

```java
// --- The loop runs for m iterations.  We have to figure out
//     the running time for each iteration.
for(int i=0; i<m; i++) {
  // --- these two statement runs in 2 time units: O(1) time
  int ballNumber = n + i + 1;
  int pred = p[i];

  // --- In the worst case, findIndex runs in time linear
  //     on the number of elements in the array.
  //     Overall the execution of the loop, the number of elements
  //     are at most n + m.  Thus, this line runs in time O(n+m).
  int pos = outArray.findIndex((Integer num) -> (num == pred));

  // --- As discussed, this statement runs in time O(n+m) as well.
  outArray.insert(pos + 1, ballNumber);
}
```

Although the loop itself runs for only $$m$$ iterations, we also have to
consider the running times of operations inside the loop.   The first two
statements runs in $$O(1)$$ time, per iteration.

From the last chapter, we knows that both `findIndex` and `insert` runs in time
at most linear on the number of elements in the array.  While the number of
elements in the array is not constant in every iteration, we can use the largest
one, i.e., $$n+m$$ elements.  Therefore both statements
run in time $$O(n+m)=O(n)$$.  Thus, the main loop runs in
time $$O(n^2)$$, which dominates
the total running time of the algorithm.

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

##### Running time

First for loop runs for $$n$$ iterations.  Each iteration takes $$O(1)$$ time;
thus, the total running time for the loop is $$O(n)$$.

The last while loop iterates through the list with $$n+m$$ elements.  Thus, the
running time is $$O(n+m)$$, the number of elements in the linked list.

The main for loop runs for $$m$$ iterations.  Let's look at the code.

```java
// --- The main loop runs for m iterations.
for(int i=0; i<m; i++) {
  // --- All statements outside the while loop run in constant time;
  //     thus, the total running time (outside while loop) is O(1).
  int ballNumber = n + i + 1;
  int pred = p[i];

  ListNode newNode = new ListNode(ballNumber);

  ListNode currentNode = head;

  // --- This while loop iterates through the list.  While we do no
  //     know how many nodes we traverse, there cannot be more than
  //     m + n nodes.  Thus, we can say that the loop runs in O(n+m) time.
  while(currentNode != null) {
    if(currentNode.val == pred) {
      break;
    }
    currentNode = currentNode.next;
  }

  newNode.next = currentNode.next;
  currentNode.next = newNode;
}
```

Since the inner code runs in time $$O(n+m) = O(n)$$ and the for loop runs
in $$m$$ times, the total running time is $$O((n+m)\cdot(m))=O(n^2)$$.


#### An efficient implementation

We can avoid searching the linked list by keeping `ListNode` in an array.

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

It is clear that the inner loop runs in time $$O(n+m)$$, and the algorithm runs
in time $$O(n+m)$$.

## List ADT

We use `ListNode` to implement a linked list in the last example.  Note that we
directly manipulate the list and the low-level codes for list processing appear
throughout our main code.  These messy codes cannot be reused easily.  We will
try to extract reusable codes from the working code in the previous example.

We shall extract a class from the codes.  To do so, we have to define class
interface (or API) so that our code client can use our class code.   Although we
can define our class any way we like, it is worthwhile to look at standard
collection library for linked list as a guideline.

### Java collections

Let's take a look at interface `List<E>` from [Java 7](https://docs.oracle.com/javase/7/docs/api/java/util/List.html).   The following table lists interesting methods on the list interface.

| Categories | Methods |
|------------|----------------------------------|
| access     | `get(int index)` |
| size       | `size()` |
| test       | `isEmpty()`, `contains`, `containsAll`, `equals` |
| add        | `add(E e)` - *add element at the end*, `add(int index, E e)`, `addAll` |
| remove     | `remove(int index)`, `remove(Object o)`, `removeAll` |
| iterators  | `iterator()`, `listIterator()` |

Note that the interface provides common operations a list provides, but it does
not provide a way to traverse the list itself in this interface.  However,
following the [iterator
pattern](https://en.wikipedia.org/wiki/Iterator_patternhttps://en.wikipedia.org/wiki/Iterator_pattern),
the list interface provides access to elements by iterators with methods
`iterator` and `listIterator`.   You can think of an iterator as a reference to
a list node that we can use to traverse elements in the list in the same way
that we use `currentNode` (of type `ListNode`) to search for elements in the previous example.

The following methods are provided by `listIterator`.

| Categories | Methods |
|------------|---------|
| movement | `next()`, `previous()` |
| test | `hasPrevious()`, `hasNext()` |
| manipulation | `add(E e)`, `remove()`, `set(E e)` |
| index | `nextIndex()`, `previousIndex()` |

Note that `listIterator` can move backwards.  This is a operation that we cannot
support with the current linked list structure that we have.  In later section
where we discuss doubly linked lists, we will see how to do that.

### Our list interface

We provides many operations for our linked list.  Our first implementation will
not provide list access with iterators, but allow direct access to list nodes
with class similar to `ListNode`.  We also provide more methods for dealing with
elements at the beginning and at the end of the lists.  In [next
section](list.md#list-iterator-pattern), we wrap the node references inside
iterators.

| Methods | Descriptions |
|---------|--------------|
| `add(E e)` | add element `e` at the end of the list |
| `addHead(E e)` | add element at the head of the list |
| `removeHead()` | remove the first element from the list |
| `getHead()` | return the first list node |
| `getTail()` | return the last list node |
| `addAfter(Node node, E e)` | add an element after a `node` |
| `removeAfter(Node node)` | remove a node after `node`|

## Linked List Implementation

### Lists of integers

The following code is a class for linked lists of integers.  We move class
`ListNode` to be an inner class in class `LinkedList`.  We also hide direct
access to `next` references and `val`, so that users of class `LinkedList`
cannot perform reference manipulation.

```java
public class LinkedList {
	public class Node {
		private int val;
		protected Node next = null;

		public Node(int val) {
			this.val = val;
			this.next = null;
		}

		public Node() {
			this(0);
		}

		public Node getNext() { return next; }
		public int getVal() { return val; }
		public void setVal(int v) { val = v; }
	}
  // ...
}
```

As in the previous example, we need to keep two references to the first and the
last nodes of the list, and initialize them to `null`.

```java
public class LinkedList {
	private Node head = null;
	private Node tail = null;
	private int size = 0;

	public LinkedList() {
		head = tail = null;
		size = 0;
	}
  //..
}
```

Let's start by writing `isEmpty`,`size`, and other access methods.

```java
  public boolean isEmpty() {
    return tail == null;
  }

  public int size() {
    return size;
  }

  public Node getHead() {
    return head;
  }

  public Node getTail() {
    return tail;
  }
```

The `add` method can be directly extracted from the code in zooma.

```java
public Node add(int v) {
  Node newNode = new Node(v);

  if(! isEmpty()) {
    tail.next = newNode;
    tail = newNode;
  } else {
    head = tail = newNode;
  }

  size++;    	
  return newNode;
}
```

Method `removeAfter` checks if `node` has the next node, then adjusts the next
pointer accordingly.

```java
	public void removeAfter(Node node) {
		if(node.next != null) {
			node.next = node.next.next; 		
			size--;
		}
	}
```

Method `addHead` creates a new node, points its next reference to the recent
`head`, and updates the `head` reference to this node.

```java
	public Node addHead(int v) {
		if(isEmpty()) {
			return add(v);
		} else {
	    	Node newNode = new Node(v);
	    	newNode.next = head;
	    	head = newNode;
	    	return newNode;
		}
	}
```

Method `removeHead` and `addAfter` are left out as exercises to the reader.

### Hiding the nodes: the iterator pattern {#list-iterator-pattern}

### Generic linked list

## Doubly Linked Lists
