# Usurper Reborn v0.51.1 - Companion Durability & Online Language Fix

## Companion Survivability Buff

Companions were dying too frequently in higher-level dungeons, especially when facing 5+ monsters in multi-mob encounters. This patch significantly improves companion durability across the board:

### HP Scaling Improvements
- **All companions** gain more HP per level: base HP gain increased from 8-20 to 12-24 per level
- **Tiered scaling**: HP gain increases by +1 for every 10 character levels (e.g., level 50 companions gain +5 extra HP/level)
- **Tank companions** (Aldric): bonus HP per level increased from +5 to +8
- **Healer companions** (Mira): now gain +2 bonus HP per level (was 0)
- **Hybrid companions**: now gain +1 bonus HP per level (was 0)
- **Bard companions** (Melodia): now gain +1 bonus HP per level (was 0)

### Defense Scaling
- **All non-tank companions** now gain +1 defense per level (previously had zero defense scaling)
- **Tank companions**: defense per level increased from +2 to +3

### Boss Damage Reduction
- Boss normal attack minimum damage against companions reduced: `level * 3` → `level * 1.5`
- Boss ability minimum damage against companions reduced: `level * 2` → `level * 1.5`
- Old God custom ability minimum damage against companions reduced: `level * 3` → `level * 2`

### Multi-Hit Damage Reduction (New)
When a companion takes multiple hits in the same combat round (common in 5+ mob fights), each additional hit deals reduced damage:
- 2nd hit: -25% damage
- 3rd+ hits: -50% damage (cap)

This prevents the "focus fire death spiral" where all monsters pile onto a single companion.

## Online Language Switching Fix

Fixed a bug where changing language from the `~` preferences menu in online/MUD mode didn't take effect for location menus. The root cause was .NET's `AsyncLocal<T>` copy-on-write semantics — modifications in awaited async methods don't propagate back to the caller.

**Fix**: Language and CompactMode preferences are now stored as properties on the `SessionContext` reference object (which IS shared across async scopes) instead of separate `AsyncLocal<T>` fields. Changes in any awaited method now correctly propagate back to the caller.

## MUD Prompt Localization Fix

The `look` command hint in the MUD prompt was being translated (e.g., "mirar" in Spanish), but `look` is a literal command that must be typed as-is. Now displays: `look para redibujar` (hardcoded command + localized explanation).

## Alchemist Overhaul

### Ability Tree Expanded
The Alchemist had a severe gap — only `throw_bomb` (level 1) and `smoke_bomb` (level 16) for the first 24 levels, leaving players with no new tools during a critical progression window. This patch fills in the ability tree with 10 new abilities and moves `acid_splash` earlier.

**`acid_splash` moved from level 24 → level 14**, giving Alchemists their first damage upgrade 10 levels sooner.

**10 new abilities added** spanning levels 6–94:

| Level | Ability | Description |
|---|---|---|
| 6 | **Stimulant Brew** | Party-wide ATK buff (+20, teammates +15) for 4 rounds |
| 20 | **Healing Mist** | Party heal — self 65 HP, teammates ~49 HP |
| 26 | **Antidote Bomb** | Cures poison, disease, and status effects on entire party |
| 32 | **Explosive Flask** | AoE attack hitting all enemies for 75 damage |
| 40 | **Smoke Screen** | Party-wide DEF buff (+40) for 3 rounds |
| 54 | **Rejuvenating Brew** | Stronger party heal — self 115 HP, teammates ~86 HP |
| 64 | **Battle Tincture** | Party ATK+50/DEF+35 buff (teammates 75%) for 5 rounds |
| 68 | **Corrosive Cloud** | AoE corrosion — applies debuff reducing all target defenses by 40% for 3 rounds |
| 80 | **Grand Remedy** | Full party restoration — major heal (175 HP) + clears all ailments |
| 94 | **Cataclysm Bomb** | Devastating AoE for 200 damage to all enemies |

### INT Scaling Improved
Alchemist base Intelligence gain per level increased from **+3 → +4**, matching Warrior's STR scaling. This improves bomb damage scaling significantly at higher levels since bomb damage uses INT as a multiplier.

### Corrosion Mechanic (New)
Corrosive Cloud applies a new **Corroded** status to all monsters hit. Corroded monsters have their effective defense reduced by 40% for 3 rounds against all damage types — regular attacks, targeted abilities, and AoE blasts. Stacking Corrosive Cloud into a fight before unleashing Explosive Flask or Cataclysm Bomb is now a strong combo.

## Warrior: Savage Charge (Level 47)

Filled a 15-level ability gap between Last Stand (40) and Whirlwind (55). Warriors now unlock **Savage Charge** at level 47 — hurl yourself at the enemy with crushing force, dealing 100 base damage and stunning them for 1 round (60% chance). Cooldown: 3 rounds. Warrior-exclusive.

## Combat Skill Self-Learning Cap

Skills and spells have always been able to improve through combat use (the system was already in place), but there was no ceiling — a player could spam Healing Mist and eventually reach Legendary proficiency without ever visiting the Level Master. This patch adds the intended cap.

**Combat use now improves skills and spells up to Good only.** Past Good, manual training at the Level Master is required. This makes early-game feel more rewarding (your abilities naturally get better as you use them) while preserving the Level Master's role for serious progression.

| Tier | Chance per use | Uses to advance (avg) |
|---|---|---|
| Untrained → Poor | 15% | ~7 |
| Poor → Average | 10% | ~20 |
| Average → Good | 7% | ~43 |
| **Good → Skilled** | **blocked** | **manual training only** |

Basic attack self-leveling is unchanged (always had this behavior, now consistent with abilities/spells).

## Ambush System: Partial Ambush Roll

Ambushes previously gave ALL monsters a free attack round with no way to mitigate it. Now each monster makes an opposed initiative check against the group leader — only monsters that win the roll get a free strike.

**Roll formula (per monster):**
- Monster stealth: `monster.Level × 3 + d20`
- Leader awareness: `(BaseAgility/4 + BaseDexterity/4 + Level×2) + d20`
- Monster wins → free attack; player wins → monster spotted

High-AGI/DEX leaders (Rangers, Assassins, Jesters) will detect most ambushers. Low-awareness leaders (Warriors focusing purely on STR) will still catch most strikes. Three outcomes:

| Result | Display |
|---|---|
| 0 monsters win | `*** You sensed the ambush! Your quick reflexes deny them a free strike! ***` |
| Some monsters win | `*** PARTIAL AMBUSH! 2 of 5 monsters catch you off guard! ***` |
| All monsters win | `*** AMBUSH! The monsters strike before you can react! ***` (same as before) |

## Poison/DoT Kill Bug Fix

Fixed a bug where monsters killed by poison, fire burn, or confusion self-damage awarded no XP or gold. These DoT death paths were returning early without registering the monster as defeated, so `HandleVictory` had no kills to reward. Now all DoT kill paths correctly add the monster to `DefeatedMonsters` before returning.

## Companion/NPC Accessory Pickup Fix

Fixed a bug where companions and NPC teammates would never pick up rings or necklaces when loot was passed to them. The auto-pickup system measured "item power" as `Attack` for weapons or `Armor` for everything else — accessories have neither, so their power was always 0, and `0 <= 0` caused them to be permanently skipped even for empty slots.

Accessories now use the sum of all stat bonuses (Strength, Dexterity, Wisdom, Charisma, Agility, HP, Mana, Armor, Attack) as their power metric. A companion with an empty ring or necklace slot will now correctly pick up any accessory with non-zero stats.

## Music Shop `look` Command Fix

The `look` command (and all other global commands: `/health`, `/gear`, auto-level-up, etc.) did not work while inside the Music Shop. `MusicShopLocation.ProcessChoice()` was missing the `TryProcessGlobalCommand()` call that all other locations have at the top of their `ProcessChoice` override.

## Quest Daily Limit Bug Fix

Fixed a bug where players could not claim any quests even when they had completed none that day.

**Root cause**: `RoyQuestsToday` was incremented in `CompleteQuest()` (completions) but checked in `CanPlayerClaim()` to gate new *claims*. So completing 3 quests blocked accepting any new ones — even with an empty active-quest list. Worse, `GetAvailableQuests()` didn't filter by the daily limit, so quests appeared in the board but then failed when accepted, producing the confusing "Cannot claim quest: DailyLimitReached" error.

**Fix**:
- `RoyQuestsToday` is now incremented in `ClaimQuest()` (when you accept a quest), not `CompleteQuest()` (when you turn one in). The daily limit now correctly tracks new quest claims.
- `GetAvailableQuests()` returns an empty list when the player is at the daily limit, so no quests are shown that can't be claimed.
- Both the "View Quests" and "Claim Quest" screens now show a clear "You have reached your daily quest limit" message with the cap instead of the generic "no quests available."

## NPC/Companion Stat Migration (Retroactive Fix)

Legacy saves often have NPC teammates and companions with far-too-low stats due to the old hardcoded level-up system. For example, a level 35 Jester might have CHA: 10, INT: 10, WIS: 10 (never gained any class-appropriate stats). This patch automatically corrects those stats the next time the player enters the dungeon.

**How it works**: When NPC teammates are added to the party on dungeon entry, `EnsureClassStatsForLevel()` runs on each NPC. It computes the expected minimum stats for their class and level (e.g., a level 35 Jester should have at least ~112 DEX, ~112 AGI, ~112 CHA from level scaling alone) and applies `Math.Max(current, expected)` — existing stats are never reduced, only under-scaled ones are brought up to the floor. Works for all NPC classes including prestige classes.

Companions with unscaled secondary stats (Constitution, Intelligence, etc. all stuck at 10) are similarly corrected on save load via role-based scaling per level.

## NPC/Companion Level-Up: Class Stats & Stat Display

Three fixes for NPC/companion level-ups:

**NPC wrong stat gains fixed**: When sharing XP with an NPC teammate (spouse, party member), their stats were increased by hardcoded random amounts (`+1-3 STR, +1-2 DEF`) regardless of class. A Jester was getting Warrior-style stat gains. Now uses `ApplyClassStatIncreases()` — the same path used for player level-ups and dungeon combat NPC level-ups. Jester NPCs now correctly gain DEX, AGI, CHA, and STA on level-up.

**Companion secondary stats added**: Companions now track the full stat set — CON, INT, WIS, CHA, DEX, AGI — in addition to their existing combat-facing BaseStats (HP/ATK/DEF/SPD/MAG/HEAL). These secondary stats scale per-role on every level-up (e.g., Tank gains +3 CON/+1 WIS, Healer gains +3 WIS/+2 INT/+1 CON, Bard gains +3 CHA/+2 INT/+1 DEX) and are used when building the Character wrapper for combat. Stats are saved and restored per-companion.

**Stat changes now displayed**: After any NPC teammate or companion levels up (both at the Level Master and in dungeon combat), the stat changes are now shown, e.g.:
```
  Kael Yarrow has reached level 35!
  STR +1  DEF +2  DEX +3  AGI +3  CHA +3  HP +10  STA +3

  Aldric has reached level 22!
  ATK +2  DEF +3  HP +28  CON +3  WIS +1
```

## Troll/Werewolf Regeneration Bug Fix

Fixed a bug where monster regeneration abilities were dealing damage to companions (Aldric, Vex, etc.) labeled "from Regeneration" — sometimes for hundreds of HP. Same bug affected `Thorns`, `ArmorHarden`, and `Vanish`.

**Root cause**: `AbilityResult.DamageMultiplier` defaults to `1.0f`. Non-damaging abilities (`Regeneration`, `Thorns`, `ArmorHarden`, `Vanish`, `Phase`) never explicitly set it to `0`, so the companion damage branch — `if (abilityResult.DamageMultiplier > 0)` — would fire, dealing a full normal-attack-equivalent hit labeled with the ability name.

**Fix**: Added `result.DamageMultiplier = 0` to all five non-damaging ability cases in `MonsterAbilities.ExecuteAbility()`. The troll's regeneration now correctly heals itself without harming your companions.

## Settlement Workshop Rework

The Workshop service was replaced with a more impactful effect. Previously it identified a single unidentified item (a duplicate of the Magic Shop's Identify service). Now:

**Workshop — Sharpen Weapon (+20% ATK, 10 combats, 1/day)**
The settlement smiths heat your blade and hone it to a razor edge. This gives a +20% weapon damage bonus that persists across 10 combats before fading. Resets each day along with other daily settlement services.

## Settlement Watchtower Rework

The Watchtower's "Scout" feature previously showed only one sample monster name and a vague level estimate — barely more useful than guessing. Now it provides a complete floor intelligence report:

**Watchtower — Reveal Dungeon Floor**
Shows the full room-by-room layout of any floor (1–100), generated from the same deterministic seed the dungeon uses, so the report exactly matches what you'll encounter:

- Every room by name with its type
- Tagged flags per room: Monsters / Trap / Treasure / Event / Boss Room / Secret / Stairs
- Sample monster with level shown for monster rooms (e.g., `→ Drake (Lv 26)`)
- Color coding: red for high-danger rooms, yellow for treasure/secrets, cyan for shrines/meditation, green for merchant dens
- Warnings at the top for Old God floors (⚠) and Seal floors (★)
- Floor theme and danger rating shown

## Spanish Translation: Inn Section Complete

Translated the remaining ~465 untranslated `inn.*` keys in `es.json` (previously showing raw English). Covers all inn content: ambient flavor text, Aldric bandit-attack event, Seth Able duel, drinking contest, tavern gossip, food/drink menus, companion management (recruit, party, details, talk, dialogues for all 5 companions, quest, romance, gift, equipment, abilities), training, gambling (High-Low Dice, Skull & Bones, Arm Wrestling), rent-a-room online logout flow, and attack-a-sleeper.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.51.1; Language and CompactMode properties rewritten to use SessionContext in MUD mode with static fallback for single-player; removed AsyncLocal fields
- `Scripts/Core/Character.cs` — Added `_hitsThisRound` field for multi-hit damage tracking
- `Scripts/Core/Monster.cs` — Added `IsCorroded` and `CorrodedDuration` properties for corrosion debuff
- `Scripts/Server/SessionContext.cs` — Added per-session `Language` and `CompactMode` properties
- `Scripts/Systems/ClassAbilitySystem.cs` — `acid_splash` moved level 24→14; 10 new Alchemist abilities (stimulant_brew, healing_mist, antidote_bomb, explosive_flask, smoke_screen, rejuvenating_brew, battle_tincture, corrosive_cloud, grand_remedy, cataclysm_bomb); Warrior `savage_charge` added at level 47; combat self-learning capped at Good (was Legendary)
- `Scripts/Systems/SpellSystem.cs` — Combat self-learning capped at Good (was Legendary)
- `Scripts/Systems/CompanionSystem.cs` — `ScaleCompanionStatsToLevel()`: buffed HP scaling (12-24/level with tier bonus), added defense scaling for all roles, increased tank HP/defense bonuses; added `Constitution`, `Intelligence`, `Wisdom`, `Charisma`, `Dexterity`, `Agility` properties to `Companion` class; all three stat-scaling paths (`ScaleCompanionStatsToLevel`, `ApplyCompanionLevelUpStats`, `SyncCompanionLevelToWrappers`) updated to use new properties; `CheckCompanionLevelUp` shows CON/INT/WIS/CHA/DEX/AGI diffs on level-up; `CompanionSaveData` extended with 6 new fields for serialization
- `Scripts/Systems/CombatEngine.cs` — DoT kill paths now register monsters in `DefeatedMonsters`; `ApplyAlchemistPartyEffect()` helper for all 6 party effects; `aoe_corrode` effect applied in both single/multi combat paths; corrosion 40% defense reduction in regular attack path, targeted ability paths, and `ApplyAoEDamage()`; corrosion tick-down in `ProcessMonsterAction()`; `MonsterAttacksCompanion()`: reduced boss minimum damage floors, multi-hit damage reduction; Alchemist INT gain +3→+4 in `ApplyClassStatIncreases()`; `TryTeammatePickupItem()`: accessories (Neck/Fingers) now use stat-sum power metric so rings/necklaces are correctly picked up by companions; ambush per-monster initiative roll (monster.Level×3+d20 vs leaderAwareness+d20); three outcomes: full/partial/foiled ambush
- `Scripts/Locations/BaseLocation.cs` — MUD prompt: hardcoded `look` command with localized explanation text
- `Scripts/Locations/LevelMasterLocation.cs` — Alchemist INT per level +3→+4
- `Localization/en.json` — `combat.corrode_fades` key added; `quest_hall.daily_limit_reached` and `quest_hall.daily_limit_info` keys added
- `Localization/es.json` — `combat.corrode_fades` translation added; `quest_hall.daily_limit_reached` and `quest_hall.daily_limit_info` translations added; ~465 `inn.*` keys translated (lines 9608–10072 — all inn content previously showing in English)
- `Scripts/Locations/LevelMasterLocation.cs` — NPC XP sharing: replaced hardcoded `+1-3 STR, +1-2 DEF` with `ApplyClassStatIncreases()`; added stat diff display after level-up; companion XP sharing stat snapshot updated to use new `Constitution`, `Intelligence`, etc. properties
- `Scripts/Systems/CombatEngine.cs` — Both NPC teammate level-up paths in combat now display stat changes per level gained
- `Scripts/Systems/MonsterAbilities.cs` — `Regeneration`, `Thorns`, `ArmorHarden`, `Vanish`, `Phase`: set `DamageMultiplier = 0` so non-damaging abilities no longer deal phantom damage to companions
- `Scripts/Locations/MusicShopLocation.cs` — Added `TryProcessGlobalCommand()` call at top of `ProcessChoice()` so `look`, `/health`, `/gear`, and all global commands work in the Music Shop
- `Scripts/Systems/QuestSystem.cs` — `ClaimQuest()`: increments `RoyQuestsToday` on accept; `CompleteQuest()`: no longer increments `RoyQuestsToday`; `GetAvailableQuests()`: returns empty list when player is at daily limit
- `Scripts/Locations/QuestHallLocation.cs` — "View Quests" and "Claim Quest" screens show explicit daily limit message when `RoyQuestsToday >= MaxQuestsPerDay`
- `Scripts/Systems/SaveDataStructures.cs` — `CompanionSaveInfo`: added `Constitution`, `Intelligence`, `Wisdom`, `Charisma`, `Dexterity`, `Agility` fields
- `Scripts/Systems/SaveSystem.cs` — Companion save/restore: serialize and restore the 6 new secondary stat fields
- `Scripts/Locations/LevelMasterLocation.cs` — `EnsureClassStatsForLevel()`: new public static method that computes expected class stats at level N and applies `Math.Max(current, expected)` for all 11 NPC classes
- `Scripts/Locations/DungeonLocation.cs` — `RestoreNPCTeammates()`: calls `EnsureClassStatsForLevel()` on each NPC before adding to party (retroactively corrects legacy under-scaled NPCs)
- `Scripts/Systems/CompanionSystem.cs` — `ScaleCompanionSecondaryStatsToLevel()`: new method for legacy companion saves; `Deserialize()`: detects legacy saves where secondary stats are all at default 10 and scales them
- `Scripts/Core/Character.cs` — Added `WorkshopBuffCombats` and `SettlementWorkshopUsedToday` properties
- `Scripts/Systems/SaveDataStructures.cs` — Added `WorkshopBuffCombats` and `SettlementWorkshopUsedToday` fields
- `Scripts/Systems/SaveSystem.cs` — Serialize/restore `WorkshopBuffCombats` and `SettlementWorkshopUsedToday`
- `Scripts/Core/GameEngine.cs` — Restore `WorkshopBuffCombats` and `SettlementWorkshopUsedToday` on load
- `Scripts/Systems/DailySystemManager.cs` — Reset `SettlementWorkshopUsedToday` on daily reset
- `Scripts/Systems/CombatEngine.cs` — Decrement `WorkshopBuffCombats` per combat; apply +20% damage bonus when `WorkshopBuffCombats > 0`
- `Scripts/Systems/SettlementSystem.cs` — Updated service labels: Workshop → "Sharpen Weapon (+20% ATK)", Watchtower → "Reveal Dungeon Floor"
- `Scripts/Locations/SettlementLocation.cs` — `UseWorkshopService()`: weapon sharpening buff (10 combats, 1/day); `UseWatchtowerService()`: full room-by-room floor reveal using `DungeonGenerator.GenerateFloor()`
