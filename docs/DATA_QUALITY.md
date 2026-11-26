# Data release checks - 2026-11-26

Release: `data-2026-11-26`. Data files are unchanged from the input archive.

| Check | Result |
|---|---|
| File completeness and source archive equality | All 10 data/protocol files match byte-for-byte |
| CSV parsing, record keys, conditions, types, null conventions, arithmetic identities, model prompts/references/cache joins and censoring rules | 649 assertions passed across 19,302 CSV rows |
| Secrets, private keys, personal emails/paths and credential-bearing URLs in the data/protocol files | No matches detected by the bounded content scan |
| Dataset counts and specified source-to-manuscript statistics | Passed against the supplied verification contract |
| Full-seed numerical reconstruction | A1 960, scalability 360, A3 5,400, A4 9,600 and contention 1,200 compared fields match |
| A2 reconstruction | Partial: 540 rule/label rows match; historical 270 model-arm rows have the documented differences |
| Frozen model evaluation | Grid/prompt hashes, reference labels, cached-source joins, recorded decisions and invalid-response treatment are consistent; live services were not queried |
| Figure/table export using private source | Ten panel PDFs and six-row Table 10 generated from the bundled tables |
| Private-source tests | Ten standard unittest cases and the standalone scenario audit passed |

Checks used an existing Python 3.11 environment. No fresh environment installation or independent comparison to the full manuscript was performed. No historical records were overwritten and no provider API calls were made during the checks. The scope and exceptions are explained in [DATA_NOTES.md](DATA_NOTES.md); the private simulator and audit scripts are not distributed here.

This report describes the checks performed, not an unconditional warranty of error-free scientific data or a substitute for model validation. Automated sensitive-pattern scanning is bounded and cannot guarantee detection of every possible sensitive value.
