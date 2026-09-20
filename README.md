# ace_and_other_data

ACE cross-section tables generated from the ENDF/B-VIII.0 evaluations committed
in [`outram-park-backend`](https://github.com/theodoreOnzGit/outram-park-backend)'s
`reference-data/endf/`, kept here because they are large and regenerable.

**Two generators, kept in separate trees, because mixing them would defeat the
purpose of having them:**

| tree | generator | role |
|---|---|---|
| `reference-njoy/` | **NJOY2016** (the reference implementation) | the oracle, and the set OpenMC transport should use |
| `outram-park-njoy/` | **`njoy-outram-park-fork`** (this project's Rust port) | the thing under test |

A table from the port is *not* interchangeable with a reference table. It is
here to be compared against one.

## Every table carries its own provenance

The ACE `hk` comment field is 70 characters of free text that travels with the
data, so each table is stamped at generation with **generator, version, commit
and date** — not recorded alongside it, recorded *inside* it. A table found
loose on disk still says where it came from.

| file | embedded ACE comment |
|---|---|
| `outram-park-njoy/endf-b-viii.0/0K/U235.ace.gz` | `ZA=92235 njoy-outram-park-fork v0.0.3 26cd692+ 2026-09-20` |
| `reference-njoy/endf-b-viii.0/0K/U235.ace.gz` | `U235 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U234.ace.gz` | `U234 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U235.ace.gz` | `U235 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U238.ace.gz` | `U238 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |

A trailing **`+`** on a commit means the tree had uncommitted changes when the
table was built. `STAMPS.tsv` is this table, machine-readable, read back out of
the archives rather than retyped.

## Why the files are gzipped

ACE is ASCII and compresses 6-20x. Raw, several of these exceed GitHub's
**100 MiB per-file hard limit** — U-235 at 129.6 MB and the 0 K U-235 at
301.6 MB — so they could not be pushed uncompressed. gzip over Git LFS so a
plain clone suffices with no LFS quota; gzip over xz because gzip is present
wherever a processing pipeline runs.

Decompress before use — NJOY and OpenMC do not read `.gz`:

```bash
gunzip -c reference-njoy/endf-b-viii.0/293.6K/U235.ace.gz > U235.ace
```

## `reference-njoy/` — NJOY2016

| file | nuclide | MAT | source tape | T | deck | raw | gz |
|---|---|---|---|---|---|---|---|
| `reference-njoy/endf-b-viii.0/293.6K/U234.ace.gz` | U234 | 9225 | `n-092_U_234-ENDF8.0.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 8.7 MB | 1.8 MB |
| `reference-njoy/endf-b-viii.0/293.6K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 129.6 MB | 22.0 MB |
| `reference-njoy/endf-b-viii.0/293.6K/U238.ace.gz` | U238 | 9237 | `n-092_U_238.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 120.7 MB | 18.9 MB |
| `reference-njoy/endf-b-viii.0/0K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 0K | RECONR+ACER | 301.6 MB | 46.9 MB |

### The two temperatures are not interchangeable

- **293.6 K** — `RECONR -> BROADR -> PURR -> ACER`. The production set; what
  OpenMC transport should use.
- **0 K** — `RECONR -> ACER` only, **no BROADR and no PURR**. This exists
  solely as a *matched oracle* for verifying the port's ACER path, which runs
  RECONR-only at 0 K. Comparing a 0 K RECONR-only table against a 293.6 K
  broadened one would confound Doppler broadening, probability tables and any
  genuine ACER difference at once. **Not for transport.**

## `outram-park-njoy/` — this project's Rust port

| file | nuclide | MAT | source tape | T | deck | raw | gz |
|---|---|---|---|---|---|---|---|
| `outram-park-njoy/endf-b-viii.0/0K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 0K | RECONR+ACER | 241.6 MB | 11.4 MB |

**This table is incomplete, and the gaps are measured, not guessed.** Against
the matched 0 K reference above:

| | |
|---|---|
| cross sections, at shared grid points | agree to **~1e-6 relative** (6-7 significant figures) |
| **fission ν̄ (NU) block** | **absent** — `JXS(2)` is `0` |
| reactions | 47 MTs against NJOY's 84 (missing MT=649, MT=800-835) |
| photon production | absent — `NXS(6)` is `0` |

**The missing NU block means this table cannot drive a fission eigenvalue** —
ν̄ reads as zero, so there is no fission source. Do not hand it to OpenMC and
expect a `k`. It is published so the comparison is reproducible, not because it
is usable for transport.

Full record:
`crates/outram-mc-libs/verification_and_validation/openmc_godiva_cross_code/ace_pipeline.md`
in `outram-park-backend`.

## Provenance

| | |
|---|---|
| Generated | 2026-09-20 UTC |
| NJOY2016 | `github.com/njoy/NJOY2016` tag **2016.79**, commit `ac5adf5f33d893e42f2eed7fb286b0d51c7580da` |
| Port | `njoy-outram-park-fork` v0.0.3, `outram-park-backend` @ `26cd692` |
| Evaluations | ENDF/B-VIII.0, from `outram-park-backend/reference-data/endf/` |
| Decks | `make_ace.sh` (293.6 K) and `make_ace_0k.sh` (0 K), committed in `outram-park-backend` under `crates/outram-mc-libs/verification_and_validation/openmc_godiva_cross_code/` |

`MANIFEST.tsv` carries the SHA-256 of each **uncompressed** table, so a
decompressed file can be checked against what was generated.

Verified usable: OpenMC (`openmc-dev/openmc` @ `afa7a14ac5cb8630f642a77229ca64dc3eaeef81`)
runs ICSBEP HEU-MET-FAST-001 on the 293.6 K reference set at
`k = 0.99939 +/- 0.00061`, leakage `0.57385`. OpenMC reads HDF5 rather than
ACE, so convert with `ace2hdf5.py` from the same directory.

## Data policy

Derived from ENDF/B-VIII.0, an openly published evaluated nuclear data library,
by NJOY2016 (open source) and this project's own port. No proprietary,
restricted or export-controlled content.
