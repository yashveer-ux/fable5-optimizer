# token-optimizer-skill

A Claude Code skill that loads at session start and enforces five token cost-reduction strategies for Fable 5 sessions. Optimized for developers who want to cut API costs without changing how they work.

## What it does

Once installed, every Claude Code session automatically applies:

1. **Prompt caching enforcement** — flags missing `cache_control` on API code (saves up to 90% on input tokens)
2. **Lean CLAUDE.md pruning** — offers minimal context summaries at task boundaries
3. **Output verbosity cap** — no preamble, no trailing summaries, right-sized `max_tokens` on API calls
4. **Context clearing prompts** — offers handoff summaries on long threads (20+ turns)
5. **Model routing** — recommends Opus 4.8, Sonnet 4.6, or Haiku 4.5 when Fable 5 is overkill

## Install

### 1. Clone into your plugins directory

```bash
git clone https://github.com/<your-username>/token-optimizer-skill ~/.claude/plugins/token-optimizer-skill
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
