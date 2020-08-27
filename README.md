# Comparison of electrostatic potential and shape
This repository contains a small code snippet to calculate similarities of shapes and electrostatic potentials between molecules. It is based on Python3, RDKit, Numpy and Scipy. The package furthermore contains functionalities to embed (create 3D coordinates) with a constrained core using RDKit functions.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
  * [ESP similarity](#esp-similarity)
  * [Embedding, alignment and similarity] (#embedding-alignment-and-similarity)
  * [Jupyter demo] (#jupyter-demo)
- [Contact](#contact)

## Installation

It is easiest to install all required package inside a conda environment. If you want to use jupyter notebooks, and plot 3D representations using py3Dmol, create the following environment (here named `espsim`):

`conda create -n espsim -c conda-forge conda-forge::rdkit numpy scipy jupyter py3dmol`

If you don't need jupyter, you can create an environment with less packages:

`conda create -n espsim -c conda-forge conda-forge::rdkit numpy scipy`

Next, activate the environment:

`conda activate espsim`

(or `source activate espsim` if you have an older version of conda). If you don't want to use conda, install Python3, RDKit, Numpy and Scipy, as well as optionally Jupyter and py3Dmol in any way convenient to you.

Now, install espsim as a pip package as follows, by (1) changing the directory to wherever you saved espsim (use the correct path instead of `<pathtoespsim>` and compiling the package (2):

1. `cd <pathtoespsim>`
2. `pip install -e .`

Then you can use `import espsim` or from `espsim import ...` in your code. To test your installation, run

`python scripts/test_imports.py`

which should print `Test passed, imports work fine.` or tell you which package/module could not be imported.

## Usage

Within a python script, you can either use only the ESP similarity routine on previously embedded, aligned RDKit molecule objects using the function `GetEspSim()` or use `EmbedAlignConstrainedScore()` to embed, align and score molecules. 

### ESP similarity

An example is provided in `scripts/test_esp_function.py`. Execute it via

`python scripts/test_esp_function.py`

The script loads two RDKit molecules from file, that have been previously embedded and aligned, and calls `GetEspSim()`, which calculates the overlap integrals of the electrostatic potentials of the two molecules. The function returns the ESP similarity. The integration is performed via fitting the `1/r` term in the Coloumb potential by Gaussians

\begin{equation*}
 \frac{1}{r} \simeq 0.3001e^{-0.0499r^2} + 0.9716*e^{-0.5026r^2} + 0.1268*e^{-0.0026r^2}
\end{equation*}

The overlap integral between the potentials of molecule A and B

\begin{equation*}
\int \phi_A \phi_B dV = \int \sum_{i=1}^N \sum_{j=1}^M \frac{q_i}{|r-R_i|} \frac{q_j}{|r-R_j|} dV
\end{equation*}

then becomes a sum of a large number of two-center integrals of Gaussian for which an analytic solution exists:

\begin{equation*}
 \int e^{-\alpha_k|r-R_i|^2}e^{-\alpha_l|r-R_j|^2} = \left( \frac{\pi}{\alpha_k+\alpha_l} \right) ^ \frac{3}{2} * e^ \left( \frac{-\alpha_k\alpha_l}{\alpha_k+\alpha_l} | R_i - R_j| \right)
\end{equation*}

This method is based on Good et al, [doi.org/10.1021/ci00007a002](https://doi.org/10.1021/ci00007a002). After integration, the overall similarity is calculated as the Carbo similarity by Carbo et al, [doi.org/10.1002/qua.560320412](https://doi.org/10.1002/qua.560320412) and Carbo, Arnau, Intl. J. Quantum Chem 17 (1980) 1185 (no doi available):

\begin{equation*}
 S_{ESP}= \frac{\int \phi_A \phi_B dV }{\left(\int \phi_A \phi_A dV  \int \phi_B \phi_B dV \right)^\frac{1}{2} }
\end{equation*}

### Embedding, alignment and similarity

Usually, the molecules that are to be compared have not been previously embedded and aligned, thus espsim provides a convenient way to do so. The function `EmbedAlignConstrainedScore()` takes a probe molecule, one or more reference molecules, and a core that is to be constrained (with 3D coordinates!) as input, computes constrained embeddings, compares the shape similarities of all combinations and returns both the shape and ESP similarity. The script 'scripts/test_embedalignscore.py' uses this function to create 10 conformers of three probe molecules each, and compare their similiarities to 10 conformers of nine reference compounds each:

`python scripts/test_embedalignscore.py`

The function `EmbedAlignConstrainedScore()` takes several optional input arguments as well: `prbNumConfs` and `refNumConfs`, which are the number of conformers to be created for each probe and reference molecule. (The default is 10 each). The more conformers, the more accurate the results, since a better alignment may be found. Thus, the computed shape and ESP similarities are actually lower bounds to the true values (with perfectly aligned conformers). The function also takes `prbCharge` (list or 1D array of partial charges for the probe molecule) and `refCharges` (list of list, or 2D array of partial charges of all reference molecules) as input, where a partial charges can be specified. If not specified (default), RDKit partial charges are calculated (Gasteiger charges). The option to input partial charges is especially convenient if you have already precomputed charges, for example from quantum mechanics. The order of the inputted partial charges must be the same as the atoms in the RDKit molecules.

### Jupyter demo

The `scripts` folder also holds a short demo of espsim on Jupyter. To view it, power up a notebook (you must have jupyter and py3Dmol installed, see Installation instructions):

`jupyter notebook`

and click on `scripts` and `short_demonstration.ipynb`. The file holds more information and explanations about espsim, as well as some 3D visualizations of molecule embeddings.

## Contact
Feel free to post questions, feedback, errors or concerns on github, or email to eheid@mit.edu.