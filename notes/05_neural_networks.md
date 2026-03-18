# 05 – Neural Networks & Deep Learning

## Biological Inspiration

The human brain contains ~86 billion neurons connected via synapses. Artificial neural networks (ANNs) loosely model this with:
- **Nodes (neurons)**: compute a weighted sum of inputs and apply an activation function.
- **Edges (synapses)**: weighted connections between neurons.

---

## The Perceptron

Simplest neuron model:

```
output = sign(w · x + b)
```

- **Perceptron learning rule**: update weights for misclassified examples.
- **Limitation**: can only learn linearly separable functions (XOR problem → Minsky & Papert 1969).

---

## Feedforward Neural Networks (MLPs)

### Architecture

- **Input layer** → one or more **hidden layers** → **output layer**.
- Each layer l computes: **a**⁽ˡ⁾ = f(W⁽ˡ⁾ **a**⁽ˡ⁻¹⁾ + **b**⁽ˡ⁾)

### Activation Functions

| Function | Formula | Range | Notes |
|----------|---------|-------|-------|
| Sigmoid | σ(z) = 1/(1+e⁻ᶻ) | (0, 1) | Vanishing gradient for large \|z\| |
| Tanh | tanh(z) | (−1, 1) | Zero-centred; still saturates |
| ReLU | max(0, z) | [0, ∞) | Most widely used; dying ReLU problem |
| Leaky ReLU | max(αz, z) α≪1 | (−∞, ∞) | Fixes dying ReLU |
| Softmax | eᶻⁱ / Σ eᶻʲ | (0,1), sum=1 | Output layer for multi-class classification |

### Universal Approximation Theorem

A feedforward network with a single hidden layer and a non-linear activation can approximate any continuous function on a compact set to arbitrary precision — given enough neurons.

---

## Backpropagation

**Algorithm for computing gradients** via the chain rule:

1. **Forward pass**: compute activations and loss L.
2. **Backward pass**: propagate error gradients from output to input.
   - δ⁽ˡ⁾ = (W⁽ˡ⁺¹⁾)ᵀ δ⁽ˡ⁺¹⁾ ⊙ f'(z⁽ˡ⁾)
   - ∂L/∂W⁽ˡ⁾ = δ⁽ˡ⁾ (a⁽ˡ⁻¹⁾)ᵀ

### Common Loss Functions

| Task | Loss |
|------|------|
| Binary classification | Binary cross-entropy: −[y log ŷ + (1−y) log(1−ŷ)] |
| Multi-class classification | Categorical cross-entropy: −Σ yᵢ log ŷᵢ |
| Regression | Mean Squared Error: (y − ŷ)² |

---

## Training Deep Networks

### Optimisers

| Optimiser | Key idea |
|-----------|---------|
| SGD | θ ← θ − α∇L |
| SGD + Momentum | Accumulate velocity in gradient direction |
| RMSProp | Adaptive learning rates per parameter (divide by running avg of gradient²) |
| Adam | Combines Momentum + RMSProp; widely used default |

### Regularisation

| Technique | Description |
|-----------|-------------|
| L2 weight decay | Add λ||w||² to loss |
| Dropout | Randomly zero activations during training (rate p) |
| Batch Normalisation | Normalise layer inputs; accelerates training |
| Early Stopping | Halt training when validation loss stops improving |
| Data Augmentation | Artificially expand training set (flips, crops, noise) |

### Initialisation

- **Xavier / Glorot**: Var(w) = 2/(nᵢₙ + nₒᵤₜ) — good for tanh/sigmoid.
- **He / Kaiming**: Var(w) = 2/nᵢₙ — good for ReLU.

---

## Convolutional Neural Networks (CNNs)

Designed for grid-structured data (images, audio spectrograms).

### Key Operations

| Layer | Description |
|-------|-------------|
| Convolution | Slide a filter (kernel) over input; compute dot product at each position |
| Pooling (max/avg) | Downsample feature maps; provides translation invariance |
| Fully connected | Standard MLP layer at the end |

### Properties

- **Parameter sharing**: same filter weights applied across all positions → fewer parameters.
- **Local connectivity**: each neuron connects only to a local receptive field.

### Landmark Architectures

| Model | Year | Innovation |
|-------|------|-----------|
| LeNet-5 | 1998 | First successful CNN for digit recognition |
| AlexNet | 2012 | Deep CNN, ReLU, dropout, ImageNet victory |
| VGG | 2014 | Very deep with 3×3 convolutions |
| GoogLeNet/Inception | 2014 | Inception modules, 1×1 convolutions |
| ResNet | 2015 | Skip connections → train 100+ layer networks |
| EfficientNet | 2019 | Compound scaling of depth/width/resolution |

---

## Recurrent Neural Networks (RNNs)

For sequential data (text, time series, speech).

- Hidden state hₜ = f(W_hh hₜ₋₁ + W_xh xₜ + b)
- Output: yₜ = g(W_hy hₜ + bᵧ)

### Vanishing / Exploding Gradients

- Gradients shrink (or explode) exponentially over long sequences.
- **Solutions**: gradient clipping (exploding), LSTM/GRU (vanishing).

### LSTM (Long Short-Term Memory)

- Cell state **c**ₜ carries long-range information.
- **Gates**:
  - **Forget gate** fₜ = σ(W_f [hₜ₋₁, xₜ] + b_f) — what to discard from cell.
  - **Input gate** iₜ = σ(W_i [hₜ₋₁, xₜ] + b_i) — what new info to store.
  - **Output gate** oₜ = σ(W_o [hₜ₋₁, xₜ] + b_o) — what to output.
- cₜ = fₜ ⊙ cₜ₋₁ + iₜ ⊙ tanh(W_c [hₜ₋₁, xₜ] + b_c)
- hₜ = oₜ ⊙ tanh(cₜ)

### GRU (Gated Recurrent Unit)

- Simpler than LSTM; merges cell and hidden state.
- Two gates: reset gate and update gate.
- Comparable performance to LSTM with fewer parameters.

---

## Attention & Transformers

### Attention Mechanism

- Allow the model to focus on different parts of the input when producing each output.
- **Scaled dot-product attention**:
  - Attention(Q, K, V) = softmax(QKᵀ / √d_k) V
  - Q = queries, K = keys, V = values.

### Transformer Architecture (Vaswani et al. 2017)

- **No recurrence** — fully attention-based.
- **Multi-head self-attention**: run attention h times in parallel with different projections.
- **Positional encoding**: add sinusoidal embeddings to preserve sequence order.
- **Components**: Encoder stack + Decoder stack (for seq2seq tasks).

### Large Language Models (LLMs)

| Model | Organisation | Year | Parameters |
|-------|-------------|------|-----------|
| GPT-2 | OpenAI | 2019 | 1.5B |
| BERT | Google | 2018 | 340M |
| GPT-3 | OpenAI | 2020 | 175B |
| PaLM | Google | 2022 | 540B |
| LLaMA | Meta | 2023 | 7B–65B |
| GPT-4 | OpenAI | 2023 | ~1T (est.) |

- **Pre-training**: predict next token (causal LM) or masked tokens (BERT).
- **Fine-tuning**: adapt to downstream task with smaller labelled dataset.
- **Prompt engineering / in-context learning**: provide examples in the prompt.
- **RLHF**: Reinforcement Learning from Human Feedback for alignment.

---

## Generative Models

### Autoencoders (AE)

- Encoder maps x → z (latent code); decoder maps z → x̂.
- Trained to minimise reconstruction loss.

### Variational Autoencoders (VAE)

- Encoder outputs mean μ and log-variance log σ² of latent distribution.
- Reparameterisation trick: z = μ + σ ⊙ ε, ε ~ N(0, I).
- Loss = reconstruction loss + KL divergence (regularisation).

### Generative Adversarial Networks (GANs)

- **Generator** G: z → x̂ (create fake samples).
- **Discriminator** D: x → P(real) (distinguish real from fake).
- Minimax game: min_G max_D E[log D(x)] + E[log(1 − D(G(z)))]
- Applications: image synthesis, data augmentation, style transfer.

### Diffusion Models

- Add Gaussian noise to data over T steps (forward process).
- Learn to reverse the process (denoise) step by step.
- State-of-the-art for image generation (DALL-E 2, Stable Diffusion, Imagen).
