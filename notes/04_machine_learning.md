# 04 – Machine Learning

## Overview

Machine Learning (ML) is a sub-field of AI that gives computers the ability to learn from data without being explicitly programmed for every task.

**Three main paradigms:**

| Paradigm | Training signal | Examples |
|----------|-----------------|---------|
| Supervised learning | Labelled input–output pairs | Classification, regression |
| Unsupervised learning | Unlabelled data | Clustering, dimensionality reduction |
| Reinforcement learning | Reward signal from environment | Game playing, robotics |

---

## Key Concepts

### Hypothesis Space and Bias

- **Hypothesis space** H: set of all functions the learner can represent.
- **Inductive bias**: assumptions made to generalise beyond training data.
  - Without bias, no generalisation is possible (No Free Lunch theorem).

### Overfitting and Underfitting

- **Overfitting**: model too complex; fits noise in training data → poor generalisation.
- **Underfitting**: model too simple; cannot capture true pattern.
- **Bias–variance trade-off**: error = bias² + variance + irreducible noise.

### Training / Validation / Test Split

- **Training set**: used to fit model parameters.
- **Validation set**: used for hyperparameter tuning and model selection.
- **Test set**: held out; used only to estimate true generalisation error.
- **k-fold cross-validation**: rotate training/validation k times; average error.

---

## Supervised Learning

### Linear Regression

- Model: ŷ = θᵀx + b
- Loss: Mean Squared Error (MSE) = (1/n) Σ(yᵢ − ŷᵢ)²
- Closed-form solution: θ = (XᵀX)⁻¹Xᵀy (normal equations)
- **Ridge regression** (L2): adds λ||θ||₂² penalty → shrinks weights.
- **Lasso** (L1): adds λ||θ||₁ penalty → sparse weights (feature selection).

### Logistic Regression

- Binary classification: P(y=1|x) = σ(θᵀx) where σ(z) = 1/(1+e⁻ᶻ) (sigmoid).
- Trained with **cross-entropy loss** (log-loss) and gradient descent.
- Multi-class: softmax function.

### Gradient Descent

- Update rule: θ ← θ − α ∇L(θ)
- Variants:
  - **Batch GD**: use all training examples per update (slow but stable).
  - **Stochastic GD (SGD)**: one example per update (noisy but fast).
  - **Mini-batch GD**: small batch per update (balances both).

### Decision Trees

- Greedily split on the feature that maximises information gain.
- **Information Gain** = H(parent) − Σ(weighted child entropy)
- **Gini impurity**: 1 − Σ pᵢ² (alternative to entropy).
- Prone to overfitting → prune or use ensembles.

### Ensemble Methods

| Method | Idea |
|--------|------|
| **Bagging** | Train multiple models on bootstrap samples; average predictions |
| **Random Forests** | Bagging + random feature subset at each split |
| **Boosting (AdaBoost)** | Sequentially train weak learners; reweight misclassified examples |
| **Gradient Boosting (GBM, XGBoost)** | Fit each tree to residuals of previous ensemble |

### Support Vector Machines (SVMs)

- Find the hyperplane that maximises the **margin** between classes.
- **Hard-margin SVM**: assumes linearly separable data.
- **Soft-margin SVM**: allows misclassifications with penalty C.
- **Kernel trick**: map data to higher-dimensional space implicitly.
  - Common kernels: linear, polynomial, RBF (Gaussian).

### k-Nearest Neighbours (k-NN)

- Non-parametric: no training phase; store all examples.
- Classify by majority vote among k nearest training examples.
- Sensitive to irrelevant features; affected by the curse of dimensionality.

---

## Unsupervised Learning

### k-Means Clustering

1. Initialise k cluster centroids randomly.
2. Assign each point to nearest centroid.
3. Recompute centroids as cluster means.
4. Repeat until convergence.

- Objective: minimise within-cluster sum of squared distances.
- Sensitive to initialisation → use **k-means++**.

### Principal Component Analysis (PCA)

- Find directions (principal components) of maximum variance.
- Steps:
  1. Standardise data.
  2. Compute covariance matrix Σ = (1/n) XᵀX.
  3. Eigen-decompose Σ → eigenvectors (PCs) and eigenvalues (variance).
  4. Project data onto top k eigenvectors.
- Used for dimensionality reduction and visualisation.

---

## Probabilistic Models

### Naive Bayes Classifier

- Assumes features are conditionally independent given class.
- P(y|x₁, …, xₙ) ∝ P(y) Π P(xᵢ|y)
- Fast, works well with text classification.

### Bayesian Networks

- DAG where nodes = random variables, edges = direct dependencies.
- Joint distribution: P(X₁, …, Xₙ) = Π P(Xᵢ | Parents(Xᵢ))
- **Inference**: variable elimination, belief propagation.
- **Learning**: parameter learning (MLE/Bayesian) + structure learning.

### Hidden Markov Models (HMMs)

- Sequence model: latent state sequence generates observed sequence.
- **Forward algorithm**: compute P(observations | model).
- **Viterbi algorithm**: find most likely state sequence.
- **Baum-Welch (EM)**: learn model parameters from unlabelled sequences.

---

## Evaluation Metrics

### Classification

| Metric | Formula |
|--------|---------|
| Accuracy | (TP + TN) / (TP + TN + FP + FN) |
| Precision | TP / (TP + FP) |
| Recall (Sensitivity) | TP / (TP + FN) |
| F1 Score | 2 · Precision · Recall / (Precision + Recall) |
| ROC-AUC | Area under the ROC curve |

### Regression

| Metric | Formula |
|--------|---------|
| MAE | (1/n) Σ \|yᵢ − ŷᵢ\| |
| MSE | (1/n) Σ (yᵢ − ŷᵢ)² |
| RMSE | √MSE |
| R² | 1 − SS_res / SS_tot |

---

## Reinforcement Learning (Introduction)

- **Agent** takes actions in an **environment**; receives **reward** signal.
- Goal: learn a **policy** π: S → A that maximises cumulative discounted reward.
- Key concepts:
  - **State** s, **Action** a, **Reward** r, **Next state** s'.
  - **Return** Gₜ = Σ γᵏ rₜ₊ₖ₊₁ (γ ∈ [0,1] discount factor).
  - **Value function** V^π(s) = E[Gₜ | sₜ = s, π].
  - **Q-function** Q^π(s,a) = E[Gₜ | sₜ = s, aₜ = a, π].
- **Bellman equation**: V(s) = Σₐ π(a|s) Σₛ' P(s'|s,a) [R(s,a,s') + γV(s')]
- **Q-learning**: off-policy TD learning; converges to optimal Q*.
- **Deep Q-Network (DQN)**: approximate Q* with a neural network.
