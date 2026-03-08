# Usurper Reborn v0.50.5 — Class Reworks & Equipment Stat Overhaul

Alchemist, Bard, Jester, and Magician class reworks, equipment stat system overhaul (Constitution/Intelligence replace raw HP/Mana), comprehensive equipment name-stat audit, protection spell scaling fix, world boss combat fixes, NPC teammate level-up persistence fix, and team recruitment bug fix.

## Class Reworks

### Alchemist Rework — Lean into Alchemy
Alchemist was a hybrid class that didn't hybridize anything — gained +3 INT/level but had no way to use it (no spells, no mana), while attack abilities scaled with STR which Alchemist never gained. Now repositioned as an INT-scaling bomb scientist with a unique Potion Mastery passive.

- **INT scales attack damage** — Alchemist bomb abilities now scale with Intelligence (3% per point) + Dexterity (1.5% per point) instead of Strength. At level 50, INT scaling hits the 5.0x cap, making Alchemist competitive with Warriors.
- **Potion Mastery passive** — +50% healing from all sources: healing potions in combat, herbs (Healing Herb heals 37.5% MaxHP instead of 25%), herb buff durations extended 50% (7 combats instead of 5), and ability heals (Healing Elixir, Greater Elixir, Transmutation). Displayed as a permanent passive in `/health`.
- **+2 Stamina per level** — Matches other physical classes, prevents ability starvation at higher levels.
- **Ability rebalance** — All 9 abilities retuned: higher base damage (Philosopher's Bomb 120->150, Acid Splash 50->60), lower stamina costs across the board, reduced cooldowns on heals. Transmutation duration extended from 5 to 6 rounds.
- **Character creation fix** — Mana preview showed "+10 mana/level" but Alchemist never gains mana. Now correctly shows "None (physical class)". Description updated to reflect new identity.

### Bard Rework — Party Support Through Song
Bard's identity was "musicians and storytellers" with 4 instrument-requiring song abilities — but every single one only buffed the Bard themselves. A Bard in a group of 5 played a song that only they could hear. Now Bard songs inspire the entire party.

- **Songs buff the entire party** — Inspiring Tune, Song of Rest, Veloura's Serenade, and Legend Incarnate now apply effects to all living teammates and companions. Teammates receive 75% of healing and 60% of attack/defense buffs. The Bard still gets the full effect.
- **CHA scales attack damage** — Bard (and Jester) attack abilities now scale with Charisma (3% per point) + Dexterity (1.5% per point) instead of Strength. With +4 CHA/level, Bard's primary stat now directly contributes to combat effectiveness.
- **+2 Stamina per level** — Up from +1 (base only), matching other physical classes.
- **Ability rebalance** — All abilities retuned: Vicious Mockery damage 22->30, Inspiring Tune +25 atk/def for 5 rounds (was +20/4), Song of Rest 55 heal (was 45), Veloura's Serenade 90 heal/+35 atk/def for 5 rounds (was 75/+30/4), Grand Finale 140 AoE (was 110), Legend Incarnate +65 atk/+60 def/+60 heal for 6 rounds (was +55/+55/5) plus self-regeneration. Lower stamina costs and cooldowns across the board.
- **Character creation fix** — Mana preview showed "+5 mana/level" but Bard never gains mana. Now correctly shows "None (physical class)". Description updated: "Party support through song. CHA scales damage. Songs buff the whole party."

### Magician Rework — Arcane Mastery
Magician was supposed to be the premier offensive caster, but had no unique advantage over Sage. Sage had higher INT growth (+5 vs +4/level), deeper mana (+18 vs +15/level), AND a Wisdom-based spell damage bonus that Magician lacked. Meanwhile, a legacy 5.0x damage cap in the spell system silently overrode the intentionally designed 8.0x soft cap, making high-INT investment pointless beyond INT ~90. Spellcasters were also locked out of universal abilities (Second Wind, Focus, Rally, Iron Will) at the Level Master because the menu redirected them straight to spells.

- **Arcane Mastery passive** — Magician-exclusive +15% spell damage multiplier. Displayed as a permanent passive in `/health`. This gives Magician a clear niche: Sage has deeper mana and Wisdom scaling, but Magician hits harder per spell.
- **Spell damage cap removed** — The `Math.Min(statBonus, 5.0)` cap in `ScaleSpellEffect()` was a pre-rework leftover that contradicted the soft cap in `StatEffectsSystem` (8.0x with diminishing returns above INT 85). High-INT investment now properly scales spell damage into endgame for all casters.
- **Spellcasters can now equip universal abilities** — Magician, Sage, and Cleric now get a hybrid [A] Abilities / [S] Spells menu at the Level Master instead of being locked into spells-only. All casters can now equip Second Wind, Focus, Rally, and Iron Will to their quickbar.
- **Spell description fixes** — 13 Magician spell descriptions showed lower damage than the code actually dealt (stale values from before damage tuning). Updated: Fireball 50-65→55-75, Lightning Bolt 55-70→60-80, Ice Storm 45-60→50-70, Chain Lightning 70-90→80-105, Disintegrate 100-130→110-150, Pillar of Fire 110-140→120-160, Meteor Swarm 120-150→130-180, Manwe's Creation 180-220→200-280, Power Word: Kill 250-320→280-380, plus protection values for Mirror Image, Prismatic Shield, Arcane Immunity, and Power Hat.
- **Character creation updated** — Description: "Arcane master. Arcane Mastery: +15% spell damage. Best offensive caster." Strengths: "Best INT. Arcane Mastery +15% spell damage."

### Jester Rework — Chaotic Trickster
Jester had the same problem as Bard — CHA-scaling abilities but STR-based basic attacks (with +0 STR/level), and a 25-level gap with no abilities between level 1 and level 26. Now repositioned as an unpredictable CHA-scaling trickster with a unique Trickster's Luck passive.

- **CHA scales basic attacks** — Jester (and Bard) basic attacks now use Charisma instead of Strength for damage calculation. With +3 CHA/level, Jester's primary stat now drives both ability and basic attack damage.
- **Trickster's Luck passive** — 20% chance on every basic attack to trigger one of three random effects: bonus damage (+50% attack power), dodge the next incoming attack, or restore 15 stamina. Displayed as a permanent passive in `/health`. Works in both single-monster and multi-monster combat.
- **2 new early abilities** — Juggler's Trick (lv10, 40 base dmg, no cooldown) and Pratfall (lv18, 50 base dmg, stun). Fills the 25-level gap where Jester previously had only Vicious Mockery from level 1 to level 26.
- **Carnival of Chaos buffed** — Base damage 140→200, stamina cost 75→70, now applies confusion to all surviving enemies (new `aoe_confusion` effect). Previously identical to Grand Finale (lv72) despite being 10 levels higher.
- **Character creation updated** — Description: "Chaotic trickster. CHA scales damage. Trickster's Luck: random combat procs." Strengths: "Best AGI and CHA. Trickster's Luck: bonus dmg, dodge, or stamina."

### Paladin Mana Preview Fix
Paladin character creation showed "+5 mana/level" but Paladin is not a mana class and never gains mana on level-up. Now correctly shows "None (physical class)".

## Improvements

### Equipment Stat Overhaul — Constitution & Intelligence Replace Raw HP/Mana
Equipment no longer grants flat +HP or +Mana bonuses. Instead, all equipment bonuses now use the primary stats: **Constitution** (which scales HP through the stat system) and **Intelligence** (which scales Mana through the stat system). This makes equipment stat bonuses consistent with the character stat system and ensures that Constitution/Intelligence on equipment actually feels impactful.

- **Loot generation** — All `item.HP` and `item.Mana` assignments in thematic bonuses, accessory templates, and enchantment effects replaced with Constitution and Intelligence via LootEffects
- **Loot-to-equipment conversion** — All 3 conversion paths in CombatEngine plus the inventory equip path in BaseLocation now transfer Constitution and Intelligence from LootEffects to Equipment
- **Loot display** — Dungeon loot drops now show "Con +X" and "Int +X" instead of "HP +X" and "Mana +X"
- **Equipment comparison** — Loot comparison screens show Constitution and Intelligence for both current and new items
- **Equipment-to-Item reverse conversion** — Unequipping preserves Constitution and Intelligence as LootEffects for proper re-equip
- **Backward compatible** — Existing saved equipment with MaxHPBonus/MaxManaBonus still works; only new drops use the new system
- **Accessory stat themes updated** — Rings and necklaces (Wisdom, Mage, Vitality, Dragon, etc.) all converted from HP/Mana to Constitution/Intelligence

### Equipment Name-Stat Audit
Comprehensive audit of all 267 armor templates to ensure item names match their stat bonuses. Previously only 36% of templates received thematic stats — the rest relied entirely on random effects, so a "Plate Mail" and a "Tattered Cloak" could get identical stats. Now ~95% of templates get appropriate bonuses:

- **Caster gear** (cloth, silk, robe, vestment) -> Intelligence
- **Holy gear** (holy, sacred, blessed, divine, priest, paladin, celestial) -> Wisdom + Constitution
- **Shadow gear** (shadow, thief, death, phantom, assassin, rogue) -> Dexterity + Agility
- **Warrior gear** (war, battle, titan, barbarian, knight, fighter, fortress, aegis) -> Strength + Constitution
- **Dragon gear** -> Strength + Defence + Constitution
- **Ranger gear** (ranger, scout, elven, forest, traveler, leather) -> Dexterity + Agility
- **Vitality gear** (vitality, endurance, resilience, fortitude, vigor) -> Constitution
- **Premium materials** (mithril, adamantine) -> Constitution + Defence
- **Metal armor** (iron, steel, chain, plate, banded, splint, studded, reinforced) -> Defence + Constitution

### Unit Tests for Thematic Bonuses
157 unit tests verify every armor template gets the correct stat group, check priority ordering (e.g., "Shadow Leather" matches shadow not leather), verify no false stat grants, and test power scaling.

## Bug Fixes

### World Boss Damage Too Low
World boss basic attacks were dealing only 1-2 damage to well-geared players. The damage formula allowed player defense to reduce boss damage to the floor of 1. Added a minimum damage of 20% of boss strength — world bosses should always feel dangerous regardless of player gear.

### World Boss Loot Reveals Name When Unidentified
Unidentified world boss loot displayed its real name in the reward message (e.g., "Shadowforged Robe of Vitality") instead of showing "Unidentified Armor." Both reward display paths (killing player and online participants) now use `GetUnidentifiedName()`.

### "Of Perfection" Items Missing Stats
The `AllStats` loot effect (used on "of Perfection" items) only granted Strength, Dexterity, Wisdom, and Charisma. Missing Agility, Intelligence, and Constitution. Now grants all 7 stats via LootEffects.

### "Of Vitality" Items Missing Thematic Bonus
Armor with "Vitality" in the name (e.g., "Robe of Vitality") had no thematic stat bonus because "vitality" wasn't in any keyword list. Items fell through to random effects only, resulting in mismatched stats like +DEX on vitality gear. Added vitality/endurance/resilience/fortitude/vigor keywords -> Constitution bonuses.

### "Knight" Matched "Night" Substring
"Knight's Shield" was matching the shadow keyword group because `"knight".Contains("night")` is true. Knight gear was getting DEX+AGI instead of STR+CON. Fixed by using `"night "` (with trailing space) in shadow keyword matching.

### Dead Team Members Not Counting Toward Team Limit
Dead (HP=0) team members were excluded from the team size count during NPC recruitment, allowing players to recruit past the 5-member cap. When dead members were resurrected, the team could exceed the limit. Team size now counts all non-permadead members regardless of HP.

### NPC Teammate Level-Ups Not Persisting
NPC team members showed "leveled up!" messages during combat XP distribution but reverted to their old level on the dashboard. Root cause: the world sim's `RestoreNPCsFromData()` clears `ActiveNPCs` and creates brand new NPC objects from the database. When this happens mid-dungeon (triggered by any player saving), the dungeon's `teammates` list still holds references to the old, orphaned NPC objects. Combat XP and level-ups modified the orphaned objects, which were never serialized. Added `SyncNPCTeammateToActiveNPCs()` that propagates XP, level, and stat changes from the teammate reference to the canonical NPC in `ActiveNPCs` after every XP award.

### Dark Alley Gold Logging
Added GOLD debug logging to pit fight wins/losses (monster and NPC), spectator bet results, and gambling den wins/losses for exploit tracking.

### Protection Spells Useless After Floor 10
Protection spells (Prismatic Shield, Arcane Shield, Divine Shield, etc.) granted tiny flat AC bonuses that didn't scale with level or Intelligence. At level 50, Prismatic Shield gave +57 AC while monsters rolled +150 attack — the spell was meaningless. All 20 protection spell bonuses across Cleric, Magician, and Sage spell schools now scale with level (2% per level, up to 3.0x at level 100) and Intelligence (1.5% per INT point above 10, soft cap at 2.5x above INT 60). At level 50 with INT 60, Prismatic Shield now gives ~199 AC instead of 57. Combo spells (Avatar, Time Stop, Wish) scale their protection and attack components independently.

### Observability Dashboard Race Names Wrong
The `RACE_NAMES` map in `dashboard.html` had completely wrong mappings — the order didn't match the `CharacterRace` enum, was missing Gnome and Half-Elf, used "Halfling" instead of "Hobbit", and only had 8 entries instead of 10. Every race except Human displayed incorrectly (e.g., Elves showed as Dwarves, Hobbits showed as Elves).

## Files Changed
- `Scripts/Core/GameConfig.cs` — Version 0.50.5; `AlchemistPotionMasteryBonus` constant (0.50); `MagicianArcaneSpellBonus` constant (1.15); `JesterTrickstersLuckChance` (20), `JesterLuckBonusDamage` (0.50), `JesterLuckStaminaRefund` (15)
- `Scripts/Systems/ClassAbilitySystem.cs` — INT scaling for Alchemist attacks; CHA scaling for Bard/Jester attacks; Potion Mastery +50% on ability heals; `party_song`/`party_legend` effects on Bard song abilities; rebalanced all 9 Alchemist abilities and 7 Bard abilities; Vicious Mockery damage 22->30; Grand Finale damage 110->140; 2 new Jester abilities (Juggler's Trick lv10, Pratfall lv18); Carnival of Chaos buffed (140→200 dmg, 75→70 STA, new `aoe_confusion` effect); removed spellcaster redirect from `ShowAbilityLearningMenu()` (casters can now access universal abilities)
- `Scripts/Systems/CombatEngine.cs` — New `ApplyBardSongToParty()` helper (75% heal, 60% buffs to all teammates); `party_song` and `party_legend` effect handlers in both single-monster and multi-monster combat paths; Alchemist Potion Mastery +50% on healing potions; CHA-based basic attacks for Bard/Jester in both combat paths; Jester Trickster's Luck proc (20% chance: bonus damage, dodge, or stamina refund) in both combat paths; `aoe_confusion` effect handler (AoE damage + confusion on survivors); boss basic damage minimum 20% of strength; loot display uses `GetUnidentifiedName()`; removed `WithMaxHP`/`WithMaxMana` from all 3 loot-to-equipment conversion paths; loot display shows Con/Int from LootEffects; equipment comparison includes Con/Int; accessory stat comparison uses LootEffects; new `SyncNPCTeammateToActiveNPCs()` propagates XP/level/stats from orphaned teammate references to canonical ActiveNPCs entries after combat XP distribution
- `Scripts/Systems/SpellSystem.cs` — Removed legacy 5.0x spell damage cap from `ScaleSpellEffect()` (StatEffectsSystem 8.0x soft cap now authoritative); Magician Arcane Mastery +15% spell damage multiplier; updated 13 Magician spell descriptions to match actual code damage values; new `ScaleProtectionEffect()` method with level scaling (2%/level) and INT scaling (1.5%/point, soft cap 2.5x); all 20 protection bonus assignments across Cleric, Magician, and Sage spell schools now use `ScaleProtectionEffect()`; combo spells (Avatar, Time Stop, Wish) scale protection independently from attack bonus
- `Scripts/Locations/LevelMasterLocation.cs` — +2 STA/level for Alchemist and Bard; spellcasters now get hybrid [A] Abilities / [S] Spells menu instead of spell-only redirect
- `Scripts/Locations/HomeLocation.cs` — Alchemist Potion Mastery: +50% Healing Herb effect, +50% herb buff durations (Ironbark, Firebloom, Swiftthistle, Starbloom)
- `Scripts/Locations/BaseLocation.cs` — Potion Mastery displayed as permanent passive in `/health` Active Buffs for Alchemist; Arcane Mastery displayed as permanent passive for Magician; Trickster's Luck displayed as permanent passive for Jester; equipment stat display adds Intelligence bonus; inventory equip path transfers CON/INT from LootEffects to Equipment
- `Scripts/Systems/CharacterCreationSystem.cs` — Fixed mana preview for Alchemist (10->0), Bard (5->0), and Paladin (5->0); updated Alchemist, Bard, Jester, and Magician class descriptions
- `Scripts/Systems/WorldBossSystem.cs` — Boss basic damage minimum 20% of strength; loot display uses `GetUnidentifiedName()`
- `Scripts/Systems/LootGenerator.cs` — Equipment stat overhaul: all `item.HP`/`item.Mana` assignments in `ApplyThematicBonuses()`, `CreateAccessoryFromTemplate()`, `ApplyEffectsToItem()`, and `ApplyCursePenalties()` replaced with Constitution/Intelligence via LootEffects; `ApplyThematicBonuses()` expanded from 8 to 9 keyword groups; holy group checked before caster; "night " trailing space fix; method visibility `internal` for testing
- `Scripts/Systems/ShopItemGenerator.cs` — All `MaxHPBonus` converted to `ConstitutionBonus` and all `MaxManaBonus` converted to `IntelligenceBonus` across weapon bonuses (Sword, Staff, Polearm, Instrument), shield bonuses (TowerShield, Standard), armor bonuses (Plate, Scale, Chain, Cloth, Magic/Artifact), and accessory bonuses (Protection, Wisdom, Power, Vitality, Gods/Planes)
- `Scripts/Core/Character.cs` — Equipment-to-Item reverse conversion preserves ConstitutionBonus/IntelligenceBonus as LootEffects
- `Scripts/Locations/ArmorShopLocation.cs` — Equipment display adds IntelligenceBonus
- `Scripts/Locations/MusicShopLocation.cs` — Instrument display shows INT/CON instead of Mana/HP
- `Scripts/Locations/MagicShopLocation.cs` — Enchanted item display adds Con/Int; Ocean's Touch enchantment grants Intelligence instead of MaxMana
- `Scripts/Locations/DarkAlleyLocation.cs` — GOLD logging on pit fights, spectator bets, and gambling den
- `Scripts/Locations/TeamCornerLocation.cs` — Team size count uses `!n.IsDead` instead of `n.IsAlive` to include dead-but-resurrectable members
- `usurper-reloaded.csproj` — `InternalsVisibleTo` for test project
- `Tests/ThematicBonusTests.cs` — Updated all 157 tests: HP assertions replaced with Constitution LootEffect checks; Mana assertions replaced with Intelligence LootEffect checks; AllStats test updated for new system
- `web/dashboard.html` — Fixed `RACE_NAMES` map to match `CharacterRace` enum (was wrong order, missing races, wrong names)
