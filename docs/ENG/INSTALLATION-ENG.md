# SFAAO 2.4.0 — Homologated Installation and Distribution

**Documentation version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Release 2.4.0 delivery documentation  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

## 1. Purpose and scope

This document covers the **completed, homologated installers** for **Windows 11, Linux Mint, and Red Hat**. SFAAO is distributed as a protected application; customers do not assemble a Python development source tree. The installers are collected under `SFAAO_DISTRIBUTION_2.4.0/` and have been checked using SHA-256. Reported product homologation is not independent certification or proof of performance in every forensic context.

## 2. Distribution artifacts and integrity

| Platform | File under `SFAAO_DISTRIBUTION_2.4.0/` | SHA-256 |
|---|---|---|
| Windows 11 | `03_WINDOWS_EXE/SFAAO_Setup_2.4.0_CPU_FINAL_PRO_LAUNCHER_RC1.exe` | `5de43c5dba6559e55442e4c25c6ba13303c95561aef09d2630059b160f78c66d` |
| Linux Mint / Debian / Ubuntu (amd64) | `01_DEB/sfaao_2.4.0_amd64.deb` | `5e7c730ed0cdd59426d896f31dc8a277ef897fba104aa38511cec0e3abc522a5` |
| Red Hat and compatible RPM distributions (x86_64) | `02_RPM/sfaao-2.4.0-1.x86_64.rpm` | `d5ead5e0fe03e2fb10fef602afc93200ff568398cda20745dc237ebfb8de4d4e` |

All three hashes are recorded in the official **`06_CHECKSUMS/SHA256SUMS`** manifest and were verified using `sha256sum -c`. The `RC1` suffix is part of the **actual homologated Windows installer filename** and must not be removed without updating the release records. To verify all packages from the distribution root:

```bash
sha256sum -c 06_CHECKSUMS/SHA256SUMS
```

A matching hash confirms byte-for-byte identity with a trusted manifest; it does **not** replace provenance/authenticity verification, code-signing policy, or malware controls.

## 3. Release security contents

Customers receive the launcher, protected application resources, public license-verification components, and the necessary operational structure. Do **not** distribute the manufacturer's private key, administrative license issuer (`license_generator.py`), credentials, real forensic cases, development databases, real signatures, third-party datasets, or internal validation models. `SecurityBootstrap` performs internal security checks **in the background, with no dedicated screen or bootstrap window**. Critical failures block protected operation.

Anti-reverse-engineering protection increases technical effort; it does not make reverse engineering impossible. The operational license is distinct from the legal software license and third-party dataset rights.

## 4. Windows 11 — installation and operation

1. Verify the distributed `.exe` using a trusted SHA-256 tool.
2. Run `SFAAO_Setup_2.4.0_CPU_FINAL_PRO_LAUNCHER_RC1.exe` in accordance with institutional policies.
3. Launch SFAAO using the installed shortcut/launcher, not internal development scripts.
4. Request and install a valid license when prompted using the provided customer utilities.
5. On first use of an operational installation with no accounts, create the initial administrator. **There are no default administrator credentials.**

Operational storage must remain separate from the protected application base. During updates/uninstallation, preserve forensic data under institutional policy. **DOCX and PDF reporting have been homologated on Windows 11**, as confirmed by the release team.

## 5. Linux Mint / Red Hat — prerequisites

The `.deb` and `.rpm` packages include SFAAO's **protected OCI image**, but still require an operational **Podman or Docker** installation. The current graphical launcher requires an available **X11** session; it forwards `DISPLAY` and the X11 socket according to host policy. A bundled container image does not remove the need for a supported host system, container runtime, or appropriate SELinux/permission configuration where relevant.

The Linux `/usr/bin/sfaao` launcher uses the image `localhost/sfaao-linux:2.4.0-cpu`. The Podman release image inspected during homologation had OCI ID `06fe901499d23db24b4c9027e7feac30531139fd2828db1d013a27cbbe8fc479`. The TAR image embedded in both DEB and RPM has **SHA-256 `e2d04f319fb57ff712af26deb5efe666bb2763d26495f287e53bd9c029dba362`**. **The OCI image ID and TAR-file hash are different identifiers.**

Example installation commands, subject to local administrator and institutional policies:

```bash
# Debian / Ubuntu / Linux Mint — from the distribution root
sudo apt install ./01_DEB/sfaao_2.4.0_amd64.deb

# Red Hat — from the distribution root
sudo dnf install ./02_RPM/sfaao-2.4.0-1.x86_64.rpm
```

Before running `sfaao`, ensure Podman or Docker is installed/configured for the user. A bundled installation helper may assist with host configuration; inspect it before running. X11 access, DNS, and SELinux labeling are host-specific.

## 6. Linux data directories, licensing, and shortcuts

On Linux Mint, the primary installation's operational data normally live under `~/.local/share/SFAAO/runtime/`:

```text
runtime/storage              cases, database, operational license
runtime/models/default       authorized default models
runtime/models/custom        authorized custom models
runtime/security_state       operational integrity manifest/security state
runtime/home                 persistent container-user home
runtime/data/datasets        user-imported datasets
runtime/data/images          user-imported images
runtime/data/models          user-imported models
```

The **`/usr/bin/sfaao`** launcher creates `~/SFAAO_Data`, pointing to `~/.local/share/SFAAO/runtime/data`, with `datasets`, `images`, and `models`. Direct execution of `/opt/sfaao/run-sfaao.sh` **does not create that shortcut**; this matters during isolated tests. The `SFAAO_ROOT` variable can select a separate test storage location; do not redirect production data shortcuts or copy real evidence into tests without authorization.

Linux customer commands included in the package: `sfaao`, `sfaao-license-request`, and `sfaao-license-install`. The operational license is outside the container image and is checked by the runtime. A valid license does not replace account authentication.

## 7. Startup and security

Conceptual flow:

```text
launcher → package integrity checks → container runtime (Linux)
         → internal/background SecurityBootstrap (no screen)
         → license, machine identity, protected time, integrity checks
         → first administrator (if no account exists) or login
```

The exact internal sequence depends on runtime implementation. Do not describe the bootstrap as a graphical interface or a user-click step. A critical check failure may prevent startup.

## 8. Training, comparison, and reports

The product supports training, scientific and forensic calibration, individual LORO/A4 comparison, pairwise-score inspection, batch comparison, and context refresh. Availability depends on authorization, license, appropriate data, selected model, and calibration. Calibration and inference must remain aligned in the A(N−1) domain, as explained in [CALIBRATION-ENG.md](CALIBRATION-ENG.md).

Both report formats, **DOCX and PDF**, have been homologated on **Windows 11, Linux Mint, and Red Hat**, as confirmed by the release owner. A PDF screenshot must not be described as a DOCX screenshot, or vice versa. Issuing a report does not replace review by the responsible handwriting examiner.

## 9. Data protection, backup, and updates

Operational storage must remain separate from protected program files. The installer does not grant redistribution rights over GPDS, CEDAR, BHSig260, MCYT, UTSig, or other third-party datasets. Models must be legitimately obtained, trained, or imported, with provenance recorded.

Before updates, migration, or uninstallation, make **consistent backups** of SQLite databases (considering WAL/SHM files or the Online Backup API), cases, models, calibrations, reports, and relevant metadata. Changing machines may require license re-issuance or rebinding. Do not manually mix protected resources from different versions. **Uninstalling the program does not authorize automatic destruction of forensic evidence or customer data.**

## 10. Homologation record and documentary limitations

| Item | Documented status |
|---|---|
| Windows 11 installer | Homologated according to tests reported by the release team |
| Linux Mint / DEB installer | Homologated according to tests reported by the release team |
| Red Hat / RPM installer | Homologated according to tests reported by the release team |
| DOCX / PDF reporting | Homologated on all three platforms |
| DEB/RPM package structure, TAR image, and SHA-256 | Verified at distribution close-out |
| Three-installer `SHA256SUMS` manifest | Verified with `OK` for all three artifacts |

This table does not replace an institutional **homologation evidence dossier**. Exact Red Hat version, dates, and individual test records should be archived when required. Do not infer, from overall homologation alone, that a particular scientific experiment, migration, ACL policy, or individual test was executed unless separately documented.

## 11. Related documentation

- [TUTORIAL-ENG.md](TUTORIAL-ENG.md): operations, contexts, and four comparison-window actions.
- [LICENSING-ENG.md](LICENSING-ENG.md): license requests, installation, profiles, limits.
- [SECURITY-ENG.md](SECURITY-ENG.md): internal security checks and policies.
- [DATASETS-ENG.md](DATASETS-ENG.md): profiles and data governance.
- [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md): provenance and auditability.

**Delivery rule:** the end user receives a homologated installer, not a Python project to compile; the manufacturer's administrative tools and secrets remain outside the customer distribution.
