---
layout: post
title: "Procedurally Generated 2D Dungeons"
date: 2022-11-06 23:19:00 -0700
---

I've been playing around with a few different methods of procedurally generating top-down 2D dungeons.

Here's a demo; source code: [`weinstein/dungeon`](http://github.com/weinstein/dungeon) on GitHub.

{% include unity-webgl-player.html name="dungeon" %}

How it works:

1. randomly place some rectangular non-overlapping rooms
2. determine adjacency by triangulating the rooms' centers with a Delaunay triangulation
3. determine which room-to-room edges to keep and turn into corridors:
   * compute the minimal spanning tree on the adjacency graph and keep all edges in that MST
   * randomly select additional edges not in the MST using a biased coin flip, creating some cycles
4. determine the starting and ending rooms by searching for the pair of rooms that have the longest path between them in the graph
5. render corridors by running A-star search between adjacent rooms
6. render staircased for the starting/ending rooms and randomly place clutter in the rooms

This entire process repeats each time the player goes down the stairs in the ending room.

Being able to generate a high-level graph representation of the dungeon is really useful. For example:
* treasure chests can be placed at "dead ends" in the graph to reward exploration
* locked doors and keys can be placed in a way that is non-trivial but solvable

Locked doors should block the critical path (from the starting room to the ending room), otherwise the player could ignore it and go around.
At the same time, keys need to be placed in rooms that are reachable before unlocking the door.

### Non-Overlapping Rooms

Rooms are placed independently of each other.
Then in a subsequent "jiggling" step, pairs of rooms with intersecting bounds are pushed away from each other so that they no longer intersect.
This process repeats for all pairs of rooms until all the rooms are non-intersecting (or a maximum number of iterations is reached).
In practice, as long as the desired number of rooms and room sizes is not too dense, this only requires a small number of fast iterations.

### Corridor Straightness

The A-star search that happens in step (5) to determine the routing for corridors has some tweaks for aesthetic purposes.
Visually, I prefer corridors that are not too straight and not too jagged, but rather somewhere inbetween.
To that end:
* distances are rounded based on a `corridorStraightness` factor
* ties are broken with preference given to paths that continue in a straight line, without changing direction

When `corridorStraightness` is `1`, the behavior is identical to regular A-star search.
Increasing `corridorStraightness` has the effect of giving larger preference to straight lines when routing corridors.
As `corridorStraightness` becomes arbitrarily large, the behavior becomes like DFS and produces very poor corridors that are as straight as possible and snake back-and-forth to the boundary of the pathable area.

### Unreachable Rooms

There are some opportunities to create unreachable rooms in the generation process:
* considering the placement of rooms and already-placed corridors, it is not always possible to route a corridor between rooms that are supposed to be adjacent without that corridor overlapping something else
* randomly placed clutter can create barriers, since the player cannot move through all kinds of clutter (ex: treasure chests)

Neither of these are properly handled in the current version; it's possible that generated levels are unsolvable due to a missing corridor or a treasure chest blocking a corridor on the critical path.

To handle the first problem, corridor routing should be part of generating the room graph, so that there are never unreachable rooms in the room graph to begin with and the starting/ending rooms are computed based on the final room graph that was renderable.

The second problem is simple to detect by running a search from the starting room to the ending room. That could add an (expensive) precondition to clutter placement, or more efficiently, it could be used as a final pass to remove blocking clutter.
