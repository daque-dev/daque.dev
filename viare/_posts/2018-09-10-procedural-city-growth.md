---
title: (Very) Simplistic model to simulate urban growth
subtitle: "Finding the reasons of big well-survived cities"
blog: viare
permalink: viare/procedural-city-growth

author: David Omar
author_url: https://github.com/davidomarf
---

For the last ~three days, I've been trying to implement a model that simulates how
a city grows its territory and forms suburbs.

My very early model consists of this:

  - create a terrain with a river and some resource clusters
  - create a city at the sides of the river
  - make it grow by "*pursuing*" resources in the terrain

To make my initial approximation to the problem, I assumed a lot of things, getting
to an overly simplistic model. Fortunately, its development has helped me to generate
more ideas for its further escalation.

My assumptions are:

- Terrain is completely flat.
- River doesn't have a flow direction.
- River's influence on city is just it's `City Core` location.
- `City core` always stablishes on one point of the river.
- `City core` is perfectly round.
- Resources are the same. There is no variety.

## Content
{:.no_toc}

* TOC
{:toc}

## Generating terrain

### River

---
I must warn this: The process explained here is by no means something serious
that I'll try to preserve for the "definitive" version of the code. It was
just to make me feel less "just-so" when choosing one point to generate a city.

---
This is a pseudocode of what happens inside the river generator.

```
define START and END
define RIVER_PATH[]
push START into RIVER_PATH
define HEAD = START
while HEAD isn't close enough to END:
  HEAD is last element of RIVER_PATH
  ANGLE is the angle of the line segment formed by HEAD and END
  sligthly distort ANGLE to a random direction
  calculate NEW_POINT drawing from HEAD in direction ANGLE with a random RADIUS
  push NEW_POINT into RIVER_PATH
```

It should (at least a little) resemble a snake trying to go from `START` to `END`
following a wobbly path.

The parts that include "randonmness" use a random gaussian function.

And the `RADIUS` average can be set using a `riverStep` variable.

Some rivers using this method can be seen right below.
From top to bottom, the `riverStep` value decreases.

{: .text-center}
![alt](/images/gen-mistakes/urban-growth/generated-rivers-examples.png)

### Resources

Splash some random coordinates and treat them as resource clusters. 

God—tier realism.

## Generating cities

Well, as I said, I just assumed they grew near the river. So I picked a random
`riverPath` point and used it as a `cityOrigin`.

A city is a n-sided polygon.

## Growing cities

This is the main and most important part. The one I actually want to build rigorously.

The first part is exploration. This way, `city` knows where it is convenient to expand,
looking for resources.

The second part is actual territorial expansion, using the results of prior exploration.

The third part is descentralization. It starts when the city passes an aerial threshold,
and it's characterized by the appearance of a satellite city.

Exploration is almost radial at first. When a resource cluster is found, exploration will
be biased towards that same direction from city. By now, the latter doesn't have any
effect on city growth, since **exploration and city expansion aren't parallel yet**.

Once the resources are maped by `city`, a `resourcesInfluence` array is built. 

It contains `location`, `closestCenter`, `distance`, `expansionInfluence`.

`location` is the `{x, y}` object of the resource.

`closestCenter` is the `{x, y}` of the closest city center or sub-center. 

`distance` is the euclidean distance between `location` and `closestCenter`.

`expansionInfluence` is the strength of the influence on the city to expand
towards that direction. 

It is calculated by squaring (1 - the normalized distance).

That way, a really big distance will result in a small number. Squaring it
will make it even smaller. 

A really close (but not the closest) distance, will be close to 1, which is
the maximum influence a resource can generate.

### How resources influence city expansion

![alt](/images/gen-mistakes/urban-growth/model-sketch.svg)