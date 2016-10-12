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

## Implementing the set interface

## Implementing the map interface
