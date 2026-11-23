# h4 J-factor: individual halo map

This reference product is a native CLUMPY HEALPix product generated with simulation mode `h4`. It contains the smooth, unresolved-subhalo, and annihilation cross-product components of the `rs01_gamma05` halo from CLUMPY's default `data/list_generic.txt` file. No individual subhaloes are drawn.

## Provenance

- CLUMPY tag: `v3.1.1`
- CLUMPY commit: `26a60888972268e70aa9ccd06668240833efd8d0`
- Halo list: the default `data/list_generic.txt` distributed with CLUMPY v3.1.1
- Compiler: GCC 12.4.0
- ROOT: 6.28/04
- GSL: 2.7
- CFITSIO: 4.3.0

The parameter file was generated from the CLUMPY `h4` defaults. `NSIDE=256` keeps the two-degree map small, while `NESTED` ordering, `FLOAT32` storage, particle-physics units, and disabled flux-map and ROOT-file outputs are made explicit.

## Generation

Run from this directory:

```bash
clumpy -h4 -p -i clumpy_params_h4_jfactor.txt
```

The command creates `output/annihil_rs01_gamma052D_FOVdiameter2.0deg_nside256.fits`.

Generated outputs are kept locally. The expected FITS layout is documented below.

## Expected FITS layout

The partial-sky map contains 56 explicitly indexed pixels centred on the halo at `(l, b) = (20 deg, 20 deg)`.

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

## ROOT requirement in CLUMPY v3.1.1

CLUMPY v3.1.1 aborts in a build without ROOT whenever halo substructure is enabled, including for `h4`. However, `h4` calls the map calculation with individual subhalo drawing disabled: its unresolved-subhalo component is computed by interpolation, and the ROOT-dependent random sampling is only used by modes that draw individual subhaloes. The ROOT requirement for this product therefore comes from an overly broad implementation guard rather than from the unresolved-subhalo calculation itself.
