# Usurper Reborn v0.50.6 — Gear Inspector & TLS Fixes

New `/gear` command for detailed equipment inspection, TLS 1.2 fixes for Windows 7 compatibility across all HTTP systems.

## New Features

### /gear Command — Detailed Equipment Inspector
New `/gear` (alias `/eq`, `/equipment`) slash command shows a comprehensive breakdown of every equipped item with all stats and special properties. Unlike the compact equipment summary in `/status`, this shows:

- **Full stat bonuses per item** — Every stat bonus (Str, Dex, Con, Int, Wis, Cha, Agi, HP, MP, Sta, Def) listed individually for each slot
- **Special properties** — Enchantments (Fire, Frost, Lightning, Poison, Holy, Shadow), proc effects (Lifesteal, Manasteal, Armor Piercing, Thorns, HP/MP Regen), crit bonuses, magic resistance, poison damage, and curse status
- **Equipment totals** — Total Weapon Power, Armor Class, and all stat bonuses summed across all equipped items with item count
- **Rarity-colored item names** — Common (white), Uncommon (green), Rare (blue), Epic (magenta), Legendary (yellow), Artifact (red)

Accessible from any location via `/gear` or `/eq`.

### Team Member Gear Inspection
When you have NPC teammates, companions, or a spouse, `/gear` now prompts you to choose whose equipment to inspect. Shows a numbered selection list of all available team members. If you're alone, it shows your own gear directly without prompting.

## Bug Fixes

### Windows 7 TLS Failures on Auto-Updater and Telemetry
The version *check* (v0.49.7) had TLS 1.2 configured with an HTTP fallback proxy for Win7 compatibility, but two other HTTP systems were missing the same treatment:

- **Auto-update download** — Used a bare `HttpClient()` to download from GitHub's HTTPS URLs. On Win7 where SChannel defaults to TLS 1.0, this silently failed. Now uses `HttpClientHandler` with `SslProtocols.Tls12 | Tls13`.
- **Telemetry (PostHog)** — Static `HttpClient` hitting `https://us.i.posthog.com` with no TLS configuration. Now uses `CreateTlsClient()` helper with TLS 1.2+ and graceful fallback.
- **Bug reports (Discord webhook)** — Same issue with HTTPS Discord endpoint. Fixed with same pattern.

## Files Changed
- `Scripts/Core/GameConfig.cs` — Version 0.50.6
- `Scripts/Locations/BaseLocation.cs` — New `ShowDetailedGear()` method; `ShowGearWithTeamSelection()` for team member inspection; `/gear`, `/eq`, `/equipment` command routing; help text entry
- `Scripts/Systems/VersionChecker.cs` — TLS 1.2 `HttpClientHandler` on auto-update download path
- `Scripts/Systems/TelemetrySystem.cs` — `CreateTlsClient()` helper; static HttpClient uses TLS 1.2+
- `Scripts/Systems/BugReportSystem.cs` — `CreateTlsClient()` helper; static HttpClient uses TLS 1.2+
