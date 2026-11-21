---
description: "Reprobates Atlas data contract: how timeline beats, locations, characters and map scenes are stored in this vault"
---
# Atlas data contract

This folder belongs to **Reprobates Atlas**, a desktop app that shows this vault as a map, a timeline, character cards and files. Everything the app knows is stored as plain files described here, so Obsidian, git and AI agents can read and write the same data. If you are an AI agent with only file access, this page is all you need.

Rules of thumb:
- The vault is the database. The app keeps no hidden data; it rebuilds its index from these files and reloads within about a second when they change.
- Write only what you mean to change. Edit frontmatter keys in place; never reformat a note's other keys or its body.
- Everything the app owns lives under `Atlas/`. Lore notes elsewhere only ever gain optional frontmatter keys.
- Check your work with `atlas validate <vault>` (it prints `file:line: error: message` and exits non-zero on errors).

## 1. Entities

Any note may say what it is with `type:`:

```yaml
type: character   # or: location, faction, item, event
```

The folder a note sits in never implies its type. References between entities are **always quoted wikilink strings**, and may point at a heading:

```yaml
affiliation: ["[[The Concord]]"]
location: "[[Supporting Cast#Chara|Chara]]"
```

Unquoted `[[Elion]]` in YAML is a nested list, not a link; `atlas validate` reports it.

**Heading entities.** Something that has no note of its own (Chara is only a heading in `Characters/Supporting Cast.md`) gets a stub note at `Atlas/Entities/<Name>.md`:

```yaml
---
type: character
source: "[[Supporting Cast#Chara]]"
---
```

The app shows the source heading's section as the stub's lore. `[[Chara]]` (the stub) and `[[Supporting Cast#Chara]]` (the heading) then mean the same entity everywhere, including timelines.

## 2. Characters

All keys optional and flat, so Obsidian's Properties panel can edit them:

| key | meaning |
| --- | --- |
| `type` | `character` |
| `portrait` | vault path of an image, e.g. `Atlas/Portraits/elion.png` |
| `aliases` | list of other names (used by search) |
| `role` | short text |
| `affiliation` | list of quoted wikilinks |
| `species` | short text |
| `born_year` | integer year (see dates below) |
| `card_style` | look of the character card: `protagonist` (gold), `antagonist` (red), `secondary` (blue), `supporting` (neutral, the default), `ally` (teal), `enigma` (violet) |
| anything else | any other flat key is shown on the card under "More" |

Existing `status` and `description` keys are read and never rewritten by the app.

## 3. Locations

| key | meaning |
| --- | --- |
| `type` | `location` |
| `parent` | quoted wikilink to the containing location; absent = top level |
| `map_kind` | places: `universe`, `galaxy`, `region`, `system`, `continent`, `city`, `district`, `building`, `room`, `other`; bodies: `star`, `planet`, `gas_giant`, `moon`, `dwarf_planet`, `asteroid`, `asteroid_belt`, `comet`, `ring` (planetary ring), `station`, `nebula`, `black_hole`. Moons and small bodies can be children of a planet: they orbit it on the planet's level and show as small companions next to it on the system level. A belt or ring is drawn as a circle around its parent's centre at its distance (`map_pos`), `map_size` wide |
| `map_pos` | `[x, y, z]` position inside the parent's scene, in parent-local units where the parent's radius is 100 |
| `map_size` | number, size inside the parent's scene (same units) |
| `map_inclination` | degrees; tilts the body's orbit (its position is rotated about the X axis). Bodies off the plane show a faint drop line |
| `map_scene` | optional `Atlas/Maps/<slug>.scene.json`; absent = default look for the `map_kind` |
| `map_color` | `#rrggbb` |
| `map_icon` | icon name |

Each map level is its own scene in its own units, so a galaxy and a room never share a coordinate system.

## 4. Timeline beats

One beat = one file `Atlas/Timeline/<Title>.md`. On a name clash append ` 2`, ` 3`. The filename never contains the date, so re-dating never renames; the file's identity is its path.

```yaml
---
type: beat
title: Elion enters the Capital
year: 0
day: 12
characters: ["[[Elion]]"]
location: "[[Concordian Capital]]"
move: arrive
timelines: [book-1]
status: provisional
description: "One line, so the Lore Index keeps working"
---
Free markdown body.
```

| key | meaning |
| --- | --- |
| `type` | `beat` (required) |
| `title` | text (required) |
| `year` | integer (required), relative to the epoch, range at least plus or minus 10^9 |
| `day` | optional integer, 1-365 (day of the standard year) |
| `hour`, `minute` | optional (need `day`); hour 0-23 and minute 0-59 in standard measure (calendars convert for display) |
| `approx` | `true` if the date is approximate |
| `end_year`, `end_day`, `end_hour`, `end_minute` | optional; make the beat a span |
| `order` | number; tie-break between beats on the same date |
| `characters` | list of quoted wikilinks |
| `location` | quoted wikilink |
| `move` | `arrive`, `depart` or `stay`; drives the living map |
| `timelines` | list of custom sub-timeline slugs from `atlas.yaml` |
| `status`, `description` | as on lore notes |
| `main_event` | optional quoted wikilink to another beat file: this beat is a sub-event of it (see Linked events) |
| `follows_main` | optional; `true` = this sub-event moves when its main event is re-dated |

**Dates.** Year 0 is the year that starts on Book 1's first day, so Book 1's first day is `year: 0, day: 1`. `year: -8` is the eighth year before it. There is a year 0. A beat without `day` covers the whole year and sorts at its start. Sort order: `year * 365 + (day - 1) + (hour + minute / 60) / 24`, then `order`, then `title`. In the app's date field you can type `-8`, `0 d12`, `0 d12 13:45` or `~-1000000` (the `~` sets `approx`); with a calendar defined, also `14 Frostmoon 1200 AE` or `14 Frostmoon -8 13:45` (a date in the active calendar). Stored dates never change when calendars change.

### Linked events

A beat can name another beat as its main event, on the sub-event only, as flat keys:

```yaml
main_event: "[[Soren awakens]]"   # quoted wikilink to the main event's file name
follows_main: true                 # optional; omit or false = the link is only organisational
```

- Re-dating a main event (in the app, or with `atlas set <beat> year|day|hour|minute ...`) shifts every sub-event with `follows_main: true` by the main's change, so its spacing stays the same. Spans move start and end. `approx` and every other key stay untouched. Sub-events of sub-events follow too when they opted in. Editing a main event's date by hand (in Obsidian or a text editor) does not move its sub-events; an agent that re-dates a main event should shift the followers itself, or use `atlas set`.
- Each sub-event keeps its own precision: a year-only sub-event moves by the change in the main event's year number (a whole-year move is exact; a move inside the same year leaves it alone); a sub-event with a day moves by the change in the main's day number (whole days); a sub-event with a time moves by the exact number of minutes. Moving the main back always restores the sub-events exactly.
- Deleting a main event unlinks its sub-events (removes their `main_event` and `follows_main`); the sub-events themselves stay. Undo puts the keys back.
- `atlas validate`: `main_event` must resolve to a beat other than itself, there must be no cycle of main events, and `follows_main` without `main_event` is a warning.

## 5. Derived timelines (never stored)

- Main: every beat.
- A character's timeline: beats whose `characters` include it.
- A location's timeline: beats whose `location` is it or any location inside it.
- A custom timeline: beats that list its slug in `timelines`.

## 6. `Atlas/atlas.yaml`

```yaml
epoch_label: "Book 1 begins"
days_per_year: 365   # used only when no calendar defines months
eras: []        # [{name, start_year, end_year, color}]
timelines: []   # [{slug, name, color, description}]
calendar: null  # unused (kept for older files)
root_location: null  # optional quoted wikilink to the map's top level
calendars: []        # optional, see below
active_calendar: null  # slug of the calendar used for display and typing
```

### Calendars (optional)

Event times are stored in **standard measures**, independent of any calendar: `year` counts 365-day years from the moment Book 1 begins, `day` is 1-365, `hour` 0-23, `minute` 0-59. Calendars only change how dates are shown and typed, through the absolute number of minutes since that moment. Editing or adding a calendar never changes or invalidates an event. Every calendar layer is optional and defaults to Earth.

```yaml
active_calendar: aurelian        # used for display and typing; default: the first calendar
calendars:
  - slug: aurelian
    name: Aurelian Reckoning
    year_zero_offset: 1200       # Book 1 begins in the calendar's year 1200
    epoch_month: 1               # ... in its first month (1-based; default 1)
    epoch_day: 1                 # ... on day 1 (default 1)
    epoch_hour: 0                # ... at hour 0 (default 0)
    before_label: BE             # optional era labels; without them years show as signed numbers
    after_label: AE
    has_year_zero: true          # false: ... 2 BE, 1 BE, 1 AE ...
    months:                      # default: the 12 Earth months (365 days); no leap years
      - { name: Frostmoon, days: 30 }
      - { name: Thaw, days: 30 }
    weekdays: [Oneday, Twoday, Threeday]   # any number; default Monday..Sunday
    week_start_offset: 0         # weekday index of Book 1's first day
    hours_per_day: 20            # default 24; hours are always 60 minutes, so this day lasts 1,200 minutes
  - slug: concord
    name: Concord Count
    epoch_day_offset: 100        # older form: Book 1 begins 100 days into the calendar's year 0
    months: [{ name: First, days: 200 }, { name: Second, days: 200 }]
```

Typing a calendar date (for example `14 Frostmoon 1200 AE 13:45`) converts it to the stored standard values; plain forms like `-8` or `0 d12` are always story years in standard measure. The `days_per_year` key is ignored for storage.

## 6b. `Atlas/view.json` (optional): shared view choices

Which lanes the timeline shows for each grouping. The app writes it when you pick lanes (Lanes menu, + Add lane, × on a lane); delete the file or a grouping to go back to automatic lanes.

```json
{
  "lanes": {
    "character": { "auto": false, "shown": ["Characters/Elion.md", "Atlas/Entities/Chara.md", "__none"] },
    "timeline": { "auto": true, "shown": [] }
  }
}
```

Groupings are `timeline` (threads, by slug), `character` and `location` (by vault path of the entity's note). `__none` is the "No thread / No character / No place" lane. `auto: true` shows every thread, and the characters and places that have events. Unknown keys are kept.

Preferences that belong to one computer rather than the vault (text size, reduce motion, the view the app opens on) are not stored in the vault.

## 7. Where is a character at a time T

The `location` of that character's latest beat at or before T that has a location, unless that beat's `move` is `depart`. After a `depart` the character is in transit until their next beat with a location, drawn as a path between the two places.

## 8. Map scenes `Atlas/Maps/<slug>.scene.json`

Declarative, validated by a JSON Schema (`atlas` exposes it; the app repo keeps it at `core/scene.schema.json`):

```json
{
  "version": 1,
  "preset": "galaxy",
  "seed": 7,
  "params": { "arms": 4, "core": 0.6, "dust": 0.4, "stars": 60000, "palette": ["#ffd39a", "#9fb4ff"] },
  "layers": [{ "type": "rings", "radii": [40, 60], "color": "#334466" }],
  "labels": [{ "text": "Outer Rim", "position": [70, 0, 10], "importance": 0.4 }]
}
```

Presets: `galaxy` (arms, bar, core, dust, stars, palette), `system` (star and orbits), `planet` (procedural colour or `texture`, atmosphere, clouds), `terrain` (heightmap image and colour ramp), `city` (block grid from the seed, or a footprint list), `plan` (a flat image or floor plan), `model` (a `.glb` in `Atlas/Maps/`, so you can model in Blender and drop the file in). Layers add points, rings, ribbons, sprites, image planes and text. An invalid scene shows a readable error and the default look, never a blank view.

`.scene.js` files are ignored unless the local user has approved that exact file; do not rely on them.

## 9. Round-trip rule

The app writes a file only when the user changed it, patches frontmatter text in place (keeping key order, quoting, comments and line endings) and never reformats bodies. Agents should do the same.
