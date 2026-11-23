# g8 D-factor: Galactic map with drawn and listed haloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g8`. It combines the `g7` Milky Way components and randomly drawn resolved Galactic subhaloes with resolved haloes read from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The configuration matches the `g8` J-factor reference product, except that `gPP_DM_IS_ANNIHIL_OR_DECAY=0` selects dark-matter decay.

## Generation

Run from this directory:

```bash
clumpy -g8 -p -i clumpy_params_g8_dfactor.txt
```

The command creates:

- `output/decay_gal2D_LOS180_0_FOV4x4_nside1024.fits`: the native HEALPix D-factor map;
- `output/decay_gal2D_LOS180_0_FOV4x4_nside1024.drawn`: an auxiliary text file describing the drawn Galactic subhaloes;
- `output/decay_gal2D_LOS180_0_FOV4x4_nside1024.list`: an auxiliary text file describing the haloes from the input list that were added to the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 4868 explicitly indexed pixels centred on `(l, b) = (180 deg, 0 deg)`.

- `DFACTOR`: `PIXEL`, `Dtot`, `Dsmooth`, `Dsub`, `Ddrawn`, and `Dlist`, in `GeV cm^-2`;
- `DFACTOR_PER_SR`: the corresponding differential quantities, in `GeV cm^-2 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

With the native `2%` contrast threshold, no resolved Galactic subhalo is retained in this fixed D-factor realisation. The `.drawn` file therefore contains no subhalo entries, and `Ddrawn` contains only the empty-pixel sentinel. Of the three active entries in the default halo list, only `rs10_gamma10` at `(l, b) = (179 deg, 0 deg)` lies inside this field and is added to the map.

The validated file satisfies, to `FLOAT32` precision,

$$
D_{\mathrm{tot}} \simeq D_{\mathrm{smooth}} + D_{\mathrm{sub}}
+ D_{\mathrm{drawn}} + D_{\mathrm{list}},
$$

and, for each physical component value $X$,

$$
D_X \simeq \frac{\mathrm{d}D_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$

### Empty-pixel sentinel in CLUMPY v3.1.1

Pixels without a drawn-subhalo contribution contain `1e-40` in both `Ddrawn` and `Ddrawn_per_sr`. This common floor value is a numerical sentinel for zero and is not rescaled by the pixel solid angle.
