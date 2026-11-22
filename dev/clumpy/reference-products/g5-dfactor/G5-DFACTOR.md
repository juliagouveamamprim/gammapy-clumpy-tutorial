# g5 D-factor: Galactic map

This reference product is a native CLUMPY HEALPix product generated with simulation mode `g5`. It contains the smooth Milky Way halo and unresolved-subhalo components, without individually resolved haloes.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Compiler: GCC 11.4.0
- GSL: 2.7.1
- CFITSIO: 4.0.0

The configuration matches the `g5` J-factor reference product, except that `gPP_DM_IS_ANNIHIL_OR_DECAY=0` selects dark-matter decay.

## Generation

Run from this directory:

```bash
clumpy -g5 -p -i clumpy_params_g5_dfactor.txt
```

The command creates `output/decay_gal2D_LOS0_0_FOV90x45_nside32.fits`, the native HEALPix map containing the D-factor components.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The map contains 1,416 explicitly indexed pixels covering the `90 deg x 45 deg` field of view centred on `(l, b) = (0 deg, 0 deg)`.

- `DFACTOR`: `PIXEL`, `Dtot`, `Dsmooth`, and `Dsub`, in `GeV cm^-2`;
- `DFACTOR_PER_SR`: the corresponding differential quantities, in `GeV cm^-2 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`.

The validated file satisfies, to `FLOAT32` precision,

$$
D_{\mathrm{tot}} \simeq D_{\mathrm{smooth}} + D_{\mathrm{sub}},
$$

and, for each component $X \in \{\mathrm{tot}, \mathrm{smooth}, \mathrm{sub}\}$,

$$
D_X \simeq \frac{\mathrm{d}D_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$
