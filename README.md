# Gated Sparsity Implementations: Self-Pruning CNNs

This repository provides two distinct architectural approaches to automated model compression: **Sigmoid Gates** and **Hard Concrete Gates**. Both models utilize learnable gating mechanisms to prune filters during training, driven by a sparsity-inducing penalty $\lambda$.

---

## 1. Self-Pruning CNN with Sigmoid Gates
This implementation applies a continuous sigmoid-based mask to the convolutional layers. It is designed to explore how weight importance shifts as the sparsity penalty increases.

### Training & Evaluation
- **Lambda Sweep:** Evaluated across $\lambda \in \{1e-5, 1e-4, 1e-3\}$ to map the accuracy-sparsity trade-off.
- **Workflow:** - Full training pipeline with multiple epochs.
    - **Checkpointing:** Automatic saving of model states for each $\lambda$.
    - **Logging:** Comprehensive tracking of loss and sparsity metrics.
- **Results:** The final output summarizes varying levels of filter retention, demonstrating the sensitivity of the model to the regularization strength.

---

## 2. Research-Grade CNN with Hard Concrete Gates
This is a more robust implementation utilizing the **Hard Concrete (L0) distribution**. This approach allows for stochastic gating that can be pushed exactly to zero, effectively performing "hard" pruning during the forward pass.

### Training & Evaluation
- **Lambda Sweep:** Evaluated across $\lambda \in \{1e-4, 1e-3, 1e-2\}$.
- **Monitoring:** Prints real-time training progress, specifically focusing on:
    - **Epoch-wise Accuracy:** Monitoring performance degradation vs. compression.
    - **Sparsity Ratios:** Precise measurement of zeroed-out gates per layer.
- **Key Advantage:** Offers a more principled way to achieve discrete sparsity compared to standard sigmoid relaxations.

---

## Core Comparison

| Feature | Sigmoid Gates Implementation | Hard Concrete Implementation |
| :--- | :--- | :--- |
| **Gating Mechanism** | Soft Sigmoid Masks | Stochastic Hard Concrete Gates |
| **Primary Focus** | Feature Importance & Smooth Pruning | Principled Sparsity & Model Compression |
| **Lambda Sweep** | `1e-5`, `1e-4`, `1e-3` | `1e-4`, `1e-3`, `1e-2` |
| **Output Type** | Final Summary Statistics | Epoch-by-Epoch Progress Logs |

---

## Mathematical Objective
Both models optimize the following objective:

$$\mathcal{L} = \text{Error}(\mathbf{y}, \mathbf{\hat{y}}) + \lambda \cdot \sum |g|$$

Where $g$ represents the gate parameters. As $\lambda$ increases, the model is forced to deactivate more gates to minimize the total loss.
