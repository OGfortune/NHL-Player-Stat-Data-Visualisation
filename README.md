# NHL Player Stats Dashboard

[![Vega-Lite](https://img.shields.io/badge/Vega--Lite-v5-orange)](https://vega.github.io/vega-lite/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

An interactive, multi-view visualization of NHL career scoring. One dropdown swaps the entire dashboard between **goals** and **assists**; three linked views cross-filter one another.

<!-- Replace with a real screenshot or GIF once you have one:
     drag the image into a GitHub issue comment, copy the generated URL, paste it here. -->
![Dashboard screenshot](docs/screenshot.png)

## Demo

- **Live:** https://ogfortune.github.io/info_vis/ *(enable GitHub Pages on this repo — Settings → Pages → Deploy from branch → `main` / root)*
- **Vega Editor:** open [vega.github.io/editor](https://vega.github.io/editor/) and paste in `spec.json`

> [!NOTE]
> GitHub does not run JavaScript inside README files, so the chart cannot render on this page. Use one of the links above, or the local setup below.

## Features

- Dropdown to switch the measured stat across all views at once
- Interval brush on the scatter plot to filter both bar charts
- Click-to-filter on either bar chart, with cross-highlighting
- Tooltips showing player name, career total, and seasons played
- Retired vs. active players distinguished by mark shape; position by colour

## Views

| View | Type | Encoding |
|---|---|---|
| Career scatter (400×400) | Point | Career total (y) vs. seasons played (x); colour = position, shape = status |
| Totals by position (100×300) | Horizontal bar | Sum of selected stat per position, sorted descending |
| Totals by seasons played (600×100) | Vertical bar | Sum of selected stat grouped by seasons played |

## Data

The spec fetches its data over HTTPS at render time:

```
https://raw.githubusercontent.com/OGfortune/info_vis/main/new_season_goals.csv
```

| Column | Type | Notes |
|---|---|---|
| `player` | string | Shown in the scatter tooltip |
| `position` | string | Colour encoding + position bar chart |
| `status` | string | Must be exactly `Retired` or `Active` |
| `years_active` | number | Quantitative on the scatter, ordinal on the bottom bar |
| `goals` | number | Selectable stat |
| `assists` | number | Selectable stat |

A top-level transform copies whichever column the dropdown names into a synthetic `stat_value` field, so every view aggregates the same thing:

```json
{ "calculate": "datum[stat_selection]", "as": "stat_value" }
```

**Adding a metric:** add the column to the CSV, then append its name to the `options` array of the `stat_selection` param. Nothing else needs to change.

## Getting started

Clone and serve the directory — opening `index.html` over `file://` will trip CORS when it fetches the spec.

```bash
git clone https://github.com/OGfortune/info_vis.git
cd info_vis
python3 -m http.server 8000
# open http://localhost:8000
```

If you don't already have an `index.html`, this is enough:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>NHL Player Stats</title>
    <script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
    <script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
    <script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>
  </head>
  <body>
    <div id="vis"></div>
    <script>
      vegaEmbed("#vis", "spec.json");
    </script>
  </body>
</html>
```

## Project structure

```
.
├── spec.json              # Vega-Lite specification
├── index.html             # embed wrapper
├── new_season_goals.csv   # source data
├── docs/screenshot.png    # README image
└── README.md
```

## Known issues

Open items in the current spec — good first contributions:

- [ ] **Duplicate param names.** `bar_selection` and `bar2_selection` are each declared in more than one view. Vega-Lite expects unique param names; duplicates can raise a *"Duplicate signal name"* error or bind to the wrong view. Declare each once, in the view the user clicks.
- [ ] **Views filtering on their own selection.** The scatter declares `bar_selection` / `bar2_selection` and also filters on them; the position bar does the same with `bar2_selection`. A view filtering on its own selection removes its unselected marks, which is rarely the intent.
- [ ] **Stale v4 syntax.** The `"selection": "bar_selection"` key inside both colour conditions is a Vega-Lite v4 leftover, ignored in v5. Safe to delete — `"param"` does the work.
- [ ] **Bottom bar colour/opacity mismatch.** It conditions colour on `bar2_selection` (its own click target) but opacity on `bar_selection`, which it never declares.
- [ ] **Scatter ignores its own brush.** `scatter_selection` filters the bar charts but is never applied back to the scatter, so brushed points stay at full opacity.
- [ ] **Case-sensitive shape scale.** Any `status` value other than `Retired` or `Active` falls outside the scale domain and gets a default shape.

## Contributing

Issues and pull requests welcome. For spec changes, please validate in the [Vega Editor](https://vega.github.io/editor/) — it reports compilation warnings that a browser will silently swallow — and include a before/after screenshot.

## Built with

- [Vega-Lite](https://vega.github.io/vega-lite/) — grammar of interactive graphics
- [Vega-Embed](https://github.com/vega/vega-embed) — rendering

## License

MIT — see [LICENSE](LICENSE).
