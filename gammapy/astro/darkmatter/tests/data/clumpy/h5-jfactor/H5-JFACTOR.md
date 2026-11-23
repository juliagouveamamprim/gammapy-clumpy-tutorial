# h5 J-factor: individual halo map with drawn subhaloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `h5`. It contains the smooth, unresolved-subhalo, annihilation cross-product, and individually drawn subhalo components of the `rs01_gamma05` halo from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The parameter file was generated from the CLUMPY `h5` defaults. The changes specific to this reference product are:

- `NSIDE=256`, to keep the two-degree map small;
- `gSIM_SEED=666` and `gSIM_USER_RSE=10`, made explicit for a reproducible subhalo realisation;
- `NESTED` ordering and `FLOAT32` storage, made explicit;
- particle-physics units (`GeV` and `cm`);
- flux-map and ROOT-file outputs disabled;
- all other physical parameters left at the CLUMPY v3.1.1 `h5` defaults.

ROOT is still required internally to draw the subhaloes.

## Generation

Run from this directory:

```bash
clumpy -h5 -p -i clumpy_params_h5_jfactor.txt
```

The command creates:

- `output/annihil_rs01_gamma052D_FOVdiameter2.0deg_nside256.fits`: the native HEALPix J-factor map;
- `output/annihil_rs01_gamma052D_FOVdiameter2.0deg_nside256.drawn`: an auxiliary text file describing the drawn subhaloes retained in the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 56 explicitly indexed pixels centred on the halo at `(l, b) = (20 deg, 20 deg)`.

- `JFACTOR`: `PIXEL`, `Jtot`, `Jsmooth`, `Jsub`, `Jcrossp`, and `Jdrawn`, in `GeV^2 cm^-5`;
- `JFACTOR_PER_SR`: the corresponding differential quantities, in `GeV^2 cm^-5 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

For the validated realisation, CLUMPY finds a mass threshold of approximately `2.77e4 Msol`, and the `.drawn` file contains 23 subhaloes retained inside the map.

The validated file satisfies, to `FLOAT32` precision,

$$
J_{\mathrm{tot}} \simeq J_{\mathrm{smooth}} + J_{\mathrm{sub}}
+ J_{\mathrm{crossp}} + J_{\mathrm{drawn}},
$$

and, for each physical component value $X$,

$$
J_X \simeq \frac{\mathrm{d}J_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$

### Empty-pixel sentinel in CLUMPY v3.1.1

Pixels without a drawn-subhalo contribution contain `1e-40` in both `Jdrawn` and `Jdrawn_per_sr`. This common floor value is a numerical sentinel for zero and is not rescaled by the pixel solid angle.
