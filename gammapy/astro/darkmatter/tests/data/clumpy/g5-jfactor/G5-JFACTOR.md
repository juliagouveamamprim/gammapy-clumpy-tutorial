# g5 J-factor: Galactic map

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g5`. It contains the smooth Milky Way halo, unresolved-subhalo, and annihilation cross-product components, without individually resolved haloes.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Compiler: GCC 11.4.0
- GSL: 2.7.1
- CFITSIO: 4.0.0

The parameter file was generated from the CLUMPY `g5` defaults. The changes specific to this reference product are:

- `NSIDE=32`, to keep the reference product small;
- the `90 deg x 45 deg` field of view centred on `(l, b) = (0 deg, 0 deg)`, to match the `g6` reference products;
- `NESTED` ordering and `FLOAT32` storage, made explicit;
- particle-physics units (`GeV` and `cm`);
- flux-map and ROOT outputs disabled;
- all other physical parameters left at the CLUMPY v3.1.1 `g5` defaults.

## Generation

Run from this directory:

```bash
clumpy -g5 -p -i clumpy_params_g5_jfactor.txt
```

The command creates `output/annihil_gal2D_LOS0_0_FOV90x45_nside32.fits`, the native HEALPix map containing the J-factor components.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The map contains 1,416 explicitly indexed pixels.

- `JFACTOR`: `PIXEL`, `Jtot`, `Jsmooth`, `Jsub`, and `Jcrossp`, in `GeV^2 cm^-5`;
- `JFACTOR_PER_SR`: the corresponding differential quantities, in `GeV^2 cm^-5 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

The validated file satisfies, to `FLOAT32` precision,

$$
J_{\mathrm{tot}} \simeq J_{\mathrm{smooth}} + J_{\mathrm{sub}}
+ J_{\mathrm{crossp}},
$$

and, for each component $X \in \{\mathrm{tot}, \mathrm{smooth}, \mathrm{sub}, \mathrm{crossp}\}$,

$$
J_X \simeq \frac{\mathrm{d}J_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$
