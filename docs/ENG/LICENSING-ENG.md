# SFAAO 2.4.0 — Licensing, Operational Profiles, and Quotas

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official technical specification  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Objective

This document defines the SFAAO 2.4.0 technical licensing architecture, the `TRIAL`, `ACADEMIC`, and `FULL` operational profiles, the semantics of features, protocols, and quotas, and activation, consumption, and persistence rules.

This document **does not replace the legal software use/distribution license**. The legal `LICENSE`, `LICENSE.pt-BR.md`, and `LICENSE.en.md` files, when published with the repository/release, prevail regarding legal grants and obligations.

---

**Release scope:** this document describes the 2.4.0 licensing model. Concrete quotas and permissions depend on the issued license and should be checked against the effective profile in the homologated application; example values are not universal. The manufacturer-side license issuer and private key are not included in the customer package.

# 2. Four concepts that shall not be conflated

In SFAAO, “license” may appear in distinct contexts:

1. **Legal software license** — defines rights of use, copying, distribution, and other legal conditions.
2. **Runtime operational license** — an authorized file, normally `storage/license.sfaao`, used to activate an installation.
3. **Operational profile** — `TRIAL`, `ACADEMIC`, or `FULL`, with features, protocols, and limits.
4. **Third-party dataset license** — independent rights over GPDS, CEDAR, BHSig260, UTSig, MCYT, or any other corpus.

Acquiring SFAAO does not automatically grant rights over third-party datasets.

---

# 3. Licensing architecture


### Official activation flow in release 2.4.0

1. `sfaao-license-request` runs the fingerprint algorithm from the 2.4.0 image itself and generates `SFAAO_LICENSE_REQUEST_V1`;
2. the manufacturer issues the signed license outside the client environment;
3. `sfaao-license-install` validates signature and machine binding using the distributed public key and only then installs `license.sfaao` in persistent storage.

The current fingerprint uses `SFAAO-FP-V2:`. The manufacturer's private key, administrative `license_generator.py`, and other secrets **are not part of the client, OCI image, or distributable repository**.

The operational license remains at `runtime/storage/license.sfaao` on the host and is mounted as `/opt/sfaao/storage/license.sfaao` in the container.

The conceptual flow is:

```text
license file
      ↓
LicenseLoader
      ↓
cryptographic verification
      ↓
installation binding
      ↓
protected temporal state
      ↓
LicenseManager
      ↓
LicenseContext
      ↓
LicensePolicy / QuotaManager
      ↓
authorized operations
```

[SECURITY-ENG.md](SECURITY-ENG.md) is canonical for cryptographic mechanisms, machine fingerprint, secure time, and key protection.

---

# 4. LicenseService

`LicenseService` is the central service that:

- locates/loads the license;
- requests signature validation;
- validates installation context;
- validates associated temporal state;
- produces the `LicenseContext` used by application policies.

The default operational path is:

```text
storage/license.sfaao
```

The verification public key belongs to the protected runtime; the issuance private key does not.

---

# 5. LicenseContext

The license context represents an already validated authorized state.

Confirmed fields include:

```text
valid
license_id
organization
license_type
machine_id
version
issued_at
expiration
profile
features
protocols
limits
```

`license_type` and `profile` are distinct fields and shall not be treated as synonyms.

---

# 6. Operational profile

An **operational profile** is a predefined set of capabilities and limits applicable to a license.

Validated profiles are:

```text
TRIAL
ACADEMIC
FULL
```

Duration/expiration is not inferred solely from the profile name. Validity is determined by signed license metadata and validated temporal state.

---

# 7. Feature

A **feature** is a functional capability controlled by the license context.

Validated features include:

```text
forensic_analysis
batch_analysis
training
calibration
comparison
docx_report
pdf_report
audit_trail
```

An enabled feature does not replace the corresponding protocol or quota check.

---

# 8. Protocol entitlement

The context also explicitly controls protocols.

Operational keys:

```text
scientific
forensic
```

Within SFAAO:

```text
scientific → SCIENTIFIC_EXTERNAL
forensic   → FORENSIC_CASE
```

Scientific definitions of these protocols belong to [CALIBRATION-ENG.md](CALIBRATION-ENG.md). This document defines only usage entitlement.

---

# 9. Protocol matrix

| Profile | SCIENTIFIC_EXTERNAL | FORENSIC_CASE |
|---|---:|---:|
| `TRIAL` | allowed | allowed |
| `ACADEMIC` | allowed | blocked |
| `FULL` | allowed | allowed |

Blocking the forensic protocol in `ACADEMIC` is a licensing policy, not a scientific statement about the protocol.

---

# 10. Commercial audit trail

The feature:

```text
audit_trail
```

is enabled in the `FULL` profile.

This **does not mean** other profiles may remove minimum provenance records required for scientific and transactional consistency.

Mandatory scientific traceability is defined in [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) and remains distinct from the commercial `audit_trail` feature.

---

# 11. TRIAL profile

`TRIAL` exposes the product's core capabilities subject to lower quotas.

It permits:

- training;
- calibration;
- comparison;
- batch analysis;
- DOCX;
- PDF;
- scientific protocol;
- forensic protocol.

`audit_trail` is not part of this profile's complete commercial feature set.

Concrete expiration shall be read from the valid license; this document does not define one universal fixed duration for `TRIAL`.

---

# 12. ACADEMIC profile

`ACADEMIC` is oriented toward academic/scientific use and has intermediate quotas.

It permits:

- training;
- calibration;
- comparison;
- batch analysis;
- DOCX;
- PDF;
- `SCIENTIFIC_EXTERNAL`.

By policy, it blocks:

```text
FORENSIC_CASE
```

Availability of other features shall not bypass this protocol restriction.

---

# 13. FULL profile

`FULL` enables:

- scientific and forensic protocols;
- validated functional features;
- `audit_trail`;
- resources without numeric limits where current fields are defined as `None`.

`None` means **unlimited under the current policy**, not absence of validation.

---

# 14. Quota matrix

| Resource | TRIAL | ACADEMIC | FULL |
|---|---:|---:|---:|
| Active users | 1 | 10 | unlimited |
| Active `EXAMINER` users | 1 | 5 | unlimited |
| Cases | 2 | 50 | unlimited |
| Individual comparisons | unlimited | unlimited | unlimited |
| Batch executions | 3 | 100 | unlimited |
| Reports | 1 | 20 | unlimited |
| CSV exports | 3 | 50 | unlimited |
| Training runs | 3 | 100 | unlimited |
| Calibrations | 2 | 50 | unlimited |
| Available datasets | 2 | 10 | unlimited |
| Registered models | 2 | 20 | unlimited |

These values describe the SFAAO 2.4.0 release documented on 2026-09-30. Future changes require versioning and documentation updates.

---

# 15. `None` means unlimited

When a resource has:

```text
limit = None
```

`QuotaManager` interprets it as unlimited.

The quota result shall preserve the distinction between:

```text
limit = None       → unlimited
limit = number     → limited
missing limit      → invalid configuration/fail-closed
```

An expected missing limit shall not silently become unlimited.

---

# 16. Quota

A **quota** is an operational limit associated with a resource.

If:

- $L$ = limit;
- $U$ = calculated usage;
- $A$ = requested amount;

a limited operation is allowed when:

$$
U + A \le L
$$

For unlimited resources, $L$ is represented by `None`.

---

# 17. Three quota semantics

Not all SFAAO quotas have the same semantics.

There are three groups:

```text
CUMULATIVE
CURRENT STATE
HYBRID
```

This distinction prevents administrative deletion from improperly changing historical consumption.

---

# 18. Cumulative resources

Cumulative resources are:

```text
training_runs
calibrations
batch_executions
reports
csv_exports
```

Usage is measured through events recorded in the `license_usage` ledger.

A consumed operation remains historically counted even if a later artifact is removed.

---

# 19. Current-state resources

Current-state resources are:

```text
users
examiners
datasets
models
```

Usage is calculated from current database state, such as active users, available datasets, and registered models.

Legitimate deactivation/removal may alter current usage where the resource rule defines it.

---

# 20. Cases: hybrid quota

`cases` uses hybrid semantics.

Conceptually:

$$
U_{cases}=\max(U_{cumulative}, U_{current})
$$

This prevents creating cases up to the limit, deleting them, and recreating them as an artificial license-bypass mechanism.

---

# 21. Individual comparisons

In the documented release:

```text
individual_comparisons = None
```

Therefore, individual comparisons are unlimited in all three profiles.

If this resource becomes limited in the future, it shall receive an explicit accounting mechanism and compatible migration.

---

# 22. QuotaManager

`QuotaManager` is responsible for:

- obtaining the limit;
- calculating usage according to resource semantics;
- deciding whether the operation is allowed;
- reporting limit, used, requested, and remaining values.

It shall not by itself record cumulative consumption; that is the responsibility of `UsageTracker`.

---

# 23. UsageTracker

`UsageTracker` maintains the cumulative-consumption ledger.

Records include, depending on the event:

```text
license_id
resource
amount
event_uid
user_id
case_id
metadata
```

`event_uid` identifies the consumption event and shall be unique to avoid accidental duplicate accounting.

---

# 24. Atomicity

Quota validation and consumption recording shall be transactional when the operation requires authoritative reservation.

Pattern:

```text
BEGIN IMMEDIATE
  ↓
revalidate quota
  ↓
reserve/create operation
  ↓
record usage ledger
  ↓
COMMIT
```

This reduces race conditions between concurrent operations.

---

# 25. Pre-check does not consume quota

A UI may query availability before starting an operation.

This **pre-check** shall be non-destructive:

```text
check() → reports availability
```

Consumption occurs only at the authoritative point defined for the resource.

---

# 26. Training runs

Each new authorized training run subject to a limit consumes:

```text
training_runs
```

Consumption represents authorization of the run, not the number of epochs executed.

Restarting work as a new training run shall create a new event when application policy classifies it as a new run.

---

# 27. Calibrations

Each new authorized calibration execution subject to a limit consumes:

```text
calibrations
```

Quota does not replace protocol validation:

```text
calibration feature enabled
+
protocol allowed
+
quota available
```

All applicable conditions shall be satisfied.

---

# 28. Batch executions

Batch analysis consumes:

```text
batch_executions
```

A batch execution is different from each individual comparison inside the batch.

Therefore, individual comparisons may be unlimited while the number of batch executions is limited.

---

# 29. Reports

A **new logical report** consumes one unit of:

```text
reports
```

Report reservation, version, and ledger entry shall occur within the same transaction.

---

# 30. Complementary report format

When the same logical report has already been authorized in one format, the complementary format may reuse:

```text
report_uid
version
logical_context_key
```

Example:

```text
existing DOCX
→ generate PDF for same context
→ no additional quota unit
```

This avoids double charging for two formats of the same logical report.

---

# 31. Failure after report reservation

If generation fails **after** an authorized reservation:

- consumed quota is not refunded;
- the record remains traceable;
- status shall indicate failure (`FAILED`) when supported.

This distinguishes consumed authorization from successfully produced artifact.

---

# 32. CSV export

CSV export has two moments:

```text
non-destructive pre-check
  ↓
user selects destination
  ↓
authoritative authorization
  ↓
csv_exports consumption
```

If the user cancels before authorization, no quota is consumed.

Once export authorization is recorded, a later write failure does not automatically refund quota.

---

# 33. Users and examiners

The quotas:

```text
users
examiners
```

are current-state quotas.

`examiners` represents active accounts with the `EXAMINER` role.

Role and authentication policy are canonical in [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 34. Datasets

The:

```text
datasets
```

quota controls the number of datasets available/registered under the current-state rule.

It grants no legal rights over a corpus.

The user remains responsible for obtaining and using each dataset legitimately as defined in [DATASETS-ENG.md](DATASETS-ENG.md).

---

# 35. Models

The:

```text
models
```

quota controls registered/available models in current state.

The commercial release does not need to distribute trained homologation models. An institution may train its own model or import an authorized package.

---

# 36. Quota messages

When an operation is denied, the UI should translate technical state into understandable feedback.

Useful information includes:

```text
resource
used
limit
requested
remaining
```

Messages shall not suggest deleting historical evidence as a way to bypass a limit.

---

# 37. Validity and expiration

The license contains temporal information including:

```text
issued_at
expiration
```

The application validates time together with the secure state defined in [SECURITY-ENG.md](SECURITY-ENG.md).

**Rule:** `TRIAL`, `ACADEMIC`, and `FULL` do not, by definition in this document, have one universal fixed duration.

The term is a property of the issued license.

---

# 38. Installation binding

A license may be bound to:

```text
machine_id
```

derived by the `MachineFingerprint` infrastructure.

This binding reduces unauthorized reuse of one license on another installation.

The technical fingerprint definition belongs to [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 39. License signature

The operational license is authenticated using an asymmetric signature:

```text
issuance     → private key
verification → public key
```

The private key shall remain outside the distributed runtime.

The release contains only required verification material.

The exact cryptographic format accepted by a release is determined by its `LicenseLoader` and corresponding security components; it shall not be inferred from isolated historical scripts.

---

# 40. Historical issuance tools

Earlier project tooling records RSA-2048, public exponent 65537, PKCS#1 v1.5, and SHA-256 for signing canonical JSON payloads.

These records are useful historical provenance but **do not replace the format actually accepted by the release `LicenseLoader`**.

Any algorithm/format change shall be versioned and handled under an explicit migration policy.

---

# 41. Secret material

Never distribute:

- `private_key.pem`;
- issuance secrets;
- operator credentials;
- developer license;
- developer-machine temporal state.

The verification public key may be included in the runtime.

Protection rules are canonical in [SECURITY-ENG.md](SECURITY-ENG.md).

---

# 42. Installation and activation

Operational flow:

```text
install release
  ↓
obtain machine/install identifier when required
  ↓
issue/receive authorized license
  ↓
install license.sfaao
  ↓
start application
  ↓
SecurityBootstrap
  ↓
valid LicenseContext
```

Detailed operational steps belong to [INSTALLATION-ENG.md](INSTALLATION-ENG.md).

---

# 43. Renewal or license change

Renewal/upgrade shall occur through installation of a new valid license issued for the authorized context.

A change may alter:

- expiration;
- profile;
- features;
- protocols;
- limits.

It shall not erase scientific records or rewrite provenance of already completed operations.

Consumption migration across different `license_id` values shall be explicitly defined by commercial policy; it shall not be inferred or silently reset.

---

# 44. No mandatory real-time remote revocation

SFAAO is designed as a predominantly local application and does not depend on a mandatory cloud service for its core operation.

Therefore, this document **does not claim a universal real-time remote revocation mechanism**.

Where revocation, renewal, or replacement is required, it shall follow the authorized procedure for the applicable distribution/license.

---

# 45. Licensing fail closed

Authorization shall fail closed when:

- the license is invalid;
- its signature cannot be verified;
- installation binding fails;
- temporal state is invalid;
- a required feature is disabled;
- a required protocol is not allowed;
- an expected limit is missing;
- a numeric quota has been exceeded.

A configuration error shall not silently become unlimited access.

---

# 46. Operational license is not user authentication

A valid licensed installation still requires human authentication and authorization.

Therefore:

```text
valid LicenseContext
≠
authenticated user
≠
user authorized for operation
```

All applicable layers shall be checked.

---

# 47. Dataset licensing

Licenses for GPDS, CEDAR, BHSig260, UTSig, MCYT, and other datasets are independent from SFAAO.

The product may provide a profile/adapter but:

- does not redistribute the corpus;
- does not grant rights over images;
- does not bypass access control;
- does not replace authorization from the rights holder.

See [DATASETS-ENG.md](DATASETS-ENG.md).

---

# 48. Model licensing

Use of an imported model shall also respect applicable rights and authorization for the package and its source data.

Technical runtime acceptance of a model is not a legal statement that its use is permitted.

---

# 49. Legal software license

SFAAO rights of use and distribution will be expressed by the release legal documents:

```text
LICENSE
LICENSE.pt-BR.md
LICENSE.en.md
```

This [LICENSING-ENG.md](LICENSING-ENG.md) documents **how the technical licensing subsystem works**.

If a disagreement concerns legal rights, the applicable legal terms prevail.

---

# 50. Backup and migration

Backup/restore shall carefully preserve:

- database;
- artifacts;
- valid license when policy permits;
- installation state;
- temporal state.

Blindly copying protected state between machines may invalidate fingerprint/binding.

Concrete procedures belong to [INSTALLATION-ENG.md](INSTALLATION-ENG.md).

---

# 51. Consumption traceability

Quota events are part of operational provenance.

They should answer:

```text
which license?
which resource?
how much?
which event?
which user?
which case?
when?
```

Canonical provenance and auditability definitions belong to [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

---

# 52. Profile update policy

Any change to `TRIAL`, `ACADEMIC`, or `FULL` shall:

1. be versioned;
2. update tests;
3. update this PT-BR/EN document pair;
4. update Manual/README when applicable;
5. preserve interpretation of already-issued licenses or provide explicit migration.

Do not silently change quotas in a maintenance patch.

---

# 53. Release licensing checklist

- [ ] `TRIAL` validated;
- [ ] `ACADEMIC` validated;
- [ ] `FULL` validated;
- [ ] features consistent;
- [ ] protocols consistent;
- [ ] `ACADEMIC` blocks `FORENSIC_CASE`;
- [ ] `FULL` enables `audit_trail`;
- [ ] cumulative quotas validated;
- [ ] current-state quotas validated;
- [ ] hybrid case quota validated;
- [ ] `None` interpreted as unlimited;
- [ ] missing expected limit handled fail-closed;
- [ ] CSV canceled before authorization consumes nothing;
- [ ] complementary report format does not double-consume;
- [ ] report failure after reservation remains `FAILED`;
- [ ] developer license absent from package;
- [ ] private key absent from package;
- [ ] verification public key present when required;
- [ ] secure time/integrity validated;
- [ ] quota messages reviewed.

---

# 54. Summary of normative rules

1. Legal license, runtime license, profile, and dataset license are distinct concepts.
2. Official profiles are `TRIAL`, `ACADEMIC`, and `FULL`.
3. `ACADEMIC` does not authorize `FORENSIC_CASE`.
4. `None` means unlimited; a missing expected field is an error.
5. Cumulative quotas are not reduced by deleting artifacts.
6. Current-state quotas reflect current state.
7. Cases use a hybrid rule.
8. Authoritative consumption shall be transactional when applicable.
9. A pre-check shall not consume quota.
10. Complementary formats of the same logical report shall not consume twice.
11. Failure after authorized reservation does not erase consumption.
12. Expiration is defined by the license, not by profile name.
13. The private key never belongs in the distributed runtime.
14. License changes do not alter historical provenance.
15. The SFAAO license grants no rights over third-party datasets.

---

# 55. References

[1] B. Kaliski, *PKCS #1: RSA Cryptography Specifications Version 2.2*, RFC 8017, IETF, Nov. 2016. https://www.rfc-editor.org/rfc/rfc8017

[2] NIST, *Secure Hash Standard (SHS)*, FIPS PUB 180-4, 2015. doi: 10.6028/NIST.FIPS.180-4.

[3] SFAAO 2.4.0, [SECURITY-ENG.md](SECURITY-ENG.md), canonical runtime-security specification.

[4] SFAAO 2.4.0, [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md), canonical provenance and auditability specification.

[5] SFAAO 2.4.0, [DATASETS-ENG.md](DATASETS-ENG.md), dataset policy and independent third-party rights.

---

# 56. Document stability

This is the canonical technical specification for the SFAAO 2.4.0 licensing subsystem.

Future changes to profile, feature, protocol, quota, signature, activation, or consumption shall be controlled and documented changes.

No commercial change shall silently modify scientific results already produced.
