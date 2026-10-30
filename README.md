# CBL 8BE090 - AI for Drug Discovery

Predicting how quickly small molecules dissociate from their protein targets (the dissociation rate constant, $k_{off}$).

## The project

Drug discovery has traditionally focused on binding *affinity* ($K_d$, IC50). However, *how long* a drug stays bound to its target – its residence time, $\tau = 1/k_{off}$ – often predicts its effect in the body better than affinity does. Measuring binding kinetics is slow and expensive, so being able to predict $k_{off}$ from the structure of the ligand and the protein would be very valuable.

In this project you will build a machine learning model that predicts $k_{off}$ for ligand–protein pairs. We provide the dataset. You decide how to:

- explore, check and curate the data;
- represent the molecules and the proteins;
- split the data into training, validation and test sets;
- choose which model(s) to try;
- evaluate and compare your models.

**Minimum:** a predictive model that you evaluate critically and present in your final presentation and report. **Beyond that:** you are free to go further, and explore adding other capabilities to your model.

## Data: `koff_data.csv`

The dataset contains 5,605 rows. Each row is a reported $k_{off}$ value for one ligand (SMILES) and one protein (amino-acid sequence). The data covers 300 unique protein sequences in 7 target classes.

### Data dictionary

| Column | Type | Unit | Description |
|--------|------|------|-------------|
| `seq_id` | string | – | Identifier of a unique protein sequence (`SEQ_0001`, …). A single target can have several `seq_id`s, for example for different constructs or mutants. |
| `target_name` | string | – | Name of the protein target, as reported in the source data (for example `MAPK1`, `HCV NS3/4A`). |
| `uniprot_id` | string | – | UniProt accession of the target. Empty if not available. |
| `target_class` | string | – | Target class: `Kinases`, `Enzymes`, `GPCRs`, `Chaperones`, `Viral Proteins`, `Gene Regulators` or `Others`. |
| `sequence` | string | – | Amino-acid sequence of the protein (one-letter codes, uppercase). |
| `smiles` | string | – | Structure of the ligand as a SMILES string. All SMILES can be parsed by RDKit, but they are **not canonical**. |
| `stereo_defined` | bool | – | `True` if the SMILES contains stereochemistry (assigned chiral centres or double-bond stereo), as determined with RDKit. |
| `pkoff` | float | – | **Prediction target.** $\mathrm{p}k_{off} = -\log_{10}(k_{off})$, with $k_{off}$ in s⁻¹. Higher pkoff means slower dissociation and a longer residence time. |
| `koff` | float | s⁻¹ | Dissociation rate constant, calculated as $k_{off} = 10^{-\mathrm{p}k_{off}}$. |
| `assay_method` | string | – | Experimental method, mapped onto a few labels: `SPR`, `BLI`, `stopped-flow`, `enzyme kinetics`, `radioligand`, `fluorescence`, `ITC`, `other`. Empty if not reported. |
| `temperature` | float | °C | Temperature of the measurement. Empty if not reported. |

### Processing and curation

The data was taken from KinetX (see [Source and licence](#source-and-licence)). It was only lightly processed:
- SMILES that RDKit could not parse were removed;
- a few sequences were repaired;
- the spelling variants of assay methods were mapped onto the labels above;
- `koff` was calculated from `pkoff`.

Beyond that, **the data has not been curated**. Exploring it, deciding what to clean and justifying those choices is part of the project.

### Source and licence

The data comes from the **KinetX** dataset, which is published under the **MIT licence**. If you use it, please cite:

> Li, J., Yang, Y., Gu, S., Wang, S., Liu, B., Liu, H., & Yao, X. (2026). BiCoA-Net: An Interpretable Bidirectional Co-Attention Framework for Predicting Protein-Ligand Binding Kinetics. *Acta Physico-Chimica Sinica*, 100284. https://doi.org/10.1016/j.actphy.2026.100284

KinetX itself was compiled from earlier databases and papers. If you discuss specific data or results from those, cite the original sources too.

## Getting started

### How to use this repository

Each group copies this repository into its **own group repository**, by cloning it and pushing to a new repo. Do not push your work to this course repository. For an introduction to Git and GitHub, see the GitHub lecture on 17/11.

### Expectations for your code

Your code is part of the group grade, so make sure someone else can reproduce your results:
- Commit an environment file (`pyproject.toml` + `uv.lock`, or `environment.yml`), so every group member uses the same packages.
- Fix the random seeds of splits and models.
- Add a README to your group repository that explains how to reproduce your main results.
- Keep a running log of how you use generative AI tools (tool, purpose, which code or text it affected, how you checked the output). You will need it for the AI usage statement in your report.

### Set up an environment

We recommend Python 3.11. Use **one** of the two options below. Packages listed are examples, feel free to explore others.

**Option A: conda**

```bash
conda create -n koff python=3.11
conda activate koff
conda install -c conda-forge rdkit pandas numpy matplotlib seaborn scikit-learn jupyter
pip install torch          # see https://pytorch.org for the right command for your system
```

**Option B: uv**

```bash
# run inside your group repository
uv init --python 3.11
uv add rdkit pandas numpy matplotlib seaborn scikit-learn jupyter torch

# scripts and notebooks can be run as follows:
uv run python {file_name}.py
uv run jupyter lab
```

### Useful packages

| Package | What for |
|---------|----------|
| [pandas](https://pandas.pydata.org/), [numpy](https://numpy.org/) | Loading, exploring and manipulating the data |
| [matplotlib](https://matplotlib.org/), [seaborn](https://seaborn.pydata.org/) | Plotting |
| [RDKit](https://www.rdkit.org/) | Cheminformatics: parsing SMILES, canonicalisation, fingerprints, descriptors, scaffolds |
| [scikit-learn](https://scikit-learn.org/) | Classical ML (random forests, kNN, linear models), splitting, metrics |
| [XGBoost](https://xgboost.readthedocs.io/) | Gradient-boosted trees |
| [PyTorch](https://pytorch.org/) | Deep learning (MLPs, CNNs, RNNs, transformers) |
| [PyTorch Geometric](https://pytorch-geometric.readthedocs.io/) | Graph neural networks on molecular graphs |
| [fair-esm](https://github.com/facebookresearch/esm) / [transformers](https://huggingface.co/docs/transformers) | Pretrained protein language models (for example ESM-2) for protein embeddings |

## Learning resources

**Books and tutorials**

- [Deep Learning for Molecules and Materials](https://dmol.pub/) (A. D. White): an online book on ML for chemistry, with code.
- [Foundations of Machine Learning](https://cs.nyu.edu/~mohri/mlbook/) (Mohri, Rostamizadeh & Talwalkar): for theory background.
- [TeachOpenCADD talktorials](https://projects.volkamerlab.org/teachopencadd/talktorials.html): hands-on notebooks on cheminformatics and structure-based drug design.
- [RDKit: Getting Started in Python](https://www.rdkit.org/docs/GettingStartedInPython.html)
- [scikit-learn user guide](https://scikit-learn.org/stable/user_guide.html) and [PyTorch tutorials](https://pytorch.org/tutorials/)
- [Practical Cheminformatics](https://practicalcheminformatics.blogspot.com/) (blog by Pat Walters), on applying ML to molecules in practice, including dataset splitting and evaluation.

**Papers to start from**
- Copeland, R. A. (2010). The dynamics of drug-target interactions: drug-target residence time and its impact on efficacy and safety. *Expert Opinion on Drug Discovery*, 5(4), 305–310.
- Sohraby, F., & Nunes-Alves, A. (2023). Advances in computational methods for ligand binding kinetics. *Trends in Biochemical Sciences*, 48(5), 437-449.
- Wu, Z. et al. (2018). MoleculeNet: a benchmark for molecular machine learning. *Chemical Science*, 9, 513–530.
- Öztürk, H., Özgür, A., & Ozkirimli, E. (2018). DeepDTA: deep drug–target binding affinity prediction. *Bioinformatics*, 34, i821–i829.
- Lin, Z. et al. (2023). Evolutionary-scale prediction of atomic-level protein structure with a language model (ESM-2). *Science*, 379, 1123–1130.
- Li, J. et al. (2026). BiCoA-Net (see [Source and licence](#source-and-licence)): the paper behind the KinetX dataset.
