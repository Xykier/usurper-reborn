# Usurper Reborn v0.51.2 - Weapon Theming & Dungeon Party Fixes

## Weapon Thematic Stat Bonuses (New)

Dungeon loot weapons now receive name-based stat bonuses matching their thematic identity — the same system that armor has had since v0.50.5. Previously, weapons only had raw Attack power and random enchantment effects, so a "Staff of Sorcery" would have zero INT or WIS despite the name strongly implying caster stats.

**How it works**: After a weapon's final name is built (template name + rarity prefix + effect suffix), `ApplyWeaponThematicBonuses()` scans the name for keywords and grants appropriate stats:

| Theme | Keywords | Stats Granted |
|---|---|---|
| **Caster/Sorcery** | staff, sorcery, archmage, magic, mage, cosmos, transmuter | +INT, +WIS |
| **Holy/Divine** | holy, sacred, scepter, avenger, righteous, celestial, judgment | +WIS, +CON |
| **Shadow/Assassin** | shadow, assassin, phantom, night, death, venom | +DEX, +AGI |
| **Warrior/Battle** | berserker, titan, executioner, annihilator, bloodreaver, dragonslayer | +STR |
| **Bard/Music** | song, lute, lyre, harp, horn, flute, drum, opus, virtuoso | +CHA, +DEX |
| **Alchemist** | alchemist, elixir, philosopher | +INT, +CON |

Stat values scale with weapon power (primaryStat = power/6, secondaryStat = power/9) — slightly lower than armor thematic bonuses since weapons already carry Attack as their primary stat.

This also fixes unidentified weapons: stats are applied before identification status is set, so an unidentified staff that reveals as "Staff of Sorcery" will correctly have INT and WIS when identified.

## Dungeon Party Duplicate Fix

Fixed an intermittent bug where teammates could appear duplicated on the dungeon party management screen. Added three layers of defensive deduplication:

1. **`DeduplicateTeammates()`** — New method that removes duplicate entries from the party list using NPC ID, CompanionId, or DisplayName as the dedup key. Logs a warning when duplicates are found for diagnosis.
2. **Called at dungeon entry** — After all four team restoration methods run (companions, NPC teammates, player echoes, royal mercenaries), deduplication runs before entry fees are checked.
3. **Called at ManageTeam start** — Every time the player opens the party management screen, duplicates are cleaned up before display.
4. **Explicit NPC ID check in AddTeammateToParty** — The only `teammates.Add()` path that previously lacked an explicit duplicate guard now checks by NPC ID before adding.
5. **CompanionId check in AddCompanionsToParty** — Companion re-add loop now checks `CompanionId` before adding, in case the `RemoveAll(IsCompanion)` step missed a variant.

## Dungeon Tutorial for New Players

First-time adventurers entering dungeon floor 1 are now offered a guided 8-screen tutorial that walks through every aspect of dungeoning. It's skippable (press N to skip and play normally) and shows only once — after completion or skipping it never appears again.

The tutorial covers:
1. **The Floor Map** — How to read the map symbols (@, ?, #, █, >, B) and navigate with N/S/E/W
2. **Entering Rooms** — What different room types contain (monsters, treasure, events, features, stairs, safe rooms)
3. **Combat** — All combat actions: [A] Attack, [D] Defend, [P] Power Attack, [S] Precise Strike, [T] Taunt, [E] Disarm, [C] Cast Spell, [1-9] Abilities, [R] Flee
4. **Potions & Healing** — [P] use potions from room menu, [U] in combat, where to buy, herbs with [J]
5. **Healing Teammates** — [H] Aid Ally in combat; companion permadeath warning; how to recruit
6. **Events & Investigate** — [V] to investigate event rooms; the types of events and their consequences
7. **Examine Features** — [X] to examine shrines, levers, puzzles, and inscriptions
8. **Rest, Return & Tips** — [R] Camp, [Q] Quit, [B] Back; Old God floors, Seal floors; /health and /gear commands

---

## Magic Shop Menu Cleanup

Removed `[F] Forge` and `[D] Dungeon Reset Scroll` from the compact/BBS mode and screen reader mode Magic Shop menus. These options were previously removed from the visual menu but the compact and SR menus still listed them.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.51.2
- `Scripts/Systems/LootGenerator.cs` — New `ApplyWeaponThematicBonuses()` method with 6 keyword-based stat groups (caster, holy, shadow, warrior, bard, alchemist); called from `CreateWeaponFromTemplate()` after `ApplyEffectsToItem()` and before curse/identification
- `Scripts/Locations/DungeonLocation.cs` — New `DeduplicateTeammates()` method using NPC ID/CompanionId/DisplayName keying with debug logging; called at dungeon entry after team restoration and at `ManageTeam()` start; `AddTeammateToParty()`: added NPC ID duplicate check before `teammates.Add()`; `AddCompanionsToParty()`: added CompanionId duplicate check in re-add loop; `RunDungeonTutorial()`: guided 8-screen tutorial for first-time floor-1 visitors; skippable; one-time (persisted in `player.HintsShown`)
- `Scripts/Locations/MagicShopLocation.cs` — Removed `[F] Forge` from screen reader and compact/BBS menus; removed `[D] Dungeon Reset Scroll` from compact/BBS menu

---

## NPC Stat Persistence Fix

NPC teammates created via the archetype-based constructor (`NPC.cs`) were missing Intelligence and Constitution stats entirely, and none of their base stats were saved to `Base*` properties — meaning `RecalculateStats()` would reset them to zero after equipment changes.

- **All 8 NPC archetypes** (thug, guard, merchant, priest, mystic, assassin, noble, citizen) now define proper INT and CON base values
- **Base stat persistence**: `GenerateStatsForArchetype()` now saves all 11 base stats (STR, DEF, DEX, AGI, CHA, WIS, STA, INT, CON, MaxHP, MaxMana) so equipment bonuses calculate correctly

## NPC Teammate Sync Overhaul

`SyncNPCTeammateToActiveNPCs()` was only syncing 3 of 11 base stats and only ran on level-up. Now:
- Syncs **all 11 base stats** plus full equipment dictionary
- Runs after **every combat** (not just level-ups) and after equipment management at Inn/Home/Team Corner
- Made `internal` so equipment management screens can call it directly after gear changes

## Duplicate Display Name Prevention (Online)

Two different accounts could create characters with the same display name because the uniqueness check compared against the wrong field. Added:
- `IsDisplayNameTaken()` SQL query checking display_name ownership by account username
- UNIQUE INDEX on `LOWER(display_name)` in the online database
- Proper check in `CharacterCreationSystem.SelectCharacterName()` using the new query

## Companion Class-Appropriate Weapons

`EquipStartingGear()` gave all companions generic one-handed weapons regardless of their class, meaning:
- **Vex** (Assassin) couldn't use Backstab, Poison Blade, Fan of Knives, or Assassinate (require Dagger)
- **Melodia** (Bard) couldn't use Inspiring Tune, Song of Rest, Veloura's Serenade, or Legend Incarnate (require Instrument)
- **Mira** (Cleric) got random weapons instead of thematic Maces

Now each companion receives class-appropriate weapons:
- Tank (Aldric/Warrior) → Sword
- Damage (Vex/Assassin) → Dagger
- Healer (Mira/Cleric) → Mace
- Hybrid (Lyris/Paladin) → Sword
- Bard (Melodia) → Instrument

## Companion Save/Load Fixes

Several companion fields were silently lost on every save/load cycle:
- **HealingPotions**: Never serialized — companions lost all potions on reload
- **ManaPotions**: Never serialized — caster companions (Mira, Lyris, Melodia) lost mana potions on reload
- **PersonalQuestSuccess**: Serialized internally but dropped at the JSON boundary (CompanionSaveData → CompanionSaveInfo conversion)

All three fields now persist through the full save pipeline: Companion → CompanionSaveData → CompanionSaveInfo (JSON) → CompanionSaveData → Companion.

## Companion Combat Weapon Requirement Check

Companion AI could attempt to use abilities requiring weapons they didn't have — `TryTeammateClassAbility()` checked stamina and cooldowns but never validated weapon requirements. Now calls `CanUseAbility()` which checks `RequiredWeaponTypes` and `RequiresShield` before selecting an ability.

---

## Additional Files Changed

- `Scripts/Core/NPC.cs` — INT/CON stats for all 8 archetypes; base stat persistence in `GenerateStatsForArchetype()`
- `Scripts/Systems/CombatEngine.cs` — `SyncNPCTeammateToActiveNPCs()` rewritten: all 11 base stats + equipment sync, always runs, made internal; added sync call after `AwardTeammateExperience()`; `TryTeammateClassAbility()` now validates weapon requirements via `CanUseAbility()`
- `Scripts/Locations/TeamCornerLocation.cs` — Sync NPC teammate after equipment management
- `Scripts/Locations/HomeLocation.cs` — Sync NPC teammate after equipment management
- `Scripts/Systems/CompanionSystem.cs` — `EquipStartingGear()` rewritten with class-appropriate weapon types via `GetShopWeaponsByType()`; `HealingPotions`/`ManaPotions` added to `CompanionSaveData`; serialization and deserialization updated
- `Scripts/Data/EquipmentData.cs` — New `GetShopWeaponsByType()` query method
- `Scripts/Systems/SaveDataStructures.cs` — `PersonalQuestSuccess`, `HealingPotions`, `ManaPotions` added to `CompanionSaveInfo`
- `Scripts/Systems/SaveSystem.cs` — `PersonalQuestSuccess`, `HealingPotions`, `ManaPotions` added to both serialization and deserialization paths
- `Scripts/Systems/SqlSaveBackend.cs` — New `IsDisplayNameTaken()` method for duplicate display name prevention
- `Scripts/Systems/ISaveBackend.cs` — `IsDisplayNameTaken()` interface method
- `Scripts/Systems/FileSaveBackend.cs` — `IsDisplayNameTaken()` stub (single-player)
- `Scripts/Systems/CharacterCreationSystem.cs` — Fixed duplicate display name check to use proper SQL query by account ownership

---

## Bank Loan Persistence Fix

Bank loans (`player.Loan`), accrued interest (`player.Interest`), and robbery attempt counters (`player.BankRobberyAttempts`) were never serialized — meaning players could take a loan, save, reload, and the debt would vanish. These are now persisted through the full save pipeline.

- **Note**: The Dark Alley loan shark (`player.LoanAmount`) was already serialized — this only affected the Bank's loan system.

## Bank Transfer Dead NPC Fix

The bank transfer recipient list could include permanently dead NPCs. Added `!n.IsDead` check to the transfer target filter so dead NPCs no longer appear as valid transfer recipients.

## Bank Robbery Safe Floor Fix

The bank robbery safe contents calculation could go negative under certain conditions, allowing the safe to show a negative balance. Added `Math.Max(0, ...)` floor to the safe deduction.

## Online Player Heartbeat Self-Healing

MUD client connections (raw TCP through sslh) could silently disappear from the website "who's online" list and Main Street player display while remaining visible in `/who`. This happened because the `online_players` heartbeat row could be deleted by stale session cleanup, and subsequent `UpdateHeartbeat()` calls would silently affect 0 rows.

- `UpdateHeartbeat()` now returns `bool` — false when 0 rows affected (row was deleted)
- Heartbeat timer detects missing rows and automatically re-registers the player
- Prevents players from becoming invisible on the website and location displays

---

## Additional Files Changed (Bank & Heartbeat)

- `Scripts/Systems/SaveDataStructures.cs` — Added `BankLoan`, `BankInterest`, `BankRobberyAttempts` to `PlayerData`
- `Scripts/Systems/SaveSystem.cs` — Bank loan fields serialized
- `Scripts/Core/GameEngine.cs` — Bank loan fields restored on load
- `Scripts/Locations/BankLocation.cs` — Dead NPC filter on transfer recipients; safe contents floor on robbery
- `Scripts/Systems/SqlSaveBackend.cs` — `UpdateHeartbeat()` returns `Task<bool>`; returns false on 0-row update
- `Scripts/Systems/IOnlineSaveBackend.cs` — `UpdateHeartbeat()` signature changed to `Task<bool>`
- `Scripts/Systems/OnlineStateManager.cs` — Heartbeat timer self-healing: detects missing row and re-registers player

---

## Monster Combat System Audit & Fixes

Comprehensive audit of the monster combat system uncovered and fixed 12 bugs across 3 files.

### Fatigue XP Penalty Inversion

Exhausted players were receiving **bonus** XP instead of a penalty. The fatigue XP penalty code used `+=` instead of `-=`, meaning the `FatigueExhaustedXPPenalty` multiplier was being *added* to XP rewards. Fixed in both the single-monster and multi-monster victory handlers.

### 73 Missing Monster Abilities

Monster families in `MonsterFamilies.cs` define 78 unique ability strings (Rage, Frenzy, WebTrap, Incorporeal, Hellfire, etc.), but only 5 matched entries in the `MonsterAbilities.AbilityType` enum. The other 73 silently failed when `Enum.TryParse` returned false, meaning most monster special abilities **never fired** — Orc Warlords never cleaved, Liches never cast spells, Dragons never used DragonFear, Spiders never used WebTrap, etc.

All 73 abilities now have enum entries and full `ExecuteAbility` implementations with appropriate damage, status effects, healing, summoning, and evasion behaviors matching each monster's thematic identity.

### Boss Status Duration Double-Ticking

Boss monsters with multiple attacks per round (2-3 via `BossContext.AttacksPerRound`) called `ProcessMonsterAction` once per attack. Since all status duration decrements (poison, stun, sleep, fear, frozen, confused, slowed, corroded) happen inside `ProcessMonsterAction`, debuffs on bosses expired 2-3x faster than intended. Added `StatusTickedThisRound` flag on Monster — status effects now tick exactly once per round regardless of attack count.

### Poison Stun Missing Duration

NightshadeExtract and WidowsKiss poisons set `IsStunned = true` but never set `StunDuration`, so the stun expired immediately on the next status tick. Both now set `StunDuration = 2`.

### Incapacitated Monster Check Incomplete

The pre-check that suppresses the "Monster attacks!" message for disabled monsters only checked `IsSleeping`, `IsFeared`, `IsStunned`, and `IsFrozen` — missing legacy `Stunned` (one-round bool), `Charmed`, and `StunRounds > 0`. This caused monsters with those states to print "attacks!" before `ProcessMonsterAction` discovered they were incapacitated.

### Reflection/Thorns Kill Tracking

Monsters killed by Scales of Law artifact reflection or equipment Thorns enchantment were never added to `result.DefeatedMonsters`, so the player received no XP or gold for those kills. Both paths now track defeated monsters and clamp HP to 0.

### Confusion Self-Damage HP Clamping

Confused monsters that damaged themselves could have HP go negative before the `if (HP <= 0)` check set it to 0. Now uses `Math.Max(0, HP - damage)` directly.

### Companion Damage Cap

`MonsterAttacksCompanion()` was missing the per-hit damage cap that protects players (75% MaxHP for normal monsters, 85% for bosses). Companions could be one-shot by high-damage abilities. Added damage cap to normal attacks and both ability damage paths (DirectDamage and DamageMultiplier).

### Companion Defending Bonus

Companions using the Defend action received the aggro weight bonus from `IsDefending` but no actual damage reduction. Now take half damage when defending, matching player behavior.

### Companion Ability HP Clamping

Two ability damage paths in `MonsterAttacksCompanion` used `companion.HP -= dmg` without clamping, allowing negative HP. Changed to `Math.Max(0, companion.HP - dmg)`.

---

## Files Changed (Monster Combat Audit)

- `Scripts/Systems/CombatEngine.cs` — Fatigue XP penalty `+=` → `-=` in both victory handlers; Scales of Law and Thorns reflection: `Math.Max(0, ...)` HP clamping + `DefeatedMonsters` tracking on kill; confusion self-damage HP clamping; NightshadeExtract and WidowsKiss: `StunDuration = 2`; incapacitated check: added `Stunned`, `Charmed`, `StunRounds > 0`; boss status tick guard: reset `StatusTickedThisRound` per round, skip duplicate ticks in `ProcessMonsterAction`; `MonsterAttacksCompanion`: damage cap (75%/85% MaxHP), defending half-damage, ability damage HP clamping
- `Scripts/Systems/MonsterAbilities.cs` — 73 new entries in `AbilityType` enum (CriticalStrike, Rally, CommandArmy, Paralyze, Incorporeal, Spellcasting, Phylactery, Rage, Frenzy, Warcry, Cleave, Flight, DragonFear, AncientMagic, Invisibility, Teleport, Hellfire, Corruption, Dominate, Boulder, Stoneskin, Lightning, Earthquake, PackTactics, Bite, Lycanthropy, Howl, Moonlight, Burn, Immolate, Fireball, Rebirth, Inferno, Corrosion, Split, Engulf, Absorb, ShapeShift, Madness, WebTrap, PhaseShift, Poison, SummonSpiders, DeadlyVenom, Swarm, Cocoon, ImmuneMagic, PoisonGas, Indestructible, SelfRepair, Overload, Sleep, TreeMeld, Charm, AnimateTrees, RootEntangle, TimeStop, WildShape, TentacleGrab, InkCloud, Whirlpool, TidalWave, HolySmite, Purify, DivineJudgment, Sanctuary, Resurrection, StrengthDrain, Terror, Possess, Nightmare, DevourSoul, RealityBreak); full `ExecuteAbility` implementations for all 73
- `Scripts/Core/Monster.cs` — New `StatusTickedThisRound` property for per-round status tick tracking

---

## Caster Defence Gear Gap Fix

Caster-themed equipment (Cloth, Magic, Wizard, Arcane, Mage, Archmage, Sorcery) across all gear generation pathways gave zero Defence stat — only Intelligence, Wisdom, and Mana. This meant casters like Magicians had virtually no way to improve their Defence through equipment, making them excessively fragile at higher dungeon floors.

Defence is now added as a secondary stat to all caster-themed gear at lower values than warrior gear (roughly 60% of tank/warrior Defence), preserving the glass cannon identity while giving casters a survivability path.

### Dungeon Loot (LootGenerator.cs)
- **Caster/Focus armor** (Wizard, Arcane, Robe, Cloth, etc.): `Defence += power/9`
- **Holy/Divine armor** (Sacred, Blessed, Paladin, etc.): `Defence += power/9`
- **Caster weapons** (Staff, Sorcery, Archmage, etc.): `Defence += power/12`
- **Holy weapons** (Sacred, Scepter, Celestial, etc.): `Defence += power/12`
- **Bard weapons** (Song, Lute, Lyre, etc.): `Defence += power/12`
- **Alchemist weapons** (Elixir, Philosopher, etc.): `Defence += power/12`
- **Caster accessories** (Wisdom, Insight, Mage, Archmage, Sorcery, Sigil, Fireball, Planes, Gods): `Defence += power/10`
- **Generic necklace fallback**: `Defence += power/10`

### Shop Items (ShopItemGenerator.cs)
- **Staff weapons**: `DefenceBonus = s * 3` (0-3, vs Polearm's 0-5)
- **Instrument weapons**: `DefenceBonus = s * 2` (0-2)
- **Cloth armor**: `DefenceBonus = s * 3 * slotScale` (vs Chain's `s * 5`)
- **Magic/Artifact armor**: `DefenceBonus = s * 3 * slotScale`
- **Caster accessories** (Wisdom, Mage, Archmage): `DefenceBonus = s * 2` (0-2, vs Protection's 1-7)
- **Gods/Planes accessories**: `DefenceBonus = s * 2` (0-2)

### Static Equipment (EquipmentData.cs)
- 25 caster-themed items updated with Defence bonuses scaled by rarity tier:
  - Uncommon: +1 Defence (Silver Amulet, Mana Ring)
  - Rare: +1-2 Defence (Sage's Ring, Holy Symbol, Mage's Cowl)
  - Epic: +2-3 Defence (Crystal Necklace, Ring of the Archmage, Amulet of the Archmage)
  - Legendary: +3-4 Defence (Celestial Pendant, Celestial Band, Robes of the Grand Sorcerer)
  - Artifact: +3-5 Defence (Ring of the Arcane, Divine Seal, Cloak of the Cosmos)

## Files Changed (Caster Defence)

- `Scripts/Systems/LootGenerator.cs` — Defence added to 8 caster/holy/bard/alchemist thematic bonus categories in `ApplyThematicBonuses()`, `ApplyWeaponThematicBonuses()`, and `CreateAccessoryFromTemplate()`
- `Scripts/Systems/ShopItemGenerator.cs` — Defence added to Staff, Instrument, Cloth armor, Magic/Artifact armor, caster accessories, and Gods/Planes accessories
- `Scripts/Data/EquipmentData.cs` — `.WithDefence(N)` added to 25 static caster-themed items across Face, Cloak, Neck, and Ring slots

---

## Team Member Curse Removal

The Magic Shop's curse removal service now detects and removes curses from companion and NPC teammate equipment, not just the player's inventory. Previously, cursed gear on companions was permanent — there was no way to unequip or purify it.

When selecting `[C] Curse Removal`, the shop now scans:
1. **Player inventory** — existing behavior (cursed `Item` objects)
2. **Active companions** — scans all equipped gear slots via `CompanionSystem`
3. **NPC teammates** — scans equipped gear on NPCs in the player's dungeon party

All cursed items are presented in a unified numbered list showing the owner's name, slot, item name, and removal cost. The purification process (20% power loss, stat penalty removal) works the same for team equipment as for player items.

## Files Changed (Team Curse Removal)

- `Scripts/Locations/MagicShopLocation.cs` — `RemoveCurse()` rewritten to scan companions and NPC teammates; new `RemoveCurseFromPlayerItem()` (extracted original logic), `RemoveCurseFromTeamEquipment()` (Equipment purification with Defence/Attack reduction and curse flag clearing), `CalculateEquipmentCurseRemovalCost()`, `DisplayEquipmentCurseDetails()` helpers

---

## Auction House Supply/Demand Fix

NPCs were buying items from the auction house faster than they could list them, leaving the marketplace perpetually empty in online mode. Three changes rebalance supply and demand:

1. **NPC starting inventory** — NPCs level 5+ now have a 50% chance to spawn with 1-3 market items. Previously `GenerateStartingInventory()` existed but was never called, so NPCs started with zero items to sell.
2. **Increased dungeon loot rate** — NPCs finding loot during simulated dungeon runs increased from 20% to 35%, giving them more items to list.
3. **Reduced NPC buying** — NPC buy chance reduced from 50% to 25% per marketplace visit. NPCs also won't buy when fewer than 5 active listings remain, keeping items available for players.

## Files Changed (Auction House)

- `Scripts/Systems/NPCSpawnSystem.cs` — `CreateNPCFromTemplate()` and `GenerateImmigrantNPC()` now call `NPCItemGenerator.GenerateStartingInventory()` for NPCs level 5+ (50% chance)
- `Scripts/Systems/WorldSimulator.cs` — Dungeon loot chance 20% → 35%; NPC marketplace buy chance 50% → 25% in both single-player and online SQL paths; minimum 5 active listings required before NPCs will buy

---

## Ranger Class Combat Audit & Fixes

Comprehensive audit of the Ranger class uncovered critical bugs in the ranged attack formula and missing stats across all Ranger-themed equipment.

### Ranged Attack [V] Formula Fix (Critical)

The `ExecuteRangedAttack` damage formula was `DEX/2 + d6` — completely ignoring weapon power, two-handed bonus, and proficiency. A level 50 Ranger with a 100-power Composite Bow dealt the same ~30 ranged damage as a level 5 Ranger with an 8-power Short Bow. Meanwhile, a basic melee [A] attack used the full formula (STR + weapon power + 2H bonus + proficiency).

Both single-monster and multi-monster ranged attack methods now use a proper damage formula:
- **Damage**: DEX + Level/2 + d20 + effective weapon power
- **Two-handed bonus**: Applied via `GetWeaponConfigDamageModifier()` (bows are two-handed)
- **Defence reduction**: Monster defence subtracted from damage (was completely missing)
- **Minimum damage**: Floor of 1 prevents zero-damage hits

This brings ranged attacks in line with Smite and melee formulas.

### Ranger Armor Missing Defence

Ranger/Scout/Elven-themed armor from dungeon loot gave +DEX and +AGI but zero Defence — the only armor theme category with no Defence stat. Rangers were losing survivability compared to every other class's themed gear. Now grants `Defence += power/9` (slightly less than heavy armor themes at power/6).

### Bow Weapon Thematic Bonuses (New)

Bow-themed weapons from dungeon loot had no thematic stat section in `ApplyWeaponThematicBonuses()`, so they only got raw Attack power with no secondary stats. Added new bow/ranger/hunter/marksman/archer keyword group granting +DEX (primary), +AGI (secondary), and +Defence (tertiary at power/12).

### Shop Bow Defence

Bows purchased from the Weapon Shop gave DEX, AGI, and Crit Chance but zero Defence. Added `DefenceBonus = s * 3` (0-3) matching Polearm scaling.

### Ranger-Themed Accessories (New)

No ring or necklace templates existed with DEX/AGI focus — Rangers had to settle for generic STR-focused accessories or caster gear. Added 5 new templates:
- **Ring of the Hunter** (Lv15-65), **Marksman's Band** (Lv35-80), **Windstrike Ring** (Lv55-100)
- **Eagle Eye Pendant** (Lv20-70), **Ranger's Talisman** (Lv40-85)

All grant primary DEX + secondary AGI + Defence, filling the gap in Ranger-appropriate accessories.

### Additional Ranger Bow Templates

Added 3 new Ranger-exclusive high-level bow templates to improve late-game weapon variety:
- **Eagle Eye Bow** (Lv60-95, power 80)
- **Sniper's Longbow** (Lv80-100, power 115)
- **Windstrike Bow** (Lv90-100, power 130)

Previously Rangers had only 4 exclusive bows with a large gap between Composite Bow (power 65) and Elven Bow (power 100).

## Files Changed (Ranger Audit)

- `Scripts/Systems/CombatEngine.cs` — `ExecuteRangedAttack()`: damage formula rewritten (DEX + Level/2 + d20 + weapon power + 2H bonus - defence); `ExecuteRangedAttackMultiMonster()`: same formula rewrite
- `Scripts/Systems/LootGenerator.cs` — `ApplyThematicBonuses()`: Ranger/Scout/Elven armor adds Defence; `ApplyWeaponThematicBonuses()`: new bow/ranger/hunter keyword group with DEX+AGI+Defence; 3 new Ranger-exclusive bow templates (Eagle Eye, Sniper's Longbow, Windstrike); 5 new ranger-themed accessories (3 rings, 2 necklaces); `CreateAccessoryFromTemplate()`: hunter/marksman/windstrike/eagle/ranger keyword matching for DEX+AGI+Defence
- `Scripts/Systems/ShopItemGenerator.cs` — Bow weapon type: added `DefenceBonus = s * 3`
