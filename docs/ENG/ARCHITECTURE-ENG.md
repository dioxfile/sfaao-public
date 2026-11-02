# SFAAO 2.4.0 — Official Platform Architecture

**Document:** `ARCHITECTURE-ENG.md`  
**Documentation version:** 2.4  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Normative for the SFAAO 2.4.0 release

---

## 1. Objective

This document defines the **official software architecture of SFAAO 2.4.0**, including its boundaries, layers, responsibilities, permitted dependencies, and evolution rules.

Within this document, *software architecture* means the fundamental organization of the system expressed through its elements, relationships, responsibilities, and design principles. The architectural description follows the conceptual guidance of **ISO/IEC/IEEE 42010:2022**, which establishes requirements for architecture descriptions of software and systems [1].

The objective is to preserve:

- clear separation of responsibilities;
- low coupling and high cohesion;
- maintainability and testability;
- scientific reproducibility;
- forensic traceability;
- compatibility among training, calibration, and inference;
- controlled software evolution;
- security and auditability suitable for institutional use.

The architecture described here shall be treated as **normative**. Structural changes should occur only when justified by a concrete technical blocker, critical correction, security requirement, or demonstrated architectural necessity.


**Scope note:** internal module names and architecture descriptions must be cross-checked against the canonical 2.4.0 source tree before being presented as an exact executable inventory; the documented scientific rules remain unchanged.
---

## 2. Architectural scope

SFAAO 2.4.0 is a modular computational platform designed to support forensic analysis of offline handwritten signatures. Its architecture integrates:

1. graphical user interface;
2. case management and orchestration;
3. structured persistence;
4. physical artifact storage;
5. scientific Artificial Intelligence core;
6. configurable training;
7. biometric inference;
8. biometric calibration of the decision threshold/operating point;
9. comparison and result consolidation;
10. forensic document generation;
11. security, integrity, and traceability mechanisms.

SFAAO **must not be interpreted as a single classifier or a single neural network**. The platform is designed to support multiple architectures, training modes, loss functions, metrics, datasets, and scientific protocols while preserving stable architectural contracts.

---

## 3. Architectural principles

### 3.1 Single responsibility

Each module, class, or service should have one clearly delimited primary responsibility. This reduces side effects and improves testing, maintenance, and auditing.

### 3.2 Separation of concerns

Interface, flow control, persistence, storage, training, calibration, inference, reporting, and security are distinct domains.

In particular:

- the GUI does not implement scientific mathematics;
- controllers do not duplicate scientific algorithms;
- training does not perform authentication logic;
- inference does not estimate calibration thresholds;
- calibration does not contain presentation logic;
- reports do not recompute scientific results that have already been persisted.

### 3.3 Low coupling and high cohesion

Modules should communicate through well-defined interfaces, configurations, domain objects, and persisted artifacts. Concrete implementations must remain encapsulated within their responsible modules.

### 3.4 Modularity and extensibility

New scientific components should, whenever possible, be incorporated through **Factories, Repositories, Pipelines, Providers, Builders, and Profiles**, without rewriting the rest of the platform.

The *Factory* pattern encapsulates object creation and reduces direct dependency on concrete classes, following the creational pattern principles described by Gamma et al. [2]. The *Repository* pattern separates domain logic from persistence and data-access details, consistent with Fowler [3].

### 3.5 Reproducibility

Every relevant scientific execution should be reconstructable from persisted configurations and artifacts. Seeds, parameters, model version, metrics, thresholds, training configuration, and associated metadata are not visual preferences: they form the technical identity of an execution.

### 3.6 Traceability

A technical decision should be linkable, when applicable, to:

`user → case → model → configuration → calibration → execution → result → report → SFAAO version`.

The detailed specification of this chain is canonically defined in [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

### 3.7 Software quality

The architecture promotes characteristics such as maintainability, reliability, security, compatibility, and efficiency, in alignment with the product quality model defined by **ISO/IEC 25010:2023** [4].

---

## 4. General layered architecture


### Consolidated SFAAO 2.4.0 architecture

Release 2.4.0 consolidates an architecture in which **training, calibration, inference, persistence, reporting, licensing, and integrity are separate contracts**, linked through explicit metadata. The central change is that the statistical unit used to calibrate a decision must be the same statistical unit used during inference.

```text
PyQt5 GUI
   ↓
Controllers / Orchestration
   ├── Training / Evaluation
   ├── Calibration pipelines
   │      └── AlignedScoreAggregator (LORO / A(N-1))
   ├── Comparison pipelines
   │      └── decision folds + conservative consolidation
   ├── Repositories / SQLite schema v6
   ├── Reports / provenance fingerprint schema v3
   └── SecurityBootstrap / Licensing / Integrity
          ↓
   persistent storage + scientific artifacts
```

Core statistical contracts:

- `PAIRWISE_SCORE` / **A1** is an elementary score and, under LORO, a provenance component;
- `LORO_MEAN` / **A(N−1)** is the aligned calibration/inference decision unit; with five references, each fold is **A4**;
- **A(N)** remains descriptive and does not receive the LORO threshold;
- calibration and comparison persist `decision_statistic`, aggregation sizes, `fold_strategy`, and reference/fold counts;
- `ALIGNED` identifies compatible calibration/inference domains; `MISMATCH` blocks new runs; `LEGACY_MISMATCH` identifies incompatible historical records.

For Cosine Similarity:

$$
\tau_{operational}=\max(\tau_{forensic},\tau_{EER})
$$

For Euclidean Distance:

$$
\tau_{operational}=\min(\tau_{forensic},\tau_{EER})
$$

LORO folds are not averaged again and no majority voting is used. Uniform compatible, incompatible, or inconclusive folds produce `STABLE_COMPATIBLE`, `STABLE_INCOMPATIBLE`, or `STABLE_INCONCLUSIVE`; any mixture produces `REFERENCE_SENSITIVE` and a consolidated `INCONCLUSIVE` result.

```text
┌────────────────────────────────────────────────────────────┐
│                  GRAPHICAL USER INTERFACE                 │
│                         PyQt5                              │
└────────────────────────────┬───────────────────────────────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────────┐
│                       CONTROLLERS                          │
│ cases • users • models • datasets • reports              │
└──────────────────────┬───────────────────┬─────────────────┘
                       │                   │
                       ▼                   ▼
┌──────────────────────────────┐   ┌─────────────────────────┐
│       SCIENTIFIC AI CORE     │   │ PERSISTENCE / STORAGE │
│ training • inference         │   │ SQLite • filesystem     │
│ calibration • evaluation     │   │ services • artifacts    │
└───────────────┬──────────────┘   └────────────┬────────────┘
                │                                │
                └──────────────┬─────────────────┘
                               ▼
                    ┌──────────────────────┐
                    │ REPORTS / AUDITING  │
                    │ DOCX • PDF • logs   │
                    └──────────────────────┘
```

### 4.1 Dependency rule

The preferred dependency direction is:

```text
GUI
 ↓
Controllers
 ↓
Repositories / Services / AI Pipelines
 ↓
Factories / Configurations / Domain
 ↓
Concrete implementations
 ↓
Persistence of results and artifacts
```

Lower layers **must not depend on the GUI**.

---

## 5. Release root structure

The main operational structure confirmed for the SFAAO 2.4.0 release is:

```text
SFAAO/
├── ai/
├── assets/
├── controllers/
├── database/
├── models/
├── reports/
├── scripts/
├── security/
├── storage/
├── storage_services/
├── ui/
├── app.py
└── requirements.txt
```

### 5.1 Root responsibilities

| Component | Architectural responsibility |
|---|---|
| `app.py` | application entry point and controlled initialization |
| `ai/` | algorithms, models, training, evaluation, calibration, and inference |
| `assets/` | static application resources |
| `controllers/` | coordination among GUI, domain, persistence, services, and AI |
| `database/` | schema, connection, repositories, and structured persistence |
| `models/` | area for models made available/managed by the release |
| `reports/` | document consolidation and generation |
| `scripts/` | auxiliary pipelines, maintenance, and controlled technical tasks |
| `security/` | authentication, authorization, licensing, and protection mechanisms |
| `storage/` | persistent artifacts associated with operation and cases |
| `storage_services/` | physical storage operations and related abstractions |
| `ui/` | windows, dialogs, widgets, and user interaction |
| `requirements.txt` | Python dependencies declared for the supported environment |

In the clean distribution package, `models/active/`, `models/default/`, and `storage/cases/` must remain empty until legitimate installation/use creates content. `storage/license.sfaao` is not part of the distribution package.

---

## 6. Graphical user interface (`ui/`)

The GUI is responsible for:

- user interaction;
- capture and basic validation of inputs;
- presentation of cases, models, configurations, and results;
- progress display;
- controller invocation;
- visualization of artifacts and operational messages.

The GUI **must not**:

- calculate FAR, FRR, EER, or thresholds;
- directly construct neural architectures;
- independently execute authentication rules;
- manipulate the database schema directly;
- duplicate calibration, inference, or training logic.

Qt widgets must be created and manipulated in the appropriate interface thread. Long-running processing may use controlled workers/threads, but widget ownership must not be transferred to secondary threads.

---

## 7. Controllers (`controllers/`)

Controllers form the application orchestration layer.

Typical flow:

```text
GUI
 ↓
Controller
 ↓
Repository / Service / AI Pipeline
 ↓
Persisted result
 ↓
Controller
 ↓
GUI
```

Controllers may validate operational preconditions and coordinate services, but **must not duplicate the scientific mathematics** of the `ai/` core.

---

## 8. Persistence (`database/`)


### Statistical persistence and schema v6

Schema v6 separates provenance, descriptive statistics, and fold-level decision data:

- `calibrations`: `decision_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`;
- `calibration_results`: `threshold_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`, `fold_count`;
- `comparison_runs`: calibration/inference domains, aggregation sizes, `fold_strategy`, `reference_count`, `fold_count`, `alignment_status`, `eer_threshold`, and `operational_threshold`;
- `questioned_results`: descriptive A(N), decision statistic, decision counts, and `stability_status`;
- `questioned_decision_folds`: excluded reference, A(N−1), thresholds, and technical decision for each fold.

Persistence of the consolidated result and its folds is **atomic**: a valid LORO run cannot exist with a partial fold set.

SFAAO uses SQLite for structured metadata and operational relationships.

Persistence may include, according to the release schema:

- users;
- cases;
- signatures and their metadata;
- registered models;
- calibrations;
- comparison executions;
- results;
- descriptive report data;
- auditing and operational history.

Large files, images, neural weights, plots, and documents should not be indiscriminately stored as database blobs when the architectural contract defines filesystem persistence.

The detailed definition of provenance and record relationships belongs to [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

---

## 9. Storage (`storage/` and `storage_services/`)

`storage/` contains physical artifacts generated or used by the application. `storage_services/` encapsulates read, write, organization, and protection operations for this storage.

Examples of artifacts include:

- case references;
- questioned signatures;
- comparison results;
- calibrations;
- plots;
- reports;
- technical artifacts linked to an execution.

The physical structure must preserve case isolation and must not allow user-provided filenames to define arbitrary paths outside authorized areas.

Specific protection, permission, integrity, and licensing rules are defined in [SECURITY-ENG.md](SECURITY-ENG.md).

---

## 10. Scientific core (`ai/`)

The `ai/` directory contains the scientific logic of the platform. It must remain independent of presentation and governance details.

Conceptually, the core includes responsibilities such as:

```text
ai/
├── backbones/
├── calibration/
├── config/
├── datasets/
├── distances/
├── inference/
├── losses/
├── models/
├── training/
└── scientific utilities
```

The exact physical structure may receive auxiliary files without altering the contracts below.

### 10.1 Scientific isolation

The `ai/` core:

- does not authenticate users;
- does not decide institutional permissions;
- does not know Qt widgets;
- does not choose arbitrary filesystem paths;
- receives parameters, configurations, and paths already authorized by the responsible layers.

---

## 11. Modular AI framework

Conceptual training flow:

```text
DATASET
  ↓
DATASET PROFILE / PROVIDER
  ↓
PAIR / TRIPLET / CLASSIFICATION DATA BUILDING
  ↓
PREPROCESSING
  ↓
BACKBONE
  ↓
EMBEDDING or REPRESENTATION
  ↓
LOSS COMPATIBLE WITH THE MODE
  ↓
OPTIMIZER / SCHEDULER
  ↓
TRAINING
  ↓
VALIDATION / EVALUATION
  ↓
METRICS AND ARTIFACTS
  ↓
MODEL PACKAGE
```

Detailed scientific definitions of datasets belong to [DATASETS-ENG.md](DATASETS-ENG.md); definitions of biometric metrics and calibration belong to [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

## 12. Configuration as a reproducible contract

SFAAO uses persistable configuration objects to separate **what must be executed** from **how each concrete implementation is constructed**.

### 12.1 `ModelConfig`

Represents the structural identity of the model, including parameters such as:

- `backbone_name`;
- `pretrained`;
- `freeze_backbone`;
- `embedding_dim`;
- `dropout`;
- `loss_name`;
- `distance_name`;
- other parameters required for compatible reconstruction of the architecture.

### 12.2 `TrainingConfig`

Represents the operational training configuration, including, when applicable:

- epochs;
- batch size;
- learning rate;
- weight decay;
- seed;
- number of pairs/triplets/samples;
- sampling ratios;
- optimizer;
- scheduler;
- early stopping;
- execution device;
- training-mode-specific parameters.

Configurations used in a scientific execution must be persisted with the model or linked to it unambiguously.

---

## 13. Factories

Factories decouple configurable selection from concrete implementation.

The framework includes, according to the release implementation:

- `BackboneFactory`;
- `ModelFactory`;
- `LossFactory`;
- `DistanceFactory`;
- `OptimizerFactory`;
- `SchedulerFactory`.

### Normative rule

The GUI must not directly instantiate a `ResNet50`, `EfficientNet`, loss function, optimizer, or concrete metric. It provides configuration; the core resolves the implementation through the corresponding factory.

This rule allows new implementations to be added with minimal impact on upper layers.

---

## 14. Neural architectures and backbones

A **backbone** is the feature extractor responsible for transforming a preprocessed image into an intermediate representation. Its selection affects representational capacity, computational cost, memory usage, and training/inference time.

SFAAO 2.4.0 supports:

- ResNet18;
- ResNet34;
- ResNet50;
- EfficientNet-B0;
- ConvNeXt-Tiny;
- MobileNetV3;
- future architectures compatible with the platform contract.

ResNet architectures use residual learning as proposed by He et al. [5]. EfficientNet uses compound scaling of depth, width, and resolution as proposed by Tan and Le [6]. MobileNetV3 was designed with emphasis on computational efficiency under resource constraints [7]. ConvNeXt modernizes convolutional architecture by incorporating design decisions inspired by the recent evolution of visual models [8].

A new backbone must be introduced by extending the corresponding module and registering it in the factory, without requiring architecture-specific logic in the GUI.

---

## 15. Embeddings and comparison architectures

An **embedding** is a learned vector representation of an input. In metric learning, the objective is to organize the representation space so that semantically related samples have a useful geometric relationship for comparison.

Siamese networks use weight-sharing branches to produce comparable representations; this principle was historically applied to signature verification by Bromley et al. [9] and to metric learning by Hadsell, Chopra, and LeCun [10].

In SFAAO:

- embedding dimensionality is configurable;
- the dimension is part of the technical identity of the model;
- training, loading, calibration, and inference must reconstruct compatible configuration;
- the architecture must not assume that every training mode is obligatorily implemented by one concrete `SiameseNet` class.

### 15.1 Training modes

The platform conceptually supports:

- `PAIR`;
- `TRIPLET`;
- `CLASSIFICATION`.

PAIR and TRIPLET naturally operate on comparable representations/embeddings. CLASSIFICATION is a distinct supervised strategy and may use a classification head or a compatible angular formulation.

The `mode × loss × architecture` selection must be validated by the scientific core before execution.

---

## 16. Losses, metrics, and compatibility rule

SFAAO supports loss functions such as:

- Contrastive Loss;
- Cosine Contrastive Loss;
- Triplet Loss;
- ArcFace, when used with a compatible mode/configuration.

Canonical mathematical definitions of these losses should remain in the appropriate scientific documentation and in [REFERENCES-ENG.md](REFERENCES-ENG.md), avoiding unnecessary duplication in this architectural document.

### 16.1 Distances and similarities

The framework supports at least:

- Cosine Similarity;
- Euclidean Distance.

The architecture must preserve the **semantics of the selected metric** across all layers:

```text
Cosine Similarity:
higher score = greater similarity
typical acceptance: score >= threshold

Euclidean Distance:
lower distance = greater proximity
typical acceptance: distance <= threshold
```

No GUI or reporting layer may invert this rule.

---

## 17. Training

The training subsystem is responsible for:

- building data according to the selected mode;
- consistent preprocessing;
- model creation through factories;
- loss calculation;
- optimization;
- scheduler execution, when configured;
- validation;
- best-state selection;
- early stopping;
- final evaluation;
- persistence of the model package and artifacts.

Training **does not define the operational threshold of a forensic case merely by automatically reusing a training threshold**. Metrics produced during training/validation characterize the model and experiment; biometric calibration is a separate stage.

---

## 18. Mandatory separation: training, calibration, and inference

The three stages are architecturally and semantically distinct:

```text
TRAINING
learns model parameters

BIOMETRIC CALIBRATION
estimates/selects a threshold or operating point
for a defined protocol

INFERENCE
applies model + metric + valid calibration
to samples intended for evaluation/comparison
```

The expression **“neural network calibration” must not be used as a generic description of SFAAO calibration**.

The canonical definition, `FORENSIC_CASE` and `SCIENTIFIC_EXTERNAL` protocols, FAR, FRR, EER, ROC, threshold, and the Independent External-Impostor Calibration methodology are covered in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

### 18.1 Case–model–calibration rule

An operational calibration must remain linked to the context that produced it. The architecture must not treat case thresholds as global constants that may be indiscriminately reused across different models, metrics, or scenarios.

---

## 19. Inference and comparison

The inference subsystem must:

1. load the model and its configuration;
2. reconstruct compatible preprocessing and architecture;
3. generate representations or outputs according to the applicable mode;
4. calculate the configured metric;
5. apply the correct metric semantics;
6. use a valid calibration/threshold for the context;
7. return a structured result for persistence and presentation.

Inference must not train the model and must not silently recalibrate the threshold.

---

## 20. ModelRepository and model package

`ModelRepository` encapsulates model storage, discovery, and reconstruction, preventing GUI and controllers from depending on internal weight details.

A **model package** must preserve, according to mode and configuration:

```text
model weights
model_config.json
training_config.json
evaluation_metrics.json
training history
plots/scientific artifacts
metadata sufficient for reconstruction
```

Physical names may vary according to the release implementation, but the contract is to preserve sufficient information to technically identify and reconstruct the model.

Homologation/training models are not included in the clean/public release package.

---

## 21. Reports (`reports/`)


### Report provenance in release 2.4.0

DOCX/PDF generation incorporates persisted scientific metadata and a canonical provenance fingerprint **schema v3**. When applicable, the canonical material includes decision domain, folds, thresholds, `alignment_status`, and `stability_status`. Reports materialize persisted results and do not silently recompute scores or decisions.

The reporting layer materializes information that has already been validated and persisted.

A report may consolidate:

- case identification;
- relevant references and questioned samples;
- model used;
- relevant configuration;
- applicable protocol/calibration;
- operational threshold;
- results and statistics;
- platform version;
- issue date;
- elements required for traceability.

**Reports must not independently recompute scores or thresholds outside the scientific pipeline.**

---

## 22. Architectural security

Security must cross architectural layers without contaminating the scientific core with governance logic.

Principles include:

- authentication and authorization before protected operations;
- path and file validation;
- separation between structured data and physical artifacts;
- no secrets or private keys in the release;
- no license bound to the developer machine;
- verifiable integrity of the distributed package;
- logs and auditing consistent with system policies.

The canonical specification is defined in [SECURITY-ENG.md](SECURITY-ENG.md).

---

## 23. Architecture evolution rules

1. Do not duplicate scientific logic.
2. Do not place business rules or mathematics in GUI code.
3. Do not place training inside the inference module.
4. Do not place inference inside calibration implementation.
5. Do not mix authentication/authorization into the scientific core.
6. New backbones must be added through the corresponding abstraction/factory.
7. New losses must preserve existing contracts.
8. New metrics must explicitly declare their decision semantics.
9. New datasets must use an appropriate profile/provider/adapter.
10. Persistence changes must preserve traceability and controlled migration.
11. Architectural changes should preserve backward compatibility whenever technically possible.
12. New code should use type hints and concise documentation for public APIs where applicable.
13. Excessively long methods should be decomposed by responsibility.
14. Excessively large files should be refactored when this improves cohesion and auditability, without artificial fragmentation.

---

## 24. Release architectural policy

The official package must not distribute:

- third-party datasets;
- images from third-party datasets;
- real cases;
- real signatures;
- trained homologation models;
- development database;
- historical snapshots and backups;
- Python caches;
- private keys;
- a license bound to the developer machine;
- developer-machine-specific temporal state;
- confidential internal intellectual-property documentation.

These restrictions are part of the architectural and security boundary of the distributable product.

---

## 25. Architectural stability

The SFAAO 2.4.0 base structure is considered frozen for final release documentation.

New features should **extend existing contracts**, rather than arbitrarily reorganizing directories, dependencies, or responsibilities.

A structural change is justified only when at least one of the following applies:

- security failure;
- demonstrated architectural inconsistency;
- technical impossibility of meeting a legitimate requirement;
- critical bug correction;
- required supported compatibility that cannot be achieved through modular extension.

---

## 26. Related documents

This document defines the architecture. Specialized concepts have their own canonical documents:

- [CALIBRATION-ENG.md](CALIBRATION-ENG.md) — biometric calibration, thresholds, FAR, FRR, EER, ROC, and protocols;
- [DATASETS-ENG.md](DATASETS-ENG.md) — datasets, profiles, adapters, data organization, and restrictions;
- [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) — provenance, traceability chain, and artifact relationships;
- [SECURITY-ENG.md](SECURITY-ENG.md) — authentication, authorization, integrity, protection, and secure distribution;
- [INSTALLATION-ENG.md](INSTALLATION-ENG.md) — environment and installation;
- [TUTORIAL-ENG.md](TUTORIAL-ENG.md) — guided platform operation;
- [GLOSSARY-ENG.md](GLOSSARY-ENG.md) — canonical terminology;
- [REFERENCES-ENG.md](REFERENCES-ENG.md) — consolidated scientific and normative bibliography.

When a concept is canonically defined in another document, this file should record only its architectural role and refer to the appropriate source.

---

## 27. References used in this document

[1] ISO/IEC/IEEE 42010:2022. *Software, systems and enterprise — Architecture description*. International Organization for Standardization, 2022.

[2] E. Gamma, R. Helm, R. Johnson, and J. Vlissides. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994.

[3] M. Fowler. *Patterns of Enterprise Application Architecture*. Addison-Wesley, 2002.

[4] ISO/IEC 25010:2023. *Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE) — Product quality model*. International Organization for Standardization, 2023.

[5] K. He, X. Zhang, S. Ren, and J. Sun. “Deep Residual Learning for Image Recognition.” *Proceedings of CVPR*, 2016, pp. 770–778. DOI: 10.1109/CVPR.2016.90.

[6] M. Tan and Q. V. Le. “EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks.” *Proceedings of ICML*, 2019.

[7] A. Howard et al. “Searching for MobileNetV3.” *Proceedings of ICCV*, 2019.

[8] Z. Liu et al. “A ConvNet for the 2020s.” *Proceedings of CVPR*, 2022.

[9] J. Bromley, I. Guyon, Y. LeCun, E. Säckinger, and R. Shah. “Signature Verification Using a Siamese Time Delay Neural Network.” *Advances in Neural Information Processing Systems*, 1994, pp. 737–744.

[10] R. Hadsell, S. Chopra, and Y. LeCun. “Dimensionality Reduction by Learning an Invariant Mapping.” *Proceedings of CVPR*, 2006, pp. 1735–1742. DOI: 10.1109/CVPR.2006.100.

---

## 28. Final normative statement

This document supersedes previous architectural descriptions that conflict with the structure and contracts of the SFAAO 2.4.0 release defined here.

In the event of a conflict between historical documentation and the frozen release, precedence is given to:

1. the validated operational structure of the release;
2. the normative contracts defined in this document;
3. the specialized canonical documents referenced in Section 26.

