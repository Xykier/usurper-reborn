# Usurper Reborn v0.52.14 Release Notes

**Release Date:** March 18, 2026
**Version Name:** The Hook

## Shield Stat Doubling Fix

Shields had their AC and Block stats doubled on every equip/unequip cycle. A shield with AC:30 Block:15 would become AC:60 Block:30 after one cycle, AC:120 Block:60 after two, escalating to absurd values like AC:7424 Block:7424. The root cause was a round-trip corruption in the Item-to-Equipment conversion pipeline:

1. **Unequip** (`ConvertEquipmentToItem`): stored `Armor = ArmorClass + ShieldBonus`, merging two separate values into one field.
2. **Re-equip** (`ConvertInventoryItemToEquipment`): set BOTH `ArmorClass = Armor` AND `ShieldBonus = Armor`, doubling the combined value.
3. **Each cycle doubled**: 30 → 60 → 120 → 240 → ... → 7424.

Fixed in 3 locations:
- `Character.ConvertEquipmentToItem` — shields now store `ShieldBonus` and `BlockChance` in their dedicated `Item` fields instead of merging into `Armor`
- `BaseLocation.ConvertInventoryItemToEquipment` — shields now read from `Item.ShieldBonus` and `Item.BlockChance` instead of duplicating `Item.Armor`
- `CombatEngine.ConvertLootItemToEquipment` — shields now use `Equipment.CreateShield()` instead of `Equipment.CreateArmor()`, preserving shield-specific stats


## Companion Loot Displaced Item Loss Fix

When a companion auto-equipped a loot drop in the dungeon (the "X picks up Y — a Z% upgrade!" message), the item they were previously wearing disappeared entirely. The displaced equipment was added to the companion's Character wrapper inventory, which is ephemeral and never transferred anywhere. Now, displaced items are moved to the player's inventory with a message: "(Their old X was added to your inventory)". Fixed in both loot distribution paths (single-monster and multi-monster).

## MUD Client Input Fix

Reverted the input delivery change from v0.52.13 that caused two issues for MUD client users (Mudlet, TinTin++):
- **Game freeze after loot equip**: The strict `buffer.Length == 0` check prevented message delivery if any stale keystroke was in the input buffer, so the next prompt never appeared.
- **Random NPC encounters bleeding through**: Queued messages (NPC interactions, world events) piled up silently and flushed all at once when the buffer finally cleared, dumping players into unexpected interactions.

Restored the original 500ms grace period: messages are delivered when the player hasn't typed for 500ms, even if text is in the buffer. This prevents mid-typing disruption while ensuring prompts and events reach the player.

## Main Street Menu Alignment

The Main Street menu columns now align vertically across all rows. Previously, each row computed its own column width based on item count (`79 / items`), so rows with 4 items used 19-char columns while rows with 5 items used 15-char columns. Now all rows use a fixed 16-char column width, keeping the `[X]` brackets lined up.

## Monk Potion Vendor Labels

The wandering monk potion vendor menu was missing `[H]` and `[M]` labels on the healing and mana potion options. Players saw the options but didn't know what to type, getting "Please choose a valid option" errors. Added `[H]` and `[M]` prefixes in all 5 languages.

## NPC Population Control

The NPC population had grown to 185 alive (target: ~60). Two changes to bring it back down naturally:
- **Pregnancy hard cap**: No new pregnancies when alive NPCs >= 120. Added steeper tiers: 120-160 total pop = 0.05% chance, 160+ = 0.025%.
- **Immigration gating**: Diversity immigration (Phase 2) disabled when alive NPCs >= 80. Extinction prevention (Phase 1, races below 2) still active at any population.

## Stale Team XP Slot Cleanup

When a teammate left or died, their XP distribution slot retained its old percentage value (e.g., `[100, 50, 0, 0, 0]` = 150% total). Now, all teammate XP slots are zeroed out when fighting solo, in all 3 combat victory paths. Self-heals on the next solo combat.

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.52.14
- `Scripts/Core/Character.cs` — `ConvertEquipmentToItem()` shield stats stored in dedicated `ShieldBonus`/`BlockChance` fields instead of merged `Armor`
- `Scripts/Systems/CombatEngine.cs` — `ConvertLootItemToEquipment()` uses `Equipment.CreateShield()` for shields; companion loot auto-equip now transfers displaced items to player inventory (both loot paths); stale teammate XP slot cleanup in all 3 victory paths
- `Scripts/Locations/BaseLocation.cs` — `ConvertInventoryItemToEquipment()` reads `Item.ShieldBonus`/`Item.BlockChance` for shields instead of duplicating `Item.Armor`
- `Scripts/Locations/MainStreetLocation.cs` — Fixed column width (`const int C = 16`) replaces dynamic `RC(items)` in `ShowClassicMenu()`
- `Scripts/UI/TerminalEmulator.cs` — Reverted input delivery to 500ms grace period (from strict `buffer.Length == 0`)
- `Scripts/Systems/WorldSimulator.cs` — Pregnancy hard cap at 120 alive NPCs; steeper fertility tiers; immigration Phase 2 gated at 80 alive NPCs
- `Localization/en.json` — `[H]`/`[M]` prefixes on monk vendor keys
- `Localization/es.json` — `[H]`/`[M]` prefixes on monk vendor keys
- `Localization/hu.json` — `[H]`/`[M]` prefixes on monk vendor keys
- `Localization/it.json` — `[H]`/`[M]` prefixes on monk vendor keys
- `Localization/fr.json` — `[H]`/`[M]` prefixes on monk vendor keys
