# CLUMPY–Gammapy tutorial development

## Goal

Develop an official Gammapy tutorial showing how to import and use products generated with CLUMPY.

The imported products should have well-defined:

- physical quantities;
- units and normalization;
- angular conventions;
- geometry and coordinate frame;
- relation between spatial morphology and the integrated J- or D-factor.

The current work is therefore focused on defining conventions, supported formats, and validation tests rather than on writing the final tutorial.

## Reference material

The implementation will be based on three existing code bases:

1. Jaume's CLUMPY HEALPix-to-WCS conversion script, available in `dev/clumpy/prototypes/makeFitsImage_JaumeZuriaga.py`;
2. the CTAO dSph analysis material available in the `ksp_dsph_material` GitLab repository;
3. Titouan's exploratory notebook, available in `dev/clumpy/notebooks/tuto_clumpy_tgirault.ipynb`.

Jaume's script reads full- or partial-sky CLUMPY HEALPix FITS products, projects them to a Cartesian WCS image, propagates selected metadata, and applies different normalization steps depending on whether the input values are differential in solid angle. Titouan's notebook explores the import of J- and D-factor maps for an individual non-Galactic target, their projection to WCS, creation of a normalized `TemplateSpatialModel`, and computation of an integrated factor. These are prototype workflows: their assumptions about map dimensions, pixel solid angle, units, coordinate metadata, and normalization still need to be inventoried and validated.

The three implementations will first be analyzed separately and then compared.

## Scope

Our implementation and tutorial should cover both Galactic and extragalactic CLUMPY use cases. The CLUMPY 4.0 mode terminology must be kept explicit during this inventory:

- `-g` models the Galactic halo;
- `-h` models one or more halos other than the Galactic halo, including an individual isolated target such as a dSph;
- `-e` models extragalactic dark matter through the cosmological/extragalactic module.

An individual non-Galactic halo selected with `-h` must therefore not be conflated with the `-e` extragalactic module. In particular, the local CLUMPY 4.0 source identifies `-h2` as `Jsm+<sub>(alpha_int)`, `-h3` as `Jsm+<sub>(theta)`, `-h4` as `2D-skymap Jsm+<sub>(halo)`, and `-h5` as `2D-skymap Jsm+sub(halo)`. The Titouan notebook is an exploratory `-h` workflow even though its current title uses “extragalactic sources.” The relevant `-e` products and the most useful tutorial-level extragalactic use case remain to be identified; this requirement is not satisfied merely by supporting a dSph through `-h`.

Based on Jaume's script, Titouan's notebook, and, mainly, on the CTAO dSph analysis material, we can define a preliminary list of products to investigate:

- HEALPix J-factor maps;
- HEALPix D-factor maps;
- WCS J-factor maps;
- WCS D-factor maps;
- cumulative \(J(\alpha_{\rm int})\) profiles;
- cumulative \(D(\alpha_{\rm int})\) profiles;
- central estimates and uncertainty intervals for J- and D-factor profiles.

The correspondence between these generic product classes and the outputs of each of the `-g`, `-h`, and `-e` modes must be documented rather than assumed. This includes determining how annihilation and decay selections are encoded and labeled in each relevant CLUMPY product.

Uncertainty handling should be investigated after the basic import interface is established. The current branch provides `add_factor_prior`, which represents a J- or D-factor uncertainty through a prior on the dark-matter spectral model's scale parameter. This should be compared with the nuisance-parameter treatment in the CTAO dSph analysis, including the precise statistical interpretation of the quoted central values and intervals.

## Expected Gammapy representation

The following Gammapy representations are expected to be investigated:

- `HpxNDMap` for HEALPix products;
- `WcsNDMap` for WCS products;
- `TemplateSpatialModel` for normalized spatial morphologies;
- `DarkMatterAnnihilationSpectralModel` with an integrated J-factor;
- `DarkMatterDecaySpectralModel` with an integrated D-factor.

A key point to clarify is the separation

$$
\frac{dJ}{d\Omega}(\Omega)
=
J_{\mathrm{int}}\,T(\Omega),
$$

where \(T(\Omega)\) is a normalized spatial template and \(J_{\mathrm{int}}\) is the integrated astrophysical factor.

The same structure applies to the D-factor.

## Compatibility with JFactory

Compatibility with `JFactory` will initially be assessed at the semantic level:

- definition of the J- or D-factor;
- differential versus integrated quantities;
- physical units;
- angular conventions;
- map geometry;
- target centre and observer geometry;
- radial extent and integration domain.

The current branch includes a revised `JFactory` line-of-sight implementation that explicitly accounts for the near and far branches of the halo, together with numerical tests. This makes direct numerical comparison a concrete validation target, but does not by itself establish `JFactory` as the reference for CLUMPY products.

Before adopting such comparisons as reference tests, the profile parameters, integration boundaries, observer geometry, angular binning, and differential-versus-pixel-integrated convention must be matched explicitly between CLUMPY and `JFactory`.

## Validation strategy

The first validation tests will focus on compatibility and self-consistency. The following checks should be considered:

- conservation of the integrated J- or D-factor during map conversion;
- conversion between values per solid angle and values integrated per pixel;
- conservation under `RING`–`NESTED` reordering for HEALPix maps;
- consistency between HEALPix and WCS representations;
- consistency between a two-dimensional map and the corresponding cumulative \(J(\alpha)\) or \(D(\alpha)\) profile, where applicable;
- normalization of spatial templates;
- reconstruction of the physical map from a normalized template and an integrated factor;
- consistency of target coordinates and coordinate-frame transformations;
- stability with respect to angular resolution.

Uncertainty-related tests should be considered separately after the basic interface has been established.

## Proposed implementation phases

### Phase 1 — Inventory and comparison

- document the products used by Jaume's script, the CTAO material, and Titouan's notebook;
- classify each product and workflow by CLUMPY mode (`-g`, `-h`, or `-e`);
- identify their physical meaning, units, geometry, and normalization;
- record ambiguities and missing metadata;
- compare the three existing workflows;
- decide which Galactic, individual non-Galactic-halo, and extragalactic-module products should be covered by the tutorial.

### Phase 2 — Minimal product conventions

- define the minimum CLUMPY input formats to support;
- define the required FITS headers and table metadata;
- decide how J- and D-factor products should be represented in Gammapy;
- identify the conversions that can be performed safely.

### Phase 3 — Prototypes and validation

- implement minimal Galactic and extragalactic prototypes, treating `-h` and `-e` use cases separately where appropriate;
- test the selected map and profile conversions;
- verify units, normalization, geometry, and self-consistency;
- document any remaining limitations or ambiguities.

### Phase 4 — Tutorial writing

- select useful and self-contained examples from the validated exploratory notebooks and prototypes;
- implement a dedicated official CLUMPY import/use tutorial in Gammapy's Sphinx-Gallery `.py` format under `examples/tutorials`;
- add tests and small reference files;
- add a short cross-reference from the basic dark-matter tutorial to the dedicated CLUMPY tutorial, rather than incorporating the full CLUMPY workflow there;
- connect the new tutorial to the existing Gammapy dark-matter documentation.

## References

- the local CLUMPY 4.0 source and output conventions in `/home/jujuba/CLUMPY`, preferred over potentially outdated online documentation;
- Gammapy dark-matter documentation;
- Gammapy `JFactory`;
- Gammapy `TemplateSpatialModel`;
- Gammapy annihilation and decay spectral models and `add_factor_prior`;
- Jaume's prototype conversion script, `dev/clumpy/prototypes/makeFitsImage_JaumeZuriaga.py`;
- CTAO dSph analysis material;
- Titouan's exploratory notebook, `dev/clumpy/notebooks/tuto_clumpy_tgirault.ipynb`.
