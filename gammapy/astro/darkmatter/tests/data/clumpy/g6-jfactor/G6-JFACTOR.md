# g6 J-factor: Galactic map with listed haloes

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g6`. It combines the Milky Way components from `g5` with resolved haloes read from CLUMPY's default `data/list_generic.txt` file.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 11.4.0
- GSL: 2.7.1
- CFITSIO: 4.0.0

The parameter file was generated from the CLUMPY `g6` defaults. The changes specific to this reference product are:

- `NSIDE=32`, to keep the reference product small;
- `NESTED` ordering and `FLOAT32` storage, made explicit;
- particle-physics units (`GeV` and `cm`);
- flux-map and ROOT outputs disabled;
- all other physical and geometrical parameters left at the CLUMPY v3.1.1 `g6` defaults.

## Generation

Run from this directory:

```bash
clumpy -g6 -p -i clumpy_params_g6_jfactor.txt
```

The command creates:

- `output/annihil_gal2D_LOS0_0_FOV90x45_nside32.fits`: the native HEALPix map containing the J-factor components;
- `output/annihil_gal2D_LOS0_0_FOV90x45_nside32.list`: an auxiliary text file describing the haloes from the input list that were added to the map.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The map contains 1,416 explicitly indexed pixels covering the default `90 deg x 45 deg` field of view centred on `(l, b) = (0 deg, 0 deg)`.

- `JFACTOR`: `PIXEL`, `Jtot`, `Jsmooth`, `Jsub`, `Jcrossp`, and `Jlist`, in
  `GeV^2 cm^-5`;
- `JFACTOR_PER_SR`: the corresponding differential quantities, in
  `GeV^2 cm^-5 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

The validated file satisfies, to `FLOAT32` precision,

$$
J_{\mathrm{tot}} \simeq J_{\mathrm{smooth}} + J_{\mathrm{sub}}
+ J_{\mathrm{crossp}} + J_{\mathrm{list}},
$$

and, for each component $X \in \{\mathrm{tot}, \mathrm{smooth}, \mathrm{sub}, \mathrm{crossp}, \mathrm{list}\}$,

$$
J_X \simeq \frac{\mathrm{d}J_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$

The default halo list has three active entries. With the default `g6` field of view, only `rs01_gamma05` at `(l, b) = (20 deg, 20 deg)` is added to this map.
