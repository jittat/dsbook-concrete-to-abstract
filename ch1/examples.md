# Motivating examples

This chapter presents two familiar examples.

## Nearby items

Let's start by thinking about the PokemonGo.  You walk around and some monster near you shows up so that you can try to capture it.  Finding nearby monsters for every user is a classic example of a nearest neighbor search problem.  In this problem you are given two types of objects, monsters and players, and a radius $$R$$ and you want to find, for a given player, all monsters which are within distance $$R$$ from this player.

A simple solution for this problem is the following.

> For a given player $$p$$, you can iterate through all active monsters, compute the distances, and report all monsters within the good radius.

This looks fairly simple.  But if you have a lot of active users and a lot of monsters, it may require a lot of work.  Let's write a simple program to test the limits of this straight-forward idea.

```java
List<Monster> findNearbyMonsters(Player p, double r) {
  ArrayList<Monster> nearbyMonster = new ArrayList();
  for(Monster m : monsters) {
     if(m.distance(p) < r) {
       nearbyMonster.add(m);
     }
  }
  return nearbyMonsters;
}
```


## Storing comments
