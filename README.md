# FABLE 5 OPTIMIZER

A Claude Code skill that loads at session start and enforces five token cost-reduction strategies for Fable 5 sessions. Optimized for developers who want to cut API costs without changing how they work.

## What it does

Once installed, every Claude Code session automatically applies:

1. **Prompt caching enforcement** — flags missing `cache_control` on API code (saves up to 90% on input tokens)
2. **Lean CLAUDE.md pruning** — offers minimal context summaries at task boundaries
3. **Output verbosity cap** — no preamble, no trailing summaries, right-sized `max_tokens` on API calls
4. **Context clearing prompts** — offers handoff summaries on long threads (20+ turns)
5. **Model routing** — recommends Opus 4.8, Sonnet 4.6, or Haiku 4.5 when Fable 5 is overkill

### Model routing table

| Task type | Recommended model | Input price | Output price |
|-----------|------------------|-------------|--------------|
| Complex reasoning, multi-file refactoring, long-horizon agentic tasks | Opus 4.8 (`claude-opus-4-8`) | $5/MTok | $25/MTok |
| Standard code generation, explanations, moderate complexity | Sonnet 4.6 (`claude-sonnet-4-6`) | $3/MTok | $15/MTok |
| Classification, data extraction, boilerplate, basic summaries | Haiku 4.5 (`claude-haiku-4-5`) | $1/MTok | $5/MTok |
| Frontier reasoning, explicit user request only | Fable 5 (`claude-fable-5`) | $10/MTok | $50/MTok |

## Projected cost savings

Calculated from documented Anthropic API pricing (Fable 5: $10/$50 per MTok input/output). All figures assume Fable 5 as the unoptimised baseline.

| Rule | Scenario | Saving |
|------|----------|--------|
| Prompt caching | 10-turn session with 4k-token system prompt | **87%** per session |
| Model routing — classification | Fable 5 → Haiku 4.5 | **90%** per call |
| Model routing — code generation | Fable 5 → Sonnet 4.6 | **70%** per call |
| Model routing — complex reasoning | Fable 5 → Opus 4.8 | **50%** per call |
| Verbosity cap | Typical padded reply trimmed | **26% fewer output tokens** |
| Context clearing | Turns 21–40 after session handoff | **72%** vs stale context |

**Combined projection — 1 developer-day (8 sessions, mixed workload):**

| | Cost |
|-|------|
| Baseline (Fable 5, no optimisation) | $16.39 |
| With token-optimizer skill | $4.39 |
| **Saving** | **73% (~$240/month per developer)** |

> These figures are calculated from API pricing, not live-measured. Real savings depend on your prompt sizes, session lengths, and task mix. Enable the skill and check your [Anthropic usage dashboard](https://console.anthropic.com) to measure actual impact.

## Install

### 1. Clone into your plugins directory

```bash
git clone https://github.com/yashveer-ux/fable5-optimizer ~/.claude/plugins/token-optimizer-skill
```

### 2. Add the SessionStart hook

Open `~/.claude/settings.json` (create it if it doesn't exist) and add:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "skill",
        "skill": "token-optimizer-skill:token-optimizer"
      }
    ]
  }
}
```

If you already have a `hooks.SessionStart` array, append the object to it — don't replace the array.

### 3. Start a new Claude Code session

The skill activates automatically. No slash command needed.

## Verification

After installing, open a new session and paste this API code snippet:

```python
import anthropic

client = anthropic.Anthropic()
response = client.messages.create(
    model="claude-fable-5",
    max_tokens=1024,
    system="You are a helpful assistant.",
    messages=[{"role": "user", "content": "Hello"}]
)
```

The skill is working if Claude immediately flags the missing `cache_control` on the system prompt block.

## Uninstall

Remove the hook entry from `~/.claude/settings.json` and delete `~/.claude/plugins/token-optimizer-skill/`.

## Requirements

- Claude Code with superpowers plugin support
- Fable 5 (`claude-fable-5`) as your active model
