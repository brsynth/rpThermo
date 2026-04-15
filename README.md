# rpThermo

Calculate the formation energy of chemical species and the Gibbs free energy for each reaction and for the heterologous pathway itself. This tool uses the [component contribution](https://gitlab.com/elad.noor/component-contribution) method for determining the formation energy of chemical species that are either not annotated, or cannot be found in the internal database.

For each species, the challenge is to find the corresponding compound in the eQuilibrator cache. To find the good compound, one tries to exact match species ID, InChIKey, InChI or SMILES and stops with the first hit. Then, if no compound has been found, in the last resort, the first part of species InChIKey is looked for within the cache. If the result (a list) is not empty, the first compound is taken.

Because we are interested in the thermodynamics of the pathway when the production of the target is optimized, we have modified coefficients of each reaction. We used a linear system solver (from [SciPy](https://www.scipy.org)) by giving the reaction that produces the target as objective and elimination of intermediate species as constraints.

## Input

Required:
* **-input**: (string) Path to the input file
* **-input_format**: (string) Valid options: tar, sbml. Format of the input file

Advanced Options:
* **-pathway_id**: (string, default=rp_pathway) ID of the heterologous pathway

## Output

* **-output**: (string) Path to the output file 

# Installation Guide

## Overview

`rpthermo` depends on `rplibs`, which depends on `cobra`, which requires `python-libsbml`.  
On Apple Silicon (`arm64`) macOS, `python-libsbml` is not available as a native Conda package.

Therefore, installation must be done using an **Intel (`osx-64`) Conda environment under Rosetta**.

---

## General case
```bash
conda install -c conda-forge rpthermo
```

---

## Apple Silicon macOS (M1/M2/M3)

### 1. Install Rosetta 2

```bash
softwareupdate --install-rosetta --agree-to-license
```

### 2. Install rpThermo

```bash
CONDA_SUBDIR=osx-64 conda install -c conda-forge rpthermo
```

Or with mamba:

```bash
CONDA_SUBDIR=osx-64 mamba install -c conda-forge rpthermo
```

### 3. Persist platform setting

```bash
conda config --env --set subdir osx-64
```

### 4. Verify installation

```bash
python -c "import rpthermo; print('rpthermo installed successfully')"
```

### 5. (Optional) Dev installation

```bash
CONDA_SUBDIR=osx-64 conda env create -f environment.yaml
```

---

## Troubleshooting

### Solver fails on Apple Silicon

Make sure you are using:

```bash
CONDA_SUBDIR=osx-64
```

### Wrong architecture environment

Check:

```bash
conda config --show subdir
```

Expected output:

```bash
subdir: osx-64
```

## Run

### rpThermo process
**From Python code**
```python
from rptools.rplibs import rpSBML
from rptools.rpthermo import runThermo

pathway = rpSBML(inFile='lycopene/rp_003_0382.sbml').to_Pathway()

runThermo(pathway)

print(pathway.get_thermo_dGm_prime().get('value'))
print(pathway.get_fba_dGm_prime())
```
```bash
>>> -3079.477259696032
>>> {'value': -3079.477259696032, 'error': 7.250256007547839, 'units': 'kilojoule / mole'}
```
**From CLI**
```sh
python -m rptools.rpthermo <input_sbml> <outfile>
```

## Tests
Test can be run with the following commands:

### Natively
```bash
cd tests
pytest -v
```

## CI/CD
For further tests and development tools, a CI toolkit is provided in `ci` folder (see [ci/README.md](ci/README.md)).


## Authors

* **Joan Hérisson**
* **Melchior du Lac**

## Acknowledgments

* Thomas Duigou


## Licence
rpThermo is released under the MIT licence. See the LICENCE file for details.
