# Price table: overpriced LOTRLOME_Armory items (TAOM 2.0.41)

A note before you start: I put this together while working on a large update of my own compatibility patch (the TAOM AIO Compatibility Patch, a big overhaul), so a few things may have got slightly mixed up. Please double-check each point before you act on it. Every item has a confidence percentage: how sure I am that it is a real problem as described, and for the PRs that the fix is right. Everything here is about base TAOM, Dual Wield and Co-op only. Confidence for this table: 85% (computed with the game's own 1.5.7 price formula from the installed files; not checked in a shop).


What this is: every Armory arrow, bolt, shield, bow, crossbow and horse harness that is sold in shops, has no value attribute, and gets a base price from the game's formula far above TAOM's own prices. Each row has a suggested value="..." for the item's XML. The same numbers are in PRICE-TABLE-armory.csv for a later AIO switch.

Checked on: TAOM 2.0.41 (installed LOTRLOME_Armory and TAOM) and Bannerlord 1.5.7, 23 Nov 2026. Paths in the tables are relative to LOTRLOME_Armory/ModuleData/. The line is the item's id line.

## How the price is made

- Source: TaleWorlds.Core DefaultItemValueModel and ItemObject, decompiled from the installed 1.5.7 game. Price = 100 x 2.75^Tierf x (1 + 0.2 x (appearance - 1)) + 100 x max(0, appearance - 1). Tierf is clamped to -1 .. 7.5, so the cap is 197,240 at appearance 1 (185,405 at appearance 0.7). Each extra point of Tierf multiplies the price by 2.75.
- Arrows and bolts: Tierf = damage + 0.1 x (stack_amount - 20). The price is for the whole stack, and every 10 arrows above 20 add a full point, so a stack of 50 costs about 21 times a stack of 20 with the same damage. The Noldor arrows (damage 4, stack 50) land on Tierf 7.0, 118,940.
- Shields: Tierf = (hit_points^1.22 + 3 x body_armor + thrust_speed) / (6 + weight^1.11) x 0.04 - 2. Hit points dominate; 600 or more hit points on a light shield go past the cap.
- Bows and crossbows: Tierf = 0.1 x damage + 0.02 x missile_speed + 0.05 x accuracy - 9.25 (crossbows -1.5, light usage +1.25, +0.5 unless long_bow or no reload on horseback).
- Horse harness: the armour tier, 0.1 x (body_armor plus the other armour values) - 0.4.
- Crafted weapons (swords, axes, spears and the like; 273 sold Armory items without a value): the tier mixes the weapon's computed crafting stats with its piece tiers. Those stats are made by the crafting system at load and are not in the XML, so they are not computed here and have no rows.
- Where value overrides the formula: ItemObject reads value="..." and uses it as the base price; only when the attribute is missing does it call the formula. The attribute changes the price only. Tierf, the item's tier and everything that reads the tier stay as they are.
- TAOM does not change item values: TAOM.dll and TAOM.Client.dll 2.0.41 hold no reference to the item value model, and the trunk source (2.0.42) has none either.

## TAOM's own hand prices (anchors)

- No Armory arrow, bolt, shield, bow, crossbow or harness has a value attribute today.
- Sold crafted weapons with a value (62): the dearest are the Numenorean elite bastard swords at 32,000 and elite greatswords at 30,000; heavy ones 20,000 to 22,000; medium ones 12,000 to 13,000. 32,000 is the highest hand price on any sold Armory item.
- Mounts: Warg and dire wolves 1,220, Great Elk 1,400, war rams 950. The War Ram's formula price would be 96,747, so TAOM already uses value to override the formula where its stats run past it.

## Rule for overpriced

An item is listed when its Tierf is above 6.5. Bannerlord's top item tier (Tier 6) covers Tierf 5.5 to 6.5; above that the price keeps multiplying by 2.75 per point up to the cap. At appearance 1 the line is 71,723. 44 items meet the rule. Six more are listed because earlier reports named them (marked "below the rule"). No item outside the earlier reports meets the rule, so no row is new.

For scale, among sold Armory items of each type: arrows and bolts p90 118,940 (31 items, almost all inflated), shields p90 49,861 (239), bows p90 42,981 (35), horse harness p90 14,626 (61). Crossbows (3 sold, top 48,324) have nothing above the line.

## Method for the suggested value

Suggested value = the highest formula price among the Armory's normal sold items of the same type and the same culture, rounded down to the nearest 500. If that culture has no normal item of the type, the highest across all cultures is used. Arrows and bolts count as one type. "Normal" means sold, no value attribute, and not in this table. The suggestion is never above the current price. So no listed item costs more than the dearest ordinary item TAOM already sells of that kind, in its own culture where there is one. This replaces the 3,000 and 1,500 first suggested in note 32, so all ammo rows use one method; TAOM may well want ammo lower still.

## Can it reach a shop

Armour acquisition only governs character armour and the 27 named items in armour_acquisition_config.xml, so none of these types is held back by armoury level. Markets are stocked by the town owner's culture (CultureMarketplace) plus vanilla stock. Every row is sold in its XML, and every row's culture has towns on the TAOM map, counted by settlement culture (Rivendell 1, Mordor 6, Erebor 4, Gondor 11, Mirkwood 2, Isengard 1); Lindon markets also draw Rivendell weapons. The only gated row is wm_theoden_shield, a named weapon: never sold while armour acquisition is on, but with it off the gate falls back to the XML flag and the shield can be stocked. No row is on a culture blacklist in 2.0.41.

Confidence: computed. The formula and the data are verified from the decompiled 1.5.7 code and the installed files; no price was checked in a shop in game.

## Arrows (26)

| item id | name | file:line | formula price now | suggested value | reaches a shop | new or already reported |
|---|---|---|---|---|---|---|
| wm_elven_arrow_q1v2 | [Noldor] Elven Arrow XI | LOTRLOME_items/LOTRAOM_weapons.xml:4868 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q1v3 | [Noldor] Elven Arrow XII | LOTRLOME_items/LOTRAOM_weapons.xml:4892 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q1v4 | [Noldor] Elven Arrow XIII | LOTRLOME_items/LOTRAOM_weapons.xml:4916 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q2v2 | [Noldor] Elven Arrow XIV | LOTRLOME_items/LOTRAOM_weapons.xml:4940 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q2v3 | [Noldor] Elven Arrow XV | LOTRLOME_items/LOTRAOM_weapons.xml:4964 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q2v4 | [Noldor] Elven Arrow XVI | LOTRLOME_items/LOTRAOM_weapons.xml:4988 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q3v2 | [Noldor] Elven Arrow XVII | LOTRLOME_items/LOTRAOM_weapons.xml:5012 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q3v3 | [Noldor] Elven Arrow XVIII | LOTRLOME_items/LOTRAOM_weapons.xml:5036 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q3v4 | [Noldor] Elven Arrow XIX | LOTRLOME_items/LOTRAOM_weapons.xml:5060 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q4v2 | [Noldor] Elven Arrow XX | LOTRLOME_items/LOTRAOM_weapons.xml:5084 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q4v3 | [Noldor] Elven Arrow XXI | LOTRLOME_items/LOTRAOM_weapons.xml:5108 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_q4v4 | [Noldor] Elven Arrow XXII | LOTRLOME_items/LOTRAOM_weapons.xml:5132 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v1_a | [Noldor] Elven Arrow I | LOTRLOME_items/LOTRAOM_weapons.xml:4434 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v1_b | [Noldor] Elven Arrow V | LOTRLOME_items/LOTRAOM_weapons.xml:4606 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v1_c | [Noldor] Elven Arrow VI | LOTRLOME_items/LOTRAOM_weapons.xml:4649 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v1_d | [Noldor] Elven Arrow VII | LOTRLOME_items/LOTRAOM_weapons.xml:4692 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v2_a | [Noldor] Elven Arrow II | LOTRLOME_items/LOTRAOM_weapons.xml:4477 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v2_b | [Noldor] Elven Arrow VIII | LOTRLOME_items/LOTRAOM_weapons.xml:4735 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v2_c | [Noldor] Elven Arrow IX | LOTRLOME_items/LOTRAOM_weapons.xml:4778 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v2_d | [Noldor] Elven Arrow X | LOTRLOME_items/LOTRAOM_weapons.xml:4821 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v3_a | [Noldor] Elven Arrow III | LOTRLOME_items/LOTRAOM_weapons.xml:4520 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_elven_arrow_v4_a | [Noldor] Elven Arrow IV | LOTRLOME_items/LOTRAOM_weapons.xml:4563 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_mirkwood_arrow_a01 | [Mirkwood] Elven Arrow I | LOTRLOME_items/LOTRAOM_weapons.xml:5156 | 43,251 | 5,500 | yes | already reported (deep pass note 32), below the rule |
| wm_mirkwood_arrow_a02 | [Mirkwood] Elven Arrow II | LOTRLOME_items/LOTRAOM_weapons.xml:5199 | 43,251 | 5,500 | yes | already reported (deep pass note 32), below the rule |
| wm_mirkwood_arrow_a03 | [Mirkwood] Elven Arrow III | LOTRLOME_items/LOTRAOM_weapons.xml:5242 | 43,251 | 5,500 | yes | already reported (deep pass note 32), below the rule |
| wm_isengard_arrow_a01 | [Isengard] Bow Arrow I | LOTRLOME_items/LOTRAOM_weapons.xml:5287 | 15,727 | 5,500 | yes | already reported (deep pass note 32), below the rule |

Reference used: sk_dwarf_erebor_arrow_a [Erebor] Bow Arrow I, 5,719, for rivendell (culture has none, so all cultures) and mirkwood (culture has none, so all cultures) and isengard (culture has none, so all cultures).

## Bolts (2)

| item id | name | file:line | formula price now | suggested value | reaches a shop | new or already reported |
|---|---|---|---|---|---|---|
| sm_dwarf_iron_bolt_a | [Iron Hills] Heavy Bolts | LOTRLOME_items/LOTRAOM_weapons.xml:6412 | 118,940 | 5,500 | yes | already reported (TAOM report point 2) |
| wm_isengard_bolt_a01 | [Isengard] Heavy Bolts | LOTRLOME_items/LOTRAOM_weapons.xml:5330 | 15,727 | 5,500 | yes | already reported (deep pass note 32), below the rule |

Reference used: sk_dwarf_erebor_arrow_a [Erebor] Bow Arrow I, 5,719, for erebor and isengard (culture has none, so all cultures).

## Shields (18)

| item id | name | file:line | formula price now | suggested value | reaches a shop | new or already reported |
|---|---|---|---|---|---|---|
| wm_elven_shield_a | [Noldor] Shield I | LOTRLOME_items/LOTRAOM_shields.xml:2678 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_elven_shield_b | [Noldor] Shield II | LOTRLOME_items/LOTRAOM_shields.xml:2721 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_gf_knight_shield | [Nolder] Golden Flower Shield I | LOTRLOME_items/LOTRAOM_shields.xml:2764 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_gf_knight_shield_cloth | [Nolder] Golden Flower Shield II | LOTRLOME_items/LOTRAOM_shields.xml:2807 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_rivendell_shield_a02 | [Noldor] Shield III | LOTRLOME_items/LOTRAOM_shields.xml:3783 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_rivendell_shield_a02_black | [Noldor] Shield III Black | LOTRLOME_items/LOTRAOM_shields.xml:3869 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_rivendell_shield_a02_silver | [Noldor] Shield III Silver | LOTRLOME_items/LOTRAOM_shields.xml:3826 | 197,240 | 49,500 | yes | already reported (TAOM report point 2) |
| wm_theoden_shield | [Rohan] Theoden's Shield | LOTRLOME_items/LOTRAOM_shields.xml:6584 | 197,240 | 49,500 | gated (named weapon) | already reported (TAOM report point 2) |
| sm_gd_shield_b1 | [Gondor] Tower Shield A | LOTRLOME_items/LOTRAOM_shields.xml:1158 | 185,405 | 43,500 | yes | already reported (TAOM report point 2) |
| sm_gd_shield_b2 | [Gondor] Tower Shield B | LOTRLOME_items/LOTRAOM_shields.xml:1200 | 185,405 | 43,500 | yes | already reported (TAOM report point 2) |
| sm_iron_shield_b_gold | [Iron Hills] Royal Guard Shield I | LOTRLOME_items/LOTRAOM_shields.xml:3996 | 185,405 | 36,000 | yes | already reported (TAOM report point 2) |
| sm_iron_shield_tower_a | [Iron Hills] Tower Shield I | LOTRLOME_items/LOTRAOM_shields.xml:4038 | 185,405 | 36,000 | yes | already reported (TAOM report point 2) |
| sm_iron_shield_tower_b | [Iron Hills] Tower Shield II | LOTRLOME_items/LOTRAOM_shields.xml:4080 | 185,405 | 36,000 | yes | already reported (TAOM report point 2) |
| sm_iron_shield_tower_c | [Iron Hills] Tower Shield III | LOTRLOME_items/LOTRAOM_shields.xml:4122 | 185,405 | 36,000 | yes | already reported (TAOM report point 2) |
| sm_iron_shield_tower_c_gold | [Iron Hills] Royal Guard Shield II | LOTRLOME_items/LOTRAOM_shields.xml:4164 | 185,405 | 36,000 | yes | already reported (TAOM report point 2) |
| sm_mordor_shield_tower_a | [Mordor] Tower Shield I | LOTRLOME_items/LOTRAOM_shields.xml:134 | 185,405 | 21,500 | yes | already reported (TAOM report point 2) |
| sm_mordor_shield_tower_b | [Mordor] Tower Shield II | LOTRLOME_items/LOTRAOM_shields.xml:176 | 185,405 | 21,500 | yes | already reported (TAOM report point 2) |
| sm_mordor_shield_tower_c | [Mordor] Tower Shield III | LOTRLOME_items/LOTRAOM_shields.xml:218 | 185,405 | 21,500 | yes | already reported (TAOM report point 2) |

Reference used: wm_rohan_shield_a01_ggg [Rohan] Edoras Royal Guard Shield I, 49,861, for rivendell (culture has none, so all cultures) and vlandia; sm_gd_shield_a1 [Gondor] Wood Shield A, 43,628, for gondor; sm_iron_shield_a [Iron Hills] Shield I, 36,334, for erebor; sm_md_num_inf_shield_heavy_a [Mordor] Black Numenorean Infantry Heavy Shield I, 21,514, for mordor.

## Bows (2)

| item id | name | file:line | formula price now | suggested value | reaches a shop | new or already reported |
|---|---|---|---|---|---|---|
| highelf_longbowd | [Noldor] Longbow IV | LOTRLOME_items/LOTRAOM_weapons.xml:3620 | 113,512 | 27,500 | yes | already reported (TAOM report point 2) |
| highelf_longbowc | [Noldor] Longbow III | LOTRLOME_items/LOTRAOM_weapons.xml:3577 | 55,912 | 27,500 | yes | already reported (TAOM report point 2), below the rule |

Reference used: highelf_longbowb [Noldor] Longbow II, 27,540, for rivendell.

## Crossbows (0)

None above the line.

## Horse harness (2)

| item id | name | file:line | formula price now | suggested value | reaches a shop | new or already reported |
|---|---|---|---|---|---|---|
| sm_md_num_barding_lord_a | [Mordor] Black Numenorean Lord Barding I | LOTRLOME_items/LOTRAOM_horses.xml:553 | 183,433 | 44,500 | yes | already reported (TAOM report point 2) |
| sm_md_num_barding_lord_b | [Mordor] Black Numenorean Lord Barding II | LOTRLOME_items/LOTRAOM_horses.xml:571 | 183,433 | 44,500 | yes | already reported (TAOM report point 2) |

Reference used: sm_md_num_barding_elite_a [Mordor] Black Numenorean Elite Barding I, 44,505, for mordor.

## Other weapons

No rows. The Armory has no plain (non-crafted) melee or thrown Item; all of them are crafted weapons, whose price cannot be computed from the XML (see above). The named crafted weapons are covered by note 36.
