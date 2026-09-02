# Character Turnaround

[English](README.md) · [繁體中文](README.zh.md)

A reusable Skill that turns one front-facing character reference into a confirmed Character Bible and four independent character reference images: front, three-quarter front, side, and back.

It is designed for characters, mascots, branded IP, games, animation, comics, and picture books. The output is compatible with the `ip-asset-generator` Skill for creating later scenes, stickers, social graphics, and transparent character assets.

## What it does

- Analyzes observable character identity from one front reference.
- Separates confirmed facts, reasonable inferences, and details that need confirmation.
- Locks identity anchors, fixed rules, allowed variations, and visual language.
- Requires confirmation before formal generation.
- Generates four separate images with consistent scale, baseline, pose, and visual treatment.
- Runs per-view QA and retries only a failed view once.

## Quick start

### Install for Codex

```bash
git clone https://github.com/Xuanwinnie/character-turnaround.git \
  ~/.codex/skills/character-turnaround
```

### Install for Claude Code

```bash
git clone https://github.com/Xuanwinnie/character-turnaround.git \
  ~/.claude/skills/character-turnaround
```

Restart the relevant AI tool, then provide one clear front-facing character reference.

```text
Use character-turnaround to analyze this front-facing character reference.
First create a Character Bible plan and separate confirmed facts,
reasonable inferences, and details that need confirmation.
Do not generate the formal reference images until I confirm the settings.
```

After confirming the settings:

```text
The Character Bible is confirmed.
Generate the four independent reference images:
front, three-quarter front, side, and back.
Keep the same character scale, baseline, neutral pose, camera distance,
visual style, lighting, and background treatment.
Do not combine the views into one sheet.
```

## Workflow

```text
Front reference image
        ↓
Analyze identity and visual language
        ↓
Separate confirmed / inferred / needs confirmation
        ↓
User confirms the Character Bible
        ↓
Generate four independent views
        ↓
Run per-view consistency QA
        ↓
Deliver the reference pack for IP Asset Generator
```

## Core output

```text
<character-id>-front.png
<character-id>-three-quarter-front.png
<character-id>-side.png
<character-id>-back.png
<character-id>-bible.yaml
turnaround-qa.yaml
```

The original front reference may be retained as the front image when requested. If a character ID is available, use `<character-id>-bible.yaml`; otherwise use `character-bible.yaml`. The three-quarter front view is included because it provides a stronger bridge between facial identity and side-view structure.

## Character Bible and IP Bible

`character-bible.yaml` is the turnaround-stage IP Bible. It uses the same core fields as `ip-asset-generator`, including:

- `identity_anchors`
- `fixed_rules`
- `allowed_variations`
- `visual_language`
- `reference_confidence`

It also records turnaround-specific source references, unknown details, confirmation status, and view requirements. Use this one file as the source of truth; do not maintain a duplicate IP Bible unless a downstream tool requires an exact legacy format.

When using `ip-asset-generator`, provide the confirmed `character-bible.yaml` together with the four reference images as one reference pack.

## Suggested supplemental references

These are recommended additions, not required core outputs:

- head and face close-up;
- signature accessory close-up;
- hands, feet, tail, wings, or other unusual structures;
- neutral full-body proportion reference;
- common expression references;
- useful pose references;
- approved color and material references.

For long-term IP production, use the complete character baseline package: the Character Bible, four core views, expression sheet, pose plan, costume plan, color rules, and future asset recommendations.

## Consistency rules

The Skill preserves species or role, face shape, body proportions, age impression, signature colors, permanent markings, signature accessories, and visual language. It must not invent unconfirmed back details as permanent facts, add text or logos, add props or extra characters, or merge the four views into a contact sheet.

No image model guarantees pixel-identical results. The Skill reports identity drift and retries only the failed view once.

## Repository structure

```text
character-turnaround/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── character-bible-schema.md
│   └── turnaround-qa-template.yaml
├── README.md
└── README.zh.md
```

## License

Use the Skill instructions and templates according to the license configured for this repository. Check usage rights separately for character artwork, brand assets, and third-party references.
