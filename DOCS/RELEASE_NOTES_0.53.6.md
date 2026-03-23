# Usurper Reborn v0.53.6 Release Notes

**Version Name:** Ancestral Spirits

## Rebellion System

Kings who abuse their execution power now face consequences. Executing NPCs triggers escalating warnings and ultimately a full rebellion:

- **3 executions**: The townspeople watch in fearful silence
- **4 executions**: Whispers of rebellion spread through the streets
- **5 executions**: The people rise up and overthrow the king

The rebellion is a multi-act cinematic sequence: the mob storms the castle, guards lay down their weapons, the king is dragged to the dungeon, tried in the town square by a people's court, and their fate is decided by a coin flip.

**Heads**: The executioner takes the king's life. Character is permanently deleted from the database.

**Tails (Walk of Shame)**: Stripped of all equipment, inventory, gold (on hand + bank), paraded barefoot through the streets while the crowd throws rotten vegetables and shouts "SHAME!" Three times. Banished from the castle with zero fame, -5,000 chivalry, +500 darkness.

Every execution is broadcast server-wide. Rebellion outcomes are broadcast server-wide. Character deletion suppresses the emergency disconnect save to prevent data restoration.

## Execution Broadcast

Every NPC execution by the king is now broadcast server-wide in red, not just logged to the news feed.

## King/Queen Title Fix

Players on the throne now properly receive the King/Queen title. Title is cleared on abdication, overthrow, or rebellion.

## Prison Null Guard

Accessing prison management when the throne is vacant no longer crashes (NullReferenceException on `currentKing.Prisoners`).

## Double Imprisonment Prevention

NPCs can no longer be imprisoned twice. The imprison command now checks the prisoner list before allowing a new imprisonment.

## Establishment Toggle Fix

Closing a shop as king displayed "opened" instead of "closed" in 4 languages. The `castle.closed_past` localization key was incorrectly set to the same value as `castle.opened`.

## Shaman Totem PvP Fix

Shaman totems (Healing, Earthbind, Searing, Windfury, Spirit Link, Ancestral Guidance) could be summoned in PvP combat but never ticked per round. The PvP combat loop (`PlayerVsPlayer`) was missing the `ProcessShamanTotemEffects` call. Totems now process for both combatants each round.

## Dungeon Guide: Safe Haven Navigation

The dungeon guide (`[G]`) now includes a `[H] Nearest safe haven` option, letting players navigate to the nearest explored safe room for rest and healing.

## Rebellion King World State Fix

The rebellion heads path (character deletion) wasn't clearing the king from the world state before disconnecting. The castle continued to display the deleted player as king. Now calls `PersistRoyalCourtToWorldState()` before the disconnect exception, properly clearing the throne.

## King System Audit (17 Bugs Fixed)

Comprehensive audit and fix pass on the King/Castle system:

- **Throne vacancy persist** (CRITICAL): When the throne was vacated (rebellion, abdication), `SaveRoyalCourtToWorldState()` returned early on null king, leaving the old king in the world state. Other players still saw the dead/overthrown king ruling. Now saves empty throne state explicitly.
- **Throne challenge null guard**: Challenging a vacant throne could crash with NullReferenceException. Added null/IsActive check at top of `ChallengeThrone()`.
- **ThroneChallengedToday not serialized**: Players could relog to bypass the daily throne challenge limit. Now persisted to save data alongside `TotalExecutions`.
- **NPC coronation gold duplication**: When an NPC was crowned, half their gold became treasury but was never deducted from the NPC. Fixed — NPC gold now reduced.
- **Prisoners lost on succession**: When a new king took the throne, the old king's prisoner records were discarded. Prisoners now inherited across all 4 succession paths (3 player + NPC coronation).
- **CrownNPC used raw constructor**: NPC succession created a bare `King` object, losing orphans, prisoners, and validation. Now uses `CreateNewKing()` with full inheritance.
- **King name validation**: `CreateNewKing()` now rejects null/empty names (falls back to "Unknown Ruler").
- **Dead NPCs in imprison list**: Added `IsAlive`, `IsPermaDead` checks and null-safe king name to the imprison target filter.
- **Sentence expiry NPC release**: When prisoners served their time in `King.ProcessDailyKingship()`, the King's prisoner record was removed but the NPC's `DaysInPrison` was never cleared. NPCs stayed stuck in "Prison" location. Now resets `DaysInPrison` and location on release.

## Prison Menu Overhaul

Prison cell management menu rewritten with standard formatting:
- Visual mode: `[X] Label` bracket format with color-coded keys
- BBS/compact mode: inline bracket format
- Screen reader mode: numbered options via `WriteSRMenuOption`
- Prisoner list: SR mode shows comma-separated readable format instead of column table
- Uses `GetInput()` instead of raw `ReadLineAsync()` for consistent prompt handling

## Prison System Overhaul (4 Audit Passes)

Comprehensive 4-pass audit and fix of the entire prison system:

**Pass 1**: Serialization of daily counters (`ExecutionsToday`, `PlayerImprisonedToday`, `NPCsImprisonedToday`, `TotalExecutions`, `ThroneChallengedToday`), rebellion prisoner release, menu overhaul with SR/BBS support, sentence expiry NPC release sync.

**Pass 2**: Eliminated duplicate prison system — `GameEngine.HandlePrison()` (broken escape formula, no sentence completion check) deleted. Login now routes to `PrisonLocation` which has proper 30-80% clamped escape, sentence tracking, Vex encounter, activities. `PrisonActivitySystem` now syncs `King.Prisoners` on NPC release.

**Pass 3**: `GameExitException` from prison quit now caught in `LocationManager`. Dungeon entry blocked while imprisoned. 70 lines of dead code removed.

**Pass 4**: DaysInPrison byte overflow guard (255 cap on escape fail increment). New **bail payment system** — `[B] Pay Bail` deducts gold, adds to king's treasury, releases immediately. New **petition system** — `[P] Petition the King`: against NPC king, auto-sets bail or 5-40% clemency chance based on chivalry; against human king, sends a message to their inbox.

## Immediate Player Arrest

When the king imprisons an online player, royal guards now seize them immediately instead of waiting for their next login. The arrested player sees a dramatic arrest message and is redirected to prison on their next action. Their `DaysInPrison` is set directly on their in-memory character.

Player sentences capped at 1 day max (NPCs can get up to 30). Player execution gold penalty changed from flat 1,000g to actual 10% via new `DeductGoldByPercentage` SQL method.

## Player Imprison/Execute SQL Fix

`ImprisonPlayer`, `DeductGoldFromPlayer`, and `DeductGoldByPercentage` SQL methods now match on `username OR display_name`. Previously they only matched on `username`, so players whose display name differed from their account name (e.g., prestige renames) were silently unaffected by imprisonment or gold penalties.

## Royal Orders Overhaul

Royal Orders menu rewritten with standard `[X] Label` format, BBS compact mode, and screen reader support.

**Establishments:**
- Establishment names localized via `castle.est_*` keys (8 new keys)
- Open/close actions broadcast server-wide (green for open, red for close)
- Closing 2+ establishments triggers citizen grumbling warning
- Closing 75% (6 of 8) triggers the same rebellion as mass executions (coin flip)
- Toggle persists to world_state in online mode

**Proclamations:**
- Broadcast server-wide in online mode
- ANSI escape code injection stripped from input
- Length capped at 200 characters
- Persists to world_state

**Bounties:**
- King cannot place bounty on themselves
- Broadcast server-wide in online mode
- Persists to world_state

## Prison Activity Exploit Fix

Prison activities (Pushups, Yoga, Reading, Meditation, Shadow Boxing, Stretching, Planning, Praying) had no daily limit — prisoners could spam them for unlimited permanent stat gains. Fixed:
- **3 activities per day** max (resets at daily maintenance)
- Stat gains reduced to +1 per activity (was +1-3)
- Shadow Boxing no longer gives permanent ATK+DEF
- Stretching no longer gives permanent MaxHP
- Reading no longer gives permanent MaxMana

## Prison Demand Release Message

Demanding release now sends a petition message to the king ("LET ME OUT!") so the king actually receives it and can respond.

## Bail Set Notification

When the king sets bail on a prisoner, the prisoner now receives both a system message AND an immediate in-game notification if online. Bail amount persists to world_state.

## Message System Display Name Fix

`GetUnreadMessages` SQL now matches on both `to_player = username` AND `to_player = display_name`, fixing petition and bail messages not being received when the king's display name differs from their account username.

## Alethia Lore: Aurelion's Lost Beloved

Alethia, Aurelion's wife, has been woven into the game's narrative. She was murdered by Noctura — Aurelion's own sister — out of forbidden love and jealousy. The truth is revealed through a layered breadcrumb trail:

**Spell Renames:** Tidesworn's "Tidal Ward" → "Alethia's Ward" and Wavecaller's "Restorative Tide" → "Alethia's Grace" — both with lore-themed descriptions invoking Aurelion's lost beloved.

**Lore Fragments (6 new):**
- Noctura fragments hint at shadow, night-blooming flowers, and "something a sister should not feel" without naming the murder directly
- Aurelion fragments reveal "Alethia was his light, his grace" and "She was murdered. And the Light has never recovered."
- Players can infer Noctura killed Alethia from the clues before the confirmation

**Ghost Encounters (floors 60-85, 25% at rest spots):** Alethia's luminous spirit appears with 4 possible dialogue sets — she speaks of her love for Aurelion, hints at Noctura's jealousy ("She loved him too. Not as a sister should."), and her own murder ("She came wearing shadows and smiling. I did not fear her. She was family."). Restores 1/3 HP and mana.

**Dream: "The Grace of Light" (levels 35-70):** A dream showing Aurelion and Alethia in a garden, a shadow watching from the treeline burning with forbidden love, and the night the garden filled with night-blooming flowers. "He never stops calling."

**Aurelion Dialogue (new "Who was Alethia?" choice):** Full revelation — Noctura loved Aurelion, he chose Alethia instead, Noctura murdered her. "My light has been going out ever since. Not because Manwe broke me. Because she was my light. And she is gone."

**Story So Far updated:** Sundering text now reads: "The shadow goddess consumed her brother's beloved in darkness. The god of light, broken by grief, faded to barely a whisper."

## Level Cap 100

Player level is now capped at 100. Auto-level-up and Level Master both enforce the cap. NPC teammate catch-up XP also respects the cap.

## Crescendo Party Buff

Wavecaller's Crescendo ability buffed: base damage 120→180, per-ally bonus 30→50, cooldown 6→5 rounds. New: inspires the entire party with +25 ATK, +25 DEF for 3 rounds on cast. Both single-monster and multi-monster handlers updated.

## Void Rupture AoE Diminishing

Void Rupture (Voidreaver AoE ability) was dealing full damage to all targets with no diminishing returns. Now uses the standard AoE diminishing multiplier (100%/75%/50%/25%). Explosion damage on kills now scales at 15% of hit damage (minimum 60) instead of a flat 60.

## Arena Immortal Filter

Players who ascended to immortality are now filtered from the PvP arena opponent list. Also filtered from the website PvP leaderboard. Uses `isImmortal` flag in player data.

## Arena Username Fix

Arena opponent save data was loaded using `DisplayName` instead of `Username`, causing "load failed" for players whose display name differs from their account name (e.g., prestige renames). `PlayerSummary` now includes a `Username` field used for all backend lookups.

## Throne Challenge Spam Cooldown

NPC throne challenges spammed dethroned players with repeated warning and defeat messages because the challenge system didn't detect king changes. Added: king change detection clears pending challenges, 10-minute cooldown after any dethronement, pending challenges cleared when throne is vacated.

## Companion Execute AI Fix

Companion AI used Execute on full-HP targets because it checked for low-HP monsters at ability selection time, but by execution time those monsters had died from earlier attacks in the round. Now re-checks living monsters before committing to Execute and cancels if no valid low-HP target exists.

## Companion Weapon Comparison Fix

Teammate weapon loot comparison used different scoring for new items (`lootItem.Attack`) vs equipped items (`currentEquip.WeaponPower`) with no stat bonuses on either side. A 138 WP weapon with good stats could replace a 164 WP weapon and be called an "upgrade." Now uses weighted scoring: WeaponPower * 3 + all stat bonuses for both sides. Dual-wield slot selection also uses the same weighted formula.

## Blood Frenzy Description Clarification

Blood Frenzy description updated from "Attack twice per round" to "Basic attacks hit twice per round. Does not affect abilities or spells." The mechanic was working correctly — Haste doubles basic attacks but not ability/spell actions — but the description was misleading.

## /g Gold Command Fix

`/g` was documented as a gold shortcut in the help menu but was intercepted by the MUD chat system as an alias for `/group` before the location could process it. Removed `/g` as a group alias — players use `/group` for grouping and `/g` now correctly shows gold status.

## Immigration Population Cap Raised

The immigration system stopped spawning diversity-based immigrants when population reached 80. After mass executions reduced the population to 60, recovery was extremely slow. Cap raised from 80 to 200, matching the target population size.

## Localization Sync

- 26 format argument mismatches fixed across es/hu/it/fr (prevented potential crashes)
- 92 missing `Loc.Get()` keys added to en.json (raw key names no longer shown to players)
- 133 missing keys synced to es/hu/it/fr
- ~650 untranslated strings translated across all 4 languages
- All 5 language files now have identical key sets (16,643 keys each)

---

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.53.6; MaxLevel 200→100
- `Scripts/Core/Character.cs` — `ExecutionsToday`, `TotalExecutions` properties
- `Scripts/Core/King.cs` — Name validation in `CreateNewKing`; sentence expiry clears NPC `DaysInPrison` and location
- `Scripts/Locations/CastleLocation.cs` — Rebellion system (coin flip, Walk of Shame, character deletion); execution broadcast; prison menu overhaul (visual/BBS/SR); prison null guard; double imprisonment check; dead NPC filter; establishment toggle fix; throne challenge null guard; prisoner inheritance on all 4 succession paths; `CrownNPC` uses `CreateNewKing`; NPC gold deduction on coronation
- `Scripts/Systems/CombatEngine.cs` — Shaman totem processing in PvP combat loop; Crescendo party buff (+25 ATK/DEF); Void Rupture AoE diminishing + scaled explosion; companion weapon weighted scoring; Execute AI re-check; Blood Frenzy description
- `Scripts/Systems/ClassAbilitySystem.cs` — Crescendo base 120→180, per-ally 30→50, cooldown 6→5; Blood Frenzy description clarified
- `Scripts/Systems/SpellSystem.cs` — Tidal Ward → Alethia's Ward; Restorative Tide → Alethia's Grace (spell renames + lore descriptions)
- `Scripts/Systems/DialogueSystem.cs` — New Aurelion "Who was Alethia?" dialogue node with full Noctura revelation
- `Scripts/Systems/DreamSystem.cs` — New "The Grace of Light" dream (levels 35-70, Alethia/Aurelion/Noctura)
- `Scripts/Systems/FeatureInteractionSystem.cs` — 4 new lore fragments (2 Noctura with Alethia hints, 2 Aurelion with Alethia references)
- `Scripts/Systems/ChallengeSystem.cs` — King change detection; 10-minute dethronement cooldown; pending challenge cleanup
- `Scripts/Systems/IOnlineSaveBackend.cs` — `Username` field added to `PlayerSummary`
- `Scripts/Systems/SqlSaveBackend.cs` — `GetAllPlayerSummaries` includes username, filters immortals; `GetUnreadMessages` matches username OR display_name
- `Scripts/Locations/ArenaLocation.cs` — Uses `Username` for `ReadGameData`, `HasAttackedPlayerToday`, same-account filter
- `Scripts/Locations/LevelMasterLocation.cs` — Level cap 100 enforcement
- `Scripts/Systems/OnlineStateManager.cs` — `SaveRoyalCourtToWorldState` saves empty state when king is null
- `Scripts/Systems/SaveDataStructures.cs` — `ThroneChallengedToday`, `TotalExecutions` fields
- `Scripts/Systems/SaveSystem.cs` — Serialize `ThroneChallengedToday`, `TotalExecutions`
- `Scripts/Core/GameEngine.cs` — Restore `ThroneChallengedToday`, `TotalExecutions` from save
- `Scripts/Systems/DailySystemManager.cs` — `ExecutionsToday` daily reset
- `Scripts/Systems/WorldSimulator.cs` — Immigration population cap 80→200
- `Scripts/Server/PlayerSession.cs` — `SuppressDisconnectSave` flag for rebellion character deletion
- `Scripts/Server/MudChatSystem.cs` — Removed `/g` as group alias (conflicts with `/gold`)
- `Scripts/Locations/DungeonLocation.cs` — Safe haven navigation in dungeon guide; Alethia ghost encounter (floors 60-85)
- `Tests/GameConfigTests.cs` — MaxLevel assertion updated for 100 cap
- `Localization/en.json` — 92 missing keys added; establishment fix; Story So Far Alethia text; Alethia spell descriptions
- `web/ssh-proxy.js` — PvP leaderboard immortal filter
- `Localization/es.json` — 133 keys synced; ~310 translated; format fixes; `castle.closed_past` fix
- `Localization/hu.json` — 133 keys synced; ~297 translated; 17 format fixes; `castle.closed_past` fix
- `Localization/it.json` — 133 keys synced; ~332 translated; format fixes; `castle.closed_past` fix
- `Localization/fr.json` — 133 keys synced; ~325 translated
