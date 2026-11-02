# SFAAO 2.4.0 — Scientific, Normative, and Technical References

**Document version:** 2.4  
**Language:** EN  
**Project:** SFAAO — Software Forense para Análise de Assinaturas Offline (Forensic Software for Offline Signature Analysis)  
**Status:** Official  
**Documentation revision:** 2026-10-29  
**Previous document baseline:** 2026-09-30

---

# 1. Purpose

This document centralizes scientific, normative, and technical references used by the official SFAAO 2.4.0 documentation.

It **does not replace citations at the point of definition**. Each technical document shall keep a reference next to the concept it supports; this file standardizes metadata, DOI/URLs, status, and scope of use.

---

# 2. Central reference catalog

| ID | Reference | SFAAO use | Source |
|---|---|---|---|
| **R01** | LeCun, Y.; Bengio, Y.; Hinton, G. “Deep learning.” Nature, 521, 436–444, 2015. DOI: 10.1038/nature14539. | Deep learning; hierarchical representation. | [official/DOI](https://doi.org/10.1038/nature14539) |
| **R02** | Bromley, J.; Guyon, I.; LeCun, Y.; Säckinger, E.; Shah, R. “Signature Verification using a ‘Siamese’ Time Delay Neural Network.” Advances in Neural Information Processing Systems 6, 1993/1994. | Siamese networks and signature verification. | [official/DOI](https://papers.nips.cc/paper/1993/hash/288cc0ff022877bd3df94bc9360b9c5d-Abstract.html) |
| **R03** | Hadsell, R.; Chopra, S.; LeCun, Y. “Dimensionality Reduction by Learning an Invariant Mapping.” CVPR, pp. 1735–1742, 2006. DOI: 10.1109/CVPR.2006.100. | Contrastive Loss; metric learning. | [official/DOI](https://doi.org/10.1109/CVPR.2006.100) |
| **R04** | Schroff, F.; Kalenichenko, D.; Philbin, J. “FaceNet: A Unified Embedding for Face Recognition and Clustering.” CVPR, pp. 815–823, 2015. | Embeddings and Triplet Loss. | [official/DOI](https://openaccess.thecvf.com/content_cvpr_2015/html/Schroff_FaceNet_A_Unified_2015_CVPR_paper.html) |
| **R05** | Deng, J.; Guo, J.; Xue, N.; Zafeiriou, S. “ArcFace: Additive Angular Margin Loss for Deep Face Recognition.” CVPR, 2019. DOI: 10.1109/CVPR.2019.00482. | ArcFace and angular margins. | [official/DOI](https://doi.org/10.1109/CVPR.2019.00482) |
| **R06** | He, K.; Zhang, X.; Ren, S.; Sun, J. “Deep Residual Learning for Image Recognition.” CVPR, pp. 770–778, 2016. DOI: 10.1109/CVPR.2016.90. | ResNet18/34/50; residual connections. | [official/DOI](https://doi.org/10.1109/CVPR.2016.90) |
| **R07** | Tan, M.; Le, Q. V. “EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks.” ICML, PMLR 97:6105–6114, 2019. | EfficientNet-B0; CNN scaling. | [official/DOI](https://proceedings.mlr.press/v97/tan19a.html) |
| **R08** | Howard, A. et al. “Searching for MobileNetV3.” ICCV, pp. 1314–1324, 2019. | MobileNetV3. | [official/DOI](https://openaccess.thecvf.com/content_ICCV_2019/html/Howard_Searching_for_MobileNetV3_ICCV_2019_paper.html) |
| **R09** | Liu, Z.; Mao, H.; Wu, C.-Y.; Feichtenhofer, C.; Darrell, T.; Xie, S. “A ConvNet for the 2020s.” CVPR, pp. 11976–11986, 2022. | ConvNeXt/ConvNeXt-Tiny. | [official/DOI](https://openaccess.thecvf.com/content/CVPR2022/html/Liu_A_ConvNet_for_the_2020s_CVPR_2022_paper.html) |
| **R10** | Srivastava, N.; Hinton, G.; Krizhevsky, A.; Sutskever, I.; Salakhutdinov, R. “Dropout: A Simple Way to Prevent Neural Networks from Overfitting.” Journal of Machine Learning Research, 15:1929–1958, 2014. | Dropout and regularization. | [official/DOI](https://www.jmlr.org/papers/v15/srivastava14a.html) |
| **R11** | Kingma, D. P.; Ba, J. “Adam: A Method for Stochastic Optimization.” International Conference on Learning Representations, 2015. arXiv:1412.6980. | Adam optimizer. | [official/DOI](https://arxiv.org/abs/1412.6980) |
| **R12** | Loshchilov, I.; Hutter, F. “Decoupled Weight Decay Regularization.” International Conference on Learning Representations, 2019. | AdamW and decoupled weight decay. | [official/DOI](https://iclr.cc/virtual/2019/poster/935) |
| **R13** | Loshchilov, I.; Hutter, F. “SGDR: Stochastic Gradient Descent with Warm Restarts.” International Conference on Learning Representations, 2017. | Cosine annealing/scheduling. | [official/DOI](https://arxiv.org/abs/1608.03983) |
| **R14** | Prechelt, L. “Early Stopping — but when?” In: Orr, G. B.; Müller, K.-R. (eds.), Neural Networks: Tricks of the Trade, LNCS 1524, pp. 55–69, Springer, 1998. | Early stopping. | [official/DOI](https://publikationen.bibliothek.kit.edu/7498) |
| **R15** | Pan, S. J.; Yang, Q. “A Survey on Transfer Learning.” IEEE Transactions on Knowledge and Data Engineering, 22(10):1345–1359, 2010. DOI: 10.1109/TKDE.2009.191. | Transfer learning; pretrained/fine-tuning. | [official/DOI](https://doi.org/10.1109/TKDE.2009.191) |
| **R16** | Bellet, A.; Habrard, A.; Sebban, M. “A Survey on Metric Learning for Feature Vectors and Structured Data.” CoRR abs/1306.6709, 2013. | Metric learning, distances, and similarities. | [official/DOI](https://arxiv.org/abs/1306.6709) |
| **R17** | Otsu, N. “A Threshold Selection Method from Gray-Level Histograms.” IEEE Transactions on Systems, Man, and Cybernetics, 9(1):62–66, 1979. DOI: 10.1109/TSMC.1979.4310076. | Otsu binarization in preprocessing. | [official/DOI](https://doi.org/10.1109/TSMC.1979.4310076) |
| **R18** | Plamondon, R.; Lorette, G. “Automatic signature verification and writer identification — the state of the art.” Pattern Recognition, 22(2):107–131, 1989. DOI: 10.1016/0031-3203(89)90059-9. | Foundations of automatic signature verification. | [official/DOI](https://doi.org/10.1016/0031-3203(89)90059-9) |
| **R19** | Jain, A. K.; Flynn, P.; Ross, A. A. (eds.). Handbook of Biometrics. Springer, 2008. | Biometrics; terminology and evaluation. | [official/DOI](https://link.springer.com/book/10.1007/978-0-387-71041-9) |
| **R20** | ISO/IEC 19795-1:2021. Information technology — Biometric performance testing and reporting — Part 1: Principles and framework. Edition 2, May 2021. | Biometric performance testing; metrics and protocols. | [official/DOI](https://www.iso.org/standard/73515.html) |
| **R21** | Moreno-Torres, J. G.; Raeder, T.; Alaiz-Rodríguez, R.; Chawla, N. V.; Herrera, F. “A unifying view on dataset shift in classification.” Pattern Recognition, 45(1):521–530, 2012. DOI: 10.1016/j.patcog.2011.06.019. | Dataset shift. | [official/DOI](https://doi.org/10.1016/j.patcog.2011.06.019) |
| **R22** | Ferrer, M. A.; Diaz-Cabrera, M.; Morales, A. “Static Signature Synthesis: A Neuromotor Inspired Approach for Biometrics.” IEEE Transactions on Pattern Analysis and Machine Intelligence, 37(3):667–680, 2015. DOI: 10.1109/TPAMI.2014.2343981. | GPDSsyntheticSignature; signature synthesis. | [official/DOI](https://doi.org/10.1109/TPAMI.2014.2343981) |
| **R23** | GPDS — Grupo de Procesado Digital de Señales, Universidad de Las Palmas de Gran Canaria. “Toolbox & Databases — GPDSsyntheticSignature database.” | Official source for the GPDSsyntheticSignature corpus and its distribution characteristics. | [official/DOI](https://gpds.ulpgc.es/downloadnew/download.htm) |
| **R24** | Kalera, M. K.; Srihari, S.; Xu, A. “Offline signature verification and identification using distance statistics.” International Journal of Pattern Recognition and Artificial Intelligence, 18(7), 2004. | CEDAR and offline verification. | [official/DOI](https://cedar.buffalo.edu/NIJ/publications/offline_signature.pdf) |
| **R25** | Pal, S.; Alaei, A.; Pal, U.; Blumenstein, M. “Performance of an Off-Line Signature Verification Method Based on Texture Features on a Large Indic-Script Signature Dataset.” 12th IAPR International Workshop on Document Analysis Systems (DAS), pp. 72–77, 2016. DOI: 10.1109/DAS.2016.48. | Introduction and characterization of BHSig260. | [official/DOI](https://doi.org/10.1109/DAS.2016.48) |
| **R26** | Soleimani, A.; Fouladi, K.; Araabi, B. N. “UTSig: A Persian offline signature dataset.” IET Biometrics, 2017. DOI: 10.1049/iet-bmt.2015.0058. | UTSig. | [official/DOI](https://doi.org/10.1049/iet-bmt.2015.0058) |
| **R27** | Ortega-Garcia, J. et al. “MCYT baseline corpus: a bimodal biometric database.” IEE Proceedings — Vision, Image and Signal Processing, 150(6):395–401, 2003. DOI: 10.1049/ip-vis:20031078. | MCYT family; corpus origin. | [official/DOI](https://doi.org/10.1049/ip-vis:20031078) |
| **R28** | W3C. PROV-DM: The PROV Data Model. W3C Recommendation, 30 April 2013. | Provenance: entities, activities, and agents. | [official/DOI](https://www.w3.org/TR/prov-dm/) |
| **R29** | ISO/IEC 27037:2012. Information technology — Security techniques — Guidelines for identification, collection, acquisition and preservation of digital evidence. | Digital evidence identification, collection, acquisition, and preservation. | [official/DOI](https://www.iso.org/standard/44381.html) |
| **R30** | Kent, K.; Chevalier, S.; Grance, T.; Dang, H. NIST SP 800-86, Guide to Integrating Forensic Techniques into Incident Response. NIST, 2006. DOI: 10.6028/NIST.SP.800-86. | Collection, examination, analysis, and reporting processes in digital forensics. | [official/DOI](https://doi.org/10.6028/NIST.SP.800-86) |
| **R31** | ISO/IEC 27001:2022. Information security, cybersecurity and privacy protection — Information security management systems — Requirements. | Information security management. | [official/DOI](https://www.iso.org/standard/27001) |
| **R32** | ISO/IEC 27002:2022. Information security, cybersecurity and privacy protection — Information security controls. | Information-security controls and practices. | [official/DOI](https://www.iso.org/standard/75652.html) |
| **R33** | Temoshok, D. et al. NIST SP 800-63B-4, Digital Identity Guidelines: Authentication and Authenticator Management. NIST, July 2025. | Authentication and modern authenticator/password policy. | [official/DOI](https://csrc.nist.gov/pubs/sp/800/63/b/4/final) |
| **R34** | OWASP Foundation. Password Storage Cheat Sheet. OWASP Cheat Sheet Series. | Password storage; PBKDF2-HMAC-SHA-256 and work-factor guidance. | [official/DOI](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) |
| **R35** | Moriarty, K.; Kaliski, B.; Rusch, A. RFC 8018 — PKCS #5: Password-Based Cryptography Specification Version 2.1. IETF, January 2017. | PBKDF2. | [official/DOI](https://www.rfc-editor.org/info/rfc8018) |
| **R36** | National Institute of Standards and Technology. FIPS PUB 180-4, Secure Hash Standard (SHS), updated 2015. DOI: 10.6028/NIST.FIPS.180-4. | SHA-256/SHA-512 and hash functions. | [official/DOI](https://doi.org/10.6028/NIST.FIPS.180-4) |
| **R37** | Ferraiolo, D. F.; Kuhn, D. R. “Role-Based Access Controls.” 15th National Computer Security Conference, 1992. | RBAC and role-based permission separation. | [official/DOI](https://www.nist.gov/publications/role-based-access-controls) |
| **R38** | Sandhu, R.; Ferraiolo, D.; Kuhn, D. “The NIST Model for Role-Based Access Control: Towards a Unified Standard.” 5th ACM Workshop on Role-Based Access Control, pp. 47–63, 2000. DOI: 10.1145/344287.344301. | RBAC model. | [official/DOI](https://doi.org/10.1145/344287.344301) |
| **R39** | Souppaya, M.; Scarfone, K.; Dodson, D. NIST SP 800-218, Secure Software Development Framework (SSDF) Version 1.1. NIST, February 2022. DOI: 10.6028/NIST.SP.800-218. | Secure development and release practices. | [official/DOI](https://doi.org/10.6028/NIST.SP.800-218) |
| **R40** | Moriarty, K.; Kaliski, B.; Jonsson, J.; Rusch, A. RFC 8017 — PKCS #1: RSA Cryptography Specifications Version 2.2. IETF, November 2016. | RSA and signatures/verification in the licensing subsystem. | [official/DOI](https://www.rfc-editor.org/info/rfc8017) |
| **R41** | SQLite. “Online Backup API.” Official SQLite Documentation. | Consistent backup of active SQLite databases. | [official/DOI](https://www.sqlite.org/backup.html) |
| **R42** | Python Software Foundation. `sqlite3` — DB-API 2.0 interface for SQLite databases. Python 3.10 Documentation. | SQLite interface used by the application; operational backup/connection support. | [official/DOI](https://docs.python.org/3.10/library/sqlite3.html) |
| **R43** | PyTorch Documentation. Optimizers and learning-rate schedulers (`Adam`, `AdamW`, `StepLR`, `CosineAnnealingLR`, `ReduceLROnPlateau`). | Semantics of optimizer/scheduler implementations used by SFAAO. | [official/DOI](https://docs.pytorch.org/docs/stable/optim.html) |
| **R44** | José, D. A. M.; Marques, L. de M.; Bueno, A.; Silva, R. B. Z. da. “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification.” Manuscript accepted for publication in IEEE Access, 2026 (final bibliographic metadata pending). | SFAAO scientific methodology; Independent External-Impostor Calibration. | Internal project record |

---


# 3. Normative and scientific status notes

## 3.1 ISO/IEC 19795-1

As of this document's reference date, **ISO/IEC 19795-1:2021, Edition 2** remains the applicable published International Standard. ISO also has **Edition 3 (ISO/IEC DIS 19795-1)** under development, intended to replace the 2021 edition.

SFAAO documents shall therefore cite the published 2021 edition until the new edition becomes a published International Standard. Any replacement requires an explicit documentation review.

## 3.2 ISO/IEC 27037:2012

The 2012 edition remains published and, as of the reference date, is under review at ISO. SFAAO shall not present an ongoing revision as an already published standard.

## 3.3 NIST SP 800-63B-4

The final version published in **July 2025** supersedes the earlier NIST SP 800-63B. It is the contemporary authentication reference adopted here. This does not mean every requirement in it is already enforced by the current SFAAO GUI; [SECURITY-ENG.md](SECURITY-ENG.md) distinguishes validated behavior from future hardening.

## 3.4 NIST SSDF

The consolidated normative reference used by this documentation is **NIST SP 800-218, SSDF Version 1.1 (2022)**. Later versions/drafts shall replace it only after final publication and SFAAO review.

## 3.5 SFAAO article accepted for publication


### Methodological reference for release 2.4.0

The independent external-impostor calibration methodology is associated with the article **“Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification”**, submitted to *IEEE Access* on 2026-07-15 (manuscript ID `Access-2026-35145`) and **accepted for publication**, as confirmed by the project team. Editorial acceptance does not mean the article has already been published.

Reference `[R44]` is an **article accepted for publication** whose final bibliographic metadata have not yet been verified for this documentation. Do not invent a DOI, volume, issue, or page numbers; add these fields only when documented by the publisher.

---

# 4. Dataset-reference policy

SFAAO distinguishes:

1. the **scientific/original corpus reference**;
2. the **official distribution page**, when available;
3. the **dataset license/terms of use**;
4. the internal SFAAO **Dataset Profile**.

A bibliographic reference **does not grant redistribution rights**. A dataset being documented or supported by SFAAO does not mean its files are part of the product.

### MCYT

`MCYT` is a broader biometric family/corpus. When operational documentation specifically refers to the offline set with **75 writers and 15 genuine + 15 forged signatures per writer**, it shall use **MCYT-75** or `MCYT-SignatureOff-75`, avoiding use of that variant as a synonym for the entire MCYT family.

---

# 5. Citation policy for SFAAO documents

- A reference shall appear at the **point where a concept is defined**, not only in this central bibliography.
- [REFERENCES-ENG.md](REFERENCES-ENG.md) centralizes metadata and reduces citation inconsistencies.
- Standards shall be identified by number, edition/year, and title.
- DOI shall be preferred when available.
- Official pages shall be preferred for standards, datasets, and technical documentation.
- Preprints/manuscripts shall be identified as such.
- A citation shall not be used to claim performance that the source did not evaluate.
- Integration of an architecture into SFAAO does not imply scientific homologation of every hyperparameter combination.

---

# 6. Reference map by document

| Document | Primary references |
|---|---|
| [ARCHITECTURE-ENG.md](ARCHITECTURE-ENG.md) | R01–R17, R43 |
| [CALIBRATION-ENG.md](CALIBRATION-ENG.md) | R18–R21, R44 |
| [DATASETS-ENG.md](DATASETS-ENG.md) | R22–R27 |
| [TRACEABILITY-ENG.md](TRACEABILITY-ENG.md) | R28–R30 |
| [SECURITY-ENG.md](SECURITY-ENG.md) | R29–R40 |
| [LICENSING-ENG.md](LICENSING-ENG.md) | R35–R40 |
| [INSTALLATION-ENG.md](INSTALLATION-ENG.md) | R39, R41–R43 |
| [TUTORIAL-ENG.md](TUTORIAL-ENG.md) | references canonical documents; does not redefine theory |
| [GLOSSARY-ENG.md](GLOSSARY-ENG.md) | references canonical documents; does not replace source material |


---

# 7. Maintenance rule

When updating a reference:

1. verify the official source/DOI;
2. update this catalog first;
3. review canonical documents citing the identifier/concept;
4. record the change in the `CHANGELOG`;
5. do not change manuscript, standard, or dataset status by inference.

This file shall remain semantically equivalent to the PT-BR version.
