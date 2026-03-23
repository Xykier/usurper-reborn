# Usurper Reborn v0.53.7 Release Notes

**Version Name:** Ancestral Spirits

## Execution Releases Prisoner

Executing an online player now properly releases them from prison. The DB update (`DaysInPrison = 0`) was already happening, but the player's in-memory state wasn't synced — they stayed trapped in the prison location despite having served their penalty.

## Dead Spouse Marriage Cleanup

Players married to NPCs who were permakilled stayed permanently "married" to a dead NPC — status showed married, but spending time or equipping the spouse showed no one available. Two fixes:

- **Login cleanup**: On login, if the player's spouse NPC is dead/missing, marriage flags are automatically cleared and the player is widowed
- **Future prevention**: When an NPC spouse is permakilled, the world sim sends a bereavement notification to the player

## Heal Spell Protection on Allies

Heal spells with protection bonuses (Power Hat, Prismatic Shield, etc.) only applied the protection to the caster, even when cast on a companion. The healing went to the ally but the AC bonus stayed on the player. Fixed in both paths:
- **Single-target heal on ally**: Now applies both healing AND protection to the target
- **Multi-target heal on party**: Protection now applies to ALL teammates, not just the caster

## Old God Damage Buff (Aurelion, Terravok, Manwe)

The last three Old Gods were being cheesed. Strength tripled: Aurelion 280→840, Terravok 350→1050, Manwe 400→1200. Phase scaling still applies on top (Phase 2: 150%, Phase 3: 200%), so Manwe Phase 3 hits with base 2,400 STR.

## AoE Ability Double-Damage Fix

AoE abilities (Crescendo, Whirlwind, Void Rupture, Chain Lightning, Corrosive Cloud, etc.) dealt damage twice — once from the generic ability handler (single-target hit) and again from the special effect handler (AoE loop). The generic handler now skips base damage for abilities whose special effects handle their own damage distribution.

## Slot-Filtered Inventory View (by Xykier)

When viewing an equipment slot in the inventory, the backpack now filters to show only items that fit that slot. Equip directly from the filtered view. Unequip items back to backpack from the slot view.

**Bug fixes applied to PR:**
- Weapon slot selection prompt was inverted (only prompted when slot was already known)
- Missing Spanish localization keys added
- Filtered inventory cache invalidated on equip/unequip/drop to prevent stale display

## Royal Bodyguard Equipment Exploit

Royal bodyguards (mercenaries) could be equipped with player inventory items through the dungeon party management screen `[Y] Party`. Bodyguards use baked-in stats and shouldn't accept equipment. Now blocked in both the auto-loot pickup path and the manual party management screen.

## Blood Moon Global Event

Blood Moon was a per-player event (each player had their own 30-day counter), so different players experienced it at different times. Now a global server-wide event: every 30th in-game day, ALL players experience Blood Moon simultaneously. Works in both online multiplayer and single-player.

## Dev Menu Removed

The secret developer menu (3,268 lines) has been removed. Admin functionality is available through the admin console. The `DEV`/`CHEATER`/`DEVMENU` commands now show a message pointing to the admin console.

## Veloura Save Dialogue Fix

Veloura's save path dialogue still told the player to "Find the Soulweaver's Loom" on floor 65 despite the v0.53.5 lore change where Veloura already has the Loom and gives it directly. Dialogue rewritten: Veloura now explains she found the Loom in ancient ruins and tried to use it on herself, then presses it into your hands during the save scene. No more floor 65 fetch quest.

## God Save Quest Flow Fix (Critical)

Sparing a god through dialogue set their state to `Awakened` (waiting for the player to find an artifact and return), but the artifact discovery events were removed in v0.53.5 — leaving gods permanently stuck in `Awakened` limbo. The spare dialogue path now routes through `HandleBossSaved` which properly sets the god state to `Saved`, grants the artifact directly, and awards XP/chivalry. Affects Veloura (Soulweaver's Loom) and Aurelion (Sunforged Blade).

Also removed: post-spare "promised to find the Loom" text, "QUEST STARTED: Find the Soulweaver's Loom" message, and "vowed to find a cure" news text. Em-dash encoding fixed for BBS terminal compatibility.

## Dead Spouse Relationship Record Cleanup

`GetSpouseName` in RelationshipSystem skipped dead spouses but left the marriage record at `RelationMarried` status. Now resets both sides to `RelationNormal` when a dead spouse is detected, preventing ghost marriage records from accumulating.

## Royal Marriage Annulment Reason

`ClearRoyalMarriage` used `HandleSpouseDeath` (recording "Died in combat") to clear the RomanceTracker record when the throne changed. Now uses `Divorce` with reason "Royal marriage annulled (throne change)" for accurate romance history.

## Relationship System Audit (17 Bugs Fixed)

Comprehensive 5-pass audit of the entire relationship system across RelationshipSystem, RomanceTracker, NPCMarriageRegistry, and Character flags:

**Pass 1 — Data Model Consistency (5 bugs):**
- Church marriage (`PerformMarriage`) missing NPCMarriageRegistry + RomanceTracker registration
- Gossip/flirt authority desync (gossip used NPCMarriageRegistry, flirt used Character flags)
- `SpouseName` inconsistently used `Name` vs `Name2`/`DisplayName` across paths
- Stale NPC marriage flags not synced with registry on world sim startup

**Pass 2 — Romance Flow (3 bugs):**
- `PerformWeddingCeremony` missing NPCMarriageRegistry registration
- Wedding ceremony didn't set NPC's marriage flags (only player's)
- Daily relationship cap (2/day) blocked confession (+8) and marriage (+10) milestone events — `overrideMaxFeeling` now bypasses daily cap

**Pass 3 — Marriage/Divorce/Death Lifecycle (4 bugs):**
- `ProcessDivorce` missing NPCMarriageRegistry + RomanceTracker cleanup
- `HandleSpouseBereavement` missing RomanceTracker.HandleSpouseDeath call
- Affair divorce `SpouseName` used `player.Name` instead of `DisplayName`
- Affair remarriage missing NPCMarriageRegistry registration

**Pass 4 — Cross-System Integration (2 bugs):**
- Castle royal marriage used `Name` instead of `DisplayName` for SpouseName and registry
- `ClearRoyalMarriage` called `EndMarriage` with king name instead of NPC ID

**Pass 5 — Edge Cases (1 bug):**
- No self-marriage guard in `PerformMarriage`

## NPC Marriage Flag Gossip Desync Fix

NPCs listed as "Single" in gossip rejected flirts with "I'm married." The gossip screen checked `NPCMarriageRegistry` (authoritative) while the flirt system checked `npc.Married`/`npc.IsMarried` (Character flags, often stale). Flirt now uses NPCMarriageRegistry as the authority, matching gossip. World sim startup also clears stale marriage flags on NPCs not in the registry.

## Shop Item Thematic Bonuses

Shop-generated items had no name-based stat bonuses — only raw ArmorClass/WeaponPower. Dungeon loot got CHA from "Orator's Gloves" but the same item from a shop had zero stats. Added `ApplyNameThematicBonuses` to ShopItemGenerator, applied to weapons, armor, and accessories. Uses the same keyword matching as dungeon loot (holy→WIS, shadow→DEX, orator→CHA, etc.).

## Floor 5 Guardian Difficulty Reduction

The Dungeon Guardian (floor 5 mini-boss encounter for new players) was too difficult for support classes like Wavecaller who rely on spells/abilities rather than basic attacks. Changed from mini-boss to regular monster scaling. Still a narrative encounter but no longer a brick wall for non-melee classes.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.53.7
- `Scripts/Locations/CastleLocation.cs` — Execute releases prisoner from prison (DB sync)
- `Scripts/Core/GameEngine.cs` — Login marriage cleanup; Xykier added to credits
- `Scripts/Systems/WorldSimulator.cs` — HandleSpouseBereavement: notify player spouse on NPC permadeath
- `Scripts/Systems/CombatEngine.cs` — Heal spell protection on allies; AoE ability double-damage fix; mercenary loot pickup skip
- `Scripts/Data/OldGodsData.cs` — Aurelion STR 280→840, Terravok 350→1050, Manwe 400→1200
- `Scripts/Systems/InventorySystem.cs` — Slot-filtered inventory (by Xykier); slot selection fix; cache invalidation
- `Scripts/Systems/DailySystemManager.cs` — Blood Moon global event (uses currentDay % 30)
- `Scripts/Locations/DungeonLocation.cs` — Mercenary equipment management blocked
- `Scripts/Locations/MainStreetLocation.cs` — Dev menu removed; commands show admin console message
- `Scripts/Locations/DevMenuLocation.cs` — **DELETED** (3,268 lines)
- `Scripts/Systems/ShopItemGenerator.cs` — `ApplyNameThematicBonuses` for name-based stat bonuses on shop items
- `Scripts/Locations/DungeonLocation.cs` — Floor 5 Guardian changed from mini-boss to regular monster; mercenary equipment block
- `Scripts/Systems/RelationshipSystem.cs` — `PerformMarriage` registers in NPCMarriageRegistry + RomanceTracker; uses DisplayName; self-marriage guard; `ProcessDivorce` cleans up NPCMarriageRegistry + RomanceTracker; daily cap bypassed for milestone events
- `Scripts/Systems/VisualNovelDialogueSystem.cs` — `HandleFlirtOption` uses NPCMarriageRegistry as authority; `PerformWeddingCeremony` sets NPC flags + registers in NPCMarriageRegistry
- `Scripts/Systems/WorldSimulator.cs` — `HandleSpouseBereavement` calls RomanceTracker.HandleSpouseDeath; stale marriage flag cleanup on startup
- `Scripts/Systems/DialogueSystem.cs` — Veloura save path dialogue rewritten (gives Loom directly); Aurelion Alethia dialogue
- `Scripts/AI/EnhancedNPCBehaviors.cs` — Affair remarriage uses DisplayName + registers in NPCMarriageRegistry
- `Scripts/Locations/CastleLocation.cs` — Royal marriage uses DisplayName; `ClearRoyalMarriage` uses NPC ID for EndMarriage + Divorce reason fix
- `Scripts/Systems/OldGodBossSystem.cs` — Spared gods route through `HandleBossSaved` instead of setting `Awakened` state
- `Scripts/Locations/DungeonLocation.cs` — Removed "promised to find Loom" and "QUEST STARTED" text from spared outcome; updated news text
- `Localization/en.json` — Xykier credit key; inventory slot keys
- `Localization/es.json` — Missing inventory slot keys added; Xykier credit
- `Localization/fr.json` — Xykier credit
- `Localization/hu.json` — Xykier credit
- `Localization/it.json` — Xykier credit
