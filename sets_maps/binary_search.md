## Binary search

We will learn how to implement *partially* the set and map interfaces when keys
do not change.  We refer to this case as the *static* case.  We will see how
sorted arrays can be searched extremely efficiently under a simple paradigm of
divide and conquer.

### Let's guess the number!

### Implementing binary search, iteratively

```java
private int binarySearch(int[] keys, int x) {
    int left = 0;
    int right = keys.length - 1;

    while(left <= right) {
        int mid = (left + right)/2;

        if(keys[mid] == x) {
            return mid;
        } else if(keys[mid] < x) {
            left = mid + 1;
        } else {
            // when keys[mid] > x
            right = mid - 1;
        }
    }
    return -1;
}
```

### Implementing binary search, recursively

```java
private int binarySearch(int[] keys, int x) {
    return binarySearch0(keys, 0, keys.length);
}

private int binarySearch0(int[] keys, int x, int left, int right) {
    if(left > right) {
        return -1;
    }

    int mid = (left + right)/2;

    if(keys[mid] == x) {
        return mid;
    } else if(keys[mid] < x) {
        return binarySearch0(keys, x, mid+1, right);
    } else {
        return binarySearch0(keys, x, left, mid-1);
    }
}
```

## Static implementation of sets and maps
