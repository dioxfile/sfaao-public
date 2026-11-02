<p align="center">
  <img src="media/images/logo/SFAAO.png" alt="SFAAO" width="200">
</p>

<h1 align="center">SFAAO 2.4.0</h1>

<p align="center">
  <strong>Software Forense para Análise de Assinaturas Offline</strong><br>
  <strong>Forensic Software for Offline Handwritten Signature Analysis</strong>
</p>

<p align="center">
  <strong>Linux DEB | Linux RPM | Windows</strong>
</p>

---

<!-- SFAAO_240_PUBLIC_UPDATE_START -->
## Destaques do SFAAO 2.4.0 / Version 2.4.0 Highlights

O **SFAAO 2.4.0** integra inteligência artificial, análise
grafoscópica especializada e procedimentos quantitativos para
apoiar a análise forense de assinaturas offline.

**Principais recursos:**

- Interface com 20 critérios grafoscópicos.
- Protocolos forense e científico metodologicamente distintos.
- Calibração conforme o protocolo e o domínio de avaliação.
- Comparação individual consolidada LORO/A4.
- Inspeção de scores pairwise sem decisão técnica isolada.
- Comparação em lote e atualização do contexto.
- Documentação técnica e relatórios PDF/DOCX.
- Distribuições Linux DEB, Linux RPM e Windows EXE.

**English:** SFAAO 2.4.0 combines artificial intelligence,
expert graphoscopic assessment, protocol-specific calibration,
LORO/A4 comparison, pairwise score inspection, batch processing,
and technical reporting.

The forensic protocol requires expert classification before
calibration. The scientific protocol may use external impostors
under the applicable experimental design.

**Limitação / Limitation:** O SFAAO não substitui o exame
realizado por um perito grafoscopista. Computational results
do not independently establish the legal authenticity
of a signature.

### Galeria de telas / Screenshots

- [Interface principal / Main interface](media/images/screenshots/main-window.png)
- [Gestão do caso / Case management](media/images/screenshots/case-window.png)
- [20 critérios grafoscópicos / 20 graphoscopic criteria](media/images/screenshots/main-window-criterios-grafos.png)
- [Comparação / Signature comparison](media/images/screenshots/compare-window.png)
- [Comparação LORO/A4 / LORO/A4 comparison](media/images/screenshots/compare-window-loro.png)
- [Inspeção pairwise / Pairwise inspection](media/images/screenshots/compare-window-pairwise_inspection.png)
- [Atualização de contexto / Context refresh](media/images/screenshots/compare-window-context_refresh.png)
- [Resultado da calibração / Calibration results](media/images/screenshots/result-calibration-window.png)
- [Relatório DOCX / DOCX report](media/images/screenshots/expert-report-result-docx.png)

### Documentação oficial / Official Documentation

[Documentação PT-BR e ENG / Documentation Index](docs/README.md)

[Notas de lançamento / Release Notes](docs/RELEASE_2.4.0_PTBR_EN.md)

<!-- SFAAO_240_PUBLIC_UPDATE_END -->

<!-- SFAAO_DOCUMENTATION_INDEX -->
## Documentação técnica / Technical Documentation

Acesse os **20 documentos técnicos**, os manuais completos em PDF e as notas da versão 2.4.0.

Access the **20 technical documents**, complete PDF manuals and release notes for version 2.4.0.

**[Índice da documentação / Documentation Index](docs/README.md)**


# Português — Brasil

## Apresentação

O **SFAAO 2.4.0** é uma plataforma computacional especializada no apoio à análise forense e científica de assinaturas manuscritas digitalizadas.

Integra técnicas de **Inteligência Artificial, Deep Learning, calibração estatística, comparação quantitativa e rastreabilidade**, oferecendo ferramentas para o exame técnico de assinaturas e a produção de documentação pericial.

O sistema foi desenvolvido para apoiar o trabalho de peritos grafoscopistas, pesquisadores e instituições que necessitam de procedimentos estruturados e reprodutíveis.

**O SFAAO não substitui o perito grafoscopista e não determina, isoladamente, a autenticidade jurídica de uma assinatura.**

## Novidades da versão 2.4.0

Esta versão reúne aperfeiçoamentos nos mecanismos de calibração, comparação de assinaturas, organização dos resultados e geração de laudos.

Entre os principais recursos estão:

- Interface para os **20 critérios grafoscópicos**.
- Comparação individual consolidada **LORO/A4**.
- Inspeção quantitativa de scores *pairwise*.
- Comparação de assinaturas em lote.
- Atualização do contexto de comparação.
- Calibração e apresentação de resultados técnicos.
- Geração de laudos e relatórios em **PDF e DOCX**.
- Mecanismos de rastreabilidade e verificação de integridade.
- Distribuições homologadas para Linux e Windows.

[Consulte as notas da versão 2.4.0](docs/RELEASE_2.4.0_PTBR_EN.md).

## Interface principal

![Interface principal do SFAAO 2.4.0](media/images/screenshots/main-window.png)

## Vinte critérios grafoscópicos

A versão 2.4.0 apresenta uma interface dedicada aos **20 critérios grafoscópicos**, integrando a avaliação especializada ao fluxo de trabalho computacional.

Essa funcionalidade reforça a participação do especialista na análise e na documentação dos elementos grafoscópicos.

![Interface dos 20 critérios grafoscópicos](media/images/screenshots/main-window-criterios-grafos.png)

## Inteligência Artificial

O SFAAO oferece diferentes configurações para pesquisa, treinamento e avaliação de modelos de análise de assinaturas.

**Arquiteturas disponíveis:** ResNet18, ResNet34, ResNet50, EfficientNet-B0, ConvNeXt-Tiny e MobileNetV3.

**Modos de treinamento:** PAIR, TRIPLET e CLASSIFICATION.

**Funções de perda:** Contrastive, Cosine Contrastive, Triplet e ArcFace.

**Métricas de comparação:** similaridade cosseno e distância euclidiana.

O ambiente também contempla configurações de embeddings, treinamento, validação e avaliação quantitativa.

## Protocolos e calibração

O SFAAO distingue dois contextos metodológicos:

**Protocolo forense:** depende da classificação especializada das assinaturas questionadas pelo perito grafoscopista. A calibração ocorre após essa etapa.

**Protocolo científico:** permite a utilização de impostores externos provenientes de bases de dados de pesquisa, de acordo com o desenho experimental.

Essa distinção é essencial para interpretar corretamente os resultados e evitar confundir avaliação científica com conclusão pericial.

## Comparação de assinaturas

A interface de comparação disponibiliza quatro operações:

1. **Comparação Individual (LORO/A4)** — avaliação consolidada segundo o contexto de calibração aplicável.
2. **Inspecionar Score Pairwise** — exame quantitativo de uma comparação entre assinaturas, sem decisão técnica isolada.
3. **Comparar em Lote** — processamento de múltiplas comparações.
4. **Atualizar Contexto** — atualização das informações utilizadas na comparação.

![Interface de comparação do SFAAO 2.4.0](media/images/screenshots/compare-window.png)

Na abordagem LORO/A4, a decisão técnica consolidada não deve ser confundida com o score individual de inspeção pairwise.

## Laudos e rastreabilidade

O SFAAO permite a elaboração de relatórios técnicos em **PDF e DOCX**, apoiando a documentação das análises e a organização dos resultados.

A rastreabilidade dos procedimentos e das configurações utilizadas é parte importante do fluxo técnico do sistema.

## Segurança e licenciamento

O SFAAO utiliza mecanismos de licenciamento e verificação de integridade, com execução local.

As ferramentas administrativas de emissão de licenças, chaves privadas e demais componentes restritos não fazem parte deste repositório público.

## Plataformas

A versão 2.4.0 possui distribuições homologadas nos seguintes formatos:

- Linux — pacote DEB.
- Linux — pacote RPM.
- Windows — instalador EXE.

Este repositório apresenta informações institucionais e técnicas. Não distribui o código-fonte proprietário nem os instaladores comerciais.

## Propriedade intelectual

O software SFAAO possui registro junto ao Instituto Nacional da Propriedade Industrial (INPI).

[Consultar documento de registro do software](publications/software-registration/SFAAO-REGISTRO.pdf).

---

# English

## Overview

**SFAAO 2.4.0 — Forensic Software for Offline Handwritten Signature Analysis** is a specialized computational platform supporting forensic and scientific examination of digitized handwritten signatures.

It combines **Artificial Intelligence, Deep Learning, statistical calibration, quantitative comparison, and traceability** in a structured analytical workflow.

The platform is designed to assist forensic handwriting examiners, researchers, and institutions requiring reproducible technical procedures.

**SFAAO does not replace a qualified forensic examiner and does not independently establish the legal authenticity of a signature.**

## What's New in Version 2.4.0

- Dedicated interface for **20 graphoscopic criteria**.
- Consolidated **LORO/A4** individual comparison.
- Pairwise score inspection.
- Batch signature comparison.
- Comparison context refresh.
- Statistical calibration and technical results.
- PDF and DOCX report generation.
- Traceability and integrity verification mechanisms.
- Validated Linux and Windows distributions.

[Read the SFAAO 2.4.0 release notes](docs/RELEASE_2.4.0_PTBR_EN.md).

## Main Interface

![SFAAO 2.4.0 main interface](media/images/screenshots/main-window.png)

## Twenty Graphoscopic Criteria

SFAAO 2.4.0 provides a dedicated interface for **20 graphoscopic criteria**, integrating specialist assessment into the computational forensic workflow.

![SFAAO graphoscopic criteria interface](media/images/screenshots/main-window-criterios-grafos.png)

## Artificial Intelligence

SFAAO supports multiple architectures and training configurations for handwritten signature analysis.

**Architectures:** ResNet18, ResNet34, ResNet50, EfficientNet-B0, ConvNeXt-Tiny, and MobileNetV3.

**Training modes:** PAIR, TRIPLET, and CLASSIFICATION.

**Loss functions:** Contrastive, Cosine Contrastive, Triplet, and ArcFace.

**Comparison metrics:** cosine similarity and Euclidean distance.

Additional capabilities include configurable embeddings, training parameters, validation procedures, and quantitative evaluation.

## Forensic and Scientific Protocols

**Forensic protocol:** questioned-signature classification is performed by a qualified forensic handwriting examiner before calibration.

**Scientific protocol:** external impostors from research datasets may be used according to the experimental design.

The two protocols must not be treated as methodologically interchangeable.

## Signature Comparison

Four main operations are available:

1. **Individual Comparison (LORO/A4)** — consolidated evaluation within the applicable calibration context.
2. **Inspect Pairwise Score** — quantitative inspection without an independent technical decision.
3. **Batch Comparison** — processing multiple comparisons.
4. **Refresh Context** — updating the active comparison context.

![SFAAO 2.4.0 signature comparison](media/images/screenshots/compare-window.png)

A consolidated LORO/A4 technical result must not be confused with an individual pairwise inspection score.

## Reports and Traceability

SFAAO supports **PDF and DOCX** technical reporting, helping document analyses, configurations, and results.

## Security and Licensing

The software includes licensing and integrity-verification mechanisms and supports local execution.

Administrative license-generation tools, private keys, and restricted proprietary materials are excluded from this public repository.

## Supported Distributions

The SFAAO 2.4.0 release includes validated distributions for:

- Linux DEB.
- Linux RPM.
- Windows EXE.

This repository provides public product documentation and does not distribute proprietary source code or commercial installers.

## Intellectual Property

SFAAO software registration documentation is available through the Brazilian National Institute of Industrial Property (INPI).

[View software registration documentation](publications/software-registration/SFAAO-REGISTRO.pdf).

---

**SFAAO 2.4.0 — Forensic and Scientific Offline Signature Analysis**
