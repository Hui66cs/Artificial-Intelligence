# 02 – Search Algorithms

## Problem Formulation

A search problem is defined by:
- **State space** S – all possible states
- **Initial state** s₀
- **Actions** A(s) – actions available in state s
- **Transition model** Result(s, a) – the state after taking action a in state s
- **Goal test** – determines whether a state is a goal state
- **Path cost** – numeric cost of a path (sum of step costs)

A **solution** is a sequence of actions leading from s₀ to a goal state.

## Uninformed (Blind) Search

No information about how close a state is to the goal.

### Breadth-First Search (BFS)

- Expand shallowest unexpanded node (FIFO frontier).
- **Complete**: yes (if branching factor b is finite).
- **Optimal**: yes (if all step costs are equal).
- **Time**: O(b^d) — b = branching factor, d = solution depth.
- **Space**: O(b^d) (keeps all nodes in memory).

### Depth-First Search (DFS)

- Expand deepest unexpanded node (LIFO / stack frontier).
- **Complete**: no (may loop in infinite spaces; yes in finite acyclic graphs).
- **Optimal**: no.
- **Time**: O(b^m) — m = maximum depth.
- **Space**: O(bm) — linear in depth (major advantage over BFS).

### Uniform-Cost Search (UCS)

- Expand node with lowest path cost g(n).
- Dijkstra's algorithm applied to search trees.
- **Complete**: yes (for positive step costs).
- **Optimal**: yes.
- **Time/Space**: O(b^(1+⌊C*/ε⌋)) — C* = optimal cost, ε = min step cost.

### Depth-Limited Search & Iterative Deepening (IDDFS)

- **Depth-limited DFS**: set a cutoff depth l; complete if l ≥ d.
- **IDDFS**: repeatedly run DLS with increasing limit 0, 1, 2, …
  - Combines BFS completeness/optimality with DFS space efficiency.
  - **Time**: O(b^d) — same as BFS asymptotically.
  - **Space**: O(bd).

### Bidirectional Search

- Search forward from start and backward from goal simultaneously.
- Meet in the middle.
- **Time/Space**: O(b^(d/2)) — dramatic improvement.

## Informed (Heuristic) Search

Use a heuristic function `h(n)` — estimated cost from n to the nearest goal.

### Greedy Best-First Search

- Expand node with smallest h(n).
- **Complete**: no (can loop).
- **Optimal**: no.
- Fast in practice but can be fooled.

### A* Search

- Evaluation function `f(n) = g(n) + h(n)`
  - g(n) = cost from start to n
  - h(n) = estimated cost from n to goal
- **Complete**: yes.
- **Optimal**: yes, provided h is **admissible** (never overestimates).
- **Consistency** (monotonicity): `h(n) ≤ c(n, a, n') + h(n')` — implies admissibility.
- Space is still exponential; use IDA* or memory-bounded variants.

### IDA* (Iterative Deepening A*)

- Iterative deepening on f-cost cutoff.
- **Space**: O(d) — linear.
- Slightly more work per iteration than A* but far lower memory use.

### Heuristic Design

- **Relaxed problem**: ignore constraints to get an admissible heuristic.
- **Pattern databases**: precompute exact costs for subproblems.
- **Combining heuristics**: `h(n) = max(h₁(n), h₂(n))` is still admissible and dominates both.

#### 8-Puzzle Example

| Heuristic | Description |
|-----------|-------------|
| h₁ | Number of misplaced tiles |
| h₂ | Sum of Manhattan distances |
| h₂ dominates h₁ (h₂(n) ≥ h₁(n) ∀n) |

## Local Search

Useful when the path doesn't matter — only the goal state.

### Hill Climbing

- Move to the best neighbouring state.
- Problems: local maxima, ridges, plateaux.
- **Stochastic hill climbing**: choose randomly among uphill moves.
- **Random restarts**: repeat from random initial states.

### Simulated Annealing

- Accept worse moves with probability e^(ΔE/T), where T decreases over time.
- Guaranteed to find global optimum given slow enough cooling schedule.
- Used in VLSI design, scheduling, etc.

### Genetic Algorithms

1. Represent states as strings (chromosomes).
2. **Selection**: choose fit individuals.
3. **Crossover**: combine pairs of parents.
4. **Mutation**: random small changes.
5. Repeat until termination condition.

## Constraint Satisfaction Problems (CSPs)

- **Variables** X₁, …, Xₙ each with domain Dᵢ.
- **Constraints** specify allowable value combinations.
- **Goal**: assign values to all variables satisfying all constraints.

### Backtracking Search

- DFS with variable/value ordering heuristics:
  - **Minimum Remaining Values (MRV)**: choose variable with fewest legal values.
  - **Degree heuristic**: choose variable with most constraints on unassigned variables.
  - **Least Constraining Value (LCV)**: choose value that leaves most options for neighbours.

### Constraint Propagation

- **Arc Consistency (AC-3)**: enforce that for every value in Xᵢ's domain there exists a consistent value in Xⱼ's domain.
- Reduces search space before/during backtracking.

### Local Search for CSPs

- **Min-conflicts heuristic**: choose value that violates fewest constraints.
- Highly effective for large CSPs (e.g., n-queens).
