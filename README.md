# ace_and_other_data

ACE cross-section tables generated from the evaluated tapes committed in
[`outram-park-backend`](https://github.com/theodoreOnzGit/outram-park-backend)'s
`reference-data/endf/`, kept here because they are large and regenerable. Most
come from ENDF/B-VIII.0; the photo-nuclear pair comes from a small **synthetic**
tape committed in the same place, for the reason given under
[`synthetic/`](#synthetic--the-photo-nuclear-tape-is-synthetic-on-purpose).

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
| `outram-park-njoy/synthetic/0K/Z6-photonuclear.ace.gz` | `ZA=6012 photonuclear njoy-outram-park-fork v0.0.3 94e5640 2026-09-22` |
| `reference-njoy/endf-b-viii.0/0K/U235.ace.gz` | `U235 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U234.ace.gz` | `U234 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U235.ace.gz` | `U235 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/endf-b-viii.0/293.6K/U236.ace.gz` | `U236 E8.0 NJOY2016 2016.79 ac5adf5 2026-10-15 opb 6e3cfd9` |
| `reference-njoy/endf-b-viii.0/293.6K/U238.ace.gz` | `U238 E8.0 NJOY2016 2016.79 ac5adf5 2026-09-20 opb 26cd692` |
| `reference-njoy/synthetic/0K/Z6-photonuclear.ace.gz` | `Z6SYN photonuclear NJOY2016 2016.79 ac5adf5 2026-09-22 opb 94e5640` |
| `reference-njoy/endf-b-viii.0/thermal/Al27_tsl_20K_16b_iwt2.ace.gz` | `Al27 tsl iwt2 E8.0 NJOY2016 2016.79 ac5adf5 2026-10-14 opb ab526668a3` |
| `reference-njoy/endf-b-viii.0/thermal/HinH2O_283.6K_64b_iwt0.ace.gz` | `HH2O 64b iwt0 E8.0 NJOY2016 2016.79 ac5adf5 2026-10-14 opb ab526668a3` |
| `reference-njoy/endf-b-viii.0/thermal/HinH2O_283.6K_64b_iwt2.ace.gz` | `HH2O 64b iwt2 E8.0 NJOY2016 2016.79 ac5adf5 2026-10-14 opb ab526668a3` |

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
| `reference-njoy/endf-b-viii.0/293.6K/U236.ace.gz` | U236 | 9231 | `n-092_U_236-ENDF8.0.endf` | 293.6K | RECONR+BROADR+PURR+ACER (deck: `U236.njoy` beside it) | 7.7 MB | 1.6 MB |
| `reference-njoy/endf-b-viii.0/293.6K/U238.ace.gz` | U238 | 9237 | `n-092_U_238.endf` | 293.6K | RECONR+BROADR+PURR+ACER | 120.7 MB | 18.9 MB |
| `reference-njoy/endf-b-viii.0/0K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 0K | RECONR+ACER | 301.6 MB | 46.9 MB |
| `reference-njoy/synthetic/0K/Z6-photonuclear.ace.gz` | Z6SYN | 600 | `photonuc-synthetic-Z6.endf` | 0K | ACER (`iopt=5`) | 556.8 kB | 44.2 kB |
| `reference-njoy/endf-b-viii.0/thermal/Al27_tsl_20K_16b_iwt2.ace.gz` | Al-27 tsl | 53 | `tsl-013_Al_027-ENDF8.0.endf` (incident `n-013_Al_027`) | 20K | THERMR tol 0.001 + ACER `iwt=2`, 16 bins | 11.9 MB | 3.1 MB |
| `reference-njoy/endf-b-viii.0/thermal/HinH2O_283.6K_64b_iwt0.ace.gz` | H in H2O | 1 | `tsl-HinH2O.endf` (incident `n-001_H_001`) | 283.6K | THERMR tol 0.001, natom 2 + ACER `iwt=0`, 64 bins | 2.3 MB | 0.86 MB |
| `reference-njoy/endf-b-viii.0/thermal/HinH2O_283.6K_64b_iwt2.ace.gz` | H in H2O | 1 | `tsl-HinH2O.endf` (incident `n-001_H_001`) | 283.6K | THERMR tol 0.001, natom 2 + ACER `iwt=2`, 64 bins | 24.8 MB | 6.6 MB |

### The two temperatures are not interchangeable

- **293.6 K** — `RECONR -> BROADR -> PURR -> ACER`. The production set; what
  OpenMC transport should use.
- **0 K** — `RECONR -> ACER` only, **no BROADR and no PURR**. This exists
  solely as a *matched oracle* for verifying the port's ACER path, which runs
  RECONR-only at 0 K. Comparing a 0 K RECONR-only table against a 293.6 K
  broadened one would confound Doppler broadening, probability tables and any
  genuine ACER difference at once. **Not for transport.**

## `synthetic/` — the photo-nuclear tape is synthetic on purpose

`Z6-photonuclear` is **not** an evaluation. It is a hand-built NSUB=0 tape
(MAT 600, ZA 6012, AWR 11.8969, MF=3/MT=5 and MF=6/MT=5 with four emitted
particles) whose generator is committed next to it as
`reference-data/endf/photonuc-synthetic-Z6.generator.py`. Nothing in it should
be read as carbon physics, and no result from it is a physics result.

It exists because the IAEA photo-nuclear sub-library (`www-nds.iaea.org`) is not
reachable from this project's build environment, so there was no real `NSUB=0`
tape to verify `acer iopt = 5` against. A synthetic tape that *exercises the
format* is enough for that job: both codes read the same bytes, so any
disagreement is a code disagreement. What it cannot do is tell you the port
handles evaluation features this tape does not contain — those are listed as
refusals in the port itself (`ielas = 1`, `LANG = 2`, `NA > 0`, `ND > 0`,
`LCT /= 1`, recoil subsections, MF=4/MF=5, `LAW = 2`/`LAW = 4`, MT=18 nubar),
and they are untested, not verified.

## `outram-park-njoy/` — this project's Rust port

| file | nuclide | MAT | source tape | T | deck | raw | gz |
|---|---|---|---|---|---|---|---|
| `outram-park-njoy/endf-b-viii.0/0K/U235.ace.gz` | U235 | 9228 | `n-092_U_235-ENDF8.0.endf` | 0K | RECONR+ACER | 241.6 MB | 11.4 MB |
| `outram-park-njoy/synthetic/0K/Z6-photonuclear.ace.gz` | Z6SYN | 600 | `photonuc-synthetic-Z6.endf` | 0K | ACER (`iopt=5`) | 556.8 kB | 44.2 kB |

### The U-235 table is incomplete, and the gaps are measured, not guessed

Against the matched 0 K reference above:

| | |
|---|---|
| cross sections, at shared grid points | agree to **~1e-6 relative** (6-7 significant figures) |
| **fission ν̄ (NU) block** | ~~**absent** — `JXS(2)` is `0`~~ **CLOSED 2026-09-20 — written, 347/347 values bit-identical to NJOY2016** |
| reactions | ~~47 MTs against NJOY's 84 (missing MT=649, MT=800-835)~~ **CLOSED 2026-09-20 — 84 MTs, set-identical to NJOY** |
| photon production | absent — `NXS(6)` is `0` |

~~**The missing NU block means this table cannot drive a fission eigenvalue** —~~
**CORRECTED 2026-09-20 — the NU block is now written.** The paragraph below is
kept for the record of what was true before; what remains missing is photon
production, which neutron transport does not need. The superseded text: —
ν̄ reads as zero, so there is no fission source. Do not hand it to OpenMC and
expect a `k`. It is published so the comparison is reproducible, not because it
is usable for transport.

Full record:
`crates/outram-mc-libs/verification_and_validation/openmc_godiva_cross_code/ace_pipeline.md`
in `outram-park-backend`.

### The photo-nuclear pair is byte-identical below the comment line

The two `synthetic/0K/Z6-photonuclear.ace.gz` files differ in **one line** —
line 2, the 70-character `hk` comment, which is where each generator stamps
itself. Every other byte of all 556 781 is the same, 27 453 XSS words, worst
value difference `1.0e-13`. Check it yourself:

```bash
diff <(gunzip -c reference-njoy/synthetic/0K/Z6-photonuclear.ace.gz | tail -n +3) \
     <(gunzip -c outram-park-njoy/synthetic/0K/Z6-photonuclear.ace.gz | tail -n +3)
```

That is the whole point of keeping both: the pair is the evidence, and the day
it stops being byte-identical the diff says so. Full record:
`crates/njoy-outram-park-fork/verification_and_validation/acer_photonuclear_vs_njoy2016.md`
in `outram-park-backend`.

## Provenance

| | |
|---|---|
| Generated | 2026-09-20 UTC (uranium; U-236 2026-10-15), 2026-09-22 UTC (photo-nuclear) |
| NJOY2016 | `github.com/njoy/NJOY2016` tag **2016.79**, commit `ac5adf5f33d893e42f2eed7fb286b0d51c7580da` |
| Port | `njoy-outram-park-fork` v0.0.3, `outram-park-backend` @ `26cd692` (uranium; U-236 @ `6e3cfd9`, 2026-10-15) and `94e5640` (photo-nuclear) |
| Evaluations | ENDF/B-VIII.0, from `outram-park-backend/reference-data/endf/`; the photo-nuclear tape is synthetic, see above |
| Decks | `make_ace.sh` (293.6 K) and `make_ace_0k.sh` (0 K), committed in `outram-park-backend` under `crates/outram-mc-libs/verification_and_validation/openmc_godiva_cross_code/`; the photo-nuclear deck is a four-line `acer` card recorded in `crates/njoy-outram-park-fork/verification_and_validation/acer_photonuclear_vs_njoy2016.md` |

`MANIFEST.tsv` carries the SHA-256 of each **uncompressed** table, so a
decompressed file can be checked against what was generated.

Verified usable: OpenMC (`openmc-dev/openmc` @ `afa7a14ac5cb8630f642a77229ca64dc3eaeef81`)
runs ICSBEP HEU-MET-FAST-001 on the 293.6 K reference set at
`k = 0.99939 +/- 0.00061`, leakage `0.57385`. OpenMC reads HDF5 rather than
ACE, so convert with `ace2hdf5.py` from the same directory.

## Data policy

Derived from ENDF/B-VIII.0, an openly published evaluated nuclear data library,
by NJOY2016 (open source) and this project's own port. The photo-nuclear pair is
derived from a synthetic tape written by this project, from no evaluation at
all. No proprietary, restricted or export-controlled content.

## `endf/`: evaluated tapes too large for `outram-park-backend` (added 2026-10-01)

Raw ENDF-6 evaluations that the backend's HTR-10 model needs but that are too
big to commit beside the other tapes in its `reference-data/endf/`. They are
kept **uncompressed**, so `Nuclide::from_endf_file` reads them directly with no
decompression step.

| file | nuclide | MAT | size | resonance format (MF2/MT151) |
|---|---|---|---|---|
| `endf/endf-b-viii.0/n-028_Ni_058-ENDF8.0.endf` | Ni-58 | 2825 | 18.0 MB | LRU=1, LRF=3 (Reich-Moore), EH 812 keV |
| `endf/endf-b-viii.0/n-028_Ni_060-ENDF8.0.endf` | Ni-60 | 2831 | 17.3 MB | LRU=1, LRF=3, EH 800 keV |
| `endf/endf-b-viii.0/n-028_Ni_061-ENDF8.0.endf` | Ni-61 | 2834 | 3.9 MB | LRU=1, LRF=2 (MLBW), EH 57 keV |
| `endf/endf-b-viii.0/n-028_Ni_062-ENDF8.0.endf` | Ni-62 | 2837 | 3.1 MB | LRU=1, LRF=2, EH 600 keV |
| `endf/endf-b-viii.0/n-028_Ni_064-ENDF8.0.endf` | Ni-64 | 2843 | 2.9 MB | LRU=1, LRF=2, EH 553 keV |

- **What uses them:** the HTR-10 control-rod sleeve steel (stainless steel,
  ~9 at.% nickel), in `outram-park-backend`'s `nee_soon` examples.
- **Provenance:** ENDF/B-VIII.0, NNDC (Brookhaven), `ENDF-B-VIII.0.zip`,
  directory `neutrons/`. The tapes were copied unmodified from the
  maintainer's local copy of that archive on 2026-10-01; tape revision line
  `$Rev:: 1451 $ $Date:: 2018-01-17`.
- **Licence:** ENDF/B is public, openly published evaluated data, the same
  basis as every tape in the backend's `reference-data/endf/`.
- **Integrity:** `endf/MANIFEST.tsv` records the byte size and SHA-256 of each
  file.

**Added 2026-11-09:** the zirconium isotopes of the MSRE fuel salt
(`outram-park-backend` gh:#1127, MSRE N-C2).

| file | nuclide | MAT | size | resonance format (MF2/MT151) |
|---|---|---|---|---|
| `endf/endf-b-viii.0/n-040_Zr_090-ENDF8.0.endf` | Zr-90 | 4025 | 1.4 MB | LRU=1, LRF=2 (MLBW) to 200 keV; LRU=2 to 1.78 MeV |
| `endf/endf-b-viii.0/n-040_Zr_091-ENDF8.0.endf` | Zr-91 | 4028 | 3.3 MB | LRU=1, LRF=2 to 26.15 keV; LRU=2 (LFW=1) to 1 MeV |
| `endf/endf-b-viii.0/n-040_Zr_092-ENDF8.0.endf` | Zr-92 | 4031 | 2.1 MB | LRU=1, LRF=2 to 121 keV; LRU=2 to 945 keV |
| `endf/endf-b-viii.0/n-040_Zr_094-ENDF8.0.endf` | Zr-94 | 4037 | 1.8 MB | LRU=1, LRF=2 to 89.4 keV; LRU=2 to 919 keV |
| `endf/endf-b-viii.0/n-040_Zr_096-ENDF8.0.endf` | Zr-96 | 4043 | 1.4 MB | LRU=1, LRF=2 to 96 keV; LRU=2 to 1.60 MeV |

Same provenance as the nickel tapes: ENDF/B-VIII.0, NNDC
`ENDF-B-VIII.0.zip`, `neutrons/`, copied unmodified from the maintainer's
local copy on 2026-11-09. The maintainer's rule since that date: new nuclear
data for `outram-park-backend` goes in this repository, not that one.

## `reference-njoy/endf-b-viii.0/900K/`: the MSRE salt nuclides at 900 K (2026-11-09)

NJOY2016 `ac5adf5` (2016.79) outputs for Li-6, Li-7, Be-9, F-19 and
Zr-90/91/92/94/96. Each nuclide has three files:
- `<name>.njoy`, the deck: RECONR (0.001), then BROADR (900 K), then PURR
  (20 bands, 64 ladders), then ACER (suffix `.90`);
- `<name>.pendf.gz`, BROADR's tape22;
- `<name>.ace.gz`, ACER's tape24.

They were generated by `outram-park-backend`'s
`crates/njoy-outram-park-fork/verification_and_validation/msre_salt_nuclides_900k/make_njoy_900k.sh`
at `e3cbf1e`. The Li, Be and F tapes are the backend's `reference-data/endf/`;
the Zr tapes are this repository's `endf/` above. Sizes and SHA-256 of the
uncompressed files are in `MANIFEST.tsv`, and the ACE comments in
`STAMPS.tsv`.

The backend's `tests/msre_salt_nuclides_900k_vs_njoy2016.rs` compares the
port against these. Every PENDF and eight of the nine ACE tables are
identical in every word; Be-9's ACE is not, because the port does not yet
convert MF=6 LAW=7.


## `reference-njoy/endf-b-viii.0/thermal/` — thermal S(α,β) tables (2026-10-14)

NJOY2016 thermal tables too large for `outram-park-backend`'s own `reference-data/acer/`, the oracle for its word-for-word thermal gate (gh #865/#866). Each `.njoy` beside its table is the deck that made it (tape20 = incident ENDF, tape21 = thermal ENDF, both from `outram-park-backend/reference-data/endf/`). Text units throughout, as OpenMC's `make_ace_thermal` writes its decks.

## `reference-njoy/endf-b-viii.0/911.483K/`: U-235 and U-238 at the MSRE N-1 temperature (2026-11-09)

NJOY2016 ac5adf5, same deck as `900K/` (RECONR 0.001 / BROADR / PURR 20 64 / ACER) at 911.483 K (1181 °F), from the backend's `reference-data/endf/` tapes, by `make_njoy_900k.sh` with `TK=911.483`. For `outram-park-backend` gh:#1310 (URR probability-table parity); sizes and SHA-256 in `MANIFEST.tsv`.
