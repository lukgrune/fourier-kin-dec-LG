# fourier-kin-dec-LG

# Kinetic Deconvolution in Fourier Space

This Python script performs kinetic deconvolution of time-resolved difference structure factors directly in Fourier (reciprocal) space. Given a kinetic model (species occupancies over time) and a series of experimental difference structure factors (ΔF) with corresponding uncertainties (σ), the program reconstructs the difference structure factors associated with individual kinetic intermediates while propagating experimental uncertainties.

This repository contains the version of the kinetic deconvolution script used for the publication *Atomic-level structural mechanism of phytochrome photoconversion from femto- to milliseconds* (Meszaros, Grunewald et al., submitted, 2026).

## Requirements

The script requires Python 3 with the following packages: NumPy, pandas. The remaining imported modules (`argparse`, `pathlib`, and `collections`) are part of the Python standard library.

The kinetic deconvolution itself does not require additional crystallographic software. However, input files must be prepared beforehand using suitable crystallographic software, such as CCP4 or Phenix.

## Input Data

The program requires:

- A **dark-state phase file** (`.hkl`) containing reflection phases.
- A **dark-state amplitude/sigma file** (`.hkl`).
- For each timepoint:
  - A **difference structure factor file** (`.phs`) containing amplitudes and phases.
  - A corresponding **sigma file** (`.hkl`) containing experimental uncertainties.
- A **concentration matrix** (`.csv`) containing the occupancy of each kinetic species at every timepoint. The names of the timepoints must match the corresponding `.phs` and `.hkl` files.

The expected column layout for each input file is defined at the beginning of the Python script (`PHS_COLUMNS`, `HKL_COLUMNS`, and `DARK_PHASE_COLUMNS`).

The required `.hkl` and `.phs` files can be generated from existing MTZ files using crystallographic software such as `mtz2various` from CCP4.

## Usage

Before running the script, organize the corresponding `.phs` and `.hkl` files into separate directories (e.g., `phs` and `hkl`). Ensure that the timepoint labels in the concentration matrix match the input filenames.

The script can be executed using:

```bash
python kinetic_deconvolution.py \
    --concentrations concentrations.csv \
    --phs-dir phs \
    --hkl-dir hkl \
    --dark-sigma dark_scaled.hkl \
    --dark-phase dark_phase.hkl \
    --min-occurrence 12 \
    --out-prefix state
```

The script first loads and displays the concentration matrix, including the number and names of the kinetic intermediates. It then performs phase correction and amplitude sign adjustment of the input difference structure factors.

The kinetic decomposition is performed independently for each reflection using the available experimental observations.

Reflections are filtered according to their occurrence across the experimental timepoints. At least as many valid observations as kinetic states are required, and the corresponding concentration matrix must have full column rank.

We recommend retaining reflections present in approximately 70% of the experimental timepoints. For the analysis presented in the publication, we required each reflection to be present in at least 12 of the 17 timepoints.

The resulting kinetic modes are saved as:

- `.phs` files containing the deconvoluted difference structure factor amplitudes and phases.
- `.hkl` files containing the corresponding propagated uncertainties.

These files can be used for subsequent difference electron density map calculations or structure factor extrapolation.

The generated `.phs` files can be converted to `.mtz` format using `f2mtz` from CCP4. Difference electron density maps can then be calculated using `fft` and visualized in programs such as Coot.

## Citation

This version of the code was used for the analysis presented in:

- Meszaros, P., Grunewald, L. et al., *Atomic-level structural mechanism of phytochrome photoconversion from femto- to milliseconds*, submitted (2026).

For a detailed description of the reciprocal-space kinetic deconvolution method, please refer to:

- Grunewald, L. et al., *Visualizing Reaction Pathways via Reciprocal Space Kinetic Decomposition*, submitted (2026).

## License and Original Repository

This repository contains the kinetic deconvolution script from the original `fourier-kin-dec` repository.

Original repository: https://github.com/Westenhoff-Lab/fourier-kin-dec/

The code is distributed under the MIT License. The original copyright and license notices are retained.
