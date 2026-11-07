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
- Convenient slash commands and Lua helper functions — see [functions.txt](functions.txt)
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

If another addon already uses `/use` or `/eq`, the `/smuse`/`/smeq` variants are equivalent. More commands (crafting, buff cancellation, scheduling, etc.) are documented in [functions.txt](functions.txt).

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

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Credits

SuperMacro by Aquendyn. Forked and maintained for the OctoWoW server.
