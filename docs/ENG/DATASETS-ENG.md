# SFAAO 2.4.0 — Datasets, Profiles, and Data Governance

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official technical specification  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Objective

This document defines the official SFAAO 2.4.0 dataset policy, the **Dataset Profile** abstraction, registration and validation components, sample-composition rules for training and evaluation, and provenance, reproducibility, privacy, and licensing requirements.

The platform-wide architecture is defined in [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md). Biometric calibration and its protocols are defined in [CALIBRATION-ENG.md](CALIBRATION-ENG.md). This document does not redefine those concepts; it describes exclusively how data and metadata are represented, validated, and exposed to the pipelines.


**Release 2.4.0:** the installer does not grant licenses or redistribution rights for third-party datasets. Imports, paths, and profiles must be checked in the installed system and the selected context.
---

# 2. Scope

This document covers:

- physical dataset;
- Dataset Profile;
- DatasetReference;
- DatasetProvider;
- DatasetCatalog;
- loaders and factories;
- registration and validation;
- writer identification;
- genuine/forgery classes;
- PAIR, TRIPLET, and CLASSIFICATION from the data perspective;
- training/validation composition;
- negative-pair types;
- seeds and sampling;
- provenance;
- dataset shift;
- external datasets for scientific calibration;
- acquisition and redistribution policy;
- privacy and chain of custody.

---

# 3. Core principle: a profile is not a dataset

SFAAO explicitly separates the **physical content of a corpus** from its **structural profile**.

```text
PHYSICAL DATASET
images + directories + files + source metadata
        │
        ▼
DATASET PROFILE
rules used to interpret the structure
        │
        ▼
DatasetProvider
validation + normalized access
        │
        ▼
SFAAO pipelines
```

A **Dataset Profile** describes how to interpret a legitimately obtained corpus. By definition, it does not contain the corpus biometric images.

**Normative rule:**

> Profiles/adapters may be distributed with SFAAO when authorized. Third-party datasets and their images are not redistributed by SFAAO.

---

# 4. Dataset definition

In this document, a **dataset** is an organized collection of samples and metadata used by a computational training, validation, evaluation, or calibration process.

For offline signature verification, a sample is typically a raster image of a scanned or synthesized handwritten signature associated, when available, with:

- writer identifier;
- sample class;
- source;
- session;
- corpus version;
- acquisition information;
- metadata required to interpret the protocol.

SFAAO does not assume that all datasets share the same physical organization or taxonomy.

---

# 5. Dataset Profile

## 5.1 Definition

A **Dataset Profile** is the abstraction that translates a dataset-specific structure into the internal SFAAO contract.

A profile may define or infer:

- directory structure;
- filename patterns;
- accepted extensions;
- writer ID;
- genuine classification;
- forgery classification;
- subsets/splits when available;
- image-discovery rules;
- corpus metadata;
- loader-specific details.

## 5.2 Purpose

Profile separation makes it possible to:

- integrate new datasets without rewriting the scientific core;
- avoid hard-coded paths;
- keep interpretation logic close to the dataset;
- preserve traceability;
- validate a corpus before training;
- reuse PAIR, TRIPLET, and CLASSIFICATION pipelines.

## 5.3 Restrictions

A profile distributed in the release shall not contain:

- dataset images;
- credentials;
- tokens;
- developer-private paths;
- unnecessary personal data;
- copies of third-party protected files;
- silent assumptions that change class semantics.

---

# 6. Dataset subsystem components

Confirmed SFAAO 2.4.0 components include:

| Component | Responsibility |
|---|---|
| `ai/datasets/dataset_reference.py` / `DatasetReference` | identifiable reference to a registered dataset |
| `ai/datasets/dataset_profile.py` / `DatasetProfile` | structural dataset contract |
| `ai/datasets/dataset_provider.py` / `DatasetProvider` | normalized access and validation |
| `ai/datasets/dataset_catalog.py` / `DatasetCatalog` | catalog of available datasets |
| `ai/datasets/dataset_factory.py` | dataset object construction |
| `ai/datasets/dataset_loader.py` | sample loading |
| `ai/datasets/signature_triplet_dataset.py` | TRIPLET data materialization |
| `ai/datasets/signature_classification_dataset.py` | CLASSIFICATION data materialization |
| `ai/datasets/profiles/` | profiles/adapters for supported structures |
| `controllers/dataset_controller.py` / `DatasetController` | dataset-governance orchestration |
| `ui/dataset_manager_window.py` / `DatasetManagerWindow` | registration and validation interface |

Pair construction for PAIR and statistical populations is centralized by the SFAAO builder infrastructure.

---

# 7. Registration and validation

The normative flow is:

```text
ADMIN
  ↓
Dataset Manager
  ↓
Add Dataset
  ↓
name + profile + version + directory
  ↓
DatasetReference
  ↓
DatasetProvider
  ↓
structural/classification validation
  ↓
image scan
  ├─ supported format?
  ├─ writer_id resolved?
  ├─ genuine?
  ├─ forgery?
  ├─ class conflict?
  └─ unclassified file?
  ↓
statistics
  ↓
final validation
  ↓
SQLite catalog persistence
  ↓
dataset enabled
```

A dataset shall not be considered operational merely because its directory exists. Its structure and classification must be validated through the corresponding profile.

---

# 8. Local catalog and persistence

SQLite maintains a **local catalog** of legitimately obtained and registered datasets.

Catalog registration represents metadata and access references. It **does not transfer ownership, license, or rights** over the corpus.

Images remain in the institution/user filesystem. The database stores the identifiers and metadata required to relate them to the pipelines.

---

# 9. Writer identity

`writer_id` represents the logical writer identity within the domain of a dataset/profile.

Rules:

1. IDs are comparable only within the context in which they were defined.
2. The same textual value in two datasets does not imply the same person.
3. Dataset/profile provenance shall accompany the ID.
4. PAIR/TRIPLET operations depend on correct writer-ID semantics.
5. A profile that cannot reliably resolve identity shall not invent IDs.

A safe conceptual key is:

```text
(dataset_id, dataset_version, writer_id)
```

---

# 10. Sample classes

For offline verification corpora, common classes include:

- **genuine** — genuine signature of the claimed writer;
- **forgery** — forgery associated with the target writer;
- **unknown/unclassified** — sample the profile could not classify.

Physical filenames shall not be interpreted directly by scientific pipelines. Interpretation belongs to the profile/provider.

---

# 11. Dataset shift

## 11.1 Definition

**Dataset shift** occurs when the statistical distribution of development data differs from the operational-domain distribution. Formally, a shift may be represented as:

$$
P_{\mathrm{dev}}(X,Y) \neq P_{\mathrm{oper}}(X,Y)
$$

or as changes in the corresponding marginal/conditional distributions [7].

In offline signatures, differences may arise from:

- writer population;
- language or writing system;
- resolution;
- scanner;
- background;
- writing instrument;
- compression;
- preprocessing;
- forgery nature;
- acquisition conditions;
- synthetic versus real data.

## 11.2 Application in SFAAO

Profile-based architecture does not eliminate dataset shift. It ensures that the **structure and provenance** of the corpus are known and traceable.

Statistical mitigation in the forensic domain, when applicable, belongs to the protocols defined in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 12. Reference datasets supported through profiles

The term **supported** means that the architecture has or may have a profile/adapter capable of interpreting a legitimately obtained copy. It does not mean redistribution.

## 12.1 GPDSsyntheticSignature

Offline synthetic corpus from the Grupo de Procesado Digital de Señales (ULPGC).

Reference characteristics:

- 4,000 synthetic individuals;
- 24 genuine signatures per identity;
- 30 forgeries per identity;
- images at an equivalent 600 dpi;
- corpus intended for offline signatures.

The official GPDS website also distinguishes this corpus from the 10,000-identity `GPDSsyntheticOnLineOffLineSignature` corpus and notes availability restrictions for older real GPDS corpora [1], [2].

In SFAAO, the profile shall record the exact corpus/version being used.

## 12.2 CEDAR

Classic offline signature database from the Center of Excellence for Document Analysis and Recognition.

Reference characteristics:

- 55 writers;
- 24 genuine signatures per writer;
- 24 forgeries per writer;
- 2,640 images in the corpus described by Kalera, Srihari, and Xu [3].

## 12.3 BHSig260

Multi-script corpus composed of two subsets:

- **Bengali:** 100 writers;
- **Hindi:** 160 writers;
- 24 genuine signatures per writer;
- 30 forgeries per writer.

Total: 260 writers and 14,040 images. The literature commonly evaluates the Bengali and Hindi subsets separately [4], [5].

The profile shall preserve the subset/script as metadata and shall not silently merge the two populations.

## 12.4 UTSig

Persian offline signature dataset.

Reference characteristics:

- 115 classes/writers;
- 8,280 images;
- 27 genuine signatures per class;
- 45 forgery samples per class;
- scanned at 600 dpi;
- grayscale images in the corpus described by the authors [6].

The original protocol contains collection-specific details and forgery categories. The profile shall preserve those details as metadata when relevant.

## 12.5 MCYT / MCYT-75

**MCYT** denotes a family of biometric corpora. For offline use in this documentation, the exact corpus must be identified explicitly.

The **MCYT-75 offline signature corpus** contains, as reference:

- 75 writers;
- 15 genuine signatures per writer;
- 15 skilled forgeries per writer;
- 2,250 images.

`MCYT-75` shall not be confused with online/bimodal variants such as MCYT100 or MCYT330, which use different protocols and sample counts [8], [9].

**Rule:** persist the exact corpus name and its version/source.

---

# 13. Institutional and custom datasets

SFAAO may also operate with:

- a custom dataset;
- a custom synthetic corpus;
- an institutional collection;
- an authorized third-party dataset.

A new corpus requires a compatible profile/adapter and validation of:

- classification;
- writer IDs;
- formats;
- structure;
- provenance;
- access rules.

Institutional ownership does not remove traceability and data-protection requirements.

---

# 14. PAIR — data view

In PAIR mode, each record represents two samples and their relationship.

Conceptual representation:

```text
(image_1, image_2, label, pair_type, writer_1, writer_2, provenance)
```

One possible convention is:

```text
label = 1 → similar/match
label = 0 → different/non-match
```

The actual convention used by the pipeline/loss shall be persisted; it shall not be inferred from filenames alone.

---

# 15. Positive pairs

For a writer with $n$ genuine samples, the maximum number of unordered genuine×genuine pairs is:

$$
N_{GG} = \binom{n}{2} = \frac{n(n-1)}{2}
$$

For example, with 24 genuine signatures:

$$
\binom{24}{2}=276
$$

distinct genuine×genuine pairs exist before any sampling strategy is applied.

---

# 16. Negative pairs

The infrastructure shall preserve semantic distinctions among negative types, for example:

- genuine × target-writer forgery;
- genuine × genuine from another writer;
- forgery × forgery;
- different-writer impostor;
- external impostor, when required by the protocol.

Different negative populations shall not be treated as equivalent without recording `pair_type`.

This distinction matters because the statistical difficulty of a skilled forgery may differ from that of a random writer.

---

# 17. `positive_ratio`

`positive_ratio` represents the planned proportion of positive pairs in a PAIR sample.

Example:

```text
positive_ratio = 0.5
```

targets approximately 50% positive pairs when data availability permits.

Both the planned value and the composition actually produced shall be traceable.

---

# 18. `forgery_negative_ratio`

`forgery_negative_ratio` controls the planned fraction of negative pairs formed by forgeries associated with the target writer when supported by the dataset.

It distinguishes:

```text
skilled-forgery negatives
versus
different-writer negatives
```

The parameter does not change the original dataset semantics; it controls the sampling strategy.

---

# 19. TRIPLET — data view

In TRIPLET mode, each unit contains:

```text
anchor
positive
negative
```

where:

- `anchor` and `positive` are semantically associated with the same identity according to the protocol;
- `negative` belongs to an incompatible identity/class.

The builder shall preserve writer IDs, source, and the rule used to produce the negative.

The mathematical definition of Triplet Loss belongs to the corresponding architecture/scientific documentation; only the data contract is defined here.

---

# 20. CLASSIFICATION — data view

In CLASSIFICATION mode, each image is associated with a dataset-defined class/identity.

Conceptually:

```text
image → writer/class label
```

Class mapping shall be:

- deterministic;
- persistable;
- reconstructable;
- compatible between training and validation.

Internal numeric class indices shall not replace the original writer ID in provenance.

---

# 21. Training/validation separation

Training and validation shall be logically independent sets.

In PAIR mode, parameters may include:

```text
train_pairs
val_pairs
positive_ratio
forgery_negative_ratio
seed
```

The split strategy shall be recorded. When a protocol requires generalization to unseen writers, identity-based separation shall prevent the same writer from improperly crossing subsets.

SFAAO does not impose one universal split policy on every experiment; the selected policy is part of experimental provenance.

---

# 22. Data-leakage prevention

**Data leakage** occurs when information that should be unavailable to a training/selection step improperly influences the model or evaluation.

For signature datasets, the following shall be avoided according to the protocol:

- the same image in training and validation;
- improperly duplicated pairs across splits;
- test-writer leakage into training in writer-independent protocols;
- forensic questioned samples used to estimate parameters that are intended to be independent;
- transformed copies of the same sample crossing splits without control.

The specific separation between scientific calibration and questioned signatures is defined in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

---

# 23. Seeds and reproducible sampling

Every relevant random operation should receive a persistable seed when technically possible.

The seed may control:

- pair selection;
- triplet selection;
- negative selection;
- impostor sampling;
- subset selection;
- shuffling.

Example:

```text
seed = 42
```

A seed improves reproducibility but does not guarantee absolute determinism across all hardware, kernels, and library versions.

---

# 24. `PairRecord` and population provenance

Pair records shall support reconstruction of the generation process. A representative SFAAO contract contains:

```text
pair_id
image_1
image_2
writer_1
writer_2
label
pair_type
dataset_name
dataset_profile
source
seed
metadata
```

Dataset/profile version, split, run, and other required identifiers shall also be included when applicable.

The goal is to ensure that an aggregate result never depends on a pair population of unknown origin.

---

# 25. Preprocessing and compatibility

The dataset supplies samples; the scientific pipeline defines preprocessing.

SFAAO requires processing used during model construction to remain compatible with later stages associated with that model.

Possible operations include:

- grayscale;
- binarization;
- background removal;
- aspect-ratio preservation;
- padding;
- resize;
- normalization;
- tensor conversion.

An original dataset resolution does not force the model to operate directly at that resolution.

---

# 26. External datasets in scientific calibration


### External datasets in the LORO domain

Under `SCIENTIFIC_EXTERNAL` 2.4.0, each external query generates A(N−1) folds by excluding each reference in turn, keeping the non-match calibration population in the same statistical domain as inference. Case questioned signatures do not participate in this selection or threshold estimation.

External datasets may supply impostor populations to scientific protocols defined in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

Data rules:

1. the external dataset source shall be identified;
2. profile and version shall be recorded;
3. samples actually used shall be traceable;
4. seed/subsampling shall be recorded;
5. questioned signatures shall not be reclassified as external samples;
6. training, biometric calibration, and inference remain distinct stages.

The **Independent External-Impostor Calibration** methodology is described in the calibration document and the SFAAO scientific article accepted for publication [10].

---

# 27. Acquisition policy

To use a third-party corpus, the user/institution shall:

1. identify the correct dataset;
2. review the current terms of use;
3. obtain authorization when required;
4. obtain files from a legitimate source;
5. store them in authorized infrastructure;
6. select/configure the profile;
7. validate the structure;
8. use the data only within granted rights.

SFAAO does not bypass authentication, paywalls, access controls, or dataset licensing.

---

# 28. Redistribution policy

The official release does not contain:

- GPDS;
- CEDAR;
- BHSig260;
- UTSig;
- MCYT;
- images from those datasets;
- other third-party datasets.

The presence of a profile/adapter **does not constitute redistribution of the corpus** and does not grant rights to use its content.

---

# 29. Privacy, data protection, and chain of custody

Before importing custom, institutional, or real datasets, the institution shall evaluate:

- legal basis;
- purpose;
- usage rights;
- data minimization;
- retention;
- anonymization or pseudonymization when applicable;
- access control;
- chain of custody when applicable;
- regulatory and institutional requirements.

SFAAO provides technical mechanisms; the legal legitimacy of collection and processing depends on the use context.

---

# 30. Integrity and paths

Distributed profiles shall not contain absolute paths from the developer machine.

Physical references shall be resolved through local configuration/authorized catalog.

Moving a directory shall not change the scientific identity of a dataset. Changing corpus content, version, or classification, however, shall be treated as a provenance-relevant change.

---

# 31. Minimum experiment reproducibility

An experiment that depends on a dataset shall be able to identify at least:

```text
dataset_name
dataset_version/corpus
dataset_profile
dataset_source
split strategy
writer population
sample counts
pair/triplet/classification strategy
seed
preprocessing contract
training configuration
platform/version
```

When possible, hashes/manifests of local artifacts may complement provenance as defined in [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

---

# 32. Relationship with TRACEABILITY.md

[TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) is the canonical definition for SFAAO provenance chains and auditability.

This document only establishes which dataset information must be available to that chain.

When concepts overlap, the canonical traceability definition prevails.

---

# 33. Relationship with CALIBRATION.md

[CALIBRATION-ENG.md](CALIBRATION-ENG.md) defines:

- biometric calibration of the decision threshold/operating point;
- FAR, FRR, and EER;
- FORENSIC_CASE and SCIENTIFIC_EXTERNAL protocols;
- Independent External-Impostor Calibration;
- threshold selection and persistence.

[DATASETS-ENG.md](DATASETS-ENG.md) defines the **origin, interpretation, and governance of data** used by those protocols.

---

# 34. Relationship with ARCHITECTURE.md

[ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) defines:

- separation of responsibilities;
- pipelines;
- factories;
- controllers;
- persistence;
- architectural rules.

[DATASETS-ENG.md](DATASETS-ENG.md) specializes the `ai/datasets/` subsystem contract without changing the platform-wide architecture.

---

# 35. Checklist for a new Dataset Profile

Before a profile is considered ready:

- [ ] name and version defined;
- [ ] exact corpus identified;
- [ ] source documented;
- [ ] terms of use reviewed;
- [ ] directories interpreted;
- [ ] image formats identified;
- [ ] writer ID validated;
- [ ] genuine samples classified;
- [ ] forgeries classified;
- [ ] unclassified files detected;
- [ ] conflicts detected;
- [ ] no developer-private paths;
- [ ] no dataset files embedded in code/profile;
- [ ] split/protocol documented when applicable;
- [ ] provenance preserved;
- [ ] functional validation executed.

---

# 36. Summary of normative rules

1. **A profile is not a dataset.**
2. **Third-party datasets are not redistributed.**
3. **Support does not imply a license.**
4. **Writer IDs always have dataset/profile context.**
5. **Classes are not silently inferred outside the profile.**
6. **PAIR/TRIPLET/CLASSIFICATION shall preserve provenance.**
7. **Negative types shall remain distinguishable.**
8. **Splits and seeds shall be recordable.**
9. **External calibration datasets shall be identifiable.**
10. **Questioned samples shall not contaminate an independent scientific protocol.**
11. **MCYT shall identify the exact corpus; MCYT-75 is not synonymous with all MCYT variants.**
12. **Corpus/version changes require provenance updates.**

---

# 37. References

[1] M. A. Ferrer, M. Diaz-Cabrera, and A. Morales, “Static signature synthesis: A neuromotor inspired approach for biometrics,” *IEEE Transactions on Pattern Analysis and Machine Intelligence*, vol. 37, no. 3, pp. 667–680, 2015. doi: 10.1109/TPAMI.2014.2343220.

[2] Grupo de Procesado Digital de Señales — Universidad de Las Palmas de Gran Canaria, “GPDS — Toolbox & Databases,” `GPDSsyntheticSignature` section. Accessed Sep. 30, 2026. https://gpds.ulpgc.es/downloadnew/download.htm

[3] M. K. Kalera, S. N. Srihari, and A. Xu, “Offline signature verification and identification using distance statistics,” *International Journal of Pattern Recognition and Artificial Intelligence*, vol. 18, no. 7, pp. 1339–1360, 2004. doi: 10.1142/S0218001404003630.

[4] S. Pal, U. Pal, and M. Blumenstein, “Off-line verification technique for Hindi signatures,” *IET Biometrics*, vol. 2, no. 4, pp. 182–190, 2013. doi: 10.1049/iet-bmt.2013.0016.

[5] IAPR TC4, “Signature Datasets — BHSig260,” International Association for Pattern Recognition Technical Committee 4. Accessed Sep. 30, 2026. https://iapr-tc4.org/signature-datasets/

[6] A. Soleimani, B. N. Araabi, and K. Fouladi, “UTSig: A Persian offline signature dataset,” *IET Biometrics*, 2017. doi: 10.1049/iet-bmt.2015.0058.

[7] J. G. Moreno-Torres, T. Raeder, R. Alaiz-Rodríguez, N. V. Chawla, and F. Herrera, “A unifying view on dataset shift in classification,” *Pattern Recognition*, vol. 45, no. 1, pp. 521–530, 2012. doi: 10.1016/j.patcog.2011.06.019.

[8] J. Ortega-Garcia et al., “MCYT baseline corpus: A bimodal biometric database,” *IEE Proceedings — Vision, Image and Signal Processing*, vol. 150, no. 6, pp. 395–401, 2003. doi: 10.1049/ip-vis:20031078.

[9] IAPR TC4, “Signature Datasets — MCYT-SignatureOff-75,” International Association for Pattern Recognition Technical Committee 4. Accessed Sep. 30, 2026. https://iapr-tc4.org/signature-datasets/

[10] D. A. M. José, L. de Medeiros Marques, A. Bueno, and R. B. Z. da Silva, “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification,” article accepted for publication in *IEEE Access*, 2026 (final bibliographic metadata pending).

---

# 38. Document stability

This document is part of the official SFAAO 2.4.0 specification.

Future changes shall preserve:

- separation between profile and content;
- compatibility with the modular architecture;
- traceability;
- reproducibility;
- non-redistribution policy;
- scientific data semantics.

New datasets should preferably be integrated through new profiles/adapters without redesigning the core.
