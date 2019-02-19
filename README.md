# Artificial Intelligence - Search Planning

In the use of artificial intelligence, forward planning is an important tool for exploring unknown spaces and states, and using data obtained by this exploration to devise solutions to problems. This project uses a forward-planning AI agent, along with a variety of algorithms and heuristics, to search for answers to four different versions of this question: What is the best plan to deliver a number of air cargo loads from their starting airports to their destinations, flying on a limited number of planes?

![Progression air cargo search](images/Progression.PNG)

The five types of algorithms that will be tested on this problem are breadth-first search, depth-first search, uniform-cost search, greedy best-first search, and A-star search. In addition to this, for greedy best-first and A-star there are four heuristics to choose from:

- Unmet goals - The heuristic is simply to successfully reach the goals.
- Level sum - The heuristic adds together the level costs (the levels at which the goals first appear on the search graph) of the combined goal 'literals' or non-variable states.
- Max level - The heuristic returns the largest level cost of any single goal 'fluent', or state variable.
- Set level - The heuristic looks for the first level where all the goals are met, in such a way that no two goal literals are mutually exclusive in the last layer of the planning graph.

This means that for each problem, there are a total of 11 possible strategies to use. The purpose of the project is to determine which strategies can make the most effective plan to deliver the air cargo - and in this case, 'effectiveness' is determined not only by finding a good plan, but one that makes good use of available memory, time, and graph space.

### Problem 1

In the first problem, we have **2 cargos** to deliver, **2 planes** to use, and **2 airports** - JFK and SFO - to travel between, as expressed in `air_cargo_problems.py`:

```
Init(∧ Cargo(C1) ∧ Cargo(C2) 
     ∧ Plane(P1) ∧ Plane(P2)
	 ∧ Airport(JFK) ∧ Airport(SFO)
     ∧ At(C1, SFO) ∧ At(C2, JFK) 
	 ∧ At(P1, SFO) ∧ At(P2, JFK))
Goal(At(C1, JFK) ∧ At(C2, SFO))
```

In all four problems, there are three available types, or 'schema' of actions to take, which can be expressed like this:

```
Action(Load(C, P, A),
	PRECOND: At(C, A) ∧ At(P, A) ∧ Cargo(C) ∧ Plane(P) ∧ Airport(A)
	EFFECT: ¬ At(C, A) ∧ In(C, P))
Action(Unload(C, P, A),
	PRECOND: In(C, P) ∧ At(P, A) ∧ Cargo(C) ∧ Plane(P) ∧ Airport(A)
	EFFECT: At(C, A) ∧ ¬ In(C, P))
Action(Fly(P, From, To),
	PRECOND: At(P, From) ∧ Plane(P) ∧ Airport(From) ∧ Airport(To)
	EFFECT: ¬ At(P, From) ∧ At(P, To))
```

So, for example, for Plane 1 to 'Load' Cargo 1, the precondition is that Plane 1 and Cargo 1 must be both be 'at' Airport SFO. The effect of this action is that the state of Cargo 1 is changed from being 'at' Airport SFO to being 'in' Plane 1.

To initiate the hunt for a solution, we use `run_search.py`, along with the selected problem and a choice of search methods. For Problem 1, there are a total of **20 possible actions** to take. Here is one possible solution to this problem, obtained by running a uniform-cost search:

```
Load(C2, P2, JFK)
Fly(P2, JFK, SFO)
Load(C1, P2, SFO)
Unload(C2, P2, SFO)
Fly(P2, SFO, JFK)
Unload(C1, P2, JFK)
```

This output represents the 'length' of the plan for the solution the search algorithm comes up with.

These are the results of running each of the 11 search strategies:

|| Possible Actions | Expansions | Goal Tests | New Nodes | Plan Length | Time Taken (Seconds) | Optimal plan found? |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Breadth-First Search | 20 | 43 | 56 | 178 | 6 | 0.0064 | YES |
| Depth-First Search | 20 | 21 | 22 | 84 | 20 | 0.0035 | -- |
| Uniform-Cost Search | 20 | 60 | 62 | 240 | 6 | 0.001 | YES |
| Greedy Best-First (Unmet Goals) | 20 | 7 | 9 | 29 | 6 | 0.0017 | YES |
| Greedy Best-First (Level Sum) |  20 | 6 | 8 | 28 | 6 | 0.4532 | YES |
| Greedy Best-First (Max Level) | 20 | 6 | 8 | 24 | 6 | 0.3434 | YES |
| Greedy Best-First (Set Level) | 20 | 6 | 8 | 28 | 6 | 1.3161 | YES |
| A* (Unmet Goals) | 20 | 50 | 52 | 206 | 6 | 0.0091 | YES |
| A* (Level Sum) | 20 | 28 | 30 | 122 | 6 | 0.6129 | YES |
| A* (Max Level) | 20 | 43 | 45 | 180 | 6 | 0.7157 | YES |
| A* (Set Level) | 20 | 33 | 35 | 138 | 6 | 2.0111 | YES |

It looks like all of the strategies, with the exception of depth-first search, are able to come up with a plan that only requires 6 steps to complete. Some of them are considerably faster than others; uniform-cost search and greedy best-first search with the unmet goals heuristic were both able to return a solution in close to 0.001 seconds, but it took greedy best-first search and A-star search 1-2 seconds when they used the set level heuristic.

The number of new nodes that each search expands to are also highly variable. The greedy best-first search only needed to expand 24-29 nodes with every heuristic; uniform-cost search and A-star search with the unmet goals heuristic needed to expand to more than 200 new nodes.

### Problem 2

In the second problem, we expand to **3 cargos** to be delivered, **3 planes** to be flown on, and **3 airports** to travel between:

```
Init(∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3)
     ∧ Plane(P1) ∧ Plane(P2) ∧ Plane(P3)
     ∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL)
     ∧ At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) 
     ∧ At(P1, SFO) ∧ At(P2, JFK) ∧ At(P3, ATL))
Goal(At(C1, JFK) ∧ At(C2, SFO) ∧ At(C3, SFO))
```

There are a total of **72 possible actions** to take for this problem, and this adds another layer of complexity for the search algorithm to work through. Here's a possible solution that the uniform cost search delivered:

```
Load(C3, P3, ATL)
Fly(P3, ATL, SFO)
Load(C1, P3, SFO)
Load(C2, P2, JFK)
Fly(P2, JFK, SFO)
Unload(C3, P3, SFO)
Fly(P3, SFO, JFK)
Unload(C2, P2, SFO)
Unload(C1, P3, JFK)
```

These are the results that each search strategy delivered:

|| Possible Actions | Expansions | Goal Tests | New Nodes | Plan Length | Time Taken (Seconds) | Optimal plan found? |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Breadth-First Search | 72 | 3343 | 4609 | 30503 | 9 | 1.2567 | YES |
| Depth-First Search | 72 | 624 | 625 | 5602 | 619 | 1.6591 | -- |
| Uniform-Cost Search | 72 | 5154 | 5156 | 46618 | 9 | 1.9624 | YES |
| Greedy Best-First (Unmet Goals) | 72 | 17 | 19 | 170 | 9 | 0.012 | YES |
| Greedy Best-First (Level Sum) | 72 | 9 | 11 | 86 | 9 | 6.2571 | YES |
| Greedy Best-First (Max Level) | 72 | 27 | 29 | 249 | 9 | 12.011 | YES |
| Greedy Best-First (Set Level) | 72 | 9 | 11 | 84 | 9 | 16.266 | YES |
| A* (Unmet Goals) | 72 | 2467 | 2469 | 22522 | 9 | 1.4256 | YES |
| A* (Level Sum) | 72 | 357 | 359 | 3426 | 9 | 150.53 | YES |
| A* (Max Level) | 72 | 2887 | 2889 | 26594 | 9 | 844.96 | YES |
| A* (Set Level) | 72 | 1037 | 1039 | 9605 | 9 | 893.24 | YES |

Once again, every search strategy _except_ depth-first search was able to arrive at a plan with an optimal length of 9 steps. The depth-first search required a whopping 619 steps to get the cargos to their intended destinations.

The variation in the number of new nodes expanded by each strategy is even greater here - the greedy best-first search with set level only requires 84 new nodes, and the uniform-cost search requires 46,618. None of the greedy best-first search heuristics need more than 249 nodes to complete their tasks. Outside of them, the search that requires the fewest new nodes is A-star with level sum, which needs 3,456.

The greedy best-first search with unmet goals is the fastest to solve this problem, needing only 0.012 seconds to do it. In second place is breadth-first search, at 1.3 seconds, and third is A-star with unmet goals at 1.4 seconds. The A-star search with max level takes a whole 845 seconds to find a solution, and A-star search with set level takes even longer, at 893.24 seconds.

Because the depth-first search strategy only seems to turn up lengthy and impractical solutions, and because A-Star search with max level and set level heuristics both take such a long time - and therefore a lot more memory - to find a solution, it seems as if all three of these search strategies are too unweildly to employ for longer tasks of this nature. Therefore, they won't be used for the next two problems.

### Problem 3

In the third problem, we now have **4 cargos** to deliver and **4 airports** to use, but only **2 planes** to fly:

```
Init(∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3) ∧ Cargo(C4)
     ∧ Plane(P1) ∧ Plane(P2)
     ∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL) ∧ Airport(ORD)
     ∧ At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) ∧ At(C4, ORD)
     ∧ At(P1, SFO) ∧ At(P2, JFK))
Goal(At(C1, JFK) ∧ At(C2, SFO) ∧ At(C3, JFK) ∧ At(C4, SFO))
```

There are **88 possible actions** available for this problem, and in this case, a uniform-cost search can return this solution:

```
Load(C2, P2, JFK)
Fly(P2, JFK, ATL)
Load(C3, P2, ATL)
Fly(P2, ATL, ORD)
Load(C4, P2, ORD)
Fly(P2, ORD, SFO)
Load(C1, P2, SFO)
Unload(C4, P2, SFO)
Unload(C2, P2, SFO)
Fly(P2, SFO, JFK)
Unload(C3, P2, JFK)
Unload(C1, P2, JFK)
```

Each of the 8 remaining search strategies performed like this:

| | Possible Actions | Expansions | Goal Tests | New Nodes | Plan Length | Time Taken (Seconds) | Optimal plan found? |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Breadth-First Search | 88 | 14663 | 18098 | 129625 | 12 | 6.7957 | YES |
| Uniform-Cost Search | 88 | 18510 | 18512 | 161936 | 12 | 8.4817 | YES |
| Greedy Best-First (Unmet Goals) | 88 | 25 | 27 | 230 | 15 | 0.0219 | -- |
| Greedy Best-First (Level Sum) | 88 | 14 | 16 | 126 | 14 | 7.6733 | -- |
| Greedy Best-First (Max Level) | 88 | 21 | 23 | 195 | 13 | 9.8533 | -- |
| Greedy Best-First (Set Level) | 88 | 35 | 37 | 345 | 17 | 59.715 | -- |
| A* (Unmet Goals) | 88 | 7388 | 7390 | 65711 | 12 | 4.7967 | YES |
| A* (Level Sum) | 88 | 369 | 371 | 3403 | 12 | 153.07 | YES |

The performance of the search strategies diverges even more when applied to Problem 3. Only four of them find an ideal solution plan of 12 steps - breadth-first search, uniform-cost search, A-star search with unmet goals heuristic, and A-star search with level sum. 

Uniform-cost search and breadth-first search need to expand to the most new nodes out of all the strategies to meet this ideal, respectively 161,936 and 129,625. Greedy best-first search with level sum only needs 126 new node expansions to find a solution, and none of the greedy best-first search strategies require more than 345 new nodes. Even though it doesn't meet the ideal plan length, the greedy best-first search with max level doesn't perform badly - it still comes close, with a plan length of 13 steps.

The search strategy that works fastest in this case is greedy best-first with unmet goals, needing only 0.02 seconds to plan a route. The next-fastest is A-star with unmet goals, which, unlike any greedy best-first search, meets the ideal plan length while needing only 4.7 seconds to do it. On the other end of the spectrum, the greedy best-first search with set level heuristic needs a full minute, or 59.7 seconds, to find a solution; and A-star with level sum needs close to three times that much time and memory, clocking in at 153 seconds.

### Problem 4

We now come to the fourth and final problem, in which we have **2 planes**, **4 airports**, and a grand total of **5 cargos** to deliver:

```
Init(∧ Cargo(C1) ∧ Cargo(C2) ∧ Cargo(C3) ∧ Cargo(C4) ∧ Cargo(C5)
     ∧ Plane(P1) ∧ Plane(P2)
     ∧ Airport(JFK) ∧ Airport(SFO) ∧ Airport(ATL) ∧ Airport(ORD)
     ∧ At(C1, SFO) ∧ At(C2, JFK) ∧ At(C3, ATL) ∧ At(C4, ORD) ∧ At(C5, ORD)
     ∧ At(P1, SFO) ∧ At(P2, JFK))
Goal(At(C1, JFK) ∧ At(C2, SFO) ∧ At(C3, JFK) ∧ At(C4, SFO) ∧ At(C5, JFK))
```

There are **104 possible actions** to take here. This is how a uniform-cost search solved the problem:

```
Load(C2, P2, JFK)
Fly(P2, JFK, ATL)
Load(C3, P2, ATL)
Fly(P2, ATL, ORD)
Load(C4, P2, ORD)
Load(C5, P2, ORD)
Fly(P2, ORD, SFO)
Load(C1, P2, SFO)
Unload(C4, P2, SFO)
Unload(C2, P2, SFO)
Fly(P2, SFO, JFK)
Unload(C5, P2, JFK)
Unload(C3, P2, JFK)
Unload(C1, P2, JFK)
```

This is how the 8 selected search strategies performed:

| | Possible Actions | Expansions | Goal Tests | New Nodes | Plan Length | Time Taken (Seconds) | Optimal plan found? |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Breadth-First Search | 104 | 99736 | 114953 | 944130 | 14 | 56.926 | YES |
| Uniform-Cost Search | 104 | 113339 | 113341 | 1066413 | 14 | 73.997 | YES |
| Greedy Best-First (Unmet Goals) | 104 | 29 | 31 | 280 | 18 | 0.0440 | -- |
| Greedy Best-First (Level Sum) | 104 | 17 | 19 | 165 | 17 | 15.647 | -- |
| Greedy Best-First (Max Level) | 104 | 56 | 58 | 580 | 17 | 34.658 | -- |
| Greedy Best-First (Set Level) | 104 | 107 | 109 | 1164 | 23 | 267.89 | -- |
| A* (Unmet Goals) | 104 | 34330 | 34332 | 328509 | 14 | 36.925 | YES |
| A* (Level Sum) | 104 | 1208 | 1210 | 12210 | 15 | 910.24 | -- |

For this problem, only 3 of the 8 search strategies used are able to find an optimal plan of 14 steps - A-star search with level sum heuristic drops out of the top performers, and falls behind to a 15-step plan.

As for the number of new nodes expanded to meet the challenge of this problem, breadth-first search now expands to 944,130 nodes, and uniform-cost search to 1,066,413 nodes! The search that needs to expand to the fewest new nodes by far is greedy best-first search with level sum, which only requires 165 new nodes to reach a solution of with a length of 17 steps.

The fastest performer is still greedy best-first with unmet goals, which takes 0.04 seconds, but it produces a plan with a length of 18 steps. The second-fastest is greedy best-first with level sum, which takes 15.6 seconds. The third-fastest search strategy is greedy best-first with max level, which takes 34.6 seconds to make a plan with a length of 17, and the fourth-fastest is A-star with unmet goals, which takes 37 seconds to come up with an optimal plan.

### Conclusions

Now that these search strategies have been tested and analyzed, there are a few questions to consider:

**Which algorithm or algorithms would be most appropriate for planning in a very restricted domain (i.e., one that has only a few actions) and needs to operate in real time?**

Based on their results and performance speed while working on Problem 1, which has only 20 actions available, it appears that **uniform-cost search** and **greedy best-first search with unmet goals heuristic** may be the best options for coming up with quick planning solutions in a restricted domain. Both are able to find an optimal plan for delivering air cargo in close to 0.001 seconds. Even when applied to Problem 2, which has more than 3 times as many actions available, greedy best-first search with unmet goals is still able to find an optimal plan in close to 0.01 seconds, and in that situation it performs faster than uniform-cost search.

**Which algorithm or algorithms would be most appropriate for planning in very large domains (e.g., planning delivery routes for all UPS drivers in the U.S. on a given day)?**

For calculating an optimal plan in a very large domain, it looks like **A-star search with unmet goals heuristic** and **breadth-first search** are the most effective search strategies. Both are able to find delivery plans of an optimal length in the large domain of Problem 4, and both are able to do so in less than 60 seconds - no other search strategies can achieve both goals. It's reasonable to imagine that both search strategies would be able to perform well on an even larger problem, using the least time and memory of their peers.

**Which algorithm or algorithms would be most appropriate for planning problems where it is important to find only optimal plans?**

If finding an optimal plan is the main goal, then it seems that **breadth-first search**, **uniform-cost search**, and **A-star search with unmet goals heuristic** are the top performers - all three search strategies maintain the ability to make a plan with an optimal number of steps when used on all four problems.

### References

This is my implementation of Udacity's 'Classical Planning' project in the Artificial Intelligence course, which can be found [here](https://github.com/udacity/artificial-intelligence/tree/master/Projects/2_Classical%20Planning).

More information about artificial intelligence search planning and heuristics can be found in Chapter 11 of the Berkeley AIMA textbook, available [here](http://aima.cs.berkeley.edu/2nd-ed/newchap11.pdf).