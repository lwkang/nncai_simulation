# Data interpretation and provenance

This release preserves the recorded research tables and frozen protocol files byte-for-byte from the reviewed input archive. It does not replace historical observations with later reconstructions. The manuscript is under revision; figure/table numbers may change.

## Historical origins and reconstruction scope

- A1 has 480 `ACTUAL_POST_P0_REPLAY` rows and 480 `P0-INVARIANT_LEGACY_CARRY_FORWARD` rows. A4 has 2,400 actual post-fix reruns (the `a1_only` arm) and 7,200 audited carry-forward rows. These are historical origin labels; later matching reconstructions do not change them.
- Current full-seed offline reconstructions match the compared numerical fields in A1 (960), scalability (360), A3 (5,400), A4 (9,600) and resource contention (1,200).
- A2 retains all 810 historical records. Its `label_only` and `cso_rule` arms (540 rows across all overlaps) match offline reconstruction. Figures 16(a,b) use only their zero-overlap subsets (180 rows total). The 270 historical `cso_llm` records do not exactly match a no-model fallback reconstruction and cannot be treated as a complete offline replay. The mismatch counts are: capability_match_rate 22, semantic_mismatch_count 5, replan_count 47, cso_validity_rate 22, llm_accept_rate 58, task_success_rate 2, episode_completion_time 38. These records are included transparently rather than silently removed or modified.
- Figure 16(c) uses a separate frozen 972-case table, not the historical A2 `cso_llm` episode arm. Each round/provider evaluates 108 grid cases, of which 90 have distinct prompt hashes and 18 reuse an identical prompt outcome. Thus the table contains 810 cache misses and 162 hits, not 972 independent API requests. The cache is scoped per round/provider. Source-case links and cached-decision consistency were checked. Future live-provider outcomes are not promised to match.

## Missing values and output conventions

- Model tests have three invalid length-truncated cases. `valid` is False, `llm_decision` is blank, and `matches_reference` is False for these rows. Empty error fields in the other 969 rows mean no recorded validation error. Invalid cases stay in the all-case accuracy denominator.
- In resource contention, 120 confirmation times are blank because the task was not confirmed by the 80-second deadline; 87 dispatch/wait times are blank because dispatch was not observed. Conversely, `genuine_wait_censored_at_s` is 80 in those 87 cases and blank in the other 1,113. `first_assignment_id` is blank in 54 rows because no assignment was observed. Restricted timing columns replace unobserved events by 80; do not drop these seeds.
- `bw_utilization` is a recorded admitted-byte load ratio that includes queued work, not bounded link occupancy; it can exceed 1. Modelled queue delay can exceed the episode horizon. These conventions were checked against the frozen simulator and preserved.
- Some recorded rates use defined defaults when no relevant event occurs: for example rollback_success_rate is 1 with no rollback attempt, and llm_accept_rate is 1 with no policy decision. Consult event indicators before interpretation. `detection_time_s == 300` denotes an undetected injection, not a measured detection at 300 seconds.
- `fixture_seed_independent_key` is a historical alias of the fixture hash and includes the seed despite its name. Preserve the field for compatibility; do not infer a seed-independent invariant from its name.

## Figure use and study scope

The figure filters and denominators are recorded in [FIGURE_TABLE_SOURCE_MAP.json](FIGURE_TABLE_SOURCE_MAP.json). Figure 17 uses actual injected episodes, not all 600 nominal episodes per cell. Figure 18 evasion also uses injected episodes only. Table 10 uses paired seeds without dropping censored observations. Its controlled single-UGV, 80-second study does not establish universal task-completion gains; the paired deadline-completion improvement interval includes zero.

This is a synthetic simulation/model-decision dataset, not field validation. Passing integrity and reconstruction checks does not establish all modelling assumptions or independently authenticate private historical provider transcripts. The manuscript itself and private API transcripts are not part of this data deposit. The current audit checked the supplied source/data contracts and the source-to-figure/table calculations.
