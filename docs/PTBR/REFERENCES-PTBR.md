# SFAAO 2.4.0 — Referências Científicas, Normativas e Técnicas

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Finalidade

Este documento centraliza as referências científicas, normativas e técnicas utilizadas pela documentação oficial do SFAAO 2.4.0.

Ele **não substitui as citações no ponto de definição**. Cada documento técnico deve manter a referência junto ao conceito que fundamenta; este arquivo existe para padronizar metadados, DOI/URLs, status e escopo de uso.

---

# 2. Catálogo central

| ID | Referência | Uso no SFAAO | Fonte |
|---|---|---|---|
| **R01** | LeCun, Y.; Bengio, Y.; Hinton, G. “Deep learning.” Nature, 521, 436–444, 2015. DOI: 10.1038/nature14539. | Deep learning; representação hierárquica. | [official/DOI](https://doi.org/10.1038/nature14539) |
| **R02** | Bromley, J.; Guyon, I.; LeCun, Y.; Säckinger, E.; Shah, R. “Signature Verification using a ‘Siamese’ Time Delay Neural Network.” Advances in Neural Information Processing Systems 6, 1993/1994. | Redes siamesas e verificação de assinaturas. | [official/DOI](https://papers.nips.cc/paper/1993/hash/288cc0ff022877bd3df94bc9360b9c5d-Abstract.html) |
| **R03** | Hadsell, R.; Chopra, S.; LeCun, Y. “Dimensionality Reduction by Learning an Invariant Mapping.” CVPR, pp. 1735–1742, 2006. DOI: 10.1109/CVPR.2006.100. | Contrastive Loss; aprendizagem métrica. | [official/DOI](https://doi.org/10.1109/CVPR.2006.100) |
| **R04** | Schroff, F.; Kalenichenko, D.; Philbin, J. “FaceNet: A Unified Embedding for Face Recognition and Clustering.” CVPR, pp. 815–823, 2015. | Embeddings e Triplet Loss. | [official/DOI](https://openaccess.thecvf.com/content_cvpr_2015/html/Schroff_FaceNet_A_Unified_2015_CVPR_paper.html) |
| **R05** | Deng, J.; Guo, J.; Xue, N.; Zafeiriou, S. “ArcFace: Additive Angular Margin Loss for Deep Face Recognition.” CVPR, 2019. DOI: 10.1109/CVPR.2019.00482. | ArcFace e margens angulares. | [official/DOI](https://doi.org/10.1109/CVPR.2019.00482) |
| **R06** | He, K.; Zhang, X.; Ren, S.; Sun, J. “Deep Residual Learning for Image Recognition.” CVPR, pp. 770–778, 2016. DOI: 10.1109/CVPR.2016.90. | ResNet18/34/50; conexões residuais. | [official/DOI](https://doi.org/10.1109/CVPR.2016.90) |
| **R07** | Tan, M.; Le, Q. V. “EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks.” ICML, PMLR 97:6105–6114, 2019. | EfficientNet-B0; escalonamento de CNN. | [official/DOI](https://proceedings.mlr.press/v97/tan19a.html) |
| **R08** | Howard, A. et al. “Searching for MobileNetV3.” ICCV, pp. 1314–1324, 2019. | MobileNetV3. | [official/DOI](https://openaccess.thecvf.com/content_ICCV_2019/html/Howard_Searching_for_MobileNetV3_ICCV_2019_paper.html) |
| **R09** | Liu, Z.; Mao, H.; Wu, C.-Y.; Feichtenhofer, C.; Darrell, T.; Xie, S. “A ConvNet for the 2020s.” CVPR, pp. 11976–11986, 2022. | ConvNeXt/ConvNeXt-Tiny. | [official/DOI](https://openaccess.thecvf.com/content/CVPR2022/html/Liu_A_ConvNet_for_the_2020s_CVPR_2022_paper.html) |
| **R10** | Srivastava, N.; Hinton, G.; Krizhevsky, A.; Sutskever, I.; Salakhutdinov, R. “Dropout: A Simple Way to Prevent Neural Networks from Overfitting.” Journal of Machine Learning Research, 15:1929–1958, 2014. | Dropout e regularização. | [official/DOI](https://www.jmlr.org/papers/v15/srivastava14a.html) |
| **R11** | Kingma, D. P.; Ba, J. “Adam: A Method for Stochastic Optimization.” International Conference on Learning Representations, 2015. arXiv:1412.6980. | Otimizador Adam. | [official/DOI](https://arxiv.org/abs/1412.6980) |
| **R12** | Loshchilov, I.; Hutter, F. “Decoupled Weight Decay Regularization.” International Conference on Learning Representations, 2019. | AdamW e weight decay desacoplado. | [official/DOI](https://iclr.cc/virtual/2019/poster/935) |
| **R13** | Loshchilov, I.; Hutter, F. “SGDR: Stochastic Gradient Descent with Warm Restarts.” International Conference on Learning Representations, 2017. | Cosine annealing/scheduling. | [official/DOI](https://arxiv.org/abs/1608.03983) |
| **R14** | Prechelt, L. “Early Stopping — but when?” In: Orr, G. B.; Müller, K.-R. (eds.), Neural Networks: Tricks of the Trade, LNCS 1524, pp. 55–69, Springer, 1998. | Early stopping. | [official/DOI](https://publikationen.bibliothek.kit.edu/7498) |
| **R15** | Pan, S. J.; Yang, Q. “A Survey on Transfer Learning.” IEEE Transactions on Knowledge and Data Engineering, 22(10):1345–1359, 2010. DOI: 10.1109/TKDE.2009.191. | Transfer learning; pretrained/fine-tuning. | [official/DOI](https://doi.org/10.1109/TKDE.2009.191) |
| **R16** | Bellet, A.; Habrard, A.; Sebban, M. “A Survey on Metric Learning for Feature Vectors and Structured Data.” CoRR abs/1306.6709, 2013. | Aprendizagem métrica, distâncias e similaridades. | [official/DOI](https://arxiv.org/abs/1306.6709) |
| **R17** | Otsu, N. “A Threshold Selection Method from Gray-Level Histograms.” IEEE Transactions on Systems, Man, and Cybernetics, 9(1):62–66, 1979. DOI: 10.1109/TSMC.1979.4310076. | Binarização de Otsu no pré-processamento. | [official/DOI](https://doi.org/10.1109/TSMC.1979.4310076) |
| **R18** | Plamondon, R.; Lorette, G. “Automatic signature verification and writer identification — the state of the art.” Pattern Recognition, 22(2):107–131, 1989. DOI: 10.1016/0031-3203(89)90059-9. | Fundamentos de verificação automática de assinaturas. | [official/DOI](https://doi.org/10.1016/0031-3203(89)90059-9) |
| **R19** | Jain, A. K.; Flynn, P.; Ross, A. A. (eds.). Handbook of Biometrics. Springer, 2008. | Biometria; terminologia e avaliação. | [official/DOI](https://link.springer.com/book/10.1007/978-0-387-71041-9) |
| **R20** | ISO/IEC 19795-1:2021. Information technology — Biometric performance testing and reporting — Part 1: Principles and framework. Edition 2, May 2021. | Teste de desempenho biométrico; métricas e protocolos. | [official/DOI](https://www.iso.org/standard/73515.html) |
| **R21** | Moreno-Torres, J. G.; Raeder, T.; Alaiz-Rodríguez, R.; Chawla, N. V.; Herrera, F. “A unifying view on dataset shift in classification.” Pattern Recognition, 45(1):521–530, 2012. DOI: 10.1016/j.patcog.2011.06.019. | Dataset shift. | [official/DOI](https://doi.org/10.1016/j.patcog.2011.06.019) |
| **R22** | Ferrer, M. A.; Diaz-Cabrera, M.; Morales, A. “Static Signature Synthesis: A Neuromotor Inspired Approach for Biometrics.” IEEE Transactions on Pattern Analysis and Machine Intelligence, 37(3):667–680, 2015. DOI: 10.1109/TPAMI.2014.2343981. | GPDSsyntheticSignature; síntese de assinaturas. | [official/DOI](https://doi.org/10.1109/TPAMI.2014.2343981) |
| **R23** | GPDS — Grupo de Procesado Digital de Señales, Universidad de Las Palmas de Gran Canaria. “Toolbox & Databases — GPDSsyntheticSignature database.” | Fonte oficial do corpus GPDSsyntheticSignature e suas características de distribuição. | [official/DOI](https://gpds.ulpgc.es/downloadnew/download.htm) |
| **R24** | Kalera, M. K.; Srihari, S.; Xu, A. “Offline signature verification and identification using distance statistics.” International Journal of Pattern Recognition and Artificial Intelligence, 18(7), 2004. | CEDAR e verificação offline. | [official/DOI](https://cedar.buffalo.edu/NIJ/publications/offline_signature.pdf) |
| **R25** | Pal, S.; Alaei, A.; Pal, U.; Blumenstein, M. “Performance of an Off-Line Signature Verification Method Based on Texture Features on a Large Indic-Script Signature Dataset.” 12th IAPR International Workshop on Document Analysis Systems (DAS), pp. 72–77, 2016. DOI: 10.1109/DAS.2016.48. | Introdução e caracterização do BHSig260. | [official/DOI](https://doi.org/10.1109/DAS.2016.48) |
| **R26** | Soleimani, A.; Fouladi, K.; Araabi, B. N. “UTSig: A Persian offline signature dataset.” IET Biometrics, 2017. DOI: 10.1049/iet-bmt.2015.0058. | UTSig. | [official/DOI](https://doi.org/10.1049/iet-bmt.2015.0058) |
| **R27** | Ortega-Garcia, J. et al. “MCYT baseline corpus: a bimodal biometric database.” IEE Proceedings — Vision, Image and Signal Processing, 150(6):395–401, 2003. DOI: 10.1049/ip-vis:20031078. | Família MCYT; origem do corpus. | [official/DOI](https://doi.org/10.1049/ip-vis:20031078) |
| **R28** | W3C. PROV-DM: The PROV Data Model. W3C Recommendation, 30 April 2013. | Proveniência: entidades, atividades e agentes. | [official/DOI](https://www.w3.org/TR/prov-dm/) |
| **R29** | ISO/IEC 27037:2012. Information technology — Security techniques — Guidelines for identification, collection, acquisition and preservation of digital evidence. | Evidência digital, identificação, coleta, aquisição e preservação. | [official/DOI](https://www.iso.org/standard/44381.html) |
| **R30** | Kent, K.; Chevalier, S.; Grance, T.; Dang, H. NIST SP 800-86, Guide to Integrating Forensic Techniques into Incident Response. NIST, 2006. DOI: 10.6028/NIST.SP.800-86. | Processos de coleta, exame, análise e relato em contexto forense digital. | [official/DOI](https://doi.org/10.6028/NIST.SP.800-86) |
| **R31** | ISO/IEC 27001:2022. Information security, cybersecurity and privacy protection — Information security management systems — Requirements. | Gestão de segurança da informação. | [official/DOI](https://www.iso.org/standard/27001) |
| **R32** | ISO/IEC 27002:2022. Information security, cybersecurity and privacy protection — Information security controls. | Controles e boas práticas de segurança. | [official/DOI](https://www.iso.org/standard/75652.html) |
| **R33** | Temoshok, D. et al. NIST SP 800-63B-4, Digital Identity Guidelines: Authentication and Authenticator Management. NIST, July 2025. | Autenticação e política moderna de autenticadores/senhas. | [official/DOI](https://csrc.nist.gov/pubs/sp/800/63/b/4/final) |
| **R34** | OWASP Foundation. Password Storage Cheat Sheet. OWASP Cheat Sheet Series. | Armazenamento de senha; orientação PBKDF2-HMAC-SHA-256 e fatores de trabalho. | [official/DOI](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) |
| **R35** | Moriarty, K.; Kaliski, B.; Rusch, A. RFC 8018 — PKCS #5: Password-Based Cryptography Specification Version 2.1. IETF, January 2017. | PBKDF2. | [official/DOI](https://www.rfc-editor.org/info/rfc8018) |
| **R36** | National Institute of Standards and Technology. FIPS PUB 180-4, Secure Hash Standard (SHS), updated 2015. DOI: 10.6028/NIST.FIPS.180-4. | SHA-256/SHA-512 e funções hash. | [official/DOI](https://doi.org/10.6028/NIST.FIPS.180-4) |
| **R37** | Ferraiolo, D. F.; Kuhn, D. R. “Role-Based Access Controls.” 15th National Computer Security Conference, 1992. | RBAC e separação de permissões por papéis. | [official/DOI](https://www.nist.gov/publications/role-based-access-controls) |
| **R38** | Sandhu, R.; Ferraiolo, D.; Kuhn, D. “The NIST Model for Role-Based Access Control: Towards a Unified Standard.” 5th ACM Workshop on Role-Based Access Control, pp. 47–63, 2000. DOI: 10.1145/344287.344301. | Modelo de RBAC. | [official/DOI](https://doi.org/10.1145/344287.344301) |
| **R39** | Souppaya, M.; Scarfone, K.; Dodson, D. NIST SP 800-218, Secure Software Development Framework (SSDF) Version 1.1. NIST, February 2022. DOI: 10.6028/NIST.SP.800-218. | Desenvolvimento seguro e práticas de release. | [official/DOI](https://doi.org/10.6028/NIST.SP.800-218) |
| **R40** | Moriarty, K.; Kaliski, B.; Jonsson, J.; Rusch, A. RFC 8017 — PKCS #1: RSA Cryptography Specifications Version 2.2. IETF, November 2016. | RSA e assinaturas/verificação no subsistema de licenciamento. | [official/DOI](https://www.rfc-editor.org/info/rfc8017) |
| **R41** | SQLite. “Online Backup API.” Official SQLite Documentation. | Backup consistente de bancos SQLite em operação. | [official/DOI](https://www.sqlite.org/backup.html) |
| **R42** | Python Software Foundation. `sqlite3` — DB-API 2.0 interface for SQLite databases. Python 3.10 Documentation. | Interface SQLite utilizada pela aplicação; suporte operacional a backup/conexões. | [official/DOI](https://docs.python.org/3.10/library/sqlite3.html) |
| **R43** | PyTorch Documentation. Optimizers and learning-rate schedulers (`Adam`, `AdamW`, `StepLR`, `CosineAnnealingLR`, `ReduceLROnPlateau`). | Semântica das implementações de optimizer/scheduler utilizadas pelo SFAAO. | [official/DOI](https://docs.pytorch.org/docs/stable/optim.html) |
| **R44** | José, D. A. M.; Marques, L. de M.; Bueno, A.; Silva, R. B. Z. da. “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification.” Manuscrito revisado e ressubmetido ao IEEE Access após avaliação por pares, 2026 (decisão editorial e metadados finais pendentes). | Metodologia científica SFAAO; Independent External-Impostor Calibration. | Registro interno do projeto |

---


# 3. Notas de status normativo e científico

## 3.1 ISO/IEC 19795-1

Na data de referência deste documento, **ISO/IEC 19795-1:2021, Edition 2** permanece a norma internacional publicada aplicável. A ISO também mantém em desenvolvimento a **Edition 3 (ISO/IEC DIS 19795-1)**, destinada a substituir a edição de 2021.

Portanto, os documentos SFAAO devem citar a edição publicada de 2021 enquanto a nova edição não for publicada como International Standard. A eventual substituição exige revisão documental explícita.

## 3.2 ISO/IEC 27037:2012

A edição de 2012 permanece publicada e, na data de referência, encontra-se em processo de revisão na ISO. O SFAAO não deve apresentar a revisão em andamento como norma já publicada.

## 3.3 NIST SP 800-63B-4

A versão final publicada em **julho de 2025** substitui a edição anterior do NIST SP 800-63B. Ela é a referência contemporânea adotada para recomendações de autenticação. Isso não significa que todos os requisitos nela contidos já estejam implementados pela GUI atual do SFAAO; [SECURITY-PTBR.md](SECURITY-PTBR.md) distingue comportamento validado de hardening futuro.

## 3.4 NIST SSDF

A referência normativa consolidada utilizada nesta documentação é **NIST SP 800-218, SSDF Version 1.1 (2022)**. Versões/drafts posteriores só devem substituir esta referência após publicação final e revisão do SFAAO.

## 3.5 Artigo SFAAO revisado e ressubmetido


### Referência metodológica do release 2.4.0

A metodologia de calibração externa independente está associada ao artigo **“Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification”**, submetido ao *IEEE Access* em 15/07/2026 (manuscript ID `Access-2026-35145`) e cuja versão revisada foi **ressubmetida após atendimento às solicitações dos revisores**. A decisão editorial sobre a aceitação permanece pendente.

A referência `[R44]` corresponde a um **manuscrito revisado e ressubmetido**, ainda sem decisão editorial ou metadados bibliográficos finais confirmados nesta documentação. Não atribuir DOI, volume, fascículo ou páginas sem comprovação editorial; registrar esses dados quando disponíveis.

---

# 4. Política para referências de datasets

O SFAAO distingue:

1. **referência científica/original do corpus**;
2. **página oficial de distribuição**, quando existente;
3. **licença/termos de uso do dataset**;
4. **Dataset Profile interno do SFAAO**.

Uma referência bibliográfica **não concede direito de redistribuição**. O fato de um dataset estar documentado ou suportado pelo SFAAO não significa que seus arquivos façam parte do produto.

### MCYT

`MCYT` é uma família/corpus biométrico mais amplo. Quando a documentação operacional se referir especificamente à base offline de **75 escritores, 15 genuínas + 15 falsificações por escritor**, deve usar **MCYT-75** ou `MCYT-SignatureOff-75`, evitando apresentar essa variante como sinônimo de toda a família MCYT.

---

# 5. Política de citação nos documentos SFAAO

- A referência deve aparecer no **ponto em que o conceito é definido**, não apenas nesta bibliografia central.
- [REFERENCES-PTBR.md](REFERENCES-PTBR.md) centraliza metadados e reduz divergências de escrita.
- Normas devem ser identificadas por número, edição/ano e título.
- DOI deve ser preferido quando existir.
- Página oficial deve ser preferida para normas, datasets e documentação técnica.
- Preprints/manuscritos devem ser identificados como tal.
- Uma citação não deve ser usada para alegar um desempenho que a fonte não avaliou.
- Integração de uma arquitetura no SFAAO não implica homologação científica de todas as combinações de hiperparâmetros.

---

# 6. Mapa de referência por documento

| Documento | Referências principais |
|---|---|
| [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) | R01–R17, R43 |
| [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) | R18–R21, R44 |
| [DATASETS-PTBR.md](DATASETS-PTBR.md) | R22–R27 |
| [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) | R28–R30 |
| [SECURITY-PTBR.md](SECURITY-PTBR.md) | R29–R40 |
| [LICENSING-PTBR.md](LICENSING-PTBR.md) | R35–R40 |
| [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) | R39, R41–R43 |
| [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) | referencia os documentos canônicos; não redefine teoria |
| [GLOSSARY-PTBR.md](GLOSSARY-PTBR.md) | referencia os documentos canônicos; não substitui as fontes |


---

# 7. Regra de manutenção

Ao atualizar uma referência:

1. verificar a fonte oficial/DOI;
2. alterar primeiro este catálogo;
3. revisar os documentos canônicos que citam o identificador/conceito;
4. registrar mudança no `CHANGELOG`;
5. não alterar status de manuscrito, norma ou dataset por inferência.

Este arquivo deve permanecer semanticamente equivalente à versão inglesa.
