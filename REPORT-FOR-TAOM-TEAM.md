# TAOM 2.0.41: findings not fixed in our PRs

For: the TAOM team (Haterade)
From: MinimalCash63
Checked on: TAOM 2.0.41 (TAOM, TAOM_Map, LOTRLOME_Armory, TAOM.Dependencies), TAOM trunk 3dcf173c7, Dual Wield 0.10.8, Bannerlord 1.5.7.125802. Found by reading the files and decompiled code, and in logs; nothing here was tested in game unless it says so. Single player only.

A note before you start: I put this together while working on a large update of my own compatibility patch (the TAOM AIO Compatibility Patch, a big overhaul), so a few things may have got slightly mixed up. Please double-check each point before you act on it. Every item has a confidence percentage: how sure I am that it is a real problem as described, and for the PRs that the fix is right. Everything here is about base TAOM, Dual Wield and Co-op only.

This goes with the separate PRs on haterade22/TAOM (one per fix). The items below were not fixed in a PR, because:
- they are in the repo but need your choice (part 1), or
- they are in LOTRLOME_Armory, TAOM_Map or Dual Wield, which are not in the repo (part 2).
Part 3 has four suggestions for TAOM's own checks. The suggested prices for the overpriced Armory items are in the attached PRICE-TABLE-armory.md (and .csv).
The last item lists what we sent before and corrects two of our earlier points: the companions' "Faction.neutral" and the "[AMordor]" sort tag are on purpose, so please ignore those.

Each item says where, what a player sees, the evidence and how sure we are, and a suggested fix.

# Part 1: in the repo, your choice

## 23. Custom Battle muster: Khand and Abanissa have no troops, Blue Craig and Misty Mountain Orcs one each
- Confidence: 90% (verified in data and code; not seen in game). Please double-check.
- Severity: broken feature (Khand, Abanissa); wrong data, odd balance (Blue Craig, Misty Mountain Orcs)
- Where: TAOM 2.0.41 installed and trunk 8b9cc1f74: Client/Features/CustomBattles/Muster/TcbVM.cs:344 (roster = soldiers of the faction's culture that IsOffered); Client/Features/CustomBattles/Muster/CaptainResolver.cs:26-27 and :36-45 (IsOffered drops troops from vanilla modules, dummies, and troops neither in a tree nor level 21+)
- What a player sees: picking Khand or Abanissa in TAOM's Custom Battle muster, the troop roster is empty, so the army is its lords alone. Blue Craig and the Misty Mountain Orcs offer one troop, the level 36 Bolg's Ironfang.
- Evidence: re-checked 23 Nov 2026 with a script over the installed 2.0.41 ModuleData of TAOM, TAOM_Map and LOTRLOME_Armory (non-hero, Soldier, not obsolete; XSLTs and per-game-type registration not applied): Culture.battania (Khand) 6 TAOM soldiers, `guard_khand` (level 16) and training dummies, all hidden by IsOffered; Culture.abanissa 0; Culture.bluecraig 2 and Culture.mistymountainorcs 2 (`guard_*` level 16 and `*_bolgs_ironfang` level 36). Khand's real line is SandBoxCore's Battanian one, which IsOffered hides by design. In taom_spcultures.xml, Abanissa's troop attributes name `harad_*` troops and Blue Craig's and Misty Mountain Orcs' name `goblin_*` troops. docs/features/custom-battles.md:132-134 records that Abanissa has no soldiers of its own culture and that the muster "never showed the Battanian line", without calling it a defect. Confidence: Verified (data and code); not seen in game.
- Fix: not fixed: no single obvious design choice. Options for the team:
  1. The muster also offers the troops each culture names in its own attributes (basic_troop, elite_basic_troop, melee_militia_troop, ranged_militia_troop, ranged_elite_militia_troop, already read by CultureTroopIdReader) and their upgrade trees. Abanissa gets the Harad line (then a copy of Harwan); Blue Craig and Misty Mountain Orcs get the goblin line (a Goblin-town reskin). Khand still gets nothing unless the vanilla-module rule is relaxed.
  2. Exempt the Battanian tree from the vanilla-module rule for Khand only (shows Calradian troops, which TAOM hides on purpose elsewhere).
  3. Leave factions with fewer than N offered soldiers out of the muster's faction list (TcbVM faction list).
  4. Give the four cultures their own TAOM troops (data work, the long-term answer).
  Each changes what these factions are in Custom Battle, so it needs the team's call. No co-op path involved.
- Commit: none
- How checked: script over the installed data (counts above); code and feature doc read. No game run.

## 16. Prince Imrahil has two of each son and two daughters of nearly one name
- Confidence: 85% (verified in data; which set is right is your call). Please double-check.
- Severity: wrong data
- Where (TAOM 2.0.41 = trunk 8b9cc1f74 unless noted, Main/_Module/ModuleData/): heroes.xslt 583, 621, 631 (lord_1_24, lord_1_35, lord_1_25); characters/heroes.xml 3787, 3792, 3797, 3802 (lord_1_9_1 to _4; inst 3786 to 3801) and 1584 (lord_1_9_6 Lothériel); characters/lords.xml 7777, 7833, 7886, 7951; TAOM.Tests/Core/LordIdentityConsistencyTests.cs 539-542
- What a player sees: clan Imrazôrionath lists two Elphirs (43 and 13), two Erchirions (33 and 10), two Amrothos (14 and 6), and daughters Lothériel (33) and Lothiriel (1), all children of Imrahil and Lothwen.
- Evidence: both sets carry father Hero.lord_1_9 and mother Hero.lord_1_9_5 (XSLT output). TAOM's test accepts the names as duplicates with the comment "Canonical children of Imrahil against unrelated Gondor lords", but the XSLT shows they are related. Confidence: Verified.
- Fix: not fixed: it needs the team's choice of which daughter is meant, and removing Hero entries touches live saves (heroes already in a campaign). Suggested fix: remove the four Hero entries lord_1_9_1 to lord_1_9_4 from the "LORDS THAT HAD NO HERO ENTRY" block of characters/heroes.xml, drop Elphir, Erchirion and Amrothos from AcceptedDuplicateNames, and keep one daughter.
- Commit: none
- How checked: read the files and the test's accepted-duplicates block. Nothing run.

## 17. Children older than their parents (Erebor and some vanilla-slot lords)
- Confidence: 85% (verified in data). Please double-check.
- Severity: wrong data
- Where (TAOM 2.0.41 = trunk 8b9cc1f74): Main/_Module/ModuleData/characters/lords.xml: lord_E4_1 Nár 18 (6861), lord_E4_2 Tíli 18 (6912), children lord_E4_3 30 (6960), lord_E4_4 Táli 37 (7011); lord_E2_1 21, lord_E2_2 23, child lord_E2_4 34 (6452); lord_E3_1 19, lord_E3_2 18, children 22 and 30; lord_E6_1 and _2 18, children 23 and 25; lord_E1_1 Dáin 38 (5958), son lord_E1_4 Náin 38 (6102). lords.xslt: lord_1_46 53 with mother lord_1_40_1 33; lord_4_23_1 61 with father lord_4_23 44 (and in another clan); lord_S9_c 29 / lord_S9_l 25; lord_A9_c 19 / lord_A9_l 19; lord_NE8_c1 19 / lord_NE8_s 19; lord_NE9_s 24 / lord_NE9_l 25
- What a player sees: encyclopedia ages that cannot be right, for example Táli (37) daughter of Nár (18) and Tíli (18).
- Evidence: the age attributes above; TAOM's race age system does not rescale XML ages (docs/features/race-age-system.md). Confidence: Verified.
- Fix: not fixed: each pair needs an age chosen (raise the dwarf founders, for example Dáin to 252 as in the books, or lower the children), which is the team's lore call, and lord_4_23_1's clan placement may be deliberate. Suggested fix: the ages listed under Where above, adjusted as you prefer.
- Commit: none
- How checked: read the files. Nothing run.

## 12. Dol Guldur Uruk Warrior upgrades sideways to the Uruk Skirmisher
- Confidence: 40% (your changelog says the side branch is deliberate). Please double-check.
- Severity: wrong data (deliberate per TAOM)
- Where: TAOM 2.0.41 troops\troops_dolguldur.xml 2197; trunk 8b9cc1f74 Main/_Module/ModuleData/troops/troops_dolguldur.xml 2281 (dg_uruk_veteran_warrior, level 21, target dg_uruk_skirmisher, level 21 at 2610)
- What a player sees: an upgrade that gives a troop of the same level and tier.
- Evidence: TAOM's own changelog (docs/changelog-archive/CHANGELOG-2026-H2-handwritten.md, near line 8761) lists "the uruk skirmisher side-branch" among ten deliberate same-level branches kept as co-equal choices, priced by TaomPartyTroopUpgradeModel. Confidence: Verified (data and the documented decision).
- Fix: not fixed: deliberate design. Suggested, only if the team changes its mind: move the target to dg_uruk_warrior (level 16, trunk 2221).
- Commit: none
- How checked: read the tree levels and the changelog entry. Nothing run.

## 13. Far-Rhûn Horse Master has no upgrade source
- Confidence: 35% (likely deliberate: the Horse Master is a volunteer recruit). Please double-check.
- Severity: wrong data (minor, likely deliberate)
- Where (TAOM 2.0.41 = trunk 8b9cc1f74): Main/_Module/ModuleData/troops/troops_rhun_new.xml 541 (far_rhun_horse_master, level 21); far_rhun_horseman 426 with one target (far_rhun_cavalry, 446)
- What a player sees: no troop upgrades into "[Rhûn] Far-Rhun Horse Master", and it upgrades into nothing.
- Evidence: a player can get it: TAOM added it to Rhûn's volunteer pool on purpose (Main/Features/TroopProgression/RecruitmentPools/VolunteerRecruitmentService.Rhun.cs 48 and 55, comment "reachability fix: its cavalry line was an AI-only orphan"). It is also in two party templates (taom_partyTemplates.xml 3472, 3660). Confidence: Verified.
- Fix: not fixed: the troop is reachable as a volunteer, and an extra upgrade edge from far_rhun_horseman would be a design change. Suggested, if the team wants it in the tree: a second target on far_rhun_horseman after trunk line 446.
- Commit: none
- How checked: grep of trunk for far_rhun_horse_master. Nothing run.

## 24. Career ability "activated!" toast and distance-cache rebuild messages are not localized
- Confidence: 90% (verified in code). Please double-check.
- Severity: cosmetic (untranslated player text)
- Where: TAOM 2.0.41 installed and trunk 8b9cc1f74: Main/Features/CareerSystem/Abilities/AbilityEffectExecutor.cs:95-97; Main/Features/EditorCacheRebuild/RuntimeCacheRebuildService.cs:52, 68, 73, 175; Main/Features/TaomSettings.cs:2115-2116 (rebuild button) and :1140-1141 (Apply battle settings button)
- What a player sees: "<ability> activated!" in English in every language whenever a career ability fires; English-only answers from the MCM "Rebuild Settlement Distance Cache" and "Apply battle settings" buttons. Two of the rebuild texts also contain an em dash.
- Evidence: `$"{abilityName} activated!"` (AbilityEffectExecutor.cs:97); literal strings in Notify and NotifyFailure and in the two button catch blocks. The sibling toast at AbilityDamageAttributionReporter.cs:41-42 uses the `{=taom_career_dmg_none}` key. Confidence: Verified (code).
- Fix: not fixed: TAOM's /localize procedure (.claude/skills/localize/SKILL.md, Case A) lets a contributor wrap and register the English only. The 12 language rows, the translation cache and the seeding run (`tools/translate_with_claude.py`, a paid AI run) are the project lead's, so the languages cannot be filled here, and keys without rows would turn LanguageFileCoverageTests red. Keys to add (English default after the key):
  - `{=taom_career_ability_activated}{ABILITY} activated!` (SetTextVariable "ABILITY")
  - `{=taom_cache_rebuild_not_ready}Cache rebuild not ready: {REASON}`
  - `{=taom_cache_rebuild_in_progress}Cache rebuild already in progress. Check the log for status.`
  - `{=taom_cache_rebuild_starting}TAOM cache rebuild starting in background. This may take 10-30 minutes. Game stays playable but pathfinding queries during the rebuild may be inconsistent.`
  - `{=taom_cache_rebuild_verify_failed}Cache rebuild verification FAILED: {REASON}. The .prev backup is intact; see rgl_log_*.txt for restoration instructions.`
  - `{=taom_cache_rebuild_start_failed}Cache rebuild FAILED to start: {ERROR}. See rgl_log_*.txt for details.`
  - `{=taom_battle_settings_apply_failed}Battle settings could not be applied: {ERROR}`
  Then `python tools/harvest_literal_loc_keys.py --apply` and your translation pass. The exception type and message stay a variable. No co-op path involved.
- Commit: none
- How checked: code and the localize skill read. No change.

## 09. Same names with and without accents in TAOM text
- Confidence: 70% (some accents follow map ids). Please double-check.
- Severity: cosmetic
- Where: trunk 8b9cc1f74 Main/_Module/ModuleData, many files. The one form that is wrong: realms_titles.xml 901 "{=realms_duchy_nurn}Nûrn" (905 comment "Barad Nûrn"), taom_realms_title_strings.xml 94, taom_culture_text_strings.xml 1850 and taom_spcultures.xml 3290 "Nûrn Slaves"
- What a player sees: Rhun / Rhûn, Numenorean / Númenórean, Lothlorien / Lothlórien and others in different places; "Nûrn" where Tolkien writes Nurn.
- Evidence: counts from a script over the files (Verified). Overlaps item 50.
- Fix: not fixed: no case is clear enough to change alone. Nûrn is tied to the Mordor duchy, a clan name and the TAOM_Map castle "Barad Nûrn", so a rename should go with the map and the team's choice of forms. Suggested fix: the team picks one form per name (Tolkien's: Rhûn, Númenórean, Anórien, Lothlórien, Théoden, Dáin, Nazgûl, Dúnedain, Círdan, Lugbúrz, Nurn, Udûn, Uglúk), then one scripted pass over the English, the language rows and the cache.
- Commit: none
- How checked: grep of trunk for Nûrn (5 hits outside Languages). Nothing run.

## 18. An Erebor dwarf lady named "Mary"
- Confidence: 75% (may be a deliberate joke name). Please double-check.
- Severity: cosmetic
- Where (TAOM 2.0.41 = trunk 8b9cc1f74): Main/_Module/ModuleData/characters/lords.xml 6757 (lord_E3_5, name key aom_lord_E3_5_name); Languages/*/std_taom_lord_name_strings_*.xml 387
- What a player sees: "Mary" in every language (Chinese 玛丽), while the other Erebor ladies have Khuzdul-style names (Tíli, Thálfi, Dráli).
- Evidence: the name rows above. Confidence: Verified.
- Fix: not fixed: a new name has to be invented, which is the team's call. Suggested fix: a dwarvish name such as "Mári" on the English default and its registered row, the 12 language rows reset to it, and the key evicted from tools/translation_cache (TRANSLATOR_GUIDE checklist).
- Commit: none
- How checked: read the files. Nothing run.

## 19. Rhûn heroes lord_6_23 and lord_6_24 are defined twice with different biographies
- Confidence: 80% (verified in data; which text wins not checked in game). Please double-check.
- Severity: cosmetic
- Where: TAOM 2.0.41 = trunk 8b9cc1f74 Main/_Module/ModuleData/heroes.xslt 3464-3477; trunk characters/heroes.xml 1965 and 2009 (inst 1964, 2008)
- What a player sees: one of two biographies for Hûz-Margôz and Nikath; the XSLT one reads "Hûz-Margôz Huz commands fortress garrisons along the western frontier", with a stray "Huz".
- Evidence: both define the same Hero ids in the same clans (clan_khuzait_12, _13) with different text. Which one wins in game is not checked. Confidence: Verified (files).
- Fix: not fixed: picking the biography is the team's call, and the winning file at load is unverified. Suggested fix: drop the text attribute from the two templates in heroes.xslt (keep faction) so characters/heroes.xml holds the only bio, then check the encyclopedia.
- Commit: none
- How checked: read the files. Nothing run.

## 59. Two Rhûn clans named "Hûz" and two named "Bozorganith"
- Confidence: 80% (verified in data). Please double-check.
- Severity: cosmetic
- Where (TAOM 2.0.41 = trunk 8b9cc1f74): Main/_Module/ModuleData/spclans.xslt 1089 ({=TAOM_clan_khuzait_1}Hûz) and characters/clans.xml 348 ({=aom_clan_khuzait_12_name}Hûz); spclans.xslt 1187 ({=TAOM_clan_khuzait_9}Bozorganith) and characters/clans.xml 439 ({=aom_clan_khuzait_19_name}Bozorganith)
- What a player sees: two different Rhûn clans with the same name in the clan list and encyclopedia.
- Evidence: the four name rows above. Confidence: Verified.
- Fix: not fixed: new clan names are the team's lore call. Suggested fix: rename clan_khuzait_12 and clan_khuzait_19, then reset their loc keys (12 language rows and cache).
- Commit: none
- How checked: read the files. Nothing run.

## 21. Behaviour-tree "ran off the main mission thread" is logged as WARNING
- Confidence: 60% (accurate warning; only the log level is in question). Please double-check.
- Severity: log only
- Where: TAOM 2.0.41 installed and trunk 8b9cc1f74: Main/BehaviorTreeWrapper/BehaviorTreeMissionLogic.cs:56-58 (`ReportOffThread` calls LogWarning) and :212-217 (OffMainThread); Main/Features/AdvancedCombat/MissionThreadGuard.cs:56-58 (the message)
- What a player sees: nothing.
- Evidence: 25 WARNING lines in 12 of 30 sessions (OnAgentShootMissile 8, OnObjectUsed 6, OnObjectStoppedBeingUsed 6, OnAgentAlarmedStateChanged 3, OnAgentDismount 1, OnAgentPanicked 1). The message says a parked engine callback "is expected". Confidence: Verified (logs and code).
- Fix: not fixed: the code and docs do not agree that the line only reports an expected state. One reporter serves every BehaviorTreeMissionLogic site, OnAgentHit and OnScoreHit included (BehaviorTreeMissionLogic.cs:355, :426), and there an off-thread call is the #592 regression the message itself names. MissionThreadGuard's class comment calls the report how "a regression announces itself", and the changelog archive calls the OnAgentShootMissile line a "tripwire line" noted for later. Lowering it to INFO would also hide that signal; splitting it per site is the team's design call. Suggested fix: keep WARNING for OnAgentHit, OnScoreHit and any site a TAOM blow can reach; log the pure engine-notification sites (shoot, use, stop using, alarmed, mount, dismount, panic, flee, focus) at INFO, since the mission-end line already counts parked replays; pin the level per site in a test. No co-op path involved.
- Commit: none
- How checked: read the code, the tests that use the guard and the changelog archive. No change, no test.

# Part 2: not in the TAOM repo

## 31. Pelargir siege map in Custom Battle can take over 17 minutes to load
- Confidence: 65% (load time from your own story mission note, not measured in Custom Battle). Please double-check.
- Severity: broken feature (looks like a hang)
- Where: TAOM 2.0.41 Modules\TAOM\ModuleData\custom_battle_scenes.xml lines 84 to 92 (id on line 85, spot-checked); trunk Main/_Module/ModuleData/custom_battle_scenes.xml same lines
- What a player sees: Choosing "[Gondor] Pelargir" as a siege map in Custom Battle leaves the loading screen up for a very long time.
- Evidence: line 85 `id="taom_gondor_town_pelargir_forceatmo"`. TAOM's own docs/features/story-missions.md:359 says the Pelargir scene took over 17 minutes to load and swapped it for taom_gondor_town_dolamroth_forceatmo in the pelargir_defend mission. The Custom Battle list was not changed with it. Likely (TAOM's own measurement on the same scene, in a mission; not measured in Custom Battle).
- Fix: not fixed in a PR: report only, with the evidence above. Where to look: the scene itself in TAOM_Map\SceneObj\taom_gondor_town_pelargir_forceatmo (what makes it slow to load was not traced).
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test. Load time not measured.

## 32. Mirkwood elven arrows cost 43,251 a stack; Isengard arrows and bolts 15,727
- Confidence: 85% (computed with the game's own price formula, not seen in a shop). Please double-check.
- Severity: wrong data
- Where: LOTRLOME_Armory 2.0.41, ModuleData\LOTRLOME_items\LOTRAOM_weapons.xml (not in the repo)
- What a player sees: [Mirkwood] Elven Arrow I to III cost about 43,000 a stack in Mirkwood markets, more than five times the dearest vanilla shop arrows (piercing_arrows 7,746). [Isengard] Bow Arrow I and [Isengard] Heavy Bolts cost 15,727, twice vanilla's dearest. Same cause as the reported Noldor arrows: no value attribute, so the price formula prices them from thrust_damage and stack_amount.
- Evidence: wm_mirkwood_arrow_a01 id line 5156 (is_merchandise line 5163), _a02 line 5199 (5206), _a03 line 5242 (5249): tier 6.0, 43,251 each. wm_isengard_arrow_a01 line 5287 (5294), wm_isengard_bolt_a01 line 5330 (5332): tier 5.0, 15,727 each. Our 22 Nov report named the Mirkwood arrow only as a comparison. Verified (formula decompiled from 1.5.7 DefaultItemValueModel, and files; lines 5156 to 5332 spot-checked); price not seen in a shop.
- Fix: not fixed in a PR: the items are in the Armory, which is not in the repo. Suggested fix: add a value attribute on the line after is_merchandise, in the style of the Warg (value="1220"). The values for every overpriced Armory item (these and the Noldor arrows, Iron Hills bolts, shields, bows and barding from our earlier report) are in the attached PRICE-TABLE-armory.md (50 rows, one method per item type; arrows and bolts 5,500). That table replaces the 3,000 and 1,500 suggested here before.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 33. 32 towns and about 50 villages still show vanilla Calradian descriptions
- Confidence: 90% (verified in the map files). Please double-check.
- Severity: wrong data
- Where: TAOM_Map 2.0.41, ModuleData\settlements.xml (live install, not in the repo; lines 319, 1025, 2141, 4746, 4971, 6057 spot-checked)
- What a player sees: The encyclopedia page of Edoras says "Sargot is a common name for hills..." (with a doubled "the the"); Helm's Deep describes "Ocs Hall ... in the Vlandian kingdom"; Dale describes "The former Nord colony of Varcheg"; Barad-dûr describes "The frontier city of Danustica". The keys are vanilla 8 character keys, so the 12 other languages show the vanilla translation of the same Calradian text. Many villages and castle villages also carry Calradian wording (Aserai, Jarjara, Devseg, Khuzait, Perassic), several with the vanilla village name too (Tubilis, Fanab, Ain Baliq, Usek, Kiraz).
- Evidence: 32 of the 42 towns that have any text use a vanilla key (the other 10, Gondor EW1 to EW9 and Orthanc, have Middle-earth text). TAOM's own village repair (#1042, tools/oneoff/write_village_descriptions.py, 109 villages, `--check` passes) does not cover these. No TAOM C# overrides settlement text (searched trunk Main). Verified in files; that the encyclopedia shows Settlement.EncyclopediaText is Likely (standard 1.5.7 behaviour, not opened in game). Tables below. Sardol is in file 34.
- Fix: not fixed in the PR: TAOM_Map is a live install. Suggested fix: extend tools/oneoff/write_village_descriptions.py to towns. Give each settlement its own key `Settlements.Settlement.text.<id>` with a Middle-earth description, add the row to the 12 loc_settlements.xml files, drop the vanilla key. Until texts exist, `text="{=Settlements.Settlement.text.town_V1}Edoras, the court of the King of Rohan..."` or an empty text is better than Calradian lore.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

### Towns (32)
| line | settlement id | name | text key |
|---|---|---|---|
| 319 | town_EN1 | Galtrev | n9WMUuSp |
| 394 | town_EN2 | Carreglyn | wKMo2CUP |
| 469 | town_EN3 | Lhan Tarren | EJZLzhJk |
| 1025 | town_ES1 | Barad Dûr | xkkI7ejQ |
| 1113 | town_ES2 | Minas Morgul | ytBRXqt8 |
| 1188 | town_ES3 | Durthang | rGXdJybe |
| 1250 | town_ES4 | Seregost | WGtzBswe |
| 1325 | town_ES5 | Thaurband | 4ENGzVaC |
| 1400 | town_ES6 | Naerband | RL2vacqv |
| 4084 | town_A1 | Korb Taskral | 1ImuW3My |
| 4547 | town_A2 | Kes Marzûk | bnl7Va4c |
| 4609 | town_A3 | Hurum Kâna | NFIYqaab |
| 4671 | town_A4 | Chelkarâ | jXBnf2hQ |
| 4808 | town_A6 | Zajâna | uO4dxCl8 |
| 4896 | town_A7 | Chatâk | GChIoZdd |
| 6057 | town_V1 | Edoras | aOn6pa0D |
| 6132 | town_V2 | Helm's Deep | nhmBQIC4 |
| 6194 | town_V3 | Aldburg | Nx0qK2g6 |
| 6344 | town_V5 | Aldenburg | 79R6gi4l |
| 6406 | town_V6 | Langhold | R6YJsK89 |
| 6481 | town_V7 | Grimslade | 0cZmKO76 |
| 7000 | town_K1 | Sturlurtsa Khand | YNm9vaHw |
| 7088 | town_K2 | Lûrmsakun | 4UNOQQRa |
| 7176 | town_K3 | Ardûvar | bPDqyqkb |
| 7264 | town_K4 | Yanûk Anlê | Dxnj5JQE |
| 7705 | town_S1 | Dale | i2VkRLBg |
| 7767 | town_S2 | Stranding | Xom9DeXZ |
| 7829 | town_S3 | Eldby | pbHXtJ82 |
| 7891 | town_S4 | Liutburg | aTMypBiY |
| 7966 | town_S5 | Vargfell | F5HlQdgf |
| 4746 | town_A5 | Parzee | IIMWPVhU |
| 4971 | town_A8 | Sormedân | J4bgNT0r |

### Villages and castle villages (51)
| line | settlement id | name | text key |
|---|---|---|---|
| 3208 | castle_village_A1_1 | Tubilis | Settlements.Settlement.text.castle_village_A1_1 |
| 3221 | castle_village_A1_2 | Fanab | Settlements.Settlement.text.village_A1_3 |
| 3708 | castle_village_A2_1 | Sahel | Settlements.Settlement.text.castle_village_A2_1 |
| 3758 | castle_village_A3_1 | Ain Baliq | Settlements.Settlement.text.castle_village_A3_1 |
| 3771 | castle_village_A3_2 | Deir Hawa | Settlements.Settlement.text.castle_village_A3_2 |
| 3808 | castle_village_A4_1 | Medeni | Settlements.Settlement.text.castle_village_A4_1 |
| 3821 | castle_village_A4_2 | Qidnar | Settlements.Settlement.text.castle_village_A4_2 |
| 3858 | castle_village_A5_1 | Jamayeh | Settlements.Settlement.text.castle_village_A5_1 |
| 3871 | castle_village_A5_2 | Hunab | Settlements.Settlement.text.village_A3_2 |
| 3921 | castle_village_A6_2 | Lamesa | Settlements.Settlement.text.village_A2_1 |
| 3958 | castle_village_A7_1 | Uqba | Settlements.Settlement.text.castle_village_A7_1 |
| 3971 | castle_village_A7_2 | Bunqaz | Settlements.Settlement.text.castle_village_A7_2 |
| 4008 | castle_village_A8_1 | Tamnuh | Settlements.Settlement.text.castle_village_A8_1 |
| 4021 | castle_village_A8_2 | Kuqa | Settlements.Settlement.text.village_A4_3 |
| 4058 | castle_village_A9_1 | Barihal | Settlements.Settlement.text.castle_village_A9_1 |
| 4071 | castle_village_A9_2 | Wadar | Settlements.Settlement.text.village_A7_1 |
| 4596 | village_A2_3 | Hoqqa | Settlements.Settlement.text.village_A2_3 |
| 4645 | village_A3_1 | Abba | Settlements.Settlement.text.village_A3_1 |
| 4658 | village_A3_3 | Bir Seif | Settlements.Settlement.text.village_A3_3 |
| 4707 | village_A4_1 | Qablab | Settlements.Settlement.text.village_A4_1 |
| 4720 | village_A4_2 | Mussum | Settlements.Settlement.text.village_A4_2 |
| 4782 | village_A5_1 | Mahloul | Settlements.Settlement.text.village_A5_1 |
| 4844 | village_A6_1 | Mijayit | Settlements.Settlement.text.village_A6_1 |
| 4857 | village_A6_2 | Hamoshawat | Settlements.Settlement.text.village_A6_2 |
| 4870 | village_A6_3 | Jahasim | Settlements.Settlement.text.village_A6_3 |
| 4883 | village_A6_4 | Nahlan | Settlements.Settlement.text.village_A6_4 |
| 4932 | village_A7_2 | Bait Hatif | Settlements.Settlement.text.village_A7_2 |
| 4945 | village_A7_3 | Mabwaz | Settlements.Settlement.text.village_A7_3 |
| 4958 | village_A7_4 | Zalm | Settlements.Settlement.text.village_A7_4 |
| 5007 | village_A8_1 | Ezbet Nahul | Settlements.Settlement.text.village_A8_1 |
| 5981 | castle_village_V6_1 | Caleus | Settlements.Settlement.text.castle_village_V6_1 |
| 6106 | village_V1_2 | Etirfurd | Settlements.Settlement.text.village_V1_2 |
| 6583 | castle_village_K1_1 | Usek | Settlements.Settlement.text.castle_village_K1_1 |
| 6596 | castle_village_K1_2 | Esme | Settlements.Settlement.text.castle_village_K1_2 |
| 6659 | castle_village_K2_2 | Kamshar | Settlements.Settlement.text.village_K5_1 |
| 6709 | castle_village_K3_1 | Hakkun | Settlements.Settlement.text.castle_village_K3_1 |
| 6722 | castle_village_K3_2 | Kiraz | Settlements.Settlement.text.village_K2_4 |
| 6772 | castle_village_K4_1 | Tepes | Settlements.Settlement.text.castle_village_K4_1 |
| 6835 | castle_village_K5_1 | Khimli | Settlements.Settlement.text.castle_village_K5_1 |
| 6848 | castle_village_K5_2 | Omrotok | Settlements.Settlement.text.village_K4_1 |
| 6898 | castle_village_K6_1 | Dinar | Settlements.Settlement.text.castle_village_K6_1 |
| 6911 | castle_village_K6_2 | Karahalli | Settlements.Settlement.text.castle_village_K6_2 |
| 6961 | castle_village_K7_1 | Simira | Settlements.Settlement.text.castle_village_K7_1 |
| 6974 | castle_village_K7_2 | Kohi Ajik | Settlements.Settlement.text.village_K6_4 |
| 7075 | village_K1_4 | Asalig | Settlements.Settlement.text.village_K1_4 |
| 7124 | village_K2_1 | Karakalat | Settlements.Settlement.text.village_K2_1 |
| 7137 | village_K2_2 | Tismil | Settlements.Settlement.text.village_K2_2 |
| 7238 | village_K3_3 | Mazen | Settlements.Settlement.text.village_K3_3 |
| 7313 | village_K4_2 | Ransam | Settlements.Settlement.text.village_K4_2 |
| 7326 | village_K4_3 | Mivanjan | Settlements.Settlement.text.village_K4_3 |
| 7339 | village_K4_4 | Urunjan | Settlements.Settlement.text.village_K4_4 |

## 34. Sardol borrows South Pelennor Fields' text key, so that village shows Calradian text in 12 languages
- Confidence: 90% (verified in the map and language files). Please double-check.
- Severity: wrong data
- Where: TAOM_Map 2.0.41 ModuleData\settlements.xml lines 2141 (castle_village_EW6_2) and 2444 (village_EW1_3), both spot-checked; the row at line 203 of all 12 Languages\<LANG>\loc_settlements.xml
- What a player sees: In English Sardol reads "Nideon sits on a low-lying meander of the Zeos river...". In every other language South Pelennor Fields shows the translated Nideon text instead of "A Gondor village.", because both use `Settlements.Settlement.text.village_EW1_3`.
- Evidence: Line 2141 `text="{=Settlements.Settlement.text.village_EW1_3}Nideon sits on..."`, line 2444 `{=Settlements.Settlement.text.village_EW1_3}A Gondor village.`. The only same key, different text pair in TAOM, TAOM_Map and Armory. 10 more castle villages borrow another village's key (castle_village_A1_2, A5_2, A6_2, A8_2, A9_2, K2_2, K3_2, K4_2, K5_2, K7_2); harmless today because no settlement owns those keys. Verified.
- Fix: not fixed in the PR: live install. Suggested fix: line 2141 text becomes `{=Settlements.Settlement.text.castle_village_EW6_2}Sardol is a farming village of Gondor in the hills of Lebennin.` (TAOM picks the wording) with a new row of that id in the 12 loc files. Reset row 203 `Settlements.Settlement.text.village_EW1_3` in the 12 loc files to `A Gondor village.` for the translator, and evict that key from tools/translation_cache/*.json (that part is in the repo).
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 35. Five Gundabad shields and maces carry culture Culture.mordor, so Mordor towns sell them
- Confidence: 55% (may be on purpose). Please double-check.
- Severity: wrong data (Likely; may be on purpose)
- Where: LOTRLOME_Armory 2.0.41, LOTRLOME_items\LOTRAOM_shields.xml lines 6635, 6677, 6719; LOTRAOM_weapons.xml lines 7390, 7409 (lines 6635 and 7390 spot-checked)
- What a player sees: [Gundabad] Medium Shield I to III and [Gundabad] Orc 2H Mace I and II are sold in Mordor towns and not in Gundabad. The other 154 Gundabad tagged items use Culture.gundabad.
- Evidence: CultureMarketplace stocks an item by its culture attribute first (CultureItemPoolService.ClassifyEffectiveCulture). Also gundabad_tournament_sparring_shield (shields line 7052) and 3 starter shields in mordor\starter_kit.xml lines 58, 74, 120 (not merchandise). Maybe on purpose, since Dol Guldur troops use the shield. Ladder bows with a vanilla donor culture (Lothlórien bows rivendell or mirkwood, Rohan bows khuzait, crossbows vlandia) are cosmetic only. Verified in files and code; which culture TAOM wants is Unverified.
- Fix: not fixed in the PR: the attribute is in the Armory and which realm sells them is the team's choice. Suggested fix: `        culture="Culture.gundabad"` on the five lines, or keep Mordor and add routing rows in Main/_Module/ModuleData/culture_marketplace/culture_marketplace_config.xml (Routing at trunk line 129): `    <Item id="wm_gundabad_shield_a01" cultures="gundabad,mordor" />` for each item.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 36. The 25 named hero weapons (and Sauron's helmet) are merchandise in their XML
- Confidence: 75% (matters only when Armour Acquisition is off). Please double-check.
- Severity: wrong data (only when Armour Acquisition is off)
- Where: LOTRLOME_Armory 2.0.41, LOTRLOME_items\LOTRAOM_weapons.xml (anduril line 27, witchking_sword line 107, wm_tuors_axe line 9338, spot-checked; wm_sauron_mace line 8); mordor\head_armors.xml line 656 (sauron_helmet, spot-checked); crafting pieces in LOTRLOME_crafting_pieces.xml and crafting_templates.xslt
- What a player sees: Nothing with the default settings: the armour acquisition gate keeps named weapons out of shops. With "Enable Armour Acquisition" off in MCM the gate falls back to the XML flag (ArmourMarketplaceGate.ForTown) and Andúril, Glamdring, the Witch-king's sword and others can be stocked. Separately, all pieces of the 25 named weapons are visible, usable smithing parts, so a player can smith copies.
- Evidence: All 25 CraftedItem ids in the NamedWeapons list of armour_acquisition_config.xml have is_merchandise="true". sauron_helmet has no is_merchandise attribute, which the engine also treats as merchandise (ItemObject). Pieces, for example wm_anduril_sword_blade and wm_glamdring_blade, are listed in crafting_templates.xslt and not hidden. Verified in code; not tested with the switch off.
- Fix: not fixed in the PR: the items are in the Armory. Suggested fix: `is_merchandise="false"` on the 25 named weapons and on sauron_helmet (the ladder awards them by id, so that does not block it). For the smithing copies, add `is_hidden="true"` to the named weapons' pieces, if TAOM does not want smithed copies (maybe on purpose).
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 37. umbar_elite_root000 upgrades into a troop with less armour
- Confidence: 85% (verified with your own validator). Please double-check.
- Severity: wrong data (balance)
- Where: TAOM 2.0.41 and trunk Main/_Module/ModuleData/troops/troops_umbar.xml lines 944 (umbar_elite_root000, spot-checked) and 1241 (umbar_elite_root0000)
- What a player sees: Upgrading the elite Umbar troop lowers armour from 154 to 146 (Body 83 to 60, Leg 23 to 14).
- Evidence: Reported by TAOM's own validator as UPGRADE_ARMOUR_REGRESSION (the one real outlier of 3; the Lindon and Rivendell royal guard 214 to 214 cases are rounding). TAOM's tools/fix_upgrade_armour_regressions.py (dry run) would give the Númenórean a vanilla Aserai body (aserai_scale_armor_on_chain) and strapped_mail_chausses, which does not fit the lore, so it was not applied. Verified.
- Fix: not fixed in the PR: needs a design decision (which Númenórean body armour). Suggested fix: pick Armory Númenórean or Umbar body and leg pieces for umbar_elite_root0000 with at least the parent's values, or lower the parent's. Then rerun tools/validate_moduledata.py.
- Commit: none
- How checked: Validator run on trunk 8b9cc1f74 (0 errors, 1915 warnings). Nothing applied.

## 57. Dual Wield 0.10.8: troop data that does not fit TAOM, and ideas for built-in support
- Confidence: 80% (verified in Dual Wield's files; some points are questions). Please double-check.
Dual Wield, not in the TAOM repo. For the Dual Wield part of the TAOM team.


- Severity: wrong data (P3a to P3c), cosmetic (P3e, P3f), question (P3d)
- Where: Modules\DualWield 0.10.8, ModuleData\troops\taom\troops_dualwield.xml and troops_dualwield.xslt (installed 20 Nov 2026). Dual Wield is TAOM's own mod but is not in haterade22/TAOM, so this is report only.
- What a player sees: a Rohan troop that can never be reached, an upgrade that gives no level, a Rhûn troop that skips a tier and is weaker than the one it skips, and name tags that differ from the rest of TAOM.
- Evidence: scripts over TAOM 2.0.41 data. Every item, culture, race, face template and civilian set id resolves, no parent gets more than two upgrade targets, and every new troop has its parent's culture and race. Line numbers are from troops_dualwield.xml, checked 23 Nov 2026. Confidence: Verified in files unless stated.
- Fix: not in the PR, because Dual Wield is not in the TAOM repo. A suggested fix is under each point. Preferred place: TAOM's own troop files (D1).
- What we do meanwhile: the TAOM AIO Compatibility Patch carries a small band-aid for P3a, P3b, P3c, P3e and P3f. It only changes values that still match 0.10.8, so it switches itself off for each point the team fixes. P3d stays as it is.
- Commit: none
- How checked: read only, no in-game test.

### P3. Troop data
- P3a. dw_rohan_twinsword_captain (level 36, "[Rohan] Twin-Sword Captain", line 571) is in the file, but no troop upgrades into it. The XSLT has 26 upgrade edges for 27 troops, and dw_rohan_twinsword_rider (line 498) has no upgrade_targets, so the player can never get the Captain. Suggested fix: add the upgrade Twin-Sword Rider to Twin-Sword Captain at the top of the Rider line (with P3b the Rider is 31 and the Captain 36). If it is on purpose, please say so.
- P3b. dw_rohan_twinsword_rider (line 498) is level 26, the same as its parent rohan_edoras_golden_hall_elite_rider ("[Rohan] King's Lancer", 26; XSLT lines 24 and 25), while the other branch, Eorlingas Rider, is 31. The upgrade gives no level. Suggested fix: `level="31"` on line 498. The skills can stay; they are already above the Eorlingas Rider's (One Handed 155 against 140, Polearm 220 against 165).
- P3c. dw_rhun_shadowblade (line 1302) is level 21, upgraded from sagarun_deckhand (level 11; XSLT lines 54 and 55), so it skips tier 3. The other branch, sagarun_watchman, is level 16. Its skills (sum 296, One Handed 60) are below the level 16 watchman's (330, 70). Suggested fix: `level="16"` with the skills kept, or keep 21 and raise the skills.
- P3d. Question for the team: five women soldier troops. Imladris Blade-Maiden (line 1096), Twin-Blade Huntress of Mirkwood (1069), Daughter of Durin of Erebor (1148), Wolf-Maiden of Dunland (1195) and Shieldmaiden of Edoras (1240, sword and shield, not a dual wielder) are is_female="true", but TAOM 2.0.41 has no female soldier troop. Rohan's shieldmaidens have a basis in the books; dwarf women warriors and elf women in the ranks are invented. Do you want them in TAOM's troop trees? They stay in the trees as they are until you decide; the AIO doesn't touch them.
- P3e. Name tags differ from TAOM's:
  - `[Rhun]` instead of TAOM's `[Rhûn]`: dw_rhun_iron_twinblade (line 746) and dw_rhun_shadowblade (line 1302).
  - `[Goblin Town]` instead of TAOM's `[Goblin]`: dw_goblintown_twinblade (line 1015).
  - `[Harad] Twin-Scimitar` (dw_harad_twinscimitar, line 692): its whole tree is tagged `[Harwan]` in TAOM (all eleven harad_reg_* troops).
  - "Uruk-hai" (dw_isengard_urukhai_twinblade, line 287): TAOM's Isengard troops say "Uruk-Hai".
  - All 27 names use `{=!}` (no translation key), while TAOM's use `{=aom_..._name}`.
  - Suggested fix: TAOM's tags, and a string id per name if you want them translatable.
- P3f. Skill shape (cosmetic, maybe on purpose): the dual wielders carry high Two Handed and Polearm (for example the Mirkwood Twin-Blade Warden has 320 and 340) that they never use, and lower Athletics than their sister troops (205 against 290). Several troops (Blade-Dancers, Edoras Twin-Sword, Rider) have one roster in five with sword and shield, so not every spawn dual wields; this looks deliberate. Meanwhile the AIO band-aid brings Two Handed and Polearm down to the sister troop's values and raises Athletics to it, keeping any skill a roster's weapon uses; each change applies only while the value is still the 0.10.8 one. Suggested fix: the same in Dual Wield's file.
- Risk: changing levels or adding the Captain edge changes existing saves' trees only for future upgrades, since the ids are unchanged.

### D1. Built-in support idea (draft, nothing written)
- D1. Move the 26 upgrade edges and 27 troops into TAOM's own troops_*.xml files (each culture file), with TAOM string ids (`{=aom_..._name}`), TAOM name tags and the P3 fixes. Dual Wield keeps only the fighting code. Why: the names become translatable, tools/validate_moduledata.py checks the troops, and the troops also exist on a co-op dedicated server (P2 below). Cost: the "NPC Dual-Wield Troops" MCM switch would need a TAOM-side setting, or it would go away.
- D2 and D3 are co-op only and are listed below.

### Suggestions: dual wielders for cultures that have none yet (ideas for later)
Dual Wield gives one or two dual-wield troops to 16 cultures. Three cultures that TAOM has or plans have none. These are ideas for the TAOM team, each with a lore reason and only TAOM's own gear. The ids and names are drafts.

- Dorwinion (playable now). TAOM's tree has no one-handed specialist: every infantry troop carries a spear, a sword and a shield. Draft: "[Dorwinion] Redwater Blade-Dancer", level 26, a second upgrade of dorwinion_redwater_guard (21), next to the Veteran Redwater Guard. Two of the tree's own Dale swords (dale_sword_a, dale_sword_b) and the Veteran Redwater Guard's mariner armour. Lore: river-folk who guard wine barges on the Running River fight in close quarters on decks, where a spear is clumsy. Not from the books; invented in TAOM's style.
- Khand (when TAOM makes it playable). Draft: "[Khand] Variag Twin-Axe". Lore: the Variags of Khand fought for Mordor at the Pelennor (The Return of the King); the books name them but give no weapons, so twin axes or curved blades are an invention in the spirit of TAOM's Easterling and Haradrim gear.
- Angmar (when TAOM makes it playable). Draft: a Hill-man of Rhudaur or an orc of Angmar with two blades, for example "[Angmar] Hill-man Reaver". Lore: Angmar's armies were orcs and hill-men from Rhudaur (Appendix A); weapons not named, so invented.
- Each new troop would follow the same rules as the existing ones: the parent's culture and race, a level one tier above the parent, and at most two upgrades per parent. Skills shaped like the sister troop (One Handed high, Two Handed and Polearm at the sister's values, Athletics at the sister's). TAOM name tags and string ids ({=aom_..._name}).
- Where: TAOM's own troop files, as in D1, or Dual Wield's troop file if D1 is not taken.

### Co-op, later (single player only for now)
- P2: on the co-op dedicated server, Dual Wield's troops do not exist. Its code does not run there (SubModule tag DedicatedServerType none), and the server never opens the troop files. D1 would fix this too.
- P4: Dual Wield's animations are missing on the dedicated server. Harmless in practice: about 10 seconds of log warnings at start.
- P5: Dual Wield's settings are per PC, not per campaign.
- D2: a server-safe start for Dual Wield's troop loader (DedicatedServerType "all", running only DwTroopSetLoader.Sync).
- D3: Dual Wield's settings in TAOM.CoopCompat's settings handshake.

## 58. Two castles share the name "Methedras" (castle_EN5 and castle_MM5)
- Confidence: 90% (verified in the map files). Please double-check.
- Severity: wrong data
- Where: TAOM_Map 2.0.41, ModuleData\settlements.xml line 119 (castle_EN5) and line 15052 (castle_MM5); ModuleData\Languages\*\loc_settlements.xml lines 23 and 1273 in all 12 language folders. TAOM_Map is not in the TAOM repo (live install only).
- What a player sees: two different castles called "Methedras" in the encyclopedia, the clan and kingdom screens, the fief lists and map tooltips. castle_EN5 is a Dunland castle (clan_empire_north_9) near Orthanc; castle_MM5 is a Misty Mountain orc castle (clan_mistymountainorcs_4) further south-east. Players cannot tell them apart in lists.
- Evidence: settlements.xml line 119 `name="{=Settlements.Settlement.name.castle_EN5}Methedras"`, posX 586.6, posY 809.9; line 15052 `name="{=Settlements.Settlement.name.castle_MM5}Methedras"`, posX 692.1, posY 947.6. Orthanc (town_isengard) is at posX 633.4, posY 831.2, so castle_EN5 sits at the southern end of the Misty Mountains, where Mount Methedras is. Every language file repeats the same name for both ids (for example CNs: 美塞德拉斯 for both). This is the only duplicate display name among the 280 towns and castles (script count of settlements.xml). Verified in files.
- Still in trunk 2.0.42: not in repo.
- Confidence: Verified (files). Whether the double name is on purpose: Unverified (it looks like a copy).
- Suggested fix: give castle_MM5 its own name and loc row, keeping castle_EN5 as "Methedras" (it is the one at the real mountain). For example `name="{=Settlements.Settlement.name.castle_MM5}Methedras Hold"` and the matching `text` in each Languages\*\loc_settlements.xml line 1273, or any name the team prefers from its own lore.
- Fixable in the repo: no (TAOM_Map, live install only).
- Commit: none
- How checked: read only, installed TAOM_Map 2.0.41 files on 23 Nov 2026. No in-game test.
- Note: both castles are on the Evil side (castle_EN5 Dunland, castle_MM5 Misty Mountain orcs). Not fixable in the PR: TAOM_Map is not in the repo. The other duplicate names found by the troop audit (Linhir, Tumladen) are in the TAOM_Map settlement-names file.

## 39. Armory item names: typos, duplicate numerals, draft names and lower case
- Confidence: 85% (verified in the Armory files). Please double-check.
- Severity: cosmetic
- Where: LOTRLOME_Armory 2.0.41 (not in the repo); files and lines below
- What a player sees: Names such as "Palace Guard Glovves", two different "[Erebor] Metal Shield I", two "Uruk-Hai Elite Plate Armor IV", "Captain's helmet II" and draft names like "[Gondor] Sword Elite B" in shops.
- Evidence: Typos: mirkwood_gloves `[Mirkwood] Palace Guard Glovves`, mirkwood\arm_armors.xml line 9 and Languages\loc_mirkwood.xml line 7. Five "Nâzgul's" items (should be Nazgûl's): mordor\arm_armors.xml:227, body_armors.xml:871, head_armors.xml:584, leg_armors.xml:227, shoulder_armors.xml:254; loc_mordor.xml:18, 63, 97, 121, 140. cts_rohan_helmet_captain `[Rohan] Captain's helmet II`, rohan\head_armors.xml line 439. Duplicate numerals: sm_dwarf_erebor_shield_metal_a2 and _b2 are "[Erebor] Metal Shield I" and "II" (LOTRAOM_shields.xml 3362, 3404; loc_LOTRAOM_shields.xml 86, 87) while _a to _d are I to IV and _c2, _d2 are VII and VIII (a2 weighs 2.0; prices 5,131 and 32,501); sk_uruk_hai_plate_elite_a3 and _b2 are both "Armor IV" (isengard\body_armors.xml 484, 534; loc_isengard.xml 50, 52; the series is a1 I, a2 II, a3 IV, b1 III, b2 IV, b3 V, so a3 should be VI, Likely). Leftover "Dont Use": loc_mordor.xml line 104 for sauron_helmet, an item nothing equips (spot-checked). Draft names English players may see while the loc row holds the finished ladder name: table below. Which side is newer is Unverified; the loc side matches the rest of the Gondor ladder. NOTE: we are not sure which one English players see: the engine code suggests the inline text, but the rgl log of 22 Nov shows the English loc files being opened. Change both places and the question does not matter. Verified in files.
- Fix: not fixed in the PR: the files are in the Armory. Suggested fix, inline name and English loc row together (then reset the 12 language rows for the translator): `name="{=aom_mirkwood_gloves_name}[Mirkwood] Palace Guard Gloves"`; `...metal_a2_name}[Erebor] Metal Shield V"`; `...metal_b2_name}[Erebor] Metal Shield VI"`; `...plate_elite_a3_name}[Isengard] Uruk-Hai Elite Plate Armor VI"`; `[Rohan] Captain's Helmet II`; Nazgûl's for the five; `<string id="aom_sauron_helmet_name" text="[Mordor] Sauron Helmet"/>`; for the draft names copy the loc text into the inline name, for example `name="{=aom_wm_gondor_sword_a07_name}[Gondor] One-Handed Sword VII"`. The TAOM clone names of the Glovves item (armour_band_clones.xml 101, 122) are fixed in the PR.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

### Inline name differs from the English loc row
| item file:line | key | XML name | English loc row | loc file:line |
|---|---|---|---|---|
| LOTRLOME_items\LOTRAOM_shields.xml:781 | aom_gond_shield_three_green_name | '[Gondor] Cavalry Shield' | '[Gondor] Shield III - Pinnath Gelin' | loc_LOTRAOM_shields.xml:33 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1327 | aom_wm_gondor_spear_a_name | '[Gondor] Fountain Guard Spear' | '[Gondor] Spear III' | loc_LOTRAOM_weapons.xml:70 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1346 | aom_wm_gondor_spear_b_name | '[Gondor] Heavy Spear' | '[Gondor] Spear IV' | loc_LOTRAOM_weapons.xml:71 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1527 | aom_wm_swan_knight_sworda_name | '[Gondor] Swan Knight Sword A' | '[Gondor] Swan Knight Sword I' | loc_LOTRAOM_weapons.xml:78 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1553 | aom_wm_swan_knight_swordb_name | '[Gondor] Swan Knight Sword B' | '[Gondor] Swan Knight Sword II' | loc_LOTRAOM_weapons.xml:79 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1579 | aom_wm_gondor_sword_a07_name | '[Gondor] Sword Elite B' | '[Gondor] One-Handed Sword VII' | loc_LOTRAOM_weapons.xml:83 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1605 | aom_wm_gondor_sword_a03_name | '[Gondor] Sword Elite A' | '[Gondor] One-Handed Sword III' | loc_LOTRAOM_weapons.xml:87 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1631 | aom_wm_gondor_sword_a02_name | '[Gondor] Sword Medium B' | '[Gondor] One-Handed Sword II' | loc_LOTRAOM_weapons.xml:88 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1657 | aom_wm_gondor_sword_a01_name | '[Gondor] Sword Medium A' | '[Gondor] One-Handed Sword I' | loc_LOTRAOM_weapons.xml:89 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1816 | aom_wm_gondor_lamedon_1h_sword_a_name | '[Gondor] Lamedon Bastard Medium Sword' | '[Gondor] Lamedon One-Handed Sword I' | loc_LOTRAOM_weapons.xml:96 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1868 | aom_wm_gondor_lamedon_2h_sword_a_name | '[Gondor] Lamedon Greatsword Heavy A' | '[Gondor] Lamedon Two-Handed Sword I' | loc_LOTRAOM_weapons.xml:97 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1894 | aom_wm_gondor_lamedon_2h_sword_b_name | '[Gondor] Lamedon Greatsword Heavy B' | '[Gondor] Lamedon Two-Handed Sword II' | loc_LOTRAOM_weapons.xml:98 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1920 | aom_wm_gondor_lamedon_2h_sword_c_name | '[Gondor] Lamedon Greatsword Elite A' | '[Gondor] Lamedon Two-Handed Sword III' | loc_LOTRAOM_weapons.xml:99 |
| LOTRLOME_items\LOTRAOM_weapons.xml:1946 | aom_wm_gondor_lamedon_2h_sword_e_name | '[Gondor] Lamedon Greatsword Elite B' | '[Gondor] Lamedon Two-Handed Sword V' | loc_LOTRAOM_weapons.xml:101 |
| LOTRLOME_items\mordor\head_armors.xml:657 | aom_sauron_helmet_name | '[Mordor] Sauron Helmet' | '[Mordor] Sauron Helmet - Dont Use' | loc_mordor.xml:104 |

## 40. Armory smithing part names: typos, doubled words, double spaces
- Confidence: 85% (verified in the Armory files). Please double-check.
- Severity: cosmetic
- Where: LOTRLOME_Armory 2.0.41, LOTRLOME_crafting_pieces.xml and Languages\loc_LOTRLOME_crafting_pieces.xml (lines 3959, 7688, 7966, 9016, 11264, 17953 spot-checked)
- What a player sees: Smithing part names like "Pelagir A01 Sword Blade", "Aranruth Sword Pomme Topaz", "Rivendell Sword Sword Blade", "Iron Hills  Sword Blade A".
- Evidence: "Pelagir" (should be Pelargir) on 10 pieces: lines 3959, 3991, 4011, 4030, 4049, 4081, 4101, 4120, 4482, 4517; loc lines 184 to 191, 203, 204. "Pomme" (Pommel) line 7688, loc 339. Doubled words "Rivendell Sword Sword Blade" and "Rivendell Spear Spear I Blade...": lines 7966, 8555, 8616, 8677, 18000, 18013; loc 350, 375, 377, 379. Double spaces: lines 9016, 9464, 9500, 9612, 9631, 17953; loc 391, 401, 402, 407, 408. Trailing space line 11264 "Gundabad Orc Spear Handle II " (loc row clean). Five inline names also differ from the loc row: aom_wm_swan_knight_sword_a_blade_name (3238, loc 128), aom_wm_gondor_sword_a01/a02/a03/a07_blade_name (3598, 3689, 3779, 3869; loc 144, 148, 152, 168). Verified.
- Fix: not fixed in the PR: Armory. Suggested fix, inline name and English loc row together: `Pelargir A01 Sword Blade`, `Aranruth Sword Pommel Topaz`, `Rivendell Sword Blade`, `Rivendell Spear I Blade` (and the Silver, Black and (Starter) variants), one space in the Iron Hills names, no trailing space. The TAOM placeholder copies of the Iron Hills names are fixed in the PR.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 41. Culture tags that differ from the rest of their culture, and about 170 items with no tag
- Confidence: 75% (some tags may be on purpose). Please double-check.
- Severity: cosmetic
- Where: LOTRLOME_Armory 2.0.41 (not in the repo); generators in the repo: tools/generate_starter_armor.py, generate_starter_kit.py, ranged_ladder.py, generate_placeholder_weapons.py
- What a player sees: Items of one culture sort under two tags, and many names have no tag at all.
- Evidence: "[Rivendell]" on 12 items while 239 Culture.rivendell items use "[Noldor]": the 9 merchandise wm_rivendell_sword/spear items (LOTRAOM_weapons.xml name lines 6121, 6147, 6173, 6199, 6225, 6251, 6277, 6295, 6313), starter_wm_rivendell_sword_a01 and _spear_a01 (rivendell\starter_kit.xml lines 10, 26), and one TAOM placeholder. "[Lórien]" on 12 items while 77 use "[Lothlórien]": wm_aranruth_sword, _ruby, _topaz, wm_galadriel_sword (name lines 5991, 6017, 6043, 6069), ladder_lothlorien_bow_t2 to _t9 (lothlorien\ranged_ladder.xml lines 10 to 66). "[Harwan]" (115) and "[Harad]" (27) are both on Culture.aserai: maybe on purpose. No tag at all on about 170 items: 78 starter armours (72 like "Rohan Infantry Armor", rohan\starter_armors.xml line 38; "Rhun Infantry Boots" without û, rhun\starter_armors.xml line 44; Gondor's 6 named ones, gondor\starter_armors.xml lines 18 to 57), 20 starter kit copies of vanilla items ("Iron Saber (Starter)" rhun\starter_kit.xml 18, "Jereed (Starter)" harad 26, "Arrows (Starter)" mercenary 10), 44 ranged ladder bows and crossbows (vanilla donor names such as "Arbalest III", "Steppe Recurve Bow I" to VII, rohan\ranged_ladder.xml lines 10 to 58), mounts "War Ram", "Highland War Ram", "Great Elk" (LOTRAOM_horses.xml 1038, 1062, 1274), cave troll gear (troll\*.xml line 6) and the 14 Lord's Materials (taom_lords_materials.xml lines 25 to 79; maybe on purpose for both). Verified.
- Fix: not fixed in the PR: Armory live files, and a tag changes the name in all 12 languages (translator run). Suggested fix: `        name="{=aom_wm_rivendell_sword_a01_name}[Noldor] Rivendell Sword I"`; `        name="{=aom_wm_aranruth_sword_name}[Lothlórien] Aranruth Sword"`; add the tag in the generators' name templates, for example `[Rohan] Infantry Armour`, `[Gondor] Arbalest III`.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 42. Different items share one name
- Confidence: 85% (verified in the Armory files). Please double-check.
- Severity: cosmetic
- Where: LOTRLOME_Armory 2.0.41 (not in the repo); trunk Main/_Module/ModuleData/armour_band_clones/armour_band_clones.xml (generated)
- What a player sees: Two entries look the same in a list but differ in stats or price.
- Evidence: Shop duplicates: Erebor Metal Shield I and II and Uruk-Hai Elite Plate Armor IV (file 39); "[Arnor] Noble Pauldrons" on _a, _b (831) and _c (2,548) (arnor\shoulder_armors.xml lines 233, 252, 271); "[Rohan] Chainmail Armour I - Brown" on cts_rohan_armor11 and cts_rohan_armor333 and "- Green" on cts_rohan_armor1111 and cts_rohan_armor3333 (rohan\body_armors.xml lines 534, 606, 678, 774; same stats, different class rows). A shop bow and a troop ladder bow with one name: "[Gondor] Ithilien Bow III" is wm_ithilien_bow_c (42,981, LOTRAOM_weapons.xml line 3749) and ladder_blackroot_bow_t3 (311, gondor\ranged_ladder.xml line 42); likewise "[Noldor] Longbow II/III", "[Harad] Composite Bow II", "[Erebor] Shortbow II", "[Gondor] Steel Shortbow II"; the Blackroot ladder reuses the Ithilien name for t3, t7 and t8. The 32 troop armour band clones reuse their source's name (taom_band_lt_mirkwood_body has armour 8 under the name of the original with 48); by design. Verified.
- Fix: not fixed in the PR: Armory names, and the ladder names come from generators the PR does not touch. Suggested fix: number the shop duplicates apart; give each ladder line its own name through the `names` override in tools/ranged_ladders.json; band clones could add "Light" or "Medium" to the name if the team wants them told apart.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 43. Lothlórien armour has no file in any of the 12 languages
- Confidence: 90% (verified in the language folders). Please double-check.
- Severity: cosmetic (untranslated)
- Where: LOTRLOME_Armory 2.0.41, ModuleData\Languages\language_data.xml, loc_lothlorien.xml and the 12 <LANG>\language_data.xml
- What a player sees: All 85 Lothlórien items (head, body, shoulder, arm, leg armour and the ranged ladder bows) show English in every language.
- Evidence: The top manifest lists 23 files including loc_lothlorien.xml (85 rows); each of the 12 language manifests lists 22 files without it and no <LANG>\loc_lothlorien.xml exists. English 4,824 rows, every language 4,739. tools/check_external_loc_coverage.py counts these rows as untranslated inside its baseline (about 85 per language), so it passes and nothing flags it. Verified. Ids: ladder_lothlorien_bow_t2, ladder_lothlorien_bow_t3, ladder_lothlorien_bow_t4, ladder_lothlorien_bow_t5, ladder_lothlorien_bow_t6, ladder_lothlorien_bow_t7, ladder_lothlorien_bow_t8, ladder_lothlorien_bow_t9, aom_sk_ll_gl_bracer_heavy_a_name, aom_sk_ll_gl_bracer_med_a_name, aom_sk_ll_gl_chest_elite_a_name, aom_sk_ll_gl_chest_elite_b_name, aom_sk_ll_gl_chest_heavy_a_name, aom_sk_ll_gl_chest_heavy_b_name, aom_sk_ll_gl_chest_med_a_name, aom_sk_ll_gl_chest_med_b_name, aom_sk_ll_gl_galad_bracer_a_name, aom_sk_ll_gl_galad_chest_a_name, aom_sk_ll_gl_galad_crown_a_name, aom_sk_ll_gl_galad_pauld_a_name, aom_sk_ll_gl_grvs_heavy_a_name, aom_sk_ll_gl_grvs_med_a_name, aom_sk_ll_gl_helmet_heavy_a_name, aom_sk_ll_gl_helmet_heavy_b_name, aom_sk_ll_gl_helmet_heavy_c_name, aom_sk_ll_gl_helmet_med_a_name, aom_sk_ll_gl_helmet_med_b_name, aom_sk_ll_gl_helmet_med_c_name, aom_sk_ll_gl_pauld_cape_heavy_a_name, aom_sk_ll_gl_pauld_heavy_a_name, aom_sk_ll_gl_pauld_med_a_name, aom_sk_ll_ml_bracer_heavy_a_name, aom_sk_ll_ml_bracer_med_a_name, aom_sk_ll_ml_chest_elite_a_name, aom_sk_ll_ml_chest_elite_b_name, aom_sk_ll_ml_chest_heavy_a_name, aom_sk_ll_ml_chest_heavy_b_name, aom_sk_ll_ml_grvs_heavy_a_name, aom_sk_ll_ml_grvs_med_a_name, aom_sk_ll_ml_helmet_elite_a_name, aom_sk_ll_ml_helmet_elite_b_name, aom_sk_ll_ml_helmet_elite_c_name, aom_sk_ll_ml_helmet_heavy_a_name, aom_sk_ll_ml_helmet_heavy_b_name, aom_sk_ll_ml_helmet_heavy_c_name, aom_sk_ll_ml_helmet_med_a_name, aom_sk_ll_ml_helmet_med_b_name, aom_sk_ll_ml_helmet_med_c_name, aom_sk_ll_ml_pauld_cape_heavy_a_name, aom_sk_ll_ml_pauld_heavy_a_name, aom_sk_ll_ml_pauld_med_a_name, aom_sk_ll_mw_bracer_heavy_a_name, aom_sk_ll_mw_bracer_med_a_name, aom_sk_ll_mw_chest_heavy_a_name, aom_sk_ll_mw_chest_heavy_b_name, aom_sk_ll_mw_chest_med_a_name, aom_sk_ll_mw_chest_med_b_name, aom_sk_ll_mw_grvs_heavy_a_name, aom_sk_ll_mw_grvs_med_a_name, aom_sk_ll_mw_helmet_arc_heavy_a_name, aom_sk_ll_mw_helmet_arc_heavy_b_name, aom_sk_ll_mw_helmet_arc_med_a_name, aom_sk_ll_mw_helmet_arc_med_b_name, aom_sk_ll_mw_helmet_elite_a_name, aom_sk_ll_mw_helmet_elite_b_name, aom_sk_ll_mw_helmet_heavy_a_name, aom_sk_ll_mw_helmet_heavy_b_name, aom_sk_ll_mw_helmet_lord_a_name, aom_sk_ll_mw_helmet_lord_b_name, aom_sk_ll_mw_helmet_med_a_name, aom_sk_ll_mw_pauld_elite_a_name, aom_sk_ll_mw_pauld_heavy_a_name, aom_sk_ll_mw_pauld_med_a_name, aom_sk_ll_rg_boots_light_a_name, aom_sk_ll_rg_bracer_med_a_name, aom_sk_ll_rg_chest_heavy_a_name, aom_sk_ll_rg_chest_light_a_name, aom_sk_ll_rg_chest_light_b_name, aom_sk_ll_rg_chest_med_a_name, aom_sk_ll_rg_grvs_med_a_name, aom_sk_ll_rg_helmet_light_a_name, aom_sk_ll_rg_helmet_med_a_name, aom_sk_ll_rg_helmet_med_b_name, aom_sk_ll_rg_pauld_light_a_name, aom_sk_ll_rg_pauld_med_a_name
- Fix: not fixed in the PR: needs a translator run and the Armory live install. Suggested fix: add `<LanguageFile xml_path="DE\loc_lothlorien.xml"/>` to each of the 12 language manifests and create the 12 files (`translate_with_claude.py --lang <L> --module Armory --sync-ids`), then `--update-baseline`.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 44. 200 Armory inline name keys have no loc row and are not in TAOM's known backlog
- Confidence: 80% (verified against your backlog list). Please double-check.
- Severity: cosmetic (untranslated)
- Where: LOTRLOME_Armory 2.0.41 (not in the repo); tools/armory-unregistered-names-baseline.txt in the repo
- What a player sees: These item and crafting piece names stay English in all 12 languages.
- Evidence: 644 Armory inline keys have no English loc row; 444 are in the baseline file (known). 200 are not: 45 starter crafting pieces in LOTRLOME_crafting_pieces.xml (lines 17867 to 18391) and the starter kits of 14 cultures, table below. Verified.
- Fix: not fixed in the PR: needs a translator run and the Armory live install. Suggested fix: add the 200 rows to the English loc_*.xml files and run the Armory translator, or add the ids to the baseline if they are meant to stay English.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

### The 200 keys
| file | count | keys |
|---|---|---|
| LOTRLOME_crafting_pieces.xml | 45 | starter_wm_gondor_sword_a01_blade, starter_wm_gondor_light_spear_blade, starter_wm_lossarnach_1h_axe_black_ash_blade, starter_wm_mordor_set1_sword_a01_blade, starter_wm_mordor_set1_polearm_a01_blade, starter_wm_mordor_set1_axe_a01_blade, starter_sm_dwarf_iron_sword_blade_a, starter_sm_dwarf_erebor_spear_blade_a, starter_sm_dwarf_erebor_axe_blade_1h_a, starter_wm_rivendell_sword_a01_blade, starter_wm_rivendell_spear_a01_blade, starter_wm_mirkwood_sword_a01_blade, starter_wm_mirkwood_spear_a01_blade, starter_wm_isengard_1h_sword_a01_blade, starter_wm_isengard_1h_spear_a01_blade, starter_wm_isengard_2h_axe_a01_blade, starter_wm_gundabad_sword_a01_blade, starter_wm_gundabad_spear_a01_blade, starter_wm_gundabad_javelin_a01_blade, starter_wm_gundabad_axe_a01_blade, starter_wm_dol_goldur_1h_sword_a01_blade, starter_wm_dol_goldur_2h_halberd_a01_blade, starter_wm_dol_goldur_1h_mace_a01_blade, starter_khuzait_blade_7, starter_sm_rh_loke_spear_blade_a, starter_aserai_blade_2, starter_spear_blade_27, starter_aserai_blade_8, starter_spear_blade_42, starter_wm_mordor_set1_sword_a02_blade, starter_axe_craft_20_head, starter_wm_dale_ws_1h_axe_a03_blade, starter_wm_dale_ws_spear_a01_blade, starter_wm_dale_ws_1h_axe_a01_blade, starter_wm_dale_ws_sword_a02_blade, starter_wm_dale_ws_sword_a03_blade, starter_wm_rohan_ws_sword_a01_blade, starter_wm_rohan_ws_spear_a01_blade, starter_wm_gondor_spear_a_blade, starter_empire_blade_4, starter_wm_dol_goldur_2h_axe_a01_blade, starter_dunland_caerdh_spear_head_a, starter_wm_harad_sword_a01_blade, starter_wm_harad_spear_a01_head, starter_spear_blade_15 |
| LOTRLOME_items\dale\starter_kit.xml | 18 | starter_sturgia_axe_2_t2, starter_dale_war_axe_c, starter_wm_dale_shield_a06, starter_dale_spear_a, starter_sk_dale_chest_infrantry_b01, starter_sk_dale_boots_infrantry_a01, starter_sk_dale_chest_infrantry_a01, starter_sk_dale_shoulder_archer_a01, starter_dale_recurve_bow_a, starter_sk_dale_chest_archer_a01, starter_dale_war_axe_a, starter_dale_sword_b, starter_wm_dale_shield_a08, starter_dale_sword_c, starter_sk_dale_boots_archer_a01, starter_sk_dale_lake_town_chest_mariner_a01, starter_ladder_dale_bow_t1, starter_northern_spear_1_t2 |
| LOTRLOME_items\dol_guldur\starter_kit.xml | 8 | starter_wm_dol_goldur_1h_sword_a01, starter_wm_dol_goldur_halberd_a01, starter_sk_dg_uruk_chest_light_a, starter_sk_dg_uruk_boots_light_a, starter_sk_dg_uruk_chest_light_b, starter_sk_dg_uruk_pauldron_med_a, starter_wm_dol_goldur_1h_mace_a01, starter_wm_dol_goldur_axe_a01 |
| LOTRLOME_items\dunland\starter_kit.xml | 3 | starter_dunland_wulf_shield_light_a, starter_empire_sword_1_t2, starter_dunland_caerdh_spear_a |
| LOTRLOME_items\erebor\starter_kit.xml | 11 | starter_sm_dwarf_iron_sword_a, starter_sm_dwarf_erebor_shield_leather_a, starter_sm_dwarf_erebor_spear_a, starter_sk_dwarf_erebor_chest_leather_med_a, starter_sk_dwarf_erebor_boots_light_a, starter_sk_dwarf_erebor_chest_leather_light_b, starter_sk_dwarf_erebor_pauldron_scale_a, starter_sm_dwarf_erebor_bow_a, starter_sk_dwarf_erebor_arrow_a, starter_sk_dwarf_erebor_chest_leather_light_a, starter_sm_dwarf_erebor_1h_axe_a |
| LOTRLOME_items\gondor\starter_kit.xml | 18 | starter_wm_gondor_sword_a01, starter_sm_gd_shield_a1, starter_wm_gondor_light_spear, starter_gondor_noble_jerkin_a, starter_sk_gd_ano_boots_a, starter_gondor_noble_coat_b, starter_ithilien_cloak, starter_gondor_steel_bow, starter_gondor_noble_coat_a, starter_wm_gondor_lossarnach_1h_axe_black_ash_a, starter_wm_gondor_light_spear_cardolan, starter_sk_ar_art_chainmail_half_a, starter_sk_ar_art_boots_a, starter_sk_ar_art_cape_a, starter_sk_gd_anf_inf_chainmail_a, starter_sk_gd_ano_pauld_inf_med_a, starter_wm_gondor_bow, starter_wm_gondor_spear_a |
| LOTRLOME_items\gundabad\starter_kit.xml | 8 | starter_wm_gundabad_sword_a01, starter_wm_gundabad_spear_a01, starter_sk_gb_uruk_chest_light_a, starter_sk_gb_uruk_boots_light_a, starter_sk_gb_uruk_chest_light_b, starter_sk_gb_uruk_cape_a, starter_wm_gundabad_javelin_a01, starter_wm_gundabad_axe_a01 |
| LOTRLOME_items\harad\starter_kit.xml | 12 | starter_aserai_sword_3_t3, starter_desert_round_shield, starter_eastern_javelin_3_t4, starter_sk_hr_st_chest_inf_chest_light_a, starter_sk_hr_st_bracer_light_a, starter_sk_hr_st_pauld_inf_med_a, starter_sk_hr_st_chest_inf_chest_light_b, starter_aserai_sword_5_t4, starter_eastern_spear_4_t4, starter_wm_harad_bow_a01, starter_wm_harad_sword_a01, starter_wm_harad_spear_a01 |
| LOTRLOME_items\isengard\starter_kit.xml | 12 | starter_isengard_1h_sword_a, starter_wm_isengard_shield_a03, starter_isengard_spear_a, starter_sk_uruk_hai_tunic_a2, starter_sk_uruk_hai_shoes_a1, starter_sk_uruk_hai_pauldron_light_a1, starter_wm_isengard_bow, starter_wm_isengard_arrow_a01, starter_sk_uruk_hai_tunic_a1, starter_isengard_2h_axe_a, starter_wm_isengard_bow_a01, starter_wm_isengard_shield_a01 |
| LOTRLOME_items\mercenary\starter_kit.xml | 8 | starter_default_arrows, starter_mirkwood_sword_a01, starter_mirkwood_spear_a01, starter_composite_bow, starter_bodkin_arrows_b, starter_bodkin_arrows_c, starter_bodkin_arrows_a, starter_battered_kite_shield |
| LOTRLOME_items\mirkwood\starter_kit.xml | 9 | starter_wm_mirkwood_shield_a01, starter_mkwd_inf3_chest_bronze, starter_mirkwood_boots, starter_mkwd_inf3_chest_silver, starter_mirkwood_shoulder, starter_wm_mirkwood_bow_a03, starter_wm_mirkwood_arrow_a01, starter_mkwd_inf3_chest, starter_wm_mirkwood_bow_a01 |
| LOTRLOME_items\mordor\starter_kit.xml | 17 | starter_wm_mordor_set1_sword_a01, starter_sm_mordor_shield_small_a, starter_wm_mordor_set1_polearm_a01, starter_sk_uruk_mordor_chainmail_light_a, starter_sk_uruk_mordor_boots_a, starter_sm_uruk_bow, starter_wm_mordor_set1_axe_a01, starter_wm_gundabad_shield_a03, starter_wm_mordor_set1_sword_a02, starter_wm_gundabad_shield_a02, starter_sk_md_orc_inf_chest_med_a, starter_sk_md_orc_boots_light_a, starter_sk_md_orc_arc_chest_light_b, starter_sk_md_orc_pauldron_light_a, starter_sm_uruk_bow_a, starter_sk_md_orc_arc_chest_light_a, starter_wm_gundabad_shield_a01 |
| LOTRLOME_items\rhun\starter_kit.xml | 13 | starter_sm_dg_khml_shield_med_a, starter_khuzait_sword_1_t2, starter_sm_rh_loke_shield_med_a, starter_sm_rh_loke_spear_a, starter_sk_rh_loke_chest_light_a, starter_sk_rh_loke_boots_a, starter_sk_rh_loke_tunic_a, starter_sk_rh_loke_pauldron_lam_heavy_a, starter_hunting_bow, starter_eastern_leather_boots, starter_composite_steppe_bow, starter_steppe_arrows, starter_ladder_rohan_bow_t2 |
| LOTRLOME_items\rivendell\starter_kit.xml | 9 | starter_wm_rivendell_sword_a01, starter_wm_elven_shield_a, starter_wm_rivendell_spear_a01, starter_rivendell_torso_light_light_tier2, starter_rivendell_boots_leather1, starter_rivendell_torso_light_light_tier1, starter_rivendell_cape_a, starter_highelf_longbow, starter_wm_elven_arrow_v1_a |
| LOTRLOME_items\rohan\starter_kit.xml | 9 | starter_wm_rohan_ws_sword_a01, starter_wm_rohan_shield_a01_gsw, starter_wm_rohan_ws_spear_a01, starter_rohan_militia_armour_b, starter_cts_rohan_boots3, starter_rohan_militia_tunic_b, starter_rohan_militia_pauldron_both, starter_rohan_militia_tunic_a, starter_wm_rohan_ws_bow |

## 45. TAOM.Dependencies: language manifests point at files that do not exist
- Confidence: 65% (likely inherited from the bundled packages). Please double-check.
- Severity: cosmetic (untranslated bundled strings)
- Where: TAOM.Dependencies 2.0.41 ModuleData\Languages\<LANG>\language_data.xml and Languages_MCM; the repo's Dependencies/_Module has no Languages folder (copied from the bundled BUTR packages at build time)
- What a player sees: In 8 languages the bundled ButterLib and MCM strings show English.
- Evidence: language_data.xml lists <LANG>/sta_strings.xml but the file is missing for BR, BYl, CNt, FR, IT, KO, PL, TR (0 strings). DE\language_data.xml lists DE/std_module_strings_xml-de.xml, which does not exist, while DE\sta_strings.xml (32 strings) sits unlisted. Languages_MCM is missing the same 8 files. Verified in files; effect Likely; probably inherited from the BUTR packages, so the real fix may be upstream.
- Fix: not fixed in the PR: it comes from the build step that copies BUTR language folders. Suggested fix: in DE\language_data.xml use `<LanguageFile xml_path="DE/sta_strings.xml"/>`. For the 8 languages without a file, ship the file or drop the manifest entry.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 46. Nine Gondor town descriptions use a doubled {== key
- Confidence: 70% (works today; fragile only). Please double-check.
- Severity: cosmetic (works today, fragile)
- Where: TAOM_Map 2.0.41 ModuleData\settlements.xml lines 2382, 2581, 2656, 2731, 2806, 2881, 2956, 3031, 3106 (town_EW1 Minas Tirith to town_EW9 Calembel; 9 `{==` hits counted); all 12 Languages\<LANG>\loc_settlements.xml (9 rows each)
- What a player sees: Nothing yet. The key reads `=Settlements.Settlement.text.town_EW1` and the language rows carry ids with the same leading `=`, so the lookup matches. Any tool that builds the id from the settlement id (write_village_descriptions.py, Apply-MapVillageNames.py) will miss these rows.
- Evidence: `text="{==Settlements.Settlement.text.town_EW1}...` on the 9 lines. Verified.
- Fix: not fixed in the PR: live install. Suggested fix: replace `{==` with `{=` on the 9 lines and rename the 9 ids in each of the 12 loc files from `=Settlements...` to `Settlements...` (whole row substitution, keep bytes).
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 47. Custom Battle maps all tagged Plain; Long Defeat classes use vanilla Calradian items
- Confidence: 45% (may be on purpose). Please double-check.
- Severity: cosmetic (maybe on purpose)
- Where: TAOM trunk Main/_Module/ModuleData/custom_battle_scenes.xml (36 rows; Dead Marshes id taom_mordor_battle_dead_marshes_forceatmo at line 126); long_defeat/classes.json installed lines 22 and 33, trunk lines 29, 43, 48
- What a player sees: Dead Marshes, Moria, Mordor and the Rohan plains all report terrain Plain. The Rohan archer class carries a Sturgian sword and plain bodkin arrows; the Rohan cavalry class and the new Gondor cavalry ride a vanilla Imperial horse.
- Evidence: All 36 rows have terrain="Plain"; vanilla's list uses Plain 179, Forest 104, Steppe 20, Desert 17, River 14, Swamp 2. What the engine does with the value in Custom Battle was not looked up. classes.json: sturgia_sword_2_t3, bodkin_arrows_a/_b, t3_empire_horse and noble_horse_imperial; all exist, nothing breaks. Verified (data); effect Unverified.
- Fix: not fixed in the PR: both need the team's design choice, and the terrain effect must be looked up first. Suggested fix: after checking what the value drives, `terrain="Swamp"` on the Dead Marshes row and fitting values on the others; swap the class items for the Rohan and Gondor Armory sword, arrows and horse the campaign troops use.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 48. Story mission names are English in every language
- Confidence: 50% (may be on purpose while the missions are new). Please double-check.
- Severity: cosmetic (maybe on purpose while dev only)
- Where: TAOM story_missions/story_missions.json (8 rows in 2.0.41, 10 in trunk, for example trunk lines 5 and 29)
- What a player sees: A story mission's name stays English in all languages.
- Evidence: Every `name` is `{=!}...`. The War Chronicle's Hornburg offer opens helms_deep_defend for players, so at least one is player facing; whether the offer shows this name was not checked. Verified (data); display Unverified.
- Fix: not fixed in the PR: needs a translator run. Suggested fix: give each row a `{=TAOM_Story_<id>}` key and run /localize.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 49. Fords of War is parked but still on the main menu
- Confidence: 40% (likely on purpose). Please double-check.
- Severity: information (maybe on purpose)
- Where: TAOM trunk docs/features/fords-of-war.md lines 18 to 29 and 967; Main/Features/FordsOfWar/FordsMenuEntry.cs (isHidden lambda); factions.json lines 310, 381, 822
- What a player sees: "Fords of War" on the main menu of a release build. The team parked it on 10 Nov 2026 with balance open (Rohan 10 of 12 wins, Gundabad 11 of 12, Mordor 1), matches that run to the 40 minute limit, and one native crash after a match end (TaleWorlds.Native.dll+0x565D8D).
- Evidence: The option is hidden only when no faction or map loads, and both config files ship 13 factions and 1 map. Trait lines say 150 and 50 fewer troops where the lane caps give 120 and 48 (balance-log.md:596). Verified (data and docs).
- Fix: not fixed in the PR: a release decision for the team. Suggested fix: label the option "(preview)" or hide it until the round 6 balance is in; correct the trait numbers.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 50. The same names are spelled with and without accents
- Confidence: 60% (partly on purpose). Please double-check.
- Severity: cosmetic (consistency; maybe partly on purpose)
- Where: TAOM, TAOM_Map and Armory English player text, 2.0.41
- What a player sees: Counts of capitalised words, without accent / with accent: Rhun 139 / Rhûn 1,786; Numenorean 331 / Númenórean 42; Anorien 106 / Anórien 44; Lothlorien 24 / Lothlórien 248; Lorien 35 / Lórien 79; Theoden 23 / Théoden 18; Theodred 11 / Théodred 3; Dain 81 / Dáin 32; Nazgul 22 / Nazgûl 26; Dunedain 18 / Dúnedain 59; Cirdan 19 / Círdan 14; Lugburz 12 / Lugbúrz 32; Nurn 37 / Nûrn 12; Udun 12 / Udûn 2; Ugluk 4 / Uglúk 14.
- Evidence: Counted over 53,184 English texts. Verified (counts). "Nûrn" is the one that is actually wrong (Tolkien: Nurn).
- Fix: not fixed in the PR: a style decision for the team, and a mass change is a translator run. Suggested fix: none urgent. If one form is wanted, Tolkien's forms are Rhûn, Númenórean, Anórien, Lothlórien, Théoden, Dáin, Nazgûl, Dúnedain, Círdan, Lugbúrz, Nurn (no accent), Udûn, Uglúk.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 51. Some named companions talk about code and XML
- Confidence: 45% (may be on purpose). Please double-check.
- Severity: cosmetic (maybe on purpose)
- Where: TAOM trunk Main/_Module/ModuleData/named_companions/named_companion_strings.xml lines 89, 101, 177, 179, 187, 189, 191, 193, 199; named_companions.xml line 659 (nc_spartan_name)
- What a player sees: "Belecthar, the Spartan... Where others wield spears, I forge worlds through code and structure.", "A Spartan who fights with XML?", Thanor "a weaver of codes", Blackrose "every line of code".
- Evidence: Same lines in installed 2.0.41. They look like tribute companions for team members. Verified text; intent Unverified.
- Fix: not fixed in the PR: a team decision. Suggested fix: none unless the team wants in world wording.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 60. Settlements sharing a name: Linhir and Tumladen
- Confidence: 85% (verified in the map files). Please double-check.
- Severity: cosmetic
- Where: TAOM 2.0.41 Modules\TAOM_Map\ModuleData\settlements.xml: castle_EW12 Linhir 1641 and village_EW4_4 Linhir 2793 (bound to Pelargir); castle_EW14 Tumladen 1741 and village_EW4_3 Tumladen 2780. Trunk 8b9cc1f74: not in repo (TAOM_Map is a live install only)
- What a player sees: two "Linhir" and two "Tumladen" on the map.
- Evidence: the settlement name rows above. Confidence: Verified. The Methedras pair is filed as item 58.
- Fix: not fixed: TAOM_Map is outside the repo. Suggested fix: rename the two villages (for example "Linhir Fields", "Tumladen Farms") and their {=Settlements.Settlement.name.*} rows in the 12 loc_settlements.xml files.
- Commit: none
- How checked: read the files. Nothing run.

## 61. Placeholder encyclopedia text on 12 Gondor villages
- Confidence: 85% (verified in the map files). Please double-check.
- Severity: cosmetic
- Where: TAOM 2.0.41 Modules\TAOM_Map\ModuleData\settlements.xml text= on village_EW1_1, village_EW2_2 ("A Gondor village"), village_EW1_2, _EW1_3, _EW2_3, _EW2_4 ("A Gondor village."), village_EW5_2, _EW5_3, _EW6_3, _EW6_4, _EW7_2, _EW7_3 ("A Gondorian Village."). Trunk 8b9cc1f74: not in repo
- What a player sees: an encyclopedia page that says only "A Gondor village." or "A Gondorian Village."
- Evidence: the text attributes above; other villages have a real sentence (for example castle_village_EN3_1). Confidence: Verified. village_EW1_3's key is also borrowed by Sardol (item 34).
- Fix: not fixed: TAOM_Map is outside the repo. Suggested fix: one line per village in the style of the others, through the repo's tools/oneoff/write_village_descriptions.py table, then the 12 language rows.
- Commit: none
- How checked: read the files. Nothing run.

## 52. Warg and mumakil action sets lack turn and lance pose rows
- Confidence: 70% (log noise seen; the visual effect not checked). Please double-check.
- Severity: cosmetic (log noise seen; visual effect Unverified)
- Where: LOTRLOME_Armory 2.0.41 ModuleData\action_sets.xml line 67114 (as_warg) and line 66867 (as_mumakil), both spot-checked; engine logs rgl_log_60584, 16068, 59812, 61248 (22 Nov 2026)
- What a player sees: Possibly a warg that rotates without a turn animation when it turns on the spot, and a mumakil rider without the proper pose in a character or party tableau. Not checked in game.
- Evidence: rgl_log_60584 (22 Nov, 2.0.42 plus Coop, battle at 14:06): `as_warg does not contain act_horse_turn_left` 2,391 times and `act_horse_turn_right` 976 times. as_warg has no base_set and no such rows; vanilla mount sets have them (even as_dog and as_cow). Also missing on as_spider, as_elephant, as_mumakil and as_dire_wolf. `as_mumakil does not contain act_pose_troop_cavalry_lance` once in each of 3 of 6 logs; as_warg, as_dire_wolf and as_chariot have it. docs/reference/lotrlome-warg-changes.md records the warg ids as deliberately unchanged. Verified (log, file).
- Fix: not fixed in the PR: Armory live install. Suggested fix: add `act_horse_turn_left` and `act_horse_turn_right` rows to as_warg (and the four other sets) pointing at an existing walk or idle clip, as the vanilla animal sets do, and an `act_pose_troop_cavalry_lance` row to as_mumakil (copy the row as_chariot uses with a mumakil idle clip). Record it in the Armory change ledger.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 53. Five smithing parts named in the crafting templates do not exist
- Confidence: 85% (verified; no effect in game). Please double-check.
- Severity: log only (no effect)
- Where: LOTRLOME_Armory 2.0.41 ModuleData\crafting_templates.xslt lines 353, 556, 558, 570, 572 (spot-checked)
- What a player sees: Nothing. The engine skips a missing UsablePiece silently (CraftingTemplate). The intended parts are simply absent.
- Evidence: Line 353 numenorean_sword_handle_e (a to d exist); 556 wm_gf_knight_spear_guard and 558 wm_gf_knight_spear_pommel (only _handle and _blade exist, LOTRLOME_crafting_pieces.xml 8908, 8927); 570 wm_dol_goldur_2h_halberd_a03_handle and 572 ..._a04_handle (only the blades exist, 12670, 12708). Verified.
- Fix: not fixed in the PR: Armory. Suggested fix: delete the five `<UsablePiece piece_id="..."/>` lines, or add the parts.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 54. 552 English loc rows in the Armory are used by no item
- Confidence: 80% (verified; no effect in game). Please double-check.
- Severity: log only
- Where: LOTRLOME_Armory 2.0.41 ModuleData\Languages\loc_*.xml
- What a player sees: Nothing. They are still translated 12 times.
- Evidence: Examples: aom_harad06_v1_cape_name "[Harad] Serpent Gaurd Pauldrons" (loc_harad.xml:61, a typo), aom_wm_lossarnach_1h_axe_pommel_name "Gondor Lossnarnach 1H Pommel A" (loc_LOTRLOME_crafting_pieces.xml:209), and the Erebor bracer and chest plate colour variants. Verified (no inline use in Armory; TAOM does not use the ids).
- Fix: not fixed in the PR: Armory. Suggested fix: retire the rows per the translator guide's orphan rule (English, 12 language files, cache), or fix the two typos if the items return.
- Commit: none
- How checked: Read only: files and decompiled 1.5.7 code read, line numbers spot-checked against the installed 2.0.41 files on 23 Nov 2026. No in-game test.

## 55. Trunk 2.0.42 class table names sk_ll_gl_crown_b, which Armory 2.0.41 lacks
- Confidence: 85% (release check only). Please double-check.
- Severity: log only (release check)
- Where: TAOM trunk Main/_Module/ModuleData/armour_acquisition/armour_classes.xml line 1975; Main/Features/ArmourAcquisition/ArmourGateService.cs:161; commit d1452035e; tools/generate_lothlorien_armor.py:140
- What a player sees: Nothing; the Lord's Crown A is not in the game until the Armory is updated.
- Evidence: Only in the two 2.0.42 sessions (4 lines): `[ArmourAcquisition] 1 class-table row(s) name no loaded item (sk_ll_gl_crown_b); regenerate: python tools/generate_armour_classes.py --apply`. The item is in no file of the installed Armory, TAOM or TAOM_Map. Verified.
- Fix: not fixed in the PR: it belongs to the release. Check: TAOM 2.0.42 must not go out with Armory 2.0.41, or this warning shows at every start. The suggested `regenerate` command would delete the row, which is the wrong action.
- Commit: none
- How checked: Logs and files read only.

## 56. MissionDiag reads campaign time too early on each mission start after a load
- Confidence: 50% (may be on purpose). Please double-check.
- Severity: log only (maybe on purpose)
- Where: TAOM trunk Main/Features/MissionDiagnostic/CampaignContextFormatter.cs:9 to 19; TAOM.Tests/Features/MissionDiagnostic/CampaignContextFormatterTests.cs:31
- What a player sees: Nothing.
- Evidence: `[MissionDiag] Campaign: <time read failed: DivideByZeroException>, MainHero=...` in 13 lines over 13 of 30 sessions, always the first mission after a load. The cause is documented in the code and tested; the guard works. Verified.
- Fix: not fixed in the PR: optional. Suggested fix: none needed. To carry the day in the crash correlation line, take the snapshot after CampaignTime.Initialize (for example from OnSessionLaunched) instead of OnGameStart.
- Commit: none
- How checked: Logs and code read only.

## 30. Already reported items, two re-checks, and co-op items for later
- Confidence: 90% (re-checks verified in code and the engine). Please double-check.
- Severity: information
- Where: TAOM 2.0.41 installed and trunk 8b9cc1f74
- What a player sees: nothing new. This file stops the same points being sent twice.
- Evidence: see the sections below. Confidence Verified unless stated.
- Fix: not fixed in the PR: this file only lists what was reported before or needs no change.
- Commit: none
- How checked: earlier reports read; TAOM trunk files and docs read for the two re-checks.

### Re-checks: our earlier report can be ignored for these two
1. "The 18 named companions point at Faction.neutral" (report 2026-11-23-10, point 3). Deliberate and harmless. Main/Adapters/NamedCompanionAdapter.cs:35 says an unrecruited named companion has no clan and that heroes.xml uses Faction.neutral, which Hero.Deserialize skips. Engine side re-read 23 Nov 2026: Bannerlord 1.5.7 Hero.Deserialize reads the faction reference and sets Clan only when `clan.StringId != "neutral"`, so "neutral" is a sentinel the engine itself supports (Verified, decompiled TaleWorlds.CampaignSystem.dll). Nothing to fix. Please ignore point 3 of that report.
2. "[AMordor], [Aharad], [ARhûn] look like typos" (reports 2026-11-23-09 point 3 and 2026-11-23-10 point 4). Deliberate. docs/features/troll-race.md:428 and :444 call [AMordor] the Custom Battle sort prefix, and docs/reviews/rca-fords-of-war-2026-item 10:61 (D16) treats it as the Custom Battle sort tag and strips it in Fords of War. Nothing to fix. Please ignore those points.

### Already reported (not repeated above)
- Noldor arrows (22), Iron Hills Heavy Bolts, 18 very dear shields, Noldor Longbow III and IV, Black Numenorean Lord Barding I and II: 2026-11-23-10, point 2.
- "Numenorean Numenorean" (6 bows), "[Nolder]" (5), "[Isengad]", "[Noldor]Silver" (3), Thenn leading spaces and missing tags, "Two-Handed Axe  II", "LongBow - Starting " and its starter copy, "CAPE_C", the "[Test]" Thranduil smithing parts: 2026-11-23-10, point 4. The Thenn leading space and the trailing space on "LongBow - Starting " exist only in the XML default; the English loc rows are clean.
- "[Rhun]" on 27 items (Armory, still open), caravan troops (104) and militia (8) without a culture tag: 2026-11-23-09. The caravan and militia tags are fixed in trunk 71503f7dd ("tag caravan and militia names with their culture", 108 names; translations follow in #1129). Thanks; nothing more from us on those.
- ActionIndexCacheRepair ERROR at every start (27 of 30 sessions): 2026-11-18-02 and 2026-11-23-10 point 5.
- FactionUI unused faction entries: 2026-11-18-03 and 2026-11-23-10 point 6.
- taom_body_length not declared: 2026-11-18-04 and 2026-11-23-10 point 7.
- Helm's Deep terrain shader compile error (also on the Custom Battle siege map): 2026-11-18-08 and 2026-11-23-10 point 1.
- Faction screen JSON naming non-playable factions: 2026-11-23-10 point 6.

### TAOM's own validator
tools/validate_moduledata.py on trunk 8b9cc1f74 with the installed 2.0.41 TAOM_Map and Armory: 0 errors, 1915 warnings (ARMOUR_MESH_TIER_LADDER 1380, WORN_ARMOUR_FLOOR 164, MELEE_LADDER_INVERSION 94, INCONSISTENT_ARMOUR_SLOT 92, LORD_ARMOUR_RANGE 84, ARMOUR_COLOUR_CLASH 52, MELEE_BLOW_CEILING 44, UPGRADE_ARMOUR_REGRESSION 3, ARMOUR_CLASS_TABLE_DRIFT 1, TROOP_SKILL_KIT_MISMATCH 1). These are the team's own balance gates and are not listed one by one. ARMOUR_CLASS_TABLE_DRIFT comes from the generator hardcoding an E:\ path.

### Co-op, later (single player only for now, no issue files written)
- A false "unexpected end" crash report on the co-op dedicated server (2026-11-18-06) and the "previous session ended without a clean exit" line (2026-11-23-11).
- Dual Wield troops and animations are missing on the co-op dedicated server (file 57, items P2 and P4).
- Dual Wield settings are per PC, not per campaign (file 57, P5).
- Logs show `[CoopCompat] settings differ from the server` in co-op sessions; expected.

# Part 3: suggestions for TAOM's own checks

Ideas only, for base TAOM. Each would have caught a group of the findings above and would keep them from coming back.

- A price check in tools/validate_moduledata.py: flag any shop item (is_merchandise) without a value attribute whose formula price is far above the other items of its type and tier. It would have caught every price finding at once (items 32 and 36, and the earlier Noldor arrows and shields), and it would check new Armory items automatically.
- A Custom Battle check: every faction a player can pick must offer a few troops on the muster screen. Khand and Abanissa can be picked but offer none, and Blue Craig and the Misty Mountain Orcs offer one each (item 23).
- Town descriptions in the village description repair (#1042): 32 towns, Edoras and Helm's Deep among them, still show Calradian text in every language (item 33). The same pass could catch a borrowed key like Sardol's (item 34).
- ActionIndexCache logging: instead of the ERROR at every start (the repair works), log the stack of the first early ActionIndexCache use once, at INFO. That would name the caller that runs too early.
