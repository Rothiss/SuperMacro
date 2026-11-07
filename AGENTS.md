# AGENTS.md

## What this is
- World of Warcraft 1.12 (vanilla) macro addon. This fork targets OctoWoW and
  requires the SuperWoW client extension (511-char macros; OctoWoW buff/debuff caps).
- Pure Lua 5.0 + WoW `.xml`/`.toc`. No build, test, lint, or CI tooling exists and
  nothing can be run locally — verification is read-only/static only.

## Language & API gotchas (do not modernize)
- Vanilla Lua 5.0 idioms everywhere: global `this` (the "self" global), `arg`/`arg1..N`
  varargs, `getglobal()`, `getn()`, `unpack()`, `tinsert()`, and string functions as
  globals (`strfind`, `gsub`, `strsub`, `strlen`).
- Do not rewrite to `string.*`, `_G`, `#table`, or `...` varargs; behavior differs and
  `this` has no modern equivalent. Functions are intentionally global and loosely scoped.

## Architecture
- Load order: `SuperMacro.toc` -> `SuperMacro.xml` (defines frames) -> Lua scripts in order:
  SuperMacro.lua, SM_Common, SM_MacroExtend, SM_Super, SM_ItemLink, SM_Slash, SM_Tooltip,
  SM_Extend (user file), localization. `SuperMacroOptions.xml` defines the options frame.
- `SuperMacro.lua` — frame/UI logic, macro run engine (`SuperMacro_RunMacro`, `RunMacro`,
  `RunSuperMacro`, `RunLine`), `SM_UpdateAction()` (hooks Blizzard/FlexUI/DAB action buttons).
- `SM_Super.lua` — hooks of WoW globals: `PickupMacro/Action`, `PlaceAction`, `UseAction`,
  `GetActionText`, `GetActionTexture`.
- `SM_Tooltip.lua` — tooltip/cooldown hooks (`ActionButton_SetTooltip`, `GetActionCooldown`)
  and spell/item finders.
- `SM_Slash.lua` — slash commands + user-facing functions (`FindItem`, `DoOrder`, `FindBuff`, …).
- `SM_MacroExtend.lua` — "extend" script editor (saved in `SM_EXTEND`).
- `SM_ItemLink.lua` — Alt/Shift-click item-link insertion. `SM_Options.lua` — options frame.
- Two macro kinds: regular (native 18 account + 18 char via `GetMacroInfo`/`EditMacro`) and
  "Super" (unlimited, 7000 chars, stored in `SM_SUPER`/`SM_ORDERED`, per-char action mapping
  `SM_ACTION` from saved `SM_ACTION_SUPER`).

## Global API hooks (high-risk)
- Hooks capture `old*` refs at file-load time and must delegate to them. Load order vs other
  addons matters.
- Hooks rely on global `this` being the Blizzard action button. pfUI (sibling `../pfUI`) and
  other bar addons call these globals from their own loops where `this` is nil — keep `this`
  nil-guarded (see `SM_Tooltip.lua` `GetActionCooldown`; do not remove that guard).
- `RunMacro` is a thin wrapper over `SuperMacro_RunMacro` so other addons overwriting
  `RunMacro` won't break internals. Use the namespaced function for internal calls.

## Deliberate absences
- `SM_Extend.lua` is referenced in `SuperMacro.xml` but intentionally absent and `.gitignore`d;
  it is a user-created customization file. Do not add it or "fix" the reference.

## Misc
- `README.md` is the user doc (usage + function/slash-command reference); `CHANGELOG.md`
  holds the version history.
- Version strings disagree: `.toc` says `3.19`, `SUPERMACRO_VERSION` in `SuperMacro.lua` is `"3.18"`.
