# Code review: original Monte Carlo prototype

This review covers the original script committed in June 2024. It is a static review of the source, not a validation study. The code is preserved for reference, but it should not be used for scientific calculations.

## Problems that change the results

### 1. The Lennard-Jones calculation mishandles zero distances

The distance matrix includes each atom paired with itself, so its diagonal is zero. The Lennard-Jones expression divides by those distances and produces infinities. The code then removes every non-finite value with `np.isfinite`. That does not fix the calculation: it silently drops those terms. The same thing can happen when two different atoms overlap.

The matrix also contains both `i,j` and `j,i`, so every finite pair interaction is counted twice.

### 2. The energy units do not match the Metropolis equation

The code sets `epsilon = 0.238` without stating its unit, while the acceptance rule uses the Boltzmann constant in joules per kelvin. The Lennard-Jones energy and `k_B T` must use the same energy unit for `exp(-delta_energy / (k_B T))` to be meaningful. As written, the exponent is not dimensionally consistent.

### 3. The moves break the protein's bonded geometry

Each Monte Carlo step moves one atom independently. There are no bond, angle, torsion, or other bonded terms, and no constraints to keep the chain intact. A move can therefore stretch or break covalent bonds. The script may produce coordinates, but that does not make them physically plausible protein structures.

### 4. The energy model is not parameterized for the atoms being read

Every atom is assigned the same `sigma` and `epsilon`. The script does not distinguish carbon, nitrogen, oxygen, sulfur, hydrogen, or different chemical environments. It also does not apply the exclusions and scaling rules needed for bonded and near-bonded pairs in a molecular force field. A single generic pair potential is not an all-atom protein force field.

## Problems with the simulation and outputs

### 5. Parallel runs share one mutable structure

Every worker receives the same Bio.PDB structure object. Each worker later writes its coordinates back into the same atom objects. The runs are not independent, and the returned list contains references to the same structure rather than separate replicas. Saving those results can therefore produce repeated or race-dependent structures.

### 6. The random-number generators are not seeded

The script mixes Python's `random` module with NumPy's global random generator. There is no seed parameter or independent generator per run, so a result cannot be reliably reproduced.

### 7. The displacement setting is not passed through the full simulation

`monte_carlo_step` accepts `max_displacement`, but `monte_carlo_folding` does not expose it or pass it to the step function. The outer simulation therefore always uses the step function's default value.

### 8. MDTraj is not used to analyse the simulation results

The script loads the starting PDB with MDTraj, but the returned trajectory and topology are not used later. The structures written at the end are not passed through a structural analysis step.

### 9. Running the original file has side effects

The original script downloads PDB entry 1AKE and starts simulations as soon as the file is executed. It has no command-line interface, no input validation, and no main guard. The fixed download path is also assumed rather than taken from the path returned by Biopython.

## What would be needed for a scientific implementation?

These issues cannot be fixed by changing one or two constants. A defensible all-atom simulation would need a validated force field, atom-specific parameters, correct bonded and non-bonded terms, exclusions, consistent units, geometry-preserving moves or a suitable molecular simulation engine, independent replicas, and structural validation.

That is a different project from this small prototype. The later [Protein-MC-Go](https://github.com/sialock/protein-folding-) rewrite takes a narrower Cα-only, reference-biased teaching approach and documents its limits. It should not be confused with an all-atom folding method either.

## Review summary

The original script is useful as a record of an early coding experiment. Its energy values and output coordinates should not be interpreted as evidence of protein folding, physical stability, or molecular dynamics.
