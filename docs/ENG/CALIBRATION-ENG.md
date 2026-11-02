# Biometric Calibration in SFAAO 2.4.0

## 1. Purpose

This document defines the concept of **biometric decision-threshold calibration** and describes its application in SFAAO 2.4.0.

The editorial rule is:

1. define and scientifically ground the concept;
2. explain its purpose and interpretation;
3. only then describe its concrete application in SFAAO.

The calibration addressed here is primarily **decision-threshold / operating-point calibration**. It must not be confused with probability-calibration techniques such as Platt scaling or temperature scaling, whose objective is to transform model outputs into calibrated probabilities.


**Release note:** the related methodology paper has been accepted for publication in IEEE Access; the effective parameters of each calibration, protocol, and metric remain specific to the context persisted by SFAAO.
---

## 2. Biometric score

### 2.1 Definition and scientific basis

In biometric verification systems, a comparison between two samples produces a **score** expressing the degree of correspondence according to the representation and metric being used. The score may be a **similarity**, where larger values indicate greater proximity, or a **distance**, where smaller values indicate greater proximity. The interpretation of scores, thresholds, and error rates is central to biometric-system evaluation (Jain, Ross, and Prabhakar, 2004; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

### 2.2 Purpose and interpretation

A score is a continuous variable. By itself, it is not a categorical decision. A **decision threshold** is required to map the score to an acceptance or rejection region.

### 2.3 Application in SFAAO

In SFAAO, scores are produced from embeddings extracted by Deep Learning models. The platform supports, among other configurations, **Cosine Similarity** and **Euclidean Distance**. Decision orientation is persisted with the model and calibration to prevent a fixed decision rule from being incorrectly applied to a metric with opposite semantics.

---

## 3. Genuine and impostor populations

### 3.1 Definition and scientific basis

A biometric verification evaluation requires two classes of comparisons:

- **genuine/match**: comparisons between samples attributed to the same identity;
- **impostor/non-match**: comparisons between samples attributed to different identities.

The resulting distributions are used to estimate system behavior across decision thresholds (Jain, Ross, and Prabhakar, 2004; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

In offline signature verification, construction of these populations requires particular attention to writer identity, skilled forgeries, dataset composition, and separation between development and evaluation (Plamondon and Lorette, 1989; Hafemann, Sabourin, and Oliveira, 2017a; Hafemann, Sabourin, and Oliveira, 2017b).

### 3.2 Purpose and interpretation

The genuine distribution characterizes scores produced when the claimed identity is consistent with the reference. The impostor distribution characterizes non-matching comparisons. Their overlap determines the achievable trade-off between false acceptances and false rejections.

### 3.3 Application in SFAAO

SFAAO builds populations according to the selected calibration protocol. Signatures, forensic roles, generated pairs, impostor origin, counts, and selection parameters must remain traceable.

---

## 4. Decision threshold

### 4.1 Definition and scientific basis

A **threshold** $\tau$ is a boundary in score space used to convert a continuous variable into an operational decision. Selecting an operating point directly determines system error rates (Jain, Ross, and Prabhakar, 2004; Fawcett, 2006; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

For a similarity metric:

$$
s \ge \tau \Rightarrow \text{accept}
$$

For a distance metric:

$$
d \le \tau \Rightarrow \text{accept}
$$

### 4.2 Purpose and interpretation

In general, there is no universal threshold independent of model, metric, population, and evaluation context. A threshold must be interpreted together with the protocol that produced it.

### 4.3 Application in SFAAO

In SFAAO:

- **Cosine Similarity** uses larger scores as greater compatibility;
- **Euclidean Distance** uses smaller distances as greater compatibility;
- the training threshold is retained as model provenance;
- the threshold used for an operational decision is derived from the calibration selected for the relevant context.

---

## 5. FAR — False Acceptance Rate

### 5.1 Definition and scientific basis

The **False Acceptance Rate (FAR)** is the proportion of impostor/non-match comparisons incorrectly assigned to the acceptance region. It is a fundamental biometric-performance measure (Jain, Ross, and Prabhakar, 2004; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

For similarity:

$$
FAR(\tau)=
\frac{\left|\{s_I \ge \tau\}\right|}{N_I}
$$

For distance:

$$
FAR(\tau)=
\frac{\left|\{d_I \le \tau\}\right|}{N_I}
$$

where $N_I$ is the number of impostor comparisons.

### 5.2 Purpose and interpretation

FAR quantifies improper acceptance within the evaluated impostor population. It should not be extrapolated to a different population without considering protocol, sampling, and dataset shift.

### 5.3 Application in SFAAO

SFAAO computes FAR during evaluation and calibration while respecting metric semantics. The value is persisted in scientific artifacts together with its corresponding threshold.

---

## 6. FRR — False Rejection Rate

### 6.1 Definition and scientific basis

The **False Rejection Rate (FRR)** is the proportion of genuine/match comparisons incorrectly assigned to the rejection region (Jain, Ross, and Prabhakar, 2004; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

For similarity:

$$
FRR(\tau)=
\frac{\left|\{s_G < \tau\}\right|}{N_G}
$$

For distance:

$$
FRR(\tau)=
\frac{\left|\{d_G > \tau\}\right|}{N_G}
$$

where $N_G$ is the number of genuine comparisons.

### 6.2 Purpose and interpretation

FRR quantifies improper rejection within the evaluated genuine population. Like FAR, it depends on the population, metric, model, and protocol.

### 6.3 Application in SFAAO

The platform computes FRR in the same threshold sweep used for FAR, enabling analysis of the trade-off between the two error types.

---

## 7. ROC and operating point

### 7.1 Definition and scientific basis

**Receiver Operating Characteristic (ROC)** analysis represents the behavior of a decision system as its threshold varies. It supports visualization of the trade-off between success and error rates and comparison of operating points (Fawcett, 2006).

In biometrics, an operating point must be reported together with the populations, test conditions, and metrics that produced it (ISO/IEC 19795-1:2021).

### 7.2 Purpose and interpretation

ROC analysis does not, by itself, determine which threshold should be used. It provides a view of system behavior across decision regions. Operational threshold selection depends on the protocol objective.

### 7.3 Application in SFAAO

SFAAO persists plots and artifacts associated with evaluation/calibration, including FAR × FRR behavior and score distributions when available.

---

## 8. EER — Equal Error Rate

### 8.1 Definition and scientific basis

The **Equal Error Rate (EER)** is the error rate associated with the operating point where false acceptance and false rejection rates are equal or, in a discrete implementation, as close as possible. EER is widely used as a compact biometric-performance measure, although it does not replace analysis of the complete operating point and evaluated populations (Jain, Ross, and Prabhakar, 2004; Jain, Flynn, and Ross, 2008; ISO/IEC 19795-1:2021).

In SFAAO, the corresponding threshold is estimated as:

$$
\tau_{\mathrm{EER}} =
\underset{\tau}{\mathrm{arg\,min}}
\left|
\mathrm{FAR}(\tau)-\mathrm{FRR}(\tau)
\right|
$$

### 8.2 Purpose and interpretation

EER provides a compact reference for population separability under a defined protocol. A low EER on one population is not a guarantee of equivalent performance on another population.

An EER of zero indicates perfect separation **for the evaluated population and considered threshold set**; it does not imply a universal zero error rate.

### 8.3 Application in SFAAO

SFAAO separately records:

- the EER rate;
- the $\tau_{EER}$ threshold;
- the metric;
- the populations;
- protocol parameters;
- the corresponding model.

Thus, `EER` and `tau_EER` are not treated as synonyms.

---

## 9. Biometric decision-threshold calibration

### 9.1 Definition and scientific basis

In this document, **biometric calibration** means a post-training process in which scores produced by a fixed model are analyzed over genuine and impostor populations in order to estimate error rates and determine a threshold or operating point appropriate to the protocol.

This interpretation follows the foundations of biometric decision making, score-based evaluation, thresholds, and error rates described by Jain, Ross, and Prabhakar (2004), Jain, Flynn, and Ross (2008), Fawcett (2006), and ISO/IEC 19795-1:2021.

The calibration implemented by SFAAO must not be confused with **probability calibration**. Its primary objective is not to transform a score into a calibrated posterior probability, but to establish and document an operational decision boundary.

### 9.2 Purpose and interpretation

Calibration separates three conceptually different stages:

```text
TRAINING
    ↓
learns model parameters

CALIBRATION
    ↓
keeps the model fixed
analyzes scores from defined populations
estimates error rates and threshold

INFERENCE / COMPARISON
    ↓
applies model + calibration
to evidence reserved for evaluation
```

This separation matters because a threshold obtained during model development may not preserve the same behavior when the operational distribution changes.

### 9.3 Application in SFAAO 2.4.0 — aligned decision domain

Calibration in 2.4.0 does not estimate a threshold in one statistical domain and apply it in another. The decision domain is explicitly persisted.

#### A1 — `PAIRWISE_SCORE`

A1 is one score between two signatures. Under LORO it is a **component/provenance value**, not the unit that receives the A(N−1) threshold.

#### A(N−1) — `LORO_MEAN`

With $N$ references, each fold excludes one reference and aggregates the pseudo-query or questioned signature against the remaining $N-1$ references:

$$
A_{-k}(Q)=\frac{1}{N-1}\sum_{j\ne k}s(Q,R_j).
$$

With five references, the decision unit is A4.

#### A(N) — descriptive statistic

$$
A_N(Q)=\frac{1}{N}\sum_{j=1}^{N}s(Q,R_j).
$$

A(N) may be persisted descriptively but is **not classified again** with the LORO threshold.

### 9.4 LORO population construction

With five genuine references there is no independent sixth genuine signature from which to build a genuine A5 unit without self-comparison. SFAAO 2.4.0 uses **Leave-One-Reference-Out (LORO)**:

- each reference acts once as pseudo-query;
- the pseudo-query is compared with the remaining four references;
- five genuine A4 values are obtained;
- for each non-match query, each reference is excluded in turn, yielding five non-match A4 values;
- FAR, FRR, EER, and thresholds are estimated in the same A4 domain.

Self-comparison is not used to manufacture a genuine A5 unit.

### 9.5 Thresholds and target FAR

Release 2.4.0 distinguishes:

- $\tau_{EER}$ — threshold at the EER point;
- $\tau_{forensic}$ — threshold associated with the operational target FAR (1% in the current official configuration);
- $\tau_{operational}$ — final conservative threshold used by the three-zone policy.

For Cosine Similarity:

$$
\tau_{operational}=\max(\tau_{forensic},\tau_{EER}).
$$

For Euclidean Distance:

$$
\tau_{operational}=\min(\tau_{forensic},\tau_{EER}).
$$

The legacy alias `threshold_operational` may appear in historical artifacts for the target-FAR-derived threshold. In 2.4.0 documentation that value is canonically named **$\tau_{forensic}$**, avoiding confusion with the final $\tau_{operational}$.

### 9.6 Three-zone policy

Cosine Similarity:

- score $\ge\tau_{operational}$ → `COMPATIBLE`;
- $\tau_{EER}\le$ score $<\tau_{operational}$ → `INCONCLUSIVE`;
- score $<\tau_{EER}$ → `INCOMPATIBLE`.

Euclidean Distance:

- distance $\le\tau_{operational}$ → `COMPATIBLE`;
- $\tau_{operational}<$ distance $\le\tau_{EER}$ → `INCONCLUSIVE`;
- distance $>\tau_{EER}$ → `INCOMPATIBLE`.

When $\tau_{forensic}=\tau_{EER}$ there is no gray zone.

## 10. Dataset shift

Dataset shift motivates the separation of the training threshold from operational calibration. The training threshold is retained as model provenance, not as an automatic case decision threshold.

## 11. Independence of questioned evidence — `SCIENTIFIC_EXTERNAL`

Under the scientific protocol, questioned signatures remain **blind during calibration**. Populations are built from references and independent external impostors; questioned signatures enter inference only after thresholds are finalized.

```text
REFERENCES + EXTERNAL IMPOSTORS
        ↓
LORO A(N-1) CALIBRATION
        ↓
τ_EER / τ_forensic / τ_operational
        ↓
QUESTIONED SIGNATURES (inference only)
```

## 12. `FORENSIC_CASE` in release 2.4.0

### 12.1 RC — contractual references

- genuine case references form the genuine LORO folds;
- the non-match population uses only `QUESTIONED_IMPOSTOR` previously classified by the forensic examiner;
- compatible questioned signatures are **not promoted to genuine samples** for calibration;
- unclassified questioned signatures do not silently enter the non-match population;
- if adequate forensic non-match evidence is unavailable, an RC threshold must not be artificially estimated.

### 12.2 RO — official pattern

For RO, all `QUESTIONED` signatures are intentionally treated as non-match for calibration because the official pattern may represent an institutional signature distinct from the contractual/questioned pattern. This is a protocol-specific rule and remains explicit in provenance.

## 13. LORO inference and consolidation

Each questioned signature is evaluated fold by fold in the same A(N−1) domain as calibration. Folds are not averaged again and no majority voting is used.

- all `COMPATIBLE` → `STABLE_COMPATIBLE`;
- all `INCOMPATIBLE` → `STABLE_INCOMPATIBLE`;
- all `INCONCLUSIVE` → `STABLE_INCONCLUSIVE`;
- any mixture → `REFERENCE_SENSITIVE`, consolidated as `INCONCLUSIVE`.

Persistence records folds, excluded reference, aggregated score, thresholds, technical decision, and stability. `ALIGNED` denotes compatible calibration/inference domains; `MISMATCH` blocks execution; `LEGACY_MISMATCH` identifies incompatible historical records.

## 14. Interpretation limitations

Calibration must be interpreted within its protocol.

One must not conclude that:

- an observed EER is a universal error rate;
- one threshold is valid for every model;
- one threshold is valid for every metric;
- a threshold remains optimal after a relevant distribution shift;
- a computational result replaces the forensic examiner's conclusion;
- a scientific calibration may silently use questioned signatures to tune the threshold that will later be applied to those same signatures.

---

## 15. References

**FAWCETT, T.** An Introduction to ROC Analysis. *Pattern Recognition Letters*, vol. 27, no. 8, pp. 861–874, 2006. DOI: 10.1016/j.patrec.2005.10.010.

**HAFEMANN, L. G.; SABOURIN, R.; OLIVEIRA, L. S.** Offline Handwritten Signature Verification — Literature Review. In: *7th International Conference on Image Processing Theory, Tools and Applications (IPTA)*, 2017, pp. 1–8. DOI: 10.1109/IPTA.2017.8310112. (2017a)

**HAFEMANN, L. G.; SABOURIN, R.; OLIVEIRA, L. S.** Learning Features for Offline Handwritten Signature Verification Using Deep Convolutional Neural Networks. *Pattern Recognition*, vol. 70, pp. 163–176, 2017. DOI: 10.1016/j.patcog.2017.05.012. (2017b)

**ISO/IEC.** ISO/IEC 19795-1:2021 — *Information technology — Biometric performance testing and reporting — Part 1: Principles and framework*. 2nd ed., 2021.

**JAIN, A. K.; FLYNN, P.; ROSS, A. A. (eds.).** *Handbook of Biometrics*. Springer, 2008. DOI: 10.1007/978-0-387-71041-9.

**JAIN, A. K.; ROSS, A.; PRABHAKAR, S.** An Introduction to Biometric Recognition. *IEEE Transactions on Circuits and Systems for Video Technology*, vol. 14, no. 1, pp. 4–20, 2004. DOI: 10.1109/TCSVT.2003.818349.

**JOSÉ, D. A. M.; MARQUES, L. de M.; BUENO, A.; SILVA, R. B. Z. da.** Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification. Accepted for publication in *IEEE Access*, 2026; editorial acceptance must not be confused with final publication.

**MORENO-TORRES, J. G.; RAEDER, T.; ALAIZ-RODRÍGUEZ, R.; CHAWLA, N. V.; HERRERA, F.** A Unifying View on Dataset Shift in Classification. *Pattern Recognition*, vol. 45, no. 1, pp. 521–530, 2012. DOI: 10.1016/j.patcog.2011.06.019.

**PLAMONDON, R.; LORETTE, G.** Automatic Signature Verification and Writer Identification — The State of the Art. *Pattern Recognition*, vol. 22, no. 2, pp. 107–131, 1989. DOI: 10.1016/0031-3203(89)90059-9.

---

## 16. Terminology note

The preferred expression in this documentation is:

> **biometric decision-threshold calibration**

or, once the context is established:

> **operating-point calibration**

The generic expression “neural-network calibration” is avoided because the procedure described here does not modify model weights and is not, by definition, a probability-calibration technique.
