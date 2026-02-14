# CLAUDE.md - Sword of the Evening CK3 AGOT Submod

## Project Overview

This is a **CK3 AGOT submod** that adds a "Sword of the Evening" prodigy system for Dayne cadet branches, mirroring the existing "Sword of the Morning" system in AGOT. It is fully additive — no AGOT files are modified.

## Architecture

### How CK3 Modding Works
- CK3 loads mod files **additively** — files in the same directory paths merge with base game/mod files
- `on_action` files can hook into existing on_actions using `on_actions = { }` blocks
- Story cycles drive timed event chains using `effect_group` with `days = N` and `triggered_effect` stages
- Localization `.yml` files must start with a UTF-8 BOM character and `l_english:` header
- All `.txt` game files should also start with BOM

### Key Concepts
- **Story cycle** (`agot_story_sote`): Drives the 5-stage training over ~10 years (731 days per stage). The prodigy is the `story_owner`. Variables track parent references and current stage.
- **Passthrough variables**: Parent/grandparent references are passed to the story cycle via `_passthrough` suffix variables on the prodigy, then picked up in `on_setup`.
- **Scopes**: Events use `scope:prodigy`, `scope:dayne_parent`, `scope:dayne_grandparent`, `scope:twilight`. Root is whoever receives the event (parent or prodigy depending on context).
- **Player routing**: Each story cycle stage checks whether the father, mother, prodigy, or grandparent is a player (`is_ai = no`) and routes the event accordingly. Parent events (0002-0008) fire when a parent/grandparent is the player; self events (0009-0014) fire when the prodigy is the player.

### AGOT Reference Files (in the main AGOT mod, not modified)
- Dayne prodigy events: `events/agot_events/agot_dayne_prodigy_events.txt`
- Story cycle SOTM: `common/story_cycles/agot_story_cycle_sotm.txt`
- Worthy trigger: `common/scripted_triggers/00_agot_character_triggers.txt` (line ~92)
- Dawn creation effect: `common/scripted_effects/00_agot_artifact_weapon_effects.txt` (line ~2)
- Birthday on_action: `common/on_action/agot_on_actions/agot_childhood_on_actions.txt` (line ~272)
- Childhood event 0011: `events/agot_events/agot_childhood_events.txt` (line ~1209)

### File Map

| File | Purpose |
|------|---------|
| `common/scripted_triggers/morau_triggers.txt` | `is_dayne_bastard_house_trigger` (cadet branch check) and `worthy_sword_of_the_evening_trigger` |
| `common/scripted_effects/morau_effects.txt` | `agot_create_artifact_vs_twilight_effect` — creates Twilight artifact |
| `common/modifiers/morau_modifiers.txt` | `vs_twilight_modifier` — prowess, prestige, opinion bonuses |
| `common/artifacts/templates/morau_artifact_templates.txt` | `vs_twilight_template` — equip/benefit conditions |
| `common/nicknames/morau_nicknames.txt` | `nick_agot_the_sword_of_the_evening` |
| `common/story_cycles/morau_story_cycle.txt` | `agot_story_sote` — 5-stage training cycle |
| `common/on_action/morau_on_actions.txt` | Hooks into `on_6th_birthday`, 5% trigger chance |
| `common/decisions/morau_decisions.txt` | 3 decisions: train child, claim worthy, claim unworthy |
| `common/character_interactions/morau_interactions.txt` | Dynasty head offers Twilight |
| `events/morau_prodigy_events.txt` | 19 events (namespace `agot_morau_prodigy`, IDs 0001-0019) |
| `localization/english/morau_l_english.yml` | All English text |

### Event Flow
```
Birthday (age 6) or Decision
  -> 0001: Parent discovers prodigy, starts story cycle
    -> Story cycle stage 0 (2 years) -> 0002/0009: Brave vs Prowess
    -> Story cycle stage 1 (2 years) -> 0003/0010: Just vs Prowess
    -> Story cycle stage 2 (2 years) -> 0004/0011: Compassionate vs Prowess
    -> Story cycle stage 3 (2 years) -> 0005/0012: Blademaster trial (risk of death/maiming)
    -> Story cycle stage 4 (2 years) -> 0006: Night before judgment
    -> on_end:
       -> 0007: Dynasty head judges (worthy/unworthy/deny)
       -> 0008: Already a SotE exists
       -> 0013: Player prodigy self-judgment
       -> 0014: Player prodigy, already a SotE

  Failure events: 0018 (parent notified), 0019 (self notified)
  Theft event: 0015 (steal Twilight after denial)
  Notification: 0016 (parent told child succeeded)
  Death: 0017 (prodigy died during training)
```

### Character Flags
- `is_sword_of_evening` — character holds the title
- `claimed_twilight` — character has claimed the artifact (allows equipping)

### Key Triggers
- `is_dayne_bastard_house_trigger`: dynasty = dynn_Dayne AND house != house_Dayne
- `worthy_sword_of_the_evening_trigger`: Above + prowess >= 20 + martial education 4/5 or blademaster under 16 + brave/just + no disqualifying traits

## Development

### Testing
1. Enable submod in CK3 launcher alongside AGOT
2. Start as any character in a Dayne cadet branch
3. Console: `event agot_morau_prodigy.0001` to trigger the chain
4. The event auto-detects eligible children and sets up scopes

### Common Pitfalls
- Localization files MUST have BOM encoding — without it CK3 silently ignores the file
- Story cycle variables persist across stages; scope references do not — always re-save scopes from variables in each `triggered_effect`
- The `first_valid` block in story cycle `effect_group` means only the FIRST matching `triggered_effect` fires per cycle tick
- When checking for players, use `is_ai = no` not `is_player = yes` (which doesn't exist in CK3 scripting)

### Possible Improvements
- Add unique art/visuals for Twilight (currently uses Dawn's visuals)
- Add more varied event text for different personality combinations
- Add events for rivalry between Sword of Morning and Sword of Evening
- Add game rule to control spawn chance (5% default)
- Support for the prodigy system triggering on grandchildren directly
- Add duel events between SotE and SotM holders
