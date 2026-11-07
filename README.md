# Protein Folding with Monte Carlo

This repository contains an early experiment from June 2024. I was testing whether a simple Monte Carlo loop and a Lennard-Jones-style energy calculation could be used to change protein coordinates.

It is kept here as a record of that first attempt. **It is not a working protein-folding tool and should not be used to predict or validate protein structures.**

## What the original script tried to do

The original script was written around PDB entry 1AKE. It downloaded the structure, read its atoms, calculated a pairwise energy, moved one atom at a time, and accepted or rejected each move using a Metropolis-style rule. It then tried to run several copies of the simulation in parallel and save the resulting PDB files.

The source is now in [`legacy_prototype.py`](legacy_prototype.py). Direct execution is disabled so the old script cannot unexpectedly download a structure and run the flawed simulation.

## Why it is kept as an archive

A closer review found problems that affect the calculation itself, not just the code style. For example, the energy calculation handles zero distances incorrectly, the energy units are not consistent with the Boltzmann factor, and moving atoms independently breaks the protein's bonded geometry. The parallel runs also share and modify the same structure object.

The full review, including the other issues and what would be needed to address them, is in [`CODE_REVIEW.md`](CODE_REVIEW.md).

## A separate, maintained project

The later [Protein-MC-Go](https://github.com/sialock/protein-folding-) repository is a separate rewrite with a package layout, tests, reproducible random seeds, and clearer structural diagnostics.

That project is also a **reference-based Cα teaching model**, not a de novo protein-folding predictor or a validated molecular-dynamics engine. The two repositories are kept separate; code from this archive is not imported by Protein-MC-Go.

## Repository contents

- `legacy_prototype.py` — the original exploratory functions, preserved for reading.
- `CODE_REVIEW.md` — a technical review of the original approach.
- `.gitignore` — ignores local Python files and generated structures.

There is no dependency file or supported installation procedure because this archive is not maintained as runnable software.

## License

No license has been added to this repository. Please contact the repository owner before reusing the code.
