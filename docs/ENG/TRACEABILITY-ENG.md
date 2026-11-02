# SFAAO 2.4.0 — Traceability, Provenance, and Auditability

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official technical specification  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Objective

This document defines the canonical **traceability**, **provenance**, and **auditability** model of SFAAO 2.4.0.

Its purpose is to ensure that a technical result can be reconstructably related to the data, agents, configurations, models, calibrations, executions, and artifacts that produced it.

Architecture is defined in [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md); datasets in [DATASETS-ENG.md](DATASETS-ENG.md); biometric calibration in [CALIBRATION-ENG.md](CALIBRATION-ENG.md); cryptographic mechanisms, secure time, permissions, and hardening in [SECURITY-ENG.md](SECURITY-ENG.md).


**Verification scope:** schema names, UIDs, and fingerprints described here are documentary contracts; conformance of any particular database or migration must be verified against the audited version and records.
---

# 2. Scope

This document covers:

- provenance;
- traceability;
- audit trail;
- stable entity identification;
- user–case–evidence relationships;
- dataset provenance;
- model provenance;
- calibration provenance;
- comparison executions;
- individual scores and aggregated results;
- report versions;
- timestamps;
- referential integrity;
- hashes and manifests;
- failures and states;
- execution reconstruction;
- computational chain of custody.

---

# 3. Canonical definition of provenance

W3C PROV-DM defines provenance as information about **entities, activities, and agents** involved in producing or delivering a piece of data or thing and the relationships among those elements [1].

In SFAAO, the abstraction is:

```text
ENTITIES
case, signature, dataset, model, calibration, score, report, artifact

ACTIVITIES
upload, validation, training, calibration, comparison, report generation

AGENTS
user, examiner, administrator, software/version
```

Provenance should answer:

```text
what?
who?
when?
with which data?
with which model?
with which calibration?
with which parameters?
which execution?
which result?
which artifact was produced?
```

---

# 4. Canonical definition of traceability

**Traceability** is the ability to traverse provenance relationships from a result back to its source elements and, conversely, to determine which results depend on a given element.

In SFAAO it is bidirectional:

```text
source → execution → result
result → execution → source
```

Example:

```text
questioned result
  → comparison_run
  → calibration
  → model
  → RC/RO signatures
  → case
  → user/examiner
```

---

# 5. Traceability is not auditing

**Traceability** explains dependencies and origin.

**Auditing** records or supports review of relevant events, including human actions and state changes.

Example:

- `comparison_runs` links scientific context and execution;
- `signature_role_audit` records forensic-role changes;
- security logs may record authentication or integrity events.

A mechanism may contribute to both, but the concepts shall not be conflated.

---

# 6. Traceability is not integrity

**Integrity** concerns detection or prevention of improper modification.

A hash may show that two contents differ, but it does not by itself explain who produced a file, through which process, or to which execution it belongs.

Therefore:

```text
hash ≠ complete provenance
provenance ≠ cryptographic integrity guarantee
```

Integrity mechanisms are detailed in [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 7. Traceability is not chain of custody

**Chain of custody** is the record and control of possession, handling, transfer, and preservation of evidence over time.

ISO/IEC 27037 provides guidance for identification, collection, acquisition, and preservation of digital evidence [2]. NIST SP 800-86 recommends detailed documentation of actions, responsible persons, tools, and evidence integrity [3].

SFAAO provides computational traceability that can **support** an institutional chain of custody, but it does not replace legal, administrative, or forensic procedures required by the competent organization.

---

# 8. Canonical SFAAO chain

The main logical chain is:

```text
USER / EXAMINER
      ↓
CASE
      ↓
SIGNATURES
RC / RO / QUESTIONED
      ↓
MODEL
      ↓
CALIBRATION
      ↓
COMPARISON RUN
      ↓
INDIVIDUAL SCORES
      ↓
QUESTIONED RESULT
      ↓
REPORT
```

Datasets, configurations, versions, and artifacts are linked to the corresponding points in this chain.

---

# 9. Stable identifiers

Important entities should use stable identifiers whenever the schema provides them.

Confirmed examples include:

- `case_uid`;
- `signature_uid`;
- `calibration_uid`;
- model IDs;
- `comparison_run` ID/identifier;
- report IDs;
- dataset IDs.

Local integer IDs may be used as relational keys but should not be treated as the sole external identity when a stable UID exists.

---

# 10. Independent version axes

SFAAO has distinct version dimensions:

```text
platform version
SQLite schema version
scientific protocol version
model version/configuration
dataset corpus/version
document/report version
```

These values are not interchangeable.

For example, updating the schema does not automatically change the calibration protocol.

---

# 11. Central relational model


### Schema v6 extensions for LORO

| Structure | Core 2.4.0 fields |
|---|---|
| `calibrations` | `decision_statistic`, `aggregation_size`, `fold_strategy`, `reference_count` |
| `calibration_results` | `threshold_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`, `fold_count` |
| `comparison_runs` | calibration/inference domains, aggregation sizes, `fold_strategy`, `reference_count`, `fold_count`, `alignment_status`, `eer_threshold`, `operational_threshold` |
| `questioned_results` | descriptive A(N), decision statistic, class counts, and `stability_status` |
| `questioned_decision_folds` | fold, excluded reference, A(N−1), thresholds, and technical decision |

`similarity_results` preserves reference×questioned A1 values as provenance. Under LORO these scores do not independently receive a verdict derived from an A(N−1) threshold.

`save_questioned_result_with_folds` is transactional: the consolidated result and folds are persisted in the same write context; failures trigger rollback.

The 2.x schema uses SQLite for metadata, traceability, auditing, and relationships, while images, weights, and scientific artifacts remain in the filesystem and are referenced by the database.

Central tables include:

| Table | Traceability role |
|---|---|
| `schema_versions` | schema version/migrations |
| `users` | application agent |
| `expert_profiles` | associated professional identity |
| `cases` | central forensic unit |
| `case_metadata` | descriptive metadata and human conclusion |
| `signatures` | RC, RO, and QUESTIONED |
| `signature_role_audit` | role/classification history |
| `datasets` | dataset catalog |
| `trained_models` | model catalog |
| `calibrations` | case+model execution/configuration |
| `calibration_samples` | calibration samples |
| `calibration_results` | metrics and artifacts |
| `comparison_runs` | identifiable execution |
| `similarity_results` | individual scores |
| `questioned_results` | questioned-level aggregation |
| `reports` | report versions |

---

# 12. Main relationships

```text
USERS
├── EXPERT_PROFILES
└── CASES
    ├── CASE_METADATA
    ├── SIGNATURES
    │   └── SIGNATURE_ROLE_AUDIT
    ├── CALIBRATIONS ───── TRAINED_MODELS
    │   ├── CALIBRATION_SAMPLES
    │   └── CALIBRATION_RESULTS
    ├── COMPARISON_RUNS ── TRAINED_MODELS
    │   ├── SIMILARITY_RESULTS
    │   └── QUESTIONED_RESULTS
    └── REPORTS
```

This structure expresses dependencies; it is not merely a storage layout.

---

# 13. User and examiner provenance

When an activity can be attributed to a human agent, the system shall preserve the available identifier of that agent.

Examples:

- case creator;
- calibration executor;
- comparison executor;
- report issuer;
- author of a forensic-role change.

Traceability does not require personal data to be copied into every record; relational IDs are preferred.

---

# 14. Case provenance

The case is the main aggregation unit.

Relevant elements include, according to the schema:

```text
case_uid
associated user/examiner
name/description
status
storage directory
timestamps
```

`case_uid` provides stable identity independent of the local integer ID.

Scientific artifacts shall not lose their relationship with the originating case.

---

# 15. Signature provenance

The `signatures` table distinguishes at least:

```text
RC
RO
QUESTIONED
```

Each signature should be related to:

- case;
- UID/ID;
- physical file;
- original filename when preserved;
- page/order when applicable;
- forensic role;
- relevant timestamps.

A file path alone is not a sufficient scientific identity.

---

# 16. Forensic-role auditing

Changes to human classification of questioned signatures are relevant events and should be preserved through `signature_role_audit` when applicable.

The purpose is to prevent a human interpretation change from silently erasing the previous state.

Definitions of forensic classes used by scientific protocols belong to [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 17. Dataset provenance

Dataset traceability shall preserve the elements canonically defined in [DATASETS-ENG.md](DATASETS-ENG.md), including, when applicable:

```text
dataset_name
corpus/version
profile
source
writer population
split
seed
sample/pair provenance
```

A profile does not replace identification of the physical corpus.

---

# 18. Model provenance

A model shall be reconstructable from weights and sufficient metadata.

Minimum provenance includes, when applicable:

```text
model_id
model package
ModelConfig
TrainingConfig
training mode
backbone
loss
distance
embedding_dim
seed
training dataset/protocol
evaluation metrics
platform version
```

The model package and its `ModelRepository` registration form part of the chain.

---

# 19. Calibration provenance

A calibration is linked to **case + model** and shall not be interpreted as a context-free global threshold.

Documented fields include:

```text
calibration_uid
case_id
model_id
created_by
calibration_mode
reference_type
distance_name
status
artifact_dir
configuration_path
parameters_json
is_selected
timestamps
```

The central modes are documented in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 20. Calibration-result provenance

`calibration_results` may relate:

```text
eer
eer_threshold
forensic_threshold / calibrated threshold
target_far
far
frr
genuine_count
impostor_count
genuine_mean / genuine_std
impostor_mean / impostor_std
bootstrap confidence interval
metric/plot paths
```


### Semantics of `target_far` in SFAAO 2.4.0

In SFAAO 2.4.0, `target_far` records the operational FAR target used by calibration. In the official calibration interface, this value is fixed by `CalibrationWindow.TARGET_FAR = 0.01`, corresponding to **1%**, and is not user-configurable in the GUI.

Calibration searches among the evaluated thresholds for a threshold empirically satisfying:

$$
\widehat{\mathrm{FAR}}(\tau)\leq 0.01.
$$

This criterion is distinct from EER. The `eer_threshold` is estimated independently at the point where FAR and FRR are closest. Therefore, `target_far = 0.01` **does not force `EER <= 1%`**.

Under the current operating policy, the threshold associated with the FAR target and the current-calibration EER threshold are conservatively combined according to metric orientation: for similarity, the larger threshold is used; for distance, the smaller threshold is used.

The legacy internal field `threshold_forensic`, when present in compatible artifacts, may represent the threshold associated with the FAR-target/calibration criterion. Its historical name must not be confused with a redefinition of the `FORENSIC_CASE` protocol.

The requirement that FAR be less than or equal to 1% refers to the **empirical FAR observed in the calibration population**. It is not a mathematical guarantee that the future population FAR will remain less than or equal to 1%.

Legacy internal names may be preserved for historical compatibility; documentation shall explain their semantics without destructive schema renaming.

---

# 21. Comparison-run provenance

Each `comparison_run` explicitly links, when applicable:

```text
case_id
model_id
calibration_id
created_by
reference_type
distance_name
threshold
status
parameters/artifacts
timestamps
```

This prevents a score from being interpreted without knowing the model, metric, and calibration used.

---

# 22. Individual scores

`similarity_results` preserves reference×questioned granularity.

A detailed entry may contain:

```text
comparison_run_id
reference_signature_id
questioned_signature_id
score
distance_value
threshold_used
pair_verdict
created_at
```

UIDs, paths, names, and pages of related signatures may also be retrieved.

Individual traceability is required even when the UI displays only aggregates.

---

# 23. Questioned-level aggregate

`questioned_results` links one questioned signature to an execution and may store:

```text
score_count
mean_score
std_score
min_score
max_score
threshold
technical_decision
timestamps
```

The aggregate shall remain linked to the individual scores from which it was derived.

---

# 24. Decision rule and metric

Provenance shall record the metric and decision orientation.

Examples:

```text
cosine similarity:
accept if score >= threshold

euclidean distance:
accept if distance <= threshold
```

Without `distance_name`, `threshold`, and the corresponding rule, an isolated numeric value may be misinterpreted.

Mathematical metric definitions belong to the corresponding scientific/architecture documentation.

---

# 25. Report provenance


### Report provenance fingerprint — schema v3

The 2.4.0 report uses a canonical provenance fingerprint with `schema_version = 3`. When applicable, canonical material includes decision domain, folds, thresholds, `alignment_status`, `stability_status`, and identifiers needed to audit the decision. This fingerprint identifies the scientific/operational content underlying the report and does not replace the hash of the final file.

A report is a **derived artifact** from previously persisted data and results.

Normative rule:

> The report pipeline shall not silently recompute scores, thresholds, or decisions using parameters different from those associated with recorded executions.

A report shall preserve or allow resolution of, as applicable:

```text
case
model
calibration
comparison run
threshold
platform version
issue date
report version/status
```

---

# 26. Report versions

A new issue shall not make a previous version indistinguishable.

When versioning is supported by the schema/pipeline, it should be possible to determine:

- which version was issued;
- when;
- by whom;
- from which results it was derived;
- whether generation failed.

Supplemental or regenerated reports shall refer to the same scientific execution when no new scientific run occurred.

---

# 27. States and failures

Executions shall have explicit state when supported.

Examples:

```text
PENDING / RUNNING
COMPLETED
FAILED
```

A failure shall not be silently converted into success or erased merely because a later attempt completed.

The error record should distinguish:

```text
not executed
executed and failed
executed successfully
```

---

# 28. Timestamps

Timestamps are part of temporal provenance.

They should contextualize:

- creation;
- update;
- start;
- completion;
- failure;
- issue;
- role change;
- calibration selection.

Cryptographic/operational trust in the clock and **secure time** belong to [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 29. Referential integrity

The runtime uses foreign keys and protections to prevent destruction of historical dependencies.

Rule:

```text
record used by historical evidence
→ shall not be freely removed
```

Examples:

- calibration already used;
- signature already compared;
- case with scientific execution;
- model referenced by a comparison.

Physical deletion shall respect dependencies and retention policies.

---

# 30. Filesystem artifacts

Images, weights, plots, JSON files, and documents may remain in the filesystem.

The database shall contain sufficient references to locate and contextualize these artifacts.

A relevant artifact should, when applicable, be related to:

```text
case
execution
type
authorized path
version
hash/manifest
timestamps
```

Physical file existence alone is not provenance.

---

# 31. Hashes

A cryptographic hash is a function that maps arbitrary-length content to a fixed-size digest.

Conceptually:

$$
h = H(m)
$$

where `m` is the content and `h` is its digest.

In SFAAO, hashes may be used to:

- detect file changes;
- record package integrity;
- verify manifests;
- compare copies.

A hash alone does not prove authorship, origin, or legitimacy.

Allowed algorithms and integrity policies are defined in [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 32. Manifests

An integrity **manifest** associates a set of paths/identifiers with hashes and metadata.

Conceptual example:

```text
artifact_1 → SHA-256(...)
artifact_2 → SHA-256(...)
artifact_3 → SHA-256(...)
```

The manifest shall be versioned or protected consistently with its purpose; otherwise, changing both a file and its manifest would reduce evidentiary value.

Concrete implementation belongs to the security policy.

---

# 33. Reproducibility

Traceability is a prerequisite for reproducibility but is not equivalent to it.

A reproducible execution also requires enough technical parameters for reconstruction:

```text
software/version
dataset/profile
model/configs
seed
mode
parameters
metric
threshold/calibration
artifacts
```

Hardware, library, and kernel differences may prevent bitwise determinism even when provenance is complete.

---

# 34. Backup, export, and migration

Backup or migration shall not break relationships between the database and filesystem.

For an open SQLite database, a consistent mechanism such as the SQLite Backup API should be used when applicable.

After restore/migration, verify:

- foreign keys;
- paths;
- UIDs;
- artifacts;
- manifests/hashes when used;
- schema version;
- software compatibility.

---

# 35. Privacy and minimization

Traceability does not justify indiscriminate duplication of personal data.

The chain should prefer:

- internal IDs;
- UIDs;
- relationships;
- necessary metadata;

instead of copying names, emails, or sensitive information into every artifact.

Detailed protection rules belong to [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 36. Minimum reconstruction of a decision

To explain a technical decision, at least the following should be recoverable:

```text
1. case
2. questioned signature
3. RC or RO set used
4. model and configuration
5. selected calibration
6. metric
7. threshold
8. comparison_run
9. individual scores
10. aggregation
11. technical decision
12. user/agent and timestamps
13. platform version
```

If an element does not apply to a protocol, its absence should be explainable.

---

# 37. Recommended audit flow

```text
select result
  ↓
identify questioned_result
  ↓
open comparison_run
  ↓
list similarity_results
  ↓
resolve questioned and reference signatures
  ↓
resolve calibration_id
  ↓
resolve model_id
  ↓
review parameters/artifacts
  ↓
resolve case and agent
  ↓
verify derived report
```

This flow should be possible without recomputing the original execution.

---

# 38. Rule against silent recomputation

No screen, report, or export shall retrospectively replace a persisted execution with a new computation without creating or identifying the new activity.

If recomputation occurs:

```text
new execution
→ new identifier
→ new timestamps
→ new parameters/artifacts
```

The previous execution shall remain distinguishable according to retention policy.

---

# 39. Relationship with ARCHITECTURE.md

[ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) defines where responsibilities reside.

[TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) defines how entities across those layers relate throughout the scientific lifecycle.

If a structural conflict occurs, the normative architecture, this document, and the changelog shall be updated consistently.

---

# 40. Relationship with DATASETS.md

[DATASETS-ENG.md](DATASETS-ENG.md) defines minimum dataset, profile, split, writer-ID, sampling, and PairRecord provenance.

This document connects those elements to models, calibrations, and later results.

---

# 41. Relationship with CALIBRATION.md

[CALIBRATION-ENG.md](CALIBRATION-ENG.md) is canonical for protocols, thresholds, FAR/FRR/EER, and Independent External-Impostor Calibration.

This document only defines how each execution and its artifacts remain linked to the case, model, samples, and results.

---

# 42. Relationship with SECURITY.md

[SECURITY-ENG.md](SECURITY-ENG.md) is canonical for:

- authentication;
- authorization;
- secure time;
- hashing policy;
- manifest protection;
- filesystem permissions;
- encryption when applicable;
- backup protection;
- hardening.

This document does not duplicate those definitions.

---

# 43. Summary of normative rules

1. Every scientific result shall have an identifiable origin.
2. `case_uid`, `signature_uid`, and other UIDs shall be preserved when available.
3. Dataset, model, calibration, and comparison run are distinct contexts.
4. An individual score shall not conceptually exist without its execution.
5. An aggregate result shall remain linked to its source scores.
6. A report derives from persisted results; it does not silently recompute them.
7. Human forensic-role changes shall be auditable when supported.
8. Failure states shall remain distinguishable.
9. Foreign keys and retention policies shall protect historical dependencies.
10. A hash is integrity evidence, not complete provenance.
11. Timestamps need context; secure time is defined in [SECURITY-ENG.md](SECURITY-ENG.md).
12. Recomputation requires a new identifiable activity.
13. Software, schema, protocol, dataset, and model versions shall not be conflated.
14. Traceability shall respect personal-data minimization.

---

# 44. References

[1] W3C, *PROV-DM: The PROV Data Model*, W3C Recommendation, 30 Apr. 2013. https://www.w3.org/TR/2013/REC-prov-dm-20130430/

[2] ISO/IEC 27037:2012, *Information technology — Security techniques — Guidelines for identification, collection, acquisition and preservation of digital evidence*. International Organization for Standardization, 2012.

[3] K. Kent, S. Chevalier, T. Grance, and H. Dang, *Guide to Integrating Forensic Techniques into Incident Response*, NIST Special Publication 800-86, 2006. doi: 10.6028/NIST.SP.800-86.

[4] D. A. M. José, L. de Medeiros Marques, A. Bueno, and R. B. Z. da Silva, “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification,” article accepted for publication in *IEEE Access*, 2026 (final bibliographic metadata pending).

---

# 45. Document stability

This is the canonical traceability and provenance definition for SFAAO 2.4.0.

Future schema, storage, or pipeline changes shall preserve the ability to explain how every result was produced.

A refactoring that preserves numeric values but destroys the links among data, agents, configurations, and executions shall be considered a traceability regression.
