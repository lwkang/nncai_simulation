# NNCAI Simulation Code and Data

This repository provides an overview of the simulation package for the Section VII case study of the NNCAI manuscript, which is currently under revision.

**Only this README is public. The complete code and datasets will be released here upon manuscript acceptance.**

## Package overview

The planned release includes the simulation source, seven canonical CSV datasets (19,302 records), frozen model-test protocols, figure/table exporters, tests and documentation.

| Experiment | Records | Manuscript output |
|---|---:|---|
| Lightweight communication | 960 | Fig. 15(a,b) |
| Scalability | 360 | Fig. 15(c) |
| Interoperability | 810 | Fig. 16(a,b), selected arms |
| Frozen model decisions | 972 | Fig. 16(c) |
| Trust governance | 5,400 | Fig. 17(a-c) |
| Combined pressures | 9,600 | Fig. 18 |
| Paired resource contention | 1,200 | Table 10 |

Figure/table numbers refer to the current revision. All scenarios are synthetic; credentials and private API transcripts are excluded.

## Reproduction after release

These instructions require Git, Conda and the complete release, which will be available after manuscript acceptance. The current README-only repository does not contain the files needed to run them.

```bash
# Download the complete release and enter its root directory
git clone https://github.com/lwkang/nncai_simulation.git
cd nncai_simulation

# Create and activate the Python 3.11 environment with all dependencies
conda env create -f environment.yml
conda activate nncai_section7

# Validate the bundled datasets and manuscript statistics
python run_all.py --verify

# Generate the figure panels and table from the bundled datasets
python run_all.py --reproduce
```

The reproduction runs offline without API keys or new simulation episodes. Outputs are written to:

- `outputs/figures/`: 10 PDF panels for Figures 15-18.
- `outputs/tables/`: `table10.tex` and `table10_values.csv` for Table 10.

To repeat the export, use a new destination, for example `python run_all.py --reproduce --output-dir outputs/run2`; existing figure PDFs will not be overwritten.

The full release will include data provenance, detailed reproduction instructions and licensing terms.
