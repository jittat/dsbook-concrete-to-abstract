## Code reuse and class extraction

We reconsider the second solution to the status reporting problem and
try to extract reusable code from it.

### Code cleaning

The code from the [second solution](examples.md#ch1-status-sol2) is
fairly long and hard to read.  Before trying to make the code
reusable, let's make it cleaner by extracting parts of the code into
methods.

The first one that we can extract is the output method.

```java
	private static void printSortedComments(int n, Comment[] sortedComments) {
		for(int i=0; i<n; i++) {
			System.out.println("- " + sortedComments[i].getId() + 
			                   " " + sortedComments[i].getMsg());
		}
	}
```

Let's extract the main loop into method `insertReplyingComments`, that
takes the comment ID and inserts all comments that reply to that
comment Id to the output data.

Note that this part of the code deals with both array `sortedComments`
and the counter `sortedCount`.  The extracted method should be able to
modify both variables.  It is easy to modify an array from a method;
it is not as easy for the counter.  We let the method returns the
number of replying comments, so that the main method `printComment`
can update the counter `sortedComments` accordingly.

The original method becomes as follows.

```java
	private static void printComment(int n, Comment[] comments) {
		Comment[] sortedComments = new Comment[n];
		int sortedCount = 0;
		
		for(int p=0; p<n; p++) {
			int replyCount = insertReplyingComments(p, n, comments, 
			                                        sortedComments, sortedCount);
			sortedCount += replyCount;
		}
		printSortedComments(n, sortedComments);
	}
```

Now, the main engine of this approach is the following method
extracted from `printComment`.  Note extra comments added to show
where the code mainly manipulates `sortedComments` and `sortedCount`.

```java
	private static int insertReplyingComments(int p, int n, Comment[] comments, 
	                                          Comment[] sortedComments, 
	                                          int sortedCount) {
		int lastPos = -1;
		boolean isFirstChild = true;
		int addedCount = 0;
	        
		for(int i=0; i<n; i++) {
			if(comments[i].getParentId() == p) {
				if(isFirstChild) {
					// ******************************************
					// * find comment with id p in sortedComments
					// ******************************************
					for(int j=0; j<sortedCount; j++) {
						if(sortedComments[j].getId() == p) {
							lastPos = j;
							break;
						}
					}
				}
					
				// ******************************************
				// * insert comments[i] at location pos
				// ******************************************
				int pos = lastPos + 1;
				for(int j=sortedCount - 1; j>=pos; j--) {
					sortedComments[j+1] = sortedComments[j];
				}
				sortedComments[pos] = comments[i];
				sortedCount++;
                                
				addedCount++;
					
				lastPos = pos;
				isFirstChild = false;
			}
		}
		return addedCount;
	}
```

These two operations are fairly general for array manipulations; thus,
we might find them useful in other contexts.  We shall proceed in two
steps.  First, we will extract a class for maintaining arrays in this
specific context.  Then, we will rewrite the class so that it becomes
general and can handle objects of any types.

### Class extraction

We shall extract those two methods into method `findById` and `insert`
in class `CommentArray`.  The main codes for `printComment` and
`insertReplyingComments` become as follows.

```java
	private static void printComment(int n, Comment[] comments) {
		CommentArray sortedComments = new CommentArray(n);
		
		for(int p=0; p<n; p++) {
			insertReplyingComments(p, n, comments, 
			                       sortedComments);
		}
		printSortedComments(n, sortedComments);
	}

	private static void insertReplyingComments(int p, int n, 
	                                          Comment[] comments,
	                                          CommentArray sortedComments) {
		int lastPos = -1;
		boolean isFirstChild = true;
	        
		for(int i=0; i<n; i++) {
			if(comments[i].getParentId() == p) {
				if(isFirstChild) {
					lastPos = sortedComments.findById(p);
				}

				int pos = lastPos + 1;
				sortedComments.insert(comments[i], pos);
				lastPos = pos;
				isFirstChild = false;
			}
		}
	}
```

Class `CommentArray` encapsulates array operations needed for
maintaining `Comment`s.

```java
public class CommentArray {

	private Comment[] comments;
	private int commentCount;
	private int size;

	public CommentArray(int size) {
		this.comments = new Comment[size];
		this.size = size;
		this.commentCount = 0;
	}
	
	public int findById(int p) {
		for(int i=0; i<commentCount; i++) {
			if(comments[i].getId() == p) {
				return i;
			}
		}
		return -1;
	}
	
	public void insert(int i, Comment comment) {
		for(int j=commentCount - 1; j >= i; j--) {
			comments[j + 1] = comments[j];
		}
		comments[i] = comment;
		commentCount++;
	}

	public Comment get(int i) {
		return comments[i];
	}
}
```

### Generics

If we look at class `CommentArray`, with an exception of method
`findById`, all method implementations do not depend on the specific
class `Comment`.  If we want this class to work for any types of
objects, we can change `Comment` type to `Object`, which is the base
class of every objects in Java.  Since every object can be "down"
casted to `Object`, our `CommentArray` can take any objects.

We should rename the class as well.  Let's call it `InsertableArray`.
We also rename `comments` to `items`.  The skeleton of the code is as
follows.

```java
public class InsertableArray {

	private Object[] items;
        private int itemCount;
	// ...

	public InsertableArray(int size) {
		this.items = new Object[size];
                this.itemCount = 0;
		// ...
	}
	
	public int findById(int p) {
		// ... ** currently broken **
	}
	
	public void insert(int i, Object item) {
		// ...
	}

	public Object get(int i) {
		// ...
	}
}
```

By down casting, you allow this class to work with all types of
objects.  This purely object-oriented implementation style is the
approach taken by early versions of Java.  This poses an important
problem with real usage of the class.  Take a look at this example:

```java
    InsertableArray arr = new InsertableArray();
    Dog d = new Dog();
    Cat c = new Cat();

    arr.insert(0, d);          // you can insert any class of objects
    arr.insert(0, c);

    Cat c = (Cat) arr.get(1);  // needs up casting + run-time check.
```

TODO: give introduction to generics here

```java
public class InsertableArray<T> {

	private T[] items;
	private int itemCount;
	private int size;

	@SuppressWarnings("unchecked")
	public InsertableArray(int size) {
		this.items = (T[]) new Object[size];  // ***
		this.size = size;
		this.itemCount = 0;
	}
	
	public void insert(int i, T comment) {
		for(int j=itemCount - 1; j >= i; j--) {
			items[j + 1] = items[j];
		}
		items[i] = comment;
		itemCount++;
	}


	public T get(int i) {
		return items[i];
	}

}
```

TODO: discuss the up casting + supresswarning

#### Functional interfaces and lambda expressions

```java
	public int findIndexBy(Predicate<T> cond) {
		for(int i=0; i<itemCount; i++) {
			if(cond.test(items[i])) {
				return i;
			}
		}
		return -1;
	}
```

Usage with lambda expression.

```java
	// ...
	if(isFirstChild) {
		lastPos = sortedComments.findIndexBy((Comment c) -> (c.getParentId() == p));
	}
	// ...
```
