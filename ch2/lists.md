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

Let's start with a concrete problem.  

**Related task:** [Zooma 1](ch2_tasks.md#tasks-ch2-zooma-1)

## Array implementation

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
