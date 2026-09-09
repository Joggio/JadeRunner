# JadeRunner

An in-game automation companion for **SilkroadWeb / Jadeway**. Configure combat, training, town errands, equipment, travel and party support from a panel while watching your character play.

JadeRunner runs through the browser game's existing session. This release is a single **Tampermonkey userscript**: no Node.js installation, terminal commands, separate bot account or build step is needed.

**[Open the userscript](jaderunner.user.js)** · **F9** opens the panel.

## Installation

1. Install [Tampermonkey](https://www.tampermonkey.net/) for your browser.
2. Enable userscript execution if your browser requires it. On current Chrome, open **Extensions → Tampermonkey → Details → Allow user scripts**. Older Chrome versions use Developer mode; see [Tampermonkey's setup instructions](https://www.tampermonkey.net/faq.php?q=Q209).
3. Open [jaderunner.user.js](jaderunner.user.js) and select **Raw** to view the complete file. If Tampermonkey offers installation, choose **Install**. Otherwise copy the entire file, open **Tampermonkey Dashboard → Create a new script**, replace the editor's starter code, and save.
4. Enable the script, disable any older copy of the bot, and **reload the game page**. The script needs to load before the game opens its connection.
5. Log in and enter the world. Press **F9** to open JadeRunner. Wait for the character and navigation data to load.

The script matches `jadeway.online`, its subdomains, and local development addresses. It is built for this browser game; it is not a bot for the original desktop Silkroad Online client. Other userscript managers have not been verified.

For a temporary session, you can also paste the entire JavaScript file into the game's developer console **before entering the world**, then enter normally. If the world connection was already open, reconnect after pasting so the script can capture the new connection. Console injection disappears when the page reloads; Tampermonkey installs it automatically on future visits.

## First run

1. In **BOT → Training position**, press **PIN HERE**, or enter both coordinates and press **APPLY X,Z**. To choose from the map, use **TRAVEL → SET TRAIN POS**, then click the location. A normal map click starts a travel mission instead of setting the camp.
2. In **SKILLS**, choose **→ Attack** and click learned skills to add them. Set **→ Buffs**, **→ On the road** and **→ Imbue** separately. Drag a compatible weapon onto any row that needs a weapon swap.
3. Configure potion consumption in the **game's own automatic potion controls**. In JadeRunner's **ITEMS**, use **HP pot** and **MP pot** to assign the item IDs the bot should recognize. This tab does not contain potion-use percentage or cooldown controls.
4. In **TOWN**, add supplies to **Buy list**, set each **keep** count, then add any explicit **Return to town if…** thresholds. Buying an item and configuring the game's automatic potion use are separate steps. Review **repair<**, **sell junk** and **trip at … free slots**, then press that section's **APPLY ⚙**.
5. Check **BOT → Behaviour**, **grind radius**, monster filters and defensive thresholds. Press the relevant **APPLY** button after editing number fields that have one, then **▶ START**. Initial state checks, town supplies and buffs can delay the first fight or walk.

Most switches, lists and selectors update immediately and are saved automatically. Fields with **APPLY**, **APPLY X,Z** or **SAVE** need their corresponding button.

## Panel controls and what Stop means

| Control | What it does |
| --- | --- |
| **F9** | Shows or hides the panel. Hiding it does not stop automation. |
| Drag the title bar | Moves the panel. |
| Three-dot size control | Cycles the wide, medium and compact tracker layouts. Use wide or medium to see the tabs, **cfg** and **Import JSON**. |
| Scale controls | Change the panel's scale; hidden in the compact tracker. |
| **▶ START / ⏹ STOP** | Starts/stops the main combat, movement, gathering, equipment and town loop. Start uses the pinned camp, or your current location if no camp is pinned. |
| **✖ cancel mission**, **✖ stop mission**, **Shift+X** | Cancels the current map/teleporter journey and any active town errand. Cancelling a town errand pauses new errands for five minutes. It does not stop the main bot or disable party-follow settings. |
| **cfg** | Copies the active configuration as JSON and also prints it in the developer console. It does not download a file. |
| **Import JSON** | Opens a paste window. Use **Load & save** to validate, load and save the pasted configuration. |
| **Log** header | Expands/collapses the log beneath the tabs. It is not a separate tab. |
| Log **all / good / warn / bad / move** | Filters displayed messages. **clear** empties the log; **copy** copies the full retained log, regardless of the visible filter. |

Logs follow new messages while you are at the bottom. Scroll up to pause following, then scroll back to the bottom to resume. Clearing the log or changing its filter returns to following the newest messages.

**Stop is not a global off switch.** These helpers have independent settings and can operate while the main bot is stopped, provided the game connection/state is available:

| Independent helper | Where to disable it |
| --- | --- |
| Party creation, invites, applications, listing registration, party buffs and resurrection | Their **PARTY** switches and assigned buff lists |
| Mastery levels and skill learning | **MASTERY → auto-level** and **learn the next rank when it unlocks** |
| Stat allocation | **MASTERY → leave stat points alone** |
| Alchemy | Its own **ALCHEMY → ⏹ STOP** button |
| Automatic quest acceptance | **QUESTS → auto-accept available quests** |
| Automatic login/start after login | **BOT → Account & auto re-login** switches |

To unload everything, disable the userscript and reload the page. The game's own automatic potion system is separate from JadeRunner.

## Features, by tab

### BOT — training, combat settings and profiles

- **Command & state:** character and target vitals, level, XP/SP, unspent stat points, gold, kills, loot count, uptime, position, zone, navigation readiness and current mission. **xp / hr** is a percentage of level progress per hour; gold/hour is the observed balance change, so purchases affect it.
- **Profile:** entering the world selects a profile by character name. The dropdown switches saved profiles. Enter a new name and use **＋ SAVE AS** to seed it from the current settings; an existing name loads that profile instead.
- **Training position:** **PIN HERE** uses the current position; **APPLY X,Z** uses entered coordinates in the current zone. **CLEAR** removes the pin, so the next Start uses your then-current position. **RESYNC POS** asks the server for the authoritative position and stops the current movement command.
- **Automatic camp progression:** enable **move up when the numbers say it is safe**. Configure kills of evidence, maximum levels above you, acceptable kill time and survival multiple. It measures elapsed combat time, including cast waits, and incoming server damage before healing/potions restore HP. Moving up also requires at least three damaging hits, damage totaling 5% of maximum HP, and ten seconds of combat; otherwise survival is reported as unknown. Known monster stats scale the estimate to possible camps in the current zone. Estimated survival is approximate and excludes healing; it is not a guarantee. The feature requires that zone's spawn data.
- **Camp downgrade:** **move DOWN after … deaths here** and **never below level** control retreat to an eligible lower-level spawn. Deaths accumulate for the saved camp, including deaths before any successful kill; a kill does not reset that count. Downgrading bypasses the upward progression's kill-evidence requirement and interval. The level floor can prevent a downgrade if no eligible lower camp remains. Moving camp changes the training pin and clears the camp's death count.
- **Behaviour:** **roam** enables movement around the camp and ordinary return-to-camp roaming logic. **stand ground** suppresses wandering once there; it does not disable attacks or the walk back to camp. **support (never attack)** suppresses offensive target acquisition while leaving maintenance/support tasks available.
- **auto-attack filler** permits basic attacks when no assigned offensive skill can be cast. **kite**, **danger %**, **recover %**, **panic %** and **overpull >** control defensive movement and pulling. Panic attempts a recall when engaged, below the panic threshold and out of the recognized HP potion; incoming damage or server restrictions can prevent that recall.
- **grind radius** limits ordinary target searching around the camp. **skip uniques / champions / giants** exclude those rarities from ordinary acquisition. The searchable monster filter uses exact names and offers **all monsters**, **kill ONLY these** and **ignore these**. It is populated from monsters observed in the zone. Defensive retaliation can still override ordinary farming preferences.
- **Account & auto re-login:** enter account email, password and character, then **SAVE**. **FORGET** clears the saved password; **LOG IN NOW** attempts login immediately. Optional switches retry dropped sessions, reload pages restored from the back/forward cache, and start after entering the world. Automatic start is armed once per page load and is cancelled by a manual stop.

The saved password uses a browser-local encrypted vault with its key in IndexedDB. It is separate from the JSON profile and must be saved again in a new browser/private session. This does not protect it from scripts with access to the same page.

### SKILLS — rotations, buffs and weapon assignments

Choose an assignment mode, then click a skill tile. Drag rows to reorder them; right-click a row or press **✕** to remove it. Clicking a skill again adds another row; imbue has only one slot. Hover tiles for their names, rank, requirements and other available metadata.

| Mode/control | Behavior |
| --- | --- |
| **→ Attack** | Offensive skills in priority order. The bot locks a target and sends the cast; the server handles approaching into range. Local cast-range estimates do not gate attack skills. |
| **rotate per monster** off | Each attack opportunity chooses the highest-priority usable row. |
| **rotate per monster** on | Advances through the ordered rotation and restarts at the top for a new target, skipping currently unusable rows. |
| **→ Buffs** | Self-maintenance at the camp. Includes supported European healing skills; ordinary instant self-heals are attempted below 85% HP, while supported timed effects use their effect/cooldown state. |
| **→ On the road** | Maintenance allowed during walks, journeys and town travel. Put travel protection here; ordinary camp buffs are suppressed during travel. |
| **→ Imbue** | One compatible buff/imbue maintained with high priority. Server cooldowns, requirements and pending casts still apply. |
| **retaliate while travelling** | Allows fighting attackers on the way to camp or during a journey/town trip. **only vs level … and above** filters minor attackers; dangerous health loss can still trigger defensive retaliation. |
| **show locked (mastery-gated)** | Shows mastery-blocked entries among the character's known skills. It is not the complete unlearned skill catalog; use **MASTERY → Skill library** for that. |
| **↻** | Refreshes the skill view using the captured character state and loaded game metadata. |

Drag a weapon from **Weapons** onto a row's weapon slot. Alternatively, click an empty slot, then a weapon tile. Click an assigned weapon slot to clear it. Equipped weapons are outlined and marked **E**. The bot selects the best stat-scored copy of the assigned item and waits for equipment confirmation before using the skill. It also repairs incompatible saved assignments when a compatible owned weapon can be identified.

Confirmed same-type weapon upgrades update matching assignments across Attack, Buffs, On the road and Imbue. Server cooldowns and cast outcomes control retries. The main loop waits for queued casts/windup instead of issuing competing movement commands. Native client target HP/name selection is mirrored when the client's target store can be found.

These self-maintenance lists are not automatic party-healing triage. Party-targeted buffs and revival are configured separately in **PARTY**, and still require appropriate learned skills, mana and equipment.

### ITEMS — assignments, bag sorting and equipment

- Select **HP pot**, **MP pot**, **Speed**, **Junk (sell)** or **Repair hammer**, then click a bag item to assign it. Clicking its assigned chip removes the assignment. **＋ pick an item you do not own yet** opens the catalog for advance configuration. Clicking a tile assigns an item; it does not immediately consume it.
- HP/MP IDs are used for supply recognition and recovery checks. Actual HP/MP drinking is handled by the game's automatic potion system. JadeRunner's **Speed** assignment maintains the chosen speed item when its effect is absent; the repair hammer is tried before a town repair trip.
- **SORT BAG** groups/reorders the bag. If the main bot is stopped, it starts a passive session for sorting rather than enabling ordinary grinding.
- **SELL JUNK NOW** currently clears a movement hold and logs a request to resume errands. It does **not** force a sale or independently start the bot; the normal town-loop conditions still decide when selling happens.
- **equip better gear automatically** compares weighted effective stat values derived from the item's definition, rolls and enhancement bonuses. Degree and enhancement matter through those values; a high roll percentage alone does not make an item better. INT and STR builds use different weights. This is a stat heuristic, not a full damage simulator or a valuation of every blue effect.
- Separate **weapons**, **armour and shields**, and **accessories** switches limit eligible categories. **only swap if the stat score improves by more than … %** is the minimum relative improvement for replacing an occupied slot, across tiers. Empty slots can be filled without that comparison.
- Upgrades happen outside combat after a quiet period, respecting level, race, mastery and weapon/off-hand constraints. Weapons retain the configured primary weapon type; the bot does not switch a wizard to an unrelated weapon type just because it has a larger number. Server refusals remain authoritative.

### MASTERY — stats, mastery levels and skill learning

- **Stat points:** choose **leave stat points alone**, **all points into STR** or **all points into INT**. Allocation is independent of both Start and the mastery-learning switches.
- Click mastery tiles to select them for automation. With **auto-level** enabled, lower selected masteries are raised first; selection order breaks ties. The character level, available SP and server mastery limits still apply.
- **gap** limits how far one selected mastery may run ahead of the lowest selected mastery. It is **not** a gap below character level. Blank/zero removes this balancing limit. **stop at** supplies an optional mastery-level ceiling.
- **learn the next rank when it unlocks** enables automatic skill learning. Click skills in **Skill library** to add them to **Auto-upgrade these skills**; right-click selected tiles to remove them. The library supports search, **learned only**, grouping by mastery or sorting by name, and displays unlock requirements.
- **max everything** adds skill groups from the selected mastery trees, with explicit skill selections retaining priority. It needs selected trees and the learning switch enabled. The bot works through prerequisites where possible and skips server-disabled skills. Selecting a library skill for learning does not add it to the combat rotation.

### LOOT — pickup rules and bag-quality rules

**pick anything up at all** is the master loot switch. The two stages use different information:

1. **Stage 1 — pick up:** choose **take everything** or **use filter**. Filter by item type, degree, seal, weapon type and required level. Empty categories mean any; populated categories must all match. Weapon-type filters narrow weapons only. **Always take these item ids** overrides the other item filters; **always take gold** controls gold separately. The master switch still takes precedence. Unknown item definitions are skipped in filter mode.
2. **Stage 2 — keep or sell:** enable **sell gear that clears none of these**. Gear is kept if it meets **any** enabled threshold: plus, blue-line count or stored variance. Only gear is judged, and leaving every threshold blank makes the filter do nothing. Rejected gear is eligible for the next town sale; it is not immediately deleted.

**keep if variance ≥** currently compares the raw stored variance field, not the weighted equipment score or a normalized overall roll percentage. Prefer plus/blue-line thresholds unless you understand that field. The advanced `bagFilter.keepIds` list exempts items from Stage 2; it does not override **TOWN → Always sell these**. Keep important items, including spare buff weapons, out of the forced-junk list.

### JOB — resource gathering

Enable **gather instead of fighting** to use gathering as the main activity. Choose wood/stone and node grades, and set **search radius**. The character must already have learned the relevant profession and own its tool; the bot equips the axe or pickaxe as needed. The panel shows profession level/XP, tool availability and nearby nodes.

**sweep the radius for new nodes** alternates between searches around the center and return visits when visible nodes are exhausted. Without sweeping, the bot can wait for nodes to reappear. Gathering uses streamed nearby nodes and server interaction rules; the script does not learn professions or obtain tools automatically.

### ALCHEMY — enhancement and magic stones

Select **use equipped weapon** or click a gear slot in the bag, then choose a mode:

- **＋ enchant:** set **stop at +**, optionally **use lucky powder**, **stop when powder drops below**, and **stop after the first failure**. It selects the elixir for the gear type and powder for its degree, sends one request at a time and stops at the target or when required materials are unavailable. The displayed odds are an estimate from the bundled enhancement table; the server decides the result. The powder threshold is checked before a fuse, so that fuse may take the count below the threshold.
- **◈ magic stones:** choose a stone, then optionally set **stop at this many blue lines** or **or when a line reaches value**. Either condition can stop the process; the value check uses the highest existing blue-line value, not a specifically selected stat. Zero disables a condition. It otherwise continues until stopped, refused or out of stones.

Use this tab's **▶ RUN / ⏹ STOP**. Alchemy operates independently of the main Start/Stop button. The target is a slot reference, so stop alchemy before sorting the bag or swapping the selected equipment. Enhancement can fail and consume materials; review the game's protection/failure rules before running it.

### TOWN — repairs, selling, supplies and death recovery

- **repair< … %dur:** repair below the configured durability threshold. A configured repair hammer is tried first; otherwise the bot approaches an appropriate merchant. Known town NPC coordinates let errands approach merchants that are not yet streamed into view.
- **sell junk** enables sales. **trip at … free slots** sets when a tight bag triggers a junk-selling trip. Junk is also sold during town trips started for other reasons. Press **APPLY ⚙** after changing these number/checkbox settings.
- **Always sell these:** explicitly mark item IDs as junk. This is in addition to LOOT's Stage 2 rules. Click a chip to remove it. **SORT BAG** is also available here.
- **Buy list:** click or drag an item from the shop catalog, set its **keep** count, drag rows to prioritize them, and right-click to remove. The list controls what gets bought. An empty list buys nothing, even if an HP/MP item is assigned in ITEMS; repair and selling can still run.
- **Return to town if…:** add an item and a **below** count. A count strictly below any listed threshold requests a supply trip and overrides the usual preference to remain with the party. If this list has entries, it replaces the buy list's supply-trip trigger; it does not replace the buy list itself or disable repair/selling needs.
- With no explicit return conditions, supplies trigger a trip below a percentage of their keep count (`shop.restockAtPct`, 50% by default). Once shopping, purchases aim for the full **keep** counts. The bot checks town supplies before departure and holds departure when essential potion purchases remain unresolved.
- **use the carrier when it is off cooldown:** requests eligible buy-list supplies through the game's carrier. It needs supported zone/carrier state and server eligibility. HP/MP supplies are prioritized when delivery slots are limited; a single order may not fill every keep count. The bot waits for carrier state and can walk to a landed delivery to collect it. Carrier use does not repair gear or sell junk.
- Long town walks can use recall (`shop.recallOverU`, 250 units by default). Server refusals and incoming attacks can interrupt it. A stuck errand may be abandoned/backed off rather than retried indefinitely.

**Teleport and death-loop controls are in TOWN:**

| Setting | Behavior |
| --- | --- |
| **Teleport to nearest point** | After town errands, uses an eligible map-teleport preset near the training position when it saves enough travel (currently at least 100 units by straight-line comparison). This selects an existing preset, not arbitrary coordinates. |
| **teleport back to last death spot** | Offers last-death teleport for the post-death return. If both options are enabled, a suitable nearest-point preset is preferred; last death is the fallback when no suitable nearest preset is chosen. A refused teleport can fall back to walking. |
| **auto-accept revive offers** | Accepts an incoming revival offer while the main bot is running. Offers are considered before ordinary respawn waiting. |
| **wait before respawning … min** | Adds the configured wait after death, not only after repeated deaths. With it off, the configured normal respawn delay applies (2.5 seconds by default). |

After respawn the bot processes town errands before heading back. Teleport arrival cancels the previous route and plans from the new position. A camp downgrade prevents returning to the superseded camp through last-death teleport. Teleports require the server's premium/eligibility checks; the script does not unlock paid features.

### PARTY — membership, recruitment and support

- **CREATE PARTY** uses **exp share** and **item share** settings. **auto-create if disbanded/kicked/deleted** recreates a party when you have none. LTP status is computed by the server, not selected by the bot.
- **invite ALL nearby players** and **invite from list only** are independently enabled rules. Set the invite radius and enter names one per line. Invites require an existing party; membership/capacity permissions are enforced by the server.
- **auto-accept invites** optionally restricts accepted inviters by name. An empty accept list allows anyone. Pending invites and recruitment applications also have manual **accept / decline** controls.
- **auto-apply to a party** watches recruitment listings for the listed leader names. It submits an application; the leader must still accept it.
- **go to a party member** uses party-broadcast positions, including out-of-view members and supported cross-zone routes. An empty name means the party leader. **stop within … units** sets following distance. This is character following; the **follow** switch in TRAVEL only follows the character with the map view.
- **Party buffs:** choose **everyone** or a named member, then select buffs. A nonempty per-member list replaces the shared list for that member; an empty list falls back to **everyone**. Right-click a name to remove its override. Members already buffed, disconnected, in another zone, outside the radius or without coordinates are skipped. This picker is for buffs, not a complete healing-rotation editor, and has no per-row weapon-swap controls.
- **auto-res party members:** select a revive skill and configure the radius. The bot casts on eligible dead members nearby; the recipient must accept the offer, manually or through their own automation. It cannot force another player's acceptance.
- **REGISTER LISTING / UNREGISTER:** manage a recruiting title, race and level range. **auto re-register if the listing disappears** restores an eligible missing listing. The roster shows leader, level and connection/death state; leaders get **promote / kick**, and members can **LEAVE PARTY**.

Party automation, including configured buffs/resurrection, has its own periodic loop and can continue after the main bot is stopped. It still needs a valid session and the server's skill/equipment requirements.

### TRAVEL — map, NPCs and portals

- Click the map to walk to a point. Clicking a different mapped zone builds a journey through known portals when a route can be found. **SET TRAIN POS** changes the next click into a camp pin instead of a movement order.
- Drag to pan, use **＋ close / mid / − far** to change zoom, and enable **follow** to center the map on your character. Panning turns map-follow off. Markers show available character/entity/NPC information.
- **Browse region…** centers the map on any region in the game's map manifest, including unvisited regions, and turns map-follow off. Browsing alone does not move your character. Tiles outside the current region are visible when panning too. Only visible tiles load, with at most four image requests in flight; older offscreen images are released. This does not preload the world's navigation meshes or reveal distant live monsters/players. NPC lists remain specific to your current zone, and areas absent from the game's map assets remain unavailable.
- Use **WALK** beside an NPC to approach it. Parsed teleporter destinations have their own buttons, including gold costs where applicable. Unknown destinations or insufficient gold can prevent those choices.
- A map/ferry mission can start a passive bot session while ordinary grinding is stopped. Manually requested destinations hold position on arrival; an errand's return-to-camp journey resumes grinding instead. **stop mission** or **Shift+X** cancels the current mission.

Full route searches run in a background Worker using the same navigation mesh/collision rules as the follower. The old movement command is stopped while a new route is computed; valid existing routes are reused. Results are discarded after relocation or significant destination/origin changes. Local collision probes, waypoint following and obstacle/alcove recovery remain active. This avoids blocking rendering with full searches, but does not guarantee every live obstacle is traversable.

If the browser blocks Workers, the log reports **background routing unavailable** and uses local steering. There is no synchronous full-search fallback that would freeze the renderer again.

### QUESTS — catalog, progress and actions

Open the game's quest log (**J**) once if this tab is empty. It uses the quest catalog/state sent to the game client, showing tracked progress, requested monsters/counts and displayed XP/gold rewards.

- **auto-accept available quests** accepts catalog quests within the character's level range, independently of Start.
- **ACCEPT**, **CLAIM** and **ABANDON** send the corresponding action. These manual buttons currently require the main bot to be started.
- Rewards are claimed manually. This feature does not automatically select quest camps, route through quest objectives or complete a quest chain for you.

## Keep your settings — especially in incognito

Settings are saved in the browser's local storage for the current game origin and character. **Private browsing storage can disappear when the private session closes.** A different browser profile or game hostname also has separate storage.

To make a backup:

1. If necessary, use the three-dot size control to leave compact tracker mode. Press **cfg** in the title bar.
2. Paste the copied JSON into a text editor and save it as a `.json` file somewhere outside the browser. The export is also printed in the developer console. If clipboard access is blocked, copy that JSON instead.
3. After entering the world on the intended character, press **Import JSON**, paste the complete JSON object, then press **Load & save**.

Import validates the data, fills omitted settings with defaults and saves it to the current profile. If the bot is running, it restarts with the imported settings. Invalid JSON or a storage failure leaves the existing configuration intact.

Old `SRB` exports remain compatible. Back up again after changing your build. In private windows, Tampermonkey also needs your browser's permission to run in incognito/private browsing. JSON does not include panel layout, cached assets or the password vault.

**The JSON is a settings backup, not a password-vault backup.** Save the login password again through **BOT → Account & auto re-login → SAVE** in a new private session. Exports normally include the login email/character but not the separately saved vault secret. Older/imported JSON can still contain a plaintext `login.password` field, so inspect and remove credentials before sharing any profile or log. This repository contains no player profiles.

## Optional console controls

The panel is sufficient for normal use. Advanced users can also run:

```js
JadeRunner.start();
JadeRunner.stop();
JadeRunner.exportConfig();
// Import a complete saved JSON string through the panel or importConfig(jsonText).
```

`SRB` remains an alias for compatibility. `JadeRunner.configure(partial)` updates live settings with a shallow merge; prefer the panel or `importConfig()` for validated, saved configuration changes. `JadeRunner.reset()` deletes the current saved profile and reloads the page.

### Advanced settings without dedicated panel controls

Use a full exported profile when editing JSON: importing a partial object fills other settings from defaults; it is not a patch of the current profile. `configure(partial)` is a shallow live merge, and normal autosave subsequently persists it. Nested objects need their other fields preserved.

| Setting/API | Meaning |
| --- | --- |
| `levelBand.grey` / `levelBand.red` | Ordinary acquisition limits below/above your level. These are not per-monster sliders in the BOT tab. |
| `homeRadius` | Distance that counts as arriving at camp. |
| `roamRadius` / `roamEveryMs` | Wandering extent and fallback movement pacing. |
| `lootRadius` | Maximum nearby loot search distance. |
| `attackEveryMs` | Minimum spacing of ordinary offensive attempts; real skill cooldowns still apply. |
| A skill row's `cond` | Optional rarity condition, such as `champion`; absent means unconditional. There is no dedicated condition editor in SKILLS. |
| `trainUp.everyMs` | Interval for considering an upward camp move; repeated-death downgrade is not delayed by this interval. |
| `shop.restockAtPct` / `shop.recallOverU` | Supply-trigger percentage and distance at which recall is considered. |
| `bagFilter.keepIds` | Items exempt from automatic bag-quality rejection, but not the forced-junk list. |
| `respawnDelayMs` | Normal respawn delay when the minute-based wait is disabled. |
| `nav.base` | Navigation asset URL prefix for the relevant game deployment. |
| `JadeRunner.trace("PlayerName")` / `JadeRunner.trace(null)` | Enables/disables the game's nearby-player trace behavior. It needs the player in view and can break off at the training camp. For out-of-view/cross-zone following, use PARTY instead. |

Legacy fields such as `engageRange`, `potions.hp.atPct` and potion cooldown settings can appear in shared/headless profiles. They do not replace server-controlled offensive approach or the game's native automatic potion controls in this userscript. Avoid hand-editing saved runtime fields such as retry timestamps and accumulated camp-death state.

## Troubleshooting and limits

| Symptom | What to check |
| --- | --- |
| F9 does nothing | Enable the script and browser userscript permission, verify the URL matches, then reload the game. |
| “No zone socket yet” | Enter the world. If the script was installed after the page loaded, reload first. |
| Missing skills | Wait for game metadata to load; check the selected assignment tab and mastery requirements. Unlearned skills are in MASTERY’s library, not the SKILLS rotation picker. Server-disabled skills remain unavailable. |
| Weapon requirement refused | Assign a compatible weapon you own. Check level requirements, bag space and off-hand conflicts. |
| “No skill cast” | Read each row's reason. Real cooldowns, insufficient mana, missing weapons, unlearned skills and server refusals still prevent casts. Report repeated unexpected cooldowns with the surrounding log. |
| Walking somewhere unexpected | Check the pinned camp's zone, map mission, party follow/trace and automatic training progression. Cancel the current mission before choosing a different destination. |
| “Background routing unavailable” | The browser or site's policy prevented the route Worker from running. Local obstacle steering remains available, but long-route planning is unavailable until the Worker can start. |
| Teleport does not run | Check the Town and death-loop toggles, server eligibility, cooldown and whether the selected preset actually shortens the trip. |
| Repeated deaths without a downgrade | Enable automatic training progression; check the death threshold, minimum level and availability of eligible lower-level spawns. |
| Town loop cannot finish | Check gold, merchant inventory, item IDs, bag space and purchase/refusal messages. |
| Bought potions are not used | Configure the game's automatic potion controls for the stocked item. ITEMS assigns IDs; it does not configure the native potion system. |
| Settings vanished | Import your JSON backup into the correct character/browser profile. Incognito storage is temporary. |

Navigation and combat fixes have automated regression coverage, including cooldown clocks, teleport recovery, town purchases and weapon assignment. This does not guarantee every live obstacle, build or server update will work. Routing depends on matching zone data, and native target display integration depends on the game's client UI. Server/client changes can require script updates. Report problems with the script version, browser, zone, reproduction steps and a sanitized log.

The script uses the game's connection and assets, fetches navigation data from the configured game asset host, and caches data locally. The panel optionally loads fonts from Google Fonts; system fonts are used if those requests fail. No separate bot backend is required.

## Credits and AI disclosure

JadeRunner grew from the SilkroadWeb bot project through human design, implementation, testing and feedback.

Thanks to **[Devsome/jadebot](https://github.com/Devsome/jadebot)** for systems we adapted for JadeRunner, including party buffing, monster targeting and passive mode.

**Parts of the code, debugging, refactoring and documentation were assisted by AI tools**, including OpenAI Codex and earlier Claude/GLM-assisted development. This was a mixed human-and-AI effort, not an entirely AI-generated project. The project maintainers remain responsible for reviewing and maintaining the result.

Thanks to Tampermonkey for the userscript platform. Game names, artwork, skill/item metadata and other game content remain the property of their respective owners. The script includes game-specific reference metadata and uses assets supplied by the game; this acknowledgement does not grant rights to that content.

## Disclaimer

Use automation only where the game operator permits it. It can move your character, spend in-game currency, use consumables, sell equipment and alter character progression according to your settings. Review those settings and keep backups. There is no guarantee against character deaths, lost items or account restrictions.

JadeRunner is a community project and is not an official Tampermonkey or Silkroad Online product.
