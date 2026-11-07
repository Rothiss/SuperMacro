# Changelog

All notable changes to SuperMacro are documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/), versions are the addon's own.

## [Unreleased]

### Fixed
- Fix action-bar cooldown/icon breakage with pfUI (nil-guard `this` in `GetActionCooldown`). (2026-11-06)
- Fix some slash commands not working in combat. (2026-04-01)
- Fix compatibility with nampower and action-set-saving addons. (2026-03-04)
- Don't pollute the global namespace for `RunMacro` (internal calls use `SuperMacro_RunMacro`). (2025-12-05)
- Fix client crash on Escape and debuff search in buff slots for OctoWoW raids. (2025-11-11)
- Fix crashes caused by some other addons. (2025-08-31)
- Fix loading when certain addons are present. (2025-08-31)
- Fix crash when pressing Escape from the Super tab. (2025-08-30)

## [3.19] - 2025-03-14
### Changed
- SuperWoW support: 511-character macro length.

## [3.18] - 2023-04-19
### Added
- Resizable macro window, adjustable font size, monospaced font, movable frame.
- Tabulation handler and current line-number display in edit boxes.
### Fixed
- Newline appended to end of edit box; extend script removed on macro rename; renaming
  native macros and macros with identical names.

## [3.17.1]
### Added
- `"mouseover"` unit identifier in `FindBuff()`; `SpellReady()` function.
### Changed
- Increased buff/debuff count scanned by `FindBuff()`.

## [3.17] - 2023-03-08
### Added
- DiscordBars support and TheoryCraft macro tooltip compatibility.

## [3.16b]
### Fixed
- Bongos support.

## [3.16a]
### Changed
- Main-menu icon edit; improved Bongos support.

## [3.16]
### Fixed
- Compatibility with Bongos action bars (partial).

## [3.15] - beta 1
### Added
- `/smacro` slash command; event-driven macros (`RegisterEventMacro` / `UnregisterEventMacro`);
  options to hide the menu button and wrap long lines.
### Fixed
- `FindFirstItem` / `FindFirstSpell` for `/use` and `/cast` (incl. ranks); `GetSpellCooldown`
  error popup in `SM_Channel()`.
### Removed
- Virtual templates from TOC (Fonts.xml, UIPanelTemplates.xml, ClassTrainerFrameTemplates.xml).

## [3.14a]
### Fixed
- Item count shows total across all bags and slots.

## [3.14]
### Added
- Alias replacement from other mods (ASF, ChatAlias) via `SM_InsertAliasFunction()`;
  `CancelBuff` / `unbuff` / `/unbuff` / `/smunbuff`.
### Changed
- Stretched popup by two rows; hide action text on Discord bars; toplevel minimap button;
  brightened rank text.
### Fixed
- Macro-code tooltip persisting after leaving button; `sayrandom` error; `SM_Tooltip` owner; `FindBuff`.

## [3.13]
### Fixed
- `SM_Tooltip.lua` syntax error.

## [3.12]
### Added
- `caststop(spell, ...)`.
### Fixed
- `this is nil` in tooltip; nil body in `FindFirstSpell`/`FindFirstItem`.

## [3.11]
### Added
- Super macros on non-default action buttons; per-character super macro settings;
  option to check cooldowns (required for auto-replace icons).
### Changed
- Optimized tooltip, cooldown, and icon replacement.

## [3.1]
### Added
- `CraftItem()` / `/craft`; `SayRandom()` / `/sayrandom`; Super macros (unlimited, 7000 chars)
  with `RunSuperMacro()` and key bindings; Ctrl/Alt-Shift-click insertion for spells/items.
### Changed
- `Print` renamed to `Printd` (preserves other addons' `Print`).

## [3.04]
### Fixed
- Tooltip and button-update bugs.
### Changed
- `SM_CastSpell` renamed to `SM_FindSpell`.

## [3.03]
### Fixed
- Cooldown and `/cast` bugs; moved menu button.

## [3.0]
### Added
- Options frame (`/supermacro options`); minimap button; game-menu button; item/spell
  tooltip, cooldown, and count; optional action-button icon replacement; DiscordActionBars
  tooltip; `SM_Channel()`; `/in` scheduling; `/shift`; shortened aliases (`cast`, `use`, `echo`, `send`, …).
### Removed
- Blizzard_MacroUI dependency; unlimited macros.
### Changed
- Right-click macro button runs the macro.

## [2.89]
### Added
- `DoOrder`, `Pass`, `Fail`; imported functions from the 1900 series.
### Fixed
- Self-cast; item link from tradeskill.

## [2.85]
### Added
- `SetActionMacro()`; `/cast` highest rank when omitted; experimental alias support.
### Fixed
- Sending chat messages with `\n` from `RunMacro`; editing `CastSpellByName`.

## [2.8]
### Added
- Macro tooltips show spell and/or macro code; `/supermacro macrotip` options.
### Changed
- Macro frame strata to HIGH; in-game extend behavior.

## [2.7]
### Changed
- In-game extend limit increased to 7000 characters (was 900); shows character limit.

## [2.6]
### Added
- `Macro()` alias for `RunMacro()`; in-game extend editor (saved to SavedVariables).

## [2.5]
### Changed
- Max macro length decreased from 1023 to 256; uses `SM_Extend.lua`.

## [2.4]
### Added
- Alt-click item-link into macro frame; Shift-click into chat.
- Item use/equip slash commands (`/use`, `/equip`, `/equipoff`, `/unequip`); `/print`;
  `/supermacro printcolor`.
