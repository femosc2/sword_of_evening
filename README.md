# Sword of the Evening

A **CK3 AGOT submod** that adds a parallel prodigy system for cadet branches of House Dayne. While the trueborn Daynes compete for the title of *Sword of the Morning* and the right to wield *Dawn*, members of Dayne cadet houses can train to become the **Sword of the Evening** and claim the Valyrian steel sword **Twilight**.

## Features

- **5-stage story cycle** mirroring the Sword of the Morning system — children train over ~10 years through tests of bravery, justice, compassion, and martial skill
- **Twilight** — a Valyrian steel artifact with stats matching Dawn (+10 prowess, +0.5 monthly prestige, +15 general opinion, +5% dynasty prestige, +10% martial lifestyle XP)
- **"Sword of the Evening" nickname** granted to those who prove worthy
- **Automatic triggering** — 5% chance on 6th birthday for eligible children in Dayne cadet branches
- **Training decision** — manually start the prodigy chain for any eligible child
- **Claim decisions** — worthy characters can formally claim Twilight; unworthy characters can seize it at the cost of 1000 prestige
- **Dynasty head interaction** — the Dayne dynast can offer Twilight to a worthy cadet branch member
- **Full event chain** — 19 events covering parent-guided training, self-guided training (if parents are dead), success, failure, theft, death, and notification events

## Requirements

- [Crusader Kings III](https://store.steampowered.com/app/1158310/Crusader_Kings_III/)
- [A Game of Thrones (AGOT)](https://steamcommunity.com/sharedfiles/filedetails/?id=2962333032) mod

## Installation

1. Download or clone this repository into your CK3 mod folder:
   ```
   Documents/Paradox Interactive/Crusader Kings III/mod/
   ```
2. Place the `morau_sword_of_evening.mod` descriptor file in the `mod/` directory (alongside the folder)
3. Enable **"Sword of the Evening - Morau Prodigy"** in the CK3 launcher alongside AGOT

The `.mod` descriptor file is not included in this repo since it contains a local path. Create it with this content, adjusting the path to match your system:

```
version="1.0.0"
tags={
    "Alternative History"
    "Gameplay"
}
name="Sword of the Evening - Morau Prodigy"
dependencies={
    "A Game of Thrones"
}
supported_version="1.*"
path="C:/Users/YourName/Documents/Paradox Interactive/Crusader Kings III/mod/morau_sword_of_evening"
```

## Eligibility

Any character who is:
- Part of the **Dayne dynasty** (`dynn_Dayne`)
- **Not** in the main House Dayne (i.e., in any cadet branch)

This means the mod works for House Morau, or any other cadet branch of House Dayne — whether founded by a bastard or otherwise.

## How It Works

1. When an eligible child turns 6, there's a 5% chance their parent gets event 0001 (or use the "Train a Child as the Sword of the Evening" decision)
2. The parent chooses to set them on the path
3. Every 2 years, a training event fires with choices between personality traits (brave/just/compassionate) or raw prowess training
4. Bad trait outcomes end the story cycle early
5. After all 5 stages, the dynasty head (or the prodigy themselves if player-controlled) judges worthiness
6. Worthy prodigies receive Twilight, the "Sword of the Evening" nickname, and massive prestige

## Console Testing

```
event agot_morau_prodigy.0001
```

Fire this on a parent character — it will automatically pick an eligible child. Or switch to the child with `play <char_id>` first.

## File Structure

```
common/
  artifacts/templates/    - Twilight artifact template
  character_interactions/ - Offer Twilight interaction
  decisions/              - Claim and training decisions
  modifiers/              - Twilight stat modifier
  nicknames/              - "the Sword of the Evening"
  on_action/              - 6th birthday hook
  scripted_effects/       - Twilight creation effect
  scripted_triggers/      - Eligibility and worthiness triggers
  story_cycles/           - 5-stage prodigy training cycle
events/                   - 19 prodigy events (namespace: agot_morau_prodigy)
localization/english/     - All English localization
```

## Compatibility

This mod is fully additive — it does not modify any AGOT files. It uses CK3's file merging to hook into the existing `on_6th_birthday` on_action.

## License

Feel free to use, modify, and redistribute. Credit appreciated but not required.
