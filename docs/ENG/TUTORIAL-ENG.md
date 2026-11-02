# SFAAO 2.4.0 — Operational Tutorial

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official tutorial  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Objective


### Release 2.4.0 operational rule

A LORO comparison is valid only when calibration and inference use the same A(N−1) domain. The interface must expose domain, `alignment_status`, reference/fold count, $\tau_{EER}$, and $\tau_{operational}$. `REFERENCE_SENSITIVE` must be treated as `INCONCLUSIVE`; no majority voting is used and A(N) is not classified again.

This tutorial guides an authorized user through the main SFAAO 2.4.0 operational flow:

```text
functional installation
→ login
→ dataset
→ model
→ case
→ signatures
→ calibration
→ comparison
→ review
→ report
```

The tutorial does not redefine scientific concepts. For formal foundations and rules, see [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md), [DATASETS-ENG.md](DATASETS-ENG.md), [CALIBRATION-ENG.md](CALIBRATION-ENG.md), [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md), [SECURITY-ENG.md](SECURITY-ENG.md), and [LICENSING-ENG.md](LICENSING-ENG.md).

---

# 2. Installation summary

The Tutorial assumes the **official Windows or Linux package**, with bundled runtime and encapsulated/protected proprietary code.

```text
obtain official package
→ verify version and integrity
→ install/run the platform package
→ activate license when requested
→ start SFAAO
→ create first ADMIN or log in
```

Details belong to [INSTALLATION-ENG.md](INSTALLATION-ENG.md), the Manual, and README/Git.

---

# 3. Preconditions

Before starting:

- SFAAO installed according to [INSTALLATION-ENG.md](INSTALLATION-ENG.md);
- valid `SecurityBootstrap`;
- active license;
- authorized user;
- functional storage;
- legitimately obtained dataset when required;
- no real case used merely for testing without authorization.

---

# 4. User roles

The tutorial uses two roles:

```text
ADMIN
EXAMINER
```

`ADMIN` governs users, datasets, models, and training.

`EXAMINER` performs permitted forensic operations.

Canonical permissions belong to [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 5. License profiles

Before executing a protocol, check the profile:

```text
TRIAL
ACADEMIC
FULL
```

Critical point:

```text
ACADEMIC
→ allows SCIENTIFIC_EXTERNAL
→ blocks FORENSIC_CASE
```

Quotas and other features belong to [LICENSING-ENG.md](LICENSING-ENG.md).

---

# 6. First access

On a clean installation:

```text
0 users
→ FirstAdminWindow
→ create ADMIN
→ login
```

On an already configured installation:

```text
LoginWindow
→ username + password
```

There are no default credentials.

---

# 7. Main window

After authentication, `MainWindow` presents resources compatible with:

- user role;
- license;
- application state.

If an `EXAMINER` cannot see administrative menus, that is expected behavior.

---

# 8. Recommended flow for a new institution

After installing the product:

```text
1. create ADMIN
2. register users
3. obtain dataset legitimately
4. register/validate dataset
5. train or import model
6. activate model
7. register professional profile
8. create case
9. add signatures
10. calibrate
11. compare
12. review
13. generate report
```

---

# 9. Register users

As `ADMIN`:

```text
User Administration
→ create user
→ assign role
→ save
```

Use `EXAMINER` for forensic work when the user does not require governance privileges.

Deactivation is preferable to destructive deletion when related history exists.

---

# 10. Prepare a dataset

SFAAO does not provide third-party datasets.

Before registration:

1. obtain the corpus legitimately;
2. identify the exact corpus;
3. confirm usage rights;
4. store it in an authorized directory;
5. identify the corresponding profile.

Documented examples include GPDS, CEDAR, BHSig260, UTSig, and MCYT/MCYT-75.

---

# 11. Open Dataset Manager

As `ADMIN`:

```text
Manage Datasets
→ Add Dataset
```

Provide, according to the interface:

```text
name
profile
version/corpus
directory
```

---

# 12. Validate the dataset

After selecting the corpus:

```text
DatasetProvider
→ scan
→ classification
→ writer IDs
→ statistics
→ validation
```

Validation may scan many images and display 0–100% progress.

Do not manually register the dataset in SQLite to bypass failures.

---

# 13. Review dataset statistics

Before accepting registration, review:

- file count;
- writers/classes;
- genuine samples;
- forgeries;
- unclassified files;
- conflicts;
- selected corpus/profile.

A loadable structure does not guarantee that the selected profile is semantically correct.

---

# 14. Dataset available

After validation and persistence:

```text
dataset
→ local catalog
→ available for training
→ available for SCIENTIFIC_EXTERNAL when applicable
```

Technical availability does not change legal rights over the corpus.

---

# 15. Open Model Manager

As `ADMIN`:

```text
Manage Models
```

Two routes exist:

```text
train a new model
or
import a compatible package
```

A clean release contains no active homologation model.

---

# 16. Train a new model

Open the training window and select the dataset.

Choose the mode:

```text
PAIR
TRIPLET
CLASSIFICATION
```

Mode, loss, and architecture compatibility shall be respected by the platform.

---

# 17. Select backbone

Documented backbones:

```text
ResNet18
ResNet34
ResNet50
EfficientNet-B0
ConvNeXt-Tiny
MobileNetV3
```

Selecting another future backbone requires explicit release support.

---

# 18. Configure pretrained weights

Option:

```text
pretrained = true/false
```

If `true`, `torchvision` may attempt to download weights when the checkpoint is absent from cache.

In offline environments, plan weight availability in advance.

---

# 19. Configure backbone freezing

Option:

```text
freeze_backbone = true/false
```

It controls whether feature-extractor parameters remain frozen during the corresponding training stage.

The choice forms part of model technical identity and shall be persisted.

---

# 20. Configure embedding

Set:

```text
embedding_dim
dropout
```

The dimension shall be architecture-compatible and remain consistent when the model is reconstructed for inference/calibration.

---

# 21. Select loss

Documented losses:

```text
Contrastive
Cosine Contrastive
Triplet
ArcFace
```

Do not freely combine every loss with every mode.

Conceptual compatibility examples:

```text
PAIR           → pair losses
TRIPLET        → Triplet Loss
CLASSIFICATION → compatible classification/angular loss
```

---

# 22. Select metric

Documented comparison metrics:

```text
Cosine Similarity
Euclidean Distance
```

Critical operational rule:

```text
cosine    → higher is more similar
euclidean → lower is closer
```

Formal rules are in [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md)/[CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 23. Configure optimization

Set as appropriate:

```text
optimizer: Adam / AdamW
learning_rate
weight_decay
batch_size
epochs
```

These parameters shall be preserved in `TrainingConfig`.

---

# 24. Configure scheduler

Documented options:

```text
none
StepLR
cosine
plateau
```

Scheduler choice is part of reproducible training configuration.

---

# 25. Configure sampling

In PAIR mode, parameters may include:

```text
train_pairs
val_pairs
positive_ratio
forgery_negative_ratio
seed
```

Pair and negative semantics are canonical in [DATASETS-ENG.md](DATASETS-ENG.md).

---

# 26. Configure early stopping

Available parameters may include:

```text
min_epochs
patience
min_delta
```

Monitoring may use the strategy/metric defined by the pipeline.

The best model does not have to be the final epoch.

---

# 27. Start training

After reviewing configuration:

```text
Start
→ TrainingPipeline
→ training
→ validation
→ metrics
→ best_state_dict selection
→ final evaluation
→ model package
```

Do not abruptly close the application while artifacts are being persisted.

---

# 28. Monitor training

The monitoring window may display:

- epoch;
- train loss;
- validation loss;
- EER;
- threshold;
- learning rate;
- best state;
- progress.

Displayed values belong to training/validation and do not replace case calibration.

---

# 29. Review training artifacts

The package may contain:

```text
weights
model_config.json
training_config.json
evaluation_metrics.json
history
plots
metadata
```

The package is a scientific artifact and shall remain intact/traceable.

---

# 30. Register and activate model

After training or import:

```text
validate package
→ register
→ select model
→ activate
```

Only the appropriate operational model shall be used for new operations.

Activating a new model does not alter historical comparisons.

---

# 31. Create a case

As an authorized user:

```text
Cases
→ New Case
→ enter identification
→ save
```

The case is the central unit connecting signatures, calibrations, comparisons, and reports.

---

# 32. Examiner profile

Register/review the professional profile before generating a report.

These data may be updated in a controlled manner without automatically recomputing persisted scientific results.

---

# 33. Open case operations

For the selected case:

```text
Open
→ CaseOperationsWindow
```

The panel groups actions such as:

- report data;
- signatures;
- calibration;
- comparison;
- results;
- report generation.

---

# 34. Add RC

Upload reference signatures classified as:

```text
RC
```

Before proceeding:

- verify images;
- verify order/page;
- verify forensic role.

Do not silently change the role after producing results.

---

# 35. Add RO

Upload the:

```text
RO
```

set.

Review the same properties.

RC and RO are distinct contexts and may produce distinct calibrations and comparisons.

---

# 36. Add QUESTIONED

Upload questioned signatures:

```text
QUESTIONED
```

Review:

- image;
- page;
- identification;
- forensic role when the protocol requires human classification.

Questioned signatures shall not be improperly used as an independent scientific calibration population.

---

# 37. Review forensic roles

Before calibration:

```text
RC correct?
RO correct?
QUESTIONED correct?
human classifications reviewed?
```

Relevant changes shall remain auditable where supported.

---

# 38. Open calibration

In the case:

```text
Calibration
→ select model
→ select RC or RO
→ select protocol
```

Protocols:

```text
FORENSIC_CASE
SCIENTIFIC_EXTERNAL
```

Use only a protocol allowed by the license.

---

# 39. FORENSIC_CASE

If the license profile permits:

```text
case
→ references/forensic roles
→ protocol parameters
→ execute
→ review
```

The methodology is not repeated here; see [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

`ACADEMIC` blocks this protocol.

---

# 40. SCIENTIFIC_EXTERNAL

Operational flow:

```text
case
→ RC or RO
→ legitimately registered external dataset
→ external impostors
→ calibration
```

`QUESTIONED` remains reserved for the later inference/evaluation stage as defined in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 41. Review calibration

After execution, review:

- protocol;
- `calibration_uid`;
- model;
- RC/RO reference type;
- metric;
- EER;
- FAR/FRR when applicable;
- threshold;
- sample counts;
- plots;
- artifacts.

Do not select a calibration merely because the threshold “looks good.”

---

# 42. Select calibration

When multiple valid calibrations exist:

```text
review
→ explicitly select operational calibration
```

Calibration belongs to:

```text
case + model
```

Do not indiscriminately reuse a threshold from another model/case.

---

# 43. Signature Comparison — Release 2.4.0 Window

After selecting the case and **active context** (relevant model, calibration, references, and questioned signatures), open the **Signature Comparison** window. Release 2.4.0 offers **four actions**, rather than the earlier three-action interface:

1. **Individual Comparison (LORO/A4):** compare one questioned signature within the active context and the calibration's A(N−1) domain. With five references, A(N−1) is A4. The selected protocol and reference classification (RC or RO) must match the active context.
2. **Inspect Pairwise Score:** inspect elementary scores between references and the questioned signature. These scores support analysis and provenance; do not apply an A(N−1) threshold directly to a single A1 pair as a substitute for the LORO decision.
3. **Batch Comparison:** execute multiple comparisons within the applicable context, authorization, result-recording, and quota constraints. Verify the selected elements before running.
4. **Refresh Context:** update the window's operational context after relevant changes. Recheck model, calibration, references, and questioned samples before comparing.

**Precondition:** do not make a LORO decision if the calibration and inference domains are misaligned. `REFERENCE_SENSITIVE` remains `INCONCLUSIVE`; A(N) is descriptive and must not be classified a second time. Scores provide technical support, while the final forensic interpretation requires human judgment.

**Data access:** the Linux `/usr/bin/sfaao` launcher provides the `~/SFAAO_Data` shortcut, normally pointing to `~/.local/share/SFAAO/runtime/data/{datasets,images,models}`. The internal `/opt/sfaao/run-sfaao.sh` script used in isolated homologation does not create that shortcut automatically. Do not redirect the production shortcut to test data.

---

# 44. Comparison with cosine

When the metric is cosine:

```text
score >= threshold
→ compatible region under the configured binary rule
```

Higher score means higher similarity.

Final forensic interpretation is not determined by the number alone.

---

# 45. Comparison with Euclidean

When the metric is Euclidean:

```text
distance <= threshold
→ compatible region under the configured binary rule
```

Lower distance means greater proximity.

Do not apply the cosine rule to Euclidean distance.

---

# 46. Review individual scores

The execution preserves reference×questioned results.

Review when needed:

- reference;
- questioned signature;
- score/distance;
- threshold;
- pair verdict;
- execution.

This supports investigation of the aggregate rather than relying only on the mean.

---

# 47. Review questioned-level result

The consolidated panel may show:

```text
score_count
mean
std
min
max
threshold
technical_decision
```

Confirm that the result belongs to the correct execution and calibration.

---

# 48. Run RC and RO separately

When both sets belong to the case:

```text
run RC × QUESTIONED
review
run RO × QUESTIONED
review
```

Do not mix RC and RO scores as if they belonged to the same population without a protocol defining that operation.

---

# 49. Batch results

The batch-results window can consolidate multiple questioned signatures and show:

- set;
- aggregate score;
- dispersion;
- threshold;
- technical decision;
- execution.

Batch analysis is subject to the license-profile quota.

---

# 50. CSV export

When available:

```text
Results
→ Export CSV
→ select destination
→ confirm
```

If the user cancels before authoritative authorization, no quota is consumed.

CSV is an auxiliary artifact and does not replace the forensic report.

---

# 51. Report data

Before issue:

- review case identification;
- review request/demand;
- review examiner profile;
- review model;
- review calibrations;
- review comparisons;
- review human conclusion when applicable.

Descriptive data shall not trigger silent scientific recomputation.

---

# 52. Generate DOCX/PDF

After review:

```text
Generate Report
→ choose format
→ produce artifact
→ register version
```

A complementary format of the same logical report may reuse the same authorization as defined in [LICENSING-ENG.md](LICENSING-ENG.md).

---

# 53. Technical report content

The report may include:

- model;
- dataset;
- backbone;
- loss;
- metric;
- embedding;
- original metrics;
- RC/RO calibration;
- FAR/FRR/EER;
- operational threshold;
- UIDs;
- results;
- attachments.

The report derives from persisted results and shall not silently recompute scores/thresholds.

---

# 54. Mandatory human review

Before delivery/archive:

- read the full document;
- verify identification;
- verify pages/attachments;
- verify model/calibration;
- verify tables;
- verify synthesis/conclusion;
- verify version.

**SFAAO supports forensic work; it does not replace the conclusion of a qualified professional.**

---

# 55. Archiving

Preserve together according to institutional policy:

```text
case
signatures
model/package
calibrations
comparison runs
scores
artifacts
reports
hashes/manifests
audit records
```

Canonical provenance policy belongs to [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

---

# 56. Operational best practices

- always work in the correct case;
- preserve originals;
- review forensic roles;
- confirm active model;
- calibrate before comparison;
- do not reuse incompatible calibration;
- do not alter evidence after execution without creating a new activity;
- preserve UIDs;
- preserve artifacts;
- use consistent backup;
- review the report.

---

# 57. Errors to avoid

Do not:

- use a dataset without usage rights;
- treat a profile as the dataset itself;
- use questioned signatures in `SCIENTIFIC_EXTERNAL` calibration;
- confuse training threshold with operational threshold;
- apply `>=` to Euclidean;
- apply `<=` to cosine;
- delete history to free cumulative quota;
- edit a scientific artifact and update the hash “to match”;
- issue a report without review;
- treat computational output as an automatic handwriting-examination conclusion.

---

# 58. Quick case checklist — operational form

The boxes below are completed by the user **for each case**; they are not release-status items.

- [ ] correct user;
- [ ] correct case;
- [ ] correct examiner;
- [ ] correct/active model;
- [ ] RC reviewed;
- [ ] RO reviewed;
- [ ] QUESTIONED reviewed;
- [ ] roles reviewed;
- [ ] protocol allowed;
- [ ] calibration executed;
- [ ] calibration selected;
- [ ] threshold reviewed;
- [ ] RC comparison executed;
- [ ] RO comparison executed when applicable;
- [ ] results reviewed;
- [ ] report generated;
- [ ] report reviewed;
- [ ] attachments reviewed;
- [ ] artifacts preserved.

---

# 59. Where each definition lives

| Topic | Canonical document |
|---|---|
| architecture, factories, modes, metrics | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| datasets, profiles, PairRecord | [DATASETS-ENG.md](DATASETS-ENG.md) |
| thresholds, FAR/FRR/EER, protocols | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| provenance, UIDs, executions, hashes | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| authentication, RBAC, integrity, secure time | [SECURITY-ENG.md](SECURITY-ENG.md) |
| profiles, protocol entitlement, quotas | [LICENSING-ENG.md](LICENSING-ENG.md) |
| distributed-product installation and first startup | [INSTALLATION-ENG.md](INSTALLATION-ENG.md) |
| short terms | [GLOSSARY-ENG.md](GLOSSARY-ENG.md) |

---

# 60. Document stability

This tutorial describes the validated SFAAO 2.4.0 operational flow.

Future screens may reorganize visual navigation but shall preserve the logical sequence and canonical technical rules.

The tutorial shall be updated when a UI change alters the actual path of a task.

---

**2.4.0 report homologation:** DOCX and PDF report generation have been confirmed under Windows 11, Linux Mint, and Red Hat. PDF and DOCX screenshots represent different formats; the security bootstrap has no dedicated user interface.
