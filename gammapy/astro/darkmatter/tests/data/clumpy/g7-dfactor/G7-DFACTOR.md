# g7 D-factor: Galactic map with drawn subhaloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g7`. It contains the smooth Milky Way halo, unresolved-subhalo, and randomly drawn resolved Galactic-subhalo components.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The configuration matches the `g7` J-factor reference product, except that `gPP_DM_IS_ANNIHIL_OR_DECAY=0` selects dark-matter decay.

## Generation

Run from this directory:

```bash
clumpy -g7 -p -i clumpy_params_g7_dfactor.txt
```

The command creates:

- `output/decay_gal2D_LOS180_0_FOV4x4_nside1024.fits`: the native HEALPix D-factor map;
- `output/decay_gal2D_LOS180_0_FOV4x4_nside1024.drawn`: an auxiliary text file describing the drawn subhaloes retained in the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 4868 explicitly indexed pixels centred on `(l, b) = (180 deg, 0 deg)`.

- `DFACTOR`: `PIXEL`, `Dtot`, `Dsmooth`, `Dsub`, and `Ddrawn`, in `GeV cm^-2`;
- `DFACTOR_PER_SR`: the corresponding differential quantities, in `GeV cm^-2 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

With the native `2%` contrast threshold, no resolved subhalo is retained in this fixed D-factor realisation. The `.drawn` file therefore contains no subhalo entries, and `Ddrawn` contains only the empty-pixel sentinel. This is a valid result of the drawing criterion, not a generation failure.

The validated file satisfies, to `FLOAT32` precision,

$$
D_{\mathrm{tot}} \simeq D_{\mathrm{smooth}} + D_{\mathrm{sub}}
+ D_{\mathrm{drawn}},
$$

and, for each physical component value $X$,

$$
D_X \simeq \frac{\mathrm{d}D_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$

### Empty-pixel sentinel in CLUMPY v3.1.1

Pixels without a drawn-subhalo contribution contain `1e-40` in both `Ddrawn` and `Ddrawn_per_sr`. This common floor value is a numerical sentinel for zero and is not rescaled by the pixel solid angle.
