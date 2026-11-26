# NNCAI Simulation Datasets

Datasets supporting the Section VII simulation results of the NNCAI manuscript, currently under revision.

**The datasets are publicly available under CC BY 4.0. Simulation and figure-generation code remains private and is planned for release upon manuscript acceptance.**

## Available data

Seven CSV files contain **19,302 records**. Frozen model-test prompts, the case grid and historical settings are also provided. All scenarios are synthetic; credentials and private API transcripts are excluded.

| Experiment | Records | Data | Manuscript output |
|---|---:|---|---|
| Lightweight communication | 960 | [CSV](data/ablation1/episodes.csv) | Fig. 15(a,b) |
| Scalability | 360 | [CSV](data/scalability/episodes.csv) | Fig. 15(c) |
| Interoperability | 810 | [CSV](data/ablation2/episodes.csv) | Fig. 16(a,b), selected arms |
| Frozen model decisions | 972 | [CSV and protocol](data/model_test/) | Fig. 16(c) |
| Trust governance | 5,400 | [CSV](data/ablation3/episodes.csv) | Fig. 17(a-c) |
| Combined pressures | 9,600 | [CSV](data/ablation4/episodes.csv) | Fig. 18 |
| Paired resource contention | 1,200 | [CSV](data/resource_contention/episodes.csv) | Table 10 |

Figure/table numbers refer to the current manuscript revision. See the [data dictionary](docs/DATA_DICTIONARY.md), [figure/table mapping](docs/FIGURE_TABLE_SOURCE_MAP.json), [data notes](docs/DATA_NOTES.md) and [file manifest](DATA_MANIFEST.json) for units, missing values, historical provenance and use of the recorded data.

## Download

```bash
git clone https://github.com/lwkang/nncai_simulation.git
cd nncai_simulation
```

Files can also be downloaded through GitHub's **Code > Download ZIP**. SHA-256 checksums are provided in [SHA256SUMS.txt](SHA256SUMS.txt). For example, with `shasum` installed, run `shasum -a 256 -c SHA256SUMS.txt` from the repository root.

## Code availability

This is a data-only release. Simulation and figure-generation commands require the private code and cannot be run from this repository yet. Reproduction instructions will accompany the code release.

## License and attribution

Data and accompanying documentation are licensed under [CC BY 4.0](LICENSE). See [license scope and attribution](LICENSE_SCOPE.md); this license does not apply to private source code.
