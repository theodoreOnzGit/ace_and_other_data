# ace_and_other_data

ACE cross-section tables generated with **NJOY2016** from the ENDF/B-VIII.0
evaluations committed in [`outram-park-backend`](https://github.com/theodoreOnzGit/outram-park-backend)'s
`reference-data/endf/`, kept out of that repository because they are large
and regenerable.

## Why the files are gzipped

ACE is ASCII and compresses about 6.4x. Two of these tables exceed GitHub's
**100 MiB per-file hard limit** raw — U-235 at 129.6 MB and the 0 K U-235 at
301.6 MB — so they could not be pushed uncompressed. gzip was chosen over
Git LFS to keep the repository usable with a plain clone and no LFS quota,
and over xz because gzip is available everywhere a processing pipeline runs.

Decompress before use — NJOY and OpenMC do not read `.gz`:

```bash
gunzip -c endf-b-viii.0/293.6K/U235.ace.gz > U235.ace
```

## Contents

| file | nuclide | MAT | source tape | T | NJOY deck | raw | gz |
|---|---|---|---|---|---|---|---|
| `endf-b-viii.0/293.6K/U234.ace.gz` | U234 | 9225 | `n-092_U_234-ENDF8.0.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 8.7 MB | 1.8 MB |
| `endf-b-viii.0/293.6K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 129.6 MB | 22.0 MB |
| `endf-b-viii.0/293.6K/U238.ace.gz` | U238 | 9237 | `n-092_U_238.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 120.7 MB | 18.9 MB |
| `endf-b-viii.0/0K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 0K | RECONR+ACER | 301.6 MB | 46.9 MB |

`MANIFEST.tsv` is the machine-readable record, and carries the SHA-256 of
each **uncompressed** table so a decompressed file can be checked against what
was generated.

## The two temperatures are not interchangeable

- **293.6 K** — `RECONR -> BROADR -> PURR -> ACER`. This is the production
  set, and the one OpenMC transport should use.
- **0 K** — `RECONR -> ACER` only, **no BROADR and no PURR**. This exists
  solely as a *matched oracle* for verifying `njoy-outram-park-fork`'s own
  ACER path, which runs RECONR-only at 0 K. Comparing a 0 K RECONR-only
  table against a 293.6 K broadened one would confound Doppler broadening,
  probability tables and any genuine ACER difference at once. **Do not use
  the 0 K table for transport.**

## Provenance

| | |
|---|---|
| Generated | 2026-09-20 UTC |
| NJOY2016 | `github.com/njoy/NJOY2016` @ `ac5adf5f33d893e42f2eed7fb286b0d51c7580da` |
| Evaluations | ENDF/B-VIII.0, from `outram-park-backend/reference-data/endf/` |
| Generating decks | `make_ace.sh` (293.6 K) and `make_ace_0k.sh` (0 K), committed in `outram-park-backend` under `crates/outram-mc-libs/verification_and_validation/openmc_godiva_cross_code/` |
| outram-park-backend | `45c21a933398831db128b5c2552c1095f40d022c` |

Verified usable: OpenMC (`openmc-dev/openmc` @ `afa7a14ac5cb8630f642a77229ca64dc3eaeef81`)
runs ICSBEP HEU-MET-FAST-001 on the 293.6 K set at
`k = 0.99939 +/- 0.00061`, leakage `0.57385`. OpenMC reads HDF5 rather than
ACE, so convert with `ace2hdf5.py` from the same directory.

## Data policy

Derived from ENDF/B-VIII.0, an openly published evaluated nuclear data
library, by NJOY2016 (open source). No proprietary, restricted or
export-controlled content.
