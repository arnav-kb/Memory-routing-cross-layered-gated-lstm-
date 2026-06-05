# Memory-routing-cross-layered-gated-lstm

# Memory-Routed Cross-Layer LSTM (MRCL-LSTM)

## Overview

Memory-Routed Cross-Layer LSTM (MRCL-LSTM) is an experimental recurrent neural network architecture that extends the traditional stacked LSTM by introducing explicit cross-layer memory routing, learnable memory fusion, and temporal attention-based retrieval.

Traditional stacked LSTMs pass information sequentially through hidden outputs from one layer to the next. While effective, this design restricts deeper layers to indirect access to information stored in lower-layer memory states. In particular, valuable long-term information contained within lower-layer cell states may become progressively compressed or lost as it propagates through the network hierarchy.

MRCL-LSTM addresses this limitation by enabling higher layers to selectively access and integrate memory representations from lower layers through learnable gating mechanisms. The architecture preserves the autonomy of lower-layer memory states while constructing a dedicated memory integration layer that aggregates information across multiple levels of abstraction.

The resulting model combines three key ideas:

1. Cross-layer memory routing.
2. Memory fusion with preservation of native recurrent dynamics.
3. Temporal attention for retrieval of important historical information.

The architecture was developed as an exploratory investigation into whether explicit memory routing can improve long-range dependency modeling while simultaneously providing greater interpretability than conventional recurrent architectures.

---

# Motivation

Standard stacked LSTMs follow a strictly hierarchical flow:

Input → Layer 1 → Layer 2 → Layer 3 → Output

Although deeper layers receive increasingly abstract representations, they do not directly access the internal memory states of lower layers.

This creates several challenges:

* Long-range information may degrade as it moves through multiple layers.
* Cell states containing useful memory remain inaccessible to deeper layers.
* The contribution of individual layers to final predictions is difficult to interpret.
* Sparse long-range signals are difficult to preserve through recurrent compression alone.

The central hypothesis behind MRCL-LSTM is that deeper layers may benefit from direct but controlled access to lower-layer memories.

Rather than forcing information to pass exclusively through hidden outputs, the architecture learns when and how much memory should be transferred between layers.

---

# Architecture

The model consists of three recurrent layers:

Layer 1: Feature Extraction Layer

Layer 2: Intermediate Representation Layer

Layer 3: Memory Integration Layer

Unlike a conventional stacked LSTM, Layer 3 does not rely solely on Layer 2 outputs. Instead, it receives routed memory information from both Layer 1 and Layer 2 through dedicated gating mechanisms.

## Cross-Layer Memory Routing

For each timestep:

Layer 1 produces:

* Hidden state h₁
* Cell state c₁

Layer 2 produces:

* Hidden state h₂
* Cell state c₂

These states are transformed into memory proposals and routed toward Layer 3 through learnable gates:

* Hidden-state routing gates
* Cell-state routing gates

The routing process determines how much information from lower layers should contribute to Layer 3's memory representation.

---

## Layer 1 → Layer 3 Routing

Layer 1 provides fine-grained and temporally precise information.

Learned gates determine:

* Which hidden-state information should be transferred.
* Which cell-state information should be transferred.

This enables Layer 3 to directly access early-stage representations without modifying Layer 1 memory.

---

## Layer 2 → Layer 3 Routing

Layer 2 provides more abstract representations.

Dedicated routing gates regulate:

* Hidden-state transfer.
* Cell-state transfer.

These routed memories provide higher-level contextual information to Layer 3.

---

## Learnable Memory Source Weighting

Two trainable parameter vectors determine the relative importance of memory sources:

α_h
α_c

Softmax normalization converts these parameters into routing coefficients.

This allows the model to learn whether Layer 3 should rely more heavily on:

* Layer 1 memory.
* Layer 2 memory.

The weighting process is learned automatically during training.

---

## Memory Fusion Mechanism

Direct memory injection can destabilize recurrent dynamics by overwriting existing memory.

To prevent this, MRCL-LSTM introduces fusion gates.

These gates combine:

1. Existing Layer 3 memory.
2. Cross-layer routed memory.

The resulting memory representation preserves Layer 3's recurrent state while incorporating useful lower-layer information.

This design prevents memory pollution of lower layers while maintaining stable recurrent behavior.

---

# Temporal Attention

Although recurrent memory routing improves information retention, very sparse long-range dependencies remain challenging.

To address this issue, temporal attention is applied over all Layer 3 outputs.

Instead of relying exclusively on the final hidden state, attention learns to identify the most relevant timesteps for prediction.

This allows the model to:

* Retrieve important early information.
* Focus on sparse signals.
* Reduce information loss caused by recurrent compression.

Experimental observations suggest that:

* Memory routing effectively handles medium-range dependencies.
* Temporal attention primarily retrieves very early sequence information.
* The two mechanisms operate in a complementary manner.

Memory routing performs compression.

Temporal attention performs retrieval.

---

# Interpretability

A key objective of MRCL-LSTM is interpretability.

Unlike conventional recurrent architectures, the model explicitly exposes internal routing decisions.

The following quantities are recorded during inference:

## Routing Gates

* gn_o_12
* gn_h_13
* gn_c_13
* gn_h_23
* gn_c_23

These reveal:

* When information is transferred.
* Which layers contribute memory.
* Relative importance of hidden versus cell states.

---

## Fusion Gates

* fuse_gate_h3
* fuse_gate_c3

These reveal:

* When Layer 3 trusts its own memory.
* When cross-layer memory dominates.

---

## Attention Weights

Temporal attention weights provide insight into:

* Which timesteps influence predictions.
* Whether the model relies on recent or distant information.
* How retrieval behavior changes across datasets.

---

# Experimental Findings

Experiments performed across multiple sequence datasets indicate several recurring trends.

## Long-Range Dependencies

The architecture performs particularly well when:

* Dependencies span many timesteps.
* Important information remains distributed across the sequence.

Cross-layer memory routing improves information retention relative to standard stacked LSTMs.

---

## Sparse Signals

When important information is concentrated within a small number of timesteps, recurrent memory alone becomes insufficient.

Temporal attention significantly improves performance in these settings by directly retrieving relevant historical states.

---

## Gate Behavior

Visualization of routing gates suggests that:

* Gates do not collapse to trivial solutions.
* Memory routing remains active throughout training.
* Different routing pathways learn distinct roles.
* Fusion gates balance recurrent memory and injected memory rather than exclusively relying on either source.

---

# Key Contributions

1. Explicit cross-layer memory routing between recurrent layers.

2. Separate routing mechanisms for hidden states and cell states.

3. Learnable weighting of multiple memory sources.

4. Memory fusion that preserves recurrent dynamics.

5. Temporal attention for retrieval of sparse historical information.

6. Built-in interpretability through gate tracking and visualization.

---

# Limitations

Several limitations remain:

* The architecture introduces additional parameters relative to standard LSTMs.
* Performance gains depend on sequence characteristics.
* Sparse long-range signals still require attention mechanisms.
* The model has not yet been evaluated against large-scale transformer architectures.
* Extensive benchmarking across diverse datasets remains future work.

---

# Future Work

Potential future directions include:

* Bidirectional memory routing.
* Dynamic routing coefficients.
* Multi-head temporal attention.
* Application to time-series forecasting.
* Application to natural language processing tasks.
* Comparison against modern transformer and state-space architectures.
* Formal theoretical analysis of memory flow dynamics.

---

# Conclusion

MRCL-LSTM explores a different perspective on recurrent sequence modeling.

Rather than treating deeper recurrent layers solely as feature processors, the architecture treats them as memory integration layers capable of selectively aggregating information from multiple levels of abstraction.

Experimental results suggest that cross-layer memory routing improves long-range information retention, while temporal attention provides an effective retrieval mechanism for sparse historical signals.

Together, these components form an interpretable recurrent architecture that combines memory compression, memory integration, and attention-based retrieval within a unified framework.
