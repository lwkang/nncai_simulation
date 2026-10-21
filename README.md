# NNCAI — Simulation Code and Data

**Manuscript status: under revision. This public repository currently contains only this README.**

> 待文章接收后，完整数据和代码将会开源。
>
> The complete simulation code and datasets will be made publicly available in this repository upon acceptance of the manuscript.

The planned full release includes the simulation source, frozen datasets and offline reproduction tools for the Section VII case study of the NNCAI manuscript. Figure and table numbers refer to the current revision and may change before publication. Figure 14 is a manually drawn schematic and is outside this reproduction package.

## Planned release contents

- `core/`, `config.py`: multi-agent simulation, communication, interoperability and trust mechanisms.
- `experiments/`: experiment drivers and paired resource-contention analysis.
- `data/`: seven canonical record-level CSV datasets and frozen model-test protocols.
- `analysis/`, `figure_helpers/`, `export_subplots_final.py`: statistics and figure/table export.
- `tests/`, `scripts/`: audit tests, privacy scan and integrity checks.
- `docs/`: data dictionary, source mapping, provenance and reproduction boundaries.

## Data and manuscript outputs

| Experiment | Canonical input | Rows | Manuscript output |
|---|---|---:|---|
| Lightweight communication | `data/ablation1/episodes.csv` | 960 | Fig. 15(a,b) |
| Scalability | `data/scalability/episodes.csv` | 360 | Fig. 15(c) |
| Interoperability | `data/ablation2/episodes.csv` | 810 | Fig. 16(a,b), using the two plotted arms |
| Frozen model decisions | `data/model_test/case_results.csv` | 972 | Fig. 16(c) |
| Trust governance | `data/ablation3/episodes.csv` | 5,400 | Fig. 17(a–c) |
| Combined pressures | `data/ablation4/episodes.csv` | 9,600 | Fig. 18 |
| Paired resource contention | `data/resource_contention/episodes.csv` | 1,200 | Table 10 |

The complete private package contains **19,302 records** across seven CSV files. `data/model_test/` also contains the frozen case grid, prompts and historical provider/model settings. These describe synthetic scenarios; private API transcripts and credentials are excluded.

## Planned setup and reproduction commands

**The commands below are documented for the full release after manuscript acceptance. They cannot run from this README-only repository because the code, data and environment files are not yet public.**

Use Python 3.11. From the full release repository root:

```bash
conda env create -f environment.yml
conda activate nncai_section7
```

Alternatively, install into a Python 3.11 virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Offline reproduction requires no API keys or provider account. The OpenAI SDK is a dependency of the simulation modules; the default reproduction commands do not make model API requests.

## Verify data and reproduce figures/tables

```bash
python run_all.py --verify
python run_all.py --reproduce
python run_all.py --verify
```

The exporter creates exactly **10 panel PDFs** in `outputs/figures/` and Table 10 in `outputs/tables/table10.tex` and `table10_values.csv`. It reads the bundled CSVs without generating new episodes. The second verification checks the generated output set as well as the data. Use a new empty destination for any additional export:

```bash
python run_all.py --reproduce --output-dir outputs/second_export
```

## Rebuild simulation data (optional, offline)

```bash
python run_all.py --source-check a1 --seed-mode smoke --output-dir rebuild_a1_smoke
python run_all.py --source-check a1 --seed-mode full --output-dir rebuild_a1_full
```

Available groups: `a1`, `scalability`, `a2`, `a3`, `a4`, `t12_3`. Use a new output directory for every run. Frozen seeds are used and outbound sockets are blocked by the source-check harness. The unrestricted `--simulate` option is disabled.

## Reproduction boundaries

- A2 retains all 810 historical records. The 540 `label_only` and `cso_rule` records match offline reconstruction; Fig. 16(a,b) selects their zero-overlap subsets. The remaining 270 historical `cso_llm` records cannot be reconstructed exactly by replacing historical live model decisions with offline fallback. A successful A2 command means its plotted arms pass, not that all 810 records match. Inspect the generated QA JSON for the historical-arm mismatches.
- Fig. 16(c) uses a separate frozen 972-record, three-round dataset. Its figure is reproduced offline. Future live-provider outputs may differ. The optional legacy live-provider experiment driver is not an exact replay harness for this frozen three-round dataset; any new results must be stored separately and labelled accordingly.
- A1 includes 480 actual post-fix rerun records and 480 audited carry-forward records. A4 includes 2,400 actual post-fix rerun records and 7,200 audited carry-forward records. The original `evidence_status` values are preserved. Matching a new simulation does not change historical provenance.
- Figure 18 values reproduce, but PDF size/crop and font rendering can vary by runtime. Pixel-identical manuscript PDFs are not promised.
- Table 10 is a controlled synthetic single-UGV test with 200 paired seeds and an 80-second deadline. Its deadline-completion improvement confidence interval includes zero; it does not establish universal task-completion gains.

## Tests and release integrity

```bash
python -m unittest discover -s tests -v
python tests/test_audit.py
python scripts/prepublish_check.py
python scripts/verify_release_hashes.py
```

The full release will include validation results, the data dictionary, figure/table mapping and source provenance. The private package has been checked with Python 3.11: ten unit tests and the independent audit suite passed; offline export generated ten PDF panels and Table 10. Full-seed reconstruction matched A1, scalability, A3, A4 and resource contention; the A2 historical-model exception above remains. A fresh Conda environment installation has not yet been validated.

## Publication and licensing

Only this overview is public during manuscript review. The complete code and datasets remain private until manuscript acceptance. No reuse license has been selected; this repository does not yet grant open-source reuse rights. Before public release, finalize code/data licenses and publication permissions, repeat the privacy and integrity checks, and update this README with the accepted article citation. The full code and datasets will be released at this repository URL after acceptance. No code or data files are currently available here.
