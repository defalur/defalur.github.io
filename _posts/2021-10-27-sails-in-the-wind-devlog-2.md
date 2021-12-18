---
layout: post
title:  "Sails in the wind, Devlog 2"
date:   2021-12-18 19:07:00 +0200
categories: devlog update
visible: 1
---
## Getting some work done

Working on a personal project is not always an easy things. Life tends to get in
the ways of everything, and in some cases the project itself could be harder
to work on than anticipated. However, I managed to progress on Sails in the wind,
which is great, so let's talk about that!

## Generating islands

My goal for this part of the project was to generate simple islands in order to
give the player places to sail to, and not have an empty endless ocean to explore.

The first step was to generate a simple island. For this step I used a noise function
which is slightly reshaped and a threshold to decide what is above the water. The method
used is basically the one described [here](https://www.redblobgames.com/maps/terrain-from-noise/),
but with far less complexity.

The goal here was to get a prototype working reasonably well with as little work as possible. Thus I used the simplex noise shipped with Godot. However, that is not sufficient to get an island.

I consider an island as being some terrain in a finite square with water on the edges. The way I can get the water on the edges is by using the size of the square to lower the terrain towards the edges. This method is very simple and gives satisfactory results for my needs.

![Island]({{site.baseurl}}/assets/devlog_2/island.png)

## Generating many islands

Now that I'm able to generate an island at a given position with a given size, how may I go about having many of them in my world?

There are a couple constraints that I wanted to follow for this feature:
* Not being able to generate islands too close to each other.
* Being able to handle a large amount of islands with reasonable performance.

Those two constraints can be solved in a simple way when using a grid. For each cell in the grid, there is a probability that an island is present in that cell. If an island is present then I can generate it when entering the cell. This allows me to only load a single island at once, know its location, and create it with an easily computable seed.

![load_grid]({{site.baseurl}}/assets/devlog_2/load_grid.png)

This little drawing illustrates the grid of islands, where the ship is present in a single cell and thus the closest island can be considered to be in that cell, which means that only the cell in green needs to contain a loaded island for the game to work.

When changing to another cell, the currently loaded island is unloaded and a new island in the new current cell can be loaded.

The main problem with this approach is that if an island is sufficiently big, the player will see it appear on the screen, which I don't want. I could solve this by making only small islands, or increasing the size of the cells, or I could load more cells at once.

## Visibility problems

While moving around to see how my island system works I noticed that the top down view of the game made it very difficult to find islands, even if they were just outside the screen. Thus I created an indicator arrow that is displayed around the ship when in a cell that contains an island. This arrow points towards the center of the cell, which is where the island is located.

![arrow]({{site.baseurl}}/assets/devlog_2/arrow.png)

This helps greatly in finding new islands, and could be improved in the future where it could display more information.

## Conclusion

That's it for this devlog. I still don't know what I'll do next, but I would like to add various reasons to explore and go from island to island. Maybe I could focus on exploration, or trade, or just give simple missions to the player.
