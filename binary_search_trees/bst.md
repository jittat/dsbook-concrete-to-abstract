# Binary Search trees

## Basic implementation

Each data item in a binary search tree is kept in a `TreeNode`, typically
defined as below:

```java
public class TreeNode {
	public int val;
	public TreeNode leftChild;
	public TreeNode rightChild;

	public TreeNode(int v) {
		val = v;
		leftChild = rightChild = null;
	}
}
```

### Find

```java
private TreeNode find(TreeNode node, int val) {
  if(node == null) {
    return null;
  }
  if(node.val == val) {
    return node;
  } else if(node.val < val) {
    return find(node.rightChild, val);
  } else {
    return find(node.leftChild, val);
  }
}
```

### Insert (version 1)

This implementation cannot deal with the case when the tree is empty.  You need
to check if the tree head is not empty first.  In the case where the head is
empty, you have to create the head outside this method.

```java
private void insert(TreeNode node, int val) {
  if(node.val == val) {
    return;
  } else if(node.val < val) {
    if(node.rightChild == null) {
      node.rightChild = new TreeNode(val);
    } else {
      insert(node.rightChild, val);
    }
  } else {
    if(node.leftChild == null) {
      node.leftChild = new TreeNode(val);
    } else {
      insert(node.leftChild, val);
    }
  }
}
```

### Insert (version 2)

To deal with an empty tree and the fact that Java does not support pass by
reference, we return the new (or the original) objects from method `insert`.

Thus, when inserting `x` to the tree, you call

```java
  head = insert(head, x);
```

The code of the method is simpler, because we do not have to deal with the case
where the node left or right children are null:

```java
private void insert(TreeNode node, int val) {
  if(node == null) {
    return new TreeNode(val);
  } else if(node.val < val) {
    node.rightChild = insert(node.rightChild, val);
  } else {
    node.leftChild = insert(node.leftChild, val);
  }
  return node;
}
```

## Deletion in binary search trees

## Implementing the set interface

## Implementing the map interface

## Running times

The running times on operations on binary search trees depend on the number of
nodes in the trees one has to consider.  In most cases, basic operations require
one to start from the root and follow down the tree to reach some particular
node.  Therefore, if there is a node in a tree which is very deep
(very far from the root), it can take a long time if one have to go from the
root to reach this node.

In a binary tree, the **depth** of a node is the number of edges from the root
to that node.  The time it takes to find a key in the tree is the depth of the
node containing that key.

### A very bad binary search trees
