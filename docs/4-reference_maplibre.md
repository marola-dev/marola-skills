# MapLibre skills

Which of MapLibre's own agent skills fit marola-site, read on 2026-11-12. marola-site's new map is
MapLibre GL JS 6.17.0 (`front/public/vendor/maplibre/INTEGRITY.txt`) over a self-hosted Protomaps
PMTiles file, its own style and glyphs, and NASA GIBS imagery (marola-site PR 195, `0dc4745`). The
legacy page is still Mapbox GL JS, with the seven `mapbox-*` skills in the
[catalogue](4-reference.md#marola-site--claudeskills).

Nothing here is copied: vendoring a skill is a PR in marola-site, through its `skills.lock` and
`skills-vendor` (MIP-0080).

## The source

[maplibre/maplibre-agent-skills](https://github.com/maplibre/maplibre-agent-skills) at
[`52567a6`](https://github.com/maplibre/maplibre-agent-skills/tree/52567a6e00c36f56c06fe30200f55a8caa54a2c0)
(2026-11-12): community-maintained under the MapLibre organisation, MIT (`LICENSE.md`, "Copyright
(c) 2026 MapLibre contributors"). Its `NOTICE` says it may adapt material from
mapbox/mapbox-agent-skills and keeps Mapbox's MIT copyright where it does; a vendored copy carries
both files. Each skill's front matter has a `metadata.status` (`verified`, `provisional` or
`process`), and `evals/` holds Promptfoo prompts and dated result CSVs. None of those evals was
run here (as [R8](4-reference_review.md#r8-evals-that-ship-but-have-never-been-run-here)).

`data/sources.json` now watches the repository, so the daily refresh lists its skills in the
[index](4-reference_index.md) and flags a pinned copy that falls behind.

## Verdicts

| Skill | Upstream status | Verdict for marola-site | Why |
|---|---|---|---|
| [`maplibre-pmtiles-patterns`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-pmtiles-patterns/SKILL.md) | verified | adopt | The site serves one PMTiles file from its own server; the skill covers the `pmtiles://` protocol, range requests and Planetiler/tippecanoe, and names Protomaps |
| [`maplibre-source-wiring`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-source-wiring/SKILL.md) | verified | adopt | Blank-map debugging: `source-layer` names against the tile schema (Protomaps' included), layer order below labels, `promoteId`, CORS and glyph failures |
| [`maplibre-fonts-glyphs`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-fonts-glyphs/SKILL.md) | provisional | adopt | `scripts/basemap_style.mjs` writes the site's own glyphs; the skill covers the `glyphs` URL and self-hosted font PBFs. Provisional upstream, so read its claims against the style |
| [`maplibre-cartography`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-cartography/SKILL.md) | verified | adopt | Label and marker legibility over imagery, which the GIBS layers put under the beach markers |
| [`maplibre-sprites-icons`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-sprites-icons/SKILL.md) | verified | adopt | `Marker` against sprite against `addImage`, and finding a missing icon; the beach markers are DOM `Marker`s today (`front/js/maplibre_ffi.mjs:104`), the choice the skill weighs |
| [`maplibre-mapbox-migration`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-mapbox-migration/SKILL.md) | provisional | adopt, until the cutover | The legacy page's move off Mapbox, seen from MapLibre's side; the counterpart of the `mapbox-maplibre-migration` that [R10](4-reference_review.md#r10-two-mapbox-skills-that-do-not-fit-marola-site) dropped. Remove it after the cutover |
| [`maplibre-tile-sources`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-tile-sources/SKILL.md) | verified | optional | GeoJSON against tiles by feature count; the boards and trails are small GeoJSON today, so it helps only if an area grows |
| [`maplibre-v6-migration`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-v6-migration/SKILL.md) | verified | skip | The site started on v6 (6.17.0); there is no v5 code to move |
| [`maplibre-terrain-rendering`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-terrain-rendering/SKILL.md) | verified | skip | No raster-dem source, hillshade or 3D terrain on the map |
| [`maplibre-native-gl-js-parity`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-native-gl-js-parity/SKILL.md) | verified | skip | No Android or iOS app |
| [`maplibre-running-evals`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-running-evals/SKILL.md) | process, `internal: true` | skip | Runs that repository's own Promptfoo evals |
| [`maplibre-skill-authoring`](https://github.com/maplibre/maplibre-agent-skills/blob/52567a6/skills/maplibre-skill-authoring/SKILL.md) | process | skip | Hands a session's findings back to upstream; useful only to contribute there |

The verdicts come from each skill's front matter and description read at `52567a6`, against
marola-site's `main` at `0dc4745`; no skill was run on the site.

## Other MapLibre skills looked at

Found with `gh api search/repositories -f q="maplibre skill"` and
`gh api search/code -f q='maplibre filename:SKILL.md'` on 2026-11-12, and not watched:

- [jaakla/openmapstack-skills](https://github.com/jaakla/openmapstack-skills) (MIT): GIS analysis
  on OSM, Overture, DuckDB and PostGIS with MapLibre output; closer to the OODS lake's data work
  than to the site, a candidate for [OODS lake skills](4-reference_oods.md).
- [mapsnippets/maplibre-skill](https://github.com/mapsnippets/maplibre-skill) (MIT): one skill
  built around MapTiler's basemaps, which the site does not use.
- Single-project skills (`defikarte/defikarte.ch`, `bbrauzzi/eo-rag`) and registry mirrors
  (`majiayu000/claude-skill-registry`, `gabrielmoreira/agent-skills-mirror`): not a maintained
  source.
