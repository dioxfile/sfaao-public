# SFAAO 2.4.0 — Application Security and Runtime Protection

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official technical specification  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Objective

This document defines the canonical SFAAO 2.4.0 application- and runtime-level security policy.

Its purpose is to preserve confidentiality, integrity, availability, operational authenticity, and traceability of assets processed by the system without conflating security mechanisms with scientific methodology.

[TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) is canonical for provenance and auditability; [LICENSING-ENG.md](LICENSING-ENG.md) for commercial license rules and profiles; [INSTALLATION-ENG.md](INSTALLATION-ENG.md) for deployment.

---

# 2. Scope

This document covers:

- security bootstrap;
- authentication;
- password storage;
- repeated-attempt protection;
- authorization and roles;
- least privilege;
- filesystem permissions;
- SQLite and auxiliary-file protection;
- licensing as a runtime authorization barrier;
- installation identity;
- protected time state;
- clock-rollback protection;
- integrity verification;
- hashes and manifests;
- key and secret protection;
- report hashes;
- hardening;
- dependencies;
- backups;
- logs/auditing;
- known limitations.

---

# 3. Security objectives

Traditional information-security objectives are:

- **confidentiality** — prevent unauthorized disclosure;
- **integrity** — prevent or detect unauthorized modification;
- **availability** — maintain authorized access when required.

SFAAO also requires:

- user authenticity;
- role-based authorization;
- runtime integrity;
- provenance preservation;
- temporal consistency;
- evidence and artifact protection.

ISO/IEC 27001:2022 specifies requirements for information security management systems and ISO/IEC 27002:2022 provides guidance on controls including access, cryptography, and secure development [1], [2].

---

# 4. Threat model

SFAAO security considers, among others, risks from:

- unauthorized local access;
- password guessing;
- misuse of administrative privileges;
- database or artifact modification;
- removal/replacement of protected files;
- license tampering;
- copying a license to an unauthorized machine;
- artificial clock rollback;
- runtime modification;
- accidental inclusion of secrets in a release;
- exposure of cases, signatures, or datasets;
- compromised dependencies.

This document does not claim absolute resistance against an attacker with complete administrative control of the operating system, hardware, or execution environment.

---

# 5. Defense in depth


### Historical protection inventory (not universal across platforms)

- One documented Linux build used **PyArmor 9.2.6 Pro**; do not infer this version for the Windows or RPM packages;
- runtime `pyarmor_runtime_015424`;
- historical Linux payload inventory: 184 files after adding the manifest (verify against the package before citing);
- historical manifest for that payload: 183 covered files (the manifest itself is excluded);
- distributed public key at `security/crypto/public_key.pem`;
- no private key and no `license_generator.py` in the client.

Canonical public-key SHA-256:

```text
349bcae57cdb6a63f5a5ddbee53962a9b4d03f693995452d91861f7fc3845c5e
```

Historical SHA-256 of the internal Linux payload manifest recorded during build (not the verified release-manifest SHA-256):

```text
f967766e7b84f877ea46942224b8802d8a60afde8eb5257af9cc522d2000772f
```

The PyArmor license provides Pro/BCC/RFT capability; documentation does not claim that BCC or RFT are enabled in a payload unless build options explicitly enable and validate them.

**Per-platform record:** the Windows homologation history includes PyArmor Pro 9.2.4 and a 192-entry integrity audit. The inventory above describes a Linux build stage and is not a universal file count. Verified installer hashes are listed in [INSTALLATION-ENG.md](INSTALLATION-ENG.md).

SFAAO uses complementary layers:

```text
OPERATING-SYSTEM PERMISSIONS
        ↓
SECURITY BOOTSTRAP
        ↓
LICENSE / INSTALLATION IDENTITY
        ↓
PROTECTED TIME STATE / TIME GUARD
        ↓
RUNTIME INTEGRITY
        ↓
AUTHENTICATION
        ↓
ROLE-BASED AUTHORIZATION
        ↓
APPLICATION RULES
        ↓
TRACEABILITY / AUDITING
```

No individual layer shall be interpreted as complete protection.

---

# 6. SecurityBootstrap

`SecurityBootstrap` centralizes internal security checks before normal application use. **It runs in the background, with no dedicated screen or user-facing bootstrap window.**

The validated flow includes:

```text
1. protect storage/runtime
2. validate license
3. generate/resolve machine identity
4. validate protected time state
5. execute TimeGuard
6. verify integrity against manifest
7. create SecurityContext
```

Failure of a critical stage shall prevent creation of a valid security context.

---

# 7. Fail closed

**Fail closed** means that when a critical check cannot confirm an authorized state, protected access is denied rather than allowed permissively.

In SFAAO:

```text
invalid license      → invalid bootstrap
invalid secure time  → invalid bootstrap
invalid time guard   → invalid bootstrap
invalid integrity    → invalid bootstrap
```

Errors should identify the stage without exposing secrets or cryptographic material.

---

# 8. Authentication

Authentication is the process of verifying a user's claimed identity.

SFAAO uses local authentication with:

```text
username + password
```

No default administrative credentials are distributed. On an installation with no users, the first administrator is explicitly created through the dedicated flow and subsequent access uses the login window.

---

# 9. Password storage

Passwords are not stored in plaintext or reversible form.

The SFAAO 2.4.0 implementation uses:

```text
PBKDF2-HMAC-SHA-256
random salt: 16 bytes
iterations: 600,000
```

The persisted representation includes the algorithm identifier, work factor, salt, and digest.

PBKDF2 is a password-based key-derivation function defined in the PKCS #5/RFC 8018 family [3]. Salting and a cost factor make offline guessing attacks more expensive.

---

# 10. Conceptual PBKDF2 equation

In simplified form:

$$
DK = PBKDF2(PRF, P, S, c, dkLen)
$$

where:

- $P$ is the password;
- $S$ is the salt;
- $c$ is the iteration count;
- `PRF` is an HMAC-based pseudorandom function;
- `DK` is the derived key/digest.

In SFAAO, the validated path uses HMAC-SHA-256 and $c = 600000$.

---

# 11. Salt

A **salt** is a random value individually associated with a password hash.

Purposes include:

- preventing equal passwords from having equal stored hashes;
- hindering precomputed tables;
- increasing the cost of bulk attacks.

SFAAO generates 16 random bytes per password in the validated path, exceeding the 32-bit minimum specified by NIST SP 800-63B-4 [4].

A salt is not secret and may be stored with the password hash.

---

# 12. Constant-time comparison

Password verification uses an appropriate digest comparison (`hmac.compare_digest`) instead of a naive content-dependent byte-by-byte comparison.

This reduces timing differences directly controlled by the location of the first differing byte.

---

# 13. Progressive rehash

The password format stores algorithm and iteration count.

When a valid authentication encounters a hash with parameters below the current policy, the repository can rehash it using the current work factor.

This supports cost evolution without a destructive one-time migration of all passwords.

---

# 14. Enumeration resistance

The authentication path performs dummy work when needed to reduce trivial differences between:

```text
unknown user
known user + invalid password
```

The UI presents generic invalid-credential feedback and shall not reveal whether a specific username exists.

---

# 15. Rate limiting and lockout

The implemented and tested behavior records:

```text
MAX_FAILED_ATTEMPTS = 5
LOCKOUT_SECONDS = 60
```

After the failed-attempt limit, the account is temporarily locked. Once the period expires, the state may be normalized.

`failed_login_attempts` and `locked_until` are part of schema hardening.

NIST SP 800-63B-4 recommends limiting authentication attempts to reduce online guessing [4].

---

# 16. Future password policy

Strong password **storage** does not replace appropriate password **selection** policy.

NIST SP 800-63B-4, published in July 2025, recommends for a password used as a single factor:

- a minimum of 15 characters;
- comparison against a blocklist of common/expected/compromised passwords;
- no artificial composition rules;
- salted and hashed storage with a cost factor [4].

These requirements should be considered in the next functional hardening cycle. This document does not claim that all of them are already enforced by the frozen 2.x GUI.

---

# 17. PBKDF2 and the OWASP reference

The OWASP Password Storage Cheat Sheet currently lists **600,000 or more** as the PBKDF2-HMAC-SHA-256 work factor, particularly for environments requiring FIPS-compatible choices [5].

The validated SFAAO value matches that reference as of this document date.

This does not constitute FIPS or OWASP certification.

---

# 18. Authorization

Authorization determines **what an authenticated user is allowed to do**.

SFAAO separates authentication from authorization:

```text
valid credential
≠
permission for every operation
```

Relevant operations shall verify role/capability and shall not rely solely on visually hidden buttons.

---

# 19. RBAC

SFAAO uses **Role-Based Access Control (RBAC)**.

In RBAC, permissions are assigned to roles and users receive those roles instead of maintaining arbitrary per-user permissions [6].

Validated roles are:

```text
ADMIN
EXAMINER
```

---

# 20. ADMIN role

`ADMIN` is intended for application governance.

It includes, according to validated operations:

- user administration;
- dataset governance;
- model governance;
- training;
- model activation;
- administrative operations allowed by the license.

The role does not automatically authorize every operating-system action inside the application.

---

# 21. EXAMINER role

`EXAMINER` is intended for forensic operation:

- cases;
- signatures;
- authorized calibration;
- comparison;
- results;
- reports.

Administrative menus shall not be made available to this role.

---

# 22. Least privilege

The **least privilege** principle states that users and processes receive only the minimum resources and authorizations required to perform their functions [7].

SFAAO application:

```text
EXAMINER → forensic operation
ADMIN    → required governance
```

Separation reduces the impact of human error or compromise of a non-administrative account.

---

# 23. First administrator

On a new installation:

```text
0 users
  ↓
FirstAdminWindow
  ↓
explicit ADMIN creation
  ↓
standard login
```

The release shall not contain a default username/password.

Initial creation shall use the same password hashing policy as other users.

---

# 24. User deactivation

Disabled accounts shall not authenticate.

Relevant administrative changes shall use authorized flows and remain compatible with [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

Physical deletion of history shall not be used as a substitute for deactivation when dependencies exist.

---

# 25. POSIX storage protection

The 2.4.0 runtime applies a restrictive policy on POSIX systems, including:

```text
umask 077
private directories: 700
private files:       600
```

Semantics:

- `700`: owner-only read/write/execute on the directory;
- `600`: owner-only read/write on the file.

This reduces accidental local exposure to other users of the same host.

---

# 26. SQLite, WAL, and SHM

Protection shall cover not only the main SQLite file but also auxiliary files that may contain database content, such as:

```text
*.db
*.db-wal
*.db-shm
```

Inconsistent permissions on these files may defeat protection of the primary database.

---

# 27. Windows ACLs

SFAAO has a Windows storage-protection path, but ACLs depend on the actual environment, execution identity, filesystem, and installer.

**Release rule:** the final Windows installation shall validate the ACLs actually applied.

The presence of ACL code alone is not proof of correct configuration on every machine.

---

# 28. Paths and isolation

Paths passed to the scientific core shall have been authorized by the application layer.

The GUI shall not grant unrestricted filesystem access merely because it can select files.

Profiles, configs, and releases shall not contain developer-private paths.

---

# 29. Local-first and network surface

SFAAO is predominantly a local desktop application.

Case management, persistence, inference with available models, calibration with local data, comparison, and report generation do not require a mandatory cloud API.

This reduces one class of remote attack surface but does not eliminate local, supply-chain, or update risks.

---

# 30. Pretrained weights

When `pretrained=True`, frameworks such as `torchvision` may attempt to retrieve external weights if the checkpoint is not cached.

Such access:

- shall not be confused with SFAAO telemetry;
- should use sources configured by the library;
- shall be documented for offline environments;
- may require authorized pre-loading of dependencies.

---

# 31. License security

A license is a commercial/runtime authorization credential, not a user credential.

`LicenseService` is responsible for:

- loading the license;
- validating its signature;
- validating installation binding;
- validating temporal state;
- exposing features/profiles/limits to the context.

Commercial semantics and license profiles are defined in [LICENSING-ENG.md](LICENSING-ENG.md).

---

# 32. Asymmetric license signing

The licensing infrastructure separates:

```text
private key → issuance/signing
public key  → runtime verification
```

The public key may be distributed with the runtime because possession of it does not permit issuing new licenses.

The issuing private key is a secret and **shall not be distributed in the release**.

Concrete cryptographic implementation and evolution are documented in [LICENSING-ENG.md](LICENSING-ENG.md).

---

# 33. Local license

The default operational path may use:

```text
storage/license.sfaao
```

The developer-machine license file **is not part of the release package**.

Each installation shall receive a license matching its authorized context.

---

# 34. Machine fingerprint

`MachineFingerprint` produces an installation identifier used by the licensing subsystem.

The fingerprint:

- is not a password;
- is not a private key;
- shall not be treated as a human authenticator;
- provides logical binding between license/state and installation.

Implementation changes shall explicitly preserve compatibility policy for legacy licenses.

---

# 35. Protected time state

SFAAO maintains temporal state so validity/licensing is not based solely on a naive reading of the current clock.

Conceptually:

```text
machine_id
timestamp/last_seen
previous state
state integrity
```

On valid first use, initial state is created in UTC.

---

# 36. TimeGuard

`TimeGuard` detects inconsistent clock rollback.

Objective:

```text
previously observed time
        ↓
anomalous rollback?
        ├─ no  → continue
        └─ yes → block protected context
```

This mechanism protects against simple time tampering; it does not replace a trusted external time service, TPM, or secure hardware.

---

# 37. IntegrityChecker

Bootstrap verifies the protected base using an integrity manifest.

Flow:

```text
expected manifest
  ↓
calculate/verify protected files
  ↓
modified?
missing?
  ↓
fail if state is invalid
```

Verification shall occur before normal security context creation.

---

# 38. Cryptographic hash

A cryptographic hash function produces a digest:

$$
h = H(m)
$$

where $m$ is the content and $h$ is the digest.

SFAAO operational protection and manifests use **SHA-256** for observed artifacts and reports.

SHA-256 is part of the Secure Hash Algorithm family specified by FIPS 180-4 [8].

Hashing does not provide confidentiality.

---

# 39. Scientific-artifact manifests

Calibration artifacts may have a `manifest.json` containing:

```text
manifest_version
calibration_uid
generated_at
artifact_type
artifact_version
files[]
    name
    sha256
```

Its purpose is to detect change and bind integrity to the correct artifact.

Manifest provenance is defined in [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md).

---

# 40. Report hashes

The traceable reporting pipeline computes SHA-256 for DOCX/PDF artifacts and may persist their digests with report metadata.

This allows later verification that the delivered file still matches the registered artifact.

A hash does not automatically make the report a qualified digitally signed document.

---

# 41. SHA-512 used for INPI registration

The software-registration certificate/filing uses a **SHA-512** digest of material submitted to INPI.

That SHA-512 serves to identify registered material and shall not be confused with:

- PBKDF2-HMAC-SHA-256 for passwords;
- SHA-256 runtime manifests;
- SHA-256 report artifacts.

Different purposes may use different algorithms without contradiction.

---

# 42. Secrets and prohibited release material

The release shall not contain:

- licensing private key;
- credentials;
- tokens;
- passwords;
- developer-machine license;
- developer-machine temporal state;
- development databases;
- real cases/signatures;
- private backups;
- confidential internal IP documents.

Exclusion shall be verified before building the final release package.

---

# 43. Backups

Backups may contain the same sensitivity as the active environment.

Therefore they shall:

- be access controlled;
- not enter releases accidentally;
- preserve database/filesystem consistency;
- follow institutional retention and disposal policy.

Backup/restore procedures belong to [INSTALLATION-ENG.md](INSTALLATION-ENG.md).

---

# 44. Logs and error messages

Logs shall not record:

- passwords;
- private keys;
- secret tokens;
- unnecessary credential contents.

Authentication messages shall resist username enumeration.

Scientific errors may be detailed for diagnosis but shall respect privacy and avoid exposing secrets/paths in public contexts.

---

# 45. Dependencies and supply chain

Third-party libraries are part of the security surface.

A release shall:

- pin/record dependencies in `requirements.txt`;
- review updates before adoption;
- remove unused dependencies;
- obtain dependencies from legitimate sources;
- run tests after updates;
- avoid arbitrary automatic package installation at runtime.

NIST SSDF recommends integrating secure development practices throughout the software lifecycle [9].

---

# 46. Future hardening

Without changing the frozen release without analysis, the following improvements should remain in the security backlog:

- password policy fully aligned with NIST SP 800-63B-4;
- compromised/common-password blocklist;
- MFA evaluation for privileged functions;
- additional automated authorization tests;
- Windows ACL validation in the final installer;
- periodic password work-factor review;
- periodic dependency analysis;
- formal vulnerability-response procedures.

These are hardening improvements, not statements that existing scientific results are invalid.

---

# 47. Secure development

Security changes shall follow:

```text
requirement
→ isolated implementation
→ tests
→ review
→ documentation update
→ changelog
→ new manifest/release
```

Security checks shall not be disabled merely to bypass an operational error without diagnosing the cause.

---

# 48. Integrity incident

When bootstrap reports a `modified` or `missing` file:

1. do not automatically assume an attack;
2. stop protected operation;
3. preserve state for diagnosis;
4. compare with the authorized release/manifest;
5. identify legitimate update or unauthorized change;
6. restore only from a trusted source;
7. generate a new manifest only as part of an authorized release.

---

# 49. Limitations

SFAAO security also depends on:

- operating-system security;
- physical workstation control;
- local malware;
- administrative privileges;
- backup protection;
- private-key protection outside the runtime;
- institutional policy;
- dependency maintenance.

The software cannot guarantee confidentiality against a host administrator with unrestricted access to plaintext files while the application uses them.

---

# 50. Relationship with TRACEABILITY.md

[TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) canonically defines:

- provenance;
- auditability;
- entity/activity/agent relationships;
- hashes as integrity evidence;
- execution reconstruction.

[SECURITY-ENG.md](SECURITY-ENG.md) defines mechanisms that protect the runtime and those records.

---

# 51. Relationship with LICENSING.md

[LICENSING-ENG.md](LICENSING-ENG.md) is canonical for:

- license types/profiles;
- features;
- enabled protocols;
- limits/quotas;
- issuance;
- activation;
- validity;
- commercial semantics.

This document addresses only the security boundary: verification, binding, public/private keys, and protected state.

---

# 52. Relationship with INSTALLATION.md

[INSTALLATION-ENG.md](INSTALLATION-ENG.md) shall specify:

- required permissions;
- first ADMIN creation;
- license placement;
- bootstrap validation;
- offline/pretrained setup;
- backup/restore;
- post-installation checks.

It shall not duplicate the canonical cryptographic definitions of this document.

---

# 53. Release security checklist

- [ ] no default credentials;
- [ ] PBKDF2-HMAC-SHA-256 operational;
- [ ] random salts;
- [ ] lockout operational;
- [ ] ADMIN/EXAMINER validated;
- [ ] EXAMINER without administrative governance;
- [ ] storage protected;
- [ ] DB/WAL/SHM protected;
- [ ] developer license absent;
- [ ] private key absent;
- [ ] developer temporal state absent;
- [ ] final manifest matches release;
- [ ] no modified/missing file in integrity check;
- [ ] no real cases;
- [ ] no third-party datasets;
- [ ] no backups/caches;
- [ ] requirements reviewed;
- [ ] artifact hashes present when applicable;
- [ ] Windows ACL validated on the target environment when distributed for Windows.

---

# 54. Summary of normative rules

1. Passwords are never stored in plaintext.
2. Authentication and authorization are separate responsibilities.
3. ADMIN and EXAMINER shall follow least privilege.
4. Do not distribute default credentials.
5. Critical bootstrap failure shall block protected context.
6. The licensing private key never belongs in the distributed runtime.
7. The developer license never belongs in the release.
8. Secure time and TimeGuard shall not be silently bypassed.
9. The integrity manifest shall match the authorized release.
10. Operational SHA-256 and INPI SHA-512 have different purposes.
11. Logs shall not contain secrets.
12. Backups shall receive protection appropriate to data sensitivity.
13. Security changes require testing, documentation, and a new release/manifest.
14. Do not claim standards compliance/certification without corresponding validation.

---

# 55. References

[1] ISO/IEC 27001:2022, *Information security, cybersecurity and privacy protection — Information security management systems — Requirements*. ISO/IEC, 2022.

[2] ISO/IEC 27002:2022, *Information security, cybersecurity and privacy protection — Information security controls*. ISO/IEC, 2022.

[3] K. Moriarty, B. Kaliski, and A. Rusch, *PKCS #5: Password-Based Cryptography Specification Version 2.1*, RFC 8018, IETF, Jan. 2017. https://www.rfc-editor.org/rfc/rfc8018

[4] D. Temoshok et al., *Digital Identity Guidelines: Authentication and Authenticator Management*, NIST SP 800-63B-4, July 2025. doi: 10.6028/NIST.SP.800-63b-4.

[5] OWASP Foundation, *Password Storage Cheat Sheet*, Cheat Sheet Series. Accessed Sep. 30, 2026. https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html

[6] D. F. Ferraiolo, J. A. Cugini, and D. R. Kuhn, “Role-Based Access Control (RBAC): Features and Motivations,” *11th Annual Computer Security Applications Conference*, 1995.

[7] NIST, “Least Privilege,” *Computer Security Resource Center Glossary*, sources including NIST SP 800-53 Rev. 5.

[8] NIST, *Secure Hash Standard (SHS)*, FIPS PUB 180-4, 2015. doi: 10.6028/NIST.FIPS.180-4.

[9] M. Souppaya, K. Scarfone, and D. Dodson, *Secure Software Development Framework (SSDF) Version 1.1*, NIST SP 800-218, 2022. doi: 10.6028/NIST.SP.800-218.

---

# 56. Document stability

This is the canonical application-security definition for SFAAO 2.4.0.

Future changes shall explicitly distinguish:

```text
validated release behavior
normative requirement
future hardening recommendation
```

A security improvement shall not erase traceability of previous releases or silently change the interpretation of scientific results already produced.
