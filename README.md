# Hookdeck Skill

[![Author](https://img.shields.io/badge/Author-Daniel_Rudaev-000000?style=flat)](https://github.com/daniel-rudaev)
[![Studio](https://img.shields.io/badge/Studio-D1DX-000000?style=flat)](https://d1dx.com)
[![Hookdeck](https://img.shields.io/badge/Hookdeck-Skill-6366F1?style=flat&logo=webhook&logoColor=white)](https://hookdeck.com)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat)](./LICENSE)

A complete reference for the Hookdeck webhook proxy API — covering event monitoring, request inspection, bulk retries, connection management, sources and destinations, filters, transformations, retry configuration, and failure investigation patterns. Built from real production work at [D1DX](https://d1dx.com).

## What's Included

| Topic | What it covers |
|-------|---------------|
| API Base | Base URL, auth header, API version (`2025-01-01`) |
| Events | List by status/response code/time range/connection, get details, single retry, mute |
| Requests | Original incoming webhook inspection, request vs event distinction |
| Bulk Operations | Bulk retry by status, response code, and time range; async batch status polling |
| Connections | List, create, pause/unpause connections |
| Sources & Destinations | List, update destination URL |
| Filters & Transformations | Connection-level payload filtering, transformation rules |
| Retry Configuration | Custom retry strategies (linear/exponential), attempt counts, intervals |
| Investigation Patterns | Diagnose failed webhooks end-to-end, count failures by HTTP status code |
| Critical Gotchas | No MCP server, request vs event ID confusion, bulk async model, pause vs disable behavior, HTTPS requirement |

## Install

### Claude Code

```bash
git clone https://github.com/D1DX/hookdeck-skill.git
cp -r hookdeck-skill ~/.claude/skills/hookdeck
```

Or as a git submodule:

```bash
git submodule add https://github.com/D1DX/hookdeck-skill.git path/to/skills/hookdeck
```

### Other AI Agents

Copy `SKILL.md` (and supporting files) into your agent's prompt or knowledge directory. The skill is structured markdown — works with any LLM agent that reads reference files.

## Structure

```
hookdeck-skill/
├── SKILL.md    — Main skill file
└── README.md   — This file
```

## Sources

Distilled from using Hookdeck as a webhook proxy layer in front of n8n — handling ingestion from Chatwoot, WAHA, and other platforms. Covers patterns for bulk failure recovery, connection management, and payload inspection developed through production incident response.

## Credits

Built by [Daniel Rudaev](https://github.com/daniel-rudaev) at [D1DX](https://d1dx.com).

## License

MIT License — Copyright (c) 2026 Daniel Rudaev @ D1DX
