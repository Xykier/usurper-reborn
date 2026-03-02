# Usurper Reborn v0.49.1 - Bug Fixes & QoL

**Release Date**: March 2026

## Overview

Post-launch fixes for the v0.49.0 "Swords and Lutes" update. New fatigue system (single-player only) — combat and exploration accumulate tiredness that gently penalizes damage, defense, and XP when you push too hard without resting. Equipment purchase quests overhauled to work with the new procedural shop inventory. Monster plural names fixed. Weapon shop handedness fix — bows no longer appear as one-handed, and Bard/Jester/Alchemist weapons no longer appear as two-handed. Jester and Alchemist weapon variety expanded. New `/time` command for checking the time of day from anywhere. Fist weapon type removed (was added by mistake). Sell All option added to all shops. Mana potions restricted to caster classes. Dungeon time-of-day messages now use underground-appropriate text. Dungeon beggar encounter expanded with rob option. Power Strike fixes (stamina cost, multi-monster defense, menu text). Dark god lifesteal now requires daily prayer. Dungeon rest renamed to "camp." Home sleep system separates healing from day advancement. Linux auto-updater fixed.

## Bug Fixes

### Equipment Purchase Quests Fixed

The "Acquire" quests from the Merchant Guild were broken after the v0.49.0 shop overhaul. Quests referenced items from the old static equipment database (e.g., "Steel Shield", "Iron Sword") that no longer appear in the procedurally generated shop inventories.

**What changed:**
- **Weapon quests** now select from the Weapon Shop's actual procedural inventory (`GetShopWeapons()`)
- **Armor quests** now select from the Armor Shop's actual procedural inventory (`GetShopArmor()`) across all 9 armor slots
- **Shield quests** now select from the Weapon Shop's actual procedural shields (`GetShopShields()`)
- **Accessory quests** (rings/necklaces) were already working — the Magic Shop still uses the static accessory database. Filter updated from price-based to level-based to better match what the Magic Shop displays.
- Quest descriptions now tell you which shop to visit ("Purchase from the Weapon Shop" instead of "Purchase from any shop")
- Level filtering matches what shops actually display: items within playerLevel -20 to +15

**Note:** Existing broken quests from v0.49.0 (like "Acquire: Steel Shield") cannot be auto-fixed. Abandon them at the Quest Hall and pick up new ones.

### Monster Plural Names Fixed

When encountering multiple monsters, names like "Wolf" displayed as "olves" instead of "Wolves" due to an off-by-one error in the substring math. Fixed in both dungeon encounters and quest descriptions.

- "Wolf" now correctly pluralizes to "Wolves"
- "Thief" now correctly pluralizes to "Thieves"
- "Elf" now correctly pluralizes to "Elves"
- "Fisherman" now correctly pluralizes to "Fishermen"

### Fist Weapon Type Removed

The Fist/Unarmed weapon type (Bo Staff, Nunchaku, Dragon Fist Wraps, Celestial Hand Wraps) was added by mistake in v0.49.0 and has been removed from the game. These items no longer appear in shops or dungeon loot.

### Linux Auto-Updater Fixed

The SysOp auto-updater on Linux BBS silently failed — download completed and progress showed 100%, but the update was never applied. The updater bash script was spawned as a child of the game process, so when the game exited and sshd terminated the session, SIGHUP killed the script before it could copy files. Fixed by launching the updater with `nohup` and backgrounding it so it survives the parent process exit.

### Marcus Dark Choice

Marcus the Wounded Soldier's "Confession" stage now offers a choice: forgive him (+25 Chivalry) or end his suffering (+100 Darkness). The dark path leads to a unique outcome with its own dialogue and rewards.

### Mana Potions Restricted to Caster Classes

Non-caster classes (Warrior, Assassin, Barbarian, Jester, Ranger) can no longer buy mana potions from the Healer. The healer gives a thematic rejection instead.

### Dungeon Time-of-Day Messages

Time transition messages while in the dungeon no longer reference outdoor scenery. Underground-appropriate messages replace them (e.g., "You sense dawn breaking somewhere far above" instead of "The morning sun warms the cobblestones").

### Power Strike Fixes

Three issues fixed with the `[P] Power Attack` combat action:

- **Missing stamina cost**: Power Attack now correctly costs 15 stamina. Previously it could be used unlimited times with no stamina check, even though the ability definition specified a cost.
- **Multi-monster defense skipped**: When fighting multiple monsters, Power Attack calculated raw damage but never subtracted the target's defense — dealing significantly more damage than intended. Now applies proper defense calculation matching single-monster combat.
- **Outdated menu descriptions**: Combat menus described Power Attack as "+50% damage, -accuracy" (from the pre-v0.48.5 design). Updated to "1.75x damage, 15 stamina" to match actual behavior.

### Daily Counters Not Resetting (Herb Gathering, Home Rests)

When the daily maintenance system ran (Pascal-compatible MAINT.PAS processing), it skipped the basic daily reset entirely — treating maintenance as a replacement rather than an additive step. This meant `HerbsGatheredToday`, `HomeRestsToday`, companion daily flags, gambling counters, and servant gold income were never reset on days when maintenance triggered. Players would see "herbs gathered 1/1" or "rests used" from the previous day carrying over. Fixed by always running the basic daily reset before maintenance processing.

### Dark God Lifesteal Requires Daily Prayer

The lifesteal effect from worshipping a dark-aligned god was permanently active — it never expired or reset. Now lifesteal only activates after praying at the Temple each day. When the daily reset clears your prayer, lifesteal stops until you pray again. This gives dark god worshippers a reason to visit the Temple daily.

### Dungeon "Camp" Rename

Resting in the dungeon is now called "camping" since it advances the game day. Menu option changed from `[R] Rest` to `[R] Camp`, with updated flavor text throughout (room messages, group broadcasts, safe haven encounters).

### Dwarf Intimacy Typo

Fixed "She's sturdy frame" → "Her sturdy frame" in the Dwarf race intimate encounter text. Wrong pronoun variable was used (`genderCap` = "She" instead of `their` = "Her").

### NPC Intimacy Personality Mapping

Most NPCs had no intimate personality — regardless of your approach choices, they would just "smile and go along" with a generic response. Two issues:

1. **Missing archetype coverage**: The romance trait generator only handled 8 specific NPC archetypes (thug, noble, merchant, guard, priest, mystic, craftsman). The majority of NPCs (citizens, commoners, adventurers) fell through with no intimate style or trait preferences assigned.
2. **Matching threshold too strict**: The trait threshold for recognizing NPC preferences (0.5) sat right at the mean of the trait distribution (0.2–0.8), causing roughly half of all trait checks to fail even for NPCs that *did* have traits assigned.

Now all NPC archetypes get a random intimate style (Dominant/Submissive/Switch/Vanilla/Adventurous) and 1–2 boosted romance traits. The matching threshold was lowered to 0.35 so NPCs with clear preferences actually express them.

### Lover/Spouse Flirt Rejection

Flirting with your own lover or spouse in the dialogue system could result in cold rejection messages like "I'm not interested in... whatever that was" — while kissing them on the same screen worked fine ("I never tire of that..."). The flirt handler never checked if the NPC was the player's partner, so it rolled against personality-based receptiveness like any stranger. Now lovers/spouses always respond warmly to flirting with 5 playful response variants.

### Music Shop Hidden for New Players

The Music Shop `[U]` was gated behind menu Tier 2 (Level 3+), meaning Bards couldn't see it until level 3 — despite needing instruments as weapons from level 1. Moved to Tier 1 so it appears on the Main Street menu for all players from the start.

### Companion Recruitment Abilities Outdated

The companion recruitment screen showed placeholder ability names (e.g., "Starlight Heal", "Smoke Bomb", "Inspiring Melody") that didn't match the actual class abilities used in combat. Updated all five companions to show their real abilities:

- **Lyris** (Paladin): Lay on Hands, Divine Smite, Aura of Protection, Holy Avenger
- **Aldric** (Warrior): Power Strike, Shield Wall, Battle Cry, Last Stand
- **Mira** (Cleric): Prayer of Mending, Holy Smite, Sanctuary, Greater Heal
- **Vex** (Assassin): Backstab, Poison Blade, Shadow Step, Death Mark
- **Melodia** (Paladin): Lay on Hands, Divine Smite, Aura of Protection, Holy Avenger

### Shield Loot Comparison vs Dual-Wield Weapon

When finding a shield in the dungeon while dual-wielding (weapon in off-hand), the equipment comparison incorrectly showed an armor-to-armor comparison — displaying the off-hand weapon's ArmorClass (0) vs the shield's armor value, making every shield look like a massive upgrade. Now shows a clear trade-off message: "Off-hand weapon (Attack: X) would be replaced by shield (Armor: Y)".

### Companion Equipment Shows Unidentified Item Names

The companion equipment management screen at the Inn revealed the real names of unidentified items (e.g., "Blade of the Void (unidentified)"). Now shows slot-appropriate mystery names matching the rest of the game (e.g., "Unidentified Weapon", "Unidentified Helm"). Fixed across all four companion equipment flows: slot display, equip list, unequip list, and take-all.

### Weapon Shop Handedness Misclassification

Several weapon templates added in v0.49.0 were placed at incorrect positions in the weapon template list, causing the shop to generate them with the wrong handedness:

- **Ranger bows** (Hunting Bow, Longbow, Composite Bow, Elven Bow) appeared in the one-handed weapon shop category. Bows are two-handed weapons.
- **Bard weapons** (Rapier, Dueling Blade, Songblade, etc.), **Bard instruments**, **Jester weapons**, and **Alchemist weapons** were generated as two-handed items. These are all one-handed weapons.

Fixed by reorganizing the weapon template list so all one-handed templates precede the two-handed boundary index, and correcting `TwoHandedWeaponStartIndex` from 37 to 55.

### Dual-Wield Off-Hand Missing on Abilities

When using class abilities (Power Strike, Backstab, Precise Strike, etc.) via the quickbar, dual-wielding players only got the main-hand ability strike — the off-hand follow-up never happened. Regular attacks correctly did both main and off-hand swings. The ability code path (`ApplyAbilityEffects`) was missing the off-hand follow-up that the legacy `ExecutePowerAttack` function had. Now all melee attack abilities get an off-hand follow-up at 50% damage when dual-wielding, matching regular attack behavior.

### Main Street Time-of-Day Description

The Main Street description ("The morning air is clear") used the real-world clock (`DateTime.Now`) instead of the game clock (`GameTimeMinutes`). If you played at 9 AM real time but the game said it was evening, Main Street still said "morning." Now uses the game clock in single-player mode.

## Enhancements

### Jester & Alchemist Weapon Variety Expanded

Both classes had very limited weapon options at high levels — Jesters had only 1 choice above level 80, Alchemists had 2. Both classes now have proper coverage across all level ranges:

**Jester** — Added to Stiletto and Mace templates. Two new Jester-specific weapons:
- Chaos Edge (levels 50-90)
- Madcap's Razor (levels 80-100)

**Alchemist** — Added to Mace, War Hammer, Flail, and Quarterstaff templates. One new Alchemist-specific weapon:
- Elixir-Infused Blade (levels 55-95)

These changes affect both shop inventory and dungeon loot drops.

### `/time` Command

New slash command to check the current time of day from any location. Accessible for screen reader users who couldn't easily find the time display in location headers.

- **Single-player**: Shows game clock (e.g., "Time: 8:00 AM (Morning)") and whether you can rest
- **Online**: Shows server time
- Aliases: `/time` or `/t`
- Listed in `/help` command reference

### Sell All in Shops

All three shops (Weapon, Armor, Magic) now offer `[A] Sell All` in the sell menu. Sells all qualifying backpack items in one action with a confirmation prompt showing total item count and gold. Cursed and unidentified items are skipped. Equipped items are not affected.

### Sleep System (Single-Player) — Home & Inn

Rest and day advancement are now separated at both the Home and the Inn:

**Home:**
- **`[E] Rest & Recover`** — Healing + Well-Rested boon only. No longer advances the day.
- **`[Z] Sleep`** — Available at evening/night (8 PM+). Full HP/Mana/Stamina recovery, Well-Rested boon from Hearth, dream check, then advances to 6 AM and triggers daily reset. Blood Price penalty still applies.
- **`[Z] Wait`** — During daytime, fast-forwards the clock to 8 PM (unchanged).

**Inn:**
- **`[E] Rest at table`** — Now heals 50% of missing HP and mana. No longer advances the day. No Well-Rested bonus.
- **`[Z] Sleep`** — Available at evening/night (8 PM+). Full HP/Mana/Stamina recovery, dream check, then advances to 6 AM and triggers daily reset. No Well-Rested bonus (that's a Home Hearth perk). Blood Price penalty still applies.
- **`[Z] Wait`** — During daytime, fast-forwards the clock to 8 PM (unchanged).

The `[Z]` key dynamically shows "Sleep (advance to morning)" in green when it's nighttime, or "Wait until nightfall" when it's daytime. Also appears in BBS compact mode menus at both locations.

### Pixie Encounter (Replaces Friendly Trader)

The redundant "friendly adventurer" dungeon encounter (buy potions / trade info) has been replaced with a pixie encounter inspired by Legend of the Red Dragon. A glowing pixie appears and you can try to catch her:

- **Catch success** (DEX-based, 35% base + 1% per DEX, capped at 85%): Pixie kiss restores full HP/Mana, grants a +15% damage/defense blessing for 5 combats, and sprinkles level-scaled gold.
- **Catch failure**: Pixie curses you with poison and steals some gold before vanishing.
- **Leave her be**: No effect.

Group members share the gold reward on success.

### Fatigue System (Single-Player Only)

A new activity-based fatigue system gives the day/night cycle mechanical weight. Fatigue accumulates from combat and exploration — shopping in town barely tires you, but fighting through 10 dungeon floors leaves you exhausted. No action is ever blocked; fatigue is a gentle nudge toward rest, not a wall.

**Fatigue Tiers:**

| Tier | Range | Penalty |
|------|-------|---------|
| Well-Rested | 0-24 | None |
| Normal | 25-49 | None (hidden) |
| Tired | 50-74 | -5% damage, -5% defense |
| Exhausted | 75-100 | -10% damage, -10% defense, -10% XP |

**Fatigue Sources:** Combat win/flee (+3), combat loss (+5), dungeon room explored (+1), travel between locations (+1).

**Fatigue Recovery:** Full sleep at Home/Inn resets to 0. Home rest [E] reduces by 20. Inn table rest [E] reduces by 15. Dungeon safe haven reduces by 10.

**Display:** Tired/Exhausted shows in location headers (e.g., "Main Street — Evening (Tired)"). `/health` command shows full fatigue details. Combat start shows a warning message when fatigued.

Online/MUD mode is completely unaffected — fatigue never increments or displays.

### Dungeon Beggar Encounter Expanded

The dungeon beggar encounter now offers three choices instead of two:
- **(G) Give gold** — level-scaled cost, +5 Chivalry, 15% chance the beggar rewards your generosity
- **(R) Rob the beggar** — steal level-scaled gold, +10 Darkness, 20% chance they fight back
- **(I) Ignore** — walk past

## Files Changed

- **`Scripts/Core/GameConfig.cs`** — Version bump to 0.49.1; fatigue system constants (thresholds, costs, reduction amounts, penalty floats)
- **`Scripts/Core/EquipmentEnums.cs`** — Removed `Fist = 16` from WeaponType enum
- **`Scripts/Systems/QuestSystem.cs`** — Equipment purchase quest generation rewritten to use shop-generated items (`GetShopWeapons`, `GetShopArmor`, `GetShopShields`); level-based filtering; shop-specific quest descriptions
- **`Scripts/Systems/ShopItemGenerator.cs`** — Removed Fist weapon stat bonuses and name inference
- **`Scripts/Systems/LootGenerator.cs`** — Removed 4 Fist weapon templates; added Jester to Stiletto/Mace; added Alchemist to Mace/War Hammer/Flail/Quarterstaff; 3 new class-specific weapon templates (Chaos Edge, Madcap's Razor, Elixir-Infused Blade); reorganized weapon template ordering so Ranger bows are in two-handed section and Bard/Jester/Alchemist weapons are in one-handed section; `TwoHandedWeaponStartIndex` corrected from 37 to 55
- **`Scripts/Locations/BaseLocation.cs`** — New `/time` and `/t` slash command with `ShowGameTime()` method; added to `/help` display; fatigue tier label appended to location header when Tired/Exhausted; `/health` command shows fatigue status with penalty description
- **`Scripts/Locations/DungeonLocation.cs`** — Fixed `GetPluralName()` substring math for wolf/thief/elf/man
- **`Scripts/Systems/QuestSystem.cs`** — Fixed `GetPluralName()` substring math (same fix as DungeonLocation)
- **`Scripts/Systems/VersionChecker.cs`** — Linux updater launched with `nohup` + background to survive SIGHUP on session exit
- **`Scripts/Systems/TownNPCStorySystem.cs`** — Marcus "Confession" stage: added forgive/kill choice; new "Silence" dark path stage
- **`Scripts/Locations/WeaponShopLocation.cs`** — `[A] Sell All` in sell menu for weapons/shields
- **`Scripts/Locations/ArmorShopLocation.cs`** — `[A] Sell All` in sell menu for armor
- **`Scripts/Locations/MagicShopLocation.cs`** — `[A] Sell All` in sell menu for magic items
- **`Scripts/Locations/HealerLocation.cs`** — Mana potion purchase blocked for non-caster classes
- **`Scripts/Systems/DailySystemManager.cs`** — `CheckTimeTransition()` accepts `inDungeon` parameter; underground-themed time messages; fixed daily reset always running even when MaintenanceSystem processes (herb/rest/companion counters were skipped); fatigue resets to 0 on full daily sleep
- **`Scripts/Core/Character.cs`** — Added `Fatigue` property (int, 0-100) and `GetFatigueTier()` helper returning label/color tuple
- **`Scripts/Systems/SaveDataStructures.cs`** — Added `Fatigue` field to PlayerData
- **`Scripts/Systems/SaveSystem.cs`** — Fatigue serialization/restore
- **`Scripts/Core/GameEngine.cs`** — Fatigue restore from save data
- **`Scripts/Systems/CombatEngine.cs`** — Power Attack: added 15 stamina cost to both single and multi-monster versions; added missing defense calculation to multi-monster version; updated menu descriptions from "+50% dmg, -accuracy" to "1.75x dmg, 15 stam"; shield loot comparison now shows trade-off message when replacing off-hand weapon instead of misleading armor-to-armor comparison; fatigue system: combat start warning message, damage penalty in single/multi-monster attack paths, defense penalty in monster attack path, XP penalty in both victory handlers, fatigue increment (+3 win/flee, +5 death) at combat exit; off-hand follow-up added to `ApplyAbilityEffectsMultiMonster` and `ApplyAbilityEffects` for melee attack abilities when dual-wielding (was missing — abilities only did main-hand hit)
- **`Scripts/Systems/LocationManager.cs`** — Travel fatigue (+1 per location change, single-player only)
- **`Scripts/Systems/DivineBlessingSystem.cs`** — `CalculateLifesteal()` now requires active prayer buff; lifesteal resets on daily reset
- **`Scripts/Locations/HomeLocation.cs`** — New `SleepAtHome()` method with full recovery + day advancement; `DoRest()` no longer advances the day; `[Z]` menu dynamically shows Sleep/Wait based on time; BBS compact menu includes Z; home rest reduces fatigue by 20
- **`Scripts/Locations/InnLocation.cs`** — `RestAtTable()` now heals 50% HP/mana, no longer advances the day; new `SleepAtInn()` method with full recovery + day advancement; `[Z]` menu dynamically shows Sleep/Wait; BBS compact menu includes Z; companion equipment screen hides real names of unidentified items across all 4 flows (display, equip, unequip, take-all); inn rest reduces fatigue by 15
- **`Scripts/Systems/CompanionSystem.cs`** — Updated all 5 companion recruitment ability display names to match actual ClassAbilitySystem abilities used in combat
- **`Scripts/AI/PersonalityProfile.cs`** — Added default case to `GenerateRomanceTraits()` for citizen/commoner/adventurer archetypes — assigns random IntimateStyle and boosts 1-2 romance traits
- **`Scripts/Systems/IntimacySystem.cs`** — Fixed Dwarf intimacy pronoun typo ("She's" → "Her"); lowered intimate action matching threshold from 0.5 to 0.35 so NPC preferences actually register
- **`Scripts/Systems/VisualNovelDialogueSystem.cs`** — Flirting with your own lover/spouse now auto-succeeds with warm responses instead of rolling against personality receptiveness
- **`Scripts/Locations/MainStreetLocation.cs`** — `GetTimeOfDay()` now uses game clock instead of real-world clock in single-player; Music Shop moved from Tier 2 to Tier 1 (all three display methods)
- **`Scripts/Locations/DungeonLocation.cs`** — Beggar encounter expanded with rob option; friendly trader replaced with pixie encounter (DEX-based catch, blessing/curse); dungeon time transition flag passed; "Rest" renamed to "Camp" throughout (menu, flavor text, group broadcasts, safe haven); room exploration adds +1 fatigue; dungeon safe haven rest reduces fatigue by 10
