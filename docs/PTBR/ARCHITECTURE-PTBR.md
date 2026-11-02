# SFAAO 2.4.0 — Arquitetura Oficial da Plataforma

**Documento:** `ARCHITECTURE-PTBR.md`  
**Versão da documentação:** 2.4  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Normativo para o release SFAAO 2.4.0

---

## 1. Objetivo

Este documento define a **arquitetura oficial de software do SFAAO 2.4.0**, seus limites, camadas, responsabilidades, dependências permitidas e regras de evolução.

No contexto deste documento, *arquitetura de software* corresponde à organização fundamental do sistema, expressa por seus elementos, relações, responsabilidades e princípios de projeto. A descrição arquitetural segue a orientação conceitual da **ISO/IEC/IEEE 42010:2022**, que estabelece requisitos para a descrição de arquiteturas de software e sistemas [1].

O objetivo é preservar:

- separação clara de responsabilidades;
- baixo acoplamento e alta coesão;
- manutenibilidade e testabilidade;
- reprodutibilidade científica;
- rastreabilidade pericial;
- compatibilidade entre treinamento, calibração e inferência;
- evolução controlada do software;
- segurança e auditabilidade compatíveis com uso institucional.

A arquitetura descrita aqui deve ser tratada como **normativa**. Mudanças estruturais somente devem ocorrer diante de bloqueador técnico concreto, correção crítica, requisito de segurança ou necessidade arquitetural comprovada.


**Limite de escopo:** as descrições estruturais e nomes internos devem ser confrontados com a árvore canônica de código 2.4.0 antes de serem apresentados como inventário executável exato; as regras científicas documentadas permanecem preservadas.
---

## 2. Escopo arquitetural

O SFAAO 2.4.0 é uma plataforma computacional modular para apoio à análise forense de assinaturas manuscritas offline. A arquitetura integra:

1. interface gráfica;
2. gerenciamento e orquestração de casos;
3. persistência estruturada;
4. armazenamento físico de artefatos;
5. núcleo científico de Inteligência Artificial;
6. treinamento configurável;
7. inferência biométrica;
8. calibração biométrica de limiar/ponto operacional;
9. comparação e consolidação de resultados;
10. geração de documentação pericial;
11. mecanismos de segurança, integridade e rastreabilidade.

O SFAAO **não deve ser interpretado como um único classificador ou uma única rede neural**. A plataforma foi projetada para permitir múltiplas arquiteturas, modos de treinamento, funções de perda, métricas, datasets e protocolos científicos, mantendo contratos arquiteturais estáveis.

---

## 3. Princípios arquiteturais

### 3.1 Responsabilidade única

Cada módulo, classe ou serviço deve possuir uma responsabilidade principal claramente delimitada. Esse princípio reduz efeitos colaterais e facilita teste, manutenção e auditoria.

### 3.2 Separação de responsabilidades

Interface, controle de fluxo, persistência, armazenamento, treinamento, calibração, inferência, relatórios e segurança constituem domínios distintos.

Em particular:

- a GUI não implementa matemática científica;
- controllers não duplicam algoritmos científicos;
- treinamento não executa lógica de autenticação;
- inferência não estima limiares de calibração;
- calibração não deve conter lógica de apresentação;
- relatórios não recalculam resultados científicos já persistidos.

### 3.3 Baixo acoplamento e alta coesão

Módulos devem se comunicar por interfaces, configurações, objetos de domínio e artefatos persistidos bem definidos. Implementações concretas devem permanecer encapsuladas no módulo responsável.

### 3.4 Modularidade e extensibilidade

Novos componentes científicos devem, sempre que possível, ser incorporados por **Factories, Repositories, Pipelines, Providers, Builders e Profiles**, sem exigir reescrita do restante da plataforma.

O padrão *Factory* encapsula a criação de objetos e reduz a dependência direta de classes concretas; o uso segue o princípio de padrões criacionais descrito por Gamma et al. [2]. O padrão *Repository* separa a lógica de domínio dos detalhes de persistência e acesso a dados, em linha com Fowler [3].

### 3.5 Reprodutibilidade

Toda execução científica relevante deve ser reconstruível a partir de configurações e artefatos persistidos. Seeds, parâmetros, versão do modelo, métricas, thresholds, configuração de treinamento e metadados associados não são preferências visuais: compõem a identidade técnica da execução.

### 3.6 Rastreabilidade

Uma decisão técnica deve poder ser vinculada, quando aplicável, a:

`usuário → caso → modelo → configuração → calibração → execução → resultado → relatório → versão do SFAAO`.

A especificação detalhada dessa cadeia é canonicamente definida em [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

### 3.7 Qualidade de software

A arquitetura favorece características como manutenibilidade, confiabilidade, segurança, compatibilidade e eficiência, em consonância com o modelo de qualidade de produto definido pela **ISO/IEC 25010:2023** [4].

---

## 4. Arquitetura geral em camadas


### Arquitetura consolidada do SFAAO 2.4.0

O release 2.4.0 consolida uma arquitetura em que **treinamento, calibração, inferência, persistência, relatórios, licenciamento e integridade são contratos separados**, ligados por metadados explícitos. A mudança central é que a unidade estatística usada para calibrar a decisão deve ser a mesma unidade estatística usada durante a inferência.

```text
GUI PyQt5
   ↓
Controllers / Orquestração
   ├── Training / Evaluation
   ├── Calibration pipelines
   │      └── AlignedScoreAggregator (LORO / A(N-1))
   ├── Comparison pipelines
   │      └── folds de decisão + consolidação conservadora
   ├── Repositories / SQLite schema v6
   ├── Reports / provenance fingerprint schema v3
   └── SecurityBootstrap / Licensing / Integrity
          ↓
   storage persistente + artefatos científicos
```

Contratos estatísticos centrais:

- `PAIRWISE_SCORE` / **A1** é score elementar e, em LORO, componente de proveniência;
- `LORO_MEAN` / **A(N−1)** é a unidade decisória para calibração e inferência alinhadas; com cinco referências, cada fold é **A4**;
- **A(N)** permanece estatística descritiva e não recebe o threshold LORO;
- calibração e comparação persistem `decision_statistic`, tamanhos de agregação, `fold_strategy` e contagem de referências/folds;
- `ALIGNED` identifica compatibilidade entre os domínios de calibração e inferência; `MISMATCH` bloqueia novas execuções; `LEGACY_MISMATCH` identifica registros históricos incompatíveis.

Para Cosine Similarity:

$$
\tau_{operacional}=\max(\tau_{forensic},\tau_{EER})
$$

Para Euclidean Distance:

$$
\tau_{operacional}=\min(\tau_{forensic},\tau_{EER})
$$

Os folds LORO não são novamente promediados e não existe votação por maioria. Todos os folds compatíveis, incompatíveis ou inconclusivos produzem, respectivamente, `STABLE_COMPATIBLE`, `STABLE_INCOMPATIBLE` ou `STABLE_INCONCLUSIVE`; qualquer mistura produz `REFERENCE_SENSITIVE` e consolidação `INCONCLUSIVE`.

```text
┌────────────────────────────────────────────────────────────┐
│                     INTERFACE GRÁFICA                      │
│                         PyQt5                              │
└────────────────────────────┬───────────────────────────────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────────┐
│                       CONTROLLERS                          │
│ casos • usuários • modelos • datasets • relatórios       │
└──────────────────────┬───────────────────┬─────────────────┘
                       │                   │
                       ▼                   ▼
┌──────────────────────────────┐   ┌─────────────────────────┐
│     NÚCLEO CIENTÍFICO AI     │   │ PERSISTÊNCIA / STORAGE │
│ training • inference         │   │ SQLite • filesystem     │
│ calibration • evaluation     │   │ services • artefatos    │
└───────────────┬──────────────┘   └────────────┬────────────┘
                │                                │
                └──────────────┬─────────────────┘
                               ▼
                    ┌──────────────────────┐
                    │ REPORTS / AUDITORIA │
                    │ DOCX • PDF • logs   │
                    └──────────────────────┘
```

### 4.1 Regra de dependência

A direção preferencial de dependência é:

```text
GUI
 ↓
Controllers
 ↓
Repositories / Services / AI Pipelines
 ↓
Factories / Configurações / Domínio
 ↓
Implementações concretas
 ↓
Persistência de resultados e artefatos
```

Camadas inferiores **não devem depender da GUI**.

---

## 5. Estrutura raiz do release

A estrutura operacional principal confirmada para o release SFAAO 2.4.0 é:

```text
SFAAO/
├── ai/
├── assets/
├── controllers/
├── database/
├── models/
├── reports/
├── scripts/
├── security/
├── storage/
├── storage_services/
├── ui/
├── app.py
└── requirements.txt
```

### 5.1 Responsabilidades da raiz

| Componente | Responsabilidade arquitetural |
|---|---|
| `app.py` | ponto de entrada da aplicação e inicialização controlada |
| `ai/` | algoritmos, modelos, treinamento, avaliação, calibração e inferência |
| `assets/` | recursos estáticos da aplicação |
| `controllers/` | coordenação entre GUI, domínio, persistência, serviços e IA |
| `database/` | schema, conexão, repositories e persistência estruturada |
| `models/` | área de modelos disponibilizados/gerenciados pelo release |
| `reports/` | consolidação e geração documental |
| `scripts/` | pipelines auxiliares, manutenção e tarefas técnicas controladas |
| `security/` | autenticação, autorização, licenciamento e mecanismos de proteção |
| `storage/` | artefatos persistentes vinculados à operação e aos casos |
| `storage_services/` | operações de armazenamento físico e abstrações associadas |
| `ui/` | janelas, diálogos, widgets e interação com o usuário |
| `requirements.txt` | dependências Python declaradas para o ambiente suportado |

No pacote de distribuição limpo, `models/active/`, `models/default/` e `storage/cases/` devem permanecer vazios até que a instalação/uso legítimo produza conteúdo. `storage/license.sfaao` não integra o pacote de distribuição.

---

## 6. Interface gráfica (`ui/`)

A GUI é responsável por:

- interação com o usuário;
- captura e validação básica de entradas;
- apresentação de casos, modelos, configurações e resultados;
- exibição de progresso;
- acionamento de controllers;
- visualização de artefatos e mensagens operacionais.

A GUI **não deve**:

- calcular FAR, FRR, EER ou thresholds;
- construir arquiteturas neurais diretamente;
- executar regras de autenticação por conta própria;
- manipular diretamente o schema do banco;
- duplicar lógica de calibração, inferência ou treinamento.

Widgets Qt devem ser criados e manipulados na thread apropriada da interface. Processamentos demorados podem utilizar workers/threads controlados, mas sem transferir ownership de widgets para threads secundárias.

---

## 7. Controllers (`controllers/`)

Controllers constituem a camada de orquestração da aplicação.

Fluxo típico:

```text
GUI
 ↓
Controller
 ↓
Repository / Service / AI Pipeline
 ↓
Resultado persistido
 ↓
Controller
 ↓
GUI
```

Controllers podem validar pré-condições de operação e coordenar serviços, mas **não devem duplicar a matemática científica** do núcleo `ai/`.

---

## 8. Persistência (`database/`)


### Persistência estatística e schema v6

O schema v6 separa proveniência, estatística descritiva e decisão por fold:

- `calibrations`: `decision_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`;
- `calibration_results`: `threshold_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`, `fold_count`;
- `comparison_runs`: domínios de calibração/inferência, tamanhos de agregação, `fold_strategy`, `reference_count`, `fold_count`, `alignment_status`, `eer_threshold` e `operational_threshold`;
- `questioned_results`: A(N) descritivo, estatística decisória, contagens por decisão e `stability_status`;
- `questioned_decision_folds`: referência excluída, A(N−1), thresholds e decisão técnica de cada fold.

A persistência do resultado consolidado e de seus folds é **atômica**: uma execução LORO válida não pode existir com conjunto parcial de folds.

O SFAAO utiliza SQLite para metadados estruturados e relacionamentos operacionais.

A persistência pode abranger, conforme o schema do release:

- usuários;
- casos;
- assinaturas e seus metadados;
- modelos registrados;
- calibrações;
- execuções de comparação;
- resultados;
- dados descritivos de relatórios;
- auditoria e histórico operacional.

Arquivos grandes, imagens, pesos neurais, gráficos e documentos não devem ser armazenados indiscriminadamente como blobs no banco quando o contrato arquitetural indicar persistência no filesystem.

A definição detalhada de proveniência e vínculos entre registros pertence a [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

---

## 9. Armazenamento (`storage/` e `storage_services/`)

`storage/` mantém artefatos físicos gerados ou utilizados pela aplicação. `storage_services/` encapsula operações de leitura, escrita, organização e proteção desse armazenamento.

Exemplos de artefatos:

- referências do caso;
- assinaturas questionadas;
- resultados de comparação;
- calibrações;
- gráficos;
- relatórios;
- artefatos técnicos vinculados a uma execução.

A estrutura física deve preservar isolamento entre casos e não deve permitir que nomes de arquivo fornecidos pelo usuário determinem caminhos arbitrários fora das áreas autorizadas.

Regras específicas de proteção, permissões, integridade e licenciamento são definidas em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

## 10. Núcleo científico (`ai/`)

O diretório `ai/` contém a lógica científica da plataforma. Ele deve permanecer independente de detalhes de apresentação e governança.

Conceitualmente, o núcleo inclui responsabilidades como:

```text
ai/
├── backbones/
├── calibration/
├── config/
├── datasets/
├── distances/
├── inference/
├── losses/
├── models/
├── training/
└── utilitários científicos
```

A estrutura física exata pode receber arquivos auxiliares sem alterar os contratos abaixo.

### 10.1 Isolamento científico

O núcleo `ai/`:

- não autentica usuários;
- não decide permissões institucionais;
- não conhece widgets Qt;
- não escolhe caminhos arbitrários de filesystem;
- recebe parâmetros, configurações e caminhos já autorizados pelas camadas responsáveis.

---

## 11. Framework modular de IA

Fluxo conceitual de treinamento:

```text
DATASET
  ↓
DATASET PROFILE / PROVIDER
  ↓
PAIR / TRIPLET / CLASSIFICATION DATA BUILDING
  ↓
PREPROCESSAMENTO
  ↓
BACKBONE
  ↓
EMBEDDING ou REPRESENTAÇÃO
  ↓
LOSS COMPATÍVEL COM O MODO
  ↓
OPTIMIZER / SCHEDULER
  ↓
TRAINING
  ↓
VALIDATION / EVALUATION
  ↓
MÉTRICAS E ARTEFATOS
  ↓
MODEL PACKAGE
```

As definições científicas detalhadas de datasets pertencem a [DATASETS-PTBR.md](DATASETS-PTBR.md); as definições de métricas biométricas e calibração pertencem a [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

## 12. Configuração como contrato reprodutível

O SFAAO utiliza objetos/configurações persistíveis para separar **o que deve ser executado** de **como cada implementação concreta é construída**.

### 12.1 `ModelConfig`

Representa a identidade estrutural do modelo, incluindo parâmetros como:

- `backbone_name`;
- `pretrained`;
- `freeze_backbone`;
- `embedding_dim`;
- `dropout`;
- `loss_name`;
- `distance_name`;
- demais parâmetros necessários à reconstrução compatível da arquitetura.

### 12.2 `TrainingConfig`

Representa a configuração operacional do treinamento, incluindo, conforme aplicável:

- epochs;
- batch size;
- learning rate;
- weight decay;
- seed;
- número de pares/triplets/amostras;
- proporções de sampling;
- optimizer;
- scheduler;
- early stopping;
- dispositivo de execução;
- parâmetros específicos do modo de treinamento.

Configurações utilizadas em uma execução científica devem ser persistidas juntamente com o modelo ou vinculadas inequivocamente a ele.

---

## 13. Factories

Factories desacoplam a seleção configurável da implementação concreta.

O framework contempla, conforme a implementação do release:

- `BackboneFactory`;
- `ModelFactory`;
- `LossFactory`;
- `DistanceFactory`;
- `OptimizerFactory`;
- `SchedulerFactory`.

### Regra normativa

A GUI não deve instanciar diretamente um `ResNet50`, `EfficientNet`, loss, optimizer ou métrica concreta. Ela fornece configuração; o núcleo resolve a implementação pela factory correspondente.

Essa regra permite adicionar novas implementações com impacto mínimo nas camadas superiores.

---

## 14. Arquiteturas neurais e backbones

Um **backbone** é o extrator de características responsável por transformar a imagem pré-processada em representação intermediária. Sua escolha influencia capacidade representacional, custo computacional, memória e tempo de treinamento/inferência.

O SFAAO 2.4.0 contempla:

- ResNet18;
- ResNet34;
- ResNet50;
- EfficientNet-B0;
- ConvNeXt-Tiny;
- MobileNetV3;
- futuras arquiteturas compatíveis com o contrato da plataforma.

As ResNets utilizam aprendizagem residual conforme He et al. [5]. EfficientNet utiliza escalonamento composto de profundidade, largura e resolução conforme Tan e Le [6]. MobileNetV3 foi projetada com foco em eficiência computacional em dispositivos/restrições de recursos [7]. ConvNeXt moderniza a arquitetura convolucional incorporando decisões de projeto inspiradas na evolução recente de modelos visuais [8].

A inclusão de um novo backbone deve ocorrer por extensão do módulo correspondente e registro na factory, sem exigir lógica específica na GUI.

---

## 15. Embeddings e arquiteturas de comparação

Um **embedding** é uma representação vetorial aprendida de uma entrada. Em metric learning, o objetivo é organizar o espaço de representação de forma que amostras semanticamente relacionadas tenham relação geométrica útil para comparação.

Redes siamesas utilizam ramos com pesos compartilhados para produzir representações comparáveis; esse princípio foi aplicado historicamente à verificação de assinaturas por Bromley et al. [9] e à aprendizagem de métricas por Hadsell, Chopra e LeCun [10].

No SFAAO:

- a dimensão do embedding é configurável;
- a dimensão integra a identidade técnica do modelo;
- treinamento, carregamento, calibração e inferência devem reconstruir configuração compatível;
- não se deve assumir que todo modo de treinamento é obrigatoriamente uma única classe concreta `SiameseNet`.

### 15.1 Modos de treinamento

A plataforma suporta conceitualmente:

- `PAIR`;
- `TRIPLET`;
- `CLASSIFICATION`.

PAIR e TRIPLET operam naturalmente sobre representações/embeddings comparáveis. CLASSIFICATION constitui estratégia supervisionada distinta e pode utilizar uma cabeça classificadora ou formulação angular compatível.

A seleção `modo × loss × arquitetura` deve ser validada pelo núcleo científico antes da execução.

---

## 16. Losses, métricas e regra de compatibilidade

O SFAAO contempla funções de perda como:

- Contrastive Loss;
- Cosine Contrastive Loss;
- Triplet Loss;
- ArcFace, quando usada em modo/configuração compatível.

As definições matemáticas canônicas dessas losses devem permanecer em documentação científica apropriada e em [REFERENCES-PTBR.md](REFERENCES-PTBR.md), evitando duplicação desnecessária neste documento arquitetural.

### 16.1 Distâncias e similaridades

O framework contempla pelo menos:

- Cosine Similarity;
- Euclidean Distance.

A arquitetura deve preservar a **semântica da métrica** em todas as camadas:

```text
Cosine Similarity:
maior score = maior similaridade
aceitação típica: score >= threshold

Euclidean Distance:
menor distância = maior proximidade
aceitação típica: distance <= threshold
```

Nenhuma camada de GUI ou relatório pode inverter essa regra.

---

## 17. Treinamento

O subsistema de treinamento é responsável por:

- construção dos dados segundo o modo selecionado;
- preprocessamento consistente;
- criação do modelo via factories;
- cálculo da loss;
- otimização;
- scheduler, quando configurado;
- validação;
- seleção do melhor estado;
- early stopping;
- avaliação final;
- persistência do pacote do modelo e artefatos.

O treinamento **não define o limiar operacional de um caso pericial por simples reutilização automática do threshold de treino**. Métricas produzidas durante treinamento/validação caracterizam o modelo e o experimento; a calibração biométrica é uma etapa distinta.

---

## 18. Separação obrigatória: treinamento, calibração e inferência

As três etapas são arquitetural e semanticamente distintas:

```text
TREINAMENTO
aprende parâmetros do modelo

CALIBRAÇÃO BIOMÉTRICA
estima/seleciona limiar ou ponto operacional
para um protocolo definido

INFERÊNCIA
aplica modelo + métrica + calibração válida
às amostras destinadas à avaliação/comparação
```

O termo **“calibração da rede neural” não deve ser utilizado como descrição genérica da etapa de calibração do SFAAO**.

A definição canônica, os protocolos `FORENSIC_CASE` e `SCIENTIFIC_EXTERNAL`, FAR, FRR, EER, ROC, threshold e a metodologia Independent External-Impostor Calibration são tratados em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

### 18.1 Regra caso–modelo–calibração

Uma calibração operacional deve permanecer vinculada ao contexto que a produziu. A arquitetura não deve tratar thresholds de caso como constantes globais reutilizáveis indiscriminadamente entre modelos, métricas ou cenários distintos.

---

## 19. Inferência e comparação

O subsistema de inferência deve:

1. carregar o modelo e sua configuração;
2. reconstruir preprocessamento e arquitetura compatíveis;
3. gerar representações ou saídas segundo o modo aplicável;
4. calcular a métrica configurada;
5. aplicar a semântica correta da métrica;
6. utilizar calibração/threshold válido para o contexto;
7. devolver resultado estruturado para persistência e apresentação.

Inferência não deve treinar o modelo e não deve recalibrar silenciosamente o threshold.

---

## 20. ModelRepository e pacote do modelo

`ModelRepository` encapsula o armazenamento, descoberta e reconstrução dos modelos, evitando que GUI e controllers conheçam detalhes internos dos pesos.

Um **model package** deve preservar, conforme o modo e a configuração:

```text
pesos do modelo
model_config.json
training_config.json
evaluation_metrics.json
histórico de treinamento
plots/artefatos científicos
metadados suficientes para reconstrução
```

Nomes físicos podem variar conforme implementação do release, mas o contrato é preservar informação suficiente para identificar e reconstruir tecnicamente o modelo.

Modelos de homologação não fazem parte do pacote público/limpo do release.

---

## 21. Relatórios (`reports/`)


### Proveniência dos laudos no release 2.4.0

A geração de DOCX/PDF incorpora metadados científicos persistidos e um fingerprint canônico de proveniência **schema v3**. O material canônico inclui, quando aplicável, domínio decisório, folds, thresholds, `alignment_status` e `stability_status`. O relatório materializa resultados persistidos e não recalcula silenciosamente scores ou decisões.

A camada de relatórios materializa informações já validadas e persistidas.

Um relatório pode consolidar:

- identificação do caso;
- referências e questionadas pertinentes;
- modelo utilizado;
- configuração relevante;
- protocolo/calibração aplicável;
- threshold operacional;
- resultados e estatísticas;
- versão da plataforma;
- data de emissão;
- elementos necessários à rastreabilidade.

**Relatórios não devem recalcular scores ou thresholds de forma independente do pipeline científico.**

---

## 22. Segurança arquitetural

A segurança deve atravessar as camadas sem contaminar o núcleo científico com lógica de governança.

Princípios:

- autenticação e autorização antes da execução de operações protegidas;
- validação de caminhos e arquivos;
- separação entre dados estruturados e artefatos físicos;
- ausência de segredos ou chaves privadas no release;
- ausência de licença vinculada à máquina do desenvolvedor;
- integridade verificável do pacote distribuído;
- logs e auditoria compatíveis com as políticas do sistema.

A especificação canônica encontra-se em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

## 23. Regras para evolução da arquitetura

1. Não duplicar lógica científica.
2. Não colocar regra de negócio ou matemática no código de GUI.
3. Não colocar treinamento dentro do módulo de inferência.
4. Não colocar inferência dentro da implementação de calibração.
5. Não misturar autenticação/autorização ao núcleo científico.
6. Novos backbones devem entrar via abstração/factory correspondente.
7. Novas losses devem preservar contratos existentes.
8. Novas métricas devem declarar explicitamente sua semântica de decisão.
9. Novos datasets devem utilizar profile/provider/adapter apropriado.
10. Alterações de persistência devem preservar rastreabilidade e migração controlada.
11. Mudanças arquiteturais devem preservar compatibilidade retroativa sempre que tecnicamente possível.
12. Código novo deve utilizar type hints e documentação concisa em APIs públicas quando aplicável.
13. Métodos excessivamente longos devem ser decompostos por responsabilidade.
14. Arquivos excessivamente grandes devem ser refatorados quando isso melhorar coesão e auditabilidade, sem fragmentação artificial.

---

## 24. Política arquitetural do release

O pacote oficial não deve distribuir:

- datasets de terceiros;
- imagens provenientes de datasets de terceiros;
- casos reais;
- assinaturas reais;
- modelos treinados de homologação;
- banco de desenvolvimento;
- snapshots e backups históricos;
- caches Python;
- chaves privadas;
- licença vinculada à máquina do desenvolvedor;
- estado temporal específico da máquina de desenvolvimento;
- documentação interna confidencial de propriedade intelectual.

Essas restrições fazem parte da fronteira arquitetural e de segurança do produto distribuível.

---

## 25. Estabilidade arquitetural

A estrutura base do SFAAO 2.4.0 é considerada congelada para o fechamento documental do release.

Novos recursos devem **estender os contratos existentes**, em vez de reorganizar arbitrariamente diretórios, dependências ou responsabilidades.

Uma mudança estrutural somente é justificável quando houver pelo menos um dos seguintes fatores:

- falha de segurança;
- inconsistência arquitetural comprovada;
- impossibilidade técnica de cumprir requisito legítimo;
- correção de erro crítico;
- necessidade de compatibilidade suportada que não possa ser obtida por extensão modular.

---

## 26. Documentos relacionados

Este documento define a arquitetura. Conceitos especializados possuem documentos canônicos próprios:

- [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) — calibração biométrica, thresholds, FAR, FRR, EER, ROC e protocolos;
- [DATASETS-PTBR.md](DATASETS-PTBR.md) — datasets, profiles, adapters, organização e restrições de dados;
- [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) — proveniência, cadeia de rastreabilidade e vínculos entre artefatos;
- [SECURITY-PTBR.md](SECURITY-PTBR.md) — autenticação, autorização, integridade, proteção e distribuição segura;
- [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) — ambiente e instalação;
- [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md) — operação guiada da plataforma;
- [GLOSSARY-PTBR.md](GLOSSARY-PTBR.md) — terminologia canônica;
- [REFERENCES-PTBR.md](REFERENCES-PTBR.md) — bibliografia científica e normativa consolidada.

Quando um conceito estiver definido canonicamente em outro documento, este arquivo deve apenas registrar sua função arquitetural e referenciar a fonte adequada.

---

## 27. Referências utilizadas neste documento

[1] ISO/IEC/IEEE 42010:2022. *Software, systems and enterprise — Architecture description*. International Organization for Standardization, 2022.

[2] E. Gamma, R. Helm, R. Johnson, and J. Vlissides. *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley, 1994.

[3] M. Fowler. *Patterns of Enterprise Application Architecture*. Addison-Wesley, 2002.

[4] ISO/IEC 25010:2023. *Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE) — Product quality model*. International Organization for Standardization, 2023.

[5] K. He, X. Zhang, S. Ren, and J. Sun. “Deep Residual Learning for Image Recognition.” *Proceedings of CVPR*, 2016, pp. 770–778. DOI: 10.1109/CVPR.2016.90.

[6] M. Tan and Q. V. Le. “EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks.” *Proceedings of ICML*, 2019.

[7] A. Howard et al. “Searching for MobileNetV3.” *Proceedings of ICCV*, 2019.

[8] Z. Liu et al. “A ConvNet for the 2020s.” *Proceedings of CVPR*, 2022.

[9] J. Bromley, I. Guyon, Y. LeCun, E. Säckinger, and R. Shah. “Signature Verification Using a Siamese Time Delay Neural Network.” *Advances in Neural Information Processing Systems*, 1994, pp. 737–744.

[10] R. Hadsell, S. Chopra, and Y. LeCun. “Dimensionality Reduction by Learning an Invariant Mapping.” *Proceedings of CVPR*, 2006, pp. 1735–1742. DOI: 10.1109/CVPR.2006.100.

---

## 28. Declaração normativa final

Este documento substitui descrições arquiteturais anteriores que conflitem com a estrutura e os contratos do release SFAAO 2.4.0 aqui definidos.

Em caso de divergência entre documentação histórica e o release congelado, prevalecem:

1. a estrutura operacional validada do release;
2. os contratos normativos definidos neste documento;
3. os documentos canônicos especializados referenciados na Seção 26.

