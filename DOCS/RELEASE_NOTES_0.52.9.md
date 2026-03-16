# Usurper Reborn v0.52.9 Release Notes

**Release Date:** March 15, 2026
**Version Name:** The Hook

## Team HQ Upgrades Now Functional

The five Team Headquarters upgrades (Armory, Barracks, Training, Infirmary, Vault) were purchasable and displayed levels in the HQ menu, but none of the combat bonuses were actually applied anywhere in gameplay. All four combat upgrades are now wired in:

- **Armory** (Lv1-10): +5% attack damage per level, applied in both single-monster and multi-monster combat paths.
- **Barracks** (Lv1-10): +5% defense per level, applied in the defense calculation against monster attacks.
- **Training** (Lv1-10): +5% XP bonus per level, applied in all three XP reward paths (single-monster, multi-monster, berserker/special).
- **Infirmary** (Lv1-10): +10% potion healing per level, applied to both quick-heal and multi-potion heal paths.
- **Vault**: Already worked (capacity calculation existed).

Upgrade levels are cached on the player at login from the database and refreshed when upgrades are purchased. Active HQ bonuses are now displayed in the `/health` Active Buffs section so players can see their team benefits.

## World Boss Spawn Cooldown

World bosses could respawn immediately after being defeated — `CheckSpawnConditions()` only checked for an `active` boss, and once a boss was marked `defeated`, the next world sim tick (every ~30 seconds) would spawn a new one. There is now a 4-hour cooldown after a boss is defeated or expires before a new one can spawn.

## Shop Category Navigation Fix

The Magic Shop and Weapon Shop had a bug where pressing `R` (return to Main Street) or `Q` (quit) while browsing a sub-category (Rings, Necklaces, One-Handed, etc.) would navigate away without clearing the category state. Re-entering the shop would drop the player directly back into the sub-category they left, which was confusing — especially in the Magic Shop where players could get stuck in the ring interface without seeing the `[B]ack` option. Category state is now cleared both on exit and on shop entry.

## NG+ Relationship Carryover Fix

NPC relationships (Friend, Trusted, Lover, etc.) were not reset when starting a New Game Plus cycle. The `RelationshipSystem` was never cleared during the NG+ flow, so the new character inherited all relationship levels from the previous playthrough. In single-player mode, all relationships are now cleared. In online mode, only the player's relationships are cleared while preserving NPC-to-NPC relationships.

## Dungeon Merchant Display Fixes

Armor items from the dungeon merchant showed confusing duplicate defense stats like "Def +8, DEF +3" — the first was the item's armor class (damage reduction), the second was a flat defence stat bonus. The armor class label is now "AC" to distinguish it. Additionally, merchant items could appear as unidentified after purchase despite showing their real name and stats on the buy screen. The loot generator marks Rare+ items as unidentified (40-100% chance), but a merchant wouldn't sell mystery items — all merchant-generated items are now force-identified.

## Dungeon Language Switch Fix

Changing the game language while on a dungeon floor left all room names, descriptions, atmosphere text, and feature names in the previous language. The dungeon generator resolves localization keys at floor generation time and caches them. The floor cache is now invalidated when the player changes language, so rooms regenerate in the new language on the next room entry.

## World Boss Menu Display Fix

The world boss attack menu showed `[Q] [B]ack` instead of `[Q] Back`. The `ui.back` localization key already contained bracket formatting (`[B]ack`), which doubled up with the `[Q]` key prefix.

## NPC Team Recruitment Persistence Fix

Recruiting an NPC to your team in online mode could result in the NPC immediately vanishing from the team. The NPC's team assignment was set in memory but not persisted to the database — the world sim reload cycle (every ~30 seconds) would overwrite it from the database where the team field was still empty. NPC state is now saved immediately after recruitment, same pattern used for equipment changes since v0.52.6.

## Website Class Stat Corrections

Corrected primary/secondary stat bars on both the main website and Steam website for 8 classes that showed inaccurate stats: Warrior (DEF→CON), Assassin (STR→AGI), Magician (CHA→WIS), Cleric (CHA→INT), Sage (WIS/MNA→INT/WIS), Barbarian (HP→CON), Tidesworn (DEF→STR), Abysswarden (STR/INT→DEX/STR). Paladin on the Steam site also had DEF instead of WIS.

## Lyris Companion: Paladin → Ranger Fix

Lyris's displayed abilities still showed her old Paladin kit ("Lay on Hands", "Divine Smite", "Aura of Protection", "Holy Avenger") despite being reclassed to Ranger in v0.52.3. Her ability display now correctly shows Ranger abilities: "Precise Shot", "Hunter's Mark", "Nature's Blessing", "Camouflage". Note: This was cosmetic only — her actual combat behavior already used Ranger abilities via the `CombatRole.Hybrid => CharacterClass.Ranger` mapping.

## Companion Heal AI Fix

Companion/NPC teammates could use healing abilities (or buff abilities with healing components like Terravok's Call) when the entire party was at full HP. The ability filter only excluded `Type == Heal` abilities when no one needed healing, but buff abilities with `BaseHealing > 0` bypassed the check. Now both pure heal abilities and buff abilities with healing components are filtered when no party member is below 70% HP. Additionally, "recovers 0 HP" messages are suppressed when a heal fires but the target is already at full health.

## Admin Level Edit Applies Class Stats

The online admin console's `[1] Level` editor only changed the level number without applying any class stat increases. Setting a player from level 1 to level 10 would give them level 10 with level 1 stats. The level editor now automatically applies the correct per-class stat gains for each level gained (same stats as the Level Master), including all 16 classes. A confirmation message shows how many levels of stat increases were applied.

## Opening Story & Captain Aldric Localization

All ~120 hardcoded English strings in the opening story sequence (dream, dormitory, first mystery, letter content, goal reveal, NG+ opening, race descriptions, class descriptions) and Captain Aldric's guided quest (intro, dungeon objectives, kill objectives, quest completion) are now localized via `Loc.Get()`. Translations added to Spanish, Hungarian, and Italian.

## Dungeon Merchant Input Fix

Pressing any key other than T or A at the dungeon merchant encounter (e.g. typing Y for "yes") would immediately dismiss the merchant with no error message. The prompt now validates input and only accepts T (trade), A (attack), L (leave), or Enter. Invalid keys silently re-prompt instead of ending the encounter.

## Equipment Name Localization

All equipment names are now translatable. Item names are composed from localized parts: base template names (263 weapons/armor/accessories), rarity prefixes (Fine, Superior, Exquisite, Legendary, Mythic), effect prefixes/suffixes (28 effects × prefix/suffix/display name), condition labels (Good/Fair/Poor/Bad/Broken), and slot fallback names (10 slots). The `BuildItemName()` method in `LootGenerator` now assembles names from `Loc.Get()` lookups instead of hardcoded English strings. A `LocalizeTemplateName()` helper converts English template names to localization keys via snake_case conversion. The `GetItemRarity()` display method also checks localized prefix strings. ~434 new `item.*` keys added to all four language files.

## Companion Quest & Combat Message Localization

All ~145 remaining hardcoded English strings in companion quest narratives and combat messages are now localized via `Loc.Get()`:

- **Combat messages**: Ocean's Voice crit, Death's Embrace trigger/revive, precision strike, ability headers (visual/SR), combat stamina display, boss enrage, 12 party-wide ability messages (Alchemist stimulant/heal/antidote/smoke/brew/remedy, Cleric divine heal/beacon/cleanse, corrosion), with player/NPC variants using `{0}` placeholders.
- **Dungeon atmosphere**: Blood Moon visual and BBS descriptions, Sunforged Blade corridor/discovery sequence (title + 5 narrative lines + warm/return).
- **Lyris quests**: Shrine encounter (intro, woman description, menu options, recruitment, talk, leave) and "The Light That Was" personal quest (all dialogue, 3 choice branches with outcomes, quest complete).
- **Aldric quest**: "Ghosts of the Guard" (title, all dialogue, 3 choice branches, Malachar boss fight combat messages with HP displays, victory scene, quest complete, XP reward).
- **Mira quest**: "The Meaning of Mercy" (title, scene setup, 3 moral choice branches with outcomes, quest complete).
- **Vex quest**: "One More Sunrise" (title, complete messages, treasure/joke/truth bucket list sub-encounters with full dialogue).
- **Melodia quest**: "The Lost Opus" (title, melody discovery, chamber, opus description, 3 choice branches, quest complete).

All keys translated to Spanish, Hungarian, and Italian.

## Comprehensive Spell System Audit & Fixes

A full audit of the spell system uncovered multiple bugs where spell effects were silently discarded or never wired in:

- **AttackBonus spells now use actual values**: 12 buff spells across Cleric, Sage, Tidesworn, Wavecaller, Cyclebreaker, and Voidreaver set numeric AttackBonus values (5-100+) but `ApplySpellEffects` converted ANY non-zero value to a flat PowerStance (+50% damage), ignoring the actual number. Bless Weapon (+12) gave the same buff as Wish (+100). Now uses `TempAttackBonus` with the spell's actual value.
- **Multi-target Buff spells now buff the whole party**: Covenant of the Deep, Tidecall Barrier, and Symphony of the Depths set `IsMultiTarget = true` on Buff-type spells, but the multi-target routing only handled Heal spells. Protection and attack bonuses are now applied to all living teammates.
- **Summon spells (Angel/Demon) now have combat effects**: Cleric Summon Angel, Sage Summon Demon, and Sage's second Summon Demon set `SpecialEffect = "angel"/"demon"` but no handler existed. Summoned creatures now grant dodge-next-attack protection to the caster.
- **Cyclebreaker Deja Vu now works**: The spell set `SpecialEffect = "dodge_next"` but no handler existed in spell effect methods. Now correctly grants dodge-next-attack.
- **Dispel Evil no longer removes player-applied charm**: Was clearing `Charmed`/`IsFriendly`/`IsConverted` — all player-beneficial effects. Now strips monster power-ups (enrage, phase immunity, channeling) and reduces defense by 20%, matching the "banish dark enchantments" description.
- **Tidal Ward reflection implemented**: Tidesworn's level 1 spell described "reflects 10% melee damage" but had no reflection code. Now applies `StatusEffect.Reflecting` which uses the existing Wavecaller damage reflection system (15% melee reflection).
- **Weaken debuff from spells now sets WeakenRounds**: Siren's Lament and other weaken spells reduced stats but never set `WeakenRounds`, so abilities like Wave's Echo (`double_vs_debuffed`) didn't detect the debuff. Both spell-path and ability-path weaken handlers now set `WeakenRounds`.
- **Wave's Echo debuff check expanded**: `double_vs_debuffed` only checked Poisoned/Stunned/Charmed/Distracted/WeakenRounds/IsSlowed. Now also checks IsMarked, IsCorroded, IsFeared, IsConfused, IsSleeping, and IsFrozen — all valid debuff states.
- **Shadow Step defense bypass implemented**: Sage's Shadow Step spell set `SpecialEffect = "shadowstep"` but no handler existed. Now deals bonus damage equal to target's armor value, bypassing defense.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` -- Version 0.52.9; `WorldBossSpawnCooldownHours = 4.0` constant
- `Scripts/Core/Character.cs` -- `HQArmoryLevel`, `HQBarracksLevel`, `HQTrainingLevel`, `HQInfirmaryLevel` transient properties (cached from DB, not serialized)
- `Scripts/Core/GameEngine.cs` -- Load HQ upgrade levels from `SqlSaveBackend` on player login when team exists (online mode); `RelationshipSystem` reset during NG+ (full reset single-player, player-only reset online); Aldric quest intro localized (~12 strings)
- `Scripts/Systems/CombatEngine.cs` -- Armory +5%/lv attack bonus in single and multi-monster paths; Barracks +5%/lv defense bonus; Training +5%/lv XP bonus in all 3 XP paths; Infirmary +10%/lv potion healing in quick-heal and multi-potion paths; heal ability filter expanded to include buff abilities with `BaseHealing > 0`; suppressed "recovers 0 HP" messages; Aldric quest kill objectives localized; ~25 combat messages localized (Ocean's Voice crit, Death's Embrace, precision strike, ability headers, combat stamina, boss enrage, 12 party ability messages, corrosion); `ApplySpellEffects` AttackBonus now uses `TempAttackBonus` instead of PowerStance; multi-target Buff spells apply protection/attack to all teammates; angel/demon/dodge_next/tidal_reflect handlers added to `HandleSpecialSpellEffect`, `HandleSpecialSpellEffectOnMonster`, and `ApplyPvPSpellEffect`; Dispel Evil strips monster enrage/immunity/channeling + 20% defense instead of player charm; `WeakenRounds` set in both spell and ability weaken handlers; `double_vs_debuffed` expanded to check all debuff states; shadowstep defense-bypass handler added
- `Scripts/Systems/SpellSystem.cs` -- Tidal Ward spell now sets `SpecialEffect = "tidal_reflect"` for damage reflection
- `Scripts/Core/Items.cs` -- Condition labels (`Good`/`Fair`/`Poor`/`Bad`/`Broken`) localized via `Loc.Get("item.condition.*")`
- `Scripts/Systems/CompanionSystem.cs` -- Lyris `Abilities` updated from Paladin to Ranger abilities; stale "Paladin" comment corrected
- `Scripts/Systems/RelationshipSystem.cs` -- `Reset()` clears all relationships; `ResetPlayerRelationships(playerName)` clears only one player's relationships for online mode
- `Scripts/Systems/OpeningStorySystem.cs` -- All ~100 hardcoded English strings converted to `Loc.Get()` calls (dream, dormitory, mystery, letter, goals, NG+ opening, race/class descriptions)
- `Scripts/Systems/OnlineAdminConsole.cs` -- Level editor applies `ApplyClassStatIncreasesToSaveData()` for each level gained; new static helper mirrors `LevelMasterLocation.ApplyClassStatIncreases()` for all 16 classes on raw save data
- `Scripts/Locations/BaseLocation.cs` -- HQ upgrade bonuses displayed in `/health` Active Buffs section; dungeon floor cache invalidated on language change
- `Scripts/Locations/MainStreetLocation.cs` -- Aldric quest completion localized (visual and BBS paths)
- `Scripts/Locations/TeamCornerLocation.cs` -- Refresh cached HQ levels after upgrade purchase; `SaveAllSharedState()` after NPC recruitment in online mode
- `Scripts/Locations/MagicShopLocation.cs` -- Clear `_currentAccessoryCategory` in `SetupLocation()` and on `R` exit; prevents returning to stale sub-category on re-entry
- `Scripts/Locations/WeaponShopLocation.cs` -- Clear `currentCategory` in `SetupLocation()` and on `R` exit; same fix as Magic Shop
- `Scripts/Locations/DungeonLocation.cs` -- `InvalidateFloorCache()` method; merchant armor label "Def" → "AC"; merchant items force-identified on generation; Aldric quest dungeon/treasure objectives localized; merchant encounter input validation (re-prompts on invalid keys instead of dismissing); ~120 companion quest strings localized (Lyris shrine + Light That Was, Aldric Ghosts of the Guard, Mira Meaning of Mercy, Vex One More Sunrise, Melodia Lost Opus); Blood Moon atmosphere and Sunforged Blade sequence localized
- `Scripts/Systems/LootGenerator.cs` -- Equipment name localization: `LocalizeTemplateName()` converts English names to `item.*` keys; `GetRarityPrefix()` uses `Loc.Get("item.rarity.*")`; `BuildItemName()` localizes base name, effect prefix/suffix, cursed prefix; `GetLocalizedEffectPrefix/Suffix/Name()` helpers; `GetItemRarity()` checks localized prefixes; `CreateBasicWeapon/Armor()` use `Loc.Get("item.slot.*")`; effect descriptions use `GetLocalizedEffectName()`
- `Scripts/Systems/WorldBossSystem.cs` -- Spawn cooldown check using `GetLastWorldBossEndTime()`; `[Q] Back` menu fix
- `Scripts/Systems/SqlSaveBackend.cs` -- `GetLastWorldBossEndTime()` queries most recent defeated/expired boss timestamp
- `Localization/en.json` -- ~120 new keys: `opening_story.*`, `aldric_quest.*`; ~434 new `item.*` keys (263 template names, 6 rarity, 84 effect prefix/suffix/name, 5 condition, 10 slot); ~145 new keys: `combat.*` (25 combat messages), `dungeon.*` (12 atmosphere/Sunforged), `quest.lyris_shrine.*` (20), `quest.lyris_light.*` (33), `quest.aldric_ghosts.*` (35), `quest.mira_mercy.*` (27), `quest.vex_sunrise.*` (36), `quest.melodia_opus.*` (23); merchant prompt updated to `[T]/[A]/[L]` format
- `Localization/es.json` -- All ~120 story keys + ~434 equipment keys + ~145 combat/quest keys translated to Spanish; merchant prompt updated
- `Localization/hu.json` -- All ~120 story keys + ~434 equipment keys + ~145 combat/quest keys translated to Hungarian; merchant prompt updated
- `Localization/it.json` -- All ~120 story keys + ~434 equipment keys + ~145 combat/quest keys translated to Italian; merchant prompt updated
- `web/index.html` -- Corrected class stat bars for 8 classes (Warrior, Assassin, Magician, Cleric, Sage, Barbarian, Tidesworn, Abysswarden)
- `web/steam.html` -- Same class stat bar corrections plus Paladin DEF→WIS fix
