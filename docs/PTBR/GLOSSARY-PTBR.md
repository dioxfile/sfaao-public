# SFAAO 2.4.0 — Glossário Técnico

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Finalidade

Este glossário fornece **definições curtas de referência rápida**. Ele não substitui as definições formais, equações, fundamentos científicos, regras operacionais e limitações estabelecidas nos documentos canônicos do SFAAO.

Quando um termo exigir interpretação metodológica, científica, normativa ou operacional, prevalece o documento indicado na coluna **Documento canônico**.

A política editorial é deliberadamente antirrepetição: conceitos não são redefinidos integralmente neste arquivo.

---

# 2. Termos


| Termo | Definição resumida | Documento canônico |
|---|---|---|
| **A1 / `PAIRWISE_SCORE`** | Score entre um par de assinaturas; componente de proveniência em LORO. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **A(N−1) / `LORO_MEAN`** | Média de cada fold com uma referência excluída; domínio decisório alinhado entre calibração e inferência. Com cinco referências, A4. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **A(N)** | Média descritiva sobre todas as referências; não receber novamente o limiar LORO para classificação. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **LORO** | Leave-One-Reference-Out; em cada fold exclui uma referência para manter a comparação no domínio A(N−1). | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`τ_EER`** | Limiar associado à estimativa de Equal Error Rate (EER). | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`τ_forensic`** | Limiar associado à meta operacional de FAR quando configurado. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`τ_operational`** | Limiar operacional conservador aplicado à regra de três zonas. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`ALIGNED` / `MISMATCH` / `LEGACY_MISMATCH`** | Estados de compatibilidade entre os domínios de calibração e inferência. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`STABLE_COMPATIBLE` / `STABLE_INCOMPATIBLE` / `STABLE_INCONCLUSIVE`** | Resultado estável quando os folds compartilham o mesmo estado de decisão. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **`REFERENCE_SENSITIVE`** | Resultado sensível à exclusão de referência, consolidado como inconclusivo. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Comparação Individual (LORO/A4)** | Comparação individual no contexto ativo e domínio A(N−1) correspondente. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **Inspecionar Score Pairwise** | Inspeção dos scores elementares de referência por amostra questionada. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **Comparar em Lote** | Processamento de múltiplas comparações conforme o contexto ativo. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **Atualizar Contexto** | Ação da janela de comparação para atualizar o contexto operacional selecionado. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **ACADEMIC** | Perfil operacional de licença destinado a uso acadêmico, sujeito às features, protocolos e cotas definidos em [LICENSING-PTBR.md](LICENSING-PTBR.md). | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **ADMIN** | Papel administrativo do SFAAO com acesso às funções de gestão autorizadas pela política de segurança e pela licença ativa. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Adam** | Otimizador adaptativo suportado no treinamento. Sua definição e uso pertencem à arquitetura de treinamento. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **AdamW** | Otimizador adaptativo com desacoplamento de weight decay, suportado pelo SFAAO. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **ArcFace** | Função/estratégia de margem angular utilizada em configurações compatíveis de classificação e embeddings. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Artifact** | Arquivo ou conjunto de arquivos produzido ou consumido por uma execução e vinculado à sua proveniência. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Audit trail** | Registro de eventos operacionais e administrativos relevantes. Não é sinônimo de proveniência científica. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Backbone** | Rede de extração de características utilizada como base de uma arquitetura de modelo. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Batch analysis** | Execução em lote de comparações/resultados segundo regras, cotas e rastreabilidade da aplicação. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **Batch size** | Número de amostras processadas por lote durante treinamento ou avaliação, conforme a configuração. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **BHSig260** | Dataset offline de assinaturas manuscritas com subconjuntos Bengali e Hindi, utilizado mediante obtenção legítima e profile apropriado. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Calibration** | No SFAAO, calibração biométrica do limiar de decisão/ponto operacional; não significa treinamento da rede neural. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Calibration UID** | Identificador estável de uma calibração persistida e rastreável. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Case** | Unidade operacional que agrupa dados, assinaturas, execuções, resultados e relatórios relacionados a uma análise. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **case_uid** | Identificador estável de um caso. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **CEDAR** | Dataset offline de assinaturas manuscritas com amostras genuínas e falsificações, utilizado mediante obtenção legítima. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Chain of custody** | Processo institucional/legal de preservação, controle e responsabilização sobre evidências. O SFAAO fornece elementos técnicos de suporte, mas não o substitui. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **CLASSIFICATION** | Modo de treinamento supervisionado por classe/escritor, distinto dos modos de pares e triplas. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Comparison run** | Execução identificável de comparação entre referências e assinaturas questionadas utilizando modelo e calibração definidos. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Contrastive Loss** | Função de perda para aprendizagem métrica em pares, aproximando pares positivos e separando pares negativos segundo margem/regra compatível. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **ConvNeXt-Tiny** | Backbone ConvNeXt suportado pelo SFAAO na variante Tiny. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Cosine Contrastive Loss** | Variação de perda contrastiva definida sobre similaridade/distância cosseno em configurações compatíveis. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Cosine Similarity** | Métrica de similaridade angular entre embeddings; valores maiores representam maior compatibilidade no regime documentado. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **CSV export** | Exportação tabular de resultados quando autorizada pelo perfil/licença e pela política de cotas. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **Dataset** | Corpus de assinaturas utilizado em treinamento, validação ou calibração científica, obtido e utilizado sob licença/autorização própria. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **DatasetCatalog** | Componente responsável por catalogar datasets registrados e seus metadados operacionais. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **DatasetFactory** | Componente de construção/seleção de implementação de dataset compatível com o profile configurado. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Dataset Profile** | Descrição formal de como o SFAAO interpreta a estrutura, nomes, identidades, classes e metadados de um dataset. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **DatasetProvider** | Componente que fornece acesso padronizado a um dataset registrado. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Dataset shift** | Mudança entre distribuições de dados de desenvolvimento/calibração e dados operacionais, capaz de alterar desempenho e ponto de operação. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Decision direction** | Regra que define se maior ou menor valor da métrica favorece compatibilidade; depende da métrica utilizada. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Decision threshold** | Valor de corte aplicado à métrica para transformar um score contínuo em decisão operacional. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **DistanceFactory** | Componente que seleciona/constrói a métrica de distância ou similaridade configurada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **DOCX report** | Relatório em formato DOCX gerado a partir dos dados e resultados persistidos e sujeito à revisão humana. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Dropout** | Técnica de regularização utilizada em configurações de modelo para reduzir coadaptação durante treinamento. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Early stopping** | Critério de interrupção do treinamento quando a métrica monitorada deixa de melhorar segundo parâmetros configurados. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **EER** | Equal Error Rate: ponto em que FAR e FRR são iguais ou aproximadamente iguais segundo o procedimento de estimação adotado. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **EfficientNet-B0** | Backbone EfficientNet suportado pelo SFAAO na variante B0. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Embedding** | Representação vetorial aprendida de uma assinatura usada para comparação métrica. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **embedding_dim** | Dimensão configurada do vetor de embedding. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Euclidean Distance** | Distância L2 entre embeddings; valores menores representam maior compatibilidade no regime documentado. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **EXAMINER** | Papel operacional destinado ao examinador/perito, com permissões limitadas pelas regras de autorização. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **External impostor** | Assinatura de indivíduo externo ao autor das referências, utilizada para representar a população impostora em calibração científica. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **FAR** | False Acceptance Rate: proporção de comparações impostoras aceitas incorretamente no ponto operacional avaliado. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Feature** | Capacidade operacional habilitável por licença, como treinamento, calibração, comparação, relatórios ou análise em lote. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **FORENSIC_CASE** | Protocolo forense em que a calibração depende de classificação prévia, por grafoscopista/perito, das assinaturas questionadas necessárias ao procedimento. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Forgery** | Assinatura não genuína atribuída ao autor-alvo; sua classificação e origem devem ser preservadas como metadados. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **forgery_negative_ratio** | Parâmetro de amostragem que controla a proporção de negativos oriundos de falsificações em populações de treinamento compatíveis. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **freeze_backbone** | Parâmetro que controla se os pesos do backbone permanecem congelados durante o treinamento. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **FRR** | False Rejection Rate: proporção de comparações genuínas rejeitadas incorretamente no ponto operacional avaliado. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **FULL** | Perfil operacional de licença com o conjunto mais amplo de capacidades, sujeito ao contrato e às políticas definidas. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **Genuine** | Assinatura produzida pelo próprio escritor/autor identificado. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **GPDSsyntheticSignature** | Corpus sintético de assinaturas da família GPDS documentado no SFAAO, utilizado somente quando obtido legitimamente. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Hash** | Resumo criptográfico utilizado para detectar alterações em arquivos/artifacts. Não substitui proveniência nem cadeia de custódia. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Independent External-Impostor Calibration** | Metodologia científica que estima o limiar com referências e impostores externos independentes, mantendo as assinaturas questionadas fora da estimação. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **IntegrityChecker** | Componente do bootstrap que verifica a integridade esperada dos arquivos protegidos do release. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Integrity manifest** | Manifesto que registra os arquivos esperados e seus identificadores de integridade. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Learning rate** | Hiperparâmetro que controla a magnitude das atualizações do otimizador durante o treinamento. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **LicenseContext** | Objeto resultante da validação da licença que expõe identidade, perfil, features, protocolos e limites aplicáveis. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **Machine fingerprint** | Identificador técnico da instalação/máquina utilizado pelo subsistema de licenciamento; não é senha nem chave privada. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Manifest** | Arquivo estruturado que descreve conteúdo, hashes ou metadados de um artifact/pacote conforme o subsistema correspondente. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **MCYT** | Família de bases biométricas MCYT. No contexto offline específico documentado pelo SFAAO, distinguir a variante MCYT-75. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **MCYT-75** | Corpus offline de assinaturas MCYT com 75 escritores, tratado separadamente de outras variantes MCYT. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **ModelConfig** | Configuração persistível da arquitetura e dos parâmetros estruturais do modelo. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **ModelFactory** | Componente que constrói a arquitetura compatível com o modo e a configuração selecionados. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Model package** | Conjunto versionado de pesos, configuração e metadados necessários para importar, registrar e utilizar um modelo de forma rastreável. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **ModelRepository** | Componente responsável pela persistência/recuperação dos artifacts de modelo segundo o formato do SFAAO. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **MobileNetV3** | Família de backbones leves suportada pelo SFAAO em configuração homologada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Optimizer** | Algoritmo que atualiza parâmetros treináveis a partir dos gradientes. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **PAIR** | Modo de treinamento baseado em pares de assinaturas e rótulo de relação compatível com aprendizagem métrica. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **PairRecord** | Registro estruturado de um par de treinamento/avaliação com identidades, rótulo, tipo, origem, seed e metadados. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **PBKDF2-HMAC-SHA-256** | Função de derivação de chave utilizada pelo SFAAO para armazenamento verificado de senhas conforme a configuração de segurança documentada. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **PDF report** | Relatório em formato PDF produzido pelo pipeline de laudo e sujeito às mesmas regras de rastreabilidade e revisão humana. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **positive_ratio** | Parâmetro de amostragem que controla a proporção de pares positivos em populações compatíveis. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **pretrained** | Parâmetro que indica uso de pesos previamente treinados no backbone quando disponíveis e metodologicamente apropriados. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Provenance** | Informação sobre origem, atividades, entidades e agentes que permite explicar como um resultado foi produzido. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **QUESTIONED** | Papel de assinatura questionada submetida à análise. No protocolo científico externo, permanece fora da estimação do limiar. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Quota** | Limite quantitativo imposto por perfil/licença a determinado recurso operacional. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **QuotaManager** | Componente que consulta e valida limites aplicáveis antes de operações sujeitas a quota. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **RC** | Referência do tipo RC utilizada no fluxo pericial conforme o cadastro e a metodologia do caso. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **Report UID** | Identificador estável de um relatório lógico, permitindo relacionar versões/formats complementares sem perda de rastreabilidade. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **ResNet18** | Backbone ResNet com 18 camadas na configuração suportada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **ResNet34** | Backbone ResNet com 34 camadas na configuração suportada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **ResNet50** | Backbone ResNet com 50 camadas na configuração suportada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **RO** | Referência do tipo RO utilizada no fluxo pericial conforme o cadastro e a metodologia do caso. | [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) |
| **ROC** | Receiver Operating Characteristic: representação da relação entre taxas de acerto/erro ao variar o limiar, conforme a definição adotada no módulo de calibração. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Scheduler** | Mecanismo que modifica a learning rate ao longo do treinamento segundo uma política configurada. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **SCIENTIFIC_EXTERNAL** | Protocolo científico de calibração que utiliza impostores externos independentes e exclui QUESTIONED da estimação do limiar. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Score** | Valor numérico resultante de uma comparação entre embeddings; sua interpretação depende da métrica e da direção de decisão. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **Secure time** | Estado temporal protegido utilizado para reduzir riscos associados a retrocesso simples do relógio do sistema. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **seed** | Valor utilizado para controlar fontes de aleatoriedade e apoiar reprodutibilidade de uma execução. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **SecurityBootstrap** | Sequência fail-closed de inicialização que valida storage, licença, fingerprint, tempo protegido, integridade e demais pré-condições de segurança. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Siamese network** | Arquitetura que aplica ramos com parâmetros compartilhados a entradas distintas para produzir representações comparáveis; não descreve obrigatoriamente todos os modos de modelo do SFAAO. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Signature UID** | Identificador estável de uma assinatura persistida no sistema. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Skilled forgery** | Falsificação produzida com conhecimento visual/intencional da assinatura-alvo. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **SQLite** | Banco relacional local utilizado pela aplicação para persistência estruturada de entidades e estados operacionais. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **Storage** | Estrutura persistente de arquivos operacionais, separada da base protegida do aplicativo. | [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) |
| **Threshold** | Sinônimo operacional de decision threshold quando o contexto estiver claro. | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| **TimeGuard** | Componente de segurança que valida coerência temporal protegida durante o bootstrap/operação definida. | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| **Traceability** | Capacidade de percorrer relações entre fontes, execuções, artifacts e resultados nos dois sentidos. | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| **TrainingConfig** | Configuração persistível de parâmetros de treinamento, amostragem, otimização, scheduler, early stopping e reprodutibilidade. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **TRIAL** | Perfil de licença de avaliação sujeito às features e cotas específicas definidas em [LICENSING-PTBR.md](LICENSING-PTBR.md). | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **TRIPLET** | Modo de treinamento baseado em triplas anchor/positive/negative para aprendizagem métrica. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Triplet Loss** | Função de perda que busca aproximar anchor e positive e afastar negative segundo uma margem. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **UsageTracker** | Componente que registra consumo cumulativo de recursos sujeitos à política de licenciamento. | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| **UTSig** | Dataset offline de assinaturas manuscritas utilizado mediante obtenção legítima e profile apropriado. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| **Validation** | Avaliação realizada em dados separados do ajuste principal para seleção/monitoramento de configuração e desempenho. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **WAL** | Write-Ahead Logging do SQLite; modo de journaling que exige cuidado em backup, cópia e migração do banco em uso. | [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) |
| **Weight decay** | Parâmetro de regularização aplicado pelo otimizador conforme sua implementação. | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| **Writer ID** | Identificador do escritor/autor dentro de um dataset ou contexto persistido; deve preservar escopo e proveniência. | [DATASETS-PTBR.md](DATASETS-PTBR.md) |

---

# 3. Mapa dos documentos canônicos

| Documento | Conceitos cuja definição detalhada pertence principalmente a ele |
|---|---|
| [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) | modos de treinamento, backbones, embeddings, losses, métricas, otimização, schedulers, configuração e reprodutibilidade |
| [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) | FAR, FRR, EER, ROC, score, limiar, direção de decisão, `FORENSIC_CASE`, `SCIENTIFIC_EXTERNAL`, calibração por impostores externos |
| [DATASETS-PTBR.md](DATASETS-PTBR.md) | datasets, profiles, writer IDs, pares, genuínas, falsificações e metadados de corpus |
| [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) | proveniência, UIDs, artifacts, execuções, reports e relações persistidas |
| [SECURITY-PTBR.md](SECURITY-PTBR.md) | autenticação, autorização, hashes, integridade, secure time, `TimeGuard`, `SecurityBootstrap` |
| [LICENSING-PTBR.md](LICENSING-PTBR.md) | perfis, features, protocolos, quotas, consumo e `LicenseContext` |
| [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) | produto distribuído, storage, runtime protegido, Windows/Linux, backup e implantação |
| [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) | sequência operacional de uso pelo ADMIN/EXAMINER e fluxo por caso |
| [REFERENCES-PTBR.md](REFERENCES-PTBR.md) | referências científicas, normativas e documentais centralizadas |

# 4. Regras de interpretação

- Termos de métrica devem ser interpretados junto com a **direção de decisão**.
- `Cosine Similarity` e `Euclidean Distance` não usam necessariamente a mesma regra de desigualdade no limiar.
- `Calibration` significa **calibração biométrica do limiar/ponto operacional**, não treinamento da rede.
- `FORENSIC_CASE` e `SCIENTIFIC_EXTERNAL` são protocolos distintos e não devem ser misturados.
- `MCYT` não deve ser usado como sinônimo automático de `MCYT-75`.
- `Hash`, `Provenance`, `Traceability`, `Audit trail` e `Chain of custody` são conceitos relacionados, porém distintos.
- Perfil de licença, papel do usuário e identidade do usuário são dimensões diferentes de controle.
- Integração técnica de um backbone/dataset não implica equivalência científica de desempenho com outras configurações.

# 5. Regra de estabilidade

Novos termos só devem ser adicionados a este glossário depois de existir uma definição canônica correspondente em um documento técnico, científico, normativo ou operacional do SFAAO.

O glossário não deve se tornar uma segunda fonte de verdade.
