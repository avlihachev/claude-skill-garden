# Garden Skill

A universal [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill — your personal gardening assistant. Optimized for container growing but supports any format: raised beds, greenhouses, open ground.

## Features

- **Seasonal calendar** — automatic sowing, pricking out, hardening, and transplanting dates based on your last frost date
- **Plant tracker** — track all crops through their full lifecycle from planning to harvest
- **Garden journal** — chronological log of all actions and observations throughout the season
- **Smart recommendations** — on each invocation the skill checks what needs doing and warns about risks
- **Reference library** — temperature regimes, substrates, fertilizers, pests, containers

## Installation

Copy the skill folder into your Claude Code skills directory:

```bash
git clone https://github.com/avlihachev/claude-skill-garden.git ~/.claude/skills/garden
```

Or manually:

```bash
cp -r garden/ ~/.claude/skills/garden/
```

The skill is automatically detected by Claude Code on the next session start.

## File Structure

```
garden/
├── SKILL.md              # skill logic (engine)
├── profile.md            # user profile (location, conditions) — created on first run
├── plants.md             # current season plant list — created on first run
├── journal.md            # observation journal — created on first run
├── README.md             # this documentation
└── references/
    ├── timing.md         # temperatures, sowing schedules, grow lights, hardening
    ├── containers.md     # container types, volumes, materials, drainage
    ├── soil.md           # substrates, components, mix recipes, pH
    ├── fertilizers.md    # fertilizer types, NPK, feeding schedules
    └── pests.md          # pests, diseases, prevention, treatment
```

### Universal vs Personal

| Files | Type | Description |
|-------|------|-------------|
| `SKILL.md` + `references/` | Universal | Logic and reference library. Not tied to any location or user. |
| `profile.md`, `plants.md`, `journal.md` | Personal | Created on first run via interactive onboarding dialog. |

## First Run

On first gardening-related query, Claude starts an onboarding dialog:

1. City and country
2. Hardiness zone or last frost date
3. Growing format (containers / raised beds / greenhouse / open ground)
4. Indoor space for seedlings (windowsill, grow lights)
5. Outdoor space (balcony, terrace, yard)
6. Available space size (approximate area or number of container spots)
7. Soil and substrate (store-bought mix / custom blends / garden soil)
8. Experience level
9. Preferred seed suppliers (optional)
10. Task backend (Obsidian / Linear / OmniFocus / none) — with MCP availability check
11. Photo identification (drop photos into a folder for plant ID)

This creates your `profile.md` with all the data needed for personalized recommendations.

### Updating Your Profile

You can update your profile at any time:

- *"Update my profile — I got a greenhouse"*
- *"Change location to Berlin"*
- *"Add a new supplier"*

The skill also proactively suggests profile updates when it notices contradictions (e.g., you mention a greenhouse but your profile says "containers only").

### Task Management

During onboarding, choose where to track garden tasks:

| Backend | How it works | MCP required |
|---------|-------------|--------------|
| **Obsidian** | Writes `Garden Tasks.md` with checkboxes to your vault | No |
| **Linear** | Creates issues in a "Garden" project with labels and due dates | Yes — [official Linear MCP](https://linear.app/docs/mcp) |
| **OmniFocus** | Creates tasks in a "Garden" project with tags and due dates | Yes — [mcp-omnifocus](https://github.com/avlihachev/mcp-omnifocus) |
| **None** | Tasks only shown in conversation | No |

The skill checks MCP availability when you select Linear or OmniFocus, and provides installation instructions if needed. You can switch backends anytime by updating your profile.

### Photo Identification

Drop photos of any plants — houseplants, garden beds, outdoor trees, even wild plants — into a designated folder in your Obsidian vault. Then ask Claude to identify them:

- *"Identify my plants"*
- *"What's growing in the photos?"*

For each photo, the skill reports:
- Species and variety (or best guess)
- Growth stage and health assessment
- Visible problems (pests, deficiency, disease)
- Location context (indoor/outdoor/container)
- For houseplants: light and watering needs

After your confirmation, identified plants are added to `plants.md` and a journal entry is created. Processed photos are moved to a `processed/` subfolder.

## Usage

### General Questions

Just ask about gardening — the skill activates automatically:

- *"When should I sow tomatoes?"*
- *"What soil mix do peppers need?"*
- *"My seedlings have yellowing lower leaves, what's wrong?"*
- *"What container size for indeterminate tomatoes?"*

### Adding Plants

Tell Claude you want to add a plant — it creates an entry in `plants.md`:

- *"Add cherry tomato Tigerella, seeds from Impecta"*
- *"I want to try growing strawberries in containers"*

### Journal

Report actions — the skill logs them in `journal.md` and updates plant statuses:

- *"Sowed basil Dark Opal today, seed-starting mix, covered with film"*
- *"Pricked out Venus tomatoes into 10 cm pots"*
- *"Peppers showing first true leaves"*

### Recommendations

On each invocation the skill evaluates:

- Current date vs seasonal formulas — what should be done now?
- Plant statuses — any overdue stages?
- Recent journal entries — any issues needing follow-up?

## Seasonal Logic

All dates are computed relative to `last_frost_date` from your profile. No hardcoded calendars.

### Formulas

| Event | Formula |
|-------|---------|
| Sow peppers/chili indoors | last_frost - 10..12 weeks |
| Sow tomatoes (determinate) | last_frost - 8..10 weeks |
| Sow tomatoes (indeterminate) | last_frost - 6..8 weeks |
| Sow basil | last_frost - 6..8 weeks |
| Start hardening | transplant_date - 14 days |
| Transplant tender crops outdoors | last_frost + 1..2 weeks |
| Bring tender perennials indoors (fall) | first_frost - 2 weeks |

### Example

If your `last_frost_date = June 15`:
- Sow peppers: late March — early April
- Sow determinate tomatoes: mid April
- Start hardening: early June
- Transplant outdoors: late June

## Plant Lifecycle

Each plant in `plants.md` moves through statuses:

```
planned → sown → germinating → seedling → hardening → outdoor → harvesting → done
                                                                              ↘ failed
```

When finished (done/failed), the plant moves to the **Archived** section with an outcome description.

## Reference Library

### timing.md
- Germination temperatures by crop family
- Seedling temperature regimes (day/night)
- Grow light parameters (height, hours, spectrum)
- Sowing lead times (weeks before last_frost)
- Transplant timing and minimum night temperatures
- Universal 2-week hardening schedule

### containers.md
- Recommended volumes by crop type (from 1L for basil to 20L for indeterminate tomatoes)
- Material comparison: plastic, fabric, ceramic, grow bags, soil bags
- Drainage requirements
- Temperature management (overheating, cold night insulation)
- Overwintering container perennials

### soil.md
- Substrate types: seed-starting, potting, universal, vegetable
- Components: peat, coir, perlite, vermiculite, compost
- Mix recipes by use case
- pH ranges by crop
- Substrate management: replacement, common problems (salt buildup, hydrophobicity, compaction)

### fertilizers.md
- Fertilizer types: slow-release, liquid, organic, foliar
- NPK: what each nutrient does, deficiency signs
- NPK ratios by growth stage
- Feeding schedules: seedling → vegetative → flowering → fruiting
- Container specifics: leaching, salt buildup, flushing

### pests.md
- Pests: aphids, whitefly, spider mites, fungus gnats, slugs
- Diseases: damping off, powdery mildew, blossom end rot, late blight
- For each: symptoms, prevention, treatment
- Container-specific considerations

## Customization

### Changing Location

Edit `profile.md` — update city, latitude, and frost dates. All recommendations recalculate automatically.

### New Season

At the start of a new season:
1. Move all plants from Active to Archived in `plants.md`
2. Update the heading: `# My Plants — [new year] Season`
3. Create a new `journal.md` (optionally rename the old one to `journal-2026.md`)
4. Add new plants

### Sharing

To share the skill with another user:
1. Share `SKILL.md` and the `references/` folder — this is the universal part
2. Remove `profile.md`, `plants.md`, `journal.md` — they will be created on first run via onboarding

## Roadmap

**Phase 2: Garden MCP Server**

A custom MCP server that will add:
- Weather forecasts and frost alerts for your location
- Soil temperature estimation
- Automatic seasonal recommendations based on real weather data
- Historical weather data for trend analysis

The skill will detect MCP server availability and use it when present, falling back to manual mode when absent.

## License

MIT
