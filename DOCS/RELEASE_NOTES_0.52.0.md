# Usurper Reborn v0.52.0 - The Hook (Player Retention Update)

A comprehensive player retention and engagement update introducing 10 new systems designed to keep players coming back, reward daily play, and give online players reasons to connect with each other.

---

## 1. Daily Login Streak Rewards (New)

Logging in on consecutive days now builds a streak with escalating rewards. Missing a day resets the streak.

| Day | Reward |
|-----|--------|
| 1 | 500 gold |
| 2 | 500 gold |
| 3 | 1,000 gold + 3 healing potions |
| 7 | 2,500 gold + "Dedicated Adventurer" achievement |
| 14 | 5,000 gold + 5 of each herb type |
| 30 | 10,000 gold + "Devoted Champion" achievement |
| 60 | 25,000 gold |
| 90 | 50,000 gold + "Legendary Devotion" achievement |
| 100+ | 1,000 gold daily loyalty bonus |

The streak display shows your current streak, all-time best, and days remaining until the next milestone. Streak data persists across saves.

## 2. Boss Kill & Death Summaries (New)

After defeating a boss monster, players are shown a paste-able **Boss Kill Summary** box with:
- Player name, level, and class
- Boss name and number of combat rounds
- Total damage dealt
- Companions who fought alongside you
- XP and gold earned
- A one-line shareable version with `#UsurperReborn` hashtag

After dying, a **Death Story** tombstone summary displays:
- Character name, level, and class
- Who killed you and on which floor
- Lifetime stats (floors explored, monsters slain, total damage dealt)
- Companion names
- A shareable one-liner for bragging about your demise

Both summaries are designed to be copy-pasted for sharing on Discord, social media, or BBS message boards.

## 3. Opening Guided Quest: Captain Aldric's Mission (New)

New characters are now greeted by Captain Aldric of the Town Guard, who assigns a four-part scouting mission that guides players through the core gameplay loop:

1. **Enter the Dungeon** — First step into the dungeon triggers completion
2. **Kill Your First Monster** — Ties into the existing first kill bonus
3. **Find a Treasure Room** — Discover the loot system
4. **Return to Main Street** — Report back to Aldric for your reward

Completing all four objectives awards 500 gold (via the achievement system) and the **"First Steps"** achievement. The quest uses the existing `HintsShown` tracking system, so no new save fields are needed. Progress messages appear contextually as each objective is completed.

## 4. Achievement Broadcasts in Online Mode (New)

When a player unlocks a notable achievement in online multiplayer mode, all other connected players see a gold-starred announcement:

```
★ PlayerName has earned [Achievement Name]!
```

Only noteworthy achievements are broadcast — Gold tier or higher, high-XP-reward achievements, and specific endgame achievements (legendary, champion, world boss, god slayer, immortal). Common early-game achievements are not broadcast to avoid spam.

## 5. Weekly Power Rankings & Rival System (New)

Players in online mode now receive a weekly power ranking based on their level. On login:
- **Rank change notification**: "You moved up 3 ranks! Now #7" or "You dropped 2 ranks. Now #12"
- **Auto-assigned rival**: The system shows your rival's name and level

Rankings update every Monday. Your current rank and rival are visible on the `/health` status screen.

## 6. Monthly Blood Moon Event (New)

Every 30 in-game days, the Blood Moon rises for one full day:

- **Monsters are 50% stronger** — increased damage output
- **XP rewards doubled** (2x multiplier)
- **Gold rewards tripled** (3x multiplier)
- **Red-tinted dungeon descriptions** — "[Blood Moon] A crimson glow bathes everything in eerie red light."
- **Blood Moon indicator** in the location header showing active multipliers
- **Server broadcast** to all online players when the Blood Moon rises

The Blood Moon represents a risk/reward window — monsters hit harder, but the rewards are significantly increased for those brave enough to dungeon during the event.

## 7. NG+ World Modifiers Per Cycle (New)

Each New Game+ cycle now adds permanent world modifiers that make the game harder but more rewarding:

| Cycle | Modifier | Effect |
|-------|----------|--------|
| 2 | **Empowered Monsters** | +20% monster stats, +50% gold drops |
| 3 | **Ancient Magic** | +30% monster stats (stacks with Cycle 2) |
| 4+ | **The Convergence** | +50% monster stats, +100% gold (stacks with all) |

At Cycle 4+, all modifiers stack: monsters have 2.34x their base stats, and gold rewards are 3x normal. Active modifiers are displayed on dungeon entry and the `/health` screen.

Monster stat scaling applies to HP, Strength, Defence, and Punch. Gold multipliers apply in all three combat reward paths (single monster, multi-monster, and berserker/special).

## 8. Guild System (New — Online Mode)

A full persistent guild system for online multiplayer, backed by SQLite:

**Chat Commands:**
| Command | Description |
|---------|-------------|
| `/gcreate <name>` | Create a guild (costs 10,000 gold) |
| `/guild` | View your guild info (members, bank, XP bonus) |
| `/ginvite <player>` | Invite an online player (leader only) |
| `/gleave` | Leave your current guild |
| `/gkick <player>` | Kick a member (leader only) |
| `/ginfo <name>` | Look up any guild's info |
| `/gc <message>` | Guild chat (members only) |
| `/gbank <amount>` | Deposit gold into the guild bank |

**Guild Perks:**
- **Shared XP Bonus**: +2% per guild member, up to +10% maximum
- **Guild Bank**: Shared gold storage via `/gbank` deposits
- **Guild Chat**: Private communication channel for members

Guild invites use the existing `/accept` and `/deny` system. When the guild leader leaves, the next senior member is automatically promoted. If the last member leaves, the guild is disbanded. Guild creation is broadcast to all online players.

The XP bonus is applied in all three combat XP calculation paths.

## 9. Floor 5 Dungeon Guardian (New)

A one-time mini-boss encounter awaits players on Floor 5:

- **"A massive figure blocks the passage ahead!"** — atmospheric intro with guardian lore
- Full interactive combat against the **Dungeon Guardian** (scaled mini-boss for level 5)
- On victory: 1,000 bonus gold + **"Guardian Slayer"** achievement (Bronze, 500 XP)
- On death: normal resurrection flow, exits dungeon
- On flee: the Guardian reappears on next visit (no flag set until defeated)

This gives new players an early boss-fight milestone before the deeper dungeon challenges, reinforcing the feeling of progression.

## 10. Logout "Tomorrow's Forecast" (New)

When quitting the game, players now see 1-2 teaser messages hinting at what awaits them next session:

**Context-sensitive forecasts** (based on player state):
- Low-level players see the next dungeon floor challenge
- Herb gatherers are reminded about fresh herbs
- Low-potion players get a Healer suggestion
- Unbanked gold triggers a Bank reminder
- Early-game players are pointed to the Quest Hall

**Generic forecasts** (one always shown from a rotating pool):
- Merchant shipment rumors, dungeon treasure whispers, arena challenges, wilderness secrets, and more

The forecast appears in all three quit paths (online home sleep, online dormitory, offline quit).

---

## New Achievements

| Achievement | Trigger | Tier | Rewards |
|------------|---------|------|---------|
| First Steps | Complete Captain Aldric's Mission | Bronze | 500g |
| Guardian Slayer | Defeat the Floor 5 Dungeon Guardian | Bronze | 500g, 500 XP |
| Dedicated Adventurer | 7-day login streak | Bronze | 500g, 200 XP |
| Devoted Champion | 30-day login streak | Gold | 2,000g, 1,000 XP |
| Legendary Devotion | 90-day login streak | Platinum | 5,000g, 5,000 XP |

---

## 11. Cleric Class Improvements

The Cleric class has been enhanced with a passive healing bonus and three new party-oriented abilities to solidify its role as the premier support class.

**Divine Grace (Passive)**: All Cleric healing from both abilities and spells is increased by 25%. This applies to the existing heal abilities (Heal, Greater Heal, Divine Light, etc.) as well as all healing spells.

**New Abilities**:
- **Circle of Healing** (Level 40, 50 STA, 5-round CD) — A radiant prayer that heals the Cleric and all nearby allies. Self heals at full power; teammates receive 75% healing.
- **Beacon of Light** (Level 52, 55 STA, 6-round CD) — Become a beacon of divine light that shields and heals all allies. Grants a defense buff for 3 rounds plus healing to the entire party.
- **Holy Covenant** (Level 72, 70 STA, 6-round CD) — Invoke a sacred covenant that heals all allies and cleanses every affliction (poison, disease, curses, stuns, burns, bleeds, freezes). The ultimate group support ability.

These additions fill critical level gaps in the Cleric ability tree (previously had no abilities between levels 32-44 and 68-80) and give the class meaningful party healing capabilities that were previously absent despite being "the healer class."

---

## Bug Fixes

- **Guild creation gold not persisted** — Creating a guild deducted 10,000 gold in memory but never saved the player, so the cost was refunded on next login. Now auto-saves after deduction.
- **Guild bank deposit gold not persisted** — Depositing gold via `/gbank` updated the guild bank in SQLite but never saved the player's reduced gold balance. Now auto-saves after deposit.
- **Guild chat echoed to sender** — `/gc` messages were broadcast to all guild members including the sender, causing duplicate output. Sender now sees a "You:" confirmation instead.
- **Achievement broadcast not excluded from earner** — Achievement announcements used display name for exclusion but session keys use account name, so the player who earned the achievement would see their own broadcast. Now uses account name for exclusion.
- **Aldric quest completion missing from BBS display** — Captain Aldric's quest completion scene only appeared in the visual menu path, not the BBS/screen reader path. Added to both display methods.
- **Weekly rival never assigned** — `UpdateWeeklyRankings()` updated the rank number but never populated `RivalName` or `RivalLevel`, leaving the rival system completely non-functional. Now assigns the closest-level online player as rival each Monday.
- **Boss kill summary missing in multi-monster combat** — `ShowBossKillSummary` was only called in the legacy single-monster victory path, but all combat now routes through the multi-monster handler. Dungeon floor bosses and the Floor 5 Guardian never displayed the shareable boss kill narrative. Now triggers for any boss in the defeated monsters list.
- **Blood Moon atmosphere missing from BBS dungeon view** — The crimson glow room description only appeared in the visual display path. BBS/compact mode players now see a condensed Blood Moon indicator with multiplier info.
- **Guild commands missing from help screens** — The 7 guild slash commands (`/guild`, `/gcreate`, `/ginvite`, `/gleave`, `/gkick`, `/gc`, `/gbank`) were not listed in either the visual or screen reader help menus, making them completely undiscoverable. Added to both help display paths.
- **Weekly rankings failed during winter months** — `UpdateWeeklyRankings()` checked `DateTime.UtcNow.DayOfWeek == Monday`, but the daily reset fires at 7 PM Eastern. During EST (Nov–Mar), 7 PM EST = Tuesday 12 AM UTC, so the Monday check always failed. Now converts to Eastern time before checking day of week.
- **Guild invite sent to players already in a guild** — `/ginvite` didn't check whether the target player was already in a guild before sending the invite. The invite would silently fail on acceptance. Now checks upfront and shows an error message.
- **Blood Moon broadcast sent multiple times** — Each player's daily reset independently triggered the Blood Moon server broadcast to all online players. If 5 players reset at 7 PM, the announcement was sent 5 times. Now uses a 30-minute cooldown guard to prevent duplicate broadcasts.
- **Blood Moon indicator inaccessible to screen readers** — The `★ BLOOD MOON ★` header and daily reset announcement used Unicode star symbols that screen readers may not render. Both now show plain text `[BLOOD MOON]` format in screen reader mode.
- **NG+ monster scaling missing from 70% of encounters** — `GenerateMonsterGroup()` same-family branch (70% of dungeon encounters) created monsters via `Monster.CreateMonster()` instead of `GenerateMonster()`, bypassing the NG+ stat multiplier entirely. Only mini-boss and mixed-family encounters (30%) received the intended difficulty scaling. Now applies NG+ multiplier to all same-family monsters.
- **Aldric quest 500 gold reward never awarded** — The quest completion scene displayed "Reward: 500 gold" but never actually added the gold to the player's inventory. Now adds 500 gold in both visual and BBS display paths.
- **Dead companions listed in boss kill summary** — `ShowBossKillSummary()` displayed all teammates in the "Fought alongside" line without filtering out dead companions. Now filters by `IsAlive`.
- **Dead companions listed in death summary** — `ShowDeathSummary()` displayed all teammates in the "Their companions" line without filtering out dead companions. Now filters by `IsAlive` and hides the line entirely if all companions are dead.
- **Achievement broadcast inaccessible to screen readers** — Achievement broadcast messages used Unicode `★` star symbols unconditionally. Now shows plain text `[Achievement]` prefix in screen reader mode.
- **`/ginfo` command missing from help screens** — The `/ginfo <guild>` command for looking up any guild's info was not listed in either the visual or screen reader help menus. Added to both.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.52.0 "The Hook"; NG+ world modifier constants and helper methods (`GetNGPlusMonsterMultiplier`, `GetNGPlusGoldMultiplier`); Blood Moon event constants
- `Scripts/Core/Character.cs` — `LoginStreak`, `LongestLoginStreak`, `LastLoginDate` for daily streaks; `BloodMoonDay`, `IsBloodMoon` for Blood Moon cycle; `WeeklyRank`, `PreviousWeeklyRank`, `RivalName`, `RivalLevel` for rankings
- `Scripts/Core/GameEngine.cs` — `ProcessLoginStreak()` method with escalating rewards and achievement unlocks; weekly rank change display on login; Captain Aldric quest intro in `CreateNewGame()`; property restore for all new fields in `LoadSaveByFileName()`
- `Scripts/Systems/CombatEngine.cs` — Boss kill summary (`ShowBossKillSummary`) and death story (`ShowDeathSummary`) methods; Aldric quest kill trigger (single and multi-monster paths); Blood Moon XP/gold/damage multipliers (3 reward paths + monster damage); NG+ gold multipliers (3 reward paths); Guild XP bonus (3 XP paths); `ApplyClericPartyEffect()` for party_heal_divine, party_beacon, party_heal_cleanse effects in both single and multi-monster combat
- `Scripts/Systems/SaveDataStructures.cs` — All new save fields: login streak (3), Blood Moon (2), weekly rankings (4)
- `Scripts/Systems/SaveSystem.cs` — Serialization for all 9 new fields in save and load blocks
- `Scripts/Systems/DailySystemManager.cs` — Blood Moon cycle activation/deactivation with server broadcast; weekly rankings update (Mondays, online mode); `UpdateWeeklyRankings()` helper
- `Scripts/Systems/MonsterGenerator.cs` — NG+ cycle monster stat scaling applied after generation (HP, Strength, Defence, Punch)
- `Scripts/Systems/AchievementSystem.cs` — 5 new achievements (first_steps, guardian_slayer, dedicated_adventurer, devoted_champion, legendary_devotion); achievement broadcast to online players for Gold+ tier unlocks
- `Scripts/Systems/GuildSystem.cs` — **NEW** — Full guild system with SQLite tables (guilds, guild_members), membership cache, guild creation/joining/leaving/kicking, guild bank deposits, guild XP bonus calculation, invite system
- `Scripts/Server/MudChatSystem.cs` — 8 guild chat commands (/guild, /gcreate, /ginvite, /gleave, /gkick, /ginfo, /gc, /gbank); guild invite handling in /accept and /deny flows
- `Scripts/Server/MudServer.cs` — GuildSystem initialization on server startup
- `Scripts/Locations/DungeonLocation.cs` — Floor 5 Dungeon Guardian encounter; Aldric quest dungeon/treasure triggers; NG+ modifier display on dungeon entry; Blood Moon red-tinted room descriptions
- `Scripts/Locations/MainStreetLocation.cs` — Aldric quest completion scene; Tomorrow's Forecast on all 3 quit paths
- `Scripts/Core/GameConfig.cs` — `ClericDivineGraceBonus` constant (0.25f)
- `Scripts/Systems/ClassAbilitySystem.cs` — 3 new Cleric abilities (circle_of_healing L40, beacon_of_light L52, holy_covenant L72); Divine Grace passive applied in `CalculateAbilityResult()` healing multiplier
- `Scripts/Systems/SpellSystem.cs` — Divine Grace passive applied in `ScaleHealingEffect()` for Cleric spell healing
- `Scripts/Locations/BaseLocation.cs` — Weekly rank and rival display on `/health`; NG+ cycle modifiers on `/health`; Blood Moon status indicator in location header; Divine Grace passive display in active buffs section
