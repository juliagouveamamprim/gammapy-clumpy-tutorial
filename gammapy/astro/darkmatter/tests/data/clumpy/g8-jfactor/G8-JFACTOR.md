# g8 J-factor: Galactic map with drawn and listed haloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g8`. It combines the `g7` Milky Way components and randomly drawn resolved Galactic subhaloes with resolved haloes read from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The parameter file uses the native `g8` geometry and drawing settings: a `4 deg x 4 deg` field centred on the Galactic anticentre, `NSIDE=1024`, seed `666`, and a subhalo contrast threshold of `2%`. `NESTED` ordering, `FLOAT32` storage, and particle-physics units (`GeV` and `cm`) are made explicit. Flux-map and ROOT-file outputs are disabled.

ROOT is required internally to draw the resolved subhaloes.

## Generation

Run from this directory:

```bash
clumpy -g8 -p -i clumpy_params_g8_jfactor.txt
```

The command creates:

- `output/annihil_gal2D_LOS180_0_FOV4x4_nside1024.fits`: the native HEALPix J-factor map;
- `output/annihil_gal2D_LOS180_0_FOV4x4_nside1024.drawn`: an auxiliary text file describing the drawn Galactic subhaloes;
- `output/annihil_gal2D_LOS180_0_FOV4x4_nside1024.list`: an auxiliary text file describing the haloes from the input list that were added to the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 4868 explicitly indexed pixels centred on `(l, b) = (180 deg, 0 deg)`.

- `JFACTOR`: `PIXEL`, `Jtot`, `Jsmooth`, `Jsub`, `Jcrossp`, `Jdrawn`, and `Jlist`, in `GeV^2 cm^-5`;
- `JFACTOR_PER_SR`: the corresponding differential quantities, in `GeV^2 cm^-5 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

The fixed, validated realisation contains 165 drawn Galactic subhaloes. Of the three active entries in the default halo list, only `rs10_gamma10` at `(l, b) = (179 deg, 0 deg)` lies inside this field and is added to the map.

The validated file satisfies, to `FLOAT32` precision,

$$
J_{\mathrm{tot}} \simeq J_{\mathrm{smooth}} + J_{\mathrm{sub}}
+ J_{\mathrm{crossp}} + J_{\mathrm{drawn}} + J_{\mathrm{list}},
$$

and, for each physical component value $X$,

$$
J_X \simeq \frac{\mathrm{d}J_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$

### Empty-pixel sentinel in CLUMPY v3.1.1

Pixels without a drawn-subhalo contribution contain `1e-40` in both `Jdrawn` and `Jdrawn_per_sr`. This common floor value is a numerical sentinel for zero and is not rescaled by the pixel solid angle.
