```md
# MCQ Fine-Tuning Experiments Comparison

## Project Overview

| Item | Details |
|---|---|
| Project | Agentic AI Tutor |
| Task | Automatic MCQ Generation |
| Student Model | Qwen2.5-1.5B-Instruct |
| Teacher Model | Gemini Flash 2.5 Lite |
| Fine-Tuning Method | LoRA SFT |
| Framework | LLaMA-Factory |
| Current Dataset Size | 2921 samples |
| External Evaluation Set | 100 unseen samples |

---

# Evaluation Metrics

| Metric | Description |
|---|---|
| D1 | Factual Grounding |
| D2 | Question Clarity |
| D3 | Correct Answer Validity |
| D4 | Distractor Plausibility |
| D5 | Bloom Alignment |
| D6 | Topic & Learning Objective Alignment |
| CMQS | Composite MCQ Quality Score |
| QPS | Quality Preservation Score |

---

# Dataset Splits

| Version | Train | Validation | Test |
|---|---|---|---|
| Version 1 | 2489 | 289 | 143 |
| Version 2 | 2632 | 289 | External only |
| Version 3 | 2632 | 289 | External only |

---

# Hyperparameter Comparison

| Hyperparameter | Version 1 | Version 2 | Version 3 |
|---|---|---|---|
| LoRA Rank | 64 | 16 | 32 |
| LoRA Alpha | 128 | 16 | 32 |
| LoRA Dropout | 0.05 | 0.05 | 0.10 |
| Learning Rate | 1e-4 | 5e-5 | 3e-5 |
| Epochs | 3 | 2 | 2 |
| Gradient Accumulation | 4 | 4 | 8 |
| Scheduler | cosine | cosine | cosine_with_restarts |
| Warmup Ratio | 0.10 | 0.05 | 0.05 |
| Weight Decay | None | 0.01 | 0.05 |
| NEFTune Noise Alpha | 5 | 10 | 15 |
| Eval Steps | 104 | 82 | 41 |
| Save Steps | 312 | 164 | 82 |

---

# Main Evaluation Results

| Metric | Teacher | Pre-FT | V1 Post-FT | V2 Post-FT | V3 Post-FT |
|---|---|---|---|---|---|
| CMQS | 4.706 | 4.075 / 4.066 | 4.319 | **4.354** | 4.304 |
| QPS | 100% | — | 91.8% | **92.5%** | 91.5% |

---

# Dimension Score Comparison

| Dimension | Teacher | Version 1 | Version 2 | Version 3 |
|---|---|---|---|---|
| D1 Factual Grounding | 4.758 | 3.954 | **4.045** | 4.022 |
| D2 Question Clarity | 5.000 | 4.954 | **4.955** | 4.933 |
| D3 Correct Answer Validity | 5.000 | 4.407 | **4.472** | 4.433 |
| D4 Distractor Plausibility | 3.978 | **3.895** | 3.832 | 3.678 |
| D5 Bloom Alignment | 4.813 | 4.546 | 4.596 | **4.611** |
| D6 Topic & LO Alignment | 4.989 | 4.930 | **4.978** | 4.911 |

---

# Training Behavior Comparison

| Aspect | Version 1 | Version 2 | Version 3 |
|---|---|---|---|
| Training Style | Aggressive | Balanced | Conservative |
| Generalization | Medium | **Best** | Good |
| Overfitting Risk | High | Low | Very Low |
| Stability | Medium | **Best** | Very Good |
| Regularization Strength | Low | Balanced | High |
| LoRA Capacity | Very High | Low | Medium |
| Eval Loss Stability | Poor late-stage | **Stable** | Stable |
| Train/Eval Gap | Large | Small | Small |
| Valid MCQ Outputs | Good | Very Good | **Best** |

---

# Observed Strengths

| Version | Main Strengths |
|---|---|
| Version 1 | Strong adaptation power, good distractor quality, high learning capacity |
| Version 2 | Best balance of quality and generalization, best CMQS/QPS, stable convergence |
| Version 3 | Strong regularization, safest training behavior, highest valid output count |

---

# Observed Weaknesses

| Version | Main Weaknesses |
|---|---|
| Version 1 | Clear overfitting, unstable eval behavior, excessive adaptation capacity |
| Version 2 | Slightly weaker distractor plausibility, lower adaptation capacity |
| Version 3 | Possible underfitting, over-regularization, lower composite quality |

---

# Training Curve Interpretation

| Version | Interpretation |
|---|---|
| Version 1 | Train loss kept decreasing while eval loss worsened later → clear overfitting |
| Version 2 | Train/eval curves improved smoothly together → healthy convergence |
| Version 3 | Stable but conservative optimization → possible mild underfitting |

---

# Overall Ranking

| Rank | Version | Reason |
|---|---|---|
| 1 | Version 2 | Best overall balance between quality, stability, and generalization |
| 2 | Version 1 | Strong quality gains but clear overfitting behavior |
| 3 | Version 3 | Stable but overly regularized and slightly weaker quality |

---

# Recommended Next Direction

## Planned Dataset Expansion

| Current Samples | Additional Samples | Future Total |
|---|---|---|
| 2921 | ~1700 | ~4600 |

---

# Recommended Next Configuration

| Hyperparameter | Recommended Value |
|---|---|
| LoRA Rank | 24 or 32 |
| LoRA Alpha | 24 or 32 |
| LoRA Dropout | 0.05 |
| Learning Rate | 4e-5 to 5e-5 |
| Epochs | 2 |
| Weight Decay | 0.02 |
| Gradient Accumulation | 4 or 8 |
| Scheduler | cosine |
| Warmup Ratio | 0.05 |
| NEFTune Noise Alpha | 10 |

---

# Why This Configuration

| Goal | Reason |
|---|---|
| Preserve Version 2 stability | Best generalization behavior |
| Increase model capacity slightly | Larger dataset can support higher LoRA rank |
| Avoid Version 1 overfitting | Lower LR and stronger regularization |
| Avoid Version 3 underfitting | Reduce excessive regularization |

---

# Key Metrics to Monitor

| Category | Metrics |
|---|---|
| Training Stability | Train/eval loss gap, eval loss trend |
| Quality | CMQS, QPS, D3, D4 |
| Overfitting Signals | Eval loss rising while train loss drops |
| Underfitting Signals | Flat curves, weak quality improvements |

---

# Final Conclusion

Version 2 achieved the best overall tradeoff between:
- MCQ quality,
- teacher-quality preservation,
- generalization,
- and training stability.

For the next larger-scale training run, the best strategy is:
- start from Version 2,
- moderately increase LoRA capacity,
- and maintain balanced regularization.
```
