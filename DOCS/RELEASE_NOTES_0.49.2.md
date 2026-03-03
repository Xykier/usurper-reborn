# Usurper Reborn v0.49.2 - Armor Weight Classes, Combat & Balance

**Release Date**: March 2026

## Overview

Armor Weight Class system adds Light/Medium/Heavy classifications to all armor with class restrictions, race restrictions, and meaningful combat trade-offs (dodge, stamina, fatigue). New characters start with class-appropriate weapons. Early-game spell damage buffed and mana costs reduced for caster viability. Stun immunity prevents infinite stunlock from Spark and other stun effects. Magician Sleep/Fear/Web spells fixed — were broken in single-monster combat. Backstab now checks off-hand weapon. Potion cap enforced on quest rewards and merchant purchases. Shop level requirements enforced. PvP escape-then-death fix. Dungeon exit confirmation prevents accidental Q-chaining. Trophy Room gate fix, marriage duration counter now uses game days. Ancient Library shrine XP rebalanced. Enhanced trap detection for Assassins/Rangers. Race-class restrictions updated for armor weight system. Bank guard scaling overhaul, multi-monster tactical combat keys, spouse level-up at Level Master, varied dungeon boss names, blood price bounty ordering fix, companion Power Strike off-hand fix. Plus Steam achievement reliability, Team Corner relocation, caster survivability buffs, and NPC/companion skill proficiency training.

## New Features

### Armor Weight Class System

All armor pieces are now classified as **Light**, **Medium**, or **Heavy**, creating meaningful gear decisions beyond raw AC numbers. Light armor users are faster and dodge more, while Heavy armor users have the best protection but tire faster.

**Combat effects by armor tier** (determined by heaviest equipped armor piece):

| Bonus | Light | Medium | Heavy |
|-------|-------|--------|-------|
| Dodge | +10% | +5% | +0% |
| Max Stamina | +20 | +10 | +0 |
| Stamina Regen | +2/round | +1/round | +0 |
| Fatigue | -30% | normal | +25% |

**Class restrictions** — each class archetype has a maximum armor tier:
- **Casters** (Magician, Sage): Light only
- **Hybrids** (Assassin, Bard, Jester, Alchemist, Ranger): Light & Medium
- **Tanks** (Warrior, Paladin, Barbarian, Cleric): All types
- **Prestige classes**: All types (they've earned it)

**Race restrictions** — small races (Hobbit, Gnome) cannot wear Heavy armor.

**STR requirement** — Heavy armor has an auto-calculated Strength requirement (15 + itemLevel/5) that prevents undergeared characters from wearing plate.

**Display integration**:
- Armor shop shows color-coded `[Light]` (cyan), `[Medium]` (yellow), `[Heavy]` (red) tags on each item
- Inventory/equipment screen shows weight class tags on equipped armor
- Character creation shows armor restriction info on class selection ("Armor: Light only", "Armor: Light & Medium", "Armor: All types") and small race warning on race selection
- Equip-time enforcement with descriptive error messages ("Your class cannot wear Heavy armor", "Your race is too small for Heavy armor", "Requires 17 Strength for Heavy armor")

**Weight class inference** — armor pieces are classified by name keywords: Plate/Greathelm/Gauntlet/Titan keywords = Heavy; Chain/Scale/Studded/Brigandine keywords = Medium; everything else (Cloth, Leather, Silk, Shadow, Robe) = Light.

**Backward compatible** — old saves load fine; items default to `None` which is treated as Light (no penalty).

### Spouse Level-Up at Level Master

Your married spouse can now benefit from your experience. The Level Master's "Share Experience" menu now includes your NPC spouse alongside team members and companions. Spouses appear with a `[Spouse]` tag and are listed first. If your spouse is also on your team, they won't be listed twice.

### NPC & Companion Skill Proficiency Training

NPCs and companions now improve their skill proficiencies over time, making them more effective combatants as the game progresses.

**NPCs train at the Gym**: When NPCs visit the Gym during world simulation, they now have a 30% chance to also improve a skill proficiency (basic attack or a random class ability) alongside their stat training. NPC proficiency is capped at **Expert** (level 5 of 8) — strong but not matching players.

**Companions improve through combat**: When companion teammates attack in combat, they now have a chance to improve their basic attack proficiency through use (same system as the player). Companion proficiency is capped at **Superb** (level 6 of 8) — one tier higher than NPCs since they're special characters.

**Full persistence**: NPC and companion proficiency data is fully serialized in save files, so progress persists across save/load cycles.

Proficiency caps keep players special:
- **Players**: Legendary (level 8) — unchanged
- **Companions**: Superb (level 6)
- **NPCs**: Expert (level 5)

### Multi-Monster Tactical Combat Keys

All tactical combat actions are now available in multi-monster combat, matching single-monster combat:
- `[P]` Power Attack, `[E]` Precise Strike, `[K]` Backstab, `[T]` Taunt, `[W]` Disarm, `[L]` Hide, `[G]` Rage (Barbarian)
- Each action prompts for target selection among living monsters
- Previously these keys were only recognized in single-monster fights

### Magic Shop Procedural Accessory Inventory

The Magic Shop's accessory browsing has been overhauled to use procedural inventory generated from loot templates — the same system that powers the Weapon Shop and Armor Shop since v0.49.0. The old hand-crafted static inventory of ~70 accessories has been replaced with procedurally generated rings and necklaces covering levels 1-100.

**New browsing experience:**
- `[1] Rings` and `[2] Necklaces` replace the old `[A]ccessories` single-list menu
- Paginated display (15 items per page) with level filtering, rarity-colored names, affordability dimming, and upgrade [+]/[-] indicators
- Currently equipped item shown at the top with stat bonuses
- Ring purchases prompt for Left/Right finger when both slots are occupied
- Full tax breakdown and quest completion tracking on purchase

**Sell improvements:**
- Dedicated `[S]ell Accessories` screen lists non-equipped accessories from inventory
- `[A]ll` option to sell all accessories at once
- Individual sale by number

**What stays the same:**
- All 10+ services unchanged: enchanting, curse removal, identification, potions, love spells, dark arts, spell study, scrying, talk to owner
- No class or race restrictions on accessories (open to all)

### Old God Aftermath — Town Reaction Scene

After defeating, saving, or allying with an Old God (floors 25-95), you now automatically emerge from the dungeon into a cinematic scene where the townsfolk react to what you did. The scene has three beats:

1. **Emergence** — climbing out of the darkness into blinding sunlight
2. **Crowd reaction** — 2-4 lines of unique dialogue based on which god you faced, the outcome, and your dialogue approach
3. **Closing** — a brief moment of reflection before you arrive on Main Street

Each of the 6 non-Manwe gods has unique crowd reactions for every combination of outcome (Defeated/Saved/Allied/Spared) and approach (aggressive, humble, merciful, diplomatic, etc.). Examples:

- Defeat Maelketh with rage: *"The crowd parts before you. A veteran mutters, 'Killed a god of war with pure rage. What does that make them?'"*
- Save Aurelion: *"Golden light breaks through the clouds. The crowd falls to their knees, tears streaming. 'THE LIGHT RETURNS!'"*
- Ally with Noctura: *"People avoid your eyes. The shadows you cast seem... longer than they should be."*

Manwe (floor 100) is excluded — the final boss has its own ending sequence. Fleeing or being defeated also skips the scene (you stay on the dungeon floor).

### Varied Dungeon Boss Names

Each dungeon theme now has a pool of 5 unique boss names instead of a single fixed name. The boss name is deterministic per floor level, so the same floor always has the same boss. Examples: Catacombs bosses include Bone Lord, Crypt Warden, Skull Revenant, Tomb Sentinel, and Ossuary King.

## Bug Fixes

### Blood Price Bounty Ordering Fix

Killing an NPC with an active bounty on them could incorrectly apply blood price (murder weight) because the bounty initiator was checked *after* `AutoCompleteBountyForNPC()` marked the quest as Deleted. Since `GetActiveBountyInitiator()` filters on non-deleted quests, the check would always return null — making sanctioned Crown/Shadows kills look like unprovoked murders. The bounty initiator is now retrieved *before* quest completion.

### Companion Power Strike Off-Hand Fix

The Power Strike ability's off-hand follow-up attack (for dual-wielding characters) was gated on `isPlayer`, preventing companions and teammates from getting their off-hand swing after using Power Strike. The check now only requires `player.IsDualWielding`, so any dual-wielding combatant gets the follow-up.

### Steam Achievement Reliability

A player reported receiving 29 achievements immediately upon opening the game for the first time. Investigation revealed two code issues in Steam stat synchronization:

- **Per-stat StoreStats() removed**: Previously, every individual `SetStat()` call triggered `StoreStats()`, causing Steam's server-side achievement evaluation to run 15 times per save (once per stat + one batch call). Now there's a single `StoreStats()` call at the end of `SyncPlayerStats()`, triggering evaluation exactly once.
- **Brand-new character guard**: Stat sync is now skipped entirely for characters with no meaningful stats (zero kills, zero damage, level 1, zero gold, <1 minute playtime). This prevents pushing all-zero stats to Steam that could trigger spurious server-side achievement evaluation.
- **Better diagnostics**: Steam stat sync now logs all key stat values when pushing to help diagnose any future issues. Achievement unlocks logged at WARNING level for visibility.

### Stun Immunity (Spark Stunlock Fix)

Stun effects (Spark, lightning enchant, Web, abilities) could stunlock monsters indefinitely — each cast reapplied stun before the monster ever got a turn. Monsters now gain 1 round of stun immunity after any stun expires, creating an alternating pattern: stunned → acts (immune) → vulnerable to stun again. This matches how stun already worked against town NPCs, where the target always gets a turn between stuns.

The immunity system applies to all 12+ stun application paths: spell stun, lightning enchant procs, ability stun effects (legendary shot, temporal prison, dissonant wave), poison stun (Nightshade Extract, Widow's Kiss), web entangle, and holy pacify.

### Magician Sleep, Fear & Web Spells Fixed

Three Magician spell effects were broken in single-monster combat due to a legacy spell handler (`HandleSpecialSpellEffect`) that was never updated alongside the modern multi-monster handler:

- **Sleep**: Worked but used a hardcoded 3-round duration instead of SpellSystem's calculated 3-7 rounds (scaling with level). Now uses the actual calculated duration.
- **Fear**: Same issue — hardcoded 3 rounds instead of the calculated 2-8 rounds. Now uses the actual calculated duration.
- **Web**: Completely missing from the handler's switch statement — the spell consumed mana and showed the cast message but did absolutely nothing. Added proper web handling (sets IsStunned with calculated duration).

Poison and freeze effects in the same handler were also updated to use calculated durations instead of hardcoded values. The multi-monster spell path was already correct and is unaffected.

### Race-Class Restriction Update

Hobbit and Gnome Paladin restrictions removed. The armor weight class system now mechanically prevents small races from wearing Heavy armor (which Paladins typically use), making the hard class restriction redundant. Both races retain their Barbarian restriction (too small for berserker rage). Orc restriction reason text corrected — previously cited "holy pursuits" despite allowing Cleric.

### Potion Cap Overflow Fix

Quest rewards could give more potions than the max cap (50), overfilling the player's inventory. This caused "Buy Max Potions" at merchants to display a negative gold cost (e.g., "-13,420g") since the calculation `maxPotions - currentPotions` went negative. Four sites fixed:

- **Quest rewards**: Now cap potions at max and show "(capped at 50)" when truncated
- **Dungeon merchant display**: Shows "full!" instead of a negative gold amount when already at or over cap
- **Dungeon merchant partial purchase**: "Could only afford X" path now can't exceed the cap
- **Magic shop**: Now checks potion room before allowing purchase and caps the buyable quantity

### Trophy Room Gate Fix

The `[T] Trophies` option at Home was accessible even without purchasing the Trophy Room upgrade. Now properly checks ownership and shows a message directing players to the Upgrades menu if they haven't bought it yet.

### Marriage Duration Counter Fix

Marriage duration displays throughout the game (Home family view, relationship discussion, Love Street) were using real-world `DateTime.Now` instead of the in-game day counter, causing marriages to show "0 days" even after multiple game days had passed. All marriage and divorce duration displays now use `MarriedGameDay` / `DivorceGameDay` tracked via `DailySystemManager.CurrentDay`. Old saves without game-day data gracefully fall back to the previous real-time calculation.

### Starting Equipment for New Characters

New characters now start with a class-appropriate weapon instead of being unarmed. This prevents the confusing situation where a freshly created Magician can't cast spells (which require a Staff) or an Assassin can't use Backstab (which requires a Dagger).

Starting weapons by class:
- **Magician, Sage, Cleric**: Wooden Staff (2H, 3 power)
- **Assassin, Jester, Alchemist**: Rusty Dagger (1H, 4 power)
- **Ranger**: Short Bow (2H, 5 power)
- **Warrior, Paladin, Bard**: Dull Sword (1H, 5 power)
- **Barbarian**: Crude Axe (1H, 5 power)

### Backstab Off-Hand Weapon Check

Backstab (and other abilities with weapon requirements) now checks both main hand AND off-hand for the required weapon type. Previously, an Assassin dual-wielding with a dagger in the off-hand couldn't use Backstab because only the main hand was checked. This also fixes weapon requirement display in the greyed-out quickbar.

### Spell Damage & Mana Cost Rebalance

Early-game spell damage was ~2.5x weaker than melee attacks, making casters feel underpowered until mid-game. Base damage increased for early spells and mana costs reduced:

| Spell | Old Damage | New Damage | Old Mana | New Mana |
|-------|-----------|-----------|----------|----------|
| Magic Missile | 10-18 | 18-28 | 5 | 4 |
| Spark | 18-28 | 28-40 | 10 | 8 |
| Frost Touch | 28-42 | 40-58 | 18 | 14 |
| Arcane Shield | — | — | 8 | 7 |
| Sleep | — | — | 15 | 12 |

INT scaling rate also increased from 0.04 to 0.05 per point (below soft cap), making Intelligence investment more impactful across all spell levels.

### Shop Level Requirement Enforcement

Weapon and armor shops now check the item's minimum level requirement before allowing purchase. Previously, players could buy high-level equipment they couldn't effectively use.

### PvP Escape-Then-Death Fix

Players who successfully escaped from PvP combat could still die from damage-over-time effects (poison, bleed) processed in the same round after escape. The escape check now happens before status effect processing, so escaped players cleanly exit combat.

### Empty Enter Key in Multi-Monster Combat

Pressing Enter without typing an action in multi-monster combat previously showed "Invalid action, please try again." Now silently re-prompts, matching single-monster combat behavior.

### Dungeon Exit Confirmation

Pressing Q to leave the dungeon from the floor overview now asks for confirmation ("Leave the dungeon and return to town? Y/N"). This prevents accidentally exiting the dungeon when pressing Q through submenus (e.g., potion menu Q → room Q → floor overview Q would chain through three menu levels).

### Ancient Library Shrine XP Rebalance

Ancient Library shrines were granting excessive XP (500-50,000 per shrine, scaling as `floor × 500`). Reduced to `50 + floor × 15` (65-1,550 XP range), making shrines a nice bonus without being an exploit.

### Assassin Trap Detection Enhancement

Assassins, Rangers, Jesters, and Bards now have enhanced trap detection and evasion in the dungeon:

**Trap evasion bonuses** (added to base AGI/3 chance):
- Assassin: +15 + DEX/8 (best in class)
- Ranger: +10 + DEX/12
- Jester/Bard: +5
- Evasion cap raised from 75% to 85%

**Trap detection hints** — rooms with traps now show class-specific warnings:
- Assassin: 65% detection ("Your trained eye catches signs of a trap mechanism...")
- Ranger: 50% detection ("Your wilderness instincts warn you of danger...")
- Jester: 40% / Bard: 35% detection
- Other classes: 25% detection ("Something feels wrong about this room...")

### Traveler's Sandals Stat Mismatch Fix

Traveler's Sandals (Assassin/Ranger footwear) were getting caster stats (+Int, +Wis, +Mana) instead of rogue stats (+Dex, +Agi, +Crit). The armor type inference classified them as Cloth due to "Sandals" and "Traveler" keywords matching the Cloth block. Moved both keywords to the Leather block so they correctly receive Dex/Agi/Crit bonuses. Also fixes Traveler's Cloak (all-class) which now gets more universally useful Dex/Agi bonuses instead of caster stats.

### Poison Duration & Antidote System Overhaul

**Poison now has a proper turn-based duration** instead of a near-permanent intensity value with a 5% per-turn chance to decrease. When poisoned, you'll see how many turns remain (e.g., "(-5 HP, 8 turns left)"), and the poison clears automatically when the counter reaches zero. Poison duration scales with dungeon depth (5 + floor/5 turns for traps).

**Antidotes are now storable consumables** instead of instant-cure services:
- **Carry limit**: 5 + level/10 antidotes (scales 5-15)
- **Buy from**: Healer (`[N] Buy Antidotes`, 75g each) or dungeon merchant (`[3] Antidote`, 75g — uses immediately if poisoned, stores if not)
- **Use from**: Dungeon potion menu (`[D] Use Antidote`), or `/antidote` quick command from any location
- **Old behavior preserved**: Healer's `[P] Poison Cure` service still works as instant cure; dungeon sanctuary still cures poison

Previously, dungeon merchant antidotes were instant-cure services that couldn't be stored — players would buy them when not poisoned and nothing would happen. Now buying when not poisoned stores the antidote for later use.

### Training System Roll Display Fix

The `RollAbilityCheck()` in the Training System was displaying `Total = 0` on automatic failure rolls instead of the actual modifier total. Now correctly shows the rolled total even when the roll results in a critical failure.

### NPC Sleep Attacks Disabled

NPC-initiated attacks on sleeping (logged-out) players in online mode have been disabled. This feature punished casual players who couldn't play in long sessions and felt unfair — you'd log in to find you'd been killed overnight with no counterplay.

**Player-vs-player sleep attacks remain available** — the `[K] Attack Sleeper` option at the Inn and Dormitory is still there as a deliberate PvP choice.

## Enhancements

### Bank Guard Scaling Overhaul

Bank robbery guards were previously flat-statted (Captain: 100 + level*5 HP, Guard: 60 + level*3 HP), making robberies trivially easy at higher levels. Guards now scale using formulas comparable to dungeon monsters:
- **Captain**: ~1.5x a same-level dungeon monster (HP: 75*level + level^1.2 * 20; STR/DEF/WeapPow scale with level)
- **Regular Guards**: Roughly equal to same-level dungeon monsters (HP: 50*level + level^1.2 * 12)
- **Guard Pitbull**: Also level-scaled (HP: 30*level + level^1.1 * 10)

Robbing a bank is now a serious challenge at any level.

### Team Corner Relocated to Main Street

The Team Corner (where players form/join teams) has been moved from a sub-menu inside the Inn to Main Street, making it much easier to find. Previously buried as `[C]` inside the Inn's "Special Areas" section, it's now directly accessible from Main Street as `[Z] Team Corner`.

- Available at **level 5+** (Tier 3 menu disclosure)
- Appears in all three menu styles (classic, BBS compact, screen reader)
- Team Corner's "Return" option now goes back to Main Street instead of the Inn
- The Inn's `[C]` key is freed up (no longer navigates to Team Corner)

## Balance

### Dodge Cap Raised to 45%

The maximum dodge chance has been raised from 35% to 45% to accommodate armor weight bonuses. Characters in Light armor can now reach up to 45% dodge, making agility-focused builds more viable. Heavy armor characters are unaffected (no dodge bonus).

### Magician & Sage Survivability Buff

Combat telemetry revealed that level 34 Magicians were dying repeatedly to same-level monsters — often killed in a single hit before they could cast a spell. The root cause: Magicians gained **zero defense per level** and only **+3 HP per level**, giving a level 34 Magician just 101 HP against monsters dealing 200-300 damage per hit. Sage had the same problem but worse (+2 HP/level, zero CON growth).

**Magician per-level growth changes:**
| Stat | Before | After |
|------|--------|-------|
| HP | +3 | **+6** |
| DEF | +0 | **+1** |
| CON | +1 | **+2** |

**Sage per-level growth changes:**
| Stat | Before | After |
|------|--------|-------|
| HP | +2 | **+5** |
| DEF | +0 | **+1** |
| CON | +0 | **+1** |

Both classes remain the squishiest in the game (Warrior gets +12 HP, +2 DEF, +3 CON per level), but can now survive 2-3 monster hits instead of dying instantly. This gives casters time to actually cast their spells before being one-shot. Existing characters will benefit on their next level-up.

### Backstab Cooldown Increase

Backstab cooldown increased from 1 to 2 rounds. Previously, the cooldown of 1 ticked down to 0 before the player's next turn, making it effectively usable every round. With cooldown 2, Backstab genuinely skips one round between uses, preventing infinite high-damage stunlock combos with high-proficiency Assassins.

## UI & Polish

### Old God Banner Centering Fix

The "DEFEATED" and "SAVED" banners shown after Old God boss encounters had misaligned right edges because the title text was padded with hardcoded spaces that didn't account for varying god name lengths. Now uses dynamic `PadLeft`/`PadRight` centering for consistent box alignment regardless of name length.

### Main Street Menu Column Alignment

The Main Street classic menu had inconsistent column widths (some items 12 chars, some 13, with extra gap insertions in certain rows). Rewrote with helper functions using a uniform 15-character column width for proper grid alignment across all 7 menu rows.

### Magic Shop Upgrade/Downgrade Legend

The accessory browsing pages now show a legend line explaining the `[+]` and `[-]` indicators: `[+] upgrade  [-] downgrade vs equipped` — displayed below the column header when the player has something equipped in that slot.

### "Look" Command Hint Gated to MUD Mode

The "type look to redraw menu" hint at the bottom of location screens was showing in single-player and BBS modes where the `look` command isn't available. Now only displays in MUD server mode where the command actually works.

## Files Changed

- `Scripts/Data/EquipmentData.cs` — Added `GetShopRings()` and `GetShopNecklaces()` query methods for procedural accessory shop inventory
- `Scripts/Locations/MagicShopLocation.cs` — Replaced static ~70-item accessory inventory with procedural browsing via EquipmentDatabase; added `AccessoryCategory` enum with category state pattern (`_currentAccessoryCategory`, `_accessoryPage`); new methods: `GetShopItemsForCategory()`, `ShowAccessoryCategoryItems()`, `ProcessAccessoryCategoryChoice()`, `BuyAccessoryItem()` (with ring finger selection prompt), `SellAccessory()` (with Sell All); removed `InitializeMagicInventory()` (~250 lines of static AddMagicItem calls), `AddMagicItem()` helper, `SellItem()`, `GetMagicInventory()`, old `BuyAccessory()` loop; updated menus: `[1] Rings` and `[2] Necklaces` replace `[A]ccessories` in both standard and BBS compact displays
- `Scripts/Core/GameConfig.cs` — Version 0.49.2; 12 armor weight class constants; `GetMaxArmorWeight()` and `IsSmallRace()` helpers; NPCProficiencyCap, CompanionProficiencyCap, NPCGymProficiencyChance constants; Hobbit/Gnome Paladin restriction removed; Orc reason text corrected
- `Scripts/Core/EquipmentEnums.cs` — `ArmorWeightClass` enum (None/Light/Medium/Heavy); `ArmorTypeExtensions` class with `GetWeightClass()` and `GetWeightColor()` extension methods
- `Scripts/Core/Items.cs` — `WeightClass` property on Equipment; class/race/STR enforcement in `CanEquip()`; `WeightClass` in `Clone()`
- `Scripts/Core/Character.cs` — `GetArmorWeightTier()` method (scans equipped armor, returns heaviest); armor weight bonus in `MaxCombatStamina`; armor regen bonus in `RegenerateCombatStamina()`; `Antidotes`, `MaxAntidotes`, `PoisonTurns` properties
- `Scripts/Core/GameEngine.cs` — Restore NPC proficiency from save data in `RestoreNPCs()`; restore `Antidotes` and `PoisonTurns` from save data
- `Scripts/Systems/StatEffectsSystem.cs` — Armor weight dodge bonus in `RollDodge()`; dodge cap raised 35%→45%; INT scaling rate 0.04→0.05 per point
- `Scripts/Core/Monster.cs` — Added `StunImmunityRounds` property for stun immunity system
- `Scripts/Systems/CombatEngine.cs` — Armor fatigue multiplier on combat fatigue; weight class assignment in `ConvertLootItemToEquipment()`; multi-monster tactical keys (P/E/K/T/W/L/G); companion Power Strike off-hand `isPlayer` guard removed; teammate proficiency training in both `ProcessTeammateAction` variants; stun immunity: 3 expiry paths set immunity, 12+ application paths check immunity; legacy `HandleSpecialSpellEffect()` fixed: added `duration` parameter, web case, use calculated durations for sleep/fear/poison/freeze; PvP escape-then-death fix (check `globalEscape` before status effects); empty Enter key silently re-prompts in multi-monster combat; `PoisonTurns = 0` on all poison cure paths (oceans_embrace, eternal_vigil, purify, cure_disease)
- `Scripts/Systems/ShopItemGenerator.cs` — `InferArmorWeightClass()` public static method (keyword-based Heavy/Medium/Light); weight class assigned in `GenerateArmorItems()`; `InferArmorType()` fix: moved "Traveler" and "Sandals" keywords from Cloth to Leather block so Assassin/Ranger footwear gets Dex/Agi/Crit bonuses instead of Int/Wis/Mana
- `Scripts/Systems/InventorySystem.cs` — Color-coded weight class tags in `DisplaySlot()`
- `Scripts/Systems/CharacterCreationSystem.cs` — Armor restriction info in `BuildClassStatsPanel()`; small race warning in `ShowRacePreviewCard()` and `BuildStatsPanel()`; `GiveStartingWeapon()` gives class-appropriate weapon at creation
- `Scripts/Systems/SteamIntegration.cs` — Removed per-stat `StoreStats()` from `SetStat()`; added brand-new character guard in `SyncPlayerStats()`; enhanced diagnostic logging
- `Scripts/Systems/WorldSimulator.cs` — Disabled `ProcessNPCAttacksOnSleepers()` call; NPC gym proficiency training (30% chance); `GetRandomNPCTrainingSkill()` helper
- `Scripts/Systems/CompanionSystem.cs` — Added `SkillProficiencies` and `SkillTrainingProgress` to Companion class and CompanionSaveData; proficiency copy-in at `GetCompanionsAsCharacters()`; sync-back via `SyncCompanionProficiency()`; serialize/deserialize support
- `Scripts/Systems/SaveSystem.cs` — Serialize/restore NPC and companion proficiency data
- `Scripts/Systems/ClassAbilitySystem.cs` — Added proficiency cap parameter to `TryImproveFromUse()` call; `CanUseAbility()` and `GetWeaponRequirementReason()` check both main hand and off-hand for required weapon types; Backstab cooldown increased 1→2
- `Scripts/Systems/SpellSystem.cs` — Added proficiency cap parameter to both `TryImproveFromUse()` calls; Magic Missile/Spark/Frost Touch base damage increased; Magic Missile/Arcane Shield/Spark/Sleep/Frost Touch mana costs reduced; spell descriptions updated
- `Scripts/Systems/StreetEncounterSystem.cs` — Blood price bounty ordering fix: check `GetActiveBountyInitiator()` before `AutoCompleteBountyForNPC()`
- `Scripts/Locations/LevelMasterLocation.cs` — Magician: HP +3→+6, DEF +0→+1, CON +1→+2; Sage: HP +2→+5, DEF +0→+1, CON +0→+1; spouse added to Share Experience menu with `[Spouse]` tag
- `Scripts/Systems/OldGodBossSystem.cs` — Added `ApproachType` to `BossEncounterResult`; set on all return paths (Defeated, Saved, Allied, Spared); dynamic PadLeft/PadRight centering for DEFEATED/SAVED banners
- `Scripts/Locations/BaseLocation.cs` — Weight class inference in `ConvertInventoryItemToEquipment()`; poison turn-based duration in `ApplyPoisonDamage()` with old-save migration; `/antidote` quick command; "look" hint gated behind MUD mode
- `Scripts/Locations/BankLocation.cs` — Bank guard scaling overhaul: Captain ~1.5x dungeon monster, Guards ~1x, Pitbull level-scaled
- `Scripts/Systems/QuestSystem.cs` — Potion reward capped at MaxHealingPotions
- `Scripts/Locations/AdvancedMagicShopLocation.cs` — Potion purchase capped at remaining room; "already at max" guard
- `Scripts/Locations/DungeonLocation.cs` — Dungeon room fatigue scaled by armor weight; varied boss name pools (5 per theme, deterministic per floor); merchant Buy Max display uses `Math.Max(0, ...)` to prevent negative cost; partial purchase capped at potionsNeeded; dungeon exit confirmation prompt; Ancient Library shrine XP reduced (`floor*500` → `50+floor*15`); enhanced trap evasion with class-specific bonuses and DEX scaling; trap detection hints with class-specific messages and detection chances; `PoisonTurns` set on all 4 poison application sites; merchant antidote stores when not poisoned; antidote `[D]` option in potion menu; sanctuary clears `PoisonTurns`; new `ShowTownReactionScene()` and `GetTownReactionLines()` methods with per-god crowd reactions; auto-return to Main Street after Old God encounters
- `Scripts/Systems/TrainingSystem.cs` — Added `maxLevel` parameter to `TryImproveFromUse()`; added `GetProficiencyCapForCharacter()` helper; fixed `Total = 0` → `Total = totalMod` in automatic failure path of `RollAbilityCheck()`
- `Scripts/Locations/WeaponShopLocation.cs` — Level requirement check before purchase confirmation
- `Scripts/Locations/ArmorShopLocation.cs` — Color-coded weight class tags in shop listing; level requirement check before purchase confirmation
- `Scripts/Locations/HomeLocation.cs` — Trophy Room `[T]` gate (requires purchase); marriage/divorce duration displays use game days with old-save fallback
- `Scripts/Locations/LoveStreetLocation.cs` — Marriage duration displays use game days with old-save fallback
- `Scripts/Systems/RomanceTracker.cs` — Added `MarriedGameDay` to Spouse; added `MarriedGameDay`/`DivorceGameDay` to ExSpouse; set game day at marriage/divorce/death; export/import updated
- `Scripts/Systems/SaveDataStructures.cs` — Added `MarriedGameDay` to SpouseData; added `MarriedGameDay`/`DivorceGameDay` to ExSpouseData; added proficiency fields to NPCData and CompanionSaveInfo; added `Antidotes` and `PoisonTurns` fields
- `Scripts/Locations/MainStreetLocation.cs` — Added `[Z] Team Corner` to all 3 menu styles in tier 3; added `case "Z"` with level 5+ gate; rewrote `ShowClassicMenu()` with uniform 15-char column width for grid alignment
- `Scripts/Locations/MagicShopLocation.cs` *(additional)* — Added [+]/[-] upgrade/downgrade legend line in accessory category view
- `Scripts/Locations/InnLocation.cs` — Removed Team Corner navigation
- `Scripts/Locations/TeamCornerLocation.cs` — Changed `PossibleExits` from `TheInn` to `MainStreet`; updated return navigation
- `Scripts/Systems/LocationManager.cs` — Updated navigation table: TeamCorner now connects to MainStreet (was TheInn); `PoisonTurns = 0` on death resurrection
- `Scripts/Locations/HealerLocation.cs` — `[N] Buy Antidotes` menu option and `BuyAntidotes()` method; `PoisonTurns = 0` on poison cure
- `Scripts/Locations/ArenaLocation.cs` — `PoisonTurns = 0` on PvP death
- `Scripts/Locations/DevMenuLocation.cs` — `PoisonTurns` set on poison debug commands
- `Scripts/Systems/RareEncounters.cs` — `PoisonTurns` set on poison fountain; `PoisonTurns = 0` on rare cures
- `Scripts/Systems/StreetEncounterSystem.cs` — `PoisonTurns` set on poison vial; `PoisonTurns = 0` on street antidote
- `Scripts/Systems/SaveSystem.cs` — Serialize/restore `Antidotes` and `PoisonTurns`
