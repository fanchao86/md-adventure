# Markdown Adventure Engine

> **A single-file text adventure game framework powered by LLM**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Markdown](https://img.shields.io/badge/Format-Markdown-blue)]()
[![LLM-Powered](https://img.shields.io/badge/Powered%20by-LLM-orange)]()

## Overview

**Markdown Adventure Engine** — one Markdown file is a complete game. The LLM serves as both the narrative engine and rules arbiter, maintaining world consistency through structured blocks.

### Core Features

- **Single-file architecture**: Everything in one `.md` file, no database needed
- **Infinite worlds**: Fantasy, sci-fi, post-apocalypse, history — any world you can imagine
- **Dynamic narrative**: LLM-powered Narrator creates immersive stories
- **Structured state management**: Snapshot as the sole source of truth, preventing narrative drift
- **Five-layer anti-drift mechanism**: Meta-instruction lock / XML tag anchoring / Per-turn self-check output / Self-contained snapshot / Context refresh
- **Free exploration**: No forced main quest, players decide their own path
- **Real consequences**: Actions have lasting effects, including adventure termination
- **AI-first design**: Template optimized for AI parsability, minimal token cost

## Quick Start

### Prerequisites

- Modern LLM (GPT-4, Claude or similar; function calling / tool use support recommended)
- Markdown editor (VS Code, Obsidian, etc.)

### Starting a Game

1. **Copy the template**: Use `markdown-game-template-en.md` as your starting point
2. **Configure snapshot storage** (optional):
   - Register at [jsonbin.io](https://jsonbin.io), get an API Key
   - Create a bin, note the BIN_ID
   - Fill in `snapshot_store` and `X-Master-Key` in the template header yaml
3. **Initialize with LLM**: Share the template and answer two questions:
   - "What kind of world do you want?" (fantasy, sci-fi, historical, etc.)
   - "Who are you?" (name, race, background)
4. **Begin your adventure**: The LLM will generate the world and start the adventure

### Example Session

```
Player: I want a medieval fantasy world with magic and dragons.
Player: I am Elara, a half-elf ranger searching for ancient artifacts.

Narrator: [Generates world setting, factions, and starting scene]
Narrator: You stand at the edge of the Whispering Woods...
```

## Project Structure

```
md-adventure/
├── README.md                    # Chinese documentation (this file's counterpart)
├── README-en.md                 # English documentation
├── markdown-game-template.md    # Chinese game template
└── markdown-game-template-en.md # English game template
```

### Template Sections

| Section | Purpose | Update Frequency |
|---------|---------|-----------------|
| Permanent Constraints `<Permanent_Constraints>` | Meta-instruction lock, highest priority rules that cannot be overridden | Never |
| Rule Quick Ref `<Rule_Quick_Ref>` | Ultra-compressed core rules, must read before each reply | Rarely |
| Core Bans `<Core_Bans>` | 6 inviolable behavioral red lines | Never |
| System Protocol | LLM behavior constraints (detail/correction/self-check) | Rarely |
| World Setting | Background, factions, geography, endgame | Occasionally |
| Game Rules | Resolution/encounters/energy/economy/growth | Fixed |
| Character | Attributes, items, gold, skills | On change |
| Snapshot `<Snapshot>` | Sole source of truth (self-contained design), can sync to cloud | After each interaction |
| Current Scene | Location, atmosphere, perception | Each turn |
| History Log | Reverse-chronological event stream | Each turn |
| World State | NPCs, faction dynamics, explored/unexplored | After key events |

## Design Philosophy

1. **Simplicity**: No installation, just Markdown
2. **Portability**: GitHub, local, cloud storage — works anywhere
3. **Transparency**: Complete game state is visible and editable
4. **Anti-drift**: Five layers of defense (meta-instructions/XML tags/per-turn self-check/self-contained snapshot/context refresh), preventing LLM memory drift
5. **Context management**: Structured sections + milestone compression + proactive refresh, maintaining consistency in long sessions

## Anti-Drift Mechanism (v1.1 Core Upgrade)

The biggest pain point in long LLM sessions is context loss and rule forgetting. v1.1 introduces a five-layer anti-drift mechanism:

| Layer | Technique | Implementation | Principle |
|-------|-----------|----------------|-----------|
| Layer 1 | Meta-instruction lock | `<Permanent_Constraints>` tag wrapping 6 permanent constraints | Declares "always active regardless of conversation length", model treats as highest priority |
| Layer 2 | XML tag anchoring | `<Rule_Quick_Ref>` / `<Core_Bans>` / `<Snapshot>` | Models are more sensitive to structured tags than plain text; tags enable easy reference |
| Layer 3 | Per-turn self-check output | Mandatory "rule checklist" at end of each reply | Forces model to re-read rules each turn, preventing entry into cold data zone |
| Layer 4 | Self-contained snapshot | `<Snapshot>` includes attributes/items/gold/skills/goals/difficulty | Game can be restored from snapshot alone, without dialogue history |
| Layer 5 | Context refresh | Proactive compression every 10 turns + player can manually "refresh"/"calibrate" | Rewrites rules from "distant memory" to "recent memory", equivalent to context reset |

### Player Actions

When you notice the LLM starting to "ramble", input any of these commands:
- `refresh` / `calibrate`

The Narrator will execute the refresh process: rewrite rules to latest position → compress history → output corrected complete snapshot.

## Game Mechanics

### Turn System
- Effective in-world action → turn+1; Chat/query/clarification does not consume
- Chapter transitions can display complete data without extra +1

### Action Resolution
- Certain (walk/pick up/speak) → direct success
- Risky (climb/cross/persuade) → five-tier resolution (great success/success/barely/failure/critical failure)
- Resolution matrix: Attribute value vs Difficulty level (3/5/7/9), equipment +1~2, situation -1~2

### Encounter System
- Turn-based: Player → resolve → situational response → loop
- Cost: 1+attribute÷2, great success ×1.5
- Resist: Dodge(AGI)/Block(CON)/Armor flat reduction (1-4)
- 4 Challenge tiers: Light → Medium → Severe → Extreme
- Negative status: Fatigue/Confused/Disarmed/Staggered

### Survival System
- Stamina: CON×2, zero = adventure terminated, rest +1/turn
- Energy: 100→-1 per turn, ≤30/-1 attribute, ≤10/-2 attribute + movement restricted, 0→stamina -2/turn
- Economy: GP currency, haggling requires CHA resolution
- GP cannot go negative (insufficient funds: cannot purchase)

### NPC System
- NPCs have autonomous behavioral logic
- World continues running, news spreads over time
- Attitude shifts dynamically with player behavior

### Growth
- Triggers: overcome severe challenge/complete commission/discover truth/explore new area/every 10 turns
- Amount: Attribute +1 or 1 new skill, choose only one per trigger
- 7 preset skills: Block/Track/Herblore/Lockpick/Tame/Insight/Cook

## Advanced Usage

### Snapshot Cloud Sync

Game state (snapshot) can be synced to the cloud for cross-session persistence and multi-device recovery.

**Setup (jsonbin.io):**
1. Register at https://jsonbin.io → get API Key
2. Create bin → note BIN_ID
3. Fill in template header yaml:
   ```yaml
   snapshot_store: "https://api.jsonbin.io/v3/b/{BIN_ID}"
   snapshot_method: PUT
   ```
4. LLM automatically PUTs snapshot JSON to the endpoint after each interaction

**LLM Call Example (via function calling / tool use):**
```
PUT https://api.jsonbin.io/v3/b/{BIN_ID}
Headers: Content-Type: application/json, X-Master-Key: {API_KEY}
Body: {"phase":"playing","turn":5,"location":"Whispering Woods","stamina":"8/10","energy":"72/100","status":["fatigued"],"keyNpc":["Old Hunter"],"conflict":["Wolf pack approaching"],"recent3":["Hunted hare","Found ruin entrance","Encountered wolves"]}
```

**Alternative Storage Options:**

| Option | Pros | Cons |
|--------|------|------|
| jsonbin.io | Zero config, pure REST | Limited free tier |
| Supabase | Query/history support | Requires table setup |
| GitHub Gist | Version history | Requires token |
| CloudBase | WeChat ecosystem | Requires login |

### Context Management
Long session overflow prevention:
1. Automatic log compression (every 10 turns)
2. Simplify explored areas on scene change
3. LLM prioritizes reading `<Rule_Quick_Ref>` + `<Snapshot>` + current scene + world state
4. Proactive context refresh every 10 turns (rewrite rules to latest position)

### Milestone Compression (every 10 turns)
- Archive snapshot to log
- Compress old logs into structured summary (key events/important changes/goal clues)
- Simplify world state and explored areas
- Rewrite `<Rule_Quick_Ref>` and `<Snapshot>` to latest position
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
