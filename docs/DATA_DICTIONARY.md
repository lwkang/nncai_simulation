# Data dictionary

CSV files are UTF-8 with a header row. Fractions use 0-1 rather than percentages unless specified. Booleans are literal `True`/`False`. Blank cells encode the documented unobserved/invalid/not-applicable values; they are not zero. Zero and sentinel conventions inherited from the simulator are preserved. Data files have not been reserialized or rounded.

## Experimental designs

| Dataset | Unique key | Frozen design |
|---|---|---|
| ablation1 | condition, bw_mbps, seed | 4 conditions x 8 bandwidths (0.01, 0.03, 0.05, 0.1, 0.3, 0.5, 1, 2 Mbps) x 30 seeds |
| scalability | condition, n_agents, seed | 2 conditions x 6 team sizes (4, 9, 14, 21, 31, 44) x 30 seeds; fixed 2 Mbps |
| ablation2 | condition, error_rate, overlap_level, seed | 3 conditions x 3 error rates (0, 0.1, 0.3) x 3 overlap conditions (0, 0.25, 0.5) x 30 seeds; fixed 5 Mbps |
| ablation3 | condition, pollution_rate, seed | 3 trust conditions x 3 injection probabilities (0.05, 0.15, 0.3) x 600 seeds; fixed 5 Mbps |
| ablation4 | pillar_config, pressure_mode, seed | 4 mechanism sets x 4 pressure modes x 600 seeds |
| model_test | round_id, provider, case_id | 3 rounds x 3 providers x 108 cases; each provider/round has 90 unique prompt outcomes and 18 cache hits |
| resource_contention | seed, tie_order, arm | 200 paired seeds x 2 report orders x 3 trust arms; one UGV, 80-second deadline, 5 Mbps |

A1 conditions are `raw_sharing` (periodic full-state), `event_triggered_raw` (raw payload with event trigger/queue), `cso_event` (lightweight CSO with event trigger/queue), and `cso_intent_aware` (lightweight plus capability filtering). In the scalability experiment `cso_event` also enables interoperability; the same label does not imply identical treatment across datasets. Team sizes map to 3, 6, 10, 16, 24 and 34 genuine targets.

A2 conditions are `label_only`, `cso_rule`, and historical `cso_llm`. A3/contention arms are `no_trust`, `trust_score`, and `trust_rollback`; `trust_score` includes admission/isolation but no later audit/rollback. In A4, `bw_only` means (0.5 Mbps, no semantic errors, no pollution), `err_only` means (5 Mbps, 0.30 error, no pollution), `poll_only` means (5 Mbps, no error, 0.15 pollution), and `full_press` means (0.5 Mbps, 0.30 error, 0.15 pollution).

## Columns by file

### `data/ablation1/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `condition` | category | Experiment-specific treatment; see the design table below. |
| `bw_mbps` | Mbps | Nominal shared-channel bandwidth; decimal megabits per second. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `payload_bytes` | bytes | Payload bytes counted as successfully enqueued/transmitted by the network model; includes queued work and is not a physical throughput measurement. |
| `bw_usage_mbps` | Mbps | Recorded bytes times 8 divided by episode_completion_time times 1,000,000. |
| `mean_latency_s` | seconds | Mean modelled delay for admitted transmissions; includes queueing/service/propagation and can exceed the episode horizon. Zero is recorded when no transmission contributes. |
| `packet_loss_rate` | fraction | Dropped transmissions divided by attempted transmissions; zero if there are no attempts. |
| `stale_cso_rate` | fraction | Stale-object count divided by the message/object counter; zero if the counter is zero. |
| `message_count` | integer | Simulator message/object counter; counts attempted messages, including messages that are subsequently dropped. |
| `payload_per_msg` | bytes/message | Recorded transmitted payload bytes divided by message_count; dropped attempts can reduce this value. |
| `task_success_rate` | fraction | Confirmed genuine tasks divided by the number of genuine targets. |
| `episode_completion_time` | seconds | Time of all-target completion, or the 300-second observation horizon if incomplete; the horizon value is not proof of completion. |
| `evidence_status` | category | Historical record origin: ACTUAL_POST_P0_REPLAY = actual post-fix rerun; P0-INVARIANT_LEGACY_CARRY_FORWARD = audited invariant carry-forward. These labels describe origin, not a later check result. |

### `data/ablation2/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `condition` | category | Experiment-specific treatment; see the design table below. |
| `error_rate` | fraction | Configured probability of injecting semantic/schema mismatch into generated objects. |
| `overlap_level` | design label | Configured UGV capability-overlap condition: 0, 0.25 or 0.5. These are experimental design labels, not a recalculated set-similarity statistic. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `capability_match_rate` | fraction | Capability matches divided by matches plus mismatches; recorded as 1 if no capability evaluations occur. |
| `action_conflict_rate` | fraction | Action-conflict counter divided by the message/object counter; zero if the denominator is zero. |
| `semantic_mismatch_count` | integer | Number of generated objects whose semantic/schema fields were corrupted. |
| `replan_count` | integer | Recorded replanning events. |
| `cso_validity_rate` | fraction | Stored alias of capability_match_rate in the frozen simulator. |
| `llm_accept_rate` | fraction | Accepted policy decisions divided by policy decisions; recorded as 1 if no such decisions occur. A value of 1 alone does not demonstrate a live model call. |
| `task_success_rate` | fraction | Confirmed genuine tasks divided by the number of genuine targets. |
| `episode_completion_time` | seconds | Time of all-target completion, or the 300-second observation horizon if incomplete; the horizon value is not proof of completion. |

### `data/ablation3/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `condition` | category | Experiment-specific treatment; see the design table below. |
| `pollution_rate` | fraction | Configured probability of the single attempted pollution injection per episode; use injection_occurred to identify actual injection. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `injection_occurred` | Boolean | Whether the episode actually received the designated pollution injection. |
| `false_acceptance_rate` | fraction | Corrupted objects accepted divided by corrupted objects accepted plus rejected/detected at admission; zero if no corrupted objects were evaluated. This excludes later audit detections. |
| `false_rejection_rate` | fraction | Valid objects rejected divided by valid objects evaluated at admission; zero if no valid objects were evaluated. |
| `detection_rate` | fraction | Admission-time corrupted-object detections divided by corrupted objects accepted plus admission detections; zero if there are no such objects. Later audit outcomes are separate. |
| `pollution_propagation` | fraction | Corrupted entries divided by all active memory entries at episode end; zero for empty memory. This is a snapshot, not cumulative exposure. |
| `pollution_exposure_s` | corrupted-entry seconds | Integral of active corrupted-entry count over the recorded post-injection observation period. |
| `pollution_exposure_frac` | dimensionless | pollution_exposure_s divided by observed post-injection time; zero when that time is zero. Despite its name, this measures average polluted-entry count. |
| `wasted_ugv_dispatch_s` | UGV-seconds | Accumulated busy time for UGV work derived from corrupted objects. |
| `post_admission_detected` | Boolean | Whether a subsequent audit flagged the designated injected object; separate from admission-time detection. |
| `audit_detect_time_s` | seconds | Delay from injection to the subsequent audit flag; zero if no audit detection is recorded. Consult post_admission_detected before interpreting zero. |
| `audit_false_positives` | integer | Legitimate objects incorrectly flagged by post-admission audits. |
| `evaded` | Boolean | Designated injected object was neither identified at admission nor by a later audit. False also occurs when no injection happened; condition on injection_occurred. |
| `downstream_linked` | integer | Assignments linked to the audit-flagged object. |
| `downstream_reverted` | integer | Linked assignments actually recalled/reverted. |
| `physical_recovery_time_s` | seconds | Recorded travel-back plus safe-disengagement recovery cost, separate from information-layer recovery. |
| `recall_travel_s` | seconds | Geometric travel-back cost charged for recalled UGVs. |
| `recall_disengage_s` | seconds | Safe-disengagement cost charged for recalling executing UGV work. |
| `recalls_from_en_route` | integer | Recalls initiated while a UGV was travelling. |
| `recalls_from_executing` | integer | Recalls initiated while a UGV was executing a task. |
| `total_recovery_time_s` | seconds | mean_recovery_time_s plus physical_recovery_time_s, as defined in the frozen output. |
| `rollback_success_rate` | fraction | Successful version restores divided by rollback attempts; recorded as 1 when there are no attempts. Do not interpret the no-attempt value as measured success. |
| `mean_recovery_time_s` | seconds | Mean recorded information-layer rollback/revert cost; zero when there are no such events. |
| `isolation_count` | integer | Recorded memory-isolation events. |
| `detection_time_s` | seconds | Delay from injection to first identification. The value 300 is a never-detected sentinel; zero also occurs when no injection happens. |
| `downstream_affected` | integer | Legacy downstream-impact counter, distinct from downstream_linked; zero throughout the released A3 table. |
| `task_success_rate` | fraction | Confirmed genuine tasks divided by the number of genuine targets. |
| `episode_completion_time` | seconds | Time of all-target completion, or the 300-second observation horizon if incomplete; the horizon value is not proof of completion. |

### `data/ablation4/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `pillar_config` | category | Enabled mechanism set: none, a1_only (lightweight), a1_a2 (lightweight plus interoperability), full_nncai (all three pillars). |
| `pressure_mode` | category | bw_only, err_only, poll_only or full_press; fixed conditions are listed below. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `injection_occurred` | Boolean | Whether the episode actually received the designated pollution injection. |
| `task_success_rate` | fraction | Confirmed genuine tasks divided by the number of genuine targets. |
| `episode_completion_time` | seconds | Time of all-target completion, or the 300-second observation horizon if incomplete; the horizon value is not proof of completion. |
| `bandwidth_usage_mbps` | Mbps | Recorded bytes times 8 divided by episode_completion_time times 1,000,000. |
| `action_conflict_rate` | fraction | Action-conflict counter divided by the message/object counter; zero if the denominator is zero. |
| `false_acceptance_rate` | fraction | Corrupted objects accepted divided by corrupted objects accepted plus rejected/detected at admission; zero if no corrupted objects were evaluated. This excludes later audit detections. |
| `pollution_propagation` | fraction | Corrupted entries divided by all active memory entries at episode end; zero for empty memory. This is a snapshot, not cumulative exposure. |
| `cascaded_failure` | integer indicator | Legacy cascade-failure output; zero throughout the released A4 table. It is not a separate empirical cascade-validation result. |
| `evaded` | Boolean | Designated injected object was neither identified at admission nor by a later audit. False also occurs when no injection happened; condition on injection_occurred. |
| `detection_rate` | fraction | Admission-time corrupted-object detections divided by corrupted objects accepted plus admission detections; zero if there are no such objects. Later audit outcomes are separate. |
| `post_admission_detected` | Boolean | Whether a subsequent audit flagged the designated injected object; separate from admission-time detection. |
| `evidence_status` | category | Historical record origin: ACTUAL_POST_P0_REPLAY = actual post-fix rerun; P0-INVARIANT_LEGACY_CARRY_FORWARD = audited invariant carry-forward. These labels describe origin, not a later check result. |

### `data/model_test/case_results.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `round_id` | category | Recorded model-evaluation round: R01, R02 or R03; cache scope is per provider and round. |
| `case_id` | SHA-256-format ID | Frozen case identifier joining to frozen_grid.json; not a provider request ID. |
| `provider` | category | Historical service label: deepseek, qwen or glm. |
| `overlap_level` | design label | Configured UGV capability-overlap condition: 0, 0.25 or 0.5. These are experimental design labels, not a recalculated set-similarity statistic. |
| `ugv_label` | category | Synthetic UGV capability profile A or B, within the overlap condition. |
| `event_type` | category | Synthetic task type: fire, casualty or hazmat. |
| `battery_pct` | percent | Synthetic agent battery state: 20, 60 or 95 percent. |
| `bandwidth_kbps` | kbps | Synthetic per-agent available bandwidth: 200 or 1000 decimal kilobits per second. |
| `rule_decision` | Boolean | Capability-only reference acceptance decision. |
| `policy_correct` | Boolean | Reference policy-feasible acceptance label, based on capability and resource constraints; this is the target decision, not a model-accuracy flag. |
| `requested_model` | text | Historical model identifier requested by the evaluation protocol. |
| `response_model` | text | Historical response model name recorded in the output; not a guarantee of fixed underlying service weights. |
| `prompt_sha256` | SHA-256 | Hash of the messages serialized as UTF-8 JSON with sorted keys and compact comma/colon separators; key into prompts.json. |
| `cache_status` | category | miss = provider outcome source; hit = reuse of an identical prompt result within that provider/round. Hits are not independent API requests. |
| `source_case_id` | SHA-256-format ID | Case supplying the prompt-cached outcome; joins to the same provider and round and to a case with the same prompt hash. |
| `valid` | Boolean | Response passed the frozen validation checks. |
| `llm_decision` | Boolean or empty | Recorded model acceptance when valid; empty for the three invalid/truncated case rows. |
| `matches_reference` | Boolean | True only when valid is True and llm_decision equals policy_correct; invalid rows are counted non-correct. |
| `fallback_used` | Boolean | Whether a rule fallback was used; False for every released model-test record. |
| `finish_reason` | category | Recorded provider finish marker: stop or length. |
| `request_status` | category | Recorded response status; received throughout this table. |
| `error_category` | text or empty | Validation exception class; ValueError for the three invalid rows and empty otherwise. |
| `validation_error` | text or empty | Validation diagnostic; finish_reason_not_stop for the three invalid rows and empty otherwise. |

### `data/resource_contention/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `arm` | category | Resource-contention treatment: no_trust, trust_score (admission), or trust_rollback (audit and rollback). |
| `audit_false_positives` | integer | Legitimate objects incorrectly flagged by post-admission audits. |
| `episode_end_active_memory_pollution` | fraction | Corrupted entries divided by all active memory entries at episode end; zero for empty memory. This is a snapshot, not cumulative exposure. |
| `fake_assigned_ever` | Boolean | Whether the false report ever occupied the UGV or was its first assignment. |
| `fake_ugv_busy_s` | seconds | Busy ticks attributed to the false report in the single-UGV contention fixture. |
| `false_acceptance_count` | integer | Corrupted reports admitted by the trust layer. |
| `false_rejection_count` | integer | Genuine reports rejected by the trust layer. |
| `first_assignment_id` | category or empty | T12_GEN = genuine report; T12_CORR = corrupted report; empty = no assignment observed. |
| `fixture_sha256` | SHA-256 | Frozen scenario-fixture hash, shared by the six cells of a seed. The hash includes the seed and therefore changes across seeds. |
| `genuine_confirmation_s` | seconds or empty | Time the genuine task was confirmed, strictly before the fixed 80-second deadline; empty if unconfirmed. |
| `genuine_confirmed_by_deadline` | Boolean | Whether genuine_confirmation_s was observed by the fixed deadline. |
| `genuine_dispatch_s` | seconds or empty | Time of first genuine-task dispatch / waiting since simultaneous arrival at time zero; empty if never dispatched by the deadline. |
| `genuine_wait_censored_at_s` | seconds or empty | 80 for a never-dispatched task; empty when dispatch was observed. This is censoring metadata, not an observed dispatch time. |
| `genuine_wait_s` | seconds or empty | Time of first genuine-task dispatch / waiting since simultaneous arrival at time zero; empty if never dispatched by the deadline. |
| `mean_information_recovery_s` | seconds | Mean recorded information-layer rollback/revert cost; zero when there are no such events. |
| `physical_recovery_s` | seconds | Recorded travel-back plus safe-disengagement recovery cost, separate from information-layer recovery. |
| `pollution_exposure_s` | corrupted-entry seconds | Integral of active corrupted-entry count over the recorded post-injection observation period. |
| `recall_disengage_s` | seconds | Safe-disengagement cost charged for recalling executing UGV work. |
| `recall_travel_s` | seconds | Geometric travel-back cost charged for recalled UGVs. |
| `rollback_events` | integer | Number of recorded version-rollback attempts. |
| `rollback_successes` | integer | Number of recorded successful version restores; cannot exceed rollback_events. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `status` | category | PROSPECTIVE_PAIRED_SEED_RESULT, identifying the historical paired-seed result records. |
| `tie_order` | category | Ordering of simultaneous reports: fake_first (stress) or genuine_first (control). |
| `wasted_ugv_dispatch_s` | UGV-seconds | Accumulated busy time for UGV work derived from corrupted objects. |
| `GEN_admission` | category | Admission outcome of the genuine report: accept, reject or network_lost. |
| `CORR_admission` | category | Admission outcome of the corrupted report: accept, reject or network_lost. |
| `both_accepted` | Boolean | True when GEN_admission and CORR_admission are both accept. |
| `wait_observed` | Boolean | True when the first genuine dispatch time is observed. |
| `restricted_wait_s` | seconds | Observed genuine dispatch/wait time, or 80 if never dispatched. Include all paired seeds when averaging. |
| `restricted_confirmation_s` | seconds | Observed genuine confirmation time, or 80 if not confirmed by the deadline. |
| `audit_flag_count` | integer | Number of distinct flagged-object IDs in the historical audit output. |
| `fixture_seed_independent_key` | SHA-256 | Historical alias of fixture_sha256. Despite the name, this value includes seed information and is not constant across seeds. |

### `data/scalability/episodes.csv`

| Field | Unit / encoding | Meaning |
|---|---|---|
| `condition` | category | Experiment-specific treatment; see the design table below. |
| `n_agents` | integer | Total UAV, UGV and fixed-sensor count. |
| `num_targets` | integer | Number of genuine targets in the scalability scenario. |
| `seed` | integer | Simulation RNG seed; 0-29 for A1/scalability/A2, 0-599 for A3/A4, 1-200 for resource contention. |
| `total_bytes` | bytes | Payload bytes counted as successfully enqueued/transmitted by the network model; includes queued work and is not a physical throughput measurement. |
| `total_MB` | MB | total_bytes divided by 1,000,000; decimal megabytes. |
| `bw_utilization` | dimensionless ratio | Recorded admitted-byte load per second divided by channel bytes/second. Queued work is included; values can exceed 1. This is not bounded physical link occupancy. |
| `mean_latency_s` | seconds | Mean modelled delay for admitted transmissions; includes queueing/service/propagation and can exceed the episode horizon. Zero is recorded when no transmission contributes. |
| `packet_loss_rate` | fraction | Dropped transmissions divided by attempted transmissions; zero if there are no attempts. |
| `task_success_rate` | fraction | Confirmed genuine tasks divided by the number of genuine targets. |
| `message_count` | integer | Simulator message/object counter; counts attempted messages, including messages that are subsequently dropped. |

## Frozen model-test protocol files

- `frozen_grid.json`: 108 case objects with case/state labels, reference decisions, messages and prompt hashes. The case grid is overlap x UGV profile x event x battery x bandwidth = 3 x 2 x 3 x 3 x 2.
- `prompts.json`: mapping from 90 distinct prompt hashes to message lists. Identical prompts across grid cases are intentionally cached within a provider/round.
- `formal_settings.json`: historical provider/model names, public endpoints, sampling/token settings, retry policy and runtime versions. These describe the historical study and do not guarantee future provider behavior. They contain no API credentials or executable program.

In JSON, reference Booleans are JSON `true`/`false`; CSV uses `True`/`False` strings. See [data notes](DATA_NOTES.md) for figure filters, historical provenance and observed missing-value counts.
