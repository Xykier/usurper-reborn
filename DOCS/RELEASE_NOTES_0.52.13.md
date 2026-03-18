# Usurper Reborn v0.52.13 Release Notes

**Release Date:** March 17, 2026
**Version Name:** The Hook

## Comprehensive Translation Pass — Hungarian, Italian, French

Full translation audit and completion pass across all three non-Spanish secondary languages. Previously, many keys added in v0.51.x and v0.52.x localization passes received English placeholder text in Hungarian, Italian, and French instead of proper translations. This pass identified and translated all remaining English placeholders:

- **Hungarian (hu.json)**: 714 keys translated — combat system messages (alchemist/cleric abilities, boss mechanics, enchantment procs, prestige passives, buff/debuff messages), home/relationship dialogue (intimacy, divorce, lifestyle discussions), prison walk (guard combat, escape mechanics, sentencing), street encounter results, and miscellaneous UI text.
- **Italian (it.json)**: 762 keys translated — same categories as Hungarian plus additional shop UI, team labels, quest progress text, and guild system strings.
- **French (fr.json)**: 915 keys translated — same categories plus achievement category names, faction headers, combat display headers, and wilderness/settlement UI text.

## Missing Alchemist Party Ability Keys

14 missing localization keys for Alchemist party abilities added to all 5 languages. These keys were referenced in CombatEngine but never added to any localization file, causing raw key names to display during gameplay when using Stimulant Brew, Smoke Screen, Battle Brew, Antidote Bomb, and Remedy abilities with party members present.

Keys added:
- `combat.alch_stimulant_self/other` + `combat.tm_energized` — Stimulant Brew party messages
- `combat.alch_smoke_self/other` + `combat.tm_obscured` — Smoke Screen party messages
- `combat.alch_brew_self/other` + `combat.tm_empowered` — Battle Brew party messages
- `combat.alch_antidote_self/other` + `combat.tm_ailments_cured` — Antidote Bomb party messages
- `combat.alch_restored_self/other` — Remedy party messages

## Monk Potion Vendor Translation Fix

9 combat monk potion vendor keys (`combat.monk_both`, `combat.monk_healing`, `combat.monk_mana`, `combat.monk_mana_option`, `combat.monk_no_thanks`, `combat.monk_your_gold`, `combat.monk_how_many`, `combat.monk_buy_limit`, `combat.monk_gold_remaining`) were displaying as raw key names in Hungarian and Italian. Now properly translated in both languages.

## Localization File Sync

All 5 language files synced to 16,473 keys each. Missing `_comment_*` section header keys (33 in Spanish, 57 in Hungarian, 56 in Italian) copied from English. One orphaned comment key (`_comment_dungeon_rooms`) removed from Hungarian. All language files now have identical key sets with zero missing functional keys and zero English placeholder text.

## Assassin Class Audit & New Ability

New **Biaxin** ability for Assassins at level 35 — coat your blade with a rare paralytic toxin that deals damage, poisons the target (4 rounds), and corrodes their armor (-40% DEF for 4 rounds). Requires dagger. Works across single-monster, multi-monster, and PvP combat. Armor corrosion uses the duration-tracked `IsCorroded` system so it properly expires rather than permanently reducing stats.

New **Lethal Precision** passive for Assassins — +25% crit damage when wielding a dagger, +10% bonus damage against poisoned targets. Synergizes with Biaxin and other poison abilities. Displayed in `/health` Active Buffs.

**Execute ability fix** — the Execute ability (`SpecialEffect = "execute"`) had no matching handler in either the single-monster or multi-monster combat paths. Warriors, Barbarians, and Assassins using Execute dealt zero bonus damage. Now properly applies tiered bonus damage: 2x below 30% HP, 50% below 50% HP, message-only above 50%.

## Arcane Shield Spell Feedback

Protection spells (Arcane Shield, etc.) now show prominent success/failure messages during casting. The D20 roll system causes ~45% failure rate for untrained casters, but there was no visual feedback — players couldn't tell if the spell actually worked. DEF display in combat status now shows `(+X)` breakdown when a magic AC bonus is active, with color change to bright_white.

## PressAnyKey Spacebar Fix

"Press any key" prompts in Steam/desktop builds now accept spacebar and any other key. Previously used `Console.ReadLine()` which required Enter — space just added to the input buffer. Fixed by using `Console.ReadKey(true)` in console mode. MUD and BBS modes are unaffected (still use line-based input).

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — `AssassinLethalPrecisionCritBonus` (0.25) and `AssassinLethalPrecisionPoisonBonus` (0.10) constants
- `Scripts/Systems/ClassAbilitySystem.cs` — New `biaxin` ability definition (level 35, Assassin, Dagger required, 45 stamina, 4 cooldown, 70 base damage); description updated to state `-40% DEF for 4 rounds`
- `Scripts/Systems/CombatEngine.cs` — Biaxin handlers in all 3 combat paths (single-monster, multi-monster, PvP) using `IsCorroded`/`CorrodedDuration` for PvE and `StatusEffect.Weakened`/`StatusEffect.Poisoned` for PvP; Execute effect handler added to both single and multi-monster paths; Assassin Lethal Precision passive in both single and multi-monster attack paths; Arcane Shield spell failure/success messages in both combat paths; DEF display shows `(+X)` magic AC bonus in all 3 display modes (visual, BBS, screen reader)- `Scripts/Locations/BaseLocation.cs` — Assassin Lethal Precision passive displayed in `/health` Active Buffs
- `Scripts/UI/TerminalEmulator.cs` — `PressAnyKey()` rewritten to use `Console.ReadKey(true)` in console mode for true any-key support
- `Localization/en.json` — 2 new keys: `combat.biaxin_apply`, `combat.biaxin_effect`; 14 Alchemist party ability keys
- `Localization/es.json` — 2 new Biaxin keys (Spanish); 14 Alchemist party ability keys; 33 missing comment keys synced
- `Localization/hu.json` — 2 new Biaxin keys (Hungarian); 714 previously-placeholder keys translated; 14 Alchemist party ability keys; 57 missing comment keys synced; 1 orphaned comment key removed
- `Localization/it.json` — 2 new Biaxin keys (Italian); 762 previously-placeholder keys translated; 14 Alchemist party ability keys; 56 missing comment keys synced
- `Localization/fr.json` — 2 new Biaxin keys (French); 915 previously-placeholder keys translated; 14 Alchemist party ability keys
