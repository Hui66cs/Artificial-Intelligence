# 03 – Knowledge Representation & Reasoning

## Overview

Knowledge representation is the sub-field of AI concerned with how knowledge about the world can be represented in a form that a computer system can use to solve complex tasks.

Key requirements:
- **Representational adequacy** – can express needed knowledge.
- **Inferential adequacy** – can derive new knowledge.
- **Inferential efficiency** – inference is tractable.
- **Acquisitional efficiency** – knowledge can be acquired easily.

---

## Propositional Logic

### Syntax

- **Atomic sentences**: P, Q, R, …
- **Connectives**: ¬ (not), ∧ (and), ∨ (or), ⇒ (implies), ⟺ (biconditional)
- **Sentences**: built from atoms and connectives.

### Semantics

A **model** m assigns true/false to every propositional symbol.

- `¬P` is true iff P is false.
- `P ∧ Q` is true iff both P and Q are true.
- `P ∨ Q` is true iff at least one is true.
- `P ⇒ Q` is false only when P is true and Q is false.
- `P ⟺ Q` is true iff P and Q have the same truth value.

### Entailment & Inference

- KB **entails** α (KB ⊨ α) iff α is true in every model in which KB is true.
- **Modus Ponens**: from `P ⇒ Q` and `P`, derive `Q`.
- **Resolution**: from `P ∨ Q` and `¬P ∨ R`, derive `Q ∨ R`.

### Normal Forms

- **CNF (Conjunctive Normal Form)**: conjunction of clauses (disjunctions of literals).
  - Every propositional sentence can be converted to CNF.
- **DNF (Disjunctive Normal Form)**: disjunction of conjunctions of literals.

### Complexity

- **Satisfiability (SAT)**: is there a model satisfying a set of clauses? → NP-complete.
- **Validity (tautology checking)**: is a sentence true in all models? → co-NP-complete.

---

## First-Order Logic (FOL)

### Syntax

- **Constants**: John, 2, …
- **Variables**: x, y, …
- **Predicates**: Loves(x, y), Prime(x)
- **Functions**: Father(John), +(2, 3)
- **Quantifiers**:
  - **Universal**: ∀x P(x) — P holds for all x.
  - **Existential**: ∃x P(x) — P holds for some x.

### Semantics

An **interpretation** specifies:
- The **domain** (universe of discourse).
- Mappings from constants/functions/predicates to domain objects/relations.

### Important Equivalences

| Equivalence | Formula |
|-------------|---------|
| De Morgan | ¬(P ∧ Q) ≡ ¬P ∨ ¬Q |
| Quantifier duality | ¬∀x P(x) ≡ ∃x ¬P(x) |
| Distribution | ∀x (P(x) ∧ Q(x)) ≡ (∀x P(x)) ∧ (∀x Q(x)) |

### Inference in FOL

- **Universal Instantiation**: from `∀x P(x)`, derive `P(c)` for any constant c.
- **Existential Instantiation**: from `∃x P(x)`, introduce a new constant (Skolem constant) k and derive `P(k)`.
- **Unification**: find substitution θ such that UNIFY(p, q) = θ where pθ = qθ.
- **Generalised Modus Ponens**: `p₁, …, pₙ, p₁ ∧ … ∧ pₙ ⇒ q ⊢ q`.

#### Resolution in FOL

1. Convert KB to CNF (includes Skolemisation, prenex normal form).
2. Negate the goal and add to KB.
3. Apply resolution refutation until empty clause derived → goal proved.

---

## Knowledge Engineering

**Steps:**
1. Identify the task.
2. Assemble relevant knowledge.
3. Decide on a vocabulary (predicates, functions, constants).
4. Encode general knowledge about the domain.
5. Encode a specific problem instance.
6. Pose queries and inspect solutions.

---

## Ontologies

A shared, formal specification of a conceptualisation.

- **Classes** (concepts) and **individuals** (instances).
- **Properties** (roles): object properties and data properties.
- **Axioms**: logical sentences constraining the ontology.

### OWL (Web Ontology Language)

- Based on Description Logic (DL) — a decidable fragment of FOL.
- **TBox**: terminological axioms (class definitions, property domains/ranges).
- **ABox**: assertional axioms (individual memberships).

### Reasoning Services

| Service | Description |
|---------|-------------|
| Satisfiability | Is a class non-empty in every model? |
| Subsumption | Is class C a subclass of D? |
| Classification | Compute the complete class hierarchy |
| Instantiation | Which individuals belong to a class? |

---

## Non-Monotonic Reasoning & Uncertainty

Classical logic is **monotonic**: adding axioms can only increase what is provable.

Real-world reasoning requires:
- **Default reasoning**: assume P unless there is evidence to the contrary.
- **Closed World Assumption (CWA)**: if P cannot be proved, assume ¬P.
- **Belief revision**: retract beliefs in light of new evidence.

### Probability as a Representation of Uncertainty

- Full joint probability distribution over all variables.
- **Conditional probability**: P(A|B) = P(A ∧ B) / P(B).
- **Bayes' theorem**: P(H|E) = P(E|H) · P(H) / P(E).

See also: [Machine Learning notes](04_machine_learning.md) for Bayesian networks.
