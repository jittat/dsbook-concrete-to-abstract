# Motivating examples

This chapter presents two familiar examples.

## Nearby items

Let's start by thinking about the PokemonGo.  You walk around and some monster near you shows up so that you can try to capture it.  Finding nearby monsters for every user is a classic example of a nearest neighbor search problem.  In this problem you are given two types of objects, monsters and players, and a radius $$R$$ and you want to find, for a given player, all monsters which are within distance $$R$$ from this player.

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

## Storing comments


## Analyzing performance

### What should we leave out from the equations?

### The big-$$O$$ notation
