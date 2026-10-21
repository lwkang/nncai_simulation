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

Figure/table numbers refer to the current revision. The manually drawn Figure 14 is outside the reproduction package. All scenarios are synthetic; credentials and private API transcripts are excluded.

## Reproduction after release

The following commands require the complete package and cannot run from this README-only repository:

```bash
conda env create -f environment.yml
conda activate nncai_section7
python run_all.py --verify
python run_all.py --reproduce
python run_all.py --verify
```

Offline reproduction requires no API keys. It reads the bundled data and exports 10 PDF panels and Table 10 (LaTeX and CSV) under `outputs/`. Use a new output directory for subsequent exports. Optional `--source-check` runs rebuild simulation data with frozen seeds and block outbound network connections.

## Validation and limitations

The package was checked in an existing Python 3.11 environment: ten unit tests and the standalone audit suite passed, and offline figure/table export succeeded. A fresh Conda installation has not yet been validated.

- Full-seed reconstruction matches A1, scalability, A3, A4 and resource contention. For interoperability (A2), the 540 `label_only` and `cso_rule` records match; the other 270 historical `cso_llm` records cannot be reconstructed exactly with offline fallback. Fig. 16(a,b) uses the zero-overlap subsets of the two matching arms.
- Fig. 16(c) is reproduced from 972 frozen model-test records. New live-provider outputs may differ from the historical observations.
- A1 and A4 preserve both actual post-fix reruns and audited carry-forward records, identified by `evidence_status`. PDF layout may vary with fonts and runtime.
- Table 10 describes a controlled synthetic single-UGV study with 200 paired seeds and an 80-second deadline; its deadline-completion improvement confidence interval includes zero.

The full release will include data provenance, detailed reproduction instructions and licensing terms.
