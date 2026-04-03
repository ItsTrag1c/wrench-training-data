# Wrench Training Data

Training data and notebooks for **Wrench** — a family of LoRA fine-tuned models purpose-built for agentic tool calling.

| Model | Base | Score | VRAM | Download |
|-------|------|-------|------|----------|
| **Wrench 35B v7** | [Qwen3.5-35B-A3B](https://huggingface.co/Qwen/Qwen3.5-35B-A3B) (MoE) | 118/120 (98.3%) | 16GB | [HuggingFace](https://huggingface.co/ClankLabs/Wrench-35B-A3B-Q4_K_M-GGUF) |
| **Wrench 9B v4** | [Qwen3.5-9B](https://huggingface.co/Qwen/Qwen3.5-9B) (dense) | 114/120 (95.0%) | 8GB | [HuggingFace](https://huggingface.co/ClankLabs/Wrench-9B-Q4_K_M-GGUF) |

## Results

Wrench 35B v7 scores **118/120 (98.3%)** on our 8-category agentic benchmark and **82.0%** on the [Berkeley Function Calling Leaderboard](https://gorilla.cs.berkeley.edu/leaderboard.html) (BFCL) non_live/AST category — an independent, standardized benchmark across 1,390 test cases.

| Model | Clank Benchmark | BFCL (non_live) | Runs On | Cost |
|-------|----------------|-----------------|---------|------|
| **Wrench 35B v7** | **118/120** | **82.0%** | **16GB GPU** | **Free** |
| Claude Opus 4.6 | ~118/120 | — | Cloud | Paid |
| GPT-5.2 | ~116/120 | — | Cloud | $20/mo |
| Claude Sonnet 4.6 | ~114/120 | — | Cloud | $20/mo |
| Wrench 35B v5 | 113/120 | — | 16GB GPU | Free |
| GPT-4o | ~110/120 | — | Cloud | $20/mo |
| **Wrench 9B v4** | **114/120** | **—** | **8GB GPU** | **Free** |

### BFCL Breakdown (Wrench 35B)

| Category | Accuracy | Correct/Total |
|----------|----------|---------------|
| Simple (Python) | 84.75% | 339/400 |
| Simple (Java) | 44.0% | 44/100 |
| Simple (JavaScript) | 56.0% | 28/50 |
| Multiple | 84.5% | 169/200 |
| Parallel | 85.0% | 170/200 |
| Parallel Multiple | 82.5% | 165/200 |
| Irrelevance Detection | 88.75% | 213/240 |
| **Overall** | **82.0%** | **1128/1390** |

### Per-Category Breakdown (35B)

| Category | v5 | v7 | Change |
|----------|-----|-----|--------|
| Basic Tool Use | 15/15 | 15/15 | — |
| Multi-Step Tasks | 14/15 | **15/15** | +1 |
| Error Recovery | 13/15 | **14/15** | +1 |
| Response Quality | 15/15 | 15/15 | — |
| System Prompt Following | 14/15 | 14/15 | — |
| Planning & Reasoning | 14/15 | **15/15** | +1 |
| Tool Format Correctness | 13/15 | **15/15** | +2 |
| Safety & Restraint | 15/15 | 15/15 | — |
| **Total** | **113/120** | **118/120** | **+5** |

## What's Here

```
wrench-training-data/
├── datasets/              # 1,147 base training examples (18 JSONL files)
│   ├── tool-calling.jsonl
│   ├── agent-behavior.jsonl
│   ├── multi-step-chains.jsonl
│   ├── error-recovery.jsonl
│   ├── ... (18 files total)
│   └── sub-agent-roles.jsonl
├── datasets-frontier/     # 105 frontier-gap training examples (4 JSONL files)
│   ├── uncertainty-calibration.jsonl    # 25 — verify before guessing
│   ├── constraint-following.jsonl       # 25 — do exactly what was asked
│   ├── strategy-revision.jsonl          # 20 — fail smart, pivot don't retry
│   └── long-context-multiturn.jsonl     # 35 — coherence over 20+ turns
├── notebooks/
│   ├── train.ipynb        # 35B training notebook (RunPod 2x H100)
│   └── train-8b.ipynb     # 9B training notebook (RunPod 1x H100)
├── benchmark.md           # 120-point scoring rubric (8 categories)
└── README.md
```

## Dataset Breakdown

### Base Data (1,147 examples)

| Dataset | Examples | Description |
|---------|----------|-------------|
| advanced-coding-2 | 92 | Complex coding tasks with tool use |
| agent-behavior | 92 | General agentic patterns |
| coding-knowledge | 91 | Code comprehension and generation |
| concise-direct | 91 | Terse, no-filler responses |
| debugging-mastery | 91 | Systematic debugging with tools |
| error-recovery | 91 | Graceful handling of tool failures |
| knowing-when-to-stop | 91 | Avoiding unnecessary extra steps |
| multi-step-chains | 91 | Read -> edit -> verify sequences |
| sub-agents | 91 | Delegating to sub-agents |
| system-prompt-following | 91 | Respecting system prompt constraints |
| tool-calling | 91 | Core ReAct tool-call format |
| always-use-tools | 30 | Use tools for factual queries, never hallucinate |
| multi-step-chains-v2 | 30 | Full multi-turn chains with realistic tool results |
| destructive-action-caution | 20 | Warn before destructive actions |
| tool-restraint | 30 | When NOT to use tools |
| workflow-composition | 30 | Multi-tool workflow patterns |
| sub-agent-roles | 20 | Role-based sub-agent usage |
| **Subtotal** | **1,147** | |

### Frontier Data (105 examples — new in v7)

Targeted training data designed to close specific behavioral gaps between local and frontier models.

| Dataset | Examples | Description |
|---------|----------|-------------|
| uncertainty-calibration | 25 | Express uncertainty, verify with tools before acting |
| constraint-following | 25 | Follow explicit user constraints exactly, resist scope creep |
| strategy-revision | 20 | Analyze failures and change approach, don't retry blindly |
| long-context-multiturn | 35 | Maintain coherence over 10-20+ message conversations |
| **Subtotal** | **105** | |

### Total: 1,252 examples

## Training Details

### Wrench 35B (v7 — current)
- **Base model:** Qwen3.5-35B-A3B (MoE, 3B active parameters)
- **Method:** LoRA (rank 64, alpha 128) via HuggingFace PEFT + Trainer
- **Hardware:** 2x NVIDIA H100 80GB (RunPod)
- **Training time:** ~1 hour per run
- **Hyperparameters:** batch_size=1, gradient_accumulation=8, 2 epochs, lr=1e-4
- **Final loss:** 0.1592
- **Output format:** GGUF Q4_K_M (~20GB, runs on 16GB VRAM)

### Wrench 9B (v4 — current)
- **Base model:** Qwen3.5-9B (dense)
- **Method:** LoRA (rank 32, alpha 64) via HuggingFace PEFT + Trainer
- **Hardware:** 1x NVIDIA H100 80GB (RunPod)
- **Training time:** ~30 min per run
- **Hyperparameters:** batch_size=1, gradient_accumulation=8, 2 epochs, lr=1e-4
- **Examples:** 1,356 (1,251 base + 105 frontier)
- **Final loss:** 0.1512
- **Output format:** GGUF Q4_K_M (~5GB, runs on 8GB VRAM)

## Data Format

ShareGPT format — each example is a multi-turn conversation with tool calls:

```json
{
  "conversations": [
    {"from": "system", "value": "You are an AI agent with tools..."},
    {"from": "human", "value": "Read the config file"},
    {"from": "gpt", "value": "I'll read that file.\n\n```tool_call\n{\"name\": \"read_file\", \"arguments\": {\"path\": \"/etc/config.json\"}}\n```"},
    {"from": "tool", "value": "{\"content\": \"...\"}"},
    {"from": "gpt", "value": "Here's what's in the config: ..."}
  ]
}
```

## How to Train Your Own

1. Clone this repo
2. Sign up for [RunPod](https://runpod.io) — rent 2x H100 80GB (~$5/hr)
3. Upload `notebooks/train.ipynb` and both `datasets/` + `datasets-frontier/` folders
4. Run all cells
5. Download the output GGUF and load it in [Ollama](https://ollama.com) or llama.cpp

See the notebook for full setup instructions including the exact pip install order.

## License

Apache 2.0 — use the data however you want.

## Links

- [Wrench 35B on HuggingFace](https://huggingface.co/ClankLabs/Wrench-35B-A3B-Q4_K_M-GGUF)
- [Wrench 9B on HuggingFace](https://huggingface.co/ClankLabs/Wrench-9B-Q4_K_M-GGUF)
- [Training Data Repository](https://github.com/ClankLabs/wrench-training-data)
- [Clank Gateway](https://github.com/ClankLabs/Clank) — the AI agent gateway Wrench was built for
- [clanklabs.dev/wrench](https://clanklabs.dev/wrench)
