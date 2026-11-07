# SuperMacro

An enhanced macro addon for World of Warcraft 1.12 (vanilla), forked for the **OctoWoW** server.

> **Use with SuperWoW only.** This fork requires the SuperWoW client extension.

## Requirements

- World of Warcraft 1.12 (vanilla) client
- The SuperWoW client extension
- The OctoWoW server — `FindBuff()` and buff/debuff handling are tuned for OctoWoW's caps

## Features

- All **18 account + 18 character** macros in one resizable, movable frame
- **Super macros** — unlimited virtual macros, up to 7000 characters each
- Works around the 256-character macro limit (chaining + extend scripts)
- Run macros from keybinds, and call other macros with `RunMacro()`
- In-game Lua "extend" code editor
- Insert item links and tradeskill recipes into macros
- Action-bar tooltips, cooldowns, and icon replacement (works with **pfUI**)
- Convenient slash commands and Lua helper functions — see the [Function & Slash Command Reference](#functions--slash-command-reference)
- Options frame

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
  - [Running & chaining macros](#running--chaining-macros)
  - [Working around the 256-character limit](#working-around-the-256-character-limit)
  - [Keybindings](#keybindings)
  - [Super macros](#super-macros)
  - [Slash commands](#slash-commands)
  - [Item links](#item-links)
  - [In-game extend editor & SM_Extend.lua](#in-game-extend-editor--smextendlua)
- [Functions & Slash Command Reference](#functions--slash-command-reference)
- [Changelog](#changelog)
- [Credits](#credits)

## Installation

1. Back up your existing `Bindings.xml` and `SM_Extend.lua` from the SuperMacro folder (if upgrading).
2. Unzip into `WoW/Interface/Addons` — a `SuperMacro` folder is created automatically.
3. Copy your backed-up `Bindings.xml` back into the `SuperMacro` folder, if you have one.
4. Optionally create a `SM_Extend.lua` file for extra-long code (see [In-game extend editor](#in-game-extend-editor--smextendlua)).

## Usage

### Running & chaining macros

Macros can call other macros with `RunMacro(index)` or `RunMacro("name")`:

- `RunMacro(1)` — runs the macro at position 1 in the frame.
- `RunMacro("Attack")` — runs the macro named "Attack".

Macros **1–18** are account-wide; **19–36** are character-specific. Adding or deleting macros changes their order, so name-based calls are safer.

Call a macro from another macro like this:

```lua
/script RunMacro("Attack");
```

`Macro()` is a shorter alias for `RunMacro()` when you're near the character limit.

> **Tip:** to show spell tooltips and button text for spells inside a macro, add one of these lines to the top of the macro:
>
> ```
> /run -- CastSpellByName("Spell Name")
> /script -- CastSpellByName("Spell Name")
> /script if nil then CastSpellByName("SPELLNAME"); end
> /run -- cast("Spell Name")
> /script -- cast("Spell Name")
> ```

### Working around the 256-character limit

A macro's body is limited to 256 characters; anything past that is cut off when the macro is saved. There are two ways around it:

**1. Chain macros.** Break a long macro into smaller pieces and have them call each other. For example, put this in a new macro (where the long macro is named `Macro1`):

```lua
/script RunMacro("Macro1") --more scripting here
```

Then put the new macro on your action bar.

**2. Use `SM_Extend.lua`.** Put very long functions and global variables in a file called `SM_Extend.lua` (you create this file yourself — it's intentionally not shipped so it isn't overwritten on update).

To define a function in the file:

```lua
function FunctionName(parameter1, parameter2, etc.)
	-- code goes here
	-- return variable
end
```

For example:

```lua
function FindTradeSkillIndex(tradeskill)
	if (TradeSkillFrame:IsVisible()) then
		for i=1,GetNumTradeSkills() do
			tsn,tst,tsx=GetTradeSkillInfo(i);
			if (tsn==tradeskill) then
				tsi=i;
				SelectTradeSkill(tsi);
				TradeSkillInputBox:SetNumber(tsx);
				TradeSkillFrame.numAvailable=tsx;
				return tsi, tsx;
			end
		end
	end
end
```

Then your macro might look like:

```lua
/script CastSpellByName("First Aid")
/script tsi,tsx=FindTradeSkillIndex("Heavy Linen Bandage") if (tsx) then DoTradeSkill(tsi,tsx-1) end
```

If you edit `SM_Extend.lua` while in-game, reload the UI (close the macro frame, then run):

```lua
/script ReloadUI()
```

### Keybindings

The addon ships default keybindings for macros #1–#36, plus two special ones (`Attack` and `PetAttack`). Edit `Bindings.xml` to add your own — back it up before upgrading.

**Attack** — create a macro named `Attack` (capital A) with this body:

```lua
/script if ( not PlayerFrame.inCombat ) then CastSpellByName("Attack") end
```

You can then bind it and call it from other macros:

```lua
/script RunMacro("Attack")
/cast Shadow Bolt
```

**PetAttack** — create a macro named `PetAttack` with this body:

```lua
/script CastPetAction(1);
```

Put the pet-attack icon in the first slot of your pet action bar (the default), then bind and call it as above.

### Super macros

Super macros are **virtual** macros that don't use the game's 18+18 macro slots. You can create an unlimited number, each holding up to **7000 characters**.

- Create and edit them in the **Super** tab of the SuperMacro frame.
- Drag them onto your action bar like a normal macro.
- Run one with `RunSuperMacro(index)` or `RunSuperMacro("name")`, or via the first ten super-macro keybindings.

> **Note:** super macros don't report range/usable state to the action button, so they won't fade when out of range. Cooldowns and tooltips still work.

### Slash commands

| Command | Description |
| --- | --- |
| `/macro` | Open the SuperMacro frame |
| `/macro <name>` | Run the named macro (`/script RunMacro("name")`) |
| `/smacro <name>` | Run a super macro |
| `/supermacro` | Show SuperMacro options help |
| `/supermacro options` | Open the options frame |
| `/supermacro hideaction 0/1` | Hide (1) or show (0) macro names on action buttons |
| `/supermacro printcolor <r> <g> <b>` | Set `/print` text color (values 0–1) |
| `/supermacro printcolor default` | Reset `/print` color to white |
| `/supermacro macrotip 0-3` | Tooltip: 0 normal, 1 spells, 2 macro code, 3 spells + code |
| `/use` / `/smuse <item>` | Use an item |
| `/equip` / `/smequip` / `/eq` / `/smeq <item>` | Equip an item |
| `/equipoff` / `/smequipoff` / `/eqoff` / `/smeqoff <item>` | Equip an item in the offhand |
| `/unequip` / `/smunequip` / `/uneq` / `/smuneq <item>` | Unequip an item |
| `/print` / `/smprint <text>` | Print text to the chat frame (only you can see it) |

> **Note:** running a macro or script from the chat line will not cast spells.

If another addon already uses `/use` or `/eq`, the `/smuse`/`/smeq` variants are equivalent. More commands (crafting, buff cancellation, scheduling, etc.) are documented in the [Function & Slash Command Reference](#functions--slash-command-reference).

### Item links

Insert item links and tradeskill recipes directly into a macro:

- **Alt-click** an item to insert its link. Works for container items, paperdoll items, bagslots, and tradeskills (bagslots are the icons on the main menu bar).
- **Alt-click** a tradeskill (top half of the frame, or its icon) to link the skill; **Ctrl-Alt-click** inserts the recipe.
- **Shift-click** an item/tradeskill inserts its name; **Ctrl-Shift-click** inserts the name in quotes.
- **Shift-click** a spell in the spellbook inserts `/cast <spell>(<rank>)`; **Ctrl-Shift-click** inserts `"<spell>(<rank>)"`; **Alt-Shift-click** inserts `<spell>(<rank>)` without quotes.

Keep `/eq` and the item link on the same line:

```
/eq [link]        -- good
/eq
[link]            -- not good
```

### In-game extend editor & SM_Extend.lua

The right edit box in the SuperMacro frame holds "extended" Lua code associated with the current macro, saved under the `SM_EXTEND` SavedVariables table (unrelated to the `SM_Extend.lua` file). Extend code is accessible to all macros, typically as functions.

- `SM_Extend.lua` is not shipped — create it yourself. It loads when the addon loads; edit it and reload the UI to apply changes.
- The in-game extend editor needs no reload — press **Save Extend**. Max length is 7000 letters.
- **Caution:** make sure your code has no errors before closing the macro frame.

Extend-code behaviors to be aware of:

- Creating a macro with the same name as an existing one inherits that macro's extend code.
- Renaming a macro to an existing name adopts that macro's extend code (the former's extend wins for both).
- Deleting a macro deletes its extend code, unless another macro shares the same name.

## Functions & Slash Command Reference

Every function and slash command in the addon, in alphabetical order. Alt-click an item to insert its item link into a macro; Shift-click inserts the item's or spell's name; Shift-Ctrl-click inserts the quoted name.

### CancelBuff(buff [,...]), unbuff, /unbuff, /smunbuff

Cancel the player's buffs that match the given strings.

```lua
/script CancelBuff("Demon Skin")
/script CancelBuff("demon")        -- cancels buffs with "demon" in the name
/script unbuff("demon")            -- same as above
/unbuff demon                      -- same as above
/unbuff mana, frost                -- cancel any buffs matching "mana" or "frost"
/unbuff .                          -- cancel all buffs (use with care)
/unbuff a                          -- cancel all buffs with "a" in their names
```

### cast

`cast` is the same as `CastSpellByName`.

```lua
cast("Heal")
```

### caststop(spell [,...])

Cast a spell, then stop. Casts multiple spells in sequence. Only works properly for spells that don't trigger the global cooldown. (Not tested.)

```lua
/script caststop("Arcane Power","Presence of Mind","Frostbolt")
```

is equivalent to:

```lua
/cast Arcane Power
/script SpellStopCasting();
/cast Presence of Mind
/script SpellStopCasting();
/cast Frostbolt
```

### Channel(spell), SM_Channel, /smchan, /smchannel

Safely channel a spell, ignoring accidental multiple keypresses.

```lua
SM_Channel("mind flay")
/smchan mind flay
```

### CraftItem(skill, item, count), /craft, /smcraft

Create an item or enchant (at least one). If `count` is omitted, makes one. If `count` is zero, makes all available. If negative, leaves that many.

```lua
/craft First aid, Heavy Linen Bandage              -- make one bandage
/smcraft First aid, Heavy Linen Bandage, 0         -- make all available
CraftItem("First aid", "Heavy Linen Bandage", -1)  -- make all but one
```

### DoOrder(spell | item [,...]), /order, /smorder

Use the first available item or cast the first spell in a comma-separated list, ignoring the rest. Alt-click an item to insert its link.

```lua
DoOrder("healing potion", "lesser healing potion", "healing touch")
/order healing potion, lesser healing potion, healing touch
-- if healing potion is found, uses it and ignores the rest

/script DoOrder( not FindBuff("mark") and "mark of the wild" or "", not FindBuff("thorns") and "thorns" or "" )
-- each comma-separated part must return a string or ""
```

### echo

See `/print`.

### /equip, /smequip, /eq, /smeq

Equip or use an item. Also works with item links.

```lua
/eq hatchet
```

### /equipoff, /smequipoff, /eqoff, /smeqoff

Equip an item into the off-hand slot. Also works with item links.

```lua
/eqoff shield
```

### Fail(message), /fail

Run a message or script if the current action (spell or item) failed. See `Pass`.

```lua
/script CastSpellByName("Attack") Fail("Can't attack")
/script CastSpellByName("nada") Fail("not a spell")
/script Fail("no action")
/fail /script use('hearthstone')

/cast Heal
/fail /p can't heal
```

### FindBuff(buff [ [, unit], item]), buffed

Check whether a unit has a specific buff, debuff, or weapon buff.

- `buff` — the buff's name or part of it (not case-sensitive).
- `unit` — the API unit value (`'player'` if empty or nil).
- `item` — boolean; if truthy, searches for a weapon (item) buff instead.

```lua
buffed("Find Herbs")         -- player's buff; returns "track", 0
buffed("herbs")              -- also works
FindBuff("rockbiter",nil,1)  -- player's weapon buff; returns "main"/"off", time left, charges
buffed("rockb")              -- same as above
buffed("gouge", 'target')    -- target's buff; returns "debuff", id
buffed("perception")         -- returns "buff", id
```

### SpellReady(spell)

Check whether a specific spell is off cooldown.

```lua
SpellReady("Innervate") -- returns true or false
```

### FindItem(item)

Find an item in your bags or inventory. If in inventory, returns `slot, nil, texture, count`. If in bags, returns `bag, slot, texture, total count`.

```lua
local bag,slot,texture,count = FindItem("Lesser Magic Essence");
```

### /in seconds[+] command, /smin, SM_IN(seconds, command[, repeat])

Execute a command after `seconds` seconds. Use `+` after the seconds to repeat. Separate multiple commands with `\n`. Does not cast spells or use items.

```lua
/in 5 /p NOW
/script SM_IN(5, "/p NOW")                    -- no repeat
/in 1 /script PlaySound("LOOTWINDOWCOINSOUND")
/smin 600+ /print 10 mins repeating           -- repeats
SM_IN(600, "/print 10 mins repeating", 1)     -- repeats (third parameter)
/in 1 /t ;me hi\n/t ;me hey                   -- multiple commands
/script SM_IN(1,"/t ;me hi\n/t ;me hey")      -- same as above
/in 1m /print 1 min passed                    -- 1m = 1 min, 1h = 1 hour, 1s = 1 second
/script SM_IN("1m","/print 1 min passed")     -- note 1m in quotes
/in 1h30m5s+ /print repeat every hour, 30 mins, and 5 seconds
```

### lastActionUsed

Variable storing the current/last action's id — usually the same id as the button you just pressed. Useful for items and super macros to check `IsActionInRange`.

```lua
/script if IsActionInRange(lastActionUsed) then use("trinket")
```

### Macro

See `RunMacro`.

### Pass(message), /pass

Run a message or script if the current action (spell or item) succeeded. See `Fail`.

```lua
/script CastSpellByName("Attack") Pass("Attacking")
/pass /macro Cheer

/cast Heal
/pass /p Healing
```

### pickup(bag, slot), pickup(inventory_slot), pickup("item")

Pick up an item from inventory or bags. One argument for inventory; two for bags. A string argument is treated as an item name. Calling again puts the item into that slot (may swap items). Also works with item links.

```lua
pickup(16)                                        -- main-hand weapon
pickup("mainhandslot")                            -- also main-hand weapon
pickup(1,4)                                       -- bag 1, 4th slot
pickup("hearthstone")                             -- pickup hearthstone
pickup("hearthstone") pickup("healing potion")    -- consecutive pickups swap the items
pickup("hearthstone") pickup(4,7)                 -- put hearthstone in that slot (may swap)
pickup("ring of iron will") pickup("finger0slot") -- put ring in first finger slot
```

### /print msg, /smprint, SM_print(msg), echo

Print a message to chat that only you can see. Identical to `DEFAULT_CHAT_MESSAGE:AddMessage`. Default color is white (changeable in options).

```lua
/print test
echo("test")
```

### PrintColor(r,g,b,msg [,...]), Printc

Print a message in a specified color (red, green, blue values between 0 and 1). Accepts any number of arguments like `Printd`.

```lua
PrintColor(1,0,1, "test")         -- purple
PrintColor(1,1,0, "msg1", "msg2") -- yellow
```

### Printd(msg [,...]), Print

Print a message to chat that only you can see. Unlike `/print`, accepts any number of arguments and prints each on a separate line. Always white. If `Print` is taken by another addon, it is not overwritten.

```lua
Printd("Test")
Print("msg 1", "msg 2") -- only if another addon doesn't define Print
```

### PrintTable(table, rowname, level), Printt

Print a table to chat (only you can see it).

```lua
PrintTable(SM_VARS)
```

### RegisterEventMacro(macro, super, event[,...]), UnregisterEventMacro(macro, super, event[,...])

Run a macro after an event (or events). `macro` is the macro name; `super` is `1` for a super macro, else `0`/`nil`; then any number of events. Best placed in the Extended LUA box. Use `ViewEventMacros()` to print the events table.

```lua
RegisterEventMacro( "todo", nil, "PLAYER_LOGIN");                  -- regular macro
RegisterEventMacro( "todo", 1, "PLAYER_LOGIN");                    -- super macro
RegisterEventMacro( "todo", nil, "PLAYER_LOGIN", "PLAYER_LOGOFF"); -- multiple events
UnregisterEventMacro( "todo", nil, "PLAYER_LOGIN");                -- unregister
RegisterEventMacro( "ding", 1, "PLAYER_LEVEL_UP");                 -- on level up
```

### RunBody(longtext)

Execute `longtext` like a macro. Each line is separated by `\n`.

```lua
RunBody("/cast Attack\n/pass /p Attack!")
/in 5 /script RunBody("/print 5 secs passed\n/macro MyMacro")
```

### RunLine(command [,...])

Execute a line like a macro or chat message. Takes multiple lines as separate arguments. Excellent for slash commands inside `if` blocks.

```lua
RunLine("/p hi")
RunLine("/p hi", "/dance", "/supermacro")
if GetSpellCooldown(1,'spell')==0 then CastSpell(1,'spell') RunLine("/in 5 /print Cooldown finished") end
```

### RunMacro(index | name), Macro, /macro

Execute a macro by name or by index.

```lua
/macro Attack      -- runs macro named Attack
RunMacro("Attack") -- same as above
Macro(1)           -- run first macro in the frame
/macro 1           -- runs macro named "1" (note the difference)
Macro("1")         -- same as last example
/macro             -- opens the SuperMacro frame (no argument)
```

### RunSuperMacro(index | super), /smacro

Execute a super macro by name or by index (as shown in the Super tab).

```lua
RunSuperMacro(1);
RunSuperMacro("MySuperMacro");
```

### SayRandom(speech [,...]), /sayrandom, /smsayrandom

Say a random phrase from a comma-separated list (can do more than chat). There's an equal chance of nothing happening (1 in N+1).

```lua
/sayrandom hi, hello                              -- says hi, hello, or nothing
/script SayRandom("hi","hello");                  -- same as above
/script SayRandom("/script Print(1)","/script Print(2)"); -- actions besides chat
/sayrandom /script Print(1), /script Print(2)     -- same as above
```

### send

`send` is the same as `SendChatMessage`. Also see `RunLine`.

```lua
send("hi")                                  -- say "hi" locally
send("hi", "WHISPER", "COMMON", "Aquendyn") -- whisper
```

### SetActionMacro(actionid, macro)

Set or replace an action button with another macro. Use the global `lastActionUsed` to get the id of the last pressed/clicked action button.

Example — swap two macros named FindHerbs and FindMinerals:

Macro `FindHerbs`:

```lua
/cast Find Herbs
/script SetActionMacro( lastActionUsed, 'FindMinerals' )
```

Macro `FindMinerals`:

```lua
/cast Find Minerals
/script SetActionMacro( lastActionUsed, 'FindHerbs' )
```

Drag either macro onto an action button; each execution swaps them (with icons and tooltips).

### SetActionSuperMacro(actionid, supername)

Set or replace an action button with a super macro. See `SetActionMacro`.

```lua
SetActionSuperMacro(1,"MySuperMacro");
```

### /shift form, /smshift

`form` is the number or name of a form, stealth, or stance.

- Druids: `bear=1`, `aquatic=2`, `cat=3`, `travel=4`, `moonkin=5`
- Rogues: `stealth=1`
- Warriors: `battle=1`, `defend=2`, `berzerk=3`

```lua
/shift bear
/shift battle
/shift stealth
/shift 1   -- battle (warrior), stealth (rogue), bear (druid)
```

### stopcast

`stopcast` is the same as `SpellStopCasting`. Also see `caststop`.

```lua
/cast Arcane Power
/script stopcast()
```

### /unequip, /smunequip, /uneq, /smuneq

Unequip an item (if you have empty bag space). Alt-click an item to insert its link.

```lua
/uneq shield
```

### use(bag,slot), use(item), /use, /smuse

Use an item by name/link, by bag and slot, or by inventory slot.

```lua
/use hearthstone
/use 4,1
/script use("hearthstone");
/script use(4,1);   -- bag 4, 1st slot
/script use(13);    -- trinket
```

### UseItemByName(item)

Use or equip an item given its name or item link.

```lua
UseItemByName("hearthstone")
```

### ViewEventMacros()

Print the events table used by `RegisterEventMacro` to chat.

```lua
ViewEventMacros();
```

Output:

```text
EventMacros {
	PLAYER_LOGIN {      -- event
		[todo] => 1     -- macro
	}
	PLAYER_LEVEL_UP {   -- event
		[ding] => 1     -- macro
	}
}
```

### Aliases (ASF)

Aliases can simplify macros significantly, but require an alias addon. These examples use ASF (Alias-Spellchecker-Filter).

For example, to get current mana, the alias is `;pm` — instead of `UnitMana('player')`. More importantly, Alias allows function values into chat. Instead of:

```lua
/script SendChatMessage("Buffing you", "WHISPER", "COMMON", UnitName("target"))
```

use a regular chat message:

```
;tt Buffing you
```

Useful default aliases:

| Alias | Meaning |
| --- | --- |
| `;z` | `/script` |
| `;cn` | `CastSpellByName` |
| `;c` | `/cast` |
| `;m` | `/macro` |
| `;rl` | `/console reloadui` |
| `;pm` | player mana |
| `;pmm` | player max mana |
| `;ph` | player health |
| `;phm` | player max health |
| `;p2h` | party2 health (and so on for party3/4/5) |

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Credits

SuperMacro by Aquendyn. Forked and maintained for the OctoWoW server.
