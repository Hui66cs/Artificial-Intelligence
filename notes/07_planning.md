# 07 – AI Planning

## Overview

AI Planning deals with automatically generating a sequence of actions that leads from an initial state to a goal state.

**Differences from search:**
- Planning uses a **factored** representation (state = set of variable assignments) rather than atomic states.
- Actions are described generically (not enumerated for every state).
- Planning algorithms exploit this structure for efficiency.

---

## Classical Planning

### Assumptions

- **Fully observable**: agent knows the complete current state.
- **Deterministic**: actions have known, predictable effects.
- **Static**: only the agent changes the world.
- **Discrete**: finite, enumerable states and actions.
- **Sequential**: one action at a time.

### STRIPS Representation

Each action is described by:
- **Preconditions**: literals that must be true for the action to be applicable.
- **Effects** (Add list + Delete list): literals added/deleted from state.

**Example — "Move block A to table":**
```
Action: Move(A, Table)
  Pre: On(A, B), Clear(A), Clear(Table)
  Add: On(A, Table), Clear(B)
  Del: On(A, B), Clear(Table)
```

### PDDL (Planning Domain Definition Language)

- Standard input language for planners.
- **Domain file**: defines types, predicates, and actions.
- **Problem file**: defines objects, initial state, and goal.

```pddl
(:action move-block
 :parameters (?b ?from ?to)
 :precondition (and (on ?b ?from) (clear ?b) (clear ?to))
 :effect (and (on ?b ?to) (clear ?from)
              (not (on ?b ?from)) (not (clear ?to))))
```

---

## Planning Algorithms

### Forward State-Space Search

- Start from initial state; apply applicable actions; reach goal.
- Use heuristics to guide search (heuristic planning).

### Backward (Regression) Search

- Start from goal; work backwards to find preconditions.
- Each step finds actions whose effects match (part of) the current goal.
- Smaller branching factor in some domains.

### Plan-Space Search

- Search through the space of partial plans.
- **Partial-Order Planning (POP)**:
  - Maintain set of steps and ordering constraints.
  - Resolve **threats** (actions that could negate preconditions of other actions) by inserting ordering constraints or causal links.
  - Produces least-commitment plans.

---

## Heuristics for Planning

### Delete-Relaxation Heuristic (h⁺)

- Ignore delete effects of all actions.
- Optimal cost in relaxed problem is a lower bound → admissible.
- Compute with **relaxed graphplan** (polynomial).

### Hadd / Hmax

- **h_max**: max cost over all sub-goals (admissible but not very informative).
- **h_add**: sum of costs of all sub-goals (inadmissible but informative).

### FF Heuristic

- Extract plan from relaxed planning graph, use plan length as heuristic.
- Used in FastForward (FF) planner — very effective in practice.

---

## Planning Graphs (Graphplan)

A **planning graph** is a levelled directed graph:

- Alternating **proposition layers** (Pᵢ) and **action layers** (Aᵢ).
- **Mutual exclusion (mutex)** relations: two propositions/actions that cannot both hold/occur.

### Algorithm

1. Expand planning graph until goal propositions appear at some Pᵢ with no mutex.
2. Search backward through graph for a valid plan.
3. If backward search fails, expand further or declare failure.

---

## Temporal and Resource Planning

Classical planning assumes actions are instantaneous and free. Extensions:

| Feature | Description |
|---------|-------------|
| **Durative actions** | Actions have start/end conditions and invariants over their duration |
| **Resources** | Consumable (fuel) or reusable (crew member); capacity constraints |
| **Concurrency** | Multiple non-conflicting actions can overlap |

**Temporal planning** requires reasoning about time points and intervals.

---

## Planning Under Uncertainty

### MDP (Markov Decision Process)

- States S, actions A, transition function P(s'|s,a), reward R(s,a).
- Goal: find policy π* that maximises expected cumulative reward.
- **Value Iteration**: repeatedly update V(s) ← max_a Σ P(s'|s,a)[R(s,a,s') + γV(s')]
- **Policy Iteration**: alternate policy evaluation and policy improvement.

### Partially Observable MDP (POMDP)

- Agent cannot directly observe state; receives an observation o with P(o|s,a).
- State is replaced by a **belief state** b (probability distribution over states).
- Belief update: b'(s') ∝ P(o|s',a) Σ P(s'|s,a) b(s)
- Exact solution is intractable; use point-based methods (PBVI, SARSOP).

### Conformant and Contingent Planning

- **Conformant**: find a plan that works regardless of initial state (no observations).
- **Contingent**: branch on observation outcomes during execution.

---

## Multi-Agent Planning

| Concept | Description |
|---------|-------------|
| **Cooperative planning** | Agents share a common goal; coordinate to avoid conflicts |
| **Adversarial planning** | Opponent tries to prevent achieving the goal |
| **Game theory** | Models strategic interactions; Nash equilibria |

---

## Classical Planners

| Planner | Approach |
|---------|---------|
| Graphplan | Planning graphs + backward extraction |
| SATPLAN | Encode planning problem as SAT instance |
| FF (FastForward) | Heuristic forward search with FF heuristic |
| LPG | Local search on planning graphs |
| LAMA | Landmark-based heuristics |
| Fast Downward | Translator + heuristic search (SAS+ representation) |

---

## Real-World Applications

- **Logistics and scheduling**: warehouse robots, airline scheduling, delivery routes.
- **Space mission planning**: NASA's Remote Agent, Mars Rover.
- **Game AI**: strategy games, puzzle solvers.
- **Workflow management**: business process automation.
- **Autonomous vehicles**: mission and task planning layers.
