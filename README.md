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


**Added 2026-11-09:** the seven stable molybdenum isotopes, for INOR-8 (Hastelloy N, 16.5 wt % Mo) in `outram-park-backend`'s MSRE full-core model (gh:#1128). They replace that model's beta Mo-95 stand-in. Same provenance as the tapes above: ENDF/B-VIII.0, NNDC `ENDF-B-VIII.0.zip`, `neutrons/`, copied unmodified from the maintainer's local copy on 2026-11-09.

| file | nuclide | MAT | size |
|---|---|---|---|
| `endf/endf-b-viii.0/n-042_Mo_092-ENDF8.0.endf` | Mo-92 | 4225 | 0.57 MB |
| `endf/endf-b-viii.0/n-042_Mo_094-ENDF8.0.endf` | Mo-94 | 4231 | 0.63 MB |
| `endf/endf-b-viii.0/n-042_Mo_095-ENDF8.0.endf` | Mo-95 | 4234 | 1.55 MB |
| `endf/endf-b-viii.0/n-042_Mo_096-ENDF8.0.endf` | Mo-96 | 4237 | 0.60 MB |
| `endf/endf-b-viii.0/n-042_Mo_097-ENDF8.0.endf` | Mo-97 | 4240 | 0.67 MB |
| `endf/endf-b-viii.0/n-042_Mo_098-ENDF8.0.endf` | Mo-98 | 4243 | 0.59 MB |
| `endf/endf-b-viii.0/n-042_Mo_100-ENDF8.0.endf` | Mo-100 | 4249 | 0.34 MB |

**Added 2026-11-17:** the thorium-cycle neutron tapes and the decay and
neutron fission-yield sublibraries, for depletion-chain building
(`outram-park-backend` gh:#1627; the port of OpenMC's `Chain.from_endf`,
gh:#1621). Unlike the tapes above, these were **downloaded directly from NNDC**
in this session, not copied from the maintainer's local copy.

| file | nuclide | MAT | size |
|---|---|---|---|
| `endf/endf-b-viii.0/n-090_Th_232-ENDF8.0.endf` | Th-232 | 9040 | 12.9 MB |
| `endf/endf-b-viii.0/n-091_Pa_233-ENDF8.0.endf` | Pa-233 | 9137 | 7.1 MB |
| `endf/endf-b-viii.0/n-092_U_233-ENDF8.0.endf` | U-233 | 9222 | 21.2 MB |
| `endf/endf-b-viii.0/decay/` | 3,821 decay tapes `dec-ZZZ_Sy_AAA[mN].endf`, plus the archive's `README.txt`, `CHANGELOG.txt`, `decay.list`, `decay-releaseNotes.pdf` | | 3,825 files, 65.9 MB |
| `endf/endf-b-viii.0/nfy/` | 31 fission-yield tapes `nfy-ZZZ_Sy_AAA[mN].endf`, plus `README.txt`, `CHANGELOG.txt`, `nfy.list`, `nfy-releaseNotes.pdf` | | 35 files, 8.3 MB |

- **Source:** NNDC, Brookhaven National Laboratory, ENDF/B-VIII.0 download
  page `https://www.nndc.bnl.gov/endf-b8.0/download.html`. Archives, with the
  size and SHA-256 of each archive as downloaded:

  | archive | URL | bytes | sha256 |
  |---|---|---|---|
  | `ENDF-B-VIII.0_neutrons.zip` | `https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_neutrons.zip` | 310158456 | `a1cced11710eb36690c5ad7b64d414465c5849f5428904049c864e9beca28894` |
  | `ENDF-B-VIII.0_decay.zip` | `https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_decay.zip` | 14173710 | `9087395f6d74fe35b47bb4c8b37ef53b44e67569196c933f835a53cdf8d03d5e` |
  | `ENDF-B-VIII.0_nfy.zip` | `https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_nfy.zip` | 2922601 | `92c5371fdb21eecf4989f48828671b904186abc6386b3d7510c8fcee2ee5ffcf` |

- **Date accessed:** 2026-11-17 UTC.
- **Processing:** none. The three neutron tapes were extracted from
  `ENDF-B-VIII.0_neutrons/` and renamed from NNDC's `n-090_Th_232.endf` form
  to this directory's `-ENDF8.0.endf` form; content unmodified (tape revision
  line `$Rev:: 1451 $ $Date:: 2018-01-17`). The two sublibraries are the
  archives' `ENDF-B-VIII.0_decay/` and `ENDF-B-VIII.0_nfy/` directories,
  unpacked unmodified, file names kept. The nfy README states that sublibrary
  is unchanged from ENDF/B-VII.1.
- **Licence / access terms:** ENDF/B-VIII.0 is openly published evaluated
  nuclear data, distributed publicly and without registration by NNDC (a US
  DOE user facility); the same basis as every other tape here and in the
  backend's `reference-data/endf/`. Public, openly licensed data within
  `DATA_POLICY.md`'s scope; no proprietary, restricted or export-controlled
  content. Cite D.A. Brown et al., "ENDF/B-VIII.0: The 8th Major Release of
  the Nuclear Reaction Data Library…", Nucl. Data Sheets 148 (2018) 1,
  doi:10.1016/j.nds.2018.02.001.
- **Integrity:** one `endf/MANIFEST.tsv` row per file (3,863 rows: 3 + 3,825
  + 35). MAT is read from columns 67–70 of each tape's first record; the
  non-tape files carry `-` for nuclide and MAT.

**Added 2026-11-17:** neutron tapes and the H-in-H2O thermal scattering law
for OpenMC pincell depletion parity (`outram-park-backend` gh:#1626): light
water and boron (H-1, O-16/17/18, B-10/11, He-3/4), cladding and structure
(Fe-54/56/57/58, Cr-50/52/53/54, the ten stable Sn isotopes), fission-product
poisons (I-135, Xe-135/136, Cs-135, Gd-156/157) and U-234/235/236/238.
**Downloaded directly from NNDC** in this session, like the gh:#1627 tapes
above.

| file | nuclide | MAT | archive member | size | sha256 |
|---|---|---|---|---|---|
| `endf/endf-b-viii.0/n-001_H_001-ENDF8.0.endf` | H-1 | 125 | `ENDF-B-VIII.0_neutrons/n-001_H_001.endf` | 671 kB | `e7b76c20c1d61f2cb2621de716d324047fa1f9583bef568f643f8d98094260fb` |
| `endf/endf-b-viii.0/n-002_He_003-ENDF8.0.endf` | He-3 | 225 | `ENDF-B-VIII.0_neutrons/n-002_He_003.endf` | 36 kB | `a29ce1ba792b18c1ee4e9565eef6a403937e426715fd58349d915559f8aa53df` |
| `endf/endf-b-viii.0/n-002_He_004-ENDF8.0.endf` | He-4 | 228 | `ENDF-B-VIII.0_neutrons/n-002_He_004.endf` | 203 kB | `4d177a8cc8a1493516545551c2c846a6b47e3f0dcec65af8870f50579a3df09c` |
| `endf/endf-b-viii.0/n-005_B_010-ENDF8.0.endf` | B-10 | 525 | `ENDF-B-VIII.0_neutrons/n-005_B_010.endf` | 873 kB | `7c8b32e1d35017048e6a47413127b141922d61fd703f357e90b3a59bdd0d9b7d` |
| `endf/endf-b-viii.0/n-005_B_011-ENDF8.0.endf` | B-11 | 528 | `ENDF-B-VIII.0_neutrons/n-005_B_011.endf` | 659 kB | `50ab204e4deb625dbcd15e22961c605000a53efb1d7bcf339d29d6bba2cc270d` |
| `endf/endf-b-viii.0/n-008_O_016-ENDF8.0.endf` | O-16 | 825 | `ENDF-B-VIII.0_neutrons/n-008_O_016.endf` | 41.9 MB | `aabef9c710c132b4e4d8028e58eff2e542d61d34b93823b5f1199649bff7f781` |
| `endf/endf-b-viii.0/n-008_O_017-ENDF8.0.endf` | O-17 | 828 | `ENDF-B-VIII.0_neutrons/n-008_O_017.endf` | 82 kB | `0d4cb1bb6d8bf565c4867f65e3866a2833aa7908e54c7d30337ca3fed07cd641` |
| `endf/endf-b-viii.0/n-008_O_018-ENDF8.0.endf` | O-18 | 831 | `ENDF-B-VIII.0_neutrons/n-008_O_018.endf` | 34 kB | `a46aa0709d2070671305a9c4e077ccd91ca012131b94220726f6d404155febc7` |
| `endf/endf-b-viii.0/n-024_Cr_050-ENDF8.0.endf` | Cr-50 | 2425 | `ENDF-B-VIII.0_neutrons/n-024_Cr_050.endf` | 10.6 MB | `ad442e640e82c98d22be47b60e41820a0ff3aeb8868c38de17b8af0cd4eee739` |
| `endf/endf-b-viii.0/n-024_Cr_052-ENDF8.0.endf` | Cr-52 | 2431 | `ENDF-B-VIII.0_neutrons/n-024_Cr_052.endf` | 10.6 MB | `c42bee3cbc9171d2fb70f3fdf409faeebfc7d2d7ea12ebfc003e78410376aa14` |
| `endf/endf-b-viii.0/n-024_Cr_053-ENDF8.0.endf` | Cr-53 | 2434 | `ENDF-B-VIII.0_neutrons/n-024_Cr_053.endf` | 8.88 MB | `41aee5af2f3d671ea8c857fc642cf3c6e80d56976f774b11dad6316daf87bdfb` |
| `endf/endf-b-viii.0/n-024_Cr_054-ENDF8.0.endf` | Cr-54 | 2437 | `ENDF-B-VIII.0_neutrons/n-024_Cr_054.endf` | 2.46 MB | `8cce14a7d12aacc82fbb1e0fc91c069daedf41063f02735e592fd1d3f3672a09` |
| `endf/endf-b-viii.0/n-026_Fe_054-ENDF8.0.endf` | Fe-54 | 2625 | `ENDF-B-VIII.0_neutrons/n-026_Fe_054.endf` | 15.5 MB | `d5000c8d07900ff7e8022c55c81ce81ca5ee837a208227a90c60b22e8588cb76` |
| `endf/endf-b-viii.0/n-026_Fe_056-ENDF8.0.endf` | Fe-56 | 2631 | `ENDF-B-VIII.0_neutrons/n-026_Fe_056.endf` | 24.2 MB | `b8bb897c76f85f7a22a989949c5c353e6b6541278b555b88be3a53102184bbae` |
| `endf/endf-b-viii.0/n-026_Fe_057-ENDF8.0.endf` | Fe-57 | 2634 | `ENDF-B-VIII.0_neutrons/n-026_Fe_057.endf` | 13.6 MB | `cdef054c5abbda7c740266cc01632c6b90ead02ec7d856630cab6906cfc96147` |
| `endf/endf-b-viii.0/n-026_Fe_058-ENDF8.0.endf` | Fe-58 | 2637 | `ENDF-B-VIII.0_neutrons/n-026_Fe_058.endf` | 9.28 MB | `57b574915c9f552950f93f8c9fbe8c40335d704d85209f1a299df5fa6fa2194c` |
| `endf/endf-b-viii.0/n-050_Sn_112-ENDF8.0.endf` | Sn-112 | 5025 | `ENDF-B-VIII.0_neutrons/n-050_Sn_112.endf` | 176 kB | `4ad85313b0c83930e7af17f46f97187526a23830f86d085b8d5e0ea5360406de` |
| `endf/endf-b-viii.0/n-050_Sn_114-ENDF8.0.endf` | Sn-114 | 5031 | `ENDF-B-VIII.0_neutrons/n-050_Sn_114.endf` | 182 kB | `585096162e32da4aaba9c4481843b11163b2ab59920538c31c7627d90edfbee5` |
| `endf/endf-b-viii.0/n-050_Sn_115-ENDF8.0.endf` | Sn-115 | 5034 | `ENDF-B-VIII.0_neutrons/n-050_Sn_115.endf` | 160 kB | `10f33e2b0c40263ff0e59a39aaa14a105bcb30957ef749802252df87bf01f184` |
| `endf/endf-b-viii.0/n-050_Sn_116-ENDF8.0.endf` | Sn-116 | 5037 | `ENDF-B-VIII.0_neutrons/n-050_Sn_116.endf` | 139 kB | `238dfb7f94de486056a5639b40f8f78b80d83c2ecf1ce836717835bd7f884ea8` |
| `endf/endf-b-viii.0/n-050_Sn_117-ENDF8.0.endf` | Sn-117 | 5040 | `ENDF-B-VIII.0_neutrons/n-050_Sn_117.endf` | 152 kB | `902cee4bf76fc8d46c1d7c6c2925e61c9ed92404c8a2e91bc55345665a66e54c` |
| `endf/endf-b-viii.0/n-050_Sn_118-ENDF8.0.endf` | Sn-118 | 5043 | `ENDF-B-VIII.0_neutrons/n-050_Sn_118.endf` | 115 kB | `d01e0932fff716558627d13e173e31e07d93ee0514ff8852690ea2d6750bbf80` |
| `endf/endf-b-viii.0/n-050_Sn_119-ENDF8.0.endf` | Sn-119 | 5046 | `ENDF-B-VIII.0_neutrons/n-050_Sn_119.endf` | 174 kB | `522fb6d685bbcb8e546d2a04b2e576eb11b82717f21e6ed345b1424b1c243c91` |
| `endf/endf-b-viii.0/n-050_Sn_120-ENDF8.0.endf` | Sn-120 | 5049 | `ENDF-B-VIII.0_neutrons/n-050_Sn_120.endf` | 1.51 MB | `1441513b6fa5cc1b99e79c67fa8209f5f4dc1f08605d52d48b3ac0408a406ba8` |
| `endf/endf-b-viii.0/n-050_Sn_122-ENDF8.0.endf` | Sn-122 | 5055 | `ENDF-B-VIII.0_neutrons/n-050_Sn_122.endf` | 137 kB | `e732a3aff26db5dad2cd8279bf012d705c00a9ea093125f02aed73c37152ca41` |
| `endf/endf-b-viii.0/n-050_Sn_124-ENDF8.0.endf` | Sn-124 | 5061 | `ENDF-B-VIII.0_neutrons/n-050_Sn_124.endf` | 165 kB | `65a1d35ee4e43cac57ffc44f9b3cd09f39468a0867ed5f7c36833937727f25d7` |
| `endf/endf-b-viii.0/n-053_I_135-ENDF8.0.endf` | I-135 | 5349 | `ENDF-B-VIII.0_neutrons/n-053_I_135.endf` | 161 kB | `ef29411619b36742f8d443644e8f6e1809d6aba8dc04b20d1630a25a01da64b2` |
| `endf/endf-b-viii.0/n-054_Xe_135-ENDF8.0.endf` | Xe-135 | 5458 | `ENDF-B-VIII.0_neutrons/n-054_Xe_135.endf` | 161 kB | `4e9e0847c4ed2b5ccbd3d99d55fbbe25cb7d7db4f074e34b16813a10653332f4` |
| `endf/endf-b-viii.0/n-054_Xe_136-ENDF8.0.endf` | Xe-136 | 5461 | `ENDF-B-VIII.0_neutrons/n-054_Xe_136.endf` | 294 kB | `d02fbbb007c6270e2f1e5307342dee89b5fac3cc250b41da3db546757cc38c32` |
| `endf/endf-b-viii.0/n-055_Cs_135-ENDF8.0.endf` | Cs-135 | 5531 | `ENDF-B-VIII.0_neutrons/n-055_Cs_135.endf` | 207 kB | `a6401c179589e2da6ee05a37e23cade4d0bd051d11a381f2c6f985b6dbe80c62` |
| `endf/endf-b-viii.0/n-064_Gd_156-ENDF8.0.endf` | Gd-156 | 6437 | `ENDF-B-VIII.0_neutrons/n-064_Gd_156.endf` | 2.79 MB | `4c61c0ba99c51e5b326b8cffd38095ccb3749266444290e94767ce71b19ed8c6` |
| `endf/endf-b-viii.0/n-064_Gd_157-ENDF8.0.endf` | Gd-157 | 6440 | `ENDF-B-VIII.0_neutrons/n-064_Gd_157.endf` | 3.04 MB | `3a365e591276d0304b0955b5ee07f65e335c80e73878174684b0927778c3f2bb` |
| `endf/endf-b-viii.0/n-092_U_234-ENDF8.0.endf` | U-234 | 9225 | `ENDF-B-VIII.0_neutrons/n-092_U_234.endf` | 1.98 MB | `ab551adbe077d008d0318aed4170ea82aba5247cd756801ac22aa0d3e1c84164` |
| `endf/endf-b-viii.0/n-092_U_235-ENDF8.0.endf` | U-235 | 9228 | `ENDF-B-VIII.0_neutrons/n-092_U_235.endf` | 36.9 MB | `199fb786ec888485a83b4de1f6fecfff2de0096479bde282e6e418153b9f3b79` |
| `endf/endf-b-viii.0/n-092_U_236-ENDF8.0.endf` | U-236 | 9231 | `ENDF-B-VIII.0_neutrons/n-092_U_236.endf` | 1.36 MB | `ab9911c2f5f0e5f45e16b32e02cc30db266eb79ab07c9227ac7d72bd5236a513` |
| `endf/endf-b-viii.0/n-092_U_238-ENDF8.0.endf` | U-238 | 9237 | `ENDF-B-VIII.0_neutrons/n-092_U_238.endf` | 14.9 MB | `2843e227f5cb23dc3294afe216442f42eecabfd90c2919dbf233b58da3107510` |
| `endf/endf-b-viii.0/tsl-HinH2O-ENDF8.0.endf` | H-in-H2O | 1 | `ENDF-B-VIII.0_thermal_scatt/tsl-HinH2O.endf` | 17.4 MB | `3df6a73d8436a0654d00c989ccb427335548f4e1d7478a230023f576fcd91dd8` |

- **Source:** NNDC, Brookhaven National Laboratory, ENDF/B-VIII.0 download
  page `https://www.nndc.bnl.gov/endf-b8.0/download.html`:

  | archive | URL | bytes | sha256 |
  |---|---|---|---|
  | `ENDF-B-VIII.0_neutrons.zip` | `https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_neutrons.zip` | 310158456 | `a1cced11710eb36690c5ad7b64d414465c5849f5428904049c864e9beca28894` |
  | `ENDF-B-VIII.0_thermal_scatt.zip` | `https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_thermal_scatt.zip` | 61182020 | `042552d380e7fc3a1e7db948523b317ebaff260a08f5c8a786e94918e6e838f8` |

  The neutrons archive re-downloaded byte-identical to the gh:#1627 record.
- **Date accessed:** 2026-11-17 UTC.
- **Processing:** none. Each tape is the named archive member, renamed from
  NNDC's `n-ZZZ_Sy_AAA.endf` / `tsl-HinH2O.endf` to this directory's
  `-ENDF8.0.endf` form; content unmodified. None of the 37 existed here
  before, so nothing was skipped or replaced.
- **Licence / access terms:** as for the gh:#1627 tapes above.
- **Integrity:** one `endf/MANIFEST.tsv` row per file (37 rows; 221.7 MB in
  all). MAT is read from columns 67–70 of each tape's first record.

**Added 2026-11-18:** the remaining stable gadolinium isotopes (Gd-152, 154,
155, 158, 160), completing natural Gd with the Gd-156/157 tapes above, for the
MSRE N-1 control-rod nuclides in `outram-park-backend`
(`vv::msre_n1::ROD_NUCLIDES`). Downloaded directly from NNDC: members of the
same `ENDF-B-VIII.0_neutrons.zip`
(`https://www.nndc.bnl.gov/endf-b8.0/zips/ENDF-B-VIII.0_neutrons.zip`,
310158456 bytes, sha256
`a1cced11710eb36690c5ad7b64d414465c5849f5428904049c864e9beca28894`, verified
again on 2026-11-18). Renamed to the `-ENDF8.0.endf` form, content unmodified;
licence as above; one `endf/MANIFEST.tsv` row per file.

| file | nuclide | MAT | archive member | size | sha256 |
|---|---|---|---|---|---|
| `endf/endf-b-viii.0/n-064_Gd_152-ENDF8.0.endf` | Gd-152 | 6425 | `ENDF-B-VIII.0_neutrons/n-064_Gd_152.endf` | 3.00 MB | `3fff188ff8bf82ae6ecf46f8b735dc9c3f7b736c408422b07929381faac498de` |
| `endf/endf-b-viii.0/n-064_Gd_154-ENDF8.0.endf` | Gd-154 | 6431 | `ENDF-B-VIII.0_neutrons/n-064_Gd_154.endf` | 4.93 MB | `7dcafcc6a810130990d2d5f58ab700f34eae18a9149b40c1eebf26acccbca739` |
| `endf/endf-b-viii.0/n-064_Gd_155-ENDF8.0.endf` | Gd-155 | 6434 | `ENDF-B-VIII.0_neutrons/n-064_Gd_155.endf` | 2.55 MB | `453b2ad3711f3be62ad2c6c54d493dd9487a58ec21ea6d55e3efc0961460b43b` |
| `endf/endf-b-viii.0/n-064_Gd_158-ENDF8.0.endf` | Gd-158 | 6443 | `ENDF-B-VIII.0_neutrons/n-064_Gd_158.endf` | 2.44 MB | `a91e96a9aff91587b05033a979049743a89ac67df73064cf22ab3c5019d43635` |
| `endf/endf-b-viii.0/n-064_Gd_160-ENDF8.0.endf` | Gd-160 | 6449 | `ENDF-B-VIII.0_neutrons/n-064_Gd_160.endf` | 2.67 MB | `388edd8854000dba556fcf2c090762478eaea0b6a11ad07375001a034b678892` |

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
