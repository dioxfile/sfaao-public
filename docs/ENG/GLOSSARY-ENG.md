# SFAAO 2.4.0 — Technical Glossary

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Purpose

This glossary provides **short quick-reference definitions**. It does not replace the formal definitions, equations, scientific foundations, operational rules, and limitations established in SFAAO canonical documents.

Whenever a term requires methodological, scientific, normative, or operational interpretation, the document listed under **Canonical document** prevails.

The editorial policy deliberately avoids repetition: concepts are not fully redefined in this file.

---

# 2. Terms


| Term | Short definition | Canonical document |
|---|---|---|
| **A1 / `PAIRWISE_SCORE`** | Score for a pair of signatures; a provenance component under LORO. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **A(N−1) / `LORO_MEAN`** | Fold mean with one reference excluded; decision domain aligned between calibration and inference. Five references produce A4. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **A(N)** | Descriptive mean across all references; do not apply the LORO threshold again to classify it. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **LORO** | Leave-One-Reference-Out; excludes one reference per fold to preserve the A(N−1) comparison domain. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`τ_EER`** | Threshold associated with the Equal Error Rate (EER) estimate. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`τ_forensic`** | Threshold associated with the operational FAR target where configured. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`τ_operational`** | Conservative operational threshold for the three-zone rule. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`ALIGNED` / `MISMATCH` / `LEGACY_MISMATCH`** | Calibration/inference decision-domain compatibility states. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`STABLE_COMPATIBLE` / `STABLE_INCOMPATIBLE` / `STABLE_INCONCLUSIVE`** | Stable decision state shared by all folds. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **`REFERENCE_SENSITIVE`** | Reference-exclusion-sensitive outcome, consolidated as inconclusive. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Individual Comparison (LORO/A4)** | Individual comparison under the active context and matching A(N−1) domain. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **Inspect Pairwise Score** | Inspection of elementary reference-to-questioned scores. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **Batch Comparison** | Execution of multiple comparisons within the active context. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **Refresh Context** | Comparison-window action to update the selected operational context. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **ACADEMIC** | Operational license profile intended for academic use, subject to the features, protocols, and quotas defined in [LICENSING-ENG.md](LICENSING-ENG.md). | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **ADMIN** | Administrative SFAAO role with access to management functions authorized by the security policy and active license. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Adam** | Adaptive optimizer supported for training. Its definition and use belong to the training architecture. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **AdamW** | Adaptive optimizer with decoupled weight decay, supported by SFAAO. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **ArcFace** | Angular-margin loss/strategy used in compatible classification and embedding configurations. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Artifact** | File or set of files produced or consumed by an execution and linked to its provenance. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Audit trail** | Record of relevant operational and administrative events. It is not synonymous with scientific provenance. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Backbone** | Feature-extraction network used as the base of a model architecture. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Batch analysis** | Batch execution of comparisons/results under the application's rules, quotas, and traceability requirements. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **Batch size** | Number of samples processed per batch during training or evaluation, according to configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **BHSig260** | Offline handwritten-signature dataset with Bengali and Hindi subsets, used only after legitimate acquisition and through an appropriate profile. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Calibration** | In SFAAO, biometric calibration of the decision threshold/operating point; it does not mean neural-network training. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Calibration UID** | Stable identifier of a persisted and traceable calibration. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Case** | Operational unit grouping data, signatures, executions, results, and reports related to an analysis. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **case_uid** | Stable identifier of a case. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **CEDAR** | Offline handwritten-signature dataset containing genuine samples and forgeries, used only after legitimate acquisition. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Chain of custody** | Institutional/legal process for preserving, controlling, and accounting for evidence. SFAAO provides supporting technical elements but does not replace it. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **CLASSIFICATION** | Supervised class/writer training mode, distinct from pair and triplet modes. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Comparison run** | Identifiable comparison execution between references and questioned signatures using a defined model and calibration. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Contrastive Loss** | Metric-learning loss for pairs, pulling positive pairs together and separating negative pairs according to a compatible margin/rule. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **ConvNeXt-Tiny** | ConvNeXt backbone supported by SFAAO in the Tiny variant. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Cosine Contrastive Loss** | Contrastive-loss variant defined over cosine similarity/distance in compatible configurations. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Cosine Similarity** | Angular similarity metric between embeddings; higher values represent greater compatibility under the documented regime. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **CSV export** | Tabular result export when authorized by the profile/license and quota policy. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **Dataset** | Signature corpus used for training, validation, or scientific calibration, acquired and used under its own license/authorization. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **DatasetCatalog** | Component responsible for cataloging registered datasets and their operational metadata. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **DatasetFactory** | Component that constructs/selects a dataset implementation compatible with the configured profile. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Dataset Profile** | Formal description of how SFAAO interprets dataset structure, names, identities, classes, and metadata. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **DatasetProvider** | Component providing standardized access to a registered dataset. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Dataset shift** | Change between development/calibration and operational data distributions that can affect performance and the operating point. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Decision direction** | Rule defining whether a higher or lower metric value favors compatibility; it depends on the selected metric. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Decision threshold** | Cutoff applied to a metric to transform a continuous score into an operational decision. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **DistanceFactory** | Component that selects/builds the configured distance or similarity metric. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **DOCX report** | DOCX report generated from persisted data/results and subject to human review. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Dropout** | Regularization technique used in model configurations to reduce co-adaptation during training. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Early stopping** | Training-stop criterion triggered when the monitored metric stops improving according to configured parameters. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **EER** | Equal Error Rate: operating point where FAR and FRR are equal or approximately equal under the adopted estimation procedure. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **EfficientNet-B0** | EfficientNet backbone supported by SFAAO in the B0 variant. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Embedding** | Learned vector representation of a signature used for metric comparison. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **embedding_dim** | Configured dimensionality of the embedding vector. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Euclidean Distance** | L2 distance between embeddings; lower values represent greater compatibility under the documented regime. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **EXAMINER** | Operational role intended for the examiner/expert, with permissions limited by authorization rules. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **External impostor** | Signature from an individual external to the reference author, used to represent the impostor population in scientific calibration. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **FAR** | False Acceptance Rate: proportion of impostor comparisons incorrectly accepted at the evaluated operating point. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Feature** | Operational capability that may be enabled by license, such as training, calibration, comparison, reporting, or batch analysis. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **FORENSIC_CASE** | Forensic protocol in which calibration depends on prior classification by a handwriting expert/examiner of the questioned signatures required by the procedure. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Forgery** | Non-genuine signature attributed to the target author; its classification and origin shall be preserved as metadata. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **forgery_negative_ratio** | Sampling parameter controlling the proportion of negative examples originating from forgeries in compatible training populations. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **freeze_backbone** | Parameter controlling whether backbone weights remain frozen during training. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **FRR** | False Rejection Rate: proportion of genuine comparisons incorrectly rejected at the evaluated operating point. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **FULL** | Operational license profile with the broadest capability set, subject to the agreement and defined policies. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **Genuine** | Signature produced by the identified writer/author. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **GPDSsyntheticSignature** | Synthetic signature corpus from the GPDS family documented by SFAAO, used only when legitimately acquired. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Hash** | Cryptographic digest used to detect changes in files/artifacts. It does not replace provenance or chain of custody. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Independent External-Impostor Calibration** | Scientific methodology that estimates the threshold from references and independent external impostors while keeping questioned signatures outside threshold estimation. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **IntegrityChecker** | Bootstrap component that verifies the expected integrity of protected release files. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Integrity manifest** | Manifest recording expected files and their integrity identifiers. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Learning rate** | Hyperparameter controlling optimizer update magnitude during training. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **LicenseContext** | Object resulting from license validation that exposes identity, profile, features, protocols, and applicable limits. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **Machine fingerprint** | Technical installation/machine identifier used by the licensing subsystem; it is neither a password nor a private key. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Manifest** | Structured file describing content, hashes, or artifact/package metadata according to the relevant subsystem. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **MCYT** | MCYT biometric-database family. For the specific offline corpus documented by SFAAO, distinguish the MCYT-75 variant. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **MCYT-75** | Offline MCYT signature corpus with 75 writers, treated separately from other MCYT variants. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **ModelConfig** | Persistable configuration of model architecture and structural parameters. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **ModelFactory** | Component that builds an architecture compatible with the selected mode and configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Model package** | Versioned set of weights, configuration, and metadata required to import, register, and use a model traceably. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **ModelRepository** | Component responsible for persisting/retrieving model artifacts according to the SFAAO format. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **MobileNetV3** | Family of lightweight backbones supported by SFAAO in a homologated configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Optimizer** | Algorithm that updates trainable parameters from gradients. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **PAIR** | Training mode based on pairs of signatures and a relation label compatible with metric learning. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **PairRecord** | Structured record of a training/evaluation pair with identities, label, type, source, seed, and metadata. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **PBKDF2-HMAC-SHA-256** | Key-derivation function used by SFAAO for verified password storage under the documented security configuration. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **PDF report** | PDF report produced by the reporting pipeline and subject to the same traceability and human-review rules. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **positive_ratio** | Sampling parameter controlling the proportion of positive pairs in compatible populations. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **pretrained** | Parameter indicating use of pretrained backbone weights when available and methodologically appropriate. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Provenance** | Information about origins, activities, entities, and agents that explains how a result was produced. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **QUESTIONED** | Role assigned to a questioned signature under analysis. In the external scientific protocol, it remains outside threshold estimation. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Quota** | Quantitative limit imposed by profile/license on a specific operational resource. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **QuotaManager** | Component that queries and validates applicable limits before quota-controlled operations. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **RC** | RC-type reference used in the forensic workflow according to case registration and methodology. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **Report UID** | Stable identifier of a logical report, allowing complementary versions/formats to be related without losing traceability. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **ResNet18** | ResNet backbone with 18 layers in the supported configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **ResNet34** | ResNet backbone with 34 layers in the supported configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **ResNet50** | ResNet backbone with 50 layers in the supported configuration. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **RO** | RO-type reference used in the forensic workflow according to case registration and methodology. | [TUTORIAL-ENG.md](TUTORIAL-ENG.md) |
| **ROC** | Receiver Operating Characteristic: representation of performance tradeoffs as the threshold varies, under the definition adopted by the calibration module. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Scheduler** | Mechanism that changes learning rate during training according to a configured policy. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **SCIENTIFIC_EXTERNAL** | Scientific calibration protocol using independent external impostors while excluding QUESTIONED samples from threshold estimation. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Score** | Numeric value resulting from an embedding comparison; its interpretation depends on the metric and decision direction. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **Secure time** | Protected temporal state used to reduce risks associated with simple system-clock rollback. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **seed** | Value used to control sources of randomness and support execution reproducibility. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **SecurityBootstrap** | Fail-closed startup sequence validating storage, license, fingerprint, protected time, integrity, and other security preconditions. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Siamese network** | Architecture applying shared-parameter branches to distinct inputs to produce comparable representations; it does not necessarily describe every SFAAO model mode. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Signature UID** | Stable identifier of a signature persisted in the system. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Skilled forgery** | Forgery produced with visual/intended knowledge of the target signature. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **SQLite** | Local relational database used by the application for structured persistence of entities and operational state. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **Storage** | Persistent structure for operational files, separated from the protected application base. | [INSTALLATION-ENG.md](INSTALLATION-ENG.md) |
| **Threshold** | Operational synonym for decision threshold when the context is clear. | [CALIBRATION-ENG.md](CALIBRATION-ENG.md) |
| **TimeGuard** | Security component validating protected temporal consistency during the defined bootstrap/operation. | [SECURITY-ENG.md](SECURITY-ENG.md) |
| **Traceability** | Ability to traverse relationships among sources, executions, artifacts, and results in both directions. | [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) |
| **TrainingConfig** | Persistable configuration of training, sampling, optimization, scheduler, early stopping, and reproducibility parameters. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **TRIAL** | Evaluation license profile subject to the specific features and quotas defined in [LICENSING-ENG.md](LICENSING-ENG.md). | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **TRIPLET** | Training mode based on anchor/positive/negative triplets for metric learning. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Triplet Loss** | Loss that pulls anchor and positive together while pushing negative away according to a margin. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **UsageTracker** | Component that records cumulative usage of resources subject to licensing policy. | [LICENSING-ENG.md](LICENSING-ENG.md) |
| **UTSig** | Offline handwritten-signature dataset used only after legitimate acquisition and through an appropriate profile. | [DATASETS-ENG.md](DATASETS-ENG.md) |
| **Validation** | Evaluation performed on data separated from primary fitting for configuration/performance selection and monitoring. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **WAL** | SQLite Write-Ahead Logging; a journaling mode requiring care when backing up, copying, or migrating an active database. | [INSTALLATION-ENG.md](INSTALLATION-ENG.md) |
| **Weight decay** | Regularization parameter applied by the optimizer according to its implementation. | [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) |
| **Writer ID** | Writer/author identifier within a dataset or persisted context; its scope and provenance shall be preserved. | [DATASETS-ENG.md](DATASETS-ENG.md) |

---

# 3. Canonical-document map

| Document | Concepts primarily defined there in detail |
|---|---|
| [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) | training modes, backbones, embeddings, losses, metrics, optimization, schedulers, configuration, and reproducibility |
| [CALIBRATION-ENG.md](CALIBRATION-ENG.md) | FAR, FRR, EER, ROC, score, threshold, decision direction, `FORENSIC_CASE`, `SCIENTIFIC_EXTERNAL`, external-impostor calibration |
| [DATASETS-ENG.md](DATASETS-ENG.md) | datasets, profiles, writer IDs, pairs, genuine samples, forgeries, and corpus metadata |
| [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) | provenance, UIDs, artifacts, executions, reports, and persisted relationships |
| [SECURITY-ENG.md](SECURITY-ENG.md) | authentication, authorization, hashes, integrity, secure time, `TimeGuard`, `SecurityBootstrap` |
| [LICENSING-ENG.md](LICENSING-ENG.md) | profiles, features, protocols, quotas, consumption, and `LicenseContext` |
| [INSTALLATION-ENG.md](INSTALLATION-ENG.md) | distributed product, storage, protected runtime, Windows/Linux, backup, and deployment |
| [TUTORIAL-ENG.md](TUTORIAL-ENG.md) | ADMIN/EXAMINER operational sequence and per-case workflow |
| [REFERENCES-ENG.md](REFERENCES-ENG.md) | centralized scientific, normative, and documentary references |

# 4. Interpretation rules

- Metric terms shall be interpreted together with the **decision direction**.
- `Cosine Similarity` and `Euclidean Distance` do not necessarily use the same threshold inequality.
- `Calibration` means **biometric calibration of the threshold/operating point**, not neural-network training.
- `FORENSIC_CASE` and `SCIENTIFIC_EXTERNAL` are distinct protocols and shall not be conflated.
- `MCYT` shall not automatically be used as a synonym for `MCYT-75`.
- `Hash`, `Provenance`, `Traceability`, `Audit trail`, and `Chain of custody` are related but distinct concepts.
- License profile, user role, and user identity are different control dimensions.
- Technical integration of a backbone/dataset does not imply scientific performance equivalence with other configurations.

# 5. Stability rule

New terms shall be added to this glossary only after a corresponding canonical definition exists in an SFAAO technical, scientific, normative, or operational document.

The glossary shall not become a second source of truth.
