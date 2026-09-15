# Hephaestus Lite

Hephaestus Lite is a small, static browser application for exploring elemental
X-ray data. It is inspired by Bruce Ravel's
[HEPHAESTUS](https://bruceravel.github.io/demeter/aug/hephaestus.html), the
periodic-table and reference-data utility distributed with DEMETER.

This project is not a port of DEMETER. It is an independent HTML/JavaScript
interface focused on a smaller set of reference tools that can run without a
server-side application.

## Features

- Find an element by symbol, name, or atomic number.
- Plot mass attenuation coefficient as a function of photon energy.
- Inspect absorption edges, core-hole widths, fluorescence yields, and jump
  ratios.
- Inspect characteristic X-ray emission energies, transitions, and relative
  intensities.
- Browse Shannon ionic and crystal radii by oxidation state, coordination, and
  spin state.
- Plot the neutral-atom form factor, \(f_0(q)\).
- Plot anomalous scattering factors \(f'\) and \(f''\) versus photon energy.
- Export plotted values as CSV.
- Zoom by drawing a rectangle, zoom with the mouse wheel, pan with
  **Shift + drag**, and reset with a right-click.

## Running locally

The application is static, but it must be served over HTTP so the browser can
load the JSON files.

```bash
python3 -m http.server 8000 --directory dist
```

Open <http://localhost:8000>.

To deploy elsewhere, publish the contents of `dist/` as the web root. The
application expects its data files under `./data/`.

## Data coverage

The interface recognizes all 118 named elements. Numerical coverage reflects
the limits of the underlying reference tables; unavailable values are not
extrapolated.

| Dataset | Elements |
| --- | ---: |
| Element registry | 118 |
| Chantler attenuation and anomalous scattering | 92 |
| Waasmaier–Kirfel neutral-atom form factors | 98 |
| Shannon radii | 96 |

## Data sources

The browser-ready JSON files were generated from the
[XrayDB SQLite database](https://github.com/xraypy/XrayDB) and a JSON
transcription of the Shannon radii table. XrayDB collects atomic properties,
characteristic X-ray energies, cross sections, and scattering data in a single
SQLite database. Its schema and units are described in the
[XrayDB documentation](https://xraypy.github.io/XrayDB/).

The principal scientific references are:

- **Edges and emission lines:** W. T. Elam, B. Ravel, and J. R. Sieber,
  “A new atomic database for X-ray spectroscopic calculations,”
  *Radiation Physics and Chemistry* **63** (2002), 121–128.
- **Mass attenuation and anomalous scattering:** C. T. Chantler,
  “Theoretical form factor, attenuation, and scattering tabulation for
  Z = 1–92,” *Journal of Physical and Chemical Reference Data* **24** (1995),
  71. See the
  [NIST form-factor and attenuation tables](https://physics.nist.gov/PhysRefData/FFast/html/form.html).
- **Neutral-atom form factors:** D. Waasmaier and A. Kirfel,
  “New analytical scattering-factor functions for free atoms and ions,”
  *Acta Crystallographica A* **51** (1995), 416–431,
  [doi:10.1107/S0108767394013292](https://doi.org/10.1107/S0108767394013292).
- **Ionic and crystal radii:** R. D. Shannon,
  “Revised effective ionic radii and systematic studies of interatomic
  distances in halides and chalcogenides,” *Acta Crystallographica A* **32**
  (1976), 751–767,
  [doi:10.1107/S0567739476001551](https://doi.org/10.1107/S0567739476001551).

The original HEPHAESTUS documentation also provides a useful overview of these
tables and their historical use in X-ray absorption spectroscopy:
[HEPHAESTUS — A Periodic Table for the X-ray Absorption Spectroscopist](https://bruceravel.github.io/demeter/aug/hephaestus.html).

## Data files

| File | Contents |
| --- | --- |
| `dist/data/elements.json` | Element properties, edges, widths, yields, and emission lines |
| `dist/data/absorption.json` | Chantler energy and mass-attenuation arrays |
| `dist/data/scattering.json` | Chantler \(f'\) and \(f''\) arrays |
| `dist/data/form-factors.json` | Waasmaier–Kirfel coefficients for neutral atoms |
| `dist/data/shannon-radii.json` | Shannon radii indexed by element and oxidation state |

The neutral-atom form factor is evaluated as

\[
f_0(q) = c + \sum_i a_i e^{-b_iq^2},
\]

where \(q = \sin(\theta)/\lambda\), and the coefficients are read from the
Waasmaier–Kirfel table.

## Rebuilding the JSON files

```bash
python3 tools/build_data.py /path/to/xraydb.sqlite \
  /path/to/shannon-radii.json dist/data
```

The generator keeps the published source coverage and retains extra sampling
points near absorption edges. It does not invent values for elements absent
from a source table.

## Project layout

```text
dist/
  index.html
  data/
tools/
  build_data.py
README.md
```

## Notes

- Energies are expressed in electronvolts (eV).
- Mass attenuation coefficients are expressed in cm²/g.
- Shannon radii are expressed in ångströms (Å).
- This is a reference and visualization tool, not a substitute for checking the
  primary literature when high-accuracy values are required.
- DEMETER and HEPHAESTUS are associated with Bruce Ravel; this independent
  browser application is not an official DEMETER release.
