# g5 J-factor: full-sky Galactic map

This reference product is a native full-sky CLUMPY HEALPix product generated with simulation mode `g5`. It complements the partial-sky `g5` product by covering a complete HEALPix geometry.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Compiler: GCC 11.4.0
- GSL: 2.7.1
- CFITSIO: 4.0.0

The parameter file is based on the partial-sky `g5` J-factor configuration. The field of view was changed to the CLUMPY full-sky disk convention:

- `gSIM_THETA_ORTH_SIZE_DEG=d`;
- `gSIM_THETA_SIZE_DEG=360`;
- `NSIDE=32`, `NESTED` ordering, and `FLOAT32` storage are retained;
- all physical parameters remain identical to the partial-sky `g5` product.

## Generation

Run from this directory:

```bash
clumpy -g5 -p -i clumpy_params_g5_jfactor_fullsky.txt
```

The command creates
`output/annihil_gal2D_LOS0_0_FOVdiameter360.0deg_nside32.fits`.
Generated outputs are kept locally.

## Validated FITS layout

The map contains all 12,288 pixels for `NSIDE=32`, corresponding to a sky fraction of 100%. The `PIXEL` column contains every index from 0 to 12,287 exactly once.

- `JFACTOR`: `PIXEL`, `Jtot`, `Jsmooth`, `Jsub`, and `Jcrossp`, in
  `GeV^2 cm^-5`;
- `JFACTOR_PER_SR`: the corresponding differential quantities, in
  `GeV^2 cm^-5 sr^-1`;
- `ORDERING=NESTED` and `INDXSCHM=EXPLICIT`;
- `SIZE_X=360 deg` and `SIZE_Y=180 deg`.

Although the product covers the full sky, CLUMPY v3.1.1 stores this native multi-component map with explicit pixel indexing. Full-sky coverage and implicit indexing must therefore be treated as separate properties by a reader.

The header also reports `OBJECT=PARTIAL`. A reader must therefore not use this keyword alone to determine sky coverage; for explicitly indexed products, a complete full-sky geometry can be identified from the pixel-index set and `NSIDE`.

The validated file satisfies, to `FLOAT32` precision,

$$
J_{\mathrm{tot}} \simeq J_{\mathrm{smooth}} + J_{\mathrm{sub}}
+ J_{\mathrm{crossp}}.
$$

For each component $X$, the integrated and differential extensions satisfy

$$
J_X \simeq \frac{\mathrm{d}J_X}{\mathrm{d}\Omega}
\,\Delta\Omega_{\mathrm{pix}}.
$$
