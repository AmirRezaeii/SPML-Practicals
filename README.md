# Security and Privacy in Machine Learning — Practical Assignments

This repository contains practical homework assignments from the **Security and Privacy in Machine Learning (SPML)** course. Each notebook implements and analyzes a core attack or defense technique from the field from crafting imperceptible adversarial perturbations and stealing model weights, to injecting backdoors and training with formal privacy guarantees.

All notebooks are written in Python with PyTorch and are designed to run on **Google Colab** with a free GPU runtime.

---

## Notebook Descriptions

### SVD — `SVD.ipynb`

An introduction to **Singular Value Decomposition (SVD)** as a tool for low-rank image compression. The notebook decomposes a grayscale image matrix into its U, Σ, and Vᵀ components and then reconstructs it using only the top k singular values, for varying values of k. For each rank, the approximation error is computed both numerically (Frobenius and spectral norms) and visually. The notebook demonstrates the fundamental trade-off between compression ratio and image quality, and confirms that the rank-k truncation gives the best possible rank-k approximation in the spectral norm sense — connecting the practical experiment directly to the Eckart–Young theorem.

---

### Gaussian Filter — `Gaussian_Filter.ipynb`

A practical study of **Gaussian kernel convolution** for signal and image smoothing. The notebook generates a noisy 1D signal by adding Gaussian noise to a clean reference, then applies Gaussian filters with two different standard deviations (σ values) and measures the smoothing error against the clean signal. A frequency-domain analysis via FFT is included to explain why Gaussian filtering works: as a low-pass filter, it attenuates high-frequency noise while preserving low-frequency structure. The notebook also shows why too large a σ over-smooths the signal — removing not just noise but meaningful signal content — making the bias–variance trade-off in kernel bandwidth concrete and visual.

---

### FGSM and Carlini–Wagner — `FGSM_CW.ipynb`

The foundational adversarial attack notebook. A **PreActResNet18** is trained from scratch on **CIFAR-10**, then attacked with two methods:

- **FGSM (Fast Gradient Sign Method)** — a single-step ℓ∞ attack that perturbs each pixel by ε in the direction that maximally increases the loss: δ = ε · sign(∇ₓ L). Simple and fast, but relatively easy to defend against.
- **Carlini–Wagner (C&W)** — a powerful optimization-based attack that finds the smallest ℓ₂ perturbation sufficient to cause a misclassification, formulated as a constrained optimization problem with a differentiable surrogate objective. Much harder to defend against than FGSM.

The notebook evaluates robust accuracy at multiple ε values, visualizes adversarial examples alongside their perturbations, and compares the two attacks in terms of success rate, perturbation magnitude, and perceptual quality. Includes a full evaluation pipeline that returns accuracy and confusion matrices under attack.

---

### DeepFool and Universal Adversarial Perturbations — `DeepFool_UniversalAdversarialPerturbations.ipynb`

Two geometry-driven adversarial attack methods applied to **CIFAR-100** with a **PreActResNet18**:

- **DeepFool** — an iterative attack that finds the closest decision boundary in input space by linearizing the classifier at the current point and computing the minimum-norm perturbation to cross it. More efficient than C&W and produces near-minimal ℓ₂ perturbations.
- **Universal Adversarial Perturbations (UAP)** — a single image-agnostic perturbation vector δ that causes misclassification on most inputs from the data distribution. Unlike per-image attacks, one UAP fools the model on any input it is added to, revealing input-independent structural weaknesses in the learned decision boundaries.

Includes training the victim model, implementing both attacks, evaluating fooling rate across perturbation budgets, and visualizing the perturbations and their effects on model predictions.

---

### Defensive Distillation and ZOO — `DefensiveDistillation_ZOO.ipynb`

Two topics in one notebook — a classical white-box defense and a black-box attack — both evaluated on **CIFAR-10**:

- **Defensive Distillation** (Papernot et al., 2016) — a defense that trains a "distilled" student network on the soft probability outputs of a teacher trained at high softmax temperature T, rather than on hard one-hot labels. The soft labels encode inter-class similarity information and make the student's gradient landscape smoother and harder to exploit. The notebook trains both teacher and student networks, verifies clean accuracy is preserved, then tests both under FGSM at varying ε to quantify the robustness improvement.
- **Zeroth-Order Optimization (ZOO)** — a black-box attack that estimates gradients entirely from model output queries, without access to model weights or architecture. Uses finite-difference approximation of the gradient: ∂L/∂xᵢ ≈ (L(x + hεᵢ) − L(x − hεᵢ)) / 2h. Demonstrates that defenses that rely on gradient masking provide only false security, since query-based methods can circumvent them.

---

### Adversarial Training — `AdversarialTraining.ipynb`

A thorough exploration of **PGD adversarial training** as the primary defense against adversarial attacks. PGD (Projected Gradient Descent) is an iterative ℓ∞-bounded attack that generates the strongest adversarial examples within an ε-ball by taking K gradient steps with step size α and projecting back to the ball after each step. The notebook compares four training scenarios:

1. **Clean baseline** — a standard model trained with no adversarial examples, which collapses under even weak PGD attacks, establishing the reference point.
2. **Standard adversarial training** — PGD adversarial examples are generated at every training step and mixed into the batch. Dramatically improves robustness at a modest cost to clean accuracy.
3. **Curriculum adversarial training** — attack strength is gradually increased during training (starting from small ε and growing), mirroring how human learners build up difficulty incrementally.
4. **Budget-constrained training** — gradient operations are tracked and capped using a budget system, forcing the model to allocate its compute wisely between generating attacks and updating weights.

Training curves, robust accuracy, and clean accuracy are compared across all scenarios.

---

### Model Extraction — `ModelExtraction.ipynb`

Demonstrates **model stealing** — recovering a functional copy of a black-box model using only its output predictions. The victim is a **ResNet34** fine-tuned on **CIFAR-100** (224×224 inputs). The attacker has no access to model weights, architecture, or training data — only the ability to query the model and observe its output logits. The extraction attack uses **knowledge distillation**: the attacker trains a student network to minimize KL-divergence between its own outputs and the victim's soft outputs at temperature T, effectively distilling the victim's knowledge into a new model. The notebook discusses the role of temperature (higher T produces softer, more informative distributions that reveal class similarity structure), evaluates the extracted model's fidelity on CIFAR-100, then demonstrates **attack transferability** — adversarial examples crafted against the extracted model also fool the original victim, even though the attacker never directly accessed it.

---

### Backdoor Attack and Defense — `Backdoor.ipynb`

An end-to-end implementation of a **BadNets backdoor attack** and a **knowledge-distillation-based defense**:

**Part A — Attack:** A small trigger pattern is stamped onto a fraction of training images, which are relabeled with the attacker's chosen target class. The model is trained normally on this poisoned dataset. On clean inputs it performs identically to a standard model; on any input containing the trigger it outputs the attacker's target class. The notebook measures both clean accuracy and **Attack Success Rate (ASR)** to verify the backdoor is both stealthy and effective.

**Part B — Defense:** A defense based on **knowledge distillation without labeled data**. A clean student model is trained using only the poisoned teacher's predictions on unlabeled images (which do not contain the trigger). Since the trigger is never present during distillation, the student learns only the teacher's normal behavior and not the backdoor. Additionally, layer-wise weight reinitialization (controlled by a parameter δ) selectively removes neurons that may encode the trigger, with smaller δ removing more backdoor-related weights. The final student achieves low ASR while preserving clean accuracy, even when evaluated on out-of-distribution data.

---

### Differential Privacy — `DeferentialPrivacy.ipynb`

A graduate-level implementation of **DP-SGD (Differentially Private Stochastic Gradient Descent)** that goes well beyond standard tutorials:

**Part 1 — Privacy Accounting:** Compares two composition theorems for accumulating privacy cost over training steps. Basic Composition (ε_total = Σεᵢ) grows linearly with steps, severely overestimating privacy cost. **Rényi Differential Privacy (RDP)** provides tighter sub-linear bounds, visualized over epochs to show why RDP accounting is necessary for training deep networks under meaningful privacy budgets.

**Part 2 — Efficient Per-Sample Gradients:** The key bottleneck of DP-SGD is clipping gradients per sample before aggregating. The notebook implements `ManualDPSGD` using `torch.func.vmap` (formerly functorch) to vectorize per-sample gradient computation without any Python loops, and benchmarks it against a naive loop implementation to verify the speedup.

**Part 3 — DP Fine-Tuning:** Training from scratch with DP is impractical due to gradient noise. The notebook uses transfer learning — fine-tuning only the final classification head of a pre-trained ResNet18 on CIFAR-10 — to achieve meaningful accuracy under differential privacy.

**Part 4 — Hyperparameter Studies:** Systematic experiments varying the privacy budget ε (noise level) and clipping norm C, visualizing the accuracy–privacy trade-off curve.

---

### Federated Learning — `FederatedLearning.ipynb`

A graduate-level federated learning notebook covering realistic heterogeneity, a stealthy backdoor attack, aggregation defenses, and a gradient-inversion privacy attack — all on **CIFAR-10**:

**Part 1 — Non-IID Data (Dirichlet Split):** Real FL data is not IID. The notebook simulates realistic client heterogeneity using a **Dirichlet distribution** Dir(α) to assign class-imbalanced shards to clients. Small α → severe non-IID (clients see almost only one class); large α → near-IID. Training curves show how client drift degrades global model convergence.

**Part 2 — Model Replacement Backdoor:** A single malicious client injects a **blended trigger** (a semi-transparent watermark) and uses **update scaling** to survive the FedAvg averaging: since averaging divides each client's update by N, the attacker scales their poisoned update by N before submitting, ensuring the backdoor survives aggregation.

**Part 3 — Aggregation Defenses:** Two defenses against the Model Replacement attack: **Norm Clipping** (any client update with ℓ₂ norm above threshold M is scaled down, neutralizing the scaling trick) and **Coordinate-wise Median** aggregation (robust to outliers, replacing the mean with the median across clients for each parameter).

**Part 4 — Gradient Inversion (Privacy Leak):** Demonstrates that gradient sharing in FL is not private. Given a gradient update ∇W, it is possible to approximately reconstruct the original training image by optimizing a dummy input to produce the same gradient. Shows a concrete example of private data leaking through what appears to be an anonymous gradient.

---

## Topics at a Glance

| Area | Notebooks |
|---|---|
| **Math Foundations** | SVD, Gaussian Filter |
| **Adversarial Attacks** | FGSM & C&W, DeepFool & UAP, ZOO (black-box) |
| **Defenses** | Defensive Distillation, Adversarial Training |
| **Privacy Attacks** | Model Extraction, Gradient Inversion |
| **Supply-Chain Attacks** | Backdoor (BadNets) |
| **Formal Privacy** | Differential Privacy (DP-SGD, RDP) |
| **Distributed Learning** | Federated Learning |

---

## Requirements

Core dependencies used across notebooks:

```
torch >= 2.0
torchvision
numpy
matplotlib
tqdm
scipy           # Gaussian_Filter
opacus          # DeferentialPrivacy (DP-SGD)
```
