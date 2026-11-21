# peakjar-data

The peak catalogue and mountain regions used by the [Peakjar](https://peakjar.com)
app, built from [OpenStreetMap](https://www.openstreetmap.org). The data is
published as [releases](https://github.com/getpeakjar/peakjar-data/releases);
this repository holds only this description and the licence notice.

## Releases

Each release is named `peaks-regions-osm-YYYY-MM-DD` after the OpenStreetMap
snapshot it was built from. Releases are never changed or deleted, so a pinned
version can always be downloaded again; new data is always a new release.

| File | Contents |
| --- | --- |
| `peaks.json` | GeoJSON points: every `natural=peak` and `natural=volcano` node in the covered countries |
| `regions.json` | GeoJSON polygons (and a few lines): named geomorphological units and mountain ranges in Poland and Slovakia |
| `snapshot.txt` | Time of the OpenStreetMap snapshot |
| `SHA256SUMS` | SHA-256 checksums of the files above |

Covered countries: Poland, Czechia, Slovakia, Austria, Switzerland,
Liechtenstein, Slovenia, Hungary and Romania.

**Peaks** have the OSM node as their id (`node/123`) and these properties,
each left out when empty:

| Property | Meaning |
| --- | --- |
| `name` | The Polish name when OpenStreetMap has one, else the local name; missing for unnamed peaks |
| `name_local` | The local name, when it differs from `name` |
| `ele` | Height in metres |
| `ele_approx` | `true` when the height comes from terrain data because OpenStreetMap has none |
| `wikipedia` | The OpenStreetMap `wikipedia` tag |
| `country` | ISO 3166-1 alpha-2 code of the country the peak is in |

**Regions** have the OSM relation or way as their id (`relation/123`) and
`name`, `name_local`, `region_type`, `boundary`, `natural`, `wikidata`,
`wikipedia` and `country`, with outlines simplified to about 30 m. Most are
polygons; mountain ranges mapped in OpenStreetMap as a single line stay lines.

### Download

```bash
version=peaks-regions-osm-2026-10-28
for file in peaks.json regions.json snapshot.txt SHA256SUMS; do
  curl -fsSLO "https://github.com/getpeakjar/peakjar-data/releases/download/$version/$file"
done
sha256sum -c SHA256SUMS
```

## How the data is made

Peakjar's map build downloads the [Geofabrik](https://download.geofabrik.de/)
extracts of the covered countries and, from the same snapshot as the map
tiles:

1. extracts every `natural=peak` and `natural=volcano` node, every
   `boundary=geomorphological-unit` relation and every `natural=mountain_range`
   feature;
2. assigns each peak the country whose border contains it, counting a peak
   within about 50 m of a border as in that country;
3. fills heights missing in OpenStreetMap from Copernicus WorldDEM-30 and marks
   them with `ele_approx`;
4. keeps only named regions in Poland and Slovakia, the covered countries
   mapped in enough detail, and simplifies their outlines to about 30 m.

Nothing else is added or changed. A wrong name or height comes from
OpenStreetMap: fix it at [openstreetmap.org](https://www.openstreetmap.org) and
it reaches the next release.

## Licence and credits

The data is © OpenStreetMap contributors and available under the
[Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/).
See [LICENSE.md](LICENSE.md) for the full notices, including the one for the
Copernicus terrain data behind approximate heights.

The vector tiles of the Peakjar map are also built from OpenStreetMap. A copy
of that data is available on request: write to
[hello@peakjar.com](mailto:hello@peakjar.com).
