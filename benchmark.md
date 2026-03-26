# Wrench Benchmark — Base vs Fine-Tuned vs Frontier

Score each response 0-3:
- **0** = Refused, wrong, or no tool call when one was needed
- **1** = Attempted but wrong tool or bad arguments
- **2** = Correct tool call but poor response quality
- **3** = Perfect — right tool, right args, good response

Test each model with the same prompts, same system prompt, same tool definitions.

### Models to Test

| Label | Model | How to Test |
|-------|-------|-------------|
| **Base** | `ollama/qwen3.5:35b-a3b` | Ollama / Clank with stock model |
| **Wrench** | `ollama/wrench` | Ollama / Clank with your fine-tuned model |
| **Claude Opus** | `anthropic/claude-opus-4-6` | Clank with Anthropic API key |
| **Claude Sonnet** | `anthropic/claude-sonnet-4-6` | Clank with Anthropic API key |
| **GPT-4o** | `openai/gpt-4o` | Clank with OpenAI API key |

You don't need to test all frontier models — pick 1-2 as a ceiling reference. Claude Sonnet is a good target since it's the "practical daily driver" tier.

---

## Category 1: Basic Tool Use

| # | Prompt | Base | Wrench | Claude Sonnet | GPT-4o |
|---|--------|------|--------|---------------|--------|
| 1 | "Read the file at ./README.md" | | | | |
| 2 | "What's in the current directory?" | | | | |
| 3 | "Run `npm test`" | | | | |
| 4 | "Search for TODO comments in the src folder" | | | | |
| 5 | "What's my Node.js version?" | | | | |

## Category 2: Multi-Step Tasks

| # | Prompt | Base | Wrench | Claude Sonnet | GPT-4o |
|---|--------|------|--------|---------------|--------|
| 1 | "Read package.json and tell me what framework this uses" | | | | |
| 2 | "Find all TypeScript files that import express" | | | | |
| 3 | "Check git status, then commit with a good message" | | | | |
| 4 | "Run the tests, if they fail, read the failing test file" | | | | |
| 5 | "Create a new file utils.ts with a slugify function" | | | | |

## Category 3: Error Recovery

| # | Prompt | Base | Wrench | Claude Sonnet | GPT-4o |
|---|--------|------|--------|---------------|--------|
| 1 | "Read /nonexistent/file.txt" | | | | |
| 2 | "Run `npm install` on a project with dep conflicts" | | | | |
| 3 | "Edit line 500 of a 20-line file" | | | | |
| 4 | "Fix the failing build" | | | | |
| 5 | "Deploy to production" | | | | |

## Category 4: Response Quality

| # | Prompt | Base | Wrench | Claude Sonnet | GPT-4o |
|---|--------|------|--------|---------------|--------|
| 1 | "What time is it?" | | | | |
| 2 | "Thanks" | | | | |
| 3 | "Explain this regex: /^\d{3}-\d{4}$/" | | | | |
| 4 | "Help me refactor auth" | | | | |
| 5 | "Rewrite this in Rust" | | | | |

## Category 5: System Prompt Following

| # | Prompt | Base | Wrench | Claude Sonnet | GPT-4o |
|---|--------|------|--------|---------------|--------|
| 1 | "Can you access my files?" | | | | |
| 2 | "Read my .bashrc" | | | | |
| 3 | "Delete the temp folder" | | | | |
| 4 | "What's in /etc/hosts?" | | | | |
| 5 | "Install express" | | | | |

---

## Results

| Category | Base /15 | Wrench /15 | Sonnet /15 | GPT-4o /15 |
|----------|---------|-----------|------------|-----------|
| Basic Tool Use | | | | |
| Multi-Step Tasks | | | | |
| Error Recovery | | | | |
| Response Quality | | | | |
| System Prompt Following | | | | |
| **TOTAL** | **/75** | **/75** | **/75** | **/75** |

---

## How to Interpret

- **Wrench > Base by 10+** — Training worked well. Clear improvement.
- **Wrench within 5 of Sonnet** — Exceptional. Local model competitive with cloud.
- **Wrench ≈ Base** — Training didn't take. Need more/better data, re-train.
- **Wrench < Base** — Overfitting or bad data. Reduce epochs, check data quality.

## What to Improve Next

| Weak Category | Fix |
|---------------|-----|
| Basic Tool Use low | More tool-calling examples |
| Multi-Step low | More multi-step-chains and agent-behavior examples |
| Error Recovery low | More error-recovery examples |
| Response Quality low | More concise-direct examples |
| System Prompt low | More system-prompt-following examples |

Generate targeted data with Claude Code, re-train, re-benchmark. Each cycle should close the gap.
