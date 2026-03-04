# Usurper Reborn v0.49.6 — Early Game Hooks

*The world was always alive. Now you'll notice it sooner.*

---

## Street Micro-Events

Main Street now shows what NPCs are actually doing. Each time you visit, there's a chance you'll see a one-liner reflecting real NPC state from the world simulation. These aren't random flavor text. They're pulled from NPC memories, relationships, and emotions.

### What You Might See

- Two NPCs who are married or in love walking together
- An NPC who recently won a fight carrying themselves with confidence
- An NPC who witnessed something traumatic looking shaken
- An NPC sporting fresh bruises from a recent attack
- Two rival NPCs eyeing each other warily
- An NPC whose emotional state shows anger, joy, sadness, or fear
- Gang members huddling together
- An NPC who has taken on an emergent role (defender, merchant, etc.)

Events only appear when there's no NPC story notification already showing, so they don't stack. The event shown is based on what's actually happening in the world simulation, not a random roll against a flavor text table.

---

## Companion Teasers

The four recruitable companions now make brief appearances at early levels, well before their actual recruitment becomes available. Each teaser is a one-time sighting tracked in your save file, so you'll only see each one once.

| Companion | Level | Location | What You See |
|-----------|-------|----------|-------------|
| Aldric | 3+ | Inn | A scarred soldier drinking alone in the far corner |
| Vex | 4+ | Main Street | A quick-fingered stranger who catches your eye and winks |
| Lyris | 5+ | Main Street | A hooded woman passing through, whispering a prayer |
| Mira | 5+ | Healer | A young woman carefully tending an injured traveler |

Teasers won't appear if the companion has already been recruited or has died.

---

## Mystery Breadcrumbs

Three small additions that hint at the deeper narrative systems without explaining anything.

### Atmospheric Dungeon Text (Floors 1-5)

On the first five dungeon floors, there's a 20% chance per room to see a brief atmospheric line hinting that something is down there. Strange carvings, shadows that move on their own, the faint taste of salt in the air. Nothing explained, just enough to make you curious about what's deeper.

### Awakening Display

The `/health` command now shows your Awakening status. New characters start at "Dormant (0/7)." The display doesn't explain what Awakening is or how to advance it. That's for you to figure out.

Awakening tiers: Dormant, Stirring, Aware, Seeking, Illuminated, Transcendent, Enlightened, Awakened.

### The Deep Call (New Dream)

A new early-game dream can trigger at levels 1-10. You're standing at the edge of an underground ocean. Something is down there. It knows you're here.

---

## Docker Self-Hosting

SysOps can now host their own Usurper Reborn MUD server using Docker. A 3-container stack provides the game server, web proxy with dashboards, and nginx reverse proxy.

```bash
git clone https://github.com/jknight/usurper-reborn.git
cd usurper-reborn
docker compose up -d
```

- **Game server** on port 4000 (raw TCP for BBS/MUD client connections)
- **Web interface** on port 80 (browser play via xterm.js, live stats, admin/balance/NPC dashboards)
- Shared SQLite database via Docker volume
- Minimal generic landing page included (sysops can mount their own)
- Full documentation in `DOCS/DOCKER.md`

### New Server Flags

| Flag | Description |
|------|-------------|
| `--auto-provision` | Trusted auth connections (no password) auto-create accounts if username doesn't exist. Designed for BBS passthrough where the BBS handles authentication. |
| `--log-stdout` | Route all debug log output to stdout instead of `logs/debug.log`. For container log aggregation (docker logs, CloudWatch, etc.) |

---

## BBS Online Passthrough

BBS door players choosing `[O]nline Play` now get seamless authentication. Instead of seeing a login/register screen, they're automatically logged in using their BBS username from the drop file. The server creates their account on first connection if `--auto-provision` is enabled.

This works for any MUD server the SysOp points to — the official server or a self-hosted Docker instance.

---

## SysOp Configurable Online Server

The SysOp Console now has an `[S] Server` option to configure the online server address and port. SysOps running their own MUD server (via Docker or otherwise) can point their BBS game installation at their own server instead of the default `play.usurper-reborn.net:4000`. The setting persists in `sysop_config.json`.

---

## Bug Fixes

- **World Boss Spell Casting** — Magicians and Sages couldn't cast spells during world boss fights even with a Staff equipped. The `CanCastSpell()` weapon check filtered out all spells, but the error message said "Not enough mana" instead of telling the player they need a Staff. Now shows the correct weapon requirement error before listing spells
- **Treasure Room Flee Exploit** — Fleeing from combat in a treasure room still marked the room as cleared and let you collect the treasure. Room clearing now requires actually winning the fight (`CombatOutcome.Victory`), not just surviving
- **Defense Stacking Invulnerability** — Players with enough active buffs (Well-Rested, God Slayer, Song, Herbs, Lover's Bliss, Divine Blessing, etc.) could stack multiplicative defense bonuses until all incoming damage was reduced to 1. Minimum damage is now 5% of the monster's attack power instead of a flat 1, so even heavily buffed characters still take meaningful hits
- **Fireball Burn Shows Poison Message** — Fire spell DoT damage displayed "Poison burns [monster] for X damage!" because fire effects reused the poison system without tracking the damage source. Fire DoT now shows "Fire burns [monster] for X damage!" in red and "consumed by flames!" on kill instead of the poison-themed messages
- **Legacy Loot Missing WeaponType** — Dungeon loot items acquired before v0.49.0 (when InferWeaponType was added) were saved with WeaponType=None. This caused Bard instruments, Ranger bows, Assassin daggers, and Mage staves from dungeon loot to fail weapon requirement checks for abilities and spells, even though the item name clearly identified the weapon type. Equipment now gets its WeaponType inferred from its name on load if it was saved as None
- **BBS Online Login Credential Leak** — When a BBS user connected to online mode and saved their credentials, the credentials file was stored in the shared game installation directory. Any subsequent BBS user would auto-login as the previous user's online account. Credential saving and auto-login are now completely disabled in BBS door mode since multiple users share the same game binary

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.49.6; `OnlineServerAddress` and `OnlineServerPort` static properties for configurable online server
- `Scripts/Core/Monster.cs` — `IsBurning` flag to distinguish fire DoT from poison DoT
- `Scripts/Locations/MainStreetLocation.cs` — `GenerateStreetMicroEvent()` method with 9-priority NPC state check; display call in `DisplayLocation()`; Vex companion teaser (level 4+); Lyris companion teaser (level 5+)
- `Scripts/Locations/InnLocation.cs` — Aldric companion teaser (level 3+) in `DisplayLocation()`
- `Scripts/Locations/HealerLocation.cs` — Mira companion teaser (level 5+) in `DisplayLocation()`
- `Scripts/Locations/DungeonLocation.cs` — 8 atmospheric breadcrumb texts for floors 1-5 (20% chance per room); flee from combat no longer clears the room
- `Scripts/Locations/BaseLocation.cs` — Awakening status display in `ShowHealthStatus()`
- `Scripts/Systems/HintSystem.cs` — 4 companion teaser hint constants
- `Scripts/Systems/DreamSystem.cs` — New "The Deep Call" narrative dream (levels 1-10)
- `Scripts/Systems/WorldBossSystem.cs` — Staff weapon requirement check with proper error message before spell listing
- `Scripts/Systems/CombatEngine.cs` — Minimum damage 5% of monster attack; fire DoT burn messages
- `Scripts/Core/GameEngine.cs` — Legacy WeaponType migration on equipment load
- `Scripts/Systems/PlayerCharacterLoader.cs` — Legacy WeaponType migration for echo characters
- `Scripts/Systems/OnlinePlaySystem.cs` — BBS passthrough auth (direct TCP with `AUTH:username:BBS`); configurable server from `GameConfig`; credential saving disabled in BBS door mode; BBS-specific connection info text
- `Scripts/BBS/DoorMode.cs` — `--auto-provision` flag parsing and `AutoProvision` property; `--log-stdout` flag parsing
- `Scripts/Systems/DebugLogger.cs` — `LogToStdout` static property; stdout write path in `FlushQueue()`; conditional directory creation
- `Scripts/Systems/SqlSaveBackend.cs` — `AutoProvisionPlayer()` method for trusted auth account creation
- `Scripts/Server/MudServer.cs` — Auto-provision check in trusted auth path before session start
- `Scripts/Systems/SysOpConfigSystem.cs` — `OnlineServerAddress` and `OnlineServerPort` in `SysOpConfig` class; wired into Apply/Sync methods
- `Scripts/Systems/SysOpConsoleManager.cs` — `[S] Server` menu option in both local and online console; `SetOnlineServer()` method
- `web/ssh-proxy.js` — `MUD_HOST` and `DB_PATH` environment variable support for Docker
- `Dockerfile` — **NEW** — Multi-stage game server build (SDK → runtime-deps)
- `docker/web/Dockerfile` — **NEW** — Node.js web proxy container
- `docker/web/index.html` — **NEW** — Minimal generic landing page with xterm.js terminal and live stats
- `docker/nginx/Dockerfile` — **NEW** — Nginx reverse proxy container
- `docker/nginx/nginx.conf` — **NEW** — Reverse proxy config with WebSocket upgrade and SSE support
- `docker-compose.yml` — **NEW** — 3-service stack (game, web, nginx) with shared volume
- `.dockerignore` — **NEW** — Build context exclusions
- `DOCS/DOCKER.md` — **NEW** — Complete Docker deployment guide
