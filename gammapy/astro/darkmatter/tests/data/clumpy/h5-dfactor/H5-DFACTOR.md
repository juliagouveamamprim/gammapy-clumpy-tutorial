# h5 D-factor: individual halo map with drawn subhaloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `h5`. It contains the smooth, unresolved-subhalo, and individually drawn subhalo components of the `rs01_gamma05` halo from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The configuration matches the `h5` J-factor reference product, except that `gPP_DM_IS_ANNIHIL_OR_DECAY=0` selects dark-matter decay.

## Generation

Run from this directory:

```bash
clumpy -h5 -p -i clumpy_params_h5_dfactor.txt
```

The command creates:

- `output/decay_rs01_gamma052D_FOVdiameter2.0deg_nside256.fits`: the native HEALPix D-factor map;
- `output/decay_rs01_gamma052D_FOVdiameter2.0deg_nside256.drawn`: an auxiliary text file describing the drawn subhaloes retained in the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 56 explicitly indexed pixels centred on the halo at `(l, b) = (20 deg, 20 deg)`.

- `DFACTOR`: `PIXEL`, `Dtot`, `Dsmooth`, `Dsub`, and `Ddrawn`, in `GeV cm^-2`;
- `DFACTOR_PER_SR`: the corresponding differential quantities, in `GeV cm^-2 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

For the validated realisation, CLUMPY finds a mass threshold of approximately `1.08e5 Msol`, and the `.drawn` file contains five subhaloes retained inside the map.

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
