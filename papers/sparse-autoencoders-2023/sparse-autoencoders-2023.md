---
title: Sparse Autoencoders Find Highly Interpretable Features in Language Models
authors: Cunningham et al.
year: 2023
venue: 2023
keywords:
  - sparse autoencoders
  - interpretable features
  - mechanistic interpretability
  - superposition
status: draft
---

# Sparse Autoencoders Find Highly Interpretable Features in Language Models

## 1. Problem: why are neurons uninterpretable?

Earlier mechanistic interpretability assumed:

\[
\text{Neuron} = \text{Feature}
\]

But many neurons are polysemantic, showing mixtures like:

- French
- parentheses
- drugs
- math

This phenomenon is called polysemanticity.

The authors argue the cause is not a "bad neuron" but superposition:

\[
N_{\text{feature}} > N_{\text{dimension}}
\]

So the model must compress:

\[
\text{Many Features} \rightarrow \text{Few Dimensions}
\]

Multiple features share a direction, creating superposition.

The core hypothesis is:

```text
polysemantic neuron = projection(superposed features)
```

---

## 2. Idea: learn the true feature basis

Let residual activation be:

\[
x \in \mathbb{R}^d
\]

True features are:

\[
g_1, g_2, \dots, g_n
\]

We want:

\[
x = \sum_i a_i g_i
\]

with sparse coefficients \(a_i\):

most \(a_i = 0\)

Only a few features fire for a given activation.

The task becomes finding a dictionary of features, analogous to sparse coding.

---

## 3. Method: Sparse Autoencoder

The authors train a sparse autoencoder over model activations.

Encoder:

\[
c = \text{ReLU}(Mx + b)
\]

Decoder:

\[
\hat x = M^T c
\]

Here \(c\) is not a hidden state but a feature activation vector.

That is, \(c\) represents interpretable features.

---

## 4. SAE loss

The objective reconstructs activations while encouraging sparsity:

\[
L(x) = \|x - \hat x\|_2^2 + \alpha \|c\|_1
\]

Reconstruction loss:

\[
\|x - \hat x\|^2
\]

Sparsity loss:

\[
\alpha \|c\|_1
\]

As \(\alpha\) increases, features become sparser but reconstruction degrades.

The paper observes a smooth tradeoff with no single best \(\alpha\).

---

## 5. What do SAE features look like?

Many learned SAE features are nearly monosemantic.

Examples include features that activate for:

- apostrophe `'
- contractions like `don't`, `won't`, `wouldn't`
- the token `If` in code context
- parentheses `(` or `)`

These features are far more interpretable than raw neurons.

---

## 6. Autointerpretability

The authors use GPT to annotate features automatically.

Process:

1. inspect top-activating text for a feature
2. ask GPT to describe the feature, e.g. "legal terms"
3. have GPT predict activation on other text
4. compute correlation as an interpretability score

Higher score means the feature is more monosemantic.

---

## 7. SAE beats PCA/ICA on interpretability

The paper compares:

- raw neurons
- random directions
- PCA
- ICA
- SAE

SAE wins by a wide margin on interpretability metrics.

Figure 2 shows SAE features are more interpretable than PCA components and neurons.

The core conclusion is:

```text
SAE features > PCA components > neurons
```

---

## 8. SAE enables causal analysis

The authors study IOI-style tasks like:

```text
Alice gave snack to ...
```

and whether the model predicts Bob or Alice.

They perform activation patching by replacing feature activations:

\[
c_i \rightarrow \bar c_i
\]

and observe output changes.

A small number of SAE features can steer behavior more precisely than PCA.

---

## 9. A surprising finding

Modifying a few SAE features can change the final model prediction.

This shows the features are causally real,

not just statistical artifacts.

That is:

```text
feature -> computation -> output
```

which makes SAE especially important.

---

## 10. Circuit discovery

The authors also trace the source of a feature.

For example, the ')' feature has upstream features like:

- dates
- acronyms
- phrases

This forms a cross-layer causal tree:

```text
feature -> feature -> feature -> token
```

beginning to reveal a Transformer circuit.

---

## 11. Limitations

The authors acknowledge SAE is not perfect.

Reconstruction error remains.

Replacing residual activation increases perplexity from 25 to 40,

showing information loss.

Also, MLP layers are hard to model and many features are dead.

---

## 12. What did this paper change?

Previously:

```text
Neuron = feature
```

Now:

```text
Feature \neq neuron
Feature = sparse direction
Neuron = mixture(feature)
```

This is one of the biggest shifts in mechanistic interpretability.

The later Anthropic line of work builds on this path:

Toy Models of Superposition
\rightarrow SAE
\rightarrow Monosemantic Features
\rightarrow Feature Steering
\rightarrow Circuit Discovery
\rightarrow Model Editing

---

## 13. One-sentence summary

This paper shows that the real computational units in Transformers may be sparse features in activation space rather than individual neurons; SAE can disentangle those features from superposition and recover interpretable, controllable, causally meaningful internal representations.

This is why many current LLM interpretability efforts start with SAE.
