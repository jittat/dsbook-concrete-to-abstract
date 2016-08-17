# Motivating examples

This chapter presents two familiar examples.  Note that each example is accompanied with a programming task.

## Nearby items

Let's start by thinking about the PokemonGo.  You walk around and some monster near you shows up so that you can try to capture it.  In an actual app, the app on your phone should receive a set of monsters close to your location and decides (locally) which monsters to show to you.  Since every player shares the same set of appearing monsters, the server has to figure out, for each player, this set of nearby monsters.

Finding nearby monsters for every user is a classic example of a nearest neighbor search problem.  In this problem you are given two types of objects, monsters and players, and a radius $$R$$ and you want to find, for a given player, all monsters which are within distance $$R$$ from this player.

**Related task:** [Nearby Search](ch1_tasks.md#tasks-ch1-nearby-search)

To encourage the reader to think about the problems, try out various solutions, and experiment with the running times, we usually provide a programming task formulated from a problem like this one.  Head to the task [Nearby Search](ch1_tasks.md#tasks-ch1-nearby-search) to read the task statement and try to implement a solution before you read further.

### First attempt

A simple solution for this problem is the following.

> For a given player $$p$$, you can iterate through all active monsters, compute the distances, and report all monsters within the good radius.

This looks fairly simple.  But if you have a lot of active users and a lot of monsters, it may require a lot of work.  Let's write a simple program to test the limits of this straight-forward idea.

We shall assume that we only know how to use arrays.  Therefore, in this example to simplify the code, we will only count the number of nearby monsters for a player $$p$$.

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

We have a simple method implementing our first idea for the nearby monster search algorithm.  The next step is to test its performance.  We will do that by running the algorithm against a set of test data and measure the running times.

We still have to think about the kind of test data we want to use and how to obtain them.  Since we want test data to represent real-world usage, if we can collect them in actual usages of the algorithm that would be best.  Since this is not possible, we have to settle with synthetically generated data.  Here, we will generate monsters and players randomly, assuming that they are likely to appear anywhere on the map.

Also, if we look at the method, there is another parameter we have to think about: the radius $$r$$.  Since the running time of this algorithm does not depend on $$r$$, we can use any number.

Let's try to put everything into places.  (Note that our main program reads the number of players and monsters from the command-line arguments.)

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

The following table summarizes the running time when for various values of $$n$$ (assuming that $$m=n$$).

| $$n$$        | Running time |
| ------------ | ------------ |
| 10           |              |
| 100          |              |
| 1000         |              |
| 10000        |              |
| 20000        |              |
| 30000        |              |

We can plot the running times agaist $n$, which is essentially the size of the problem.

TODO: graph + discussion on the quadratic running time

### The limits

### Can we do better?

Data partitioning is the answer.

## Storing comments

The second problem we shall consider is about how to display comments in an on-line forum (or a Facebook status).  Modern forum software allows users to post comments to comments.  While many systems, like Facebook, do not allow you to post comments to comments of a comments (i.e., the level of comments is only two), we consider a more general commenting structure, where any comments can have comments.  In this case, you end up with a situation like forums like Reddit or Hacker News, where users keep posting comments and you have to display these comments nicely, showing clearly the "nested" structure of the comments.

Let's start with an example.  Suppose we have 7 comments, each comment has a unique ID.

| ID     | Reply to ID  | Messages   |
|--------|--------------|--------------------|
| 1      | status | hello world |
| 2 | status | this is another comment |
| 3 | 1 | first reply |
| 4 | 2 | good morning thailand |
| 5 | 1 | second one |
| 6 | 1 | third one |
| 7 | 5 | when it is ok |

When two comments reply to the same comment, you want to show the later one (with higher ID) later.  The following is a possible way to display the comments (showing the nested structure).

```
- 1 hello world
  - 3 first reply
  - 5 second one
    - 7 when it is ok
  - 6 third one
- 2 this is another comment
  - 4 good morning thailand
```

**Related task:** [Status comments](ch1_tasks.md#tasks-ch1-comments)

### Nested comments: diving in

### Going back

### Build before print

### Same problem, different instances

### A recursive approach

## Analyzing performance

### What should we leave out from the equations?

### The big-$$O$$ notation
