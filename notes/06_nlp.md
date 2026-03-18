# 06 – Natural Language Processing

## Overview

Natural Language Processing (NLP) is the branch of AI concerned with enabling computers to understand, generate, and reason about human language.

**Core challenges:**
- **Ambiguity**: "I saw the man with a telescope" (who has the telescope?)
- **Context dependence**: "it" can refer to many things.
- **World knowledge**: "The trophy didn't fit in the suitcase because it was too big."
- **Diversity**: thousands of languages; informal text (typos, slang, emojis).

---

## Text Pre-processing

| Step | Description |
|------|-------------|
| Tokenisation | Split text into tokens (words, sub-words, characters) |
| Lowercasing | Reduce vocabulary size |
| Stop-word removal | Remove high-frequency, low-information words (the, is, …) |
| Stemming | Reduce words to base form (running → run) — heuristic |
| Lemmatisation | Morphologically correct base form (better → good) |
| Sentence splitting | Break document into sentences |

---

## Text Representation

### Bag of Words (BoW)

- Represent document as vector of word counts (ignore order).
- Vocabulary size V → vector ∈ ℝᵛ.
- Sparse and high-dimensional.

### TF-IDF

- **Term Frequency**: TF(t, d) = count(t in d) / |d|
- **Inverse Document Frequency**: IDF(t) = log(N / df(t)) where N = total docs, df(t) = docs containing t.
- TF-IDF(t, d) = TF(t, d) × IDF(t)
- Down-weights common words; up-weights rare but informative words.

### N-gram Language Models

- Model probability of a word given previous n−1 words.
- **Bigram**: P(wₙ | wₙ₋₁), **Trigram**: P(wₙ | wₙ₋₂, wₙ₋₁).
- **Smoothing**: Laplace, Kneser-Ney to handle unseen n-grams.
- **Perplexity**: PP(W) = P(W)^(−1/N) — lower is better.

### Word Embeddings

Represent words as dense low-dimensional vectors; semantically similar words are close.

| Model | Approach | Year |
|-------|----------|------|
| Word2Vec (CBOW) | Predict word from context | 2013 |
| Word2Vec (Skip-gram) | Predict context from word | 2013 |
| GloVe | Factorise word co-occurrence matrix | 2014 |
| FastText | Sub-word embeddings; handles OOV | 2016 |
| ELMo | Contextualised embeddings from bi-LSTM | 2018 |
| BERT / GPT | Deep contextualised embeddings | 2018/2019 |

#### Word2Vec (Skip-gram)

- Objective: maximise P(context words | centre word).
- Use negative sampling for efficiency.
- Captures analogies: king − man + woman ≈ queen.

---

## Core NLP Tasks

### Part-of-Speech (POS) Tagging

- Assign grammatical tag (NN, VB, JJ, …) to each token.
- Methods: HMM (Viterbi), CRF, neural sequence labeller.

### Named Entity Recognition (NER)

- Identify and classify named entities: person, location, organisation, date, …
- BIO tagging scheme: B-PER, I-PER, O.
- State-of-the-art: fine-tuned BERT.

### Parsing

- **Constituency parsing**: hierarchical phrase-structure tree (S → NP VP).
- **Dependency parsing**: directed graph of grammatical relations (nsubj, dobj, …).
- Algorithms: CYK (constituency), Eisner (dependency), neural models.

### Sentiment Analysis

- **Document-level**: positive / negative / neutral.
- **Aspect-level**: sentiment towards specific aspects.
- Methods: lexicon-based, ML classifiers, fine-tuned LLMs.

### Machine Translation

- **Statistical MT**: noisy-channel model P(e|f) ∝ P(f|e) P(e).
- **Neural MT (seq2seq)**: encoder–decoder with attention.
- **Transformer-based**: current state of the art (Google Translate, DeepL).
- Evaluation: **BLEU** score — measures n-gram overlap with reference translations.

### Question Answering (QA)

| Type | Description |
|------|-------------|
| Extractive | Locate answer span in a passage (SQuAD benchmark) |
| Abstractive | Generate answer in own words |
| Open-domain | Retrieve relevant document first, then answer |
| Knowledge-base | Query a structured KB (SPARQL) |

### Summarisation

- **Extractive**: select and concatenate important sentences.
- **Abstractive**: generate new sentences that capture the key ideas.
- Evaluation: **ROUGE** score — recall-oriented n-gram overlap.

### Coreference Resolution

- Identify all expressions that refer to the same entity.
- Example: "Alice said she was tired." (she = Alice)

---

## Modern NLP: Pre-trained Language Models

### BERT (Bidirectional Encoder Representations from Transformers)

- Pre-trained on **Masked Language Model (MLM)** + **Next Sentence Prediction (NSP)**.
- Bi-directional — reads left and right context simultaneously.
- Fine-tune on downstream tasks with a task-specific head.
- Variants: RoBERTa (no NSP, more data), ALBERT (parameter sharing), DistilBERT (distillation).

### GPT (Generative Pre-trained Transformer)

- Auto-regressive language model: predict next token.
- Left-to-right attention only (causal mask).
- GPT-3 onwards: in-context few-shot learning.

### Fine-tuning Strategies

| Strategy | Description |
|----------|-------------|
| Full fine-tuning | Update all parameters on task data |
| Feature extraction | Freeze backbone; train only task head |
| Adapter layers | Insert small trainable layers; freeze backbone |
| LoRA | Low-rank decomposition of weight updates; parameter-efficient |
| Prompt tuning | Learn soft prompt tokens; freeze model |

---

## Evaluation

| Metric | Used for |
|--------|---------|
| Accuracy / F1 | Classification tasks (NER, POS, QA) |
| BLEU | Machine translation |
| ROUGE | Summarisation |
| Perplexity | Language model quality |
| BERTScore | Contextual similarity for generation tasks |

---

## Ethical Considerations in NLP

- **Bias**: models trained on internet text inherit societal biases.
- **Hallucination**: LLMs can generate plausible but factually incorrect text.
- **Toxicity**: models can generate harmful content.
- **Privacy**: personal information in training data.
- **Environmental cost**: large model training consumes significant energy.
