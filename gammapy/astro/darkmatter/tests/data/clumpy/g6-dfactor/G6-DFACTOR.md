# g6 D-factor: Galactic map with listed haloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g6`. It combines the Milky Way components from `g5` with resolved haloes read from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 11.4.0
- GSL: 2.7.1
- CFITSIO: 4.0.0

The configuration matches the `g6` J-factor reference product, except that `gPP_DM_IS_ANNIHIL_OR_DECAY=0` selects dark-matter decay.

## Generation

Run from this directory:

```bash
clumpy -g6 -p -i clumpy_params_g6_dfactor.txt
```

The command creates:

- `output/decay_gal2D_LOS0_0_FOV90x45_nside32.fits`: the native HEALPix map containing the D-factor components;
- `output/decay_gal2D_LOS0_0_FOV90x45_nside32.list`: an auxiliary text file describing the haloes from the input list that were added to the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The map contains 1,416 explicitly indexed pixels covering the default `90 deg x 45 deg` field of view centred on `(l, b) = (0 deg, 0 deg)`.

- `DFACTOR`: `PIXEL`, `Dtot`, `Dsmooth`, `Dsub`, and `Dlist`, in
  `GeV cm^-2`;
- `DFACTOR_PER_SR`: differential quantities in `GeV cm^-2 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

The validated file satisfies, to `FLOAT32` precision,

$$
D_{\mathrm{tot}} \simeq D_{\mathrm{smooth}} + D_{\mathrm{sub}}
+ D_{\mathrm{list}}.
$$

### Column-name issue in CLUMPY v3.1.1

In `DFACTOR_PER_SR`, the values are stored in the order total, smooth, substructure, and listed haloes. However, the last two columns are labelled `Dlist_per_sr` and `dummy4`. Numerical comparison with `DFACTOR` shows that `Dlist_per_sr` contains the differential substructure component and `dummy4` contains the differential listed-halo component. The reference FITS file is kept unchanged so that the audit records the native CLUMPY v3.1.1 output.

The default halo list has three active entries. With the default `g6` field of view, only `rs01_gamma05` at `(l, b) = (20 deg, 20 deg)` is added to this map.
