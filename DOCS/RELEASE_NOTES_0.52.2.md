# Usurper Reborn v0.52.2 - Secret Boss & Prestige Combat Fix

---

## Bug Fixes

- **Steam integration completely non-functional** — `SteamIntegration.Initialize()` was only called in the standard console mode path (no command-line args). Both Steam launchers (`Play.bat` via WezTerm and `Play-Accessible.bat`) pass `--local`, which routes through the BBS door mode code path and returns before Steam is ever initialized. This means every Steam build ever shipped had no working Steam integration — achievement unlocks, stat syncing, and dev menu Steam reset all silently failed. Moved `SteamIntegration.Initialize()` before the door mode branch so it runs for all launch modes. Added `SteamIntegration.Shutdown()` to the door mode cleanup path.

- **Secret boss encounters completely inaccessible** — The four secret bosses (The First Wave on floor 25, The Forgotten Eighth on floor 50, Echo of Self on floor 75, The Ocean Speaks on floor 99) were impossible to encounter. `PlaceSecretBoss()` in the dungeon generator set `EventType = DungeonEventType.SecretBoss` on the SecretVault room but never set `HasEvent = true`. Since the `[V] Investigate` menu option checks `room.HasEvent && !room.EventCompleted`, the option never appeared and players could never trigger the secret boss encounter. Players could clear the entire floor without ever seeing the secret boss. This bug has existed since secret bosses were first implemented.

- **All 5 prestige class abilities broken in single-monster combat** — Every ability for Tidesworn (11), Wavecaller (10), Cyclebreaker (10), Abysswarden (11), and Voidreaver (11) had their special effect cases implemented only in `ApplyAbilityEffectsMultiMonster` (multi-monster combat) but were completely missing from `ApplyAbilityEffects` (single-monster combat). This meant that in the most common combat scenario — fighting a single dungeon monster or boss — prestige class abilities would cost stamina but produce no special effect (no damage bonuses, no lifesteal, no debuffs, no status effects, no healing). Only the base damage from the ability was applied. All 44 missing cases have been added with appropriate single-target adaptations for AoE abilities.

- **Wavecaller damage reflection never worked** — Empathic Link and Harmonic Shield both set `StatusEffect.Reflecting` on the player, but no code anywhere in CombatEngine checked for this status to actually reflect damage. The status was applied and expired silently with zero effect. Added 15% damage reflection when Reflecting is active — damage is reflected back at the attacking monster after each hit.

- **Wavecaller missing CHA-based basic attacks** — Wavecaller is a CHA-scaling support class (like Bard) but was using STR for basic attack damage. Since Wavecallers invest in CHA for ability scaling, their basic attacks hit like wet noodles. Now uses CHA for basic attack damage like Bard and Jester.

- **Wavecaller Ocean's Voice +20% crit bonus never applied** — Ocean's Voice was described as a powerful ultimate buff but only granted ATK/DEF bonuses. The intended +20% critical hit chance was never implemented. Added crit bonus to basic attacks and abilities in both single and multi-monster combat paths.

---

## Balance Changes

- **Tidesworn: Ocean's Blessing passive** — New class passive grants +25% healing effectiveness on all abilities and spells, matching Cleric's Divine Grace. Displayed in `/health` under active buffs.

- **Tidesworn: Ocean's Resilience passive** — Tidesworn regenerates 2% max HP per combat round when below 50% HP. Provides sustained tankiness without requiring ability usage. Displayed in `/health`.

- **Tidesworn: Living Waters moved to level 15** — Swapped with Breakwater (now level 25). Tidesworn previously had no healing until level 25, making the early game feel weak for a tank/healer hybrid. Living Waters (100 HP heal + regen) is now available 10 levels earlier.

- **Tidesworn: Ocean's Embrace restores mana** — Ocean's Embrace now restores 25% max mana in addition to party healing and debuff cleansing. Makes the Tidesworn mana pool feel relevant instead of underutilized (abilities use stamina, spells use mana, but no way to recover mana mid-combat via abilities).

- **Wavecaller: Harmonic Resonance passive** — New class passive grants +25% healing effectiveness on all abilities and spells. Wavecaller is a support/healer hybrid — this makes healing abilities competitive with Cleric and Tidesworn. Displayed in `/health` under active buffs.

- **Wavecaller: Wave Echo damage buffed** — Base damage increased from 50 to 70. Wave Echo is the Wavecaller's bread-and-butter attack ability (level 5) and was hitting too weakly for an NG+ class. Damage still doubles against debuffed targets for 140 effective.

---

## Files Changed

- `Console/Bootstrap/Program.cs` — Moved `SteamIntegration.Initialize()` before the door mode branch so Steam works for `--local` (WezTerm/accessible launchers), BBS door, and standard console modes; added `SteamIntegration.Shutdown()` to door mode cleanup path
- `Scripts/Core/GameConfig.cs` — Version 0.52.2; `TideswornOceansBlessingBonus` (+25% healing), `TideswornOceansResiliencePercent` (2% HP regen), `WavecallerHarmonicResonanceBonus` (+25% healing), `WavecallerReflectionPercent` (15% reflection), `WavecallerOceansVoiceCritBonus` (+20% crit) constants
- `Scripts/Systems/DungeonGenerator.cs` — `PlaceSecretBoss()` now sets `HasEvent = true` on the SecretVault room alongside the EventType assignment, making the `[V] Investigate` option appear and the secret boss encounter accessible
- `Scripts/Systems/CombatEngine.cs` — Added 44 missing prestige ability cases to `ApplyAbilityEffects` (single-monster path): 11 Tidesworn, 10 Wavecaller, 10 Cyclebreaker, 11 Abysswarden, 11 Voidreaver; AoE abilities adapted to single-target; Ocean's Resilience passive in `ProcessEndOfRoundAbilityEffects()` (2% max HP regen below 50% HP); Ocean's Embrace mana restore (25% max mana) in both single and multi-monster paths; Wavecaller added to CHA-based basic attacks (alongside Bard/Jester); 15% damage reflection when `StatusEffect.Reflecting` active (after monster attacks player); Ocean's Voice +20% crit bonus in basic attacks (single and multi-monster) and ability crit rolls (both paths)
- `Scripts/Systems/ClassAbilitySystem.cs` — Tidesworn Ocean's Blessing healing passive (+25%) applied in healing formula; Wavecaller Harmonic Resonance healing passive (+25%) applied in healing formula; Living Waters moved from level 25 to level 15; Breakwater moved from level 15 to level 25; Ocean's Embrace description updated to mention mana restore; Wave Echo base damage 50→70
- `Scripts/Locations/BaseLocation.cs` — Tidesworn added to class passive display in `/health` showing Ocean's Blessing and Ocean's Resilience; Wavecaller added showing Harmonic Resonance and Damage Reflection
