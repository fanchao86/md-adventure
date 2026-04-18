# Markdown Adventure Engine

> **A single-file text adventure game framework powered by LLM**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Markdown](https://img.shields.io/badge/Format-Markdown-blue)]()
[![LLM-Powered](https://img.shields.io/badge/Powered%20by-LLM-orange)]()

**[中文文档](README.md)**

## Overview

**Markdown Adventure Engine** — one Markdown file is a complete game. The LLM serves as both the narrative engine and rules arbiter, maintaining world consistency through structured blocks.

### Core Features

- **Single-file architecture**: Everything in one `.md` file, no database needed
- **Infinite worlds**: Fantasy, sci-fi, post-apocalypse, history — any world you can imagine
- **Dynamic narrative**: LLM-powered Narrator creates immersive stories
- **Structured state management**: Snapshot as the sole source of truth, preventing narrative drift
- **Multi-layer anti-drift mechanism**: Permanent constraints / Quick ref anchoring / Per-turn self-check / Self-contained snapshot / Context refresh
- **Free exploration**: No forced main quest, players decide their own path
- **Real consequences**: Actions have lasting effects, including adventure ending
- **AI-first design**: Template optimized for AI parsability, minimal token cost

## Quick Start

### Prerequisites

- Modern LLM (DeepSeek, GPT-4, Claude, etc.)
- No software installation required

### Starting a Game

1. **Copy the template**: Open `markdown-game-template.md` (Chinese) or `markdown-game-template-en.md` (English), copy all content
2. **Paste into LLM chat**: Paste the template into any LLM's chat window — DeepSeek, GPT, Claude, etc.
3. **Answer two questions**: The LLM will ask you —
   - "What kind of world do you want?" (fantasy, sci-fi, post-apocalypse, historical, etc.)
   - "Who are you?" (name, race, class, background)
4. **Begin your adventure**: The LLM will generate the world and start the narrative

### Example Session

```
Player: I want a medieval fantasy world with special abilities and dragons.
Player: I am Elara, a half-elf ranger searching for ancient artifacts.

Narrator: [Generates world setting, factions, and starting scene]
Narrator: You stand at the edge of the Whispering Woods...
```

## Project Structure

```
md-adventure/
├── README.md                    # Chinese documentation
├── README-en.md                 # English documentation (this file)
├── markdown-game-template.md    # Chinese game template
└── markdown-game-template-en.md # English game template
```

### Template Sections

| Section | Purpose | Update Frequency |
|---------|---------|-----------------|
| Permanent Constraints | Highest priority rules, cannot be overridden | Never |
| Quick Ref | Ultra-compressed core rules, must read before each reply | Rarely |
| Snapshot | Sole source of truth (self-contained design) | After each interaction |
| Setup Flow | Initialize / build world / character template | At initialization |
| Game Flow | Self-check / output modules / command list | Fixed |
| Game Rules | Resolution / encounters / survival / growth | Fixed |

## Design Philosophy

1. **Simplicity**: No installation, just Markdown
2. **Portability**: GitHub, local, cloud storage — works anywhere
3. **Transparency**: Complete game state is visible and editable
4. **Anti-drift**: Multi-layer defense (permanent constraints / quick ref / per-turn self-check / self-contained snapshot / context refresh), preventing LLM memory drift
5. **Context management**: Structured sections + milestone compression + proactive refresh, maintaining consistency in long sessions

## Anti-Drift Mechanism

The biggest pain point in long LLM sessions is context loss and rule forgetting. This template introduces a multi-layer anti-drift mechanism:

| Layer | Technique | Principle |
|-------|-----------|-----------|
| Layer 1 | Permanent constraints table | Declares "must/must not" constraints, model treats as highest priority |
| Layer 2 | Quick ref anchoring | Structured table format, models are more sensitive to tables than plain text |
| Layer 3 | Per-turn self-check output | Mandatory 8-item self-check before each reply, preventing entry into cold data zone |
| Layer 4 | Self-contained snapshot | Snapshot contains complete game state, game can be restored from snapshot alone |
| Layer 5 | Context refresh | Proactive compression every 10 turns + player can manually "refresh"/"calibrate" |

### Player Actions

When you notice the LLM starting to "ramble", input any of these commands:
- `refresh` / `calibrate`

The Narrator will execute the refresh process: rewrite rules to latest position → compress history → output corrected complete snapshot.

## Game Mechanics

### Turn System
- Effective in-world action → turn+1; Chat/query/clarification does not consume
- Chapter transitions do not add +1

### Action Resolution
- Certain (walk/pick up/speak) → direct success
- Risky (climb/cross/persuade) → five-tier resolution (great success/success/barely/setback/severe setback)
- Resolution matrix: Attribute value vs Difficulty level (3/5/7/9), equipment +1~2, situation -1~2

### Encounter System
- Turn-based: Player → resolve → situational response → loop
- Cost: Determined by action type and attribute, great success halves cost
- Defense: Dodge(agility) / Parry(constitution) / Armor reduces damage
- 3 Challenge tiers: Normal → Dangerous → Severe
- Conditions: Fatigue/Confused/Disarmed/Staggered

### Survival System
- Stamina: CON×2, depleted = adventure ends, rest or items can restore
- Energy: 100→-1 per turn (tense: additional -1), low energy → attribute penalty, depleted → stamina -2/turn
- Economy: Currency, buy at price/sell at half, haggling requires CHA resolution, cannot go negative

### NPC System
- NPCs have autonomous behavioral logic
- World continues running, news spreads over time
- Attitude shifts dynamically with player behavior

### Growth
- Triggers: overcome major challenge/complete commission/discover truth/explore new area/every 10 turns
- Amount: Attribute +1 or 1 new skill, choose only one per trigger
- 7 preset skills: Parry/Track/Herblore/Decipher/Tame/Insight/Cook

## Advanced Usage

### Snapshot Cloud Sync

Game state (snapshot) can be synced to the cloud for cross-session persistence and multi-device recovery. Supports any REST API endpoint — configure `snapshot_store` and authentication in the template header yaml.

**Storage Options:**

| Option | Pros | Cons |
|--------|------|------|
| Supabase | Query/history support | Requires table setup |
| GitHub Gist | Version history | Requires token |
| CloudBase | WeChat ecosystem | Requires login |

### Context Management
Long session overflow prevention:
1. Automatic log compression (every 10 turns)
2. Simplify explored areas on scene change
3. LLM prioritizes reading Quick Ref + Snapshot + current scene + world state
4. Proactive context refresh every 10 turns (rewrite rules to latest position)

### Milestone Compression (every 10 turns)
- Archive snapshot to log
- Compress old logs into structured summary (key events/important changes/goal clues)
- Simplify world state and explored areas
- Rewrite quick ref and snapshot to latest position
- Notify player

### Customization Options
- Multiplayer mode: Add multiple character cards
- Module system: Split world setting into separate `.md` files
- Difficulty adjustment: Modify difficulty thresholds and resolution matrix
- Custom mechanics: Crafting, relationships, reputation, etc.

## Use Cases

- Solo RPG gaming
- Creative writing tool
- Game design prototyping
- Education (historical simulation, language learning)
- Team building

## Contributing

Contributions welcome:
- Additional template variants (horror, mystery, romance)
- Translations into other languages
- Automation tools (turn counters, state trackers)
- Integration with specific LLM platforms
- Community-created world modules

## License

MIT License — use, modify, and share freely.

## Acknowledgments

Inspired by: Traditional tabletop RPGs (D&D, Pathfinder), text adventure games (Zork), choose-your-own-adventure books, modern AI-assisted narrative tools

---

**Ready to start your adventure?** Copy the template, fire up your favorite LLM, and let the journey begin!
