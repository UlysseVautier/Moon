---
layout: post
title: "Dubins Traveling Salesman Problem"
date: 2018-04-27
excerpt: "On the Dubins Traveling Salesman Problem for Area Scanning"
tags: [Dubins, Traveling Salesman Problem, TSP, DTSP]
comments: true
---

## Overview

The Travelling Salesman Problem (**TSP**) origins are unclear but was mathematically formulated by W.R. Hamilton in the 1800's.
The problem is formulated as the following :  "Given a list of cities and the distances between each pair of cities, what is the shortest possible route that visits each city and returns to the origin city?".  
While the problem is easy to understand, the absolute solution to this problem has yet to be found.
It is a [NP-Hard](https://en.wikipedia.org/wiki/NP-hardness) problem and the record set to this date includes **7,512,218,268** cities and was established by the [Concorde TSP](http://www.math.uwaterloo.ca/tsp/world/) solver.

The problem can be solved approximately or exactly. While the approximate algorithms often don't give the most optimal solution, they are useful because of the time it takes to compute the solution.
At the moment the LKH algorithm is the best approximate algorithm and the Concorde is the best exact algorithm.

The first published approximate methods trying to find the solution are from [\[Flood, Merrill L. (1956)\]](https://doi.org/10.1287/opre.4.1.61) and [\[Croes,G.A. (1958)\]](https://doi.org/10.1287/opre.6.6.791).
They had the idea of the 2-Opt algorithm : deleting two edges and reconnecting them in the other possible way from an existing Hamiltonian Tour.
Since then the problem has caught the interest of all mathematicians and computer scientist all over the world.  
While a lot of work had been done for over a decade and a half, the most known approximate algorithm used from 1973 to about 1989 came from [\[Lin, S.; Kernighan, B. W. (1973)]\](https://doi.org/10.1287/opre.21.2.498) called the Lin-Kernighan algorithm.
This algorithm is uses the K-Opt algorithm based on the 2-Opt algorithm, and other works.  
This champion was then improved by [\[Helsgaun, K. (2000)\]](https://www.sciencedirect.com/science/article/pii/S0377221799002842) and has still been improved a little to be the most popular one used today, known as the LKH (Lin-Kernighan Helsgaun) solver.

Concerning exact algorithms, the simplest algorithm is to build all the possible Hamiltonian tours and calculate the shortest one. This has a factorial complexity which is obviously not the best method.
The first researchers to optimize the algorithms are [\[Held, M.; Karp, R. (1962)\]](https://epubs.siam.org/doi/abs/10.1137/0110015?journalCode=smjmap.1) by using dynamic programming. Exact algorithms have then been found to decrease the calculation time.  
The Branch-and-Bound algorithm proposed by [\[Land, A. H.; Doig, A. G. (1960)\]](https://www.jstor.org/stable/1910129) was used, the Cutting-Plane method by [\[Dantzig, G.; Fulkerson, R.; Johnson, S. (1954)\]](https://pubsonline.informs.org/doi/abs/10.1287/opre.2.4.393) or the Branch-and-Cut from [\[Padberg, M.; Rinaldi, G. (1991)\]](https://epubs.siam.org/doi/10.1137/1033004).
All of which try to solve the **ILP** (Integer linear programming) problem or try to minimize the number of Hamiltonian Tour to search.  
The Concorde TSP Solver which holds today the world record was written by David Applegate, Robert E. Bixby, Vašek Chvátal, and William J. Cook and contains all the different exact algorithms for the **TSP** problem.

## On the Dubins Travelling Salesman Problem

The **TSP** has forked into different branches of more specific problems such as the Travelling Purchaser Problem (**TPP**), where a shopping list and shops are added to the **TSP** problem 
or the Sequential ordering problem (**SOP**), another **TSP** with additional constraints.  
Some notable problems in robotics are the Travelling Salesman Problem with Neighbourhood (**TSPN**), where instead of visiting a point, you need to visit an area, or the Travelling Salesman Problem with Constrained Manoeuvres (**TSP-CM**) where the manoeuvres at each waypoints are specific.
Since the number of different existing **TSP** problems, the most natural one - visiting the cities on the planet - have been named the Euclidean Travelling Salesman Problem (**ETSP**) since it's based in the euclidean distance between the cities, and finding the shortest tour in distance.

The Dubins Travelling Salesman Problem (**DTSP**) is one of those branches of our interest. The problem is still the same, travelling to all available waypoints only once, but this time by using a Dubins vehicle.
A Dubins Vehicle is vehicle modeled as follows :

$$
\left\{
\begin{array}{c}
\dot{x} = Vcos(\theta) \\
\dot{y} = Vsin(\theta) \\
\dot{\theta} = \frac{V}{\rho}u 
\end{array}
\right.
$$

Where \\(u\\) is the input control, \\(V\\) the velocity of the vehicle and \\(\rho\\) the turn-radius of the vehicle. The [Dubins vehicle](https://en.wikipedia.org/wiki/Dubins_path) represents a non-holonomic vehicle such as cars, planes or boats.
The problem of the **DTSP** is to take into account this difficulties in manoeuvres and still have the shortest route.

Important work has been done in this subject by giving these algorithms to solve it :
* The Alternating Algorithm (**AA**) and Bead-Tiling Algorithm (**BTA**)
* Look-Ahead Algorithm (**LAA**)
* The Randomized Heading Algorithm (**RHA**)
* The Genetic Algorithm (**GA**)
* The Discretized Look-Ahead Algorithm (**DLAA**)

The first algorithm useful to this problem was given by [\[Savla, K.; Frazzoli, E.; Bullo, F. (2005)\]](https://ieeexplore.ieee.org/abstract/document/1470055/) with the Alternating Algorithm (**AA**) by solving first the (**ESTP**) with the known algorithms explained above, then changing all odd-edges by a Dubins Path. They also introduced the Bead-Tiling Algorithm (**BTA**) in this same paper.
The Look-Ahead Algorithm (**LAA**) came shortly after with [\[Xiang Ma, D. A.; Castanon, D. A. (2006)\]](https://ieeexplore.ieee.org/abstract/document/4177966/) 
by looking at three waypoints ahead for each waypoints to prepare for a best Dubins Path and rebuilding the path this way.  
[\[Le Ny, J.; Feron, E.; Frazzoli, E. (2012)\]](https://ieeexplore.ieee.org/abstract/document/6004813/) introduced the Randomized Heading Algorithm (**RHA**) by using the 2-Opt concept explained above and randomizing the heading at each waypoints for the Dubins Path.
By using the Genetic Algorithm, [\[Xin Yu, J. Y.; Hung, J. Y. (2012)\]](https://ieeexplore.ieee.org/abstract/document/6237270/) have found a way to solve this problem by selecting and mutating a population in a specific way.  
Finally, last to date is the Discretized Look-Ahead Algorithm (**DLAA**) from [\[Cohen, I.; Epstein, C.; Isaiah, P.; Kuzi, S.; Shima, T.\]](https://ieeexplore.ieee.org/abstract/document/7572046/) by discreticizing the heading at each waypoints and using the same principle as the (**LAA**).

Some of these algorithms can be arranged in two different categories :
* Ones that solve the **ETSP** and modify the already best Hamiltonian tour (**AA**, **LAA**, **DLAA**)
* Ones that solve directly the **DTSP** without solving the **ETSP** (**BTA**, **RHA**, **GA**)

The problem of solving first the **ETSP** is that in a dense area of waypoints (where the minimum turn-radius is too big) the modifications done to the best path is more cost-effective, producing a bad **DTSP** tour in most cases.
