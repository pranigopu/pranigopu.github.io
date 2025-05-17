<h1>Multi-Agent Path Planning</h1>

---

**Contents**:

- [Problem statement](#problem-statement)
- [Implementation approach](#implementation-approach)
  - [1. Establish test environment](#1-establish-test-environment)
  - [2. Separate item-handling and path planning](#2-separate-item-handling-and-path-planning)
  - [3. Choose the most extensible object-oriented approach](#3-choose-the-most-extensible-object-oriented-approach)
- [Algorithm choices](#algorithm-choices)
- [Practical testing](#practical-testing)

---

# Problem statement
In essential terms, we are dealing with a **search problem** with 3 essential capabilities given to the searching agent: (1) detect obstacles/collisions from a given position, (2) search and obtain possible movement positions, (3) move to a possible movement position. What we are seeking is generalisable solution for cooperative path planning, i.e. generalisable logic to handle dynamic obstacles (i.e. other agents) within path planning. If we can implement and test this solution in a simpler environment while ensuring that the solution's logic relies only on essentials and not on implementation-specific details (e.g. the specific method of obstacle detection and agent movement), we can generalise this logic for any grid-based environment and navigation.

**NOTE**: _Agent dimension and orientation are subsumed within obstacle detection, since these factors are key to detecting collisions with other bounding boxes in the environment (dynamic or static)._

# Implementation approach
## 1. Establish test environment
Establish a simple environment to learn about and test the cooperative path planning solution (so as to focus on resolving the complexities of cooperative path planning, and to ensure that the core solution can be easily presented, explained and generalised).

## 2. Separate item-handling and path planning
Keep item-handling separate from path planning. Why? Positions to pick and place items register simply as navigational waypoints to the path planner. Furthermore, to optimise item-handling (especially item-picking and putaway), we can either obtain heuristics or some path planning algorithm to obtain true distances, so as to figure out and minimise total distances travelled; even here, once the destinations are decided, all that remains is cooperative path planning to reach these destinations, all the while accounting for time taken to perform item-handling operations at the destinations (which can simply be inputs to the path planning algorithm, e.g. wait/pause time).

As for algorithms that plan which agents should handle which items in what way (so as to optimise overall operations), based on agent positions, capabilities and item placements, I am not sure what the best solution structure is. However, even if it requires integrated methods to handle both item-handling and path planning, it is still useful to implement and test path planning separately before integrating it (to come to terms with the complexities within path planning itself). Moreover, as far as I can project, the same essentials that apply for path planning on its own also apply to such integrated methods, namely (1) obstacle detection and collision avoidance, (2) possible movement position search, and (3) optimal navigation from position to position. As of now, I have no clear reason to integrate item-handling and path planning, and I have ample reasons to keep the solution modular (e.g. wide applicability in a variety of potential use-cases, and also, the broader coding practice-related reasons, e.g. separation of concerns). Also, the modular approach has more use-cases and offers better demonstrability, especially in the earlier and intermediate stages of development.

## 3. Choose the most extensible object-oriented approach
The goal is to implement cooperative path planning. In this, the agents must cooperate, but by what means? I see 2 options, broadly: (1) shared data, with planning logic distributed to each agent, and (2) an integrated "agent manager" object which manages agent coordination and gives the necessary path planning results to each agent (by necessary, I mean the information needed by the agent to move along the intended path; this may omit everything except the waypoints and wait times).

Ultimately, both approaches must achieve cooperative path planning, which means both approaches must be equivalent in terms of the outputs of the simulation (provided that the same constraints and planning algorithms are applied). However, the choice of approach could have significant consequences in practice; I say this for the following reasons:

A.<br>
(1) offers a decentralised approach with intelligent (and thus more computationally intensive) agents and frequent inter-agent communication, and (2) offers a centralised approach with an intelligent planner and unintelligent agents (thus, overall, less computationally intensive) with minimal communication between agents and more of a client-server system.

B.<br>
However, (1) makes agents more adaptable and less dependent on a central system and less reliant on a centralised communication network, thereby also offering more redundancy, since failure in one system (i.e. one agent) leaves the others intact and capable, and potentially, capable of compensating for the failure. On the other hand, (2) relies on a robust centralised infrastructure, and failure in either the central system or centralised communication network would impair the operation of the entire fleet of agents.

_Hence, the approach significantly shapes challenges and opportunities._

---

At this point, I am not sure which approach is better in practice, or whether the approach must change based on the use-case. Here, since both (1) and (2) are equivalent in terms of simulation output, and since it is not clear which approach should be prioritised, the key question becomes: what is the most extensible and adaptible solution?

To answer this, consider: (2) allows agent classes to be much simpler. Furthermore, the agent manager class will still have to associate agents to agent-specific data (i.e. the agent's movement across time) and logic (i.e. the agent's reactions and interactions with other agents); in my judgement, this association can be translated to (1) if necessary.

Secondly, both (1) and (2) involve an equal (or at least similar) amount of data consolidation for the path planner: agent positions and movements across time, how the agents must coordinate their movements with respect to other agents across time, what are the other agents' dimensions and speeds, how the other agents would react the an agent's decisions across time, etc. Hence, even to implement an intelligent agent approach, it makes sense to implement (2) and, if necessary, simply give access to the agent manager object (or its duplicates) to each agent, with minor modifications to ensure the agent executes its own decisions and not some other agent's.

---

Hence, in conclusion, solution (2) is viable for both:

- Centralised solution
- Decentralised solution

However, solution (1) is less straightforward for centralisation.

# Algorithm choices
- [Cooperative A\*](https://pranigopu.github.io/autonomous-navigation/cooperative-a-star.html)

# Practical testing
> [`autonomousNavigation`/`implementation`/`path_planning`, owned by `pranigopu`, GitHub](https://github.com/pranigopu/autonomousNavigation/tree/main/implementation/path_planning) <br> *Directory containing practical implementations*