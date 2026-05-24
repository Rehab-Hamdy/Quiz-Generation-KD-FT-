# Agentic AI Tutor — Configuration v2 and Evaluation Results

## Training configuration

```yaml
### model
model_name_or_path: /kaggle/working/qwen_model
trust_remote_code: true
flash_attn: sdpa

### method
stage: sft
do_train: true
finetuning_type: lora

### LoRA settings
lora_rank: 32
lora_alpha: 32
lora_dropout: 0.1
lora_target: all

### dataset
dataset: mcq_sft_train
eval_dataset: mcq_sft_val
template: qwen
cutoff_len: CUTOFF_LEN
train_on_prompt: false
packing: false
overwrite_cache: true
preprocessing_num_workers: 8

### output
output_dir: /kaggle/working/mcq-lora-adapter_v3
logging_strategy: steps
save_strategy: steps
eval_strategy: steps
logging_steps: 10
save_steps: 82
eval_steps: 41
save_total_limit: 5
plot_loss: true
include_num_input_tokens_seen: true

### best-model tracking
load_best_model_at_end: true
metric_for_best_model: eval_loss
greater_is_better: false

### train
per_device_train_batch_size: 1
gradient_accumulation_steps: 8
learning_rate: 3.0e-5
num_train_epochs: 2.0
lr_scheduler_type: cosine_with_restarts
warmup_ratio: 0.05
weight_decay: 0.05
bf16: true
ddp_timeout: 180000000
neftune_noise_alpha: 15

### eval
per_device_eval_batch_size: 1

### Hugging Face Hub
push_to_hub: true
export_hub_model_id: LORA_REPO_ID
hub_private_repo: true
hub_strategy: all_checkpoints

### tracking
report_to: ["wandb"]
run_name: mcq-bloom-qwen-v2-maxquality
```

## Output paths

- LORA adapter dir: `/kaggle/working/mcq-lora-adapter_v3`
- Best LoRA dir: `/kaggle/working/mcq-lora-adapter-best_v3`
- Best merged model dir: `/kaggle/working/mcq-qwen-merged-best_v3`
- YAML path: `/kaggle/working/LLaMA-Factory/examples/train_lora/mcq_sft_finetune.yaml`

## Evaluation results

### Summary

- Teacher N: 91 valid MCQs scored
- Pre-FT N: 85 valid MCQs scored
- Post-FT N: 90 valid MCQs scored
- CMQS (teacher): 4.706
- CMQS (pre-FT): 4.066
- CMQS (post-FT): 4.304
- Fine-tuning gain: +0.2374
- Quality Preservation Score (QPS): 91.46%

### Main dimensions

| Dimension                      |   Teacher |    Pre-FT |   Post-FT |    FT Gain |     QPS % |
| ------------------------------ | --------: | --------: | --------: | ---------: | --------: |
| D1 — Factual Grounding         |     4.758 |     3.788 |     4.022 |     +0.234 |     84.5% |
| D2 — Question Clarity          |     5.000 |     4.753 |     4.933 |     +0.180 |     98.7% |
| D3 — Correct Answer Validity   |     5.000 |     3.929 |     4.433 |     +0.504 |     88.7% |
| D4 — Distractor Plausibility   |     3.978 |     3.682 |     3.678 |     -0.005 |     92.5% |
| D5 — Bloom Alignment           |     4.813 |     4.247 |     4.611 |     +0.364 |     95.8% |
| D6 — Topic & LO Alignment      |     4.989 |     4.847 |     4.911 |     +0.064 |     98.4% |
| **CMQS (Composite, weighted)** | **4.706** | **4.066** | **4.304** | **+0.237** | **91.5%** |

### Quality preservation

QPS = Post-FT / Teacher × 100 = 4.304 / 4.706 × 100 = 91.46%

Interpretation: **EXCELLENT** — the student matches teacher quality closely.

### Bloom-level breakdown

| Bloom Level | Teacher | Pre-FT | Post-FT | QPS % |
| ----------- | ------: | -----: | ------: | ----: |
| remember    |   4.675 |  4.550 |   4.500 | 96.3% |
| understand  |   4.767 |  4.311 |   4.374 | 91.8% |
| apply       |   4.715 |  4.227 |   4.498 | 95.4% |
| analyze     |   4.739 |  3.819 |   4.224 | 89.1% |
| evaluate    |   4.629 |  3.746 |   3.865 | 83.5% |
| create      |   4.470 |  3.717 |   4.270 | 95.5% |

### Output files

- `V1_radar_all_stages.png`
- `V2_grouped_bar_dimensions.png`
- `V3_bloom_level_lines.png`
- `V4_quality_distribution.png`
- `V5_gap_heatmap.png`
- `V6_cmqs_bar.png`

## Comparison: Version 1 vs Version 3

| Metric           | Version 1 | Version 3 | Difference |
| ---------------- | --------: | --------: | ---------: |
| Teacher N        |        91 |        91 |          0 |
| Pre-FT N         |        83 |        85 |         +2 |
| Post-FT N        |        86 |        90 |         +4 |
| CMQS (Pre-FT)    |     4.075 |     4.066 |     -0.009 |
| CMQS (Post-FT)   |     4.319 |     4.304 |     -0.015 |
| QPS              |     91.8% |    91.46% |   -0.34 pp |
| Fine-Tuning Gain |    +0.245 |   +0.2374 |    -0.0076 |

### Dimension-by-dimension comparison

| Dimension                    | V1 Post-FT | V3 Post-FT | Change |
| ---------------------------- | ---------: | ---------: | -----: |
| D1 — Factual Grounding       |      3.954 |      4.022 | +0.068 |
| D2 — Question Clarity        |      4.954 |      4.933 | -0.021 |
| D3 — Correct Answer Validity |      4.407 |      4.433 | +0.026 |
| D4 — Distractor Plausibility |      3.895 |      3.678 | -0.217 |
| D5 — Bloom Alignment         |      4.546 |      4.611 | +0.065 |
| D6 — Topic & LO Alignment    |      4.930 |      4.911 | -0.019 |

### Comparison summary

Version 3 is very close to Version 1 overall. It improves:

- factual grounding,
- correct answer validity,
- and Bloom alignment.

Version 1 is slightly better on:

- distractor plausibility,
- question clarity,
- and topic/LO alignment.

Overall, Version 1 remains marginally stronger on the composite score, but Version 3 is still a strong run and keeps quality preservation above 91%.

## Notes

- Version 3 uses a smaller LoRA rank, stronger regularization, and a lower learning rate.
- Version 3 also uses `cosine_with_restarts`, which may help escape local minima.
- The stronger regularization appears to preserve quality well, but it did not outperform Version 1 on CMQS.
