# 🏥 Trust But Verify  
### **Auditing DenseNet121 Chest X-Ray Classification using High-Resolution Perturbation-Based Explainability**

## 🚀 Abstract
Deep Learning models are reaching radiologist-level accuracy in chest pathology detection, but their decision behavior remains opaque. We propose a **DenseNet121 framework combined with a high-resolution RISE (Randomized Input Sampling for Explanation) interpretability module**. Unlike gradient-based methods that produce visually clean but potentially misleading explanations, our approach prioritizes **faithfulness to the model’s true decision logic**.

To address noise in perturbation-based heatmaps, we introduce **Adaptive Thresholding**, improving clarity without sacrificing fidelity. Through Faithfulness, Sparsity, and Entropy metrics, we show that although GradCAM++ provides superior localization, **RISE offers significantly higher faithfulness**, exposing a **“Clever Hans” failure mode** where predictions relied on radiographic side markers rather than pathology.

## 📌 1. Introduction
Deep learning models like **DenseNet121** have demonstrated excellent performance in diagnosing chest pathologies. However, high accuracy alone is insufficient for clinical adoption because models can exploit **spurious correlations** instead of genuine disease features (Shortcut Learning).

To evaluate model trustworthiness, we compare three major categories of XAI:

| Category | Method |
|---------|--------|
| Gradient-based | **GradCAM++** |
| Ablation-based | **AblationCAM** |
| Perturbation-based | **RISE (proposed)** |

> Our hypothesis: *Gradient-based methods are ideal for visualization, while perturbation-based methods are necessary for safety auditing in medical AI.*

## ⚙️ 2. Methodology

### 🔹 2.1 Dataset & Preprocessing
- NIH Chest X-Ray dataset (curated subset)
- Images resized to **224 × 224**
- Parallel **Artifact-Injected** test set created by adding “R” markers to simulate spurious shortcuts

### 🔹 2.2 Model — DenseNet121
- Binary Cross-Entropy Loss
- Adam optimizer
- Dense connectivity promotes multi-scale feature reuse → ideal for subtle pathology detection

### 🔹 2.3 Explainability Framework
| Method | Type | Core Principle |
|--------|------|----------------|
| **GradCAM++** | Gradient | Weighted pixel-level gradients over final conv feature maps |
| **AblationCAM** | Ablation | Removes feature maps to measure drop in predicted probability |
| **RISE (proposed)** | Perturbation | Random mask sampling + weighted heatmap aggregation |

🆕 **Adaptive Thresholding**
\[
\text{threshold} = \mu + \alpha\sigma
\]  
Removes irrelevant background noise while retaining the granular fidelity of perturbation maps.

## 📊 3. Results

### 🔹 3.1 Model Performance
| Model Architecture | Accuracy | AUC (Average) | Precision | Recall |
|-------------------|----------|--------------|-----------|--------|
| **DenseNet121 (Ours)** | **0.842** | **0.885** | **0.810** | **0.795** |
| ResNet50 (Baseline) | 0.815 | 0.860 | 0.785 | 0.760 |

### 🔹 3.2 Explainability Evaluation
| XAI Method | Faithfulness (Correlation ↑ toward 0 = better) | Sparsity (Gini ↑ = more localized) | Complexity (Entropy ↓ = cleaner) |
|------------|----------------------------------------------|-----------------------------------|----------------------------------|
| **GradCAM++** | **−0.6195** | **0.8231** | **9.1051** |
| AblationCAM | −0.5075 | 0.7726 | 9.4218 |
| **RISE (Ours)** | **−0.2880 (Best)** | 0.7593 | 9.4346 |

⮞ GradCAM++ → visually clean but least faithful  
⮞ RISE → noisier but most truthful and reveals model weaknesses

## 💬 4. Discussion

### 🔸 Clarity vs. Truth
| Clinical Use Case | Recommended Method | Why |
|-------------------|-------------------|-----|
| Clinician-facing visual localization | **GradCAM++** | Sharp, intuitive heatmaps |
| Safety auditing & model vulnerability detection | **RISE** | Highest faithfulness; exposes biases |

### 🔸 Shortcut Learning (Clever Hans)
RISE exposed that DenseNet121 relied on **radiographic side markers (L/R)** for prediction.  
GradCAM++ smoothed over this dependency — giving a **false sense of safety**.

A paired t-test on the **Artifact-Injected** dataset confirmed statistically significant prediction shifts (*p* < 0.05), proving that the model exploited dataset artifacts rather than anatomical features.

## 🧾 5. Conclusion
✔ DenseNet121 is powerful but **vulnerable to Shortcut Learning**  
✔ GradCAM++ is **not sufficient** for safety-critical transparency  
✔ **High-resolution perturbation-based auditing (RISE)** is essential before clinical deployment  
✔ High clinical readiness = **High AUC + Verified Interpretability**

📌 **Future Work**
Use an **ImpactX attention-guided training scheme** to penalize non-anatomical focus and eliminate artifact dependency.

## 📦 Citation (optional)
```
@article{trustbutverify2025,
  title={Trust But Verify: Auditing DenseNet121 Chest X-Ray Classification using High-Resolution Perturbation-Based Explainability},
  author={Your Name et al.},
  year={2025}
}
```

## ⭐ Suggested Add-On Sections (if needed later)
- Model Weights & Dataset Path
- Training & Evaluation Commands
- Project Tree
- Requirements.txt
- License
