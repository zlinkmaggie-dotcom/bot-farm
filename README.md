# Bot Farm — Config-Template Chatbot Production Line

> A shared engine that spins up Telegram AI character bots from a config template — each with long-term memory, scene state and voice. 5 characters built so far: 3 on the original standalone scripts, 2 on the shared engine.

## The Problem

The first bots were each a separate multi-thousand-line `bot.py` with character details hard-coded into the script. Changing one personality trait meant editing code in several places — and every new character started from a copy-paste.

## What I Built

**One shared engine, zero per-bot code.** All bots on the engine run the same `engine/`. Creating a character is `newbot.py` copying a template directory containing:

- `config.yaml` — bot token, model selection, feature flags
- Markdown files — persona, world, story, memory, beats, tail

New character = edit config + write markdown. No code changes.

**Fully LLM-powered, not rule-based.** Main dialogue runs on DeepSeek v4-pro through an Anthropic-compatible interface (the engine also supports Anthropic and OpenAI endpoints). Voice uses MiniMax TTS; speech-to-text and image understanding go through Alibaba DashScope; location tracking and memory extraction each get their own dedicated model calls.

**Long-term memory and scene state.** Each bot keeps per-user memory and tracks conversation state across sessions, backed by SQLite.

**Production operations.** `check.py` health checks, per-instance systemd units, nightly cron backups (keep 7), and log hardening after a token-leak incident.

## Tech Stack

Python · Telegram Bot API · DeepSeek v4 (Anthropic-compatible SDK) · MiniMax TTS · Alibaba DashScope (ASR, Qwen-VL) · SQLite · systemd · Linux (Ubuntu, Vultr VPS)

## Project Layout

```
bot-farm/
├── engine/                 # shared runtime — never copied per bot
│   ├── llm.py              # provider-agnostic LLM calls (DeepSeek / Anthropic / OpenAI)
│   ├── state.py            # scene and location state tracking
│   ├── memory.py           # long-term memory extraction
│   └── media.py            # TTS, speech-to-text, image understanding
├── bots/
│   ├── _template/          # copied by newbot.py for each character
│   │   ├── config.yaml     # token, model, feature flags
│   │   ├── persona.md
│   │   ├── world.md
│   │   ├── story.md
│   │   ├── memory.md
│   │   ├── beats.md
│   │   ├── tail.md
│   │   └── state.json
│   └── <name>/             # one directory per character
├── tools/
│   ├── check.py            # health checks
│   └── audit.py            # audit script
├── run.py                  # starts one bot instance
└── newbot.py               # scaffolds a new character from bots/_template/
```

## Results

- 5 characters built (companion characters, a Japanese speaking tutor): 3 on the original standalone scripts, 2 on the shared engine
- New character lead time: from "edit code in three places" to "fill config + write markdown"
- Every incident (duplicate processes racing for one token, leaked tokens in logs) written up as a runbook entry

## Status

Shared engine deployed in September 2026; characters launched and iterated since 2026.

Source code will be added after sanitisation.

## Contact

Open to custom character-bot builds and engine set-ups.

Maggie Yue · Hangzhou Zhenbao Trading Co., Ltd. · [sales@zhenbaotrading.com](mailto:sales@zhenbaotrading.com)
