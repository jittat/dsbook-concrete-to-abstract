## Node Deletion

### Deletion in standard binary search trees

### Lazy Deletion

In balanced binary search trees, maintaining the ordering condition with
balancing condition is very complicated.  In this section we shall introduce
another technique to get around this issue, called **lazy deletion**.

As the name implies, when we use lazy deletion, we do not actually delete any
node from the tree, but simply mark that particular node as **deleted** node.
When another search reaches that node, we act as if that key is not in the tree.
If a new node is inserted with the same key, we replace the deleted one with the
new one.
