---
layout: post
title: Clustering with Voronoi diagrams
---

[Voronoi diagrams](http://en.wikipedia.org/wiki/Voronoi_diagram) are a well-established method in computational geometry, having seen applications across most all fields in the physical sciences. We introduce the notion here and proceed toward an uncommon direction: its connection to clustering algorithms in data.

Let $X=\\{x\_1,\ldots,x\_n\\}$ be a non-empty set in a (Banach) space $A$ equipped with a specified norm $\|\cdot\|$, and $B\_{x\_i}$ a subspace of $X$ which contains $x\_i\in X$ but no other point $x\_j\in X$. A _Voronoi diagram_ is a partition of the space $A$ into $n$ cells $\{B\_{x\_1},\ldots, B\_{x\_n}\}$, such that that a point $y$ lies in the cell corresponding to the element $x\_i$ iff
$$
\|x\_i - y\| < \|x\_j - y\| \;\textrm{for all}\;i\neq j.
$$
The boundaries of each cell, or segments in a Voronoi diagram, correspond to all points in the space equidistant to the two nearest points. (More specifically, the segments are the collection of boundary points which minimize a sum of normed distances.)

![Three-dimensional Voronoi diagram as computed using Voro++](/blog/content/images/2014/Sep/voronoi.png)

This has a very natural physical intuition. Say you have a bunch of points on a map, and each point represents the location of a coffee shop; then the resulting Voronoi diagram displays the closest coffee shop for any point on the map. Use the $L\_1$ norm in particular and you'll have a measure of distance more accurately depicting the paths you can actually take in order to reach one coffee shop or another (hence why $L\_1$ is also known as the taxicab distance).

Let's alter the language in expressing Voronoi diagrams in a way slightly more fitting to computer science. The Voronoi diagram is a cut of the space $A$ into decision boundaries around each observation in a given set $X$, such that any point in $A$ can be classified by the "most closely related" one in $X$ (by choice of minimizing the normed distance). Notice in particular that the Voronoi diagram is exactly the same as the resulting decision boundaries from the nearest neighbor algorithm (1-NN). In fact, it's a natural idea to then use the algorithm involved in computing Voronoi diagrams as a way to reduce the number of expected distance calculations for $k$-means [[1]](http://www2.informatik.uni-freiburg.de/~danlee/publications/voronoi.pdf).

This latter application is interesting because we are applying a parameter-free, extensible method to help initialize a randomized one. For example, it is inherent from the minimization problem in Voronoi diagrams that one can assign weights to each point, so that the Voronoi diagram assigns more/less space based on the weight multiplied by the distance to the point.

Here's an example: not all coffee shops are equal, so say we'd be more willing to trek the distance for certain ones more than others; we may decide not to go to Starbucks unless it were very very close, or we just like Blue Bottle so much that we would disregard most of the time it would take to arrive there. To include this information into our model, we can assign a weight to each coffee shop by measure of tastiness (or whatever additional metric—possibly some nonlinear combination of an arbitrarily large number of parameters), and then use the newly reformed Voronoi diagram as one's classification system.

Further, the weights to be assigned to each observation do not necessarily need to be known. From a Bayesian perspective, we can assign these weights based on a prior distribution which reflects our own knowledge of how "important" certain points may be than others. I'm unaware of anyone taking this weighted Voronoi approach (equipped with priors) to clustering. It could be a worthy approach giving rise to new challenges and solutions, and one certainly worth investigating in more detail. (A fleeting thought is that weighted Voronoi diagrams are an equivalent formulation of clustering the data set with $p$ parameters by minimizing the normed distance.)
