<h1>COOPERATIVE A* (CA*)</h1>

---

**Contents**:

- [Motivation for CA\*](#motivation-for-ca)
- [Motivation for understanding CA\* conceptually](#motivation-for-understanding-ca-conceptually)
- [Overview of CA\*](#overview-of-ca)
- [Key considerations in implementing CA\*](#key-considerations-in-implementing-ca)
  - [Search space definition accounting for time](#search-space-definition-accounting-for-time)
  - [Allowing for waiting and backtracking](#allowing-for-waiting-and-backtracking)
  - [Limiting wait time](#limiting-wait-time)
  - [Stopping search](#stopping-search)
  - [Accounting for collisions](#accounting-for-collisions)
- [Technical considerations in implementing CA\*](#technical-considerations-in-implementing-ca)
  - [Implementing reservation table](#implementing-reservation-table)
  - [Standardising speed representation using integer time steps](#standardising-speed-representation-using-integer-time-steps)
  - [Starting with equal speed assumption](#starting-with-equal-speed-assumption)
- [Fixed-priority equal speed CA\*](#fixed-priority-equal-speed-ca)
  - [Motivation for starting with fixed priority equal-speed CA\*](#motivation-for-starting-with-fixed-priority-equal-speed-ca)
- [Windowed CA\* (WCA\*)](#windowed-ca-wca)
- [Practical testing](#practical-testing)

---

# Motivation for CA\*
> **Key reference**: ["The Problem with A\*", _Cooperative Pathfinding_ by David Silver, **CourseWare Wiki**](https://cw.fel.cvut.cz/b211/_media/courses/b3m33mkr/coop-path-aiwisdom.pdf)

---

A\* is an optimal, relatively simple and (as of now) sufficiently well-performing algorithm for single-agent pathfinding; moreover, due to the modularity of the code, we can easily switch the pathfinding algorithms used in the future, if need be, as long as we follow the same standard format for path representation. Due to this, my first attempt at multi-agent path planning will be to implement the simplest extension of A\* into a multi-agent context, namely cooperative A\*. Due to its basis in A\*, it should be relatively easy to understand, implement and test, and due to its optimal nature, it should produce promising results to present for the next minimum viable solution (MVS). Moreover, it introduces (in practical terms) multi-agent concepts such as consideration of other agent paths into an agent's own path planning, priority-based planning and fixed-window projection of future coordination (since cooperative path planning would be an ongoing process in a warehouse simulation context). Hence, in short, my first step toward multi-agent path planning is CA\* due to the following reasons:

- Relative simplicity (conceptually and technically)
    - Allows for quick and promising deliverable
    - Is potentially viable in practice with optimisations
    - Is a theoretically optimal and complete solution
- Introduces transferrable practical understanding
    - Consolidation and processing of data from multiple agents <br> _Improves understanding of necessary data structures, object-oriented approaches, etc._
    - Continual but efficient multi-agent processing <br> _Since simulations may run for long durations_ <br> **Examples**: _Windowed multi-agent processing, fixed-depth path optimisation, etc._

# Motivation for understanding CA\* conceptually
A conceptual solution:

- Integrates a range of specific contexts; thus, is generalisable
- Enables the recognition of essentials and broader purposes
    - Keeps focus simple when implementing solutions
    - Allows us to set purposeful and effective goals

---

The same applies for the conceptual solution for CA\*. In particular, the conceptual solution for CA\* allows us to see what is the essential functionality that makes CA\* effective, and what is the essential structure of the algorithm that can then be understood in and translated to implementations. This is important, especially when implementing less optimised versions of CA\*, because grasping the essentials also helps us see the fundamental limitations and potential extensions of the core logic itself, thereby leading to a clearer grasp of a more optimised implementation.

# Overview of CA\*
_Consider a grid environment divided into equally-sized cells._

At each time step, each agent $A_i$ reserves a position for the next $k_i$ time steps. The agent with the highest priority reserves its position(s) without taking other agents into account for these $k$ time steps. At any given time step, agents with lower priorities search for open neighbours in the next time step (open neighbours are cells allowing movement and avoiding collisions with static obstacles and other agents); static obstacles are avoided by referring to the grid environment itself, and agents with higher priorities are avoided by referring to the reservation table. Each agent reserves its positions before agents with lower priorities, while accounting for agents with higher priorities.

Now, I have mentioned $k_i$ time steps for an agent $A_i$. Here, $k_i$ can be interpreted based on the implementation. In an implementation that reassigns agent priorities after a fixed window of time steps, $k_i$ is equal for all $i$ and represents the window size. In an implementation that fixes priorities at the start and does not change them, $k_i$ is the number of time steps $A_i$ needs to complete its path.

_The algorithm for CA\* is otherwise very similar to A\*._

# Key considerations in implementing CA\*
## Search space definition accounting for time
Unlike A\* (which is a single-agent algorithm), a node in the algorithm's search space is not merely a position but a position _with_ a time stamp (i.e. a value indicating the number of time steps that have passed since some starting point). Hence, a position explored at a later time stamp is distinct from the same position explored at an earlier time stamp. This has some significant implications in the nature of the algorithm's progression:

- Re-expanding a previously explored nodes involves:
    - Moving to another path in space (like A\*)
    - Shifting to another (past or future) time stamp
- Due to the inclusion of time stamps, a position can be:
    - Validly revisited/backtracked to
    - Maintained across time stamps (e.g. the agent can "wait")

## Allowing for waiting and backtracking
It is important to allow the agent to backtrack to previous positions at later time stamps, or to allow the agent to wait at the same position across time stamps, in order to accommodate/give way to dynamic obstacles (e.g. other agents) without moving around unnecessarily. However, to this end, it is important to identify an obstacle as static or dynamic; if it is possible to stay in the same position across time stamps when a static obstacle is encountered, an agent may simply freeze if neighbouring cells are further away from the goal, but the ability to stay in the same position across time stamps when a dynamic obstacle is encountered can prevent unnecessary movement, since a dynamic obstacle can be expected to give way later and let the agent continue in an efficient path.

## Limiting wait time
Even though the ability to wait for dynamic obstacles to pass is valuable, it may be necessary to introduce a limit to how much an agent can wait, so that, in case a dynamic obstacle (e.g. another agent) stays in one place for an unexpectedly long duration (e.g. in a realistic use-case, due to a vehicle breakdown or time-consuming work at a location), the agent is not held in place for an indefinite or unnecessary amount of time.

## Stopping search
Since every time stamp makes the same position a part of a new node in the algorithm's search space, the algorithm on its own cannot realise whether the positions have been searched exhaustively enough to conclude if no path can be found. Some ways to mitigate this are: (1) limit the number of time steps for which the agent is allowed to search for a path, (2) run a parallel logic that checks for positions irrespective of time, or (3) run A\* at the start to ensure that the goal is reachable (omitting dynamic obstacles).

## Accounting for collisions
A position cannot be considered to be not open (i.e. possible to move into, even if it is free in the next time stamp) if either (1) a dynamic obstacle in the current time stamp swaps positions with the agent in the current cell in the next time stamp, or (2) if the vector of a dynamic obstacle across the current and next time stamps and the vector of the agent in the current cell across the current and next time stamp intersect (this would be a "cross-over" and a likely collision in reality). Realistically, both cases would lead to collisions, despite the positions being "free" in the next time stamp. This is harder to figure out of agents can move across different number of cells in a single time step; however, we can standardise the algorithm to make sure an agent moves at most one cell in one time step (see: [Standardising speed representation using integer time steps](#standardising-speed-representation-using-integer-time-steps)).

# Technical considerations in implementing CA\*
## Implementing reservation table
A data structure that informs which position is reserved by which agent at a given time stamp (if at all). This a crucial data structure when planning while accounting for the paths of dynamic, such as other agents. A simple implementation of a reservation is as follows (using a Python dictionary as the data structure):

- **Key**: A tuple in the form `(row index, column index, time stamp)`
- **Item**: The index/ID of the reserving agent

## Standardising speed representation using integer time steps
**Preference for using integer time steps**:

- Simpler to interpret and implement in code
- Can be standardised for any use-case, as shall be shown below
- Integer time stamps have lesser memory overhead <br> _Note that time stamps are to be stored for each position in_...
    - *Reservation table*
    - *Path planner search graph data storage*

---

**Extensiblity of standardisation using integer time steps**:

**NOTE**: *LCM stands for "least common multiple".*

Let the speeds of the agents $A_1, A_2 ... A_n$ (measured in cells per time step, i.e. cells / $\Delta t$) be $\frac{a_1}{\Delta t}, \frac{a_2}{\Delta t} ... \frac{a_n}{\Delta t}$ respectively. First, let us ensure that the numerators are whole numbers; to do this, multiply each of $a_1, a_2 ... a_n$ with a constant $b$. Such a constant exists as long as $a_1, a_2 ... a_n$ are rational. More precisely, if:

 $a_i = \frac{p_i}{q_i} \text{ } \forall i \in \set{1, 2 ... n}$, then:
 
 $b = LCM(q_1, q_2 ... q_n)$

**KEY POINT**: *Each of* $a_1, a_2 ... a_n$ *must be a rational number.*

---

Now, let us define $b_i$ such that:

$b \cdot \frac{a_i}{\Delta t} = \frac{b_i}{\Delta t} \text{(where } b_i \in ℕ \text{)} \text{ } \forall i \in \set{1, 2 ... n}$

Thus, multiplying each speed with $b$, we get $\frac{b_1}{\Delta t}, \frac{b_2}{\Delta t} ... \frac{b_n}{\Delta t}$. These values may not be the exact speeds of the agents, but they have the same ratio between each other as the corresponding agent speeds have between each other (hence, these values tell us about relative speeds, i.e. about how much faster is one agent compared to another; this information allows us to scale the speed appropriately when rendering the simulation). Now, note that:

$\frac{b_i}{\Delta t} = \frac{1}{\Delta t / b_i} \text{ } \forall i \in \set{1, 2 ... n}$

Now, let $c = LCM(b_1, b_2 ... b_n)$; thus, $c / b_i = c_i \in ℕ$. Hence:

$\frac{1}{c} \cdot \frac{1 }{\Delta t / b_i} = \frac{1}{(c / b_i) \Delta t} = \frac{1}{c_i \Delta t} \text{ } \forall i \in \set{1, 2 ... n}$

---

Now, note that:

- $\frac{1}{c_1 \Delta t}, \frac{1}{c_2 \Delta t} ... \frac{1}{c_n \Delta t}$ also tell us about relative speeds
- $\frac{1}{c_i \Delta t}$ can be read as: stay in 1 cell for $c_i$ time steps

This method allows us to duplicate agent positions across time steps in a structured and integer-oriented manner. By doing so, we conveniently and accurately represent relative speeds without requiring fractional time steps. Making sure time steps are integers while accurately conveying relative speeds is valuable because we can simplify and standardise the logic of the implementation while making sure it can be applied in a real-life use-case. Moreover, relative speed is sufficient for the cooperative path planning logic, since what such a planner needs is information about where the other agents would be or move in relation to a given agent with every iteration; the exact time-scale of the iteration is irrelevant. Furthermore, for rendering the simulation (either in real-time or in scaled up/down simulated time), we can simply scale up/down the simulator's time steps. To put it briefly, the unit of measurement is irrelevant for the path planner, and the desired unit can be applied by simply scaling (i.e. constant multiplication).

## Starting with equal speed assumption
Starting with the assumption that all agents have equal speeds helps us focus on cooperation-related complexities before moving on to synchronisation-related complexities. Moreover, a solution with this assumption is an extensible starting point, since unequal speeds can be established by duplicating an agent's position across time steps in the reservation table. This aspect is expanded upon in ["Standardising speed representation using integer time steps" in this document](#standardising-speed-representation-using-integer-time-steps).

# Fixed-priority equal speed CA\*
## Motivation for starting with fixed priority equal-speed CA\*
Firstly, note that equal speed assumption is justified in ["Starting with equal speed assumption" in this document](#starting-with-equal-speed-assumption). Now, fixed-priority CA\* is one where each agent is given a fixed precedence in reserving positions during pathfinding; the 1st agent simply pathfinds using A\*, the 2nd agent pathfinds while considering the 1st agent's movements, the 3rd agent pathfinds while considering the 1st and 2nd agents' movements, etc. This is the simplest cooperative A\* approach to implement, because a complete reservation table can be built simply by sequentially processing agents as per their priorities.

---

**Extensibility: Why start with fixed-priority CA\*?**

Once we have acquired the ability to build a reservation table as per agent priorities, we can apply this ability to build reservation tables that extend for only a fixed period of time (i.e. for a fixed window); this opens the door to windowed CA\* (WCA\*), which can (1) apply CA\* within an indefinite/long-term simulation, and (2) reprioritise agents with every window, thus potentially overcoming bottlenecks or deadlocks caused by a certain prioritisation, because the effectiveness of CA\*'s solution depends on the prioritisation of the agents, i.e. the order in which agent positions are reserved. Take the following example...

---

<details>
<summary><b>Demonstrating agent reservation order sensitivity</b></summary>
<p>

<pre>
Start state:
.  .  X  X  X  
B  1  .  2  A  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  .  
</pre>

Here:

<ul>
    <li><code>1</code> denotes agent 1</li>
    <li><code>3</code> denotes agent 2</li>
    <li><code>A</code> marks the goal of agent 1</li>
    <li><code>B</code> marks the goal of agent 2</li>
    <li><code>X</code> denotes an obstacle cell</li>
    <li><code>.</code> denotes a free space cell</li>
</ul>

<b>CASE 1: Agent 1 has priority of reservation</b>:
<br>
<i>Agent 1 thus plans its path to A without considering agent 2.</i>

<pre>
1 reserves the next spot; 2 waits:
.  .  X  X  X  
B  .  1  2  A  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  .

1 reserves the next spot; 2 moves back:
.  .  X  X  X  
B  .  1  .  2  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  . 

Deadlock:
.  .  X  X  X  
B  .  .  1  2  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  . 
</pre>

<b>CASE 2: Agent 2 has priority of reservation</b>:
<br>
<i>Agent 2 thus plans its path to A without considering agent 1.</i>

<pre>
2 reserves the next spot; 1 waits:
.  .  X  X  X  
B  1  2  .  A  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  .

2 reserves the next spot; 1 moves back:
.  1  X  X  X  
B  .  2  .  A  
.  X  .  X  X 
.  .  .  .  .  
.  .  .  .  . 

The next steps: 1 waits until 2 moves to B, then navigates to A.
</pre>
</p>
</details>

---

The above example demonstrates how prioritisation can significantly impact the effectiveness (and success) of multi-agent path planning. Fixed-priority CA\* can lead to unresolvable deadlocks, whereas a more flexible, dynamic prioritisation can resolve such deadlocks by simply changing the prioritisation (although, note that it is still not guaranteed that the new prioritisation would work, and if poorly designed, the reprioritisation mechanism may result in endless cycles; imagine, in the above example, if we kept alternatively prioritising agents 1 and 2).

That being said, fixed-priority CA\* gives the technical foundations on which more optimised/well-designed algorithms can be built. By first implementing a robust reservation system and understanding its limitations, we can then introduce mechanisms such as cycle detection, adaptive heuristics (?), or local conflict resolution to enhance prioritisation strategies. This makes fixed-priority CA\* both a relatively simple and highly extensible starting point.

---

In this version of CA\*, each agent has a fixed priority even before the algorithm runs. The 1st agent pathfinds without considering any other agent, the 2nd agent pathfinds while considering only the 1st agent, the 3rd agent pathfinds while considering only the 1st and 2nd agents, etc. Under the equal speed assumption, all agents move at the same rate; specifically, for simplicity, all agents cover at most one cell per time step. Since there are no additional speed adjustments (e.g. artificial waiting to balance speed differences), agents move every time step unless they choose to wait when obstructed by a dynamic obstacle.

# Windowed CA\* (WCA\*)
> **Key reference**: [*Cooperative Pathfinding (Academic Paper)* by David Silver](https://dl.acm.org/doi/10.5555/3022473.3022494)

---

Fixed-priority CA\* has a number of issues:

- Certain agent reservation orders may have no solutions
- Agents "disappear" once they reach their goals
    - Hence, inactive agents cannot be accounted for\*
    - Agent paths may be unequal, making this issue relevant
- It is not applicable in an indefinite/long simulation
    - There is no clear repeatable pathfinding process
    - Time-coordination is not straightforward <br> _E.g. if a lower priority reaches its goal sooner, when and how does it move to its next path?_

\* *We can account for higher priority inactive agents by marking them as obstacles in the environment grid; however, this is inadequate, since lower priority inactive agents are still ignored, i.e. their positions are treated as free space by higher priority agents; hence, the rendered simulation would still show a number of agents passing through the positions of inactive agents.*

---

To address these issues, we can instead prioritise and _cooperatively_ pathfind for a fixed number of time steps, i.e. a fixed window of time steps, before reprioritising. Here, an agent follows its destinations or waits at a destination however it needs to, but its steps are partitioned into a fixed window of time steps for which it contributes to the reservation table as per its assigned priority for the current window. Within each window, it _cooperatively_ pathfinds to its destination(s) for up to a fixed number of time steps, which means up to a fixed search depth (new destination(s) may be considered if the agent has reached one destination within the window and needs to reroute to its next destination); this means that an agent may not find and move according to its full optimal _cooperative_ path to its destination. This approach has some tradeoffs:

- The entire _cooperative_ optimal path may not be found
- Thus, partial paths may be suboptimal in a cooperative context <br> _Because future reroutes for more optimal cooperative paths may be ignored_

**NOTE**: *The word "cooperative" is emphasised, because, logically, we only need to limit the cooperative search due to window-based reprioritisation; however, in order to orient itself effectively, an agent may still pathfind non-cooperatively (i.e. using regular A\*) beyond the window. This is further discussed below.*

---

Quoting from [David Silver's *Cooperative Pathfinding (Academic Paper)*](https://dl.acm.org/doi/10.5555/3022473.3022494):

*To ensure that the agent heads in the correct direction, only the cooperative search depth is limited to a fixed depth, whilst the abstract search is executed to full depth. A window of size w can be viewed as an intermediate abstraction that is equivalent to the base level state space for w steps, and then equivalent to the abstract level state space for the remainder of the search. In other words, other agents are only considered for w steps (via the reservation table) and are ignored for the remainder of the search.*

In other words, during pathfinding, the agent considers other agents for only $w$ time steps ($w$ being the window size), after which it pathfinds as if there are no agents (i.e. in the "abtract search space", which is simply the grid without any other agents). Hence, the agent pathfinds cooperatively for $w$ time steps, and non-cooperatively for the remainder of the time steps until it finds the path to its goal.

**NOTE**: *Finding and following the full cooperatively optimised path based on the current prioritisation is not geared to be effective, since the current prioritisation is likely to change in the next window before the agent reaches its destination.*

---

<details>
<summary><b>SIDE NOTE: Connection to hierarchical cooperative A* (HCA*)</b></summary>
<p>
In the same referenced paper, the following is given:
<br><br>
<i>To search this new search space efficiently, a simple trick can be used. Once w steps have elapsed, agents are ignored and the search space becomes identical to the abstract search space. This means that the abstract distance provides the same information as completing the search. For each node Ni reached after w steps a special terminal edge is introduced, going directly from Ni to the destination G, with a cost equal to the abstract distance from Ni to G. Using this trick, the search is reduced to a w-step window using the abstract distance heuristic introduced for HCA*.</i>
<br><br>
I shall explain this for clarity (based on my own reading of the paper and broader understanding), and because it may be potentially useful to apply HCA* and windowed HCA* (WHCA*) in the future (for reasons I shall make clear now). HCA* is just like CA* but with a more sophisticated heuristic, namely the abstract distance heuristic, which is the exact grid distance between a given node and the goal, not accounting for any agents (the paper discusses RRA* as a method to efficiently calculate this heuristic on demand, and the effectiveness of this heuristic is also explored, but I shall not go into it right now, since I am not yet implementing HCA* or WHCA*). If this heuristic is available, then for WCA* (which then becomes WHCA* due to the use of this heuristic), the abstract (i.e. non-cooperative) search that must be done after the window of w time steps is redundant, since nodes in the cooperative part of the agent's would already have been expanded based on what would be most effective considering an optimal path found by abstract search (i.e. A* search without considering agents).
<br><br>
Hence, WCA* would only need to pathfind cooperatively for w time steps using the abstract distance heuristic to achieve the same effectiveness in orienting itself toward the goal. In other words, if we use the abstract distance heuristic in WHCA*, then after the cooperative window, the non-cooperative search is unnecessary because the cooperative steps already used a strong heuristic. Since WHCA* improves orientation toward the goal without extra non-cooperative search, it may be a worthwhile alternative to WCA* in scenarios requiring more scalability or heuristic-driven efficiency.
</p>
</details>

---

WCA\* is an improvement on fixed-priority CA\* for 3 reasons:

<details>
<summary><b>1. Indefinite repeatability</b></summary>
<p>
It provides a structured and repeatable process that can be extended to any current or future configuration of agents and destinations.
</p>
</details>

<details>
<summary><b>2. Simple but crucial extension of fixed-priority CA*</b></summary>
<p>
It builds on fixed-priority CA* while adding the feature of indefinite repeatability, which is a required feature for an indefinite/long-term simulation. Thus, it is a practical next step in progressing toward a minimum viable solution (MVS) that manages cooperative pathfinding within a warehouse simulation.
</p>
</details>

<details>
<summary><b>3. Conflict-resolution and robustness to deadlocks</b></summary>
<p>
While it may produce suboptimal results, it is a more robust solution than fixed-priority CA* as it prevents unresolvable agent reservation orders from remaining unresolved over time; essentially, <b><i>WCA* sacrifices some optimality for repeatability and adaptability in dynamic environments</i></b>.
</p>
</details>

---

Hence, in practice, due to its ability to (1) extend indefinitely across time, (2) provide a sufficiently optimised approach in practice (especially in an environment with a relatively simple arrangement of static obstacles, e.g. a warehouse) and (3) provide a robust yet simple system for managing conflicts and avoiding deadlocks, WCA* offers a structured, repeatable and adaptable alternative to fixed-priority CA\*, making it a promising step toward scalable multi-agent pathfinding.

---

**Reprioritisation methods to consider**:

- **Randomised**: <br> Simple but lacks predictability
- **Round-Robin**: <br> Ensures fairness but may not account for agent workload
- **Dynamic Load Balancing**: <br> More complex but optimizes agent efficiency over time

---

**Considerations**:

- Effect of window size
- (*Related to the above point*) Effect of replanning frequency

# Practical testing
- [`autonomousNavigation`/`implementation`/`path_planning`, owned by `pranigopu`, GitHub](https://github.com/pranigopu/autonomousNavigation/tree/main/implementation/path_planning) <br> *Directory containing practical implementations*