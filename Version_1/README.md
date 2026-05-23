# Agentic AI Tutor — Fine-Tuning README

## Training Configuration

```yaml
model_name_or_path: /kaggle/working/qwen_model
trust_remote_code: true

stage: sft
do_train: true
finetuning_type: lora

lora_rank: 64
lora_alpha: 128
lora_dropout: 0.05
lora_target: all

dataset: mcq_sft_train
eval_dataset: mcq_sft_val
template: qwen
cutoff_len: <cutoff>
train_on_prompt: false
packing: false
overwrite_cache: true
preprocessing_num_workers: 8

output_dir: /kaggle/working/mcq-lora-adapter
logging_strategy: steps
save_strategy: steps
eval_strategy: steps
logging_steps: 10
save_steps: 312
eval_steps: 104
save_total_limit: 3
plot_loss: true
include_num_input_tokens_seen: true

load_best_model_at_end: true
metric_for_best_model: eval_loss
greater_is_better: false

per_device_train_batch_size: 1
gradient_accumulation_steps: 4
learning_rate: 1.0e-4
num_train_epochs: 3.0
lr_scheduler_type: cosine
warmup_ratio: 0.1
bf16: true
ddp_timeout: 180000000
neftune_noise_alpha: 5

per_device_eval_batch_size: 1

push_to_hub: true
export_hub_model_id: <LORA_REPO_ID>
hub_private_repo: true
hub_strategy: all_checkpoints

report_to:
  - wandb
run_name: mcq-bloom-qwen-kaggle

# resume_from_checkpoint: /kaggle/working/mcq-lora-adapter/checkpoint-500
```

### Notes
- `packing: false` was used because the dataset contains multi-turn ShareGPT-style records.
- `train_on_prompt: false` ensures only assistant turns contribute to the loss.
- `bf16: true` was used because Qwen2.5 models can be unstable with FP16.
- Checkpoints were saved during training and the best checkpoint was selected using `eval_loss`.

## Checkpoint History

| Step | Eval Loss | Best? |
|---:|---:|:---:|
| 104 | 0.6924 | ⭐ |
| 208 | 0.6728 | ⭐ |
| 312 | 0.6508 | ⭐ |
| 416 | 0.6792 |  |
| 520 | 0.6733 |  |
| 624 | 0.6647 |  |
| 728 | 0.7733 |  |
| 832 | 0.7756 |  |
| 936 | 0.7752 |  |

**Best checkpoint:** `checkpoint-312`  
**Best eval loss:** `0.6508`

## Final Evaluation Comparison Report

**Judge model:** `meta-llama/llama-3.3-70b-instruct`  
**Eval sample N:** `100`  

| Model | Valid MCQs Scored |
|---|---:|
| Teacher | 91 |
| Pre-Finetune Student | 83 |
| Post-Finetune Student | 86 |

### Main Results

| Dimension | Teacher | Pre-FT | Post-FT | FT Gain | QPS % |
|---|---:|---:|---:|---:|---:|
| D1 — Factual Grounding | 4.758 | 3.831 | 3.954 | +0.122 | 83.1% |
| D2 — Question Clarity | 5.000 | 4.807 | 4.954 | +0.146 | 99.1% |
| D3 — Correct Answer Validity | 5.000 | 3.892 | 4.407 | +0.515 | 88.1% |
| D4 — Distractor Plausibility | 3.978 | 3.747 | 3.895 | +0.148 | 97.9% |
| D5 — Bloom Alignment | 4.813 | 4.193 | 4.546 | +0.354 | 94.5% |
| D6 — Topic & LO Alignment | 4.989 | 4.795 | 4.930 | +0.135 | 98.8% |
| **CMQS (Composite, weighted)** | **4.706** | **4.075** | **4.319** | **+0.245** | **91.8%** |

### Quality Preservation

**Quality Preservation Score (QPS) = Post-FT / Teacher × 100**  
**QPS = 4.319 / 4.706 × 100 = 91.8%**

## Summary

The fine-tuned student model improved over the pre-finetuned student on every main evaluation dimension, especially:
- **Correct Answer Validity**
- **Bloom Alignment**
- **Question Clarity**

The post-finetuned model still remained below the teacher model overall, but it preserved most of the teacher quality while recovering a meaningful portion of the performance gap.

