# CLUMPY J-factor and D-factor support in Gammapy

This directory contains the development work for importing CLUMPY J-factor and D-factor products into Gammapy. The goal is to produce an official Gammapy-CLUMPY tutorial, together with the readers and validation tests required to support the workflow.

The tutorial should guide users through products that can be converted directly into Gammapy spatial models. **This implementation therefore focuses on two-dimensional J- and D-factor maps and on the information required to interpret their physical quantity, units, geometry, components, and normalization.**

Our main references are:

- the current CLUMPY development source and its in-tree documentation;
- Jaume's code in `dev/clumpy/prototypes/makeFitsImage_JaumeZuriaga.py`;
- Titouan's notebook in `dev/clumpy/notebooks/tuto_clumpy_tgirault.ipynb`;
- the CTAO dSph analysis material in the `ksp_dsph_material` repository on GitLab;
- the current Gammapy API.

## Scope

This work will cover J- and D-factor maps for:

- the Galactic halo, either alone or combined in the same map with a user-defined list of additional halos;
- a selected non-Galactic halo (e.g. a dwarf spheroidal galaxy or another isolated target).

In the current CLUMPY development source, these cases are handled by different modes:

1. `-g`: models the MW halo and additional galactic subhalos;
2. `-h`: models extragalactic halos.

NOTE: CLUMPY also provides a cosmological `-e` module for products such as halo-population calculations, extragalactic intensities, and absorption. Those products are not the J- or D-factor map products considered here and are outside the scope of this work. Here, “extragalactic target” refers to a halo represented individually or as part of a user-defined halo list, not to a cosmological `-e` simulation.

NOTE: Statistical products from CLUMPY's `-s` module, including confidence intervals for J and D, are also outside the initial tutorial scope. **They may become relevant in a later extension of the Gammapy dark-matter module together with appropriate likelihood treatment.**

## Initial implementation

The initial implementation should support:

1. CLUMPY HEALPix J- and D-factor maps with values integrated over each pixel.
2. CLUMPY HEALPix J- and D-factor maps with differential values in solid angle.
3. Optional conversion of the imported HEALPix map to WCS within Gammapy, preserving the integrated J- or D-factor.
4. Construction of a normalized `TemplateSpatialModel`, providing an associated integrated J- or D-factor.


## Relevant CLUMPY modes and products

### Galactic maps

| Mode | CLUMPY product |
|-|-|
| `-g5` | Two-dimensional smooth plus mean-substructure map |
| `-g6` | `g5` product + supplied list of objects |
| `-g7` | Two-dimensional map with drawn or resolved subhalos |
| `-g8` | `g7` product + supplied list of objects |

### Individual extragalactic halo maps

| Mode | CLUMPY product |
|-|-|
| `-h4` | Two-dimensional smooth plus mean-substructure map |
| `-h5` | Two-dimensional map including drawn subhalos |

Modes `-h4` and `-h5` compute the map for one halo selected from the input halo definitions. Multi-halo maps are represented by `-g6` and `-g8`, which add a user-defined halo list to the Galactic map. The import workflow should therefore support both single-halo and multi-halo map products.

## Product inventory

| Priority | Product | CLUMPY quantity and format | Modes | Gammapy (possible) representations |
|---|---|---|---|---|
| Initial | Pixel-integrated J map | HEALPix FITS binary table, `JFACTOR` extension | `-g5` to `-g8`, `-h4`, `-h5` | `HpxNDMap` and `TemplateSpatialModel` |
| Initial | Pixel-integrated D map | HEALPix FITS binary table, `DFACTOR` extension | `-g5` to `-g8`, `-h4`, `-h5` | `HpxNDMap` and `TemplateSpatialModel` |
| Initial | Differential J map | HEALPix FITS binary table, `JFACTOR_PER_SR` extension | `-g5` to `-g8`, `-h4`, `-h5` | `HpxNDMap` and `TemplateSpatialModel` |
| Initial | Differential D map | HEALPix FITS binary table, `DFACTOR_PER_SR` extension | `-g5` to `-g8`, `-h4`, `-h5` | `HpxNDMap` and `TemplateSpatialModel` |
| Follow-up | Cumulative J or D profile | Integrated factor versus aperture | `-g2`, `-h2` | Integrated factor extraction and validation |
| Follow-up | Differential angular profile | Factor per solid angle versus angular separation | `-g3`, `-h3` | Radial template construction |
| Future DM module extension | Statistical J or D profile | Central estimates and confidence intervals | `-s6`, `-s7` | Likelihood or nuisance-parameter treatment not yet defined |

NOTE: All map columns should (in my opinion) be preserved by a reader even if the first public interface selects the total component by default.

## CLUMPY map conventions

CLUMPY two-dimensional outputs are HEALPix FITS binary tables. They may be full-sky or partial-sky, use implicit or explicit pixel indexing, and use `RING` or `NESTED` ordering.

For annihilation, native files can contain:

- `JFACTOR`, with values integrated over each HEALPix pixel;
- `JFACTOR_PER_SR`, with values averaged per unit solid angle;
- columns such as `Jtot`, `Jsmooth`, `Jsub`, `Jcrossp`, `Jdrawn`, and `Jlist`, depending on the mode and configuration.

Decay uses analogous files.

Relevant FITS metadata (we should pay attention to these!) include:

- `PIXTYPE`, `NSIDE`, `ORDERING`, `INDXSCHM`, and `COORDSYS`;
- `SIMUMODE` and `DMMODEL`;
- the selected unit convention;
- target position, field of view, density profile, and substructure parameters.

NOTE: the differential values are angular averages computed with CLUMPY's oversampling procedure. They must not be reinterpreted as a single line-of-sight evaluation at the pixel centre!

## Physical conventions

For annihilation,

```math
J(\Delta\Omega)
=
\int_{\Delta\Omega} d\Omega
\int_{\mathrm{l.o.s.}} dl\,\rho^2.
```

For decay,

```math
D(\Delta\Omega)
=
\int_{\Delta\Omega} d\Omega
\int_{\mathrm{l.o.s.}} dl\,\rho.
```

NOTE: CLUMPY does not include the particle-physics $1/(4\pi)$ term in J or D. The Gammapy import must not add or remove this factor.

CLUMPY supports two unit families:

| Convention | J | D |
|---|---|---|
| Particle physics | `GeV2 cm-5` | `GeV cm-2` |
| Astrophysical | `M_sun2 kpc-5` | `M_sun kpc-2` |

Differential values additionally carry `sr-1`. The reader should accept both unit families and convert them explicitly where required by a Gammapy model.

### Pixel-integrated and differential maps

For a pixel-integrated map,

```math
J_{\mathrm{int}} = \sum_i J_{\mathrm{pix},i}.
```

For a differential map,

```math
J_{\mathrm{int}}
=
\sum_i
\left(\frac{dJ}{d\Omega}\right)_i
\Delta\Omega_i.
```

The same definitions apply to D. Conversion between the two forms must use the solid angle of each pixel.

### Spatial morphology and integrated factor

The current Gammapy DM module treatment of J and D-factors separates morphology from normalization:

```math
\frac{dJ}{d\Omega}(\Omega)
=
J_{\mathrm{int}}\,T(\Omega),
```

with

```math
\int T(\Omega)\,d\Omega = 1.
```

For pixel-integrated input,

```math
T_i
=
\frac{J_{\mathrm{pix},i}}
{J_{\mathrm{int}}\,\Delta\Omega_i}.
```

For differential input,

```math
T_i
=
\frac{1}{J_{\mathrm{int}}}
\left(\frac{dJ}{d\Omega}\right)_i.
```

This distinction must be handled before creating a `TemplateSpatialModel` or converting the imported map to WCS. An optional HEALPix-to-WCS conversion must account for the input and output pixel solid angles and preserve the integrated J- or D-factor.

## Current Gammapy compatibility

The CLUMPY-distributed example `$CLUMPY/data/jfactor_map_from_h5.fits` is not currently recognized by the generic `Map.read()` convention detector. It is a partial-sky, explicitly indexed, `NESTED`, `NSIDE=1024` file with a `PIXEL` column and `JFACTOR` and `JFACTOR_PER_SR` extensions.

This is an interface gap rather than a geometry limitation: Gammapy can represent partial-sky HEALPix maps, but it needs either a CLUMPY-specific reader or an additional recognized FITS convention.

## Recommended implementation: reader and conversions

I think that a  dedicated reader is preferable to placing CLUMPY-specific heuristics in the tutorial or in generic FITS-map detection. One CLUMPY file may contain multiple physical components, both pixel-integrated and per-solid-angle extensions, explicit pixel indices, and model metadata.

The import and conversion workflow should:

1. identify J or D from consistent headers, extensions, columns, and units;
2. reconstruct full- or partial-sky HEALPix geometry;
3. preserve ordering and coordinate metadata;
4. deal with pixel-integrated and differential quantities without ambiguity;
5. select components;
6. convert native CLUMPY HEALPix maps to WCS when requested, accounting for the input and output pixel solid angles and preserving the integrated J- or D-factor;
7. calculate an integrated factor over a declared region;
8. produce a unit-normalized spatial template.

An example of a possible high-level map interface is:

```python
product = ClumpyMap.read(
    filename,
    factor="j",
    component="total",
    ...,
)
```

The object could expose both the physical map and a normalized template, together with the integrated factor calculated over the selected region.


## To decide

- Should CLUMPY support use a dedicated reader or a `Map.read()` FITS convention? (I prefer the reader idea :) )
- Which CLUMPY versions and header variants must be supported?
- Which physical component should be selected by default?


## Tutorial dev

Development will continue in notebooks and standalone prototypes. Once the map-inputs and validation tests are stable, the final contribution should be a Sphinx-Gallery tutorial under `examples/tutorials`. The basic dark-matter tutorial could contain only a short cross-reference to the dedicated CLUMPY workflow tutorial.

## References

- the current CLUMPY development source and its in-tree documentation;
- [CLUMPY repository](https://gitlab.com/clumpy/CLUMPY);
- [CLUMPY two-dimensional map documentation](https://clumpy.gitlab.io/CLUMPY/v3.1.1/physics_2Dmaps.html);
- [Gammapy HEALPix map documentation](https://docs.gammapy.org/dev/user-guide/maps/hpxmap.html);
- [Gammapy `HpxMap`](https://docs.gammapy.org/dev/api/gammapy.maps.HpxMap.html);
- [Gammapy `TemplateSpatialModel`](https://docs.gammapy.org/dev/api/gammapy.modeling.models.TemplateSpatialModel.html);
- [Gammapy `JFactory`](https://docs.gammapy.org/dev/api/gammapy.astro.darkmatter.JFactory.html);
- [Gammapy dark-matter tutorial](https://docs.gammapy.org/dev/tutorials/astrophysics/dark_matter_basics.html);
- Jaume's prototype in `dev/clumpy/prototypes/makeFitsImage_JaumeZuriaga.py`;
- Titouan's exploratory notebook in `dev/clumpy/notebooks/tuto_clumpy_tgirault.ipynb`;
- CTAO dSph analysis material in the `ksp_dsph_material` repository.

The current CLUMPY development source used in this work identifies its executable and generated FITS products as version 4.0, although its CMake project metadata declares version 3.2.0. The latest tagged release and public online documentation are version 3.1.1.
