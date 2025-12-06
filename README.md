

## Abstract
Deep Learning models are achieving radiologist-level performance in chest pathology detection, but their decision behavior remains opaque. We propose a DenseNet121 framework combined with a high-resolution RISE (Randomized Input Sampling for Explanation) interpretability module. Unlike gradient-based methods that generate smooth but potentially misleading visualizations, our approach prioritizes faithfulness to the model’s true decision logic.

To address the inherent noise of perturbation-based maps, an Adaptive Thresholding step is introduced to improve visual clarity without sacrificing fidelity. Evaluation using Faithfulness, Sparsity, and Entropy demonstrates that although GradCAM++ provides superior localization, RISE achieves significantly higher faithfulness and reveals shortcut learning. Specifically, RISE exposed a failure mode where the model relied on radiographic side markers rather than pathology.

## 1. Introduction
Convolutional Neural Networks such as DenseNet121 perform well on chest pathology classification, yet clinical deployment requires transparency. Existing explainability methods often emphasize aesthetic visualization instead of identifying whether a model is relying on valid anatomical features or dataset shortcuts.

We compare three explainability paradigms:
| Category | Method |
|---------|--------|
| Gradient-based | GradCAM++ |
| Ablation-based | AblationCAM |
| Perturbation-based | RISE (proposed) |

Our hypothesis: gradient-based methods are suited for visualization, whereas perturbation-based methods are required for reliable model auditing.

## 2. Methodology

### 2.1 Dataset and Preprocessing
- Curated subset of the NIH Chest X-Ray dataset
- Images resized to 224 × 224 and normalized
- Additional “Artifact-Injected” test set created by adding synthetic radiographic markers to evaluate shortcut learning

### 2.2 DenseNet121 Classification Model
- Binary Cross-Entropy loss
- Adam optimizer
- Dense connectivity encourages multi−scale feature reuse, suitable for subtle manifestations of pathology

### 2.3 Explainability Framework
| Method | Type | Core Principle |
|--------|------|----------------|
| GradCAM++ | Gradient-based | Weighted spatial gradients over final convolutional feature maps |
| AblationCAM | Ablation-based | Removes feature maps and measures drop in prediction |
| RISE (proposed) | Perturbation-based | Randomized masking and score-weighted relevance reconstruction |

**Adaptive Thresholding for RISE**  
A dynamic threshold defined as μ + ασ is applied to filter low-intensity activations, reducing noise while preserving high-fidelity relevance patterns.

## 3. Results

### 3.1 Classification Performance
| Model Architecture | Accuracy | AUC (Average) | Precision | Recall |
|-------------------|----------|--------------|-----------|--------|
| DenseNet121 (Ours) | 0.842 | 0.885 | 0.810 | 0.795 |
| ResNet50 (Baseline) | 0.815 | 0.860 | 0.785 | 0.760 |

### 3.2 Explainability Metrics
| XAI Method | Faithfulness (Correlation ↑ toward 0 = better) | Sparsity (Gini ↑ = better localization) | Complexity (Entropy ↓ = cleaner) |
|------------|----------------------------------------------|-----------------------------------------|----------------------------------|
| GradCAM++ | −0.6195 | 0.8231 | 9.1051 |
| AblationCAM | −0.5075 | 0.7726 | 9.4218 |
| RISE (proposed) | −0.2880 (best) | 0.7593 | 9.4346 |

RISE achieves the highest faithfulness, exposing the actual decision process even when visually noisy. GradCAM++ yields attractive but less truthful maps that can conceal model vulnerabilities.

## 4. Discussion
A trade-off exists between clarity and truthfulness. GradCAM++ provides focused regions useful for clinician interpretation but can mask dependencies on spurious cues. RISE, while less visually polished, revealed that the DenseNet121 model relied on radiographic side-marker text (“L” / “R”), confirming Shortcut Learning. This was validated using a paired t-test on the Artifact-Injected dataset (p < 0.05).

Clinical deployment requires not only high accuracy but demonstrated independence from dataset artifacts.

## 5. Conclusion
- DenseNet121 achieves strong diagnostic performance, but is susceptible to Shortcut Learning.
- Gradient-based methods are insufficient for safety-critical interpretability.
- Perturbation-based auditing (RISE) is essential to verify that predictions are based on pathology rather than confounding artifacts.
- Clinical readiness requires both high AUC and verified interpretability.
