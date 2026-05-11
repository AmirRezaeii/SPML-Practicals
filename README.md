# SPML Practical Assignments

This repository contains practical implementations of core security and privacy attacks & defenses in Machine Learning. Developed as part of the **Security and Privacy in Machine Learning (SPML)** course, each Jupyter notebook demonstrates a key concept with code, visualizations, and explanations.

The goal is to provide clear, runnable examples of vulnerabilities (e.g., adversarial examples, model stealing, backdoor attacks) and countermeasures (e.g., differential privacy, defensive distillation, adversarial training) in modern ML pipelines.

## Repository Structure & Detailed Explanations

| Notebook | Topics Covered |
|----------|----------------|
| **Gaussian_Filter.ipynb** | Simple defense against adversarial examples by applying Gaussian smoothing to inputs, removing high-frequency noise. |
| **SVD.ipynb** | Singular Value Decomposition for data compression and potential defense against adversarial perturbations via low-rank approximation. |
| **DeepFool_UniversalAdversarialPerturbations.ipynb** | Computing minimal perturbations to fool classifiers (DeepFool) and generating image-agnostic universal adversarial perturbations that work across multiple inputs. |
| **FGSM_CW.ipynb** | Fast Gradient Sign Method (FGSM) for single-step adversarial attacks, and Carlini & Wagner (C&W) L2 optimization-based attack for stronger, more imperceptible perturbations. |
| **AdversarialTraining.ipynb** | Defending against adversarial examples by augmenting training data with adversarial samples, including **Adversarial Training for Free** using gradient reuse. |
| **DefensiveDistillation_ZOO.ipynb** | Two key techniques: (1) **Defensive Distillation** – training student models on teacher's soft labels to reduce gradient information; (2) **ZOO (Zeroth Order Optimization) based Black-Box Attacks** – estimating gradients via queries without access to model internals. Also covers **Adversarially Robust Distillation**. |
| **Backdoor.ipynb** | **BadNets backdoor injection method** – inserting triggers into training data to cause targeted misclassification, along with **defense methods** against such backdoor attacks. |
| **ModelExtraction.ipynb** | Stealing a target model's functionality via query access, and **Distilling the Knowledge in a Neural Network** (knowledge distillation) for model compression and transfer learning. |
| **DeferentialPrivacy.ipynb** | Implementing **Differential Privacy in Deep Learning** (DP-SGD), including clipping gradients and adding calibrated noise to achieve formal privacy guarantees. |
| **FederatedLearning.ipynb** | Simulating **Federated Learning** across multiple clients without sharing raw data. Implements **User-Level Differential Privacy** to defend against inversion attacks and protect individual user contributions. |

## Key Concepts Illustrated

| Category | Techniques Demonstrated |
|----------|------------------------|
| **Evasion Attacks** | FGSM, C&W, DeepFool, Universal Adversarial Perturbations, ZOO Black-Box Attacks |
| **Poisoning/Backdoor** | BadNets backdoor injection, defense methods against backdoor attacks |
| **Privacy Attacks** | Model extraction, inversion attacks (defended via DP), membership inference |
| **Defenses** | Adversarial Training (including Free version), Defensive Distillation, Adversarially Robust Distillation, Gaussian Filtering, SVD compression |
| **Privacy-Preserving ML** | Differential Privacy (DP-SGD), Federated Learning, User-Level Differential Privacy |
| **Knowledge Transfer** | Knowledge distillation, model extraction, distilling neural networks |
