# Usurper Reborn v0.50.0 — Open Doors (Accessibility Update)

Screen reader accessibility pass for multiple locations. Locations now detect `IsScreenReader` mode and render clean, plain-text layouts free of box-drawing characters, decorative line separators, and dense grid menus. New `DisplayLocationSR()` methods provide structured text with labeled menu options via `WriteSRMenuOption()` for easy screen reader navigation. Shared UI helpers (`WriteBoxHeader`, `WriteSectionHeader`, `WriteDivider`) used throughout automatically switch between visual box-drawing and plain headings based on screen reader state, so non-SR users see the same presentation as before.

### Anchor Road
- New `DisplayLocationSR()` screen reader layout with categorized menu sections (Challenges, Town Control, Other)
- Box-drawing separators in challenge status, bounty board, rival teams list, gauntlet details, and prisoner list suppressed in screen reader mode
- All inline box headers (`BOUNTY HUNTING`, `GANG WAR`, `THE GAUNTLET`, `CLAIM TOWN`, `PRISON GROUNDS`) converted from manual `╔═╗` patterns to `WriteBoxHeader()`
- All section headers (`BOUNTY COLLECTED!`, `GANG WAR VICTORY/DEFEAT!`, `WAVE X/Y`, `GAUNTLET CHAMPION!`, `GAUNTLET SUMMARY`, `FIGHT X/Y`) converted to `WriteSectionHeader()`
- Prison Grounds menu uses `WriteSRMenuOption()` in screen reader mode

### Church
- New `DisplayLocationSR()` screen reader layout with alignment display, bishop greeting, and labeled service menu
- Church header converted from manual box-drawing to `WriteBoxHeader()`
- Section headers (`CHURCH HEALING SERVICES`, `CONFESSION`, `CHURCH RECORDS`, `AUDIENCE WITH THE BISHOP`) converted to `WriteSectionHeader()`
- Box-drawing separators in church services menu, marriage relationship table, and church records suppressed in screen reader mode
- Static `CheckChurchEntryRestrictions()` uses `player.ScreenReaderMode` to skip box-drawing in entry-denied banner

### Weapon Shop
- New `DisplayLocationSR()` screen reader layout with current weapons summary and categorized menu
- Shop header converted from manual box-drawing to `WriteBoxHeader()`
- Main menu options (categories, sell, auto-buy, return) converted to `WriteSRMenuOption()`
- Category section headers converted to `WriteSectionHeader()`
- Column header dividers in item lists converted to `WriteDivider()`
- Sell and auto-buy section headers converted to `WriteSectionHeader()`
- Auto-buy offer divider converted to `WriteDivider()`

### Armor Shop
- New `DisplayLocationSR()` screen reader layout with total AC, per-slot equipment summary, and categorized menu
- Shop header converted from manual box-drawing to `WriteBoxHeader()`
- Main menu slot list renders via `WriteSRMenuOption()` in screen reader mode (slot name + current item + AC)
- Sell, auto-buy, and return options converted to `WriteSRMenuOption()`
- Slot item section headers converted to `WriteSectionHeader()`
- Column header divider in item lists converted to `WriteDivider()`
- Sell and auto-buy section headers converted to `WriteSectionHeader()`
- Auto-buy per-slot dividers converted to `WriteSectionHeader()`

### Magic Shop
- New `DisplayLocationSR()` screen reader layout with categorized menu sections (Shopping, Enchanting, Potions & Scrolls, Arcane Arts)
- Shop header converted from manual `╔═╗` pattern to `WriteBoxHeader()`
- 6 additional inline box headers converted to `WriteBoxHeader()`: Enchantment Forge, Enchanting item, NPC target picker, Arcane Romance, Dark Arts, death spell confirmation
- 11 section headers converted to `WriteSectionHeader()`: Unidentified Items, Item Properties, Curse Removal Service, Enchantment & Blessing Services, Enchantment Removal, accessory category, Sell Accessories, Mana Potions, Scrying Service, scrying target, shopkeeper conversation
- 3 enchantment sub-section headers (Stat Enchantments, Special Enchantments, Mythic & Elemental) converted to `WriteSectionHeader()`
- 8 column header dividers converted to `WriteDivider()`: Enchantment Forge equipment list, 3 enchantment tier sections, accessory category items, NPC target picker, Love Spells, Dark Arts

### Music Shop
- New `DisplayLocationSR()` screen reader layout with services menu (instruments, performance, Melodia, lore songs)
- Shop header converted from manual `╔═╗` pattern to `WriteBoxHeader()`
- 4 section headers converted to `WriteSectionHeader()`: Instruments, Hire a Performance, Lore Songs of the Old Gods, lore song title
- Instrument column header divider converted to `WriteDivider()`
- `WriteSRMenuOption()` used for all menu items with Bard-only instrument availability

### Main Street
- Main Street header converted from manual `╔═╗` pattern to `WriteBoxHeader()`
- Level eligibility box wrapped in `!IsScreenReader` guard (plain text summary in SR mode)
- Player Statistics screen: header converted to `WriteBoxHeader()`; 6 stat category headers (COMBAT, ECONOMY, EXPERIENCE, EXPLORATION, SURVIVAL, TIME) converted to `WriteSectionHeader()`
- Achievements screen: header and pagination headers converted to `WriteBoxHeader()`; `║`-bordered summary rows stripped to plain indented text in SR mode; category menu plain text branch for SR
- Hall of Fame screen: header converted to `WriteBoxHeader()`; subtitle and bottom border wrapped in `!IsScreenReader` guards; column divider converted to `WriteDivider()`
- Citizens screen: header converted to `WriteBoxHeader()`; 3 section headers (PLAYERS, ADVENTURERS, FALLEN) converted to `WriteSectionHeader()`; bottom border wrapped in `!IsScreenReader` guard
- Journey/Story Progress screen: complete `║`-bordered content rewrite — header converted to `WriteBoxHeader("YOUR JOURNEY")`; 4 section headers (SEVEN SEALS, OLD GODS, OCEAN PHILOSOPHY, ALIGNMENT) converted to `WriteSectionHeader()`; all `║  content  ║` rows converted to plain indented text
- Session Summary screen: header converted to `WriteBoxHeader()`; 2 internal dividers converted to `WriteDivider()`
- Settings, Preferences, Help, Attack screens: headers converted to `WriteBoxHeader()`
- 7 preference sub-screens (Combat Speed, Daily Cycle Modes, Auto-Save Settings, Load Different Save, Delete Save Files, Save File Information, Force Daily Reset) converted to `WriteSectionHeader()`

### Dungeon
- 12 box headers converted to `WriteBoxHeader()`: Boss Encounter, Floor Conquered, Traveling Merchant, Arena Portal, Lost Explorer, Abandoned Camp, Mysterious Fountain, Safe Haven, Wounded Adventurer, Trapped Chest, Dungeon Guide, Ancient Shrine

### Dark Alley
- New `DisplayLocationSR()` screen reader layout with categorized menu sections (Shady Establishments, Underground Services with locked note, Faction/Tribute, Evil Deeds)
- 16 box headers converted to `WriteBoxHeader()`: The Dark Alley, The Drug Palace, The Shadows, The Initiation, You Have Joined The Shadows, The Black Market, The Informant, The Gambling Den, Pickpocketing, The Pit, The Loan Shark, Fence Stolen Goods, The Safe House, Pay Tribute, The Enforcer Has Found You, Evil Deeds
- ACCESS DENIED entry restriction banner wrapped in `IsScreenReader` guard with plain text alternative
- 6 section headers converted to `WriteSectionHeader()`: Benefits of The Shadows, Requirements Not Met, Requirements Met, Wealthiest Marks, Wanted (Darkness > 200), Your Active Targets, Outstanding Loan
- Drug Palace menu (10 numbered drugs + leave) uses `WriteSRMenuOption()`
- Gambling Den menu (3 games + leave), Loaded Dice (over/under), Three Card Monte (3 cards), Skull & Bones (3 risk levels) all use `WriteSRMenuOption()`
- The Pit menu (fight monster/NPC/leave), NPC target selection (numbered + back out), Spectator Bet (4 multiplier options) use `WriteSRMenuOption()`
- Black Market menu (3 items + leave), Loan Shark repayment/new loan menus use `WriteSRMenuOption()`
- Pickpocket targets (numbered NPCs + cancel), Fence items (numbered + leave) use `WriteSRMenuOption()`
- Evil Deeds numbered deed list uses `WriteSRMenuOption()` with SR-friendly comma-separated stats instead of fixed-width columns

### Love Street
- New `DisplayLocationSR()` screen reader layout with categorized menu sections (Pleasure Houses, Social, Potions) and active potion status
- 15 box headers converted to `WriteBoxHeader()`: Love Street, Pleasure Awaits, The Beauty Nest, Hall of Dreams, A Night of Passion, Something Is Wrong, Mingle, Take Someone on a Date, Love Street Gift Shop, Madame Whispers' Gossip, Madame Zara's Love Potions, Your Romantic Life, plus dynamic headers for courtesan/gigolo names, NPC mingle, and date partner
- 1 section header converted to `WriteSectionHeader()`: Night of Pleasure results
- Courtesan menu (9 companions + return) uses `WriteSRMenuOption()` with SR-friendly stats (name, race, price, risk level)
- Gigolo menu (9 companions + return) uses `WriteSRMenuOption()` with SR-friendly stats
- Mingle NPC list uses `WriteSRMenuOption()` with relationship tags and personality hints as comma-separated text
- MingleWithNPC interaction menu (Flirt/Compliment/Buy Drink/Date/Talk/Back) converted to `WriteSRMenuOption()`
- Date activity menu (5 options + cancel) converted to `WriteSRMenuOption()`
- Date NPC selection uses `WriteSRMenuOption()`
- Gift Shop items (9 gifts + leave) converted to `WriteSRMenuOption()` with affordability indicator
- Gift recipient NPC selection uses `WriteSRMenuOption()`
- Gossip menu (4 services + leave) converted to `WriteSRMenuOption()`
- Gossip investigate NPC list converted to `WriteSRMenuOption()`
- Love Potions menu (4 potions + romance stats + leave) converted to `WriteSRMenuOption()` with active potion indicators

### Love Corner (Legacy)
- 5 parenthetical menus converted from `(X)` notation to `X.` format for screen reader clarity: main menu, dating actions, change feelings, gift shop, love history

### Level Master
- 5 box headers converted to `WriteBoxHeader()`: Level Master's Sanctum, Milestone Reached, The Crystal Ball, Visions in the Crystal, Help Ally

### SysOp Console
- 5 box headers converted to `WriteBoxHeader()`: SysOp Console, System Management, Game Configuration, Player Management, World Management
- Multi-line UPDATE AVAILABLE box wrapped in `!IsScreenReader` guard with plain text alternative

### Arena, Bank, Dormitory, Wilderness, News
- Arena header converted to `WriteBoxHeader()`
- Bank header converted to `WriteBoxHeader()`
- Dormitory header converted to `WriteBoxHeader()`
- Wilderness header converted to `WriteBoxHeader()`
- News Board headers (2) converted to `WriteBoxHeader()`

### Pantheon
- 8 box headers converted to `WriteBoxHeader()`: The Pantheon, God Details, Worship Confirmation, Divine Ascension, Immortal Status, God Rankings, Prayer, Divine Blessing

### Team Corner
- New `DisplayLocationSR()` screen reader layout with team status and categorized menu sections (Information, Actions, Communication, Online Features, Navigation)
- 7 box headers converted to `WriteBoxHeader()`: Team Corner, Create Team, Team Management, Team Roster, Team Challenge, Team Treasury, Team Recruitment
- 12 section headers converted to `WriteSectionHeader()`: Team Information, Team Status, Team Members, member name, Equipment, Equip Item To, Unequip From, Choose Opponent Team, Team War, Your Team Wins/Loses, War History, Facilities
- Equipment management menu (Equip/Unequip/Take All/Done), Team Wars menu (Challenge/History/Back), and HQ menu (Upgrade/Deposit/Withdraw/Back) converted to `WriteSRMenuOption()`
- 5 column dividers (`─`) in rankings, detailed members, NPC recruitment, player allies, and opponent teams suppressed in screen reader mode
- Hand selection prompt uses plain text in SR mode instead of bracket notation

### Settlement
- 3 box headers converted to `WriteBoxHeader()`: The Outskirts, Building Details, Proposal Details

### Quest Hall
- Main menu uses `WriteSRMenuOption()` in screen reader mode with quest counts as plain text (no `|` pipe separators)
- `─── Quest Hall ───` divider suppressed in screen reader mode
- 3 section headers (Available Quests, Your Active Quests, BOUNTY BOARD) converted to `WriteSectionHeader()`
- Quest detail `╔═ title ═╗` box converted to `WriteSectionHeader()`
- Claim/Turn-In/Abandon numbered selection menus use `WriteSRMenuOption()` for consistent screen reader formatting
- Pipe `|` separators in quest summary and bounty board replaced with commas

### Marketplace, Love Street, Character Creation, Dev Menu, Healer, Prison
- Marketplace: 3 headers converted; Love Street: 2 headers converted; Character Creation: 2 headers converted
- Dev Menu: 4 headers converted; Quest Hall: 1 header converted; Healer: 1 header converted; Prison: 1 header converted

### Base Location (all locations)
- 15+ additional box headers converted to `WriteBoxHeader()`: Important News, Castle Under Attack, Mysterious Encounter, NPC encounters, Active Quests, Crafting Materials, Ambush!, Quick Preferences, People Nearby, Talking to NPC, Debug NPC, Your Mailbox, Trade Packages, Bounty Board, Auction House
- Stranger encounter menu options wrapped in SR guard using `WriteSRMenuOption()`
- New `ShowQuickCommandsHelpSR()` method providing plain text Quick Commands help screen
- Status bar (`HP: X/Y | Gold: Z | Stamina: A/B | Lv N (X%)`) renders as plain labeled text in screen reader mode, removing inline color switches and `|` pipe separators
- Quick command bar `─────` divider suppressed in screen reader mode; `[S]tatus [*]Inventory` bracket notation replaced with plain `S Status, * Inventory` comma-separated list
- Character Status (`/status`) screen: header converted to `WriteBoxHeader()`; 13 section headers (Basic Information, Level & Experience, Combat Statistics, Equipment, Wealth, Relationships, Alignment & Reputation, Faction, Battle Record, Dungeon Progress, Divine Status, Artifacts, Afflictions) converted to `WriteSectionHeader()`; footer `────────` divider suppressed in screen reader mode
- NPC approach encounter `─────────` dividers suppressed in screen reader mode
- Combat rewards header converted to `WriteSectionHeader()`
- `ClearScreen()` separator in screen reader mode changed from box-drawing `────────` to plain `---`

### Systems (codebase-wide box header conversion)
- New `UIHelper.WriteBoxHeader()`, `UIHelper.WriteSectionHeader()`, `UIHelper.WriteDivider()` static methods for Systems files that don't inherit BaseLocation — check `GameConfig.ScreenReaderMode` and render plain text or box-drawing accordingly
- 48 box headers converted across 24 Systems files: CombatEngine (4), EndingsSystem (5), StreetEncounterSystem (13), BetrayalSystem (4), OldGodBossSystem (3), VisualNovelDialogueSystem (2), WorldBossSystem (2), SysOpConsoleManager (3), AchievementSystem (2), ArtifactSystem (2), CharacterCreationSystem (1), CompanionSystem (1), GriefSystem (1), IntimacySystem (2), InventorySystem (2), MoralParadoxSystem (2), OnlineAdminConsole (2), OnlinePlaySystem (1), OpeningSequence (1), OpeningStorySystem (3), PuzzleSystem (1), QuestSystem (1), RareEncounters (1), SevenSealsSystem (2), VersionChecker (1)

### Bank
- New `DisplayLocationSR()` screen reader layout with account summary (gold on hand, in bank, total worth, guard status, loan) and labeled menu
- `ACCESS DENIED`, `BANK ROBBERY`, robbery success/defeat boxes converted to `WriteBoxHeader()`
- 10 section headers converted to `WriteSectionHeader()`: Deposit Gold, Withdraw Gold, Transfer Gold, Loan Services, Interest & Rates, Ironvault Bank Rate Schedule, Account Summary, Guard Duty Application, Resign from Guard Duty, Your Account Status

### Marketplace (Auction House)
- New `DisplayLocationSR()` screen reader layout with listing stats and labeled menu
- 3 box-drawing dividers wrapped in `!IsScreenReader` guards

### Healer
- New `DisplayLocationSR()` screen reader layout with HP/gold/potions/afflictions summary and labeled service menu
- `YOUR HEALTH STATUS` box converted to `WriteBoxHeader()`
- 5 section headers converted to `WriteSectionHeader()`: Healing Services, Disease Treatment, Other Services, Services, Addiction Rehabilitation Program
- 12 instances of split `, {Manager} says` dialogue merged into single lines for natural reading flow
- Tax breakdown `─────` divider in `CityControlSystem.DisplayTaxBreakdown()` suppressed in screen reader mode

### Melodia Companion Fix
- Melodia now correctly classified as Bard instead of Paladin — was using Paladin abilities (Lay on Hands, Divine Smite, Aura of Protection, Holy Avenger) instead of music-themed abilities
- New `CombatRole.Bard` enum value added with proper `CharacterClass.Bard` mapping
- Abilities changed to: Healing Melody, War March, Lullaby of Iron, Battle Hymn
- Bard-specific stat growth on level-up: emphasis on Magic Power, Healing Power, and Speed
- All 4 CombatRole-to-CharacterClass mapping switches updated (CompanionSystem + 3 in InnLocation)

### Companion Improvements
- Companions now receive level-appropriate starting equipment when recruited — weapon, body armor, and head armor selected from the shop database at their level. Tanks also receive a shield. Previously companions started with empty equipment slots.
- Companion AI no longer wastes turns using heal abilities when nobody needs healing. Heal-type class abilities are now filtered from the AI ability pool unless a party member is below 70% HP. Previously the random ability selection could pick a heal ability at full HP, showing "recovers 0 HP" and wasting the companion's turn.

### Bug Fixes
- Dormitory return navigation: leaving the Dormitory incorrectly navigated to Anchor Road instead of Main Street
- Music Shop return key: used Q instead of R, inconsistent with all other locations
- Quest turn-in "NotYourQuest" error: `CompleteQuest` used exact case-sensitive `!=` comparison on quest Occupier while other quest methods used case-insensitive matching — if the player's display name ever differed in casing from the stored Occupier, turn-in silently failed. Now uses `StringComparison.OrdinalIgnoreCase` throughout. Additionally, `CompleteQuest` now prioritizes quests matching both ID and player name when looking up by ID, preventing wrong-quest returns from duplicate IDs in the shared MUD database.
- Redundant double `MergePlayerQuests` call on online login removed — `LoadSaveByFileName` called it once inside `RestorePlayerFromSaveData` and then again explicitly, creating orphaned Quest objects disconnected from the player's ActiveQuests list.
- Dungeon room navigation confusion: `ConnectRooms` assigned random compass directions without tracking spatial positions, so going North then South could land in a different room than where you started. Rooms are now placed on a 2D grid during generation and connections only use directions consistent with relative grid positions — going North always moves to the room above, South always below, etc. Extra loop connections also respect spatial consistency.
- Explored dungeon rooms now announce their name on re-entry ("You return to: {room name}") so players always know where they are. Previously only newly-discovered rooms showed an entry message, leaving explored rooms silent — especially confusing for screen reader users.
- Dungeon map invisible rooms after map reveal: `BuildRoomPositionMap` had a hard depth limit of 3 rooms from the player's current position, so after a map-reveal event (wounded adventurer map, vision) rooms beyond 3 hops simply didn't appear on the map — including the stairs room. Depth limit removed; map now shows all reachable rooms. Map also anchors to the entrance room instead of shifting perspective with every move.
- Bank guard job lost on quit: `BankGuard` and `BankWage` were never serialized to save data, so players had to reapply for the guard position every session. Both fields now persist across save/load.
- Faction ambush from teammates: NPCs on the player's team could still ambush the player if they belonged to a rival faction. Team members are now excluded from the potential ambusher pool.
- Ghost team member — dead but can't resurrect: `HandleNpcTeammateDeath` ignored the return value of `MarkNPCDead`, which heals player team NPCs instead of killing them. After the heal, the code unconditionally set `IsDead = true`, removed the NPC from the party, and triggered grief — completely overriding the team protection. Now respects `MarkNPCDead`'s protection: if the NPC survives (player team heal), shows "knocked down but staggers back" and keeps them in the party. Resurrect menu now explains when dead members are permanently slain instead of saying "All your team members are alive!" Team status shows "Gone" instead of "Dead" for permadead NPCs.
- NPC team member equipment lost on server restart: `OnlineStateManager.SerializeCurrentNPCs()` saved the `EquippedItems` slot→ID mapping but never serialized the actual `DynamicEquipment` objects (dungeon loot with IDs ≥ 100000). On world state reload, `WorldSimService.LoadWorldState()` found an empty `DynamicEquipment` list and skipped re-registering the equipment — leaving EquippedItems pointing to stale IDs. NPCs appeared with missing gear, or wore random items if the old IDs coincidentally matched equipment registered by other sessions. Now serializes full `DynamicEquipment` alongside `EquippedItems`, matching the single-player `SaveSystem` path.

## Files Changed

- `Scripts/Core/GameConfig.cs` — Version 0.50.0; VersionName "Open Doors"
- `Scripts/Locations/AnchorRoadLocation.cs` — Screen reader `DisplayLocationSR()` with categorized menus; `IsScreenReader` guards on 8 box-drawing separator pairs; 6 inline box headers converted to `WriteBoxHeader()`; 7 section headers converted to `WriteSectionHeader()`; prison menu uses `WriteSRMenuOption()`
- `Scripts/Locations/ChurchLocation.cs` — Screen reader `DisplayLocationSR()` with alignment info and service menu; `IsScreenReader` guard in static entry restriction check; church header converted to `WriteBoxHeader()`; 4 section headers converted to `WriteSectionHeader()`; 3 separator lines suppressed in screen reader mode
- `Scripts/Locations/WeaponShopLocation.cs` — Screen reader `DisplayLocationSR()` with weapon summary and categorized menu; shop header converted to `WriteBoxHeader()`; main menu uses `WriteSRMenuOption()`; category/sell/auto-buy headers converted to `WriteSectionHeader()`; column and offer dividers converted to `WriteDivider()`
- `Scripts/Locations/ArmorShopLocation.cs` — Screen reader `DisplayLocationSR()` with AC summary and per-slot menu; shop header converted to `WriteBoxHeader()`; main menu slot list and actions use `WriteSRMenuOption()` in SR mode; slot/sell/auto-buy headers converted to `WriteSectionHeader()`; column divider converted to `WriteDivider()`
- `Scripts/Locations/MagicShopLocation.cs` — Screen reader `DisplayLocationSR()` with categorized menu; shop header converted to `WriteBoxHeader()`; 6 additional box headers converted to `WriteBoxHeader()`; 14 section headers converted to `WriteSectionHeader()`; 8 column dividers converted to `WriteDivider()`
- `Scripts/Locations/MusicShopLocation.cs` — Screen reader `DisplayLocationSR()` with services menu; shop header converted to `WriteBoxHeader()`; 4 section headers converted to `WriteSectionHeader()`; instrument column divider converted to `WriteDivider()`; menu uses `WriteSRMenuOption()`; return key changed from Q to R for consistency
- `Scripts/Locations/MainStreetLocation.cs` — Main header converted to `WriteBoxHeader()`; level eligibility box SR guard; Player Statistics header + 6 category headers converted; Achievements header/pagination/content SR rewrite; Hall of Fame header + divider + SR guards; Citizens header + 3 section headers converted; Journey screen complete `║`-border removal with 4 `WriteSectionHeader()` calls; Session Summary header + 2 dividers; Settings/Preferences/Help/Attack headers converted to `WriteBoxHeader()`; 7 preference sub-screens converted to `WriteSectionHeader()`
- `Scripts/Locations/BaseLocation.cs` — 15+ additional box headers converted to `WriteBoxHeader()`; stranger encounter SR menu; new `ShowQuickCommandsHelpSR()` method; status bar plain text in SR mode; quick command bar SR rewrite; Character Status header + 13 section headers converted; `/status` footer divider and NPC approach dividers suppressed in SR mode; combat rewards header converted; faction ambush excludes player's team members
- `Scripts/Locations/DungeonLocation.cs` — 12 box headers converted to `WriteBoxHeader()`; explored rooms show "You return to: {room name}" on re-entry; `BuildRoomPositionMap` depth limit removed and anchored to entrance room
- `Scripts/Locations/DarkAlleyLocation.cs` — Screen reader `DisplayLocationSR()` with categorized menus; ACCESS DENIED SR guard; 16 box headers converted to `WriteBoxHeader()`; 7 section headers converted to `WriteSectionHeader()`; all sub-location menus (Drug Palace, Gambling Den, The Pit, Black Market, Loan Shark, Pickpocket, Fence, Evil Deeds) converted to `WriteSRMenuOption()`
- `Scripts/Locations/LevelMasterLocation.cs` — 5 box headers converted to `WriteBoxHeader()`
- `Scripts/Locations/SysOpLocation.cs` — 5 box headers converted to `WriteBoxHeader()`; UPDATE AVAILABLE box SR guard
- `Scripts/Locations/ArenaLocation.cs` — Header converted to `WriteBoxHeader()`
- `Scripts/Locations/BankLocation.cs` — Screen reader `DisplayLocationSR()`; `ACCESS DENIED` / `BANK ROBBERY` / robbery outcome boxes converted to `WriteBoxHeader()`; 10 section headers converted to `WriteSectionHeader()`
- `Scripts/Locations/DormitoryLocation.cs` — Header converted to `WriteBoxHeader()`; return navigation fixed from Anchor Road to Main Street
- `Scripts/Locations/WildernessLocation.cs` — Header converted to `WriteBoxHeader()`
- `Scripts/Locations/NewsLocation.cs` — 2 headers converted to `WriteBoxHeader()`
- `Scripts/Locations/PantheonLocation.cs` — 8 headers converted to `WriteBoxHeader()`
- `Scripts/Locations/TeamCornerLocation.cs` — 7 headers converted to `WriteBoxHeader()`; new `DisplayLocationSR()` with categorized menus; 12 section headers converted to `WriteSectionHeader()`; equipment/wars/HQ menus converted to `WriteSRMenuOption()`; 5 column dividers SR-guarded; hand selection SR-friendly; resurrect shows permadead/aged reason instead of "everyone alive"; status shows "Gone" for permadead NPCs
- `Scripts/Locations/SettlementLocation.cs` — 3 headers converted to `WriteBoxHeader()`
- `Scripts/Locations/MarketplaceLocation.cs` — Screen reader `DisplayLocationSR()`; 3 headers converted to `WriteBoxHeader()`; 3 box-drawing dividers SR-guarded
- `Scripts/Locations/LoveStreetLocation.cs` — Screen reader `DisplayLocationSR()` with categorized menus and potion status; 15 box headers converted to `WriteBoxHeader()`; 1 section header converted to `WriteSectionHeader()`; all sub-location menus (Beauty Nest, Hall of Dreams, Mingle, Dates, Gift Shop, Gossip, Love Potions) converted to `WriteSRMenuOption()`
- `Scripts/Locations/LoveCornerLocation.cs` — 5 parenthetical menus converted from `(X)` to `X.` notation for screen reader clarity
- `Scripts/Locations/CharacterCreationLocation.cs` — 2 headers converted to `WriteBoxHeader()`
- `Scripts/Locations/DevMenuLocation.cs` — 4 headers converted to `WriteBoxHeader()`
- `Scripts/Locations/QuestHallLocation.cs` — 1 header converted to `WriteBoxHeader()`; SR menu with `WriteSRMenuOption()`; 3 section headers + quest detail box converted to `WriteSectionHeader()`; numbered selection menus SR-friendly; pipe separators replaced with commas
- `Scripts/Locations/HealerLocation.cs` — Screen reader `DisplayLocationSR()`; `YOUR HEALTH STATUS` box converted to `WriteBoxHeader()`; 5 section headers converted to `WriteSectionHeader()`; 12 split `, {Manager} says` dialogue lines merged into single lines
- `Scripts/Locations/PrisonLocation.cs` — 1 header converted to `WriteBoxHeader()`
- `Scripts/UI/UIHelper.cs` — New static `WriteBoxHeader()`, `WriteSectionHeader()`, `WriteDivider()` methods for SR-aware rendering in Systems files
- `Scripts/Systems/CombatEngine.cs` — 4 box headers converted to `UIHelper.WriteBoxHeader()`; companion AI heal ability filter — Heal-type class abilities excluded from random ability pool when no party member is below 70% HP; `HandleNpcTeammateDeath` respects `MarkNPCDead` return value — player team NPCs survive with knockdown message instead of dying
- `Scripts/Systems/EndingsSystem.cs` — 5 box headers converted
- `Scripts/Systems/StreetEncounterSystem.cs` — 13 box headers converted
- `Scripts/Systems/BetrayalSystem.cs` — 4 box headers converted
- `Scripts/Systems/OldGodBossSystem.cs` — 3 box headers converted
- `Scripts/Systems/VisualNovelDialogueSystem.cs` — 2 box headers converted
- `Scripts/Systems/WorldBossSystem.cs` — 2 box headers converted
- `Scripts/Systems/SysOpConsoleManager.cs` — 3 box headers converted
- `Scripts/Systems/AchievementSystem.cs` — 2 box headers converted
- `Scripts/Systems/ArtifactSystem.cs` — 2 box headers converted
- `Scripts/Systems/CharacterCreationSystem.cs` — 1 box header converted
- `Scripts/Systems/CompanionSystem.cs` — 1 box header converted; FALLEN death scene box wrapped in `!ScreenReaderMode` guard; new `CombatRole.Bard` enum value; Melodia changed from Hybrid/Paladin to Bard with music-themed abilities; Bard stat growth in both level-up paths; Bard potion allocation; new `EquipStartingGear()` method gives level-appropriate weapon, body armor, head armor (and shield for tanks) from shop database on recruitment
- `Scripts/Systems/GriefSystem.cs` — 1 box header converted
- `Scripts/Systems/IntimacySystem.cs` — 2 box headers converted
- `Scripts/Systems/InventorySystem.cs` — 2 box headers converted
- `Scripts/Systems/MoralParadoxSystem.cs` — 2 box headers converted
- `Scripts/Systems/OnlineAdminConsole.cs` — 2 box headers converted
- `Scripts/Systems/OnlinePlaySystem.cs` — 1 box header converted
- `Scripts/Systems/OpeningSequence.cs` — 1 box header converted
- `Scripts/Systems/OpeningStorySystem.cs` — 3 box headers converted
- `Scripts/Systems/PuzzleSystem.cs` — 1 box header converted
- `Scripts/Systems/QuestSystem.cs` — `CompleteQuest` case-insensitive Occupier matching; 1 box header converted
- `Scripts/Systems/RareEncounters.cs` — 1 box header converted
- `Scripts/Systems/SevenSealsSystem.cs` — 2 box headers converted
- `Scripts/Systems/VersionChecker.cs` — 1 box header converted
- `Scripts/Systems/DungeonGenerator.cs` — `ConnectRooms` rewritten with grid-based spatial tracking; new `DirectionOffset()` helper; rooms placed on 2D grid so compass directions are spatially consistent; extra loop connections only between grid-adjacent rooms
- `Scripts/UI/TerminalEmulator.cs` — Screen reader `ClearScreen()` separator changed from box-drawing `────────` to plain `---`
- `Scripts/Locations/InnLocation.cs` — 3 CombatRole-to-CharacterClass mapping switches updated with `CombatRole.Bard => CharacterClass.Bard`
- `Scripts/Systems/CityControlSystem.cs` — Tax breakdown `─────` divider suppressed in screen reader mode
- `Scripts/Core/GameEngine.cs` — Removed redundant second `MergePlayerQuests` call in `LoadSaveByFileName`; BankGuard and BankWage restore from save data
- `Scripts/Systems/SaveDataStructures.cs` — Added `BankGuard` and `BankWage` fields to PlayerData
- `Scripts/Systems/SaveSystem.cs` — BankGuard and BankWage serialization
- `Scripts/Systems/OnlineStateManager.cs` — `SerializeCurrentNPCs()` now serializes `DynamicEquipment` list for NPC-worn items with IDs ≥ 100000, matching the SaveSystem serialization path; fixes NPC team member equipment loss on server restart

### Comprehensive Screen Reader Pass (Second Wave)

Full codebase audit ensuring every remaining box-drawing character, decorative separator, visual bar, and special Unicode symbol is guarded by `GameConfig.ScreenReaderMode`. Visual players see zero changes — all decorative output is preserved in the `else` branch.

**GameEngine.cs** — Main menu redrawn with plain `S. Single-Player` format (no colored brackets) when screen reader mode is toggled; all sub-screens (Load Save, Online Multiplayer, Story So Far pages 2-4, Usurper History, BBS & Online Server List, Credits, Support the Developer) have box headers, decorative dividers, and separators wrapped with screen reader guards. Specific fixes: Save File Management box, Save Slots box, While You Were Gone box (open + close), Spectator Mode spectating header, Support Usurper Reborn box, "How You Can Help" divider, Support page decorative cyan divider, telemetry opt-in box, Change Password header, You Have Died banner, Death Penalties separator, Prison underline, BBS List header box and dividers, Credits header box.

**UsurperHistorySystem.cs** — All 5 page headers (`+=====+|` boxes) wrapped with SR guards showing plain title text. All 4 person boxes (Jakob Dangarden, Rick Parrish, Daniel Zingaro, Jason Knight) wrapped with SR guards showing `NAME - Title` format with unbordered text. Fixed missing line in Rick Parrish visual branch ("original Pascal source code to modern systems. He created 32-bit").

**CombatEngine.cs** — Status Effects header, loot drop decorative separators (epic/rare/common), equipment comparison separators, TIP box, Select Poison header, Combat Abilities headers (3 locations), Heal Ally header, NPC/mercenary fallen banners, victory banner, First Blood box, Nightmare permadeath lines, Rage Round header, Player Fight header, Spell Casting header, Phase header, teammate turn header, follower loot notifications.

**DungeonLocation.cs** — 8 decorative Unicode symbols replaced with plain text (▼→--, ⚠→WARNING:, ♀→--, ✚→--, ✦→--, ★→*); 6 heavy-line (━━━) banner sections hidden in screen reader mode; group party ★/· prefixes replaced with */-.

**BaseLocation.cs** — Location header underlines (3 places), deposed notification dividers, ShowBBSHeader box frame, mail list dividers (2), ReadMail header, trade package dividers (3), bounty board divider, 4 auction section headers, auction listing divider, alignment visual bar suppressed.

**WorldBossSystem.cs** — HP bar in DrawBossStatusScreen and RunWorldBossCombat shows plain "HP: X/Y (Z%)" text; Round header, Damage Leaderboard header, Combat Session Summary header, action menu box, Available Spells header, Class Abilities header.

**AlignmentSystem.cs** — Decorative separator lines around "ALIGNMENT STATUS" skipped; alignment bar (█░) replaced with plain "Chivalry: X/1000 — Darkness: Y/1000".

**OnlineAuthScreen.cs** — Login banner box replaced with plain title; Create Account separator.

**Location Files:**
- MainStreetLocation.cs — BBS header box, bottom borders, online section separators (BBS + full-size)
- WildernessLocation.cs — Region name, discoveries, individual discovery headers
- CharacterCreationLocation.cs — Helpful Hints header
- AnchorRoadLocation.cs — Header box
- DormitoryLocation.cs — NPC name underline
- PrisonLocation.cs — Prison Activities titled box
- PantheonLocation.cs — Table header separator
- ArenaLocation.cs — 8 headers (Choose Opponent, opponent separator, Victory, Defeat, Leaderboard, leaderboard separator, Recent Fights, PvP Record)

**Systems Files:**
- OpeningSequence.cs — Decorative separator
- OpeningStorySystem.cs — Letter box (┌─┐│└─┘), 2 separators, NG+ revelation box
- EndingsSystem.cs — 3 title boxes, 10 decorative separators
- MoralParadoxSystem.cs — 3 separators
- OldGodBossSystem.cs — Boss title box, RenderHealthBar plain text
- SevenSealsSystem.cs — 2 lore separators
- RareEncounters.cs — Hidden Tavern box
- PuzzleSystem.cs — Puzzle title box, hint header
- VisualNovelDialogueSystem.cs — Conversation header box, 2 debug separators, 2 "A Decision Is Made" headers, marriage banner
- IntimacySystem.cs — Connection quality separators, birth announcement separators
- InventorySystem.cs — 4 section headers, menu separator, 2 comparison separators
- BetrayalSystem.cs — Motivation separator
- CharacterCreationSystem.cs — Nightmare mode borders, prestige classes header, stat roll header
- SysOpConsoleManager.cs — Header box, 22 section/subsection headers, 4 table separators, progress bar
- OnlineAdminConsole.cs — 14 section headers and separators
- OnlinePlaySystem.cs — Account Login decorative lines
- OnlineDuelSystem.cs — 2 headers
- OnlineChatSystem.cs — Who's Online box, Town News box
- MailSystem.cs — 2 mail headers
- HagglingEngine.cs — Haggle header
- HintSystem.cs — TIP box (┌─┐│└─┘)
- AchievementSystem.cs — Achievement unlock and multiple achievement boxes
- DailySystemManager.cs — Daily reset decorative lines
- TeamBalanceSystem.cs — Party Balance header
- WorldEventSystem.cs — World Events decorative lines
- LocationManager.cs — Death screen borders, death penalty separator, placeholder underline
- MaintenanceSystem.cs — Maintenance header and completion decorative lines
- BugReportSystem.cs — Bug Report header

**Admin/Dev Files:**
- DevMenuLocation.cs — Main menu header, status separator, Steam warning box, 22 sub-page headers, 8 inline section headers
- SysOpLocation.cs — Update available box, 11 section headers, download progress bar

### Comprehensive Screen Reader Pass (Third Wave)

Deep accessibility audit from a blind user's perspective, fixing all remaining issues that would confuse a screen reader.

**ANSI Art Rendering** — `DisplayArt()`, `DisplayArtAnimated()`, and `ShowASCIIArt()` now return immediately when SR mode is on. Covers all monster art, Old God art, NPC portraits, title screen, death, boss victory, treasure, level up, dungeon entrance, and combat start art — a single fix at the rendering layer that protects all callers.

**`--screen-reader` Command-Line Flag** — New flag in DoorMode.cs sets `GameConfig.ScreenReaderMode = true` at startup before any rendering. `Play-Accessible.bat` and `play-accessible.sh` launchers now pass this flag, so Steam's Screen Reader launch option automatically enables SR mode from the first screen.

**Group Combat Broadcasts** — Added `ScreenReaderMode` property to `PlayerSession`, synced from player save on load. `BroadcastToAllGroupSessions()` now sanitizes messages per-recipient, replacing box-drawing (`═→-`, `║→|`) and decorative Unicode (`★→*`, `✦→*`) with plain text. Direct `EnqueueMessage` calls for divine power and level-up notifications also sanitized per-recipient.

**WriteMenuKey() Helper** — Now outputs `key. label` format instead of `[key] label` in SR mode. Covers all BBS/compact menus.

**News Feed Symbols** — All 14 news methods in NewsSystem.cs strip leading Unicode symbols (`†`, `♥`, `⚱`, `🎂`, `⬆`, `✗`, `💋`, `♔`, `✝`, `⚔`, `⚑`, `⛓`) in SR mode. WorldSimulator.cs and WorldEventSystem.cs news entries also stripped.

**Combat Tips** — Bracket-key formatting (`[P]ower Attack`) stripped via regex in SR mode, showing plain text (`Power Attack`).

**Dungeon Room Menu** — Full SR branch added to `ShowRoomActions()` using `WriteSRMenuOption()` for all conditional options. Exit navigation also SR-friendly. Seal display uses "Collected"/"Not collected" instead of `[X]`/`[ ]`. Story status uses "Complete"/"Not started"/"In progress" instead of `[✓]`/`[X]`/`[~]`.

**Rare Encounter Headers** — 6 decorative Unicode headers (`♪♫`, `♀`, `⚗`, `🔥`, `⚡`) show plain text in SR mode.

**Romance/Dialogue** — Heart symbols (`♥`) stripped from 3 romance text lines in VisualNovelDialogueSystem.cs.

**Sub-Menu Brackets Across Locations:**
- InnLocation.cs — Aldric/companion recruitment, NPC patron list, ability toggle
- BankLocation.cs — Ban screen, loan repayment, bank robbery
- TempleLocation.cs — Mira sub-menu, flock reference
- WildernessLocation.cs — Region menu, discoveries, navigation
- HomeLocation.cs — Herb list, cancel
- MagicShopLocation.cs — Cancel buttons, pagination, ring finger selection
- PrisonLocation.cs — Decorative `III` header, two-column menu, separators
- SettlementLocation.cs — Progress bars (percentage text only), all bracket menus, proposal voting
- DungeonLocation.cs — Party management, companion recruitment, settlement NPC, healing menus

**GameEngine.cs Additional Fixes:**
- `ShowInfoScreen()` `═` separator guarded
- Save slot display uses `1. PlayerName` format in SR mode
- `[N] New Character`/`[B] Back` use dot format in SR mode
- Spectator player list uses dot format in SR mode
- `†` death symbol in MainStreetLocation NPC roster replaced with `(dead)` text

**Online/Server Screens:**
- OnlinePlaySystem.cs — Login menu `[L]`/`[R]`/`[Q]` use dot format in SR mode
- ClassAbilitySystem.cs — Quickbar menu uses plain text in SR mode
- SpellLearningSystem.cs — Spell quickbar menus use plain text in SR mode
- MudChatSystem.cs — `/who` separators guarded; `★` immortal symbol becomes `(Immortal)` text; `/group` separators guarded
- WizardCommandSystem.cs — All 5 wizard screens (`/wizhelp`, `/wizwho`, `/stat`, `/where`, `/wizlog`) have full SR branches with plain text
- SysOpConsoleManager.cs — `SysOpMenuRow()` uses dot format in SR mode; `★ UPDATE` and `◄` current marker guarded
- OnlineAdminConsole.cs — Admin menu and player selection use dot format in SR mode
