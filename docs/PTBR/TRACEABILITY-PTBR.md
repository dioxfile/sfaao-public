# SFAAO 2.4.0 — Rastreabilidade, Proveniência e Auditabilidade

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Especificação técnica oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Objetivo

Este documento define o modelo canônico de **rastreabilidade**, **proveniência** e **auditabilidade** do SFAAO 2.4.0.

Seu objetivo é garantir que um resultado técnico possa ser relacionado, de forma reconstruível, aos dados, agentes, configurações, modelos, calibrações, execuções e artefatos que o produziram.

A arquitetura é definida em [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md); datasets em [DATASETS-PTBR.md](DATASETS-PTBR.md); calibração biométrica em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md); mecanismos criptográficos, secure time, permissões e hardening em [SECURITY-PTBR.md](SECURITY-PTBR.md).


**Limite de verificação:** nomes de schema, UIDs e fingerprints aqui descritos são contratos documentais; a conformidade de uma migração ou banco específico deve ser verificada na versão e nos registros auditados.
---

# 2. Escopo

Este documento cobre:

- proveniência;
- rastreabilidade;
- audit trail;
- identificação estável de entidades;
- relação usuário–caso–evidência;
- proveniência de datasets;
- proveniência de modelos;
- proveniência de calibrações;
- execução de comparações;
- scores individuais e resultados agregados;
- versões de relatórios;
- timestamps;
- integridade referencial;
- hashes e manifests;
- falhas e estados;
- reconstrução de uma execução;
- cadeia de custódia computacional.

---

# 3. Definição canônica de proveniência

O W3C PROV-DM define proveniência como informação sobre **entidades, atividades e agentes** envolvidos na produção ou entrega de um dado ou objeto e sobre as relações entre esses elementos [1].

No SFAAO, a abstração é:

```text
ENTIDADES
caso, assinatura, dataset, modelo, calibração, score, relatório, artifact

ATIVIDADES
upload, validação, treinamento, calibração, comparação, geração de relatório

AGENTES
usuário, perito, administrador, software/versão
```

A proveniência deve permitir responder:

```text
o quê?
quem?
quando?
com quais dados?
com qual modelo?
com qual calibração?
com quais parâmetros?
qual execução?
qual resultado?
qual artefato foi produzido?
```

---

# 4. Definição canônica de rastreabilidade

**Rastreabilidade** é a capacidade de percorrer as relações de proveniência de um resultado até seus elementos de origem e, inversamente, identificar quais resultados dependem de determinado elemento.

No SFAAO ela é bidirecional:

```text
origem → execução → resultado
resultado → execução → origem
```

Exemplo:

```text
resultado questionada
  → comparison_run
  → calibração
  → modelo
  → assinaturas RC/RO
  → caso
  → usuário/perito
```

---

# 5. Rastreabilidade não é auditoria

**Rastreabilidade** explica dependências e origem.

**Auditoria** registra ou permite revisar eventos relevantes, incluindo ações humanas e mudanças de estado.

Exemplo:

- `comparison_runs` relaciona ciência e execução;
- `signature_role_audit` registra mudanças de classificação pericial;
- logs de segurança podem registrar autenticação ou eventos de integridade.

Um mesmo mecanismo pode contribuir para ambos, mas os conceitos não devem ser confundidos.

---

# 6. Rastreabilidade não é integridade

**Integridade** trata da detecção ou prevenção de alteração indevida.

Um hash pode demonstrar que dois conteúdos diferem, mas não explica sozinho quem produziu o arquivo, por qual processo ou a qual execução ele pertence.

Portanto:

```text
hash ≠ proveniência completa
proveniência ≠ garantia criptográfica de integridade
```

Os mecanismos de integridade são detalhados em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 7. Rastreabilidade não é cadeia de custódia

**Cadeia de custódia** é o registro e controle da posse, manipulação, transferência e preservação de evidência ao longo do tempo.

ISO/IEC 27037 fornece diretrizes para identificação, coleta, aquisição e preservação de evidência digital [2]. NIST SP 800-86 recomenda documentação detalhada das ações, responsáveis, ferramentas e integridade da evidência [3].

O SFAAO fornece rastreabilidade computacional que pode **apoiar** uma cadeia de custódia institucional, mas não substitui os procedimentos jurídicos, administrativos ou periciais exigidos pela organização competente.

---

# 8. Cadeia canônica do SFAAO

A cadeia lógica principal é:

```text
USUÁRIO / PERITO
      ↓
CASO
      ↓
ASSINATURAS
RC / RO / QUESTIONED
      ↓
MODELO
      ↓
CALIBRAÇÃO
      ↓
COMPARISON RUN
      ↓
SCORES INDIVIDUAIS
      ↓
RESULTADO POR QUESTIONADA
      ↓
RELATÓRIO
```

Datasets, configurações, versões e artifacts são ligados aos pontos correspondentes dessa cadeia.

---

# 9. Identificadores estáveis

Entidades importantes devem possuir identificadores estáveis sempre que o schema fornecer essa capacidade.

Exemplos confirmados:

- `case_uid`;
- `signature_uid`;
- `calibration_uid`;
- IDs de modelo;
- ID/identificador de `comparison_run`;
- IDs de relatório;
- IDs de dataset.

IDs inteiros locais podem ser usados como chaves relacionais, mas não devem ser tratados como a única identidade externa quando existir UID estável.

---

# 10. Versionamentos independentes

O SFAAO possui diferentes eixos de versão:

```text
versão da plataforma
versão do schema SQLite
versão do protocolo científico
versão/configuração do modelo
versão/corpus do dataset
versão do documento/relatório
```

Esses valores não são intercambiáveis.

Exemplo: atualizar o schema não significa alterar automaticamente o protocolo de calibração.

---

# 11. Modelo relacional central


### Extensões do schema v6 para LORO

| Estrutura | Campos centrais 2.4.0 |
|---|---|
| `calibrations` | `decision_statistic`, `aggregation_size`, `fold_strategy`, `reference_count` |
| `calibration_results` | `threshold_statistic`, `aggregation_size`, `fold_strategy`, `reference_count`, `fold_count` |
| `comparison_runs` | domínios de calibração/inferência, tamanhos de agregação, `fold_strategy`, `reference_count`, `fold_count`, `alignment_status`, `eer_threshold`, `operational_threshold` |
| `questioned_results` | A(N) descritivo, estatística decisória, contagens por classe e `stability_status` |
| `questioned_decision_folds` | fold, referência excluída, A(N−1), thresholds e decisão técnica |

`similarity_results` preserva A1 referência×questionada como proveniência. Em LORO, esses scores não recebem isoladamente um veredicto derivado de threshold A(N−1).

`save_questioned_result_with_folds` é transacional: resultado consolidado e folds são persistidos no mesmo contexto de escrita; falhas provocam rollback.

O schema 2.x utiliza SQLite para metadados, rastreabilidade, auditoria e relacionamentos, enquanto imagens, pesos e artifacts científicos permanecem no filesystem e são referenciados pelo banco.

Tabelas centrais incluem:

| Tabela | Função de rastreabilidade |
|---|---|
| `schema_versions` | versão/migrações do schema |
| `users` | agente da aplicação |
| `expert_profiles` | identidade profissional associada |
| `cases` | unidade central da perícia |
| `case_metadata` | metadados descritivos e conclusão humana |
| `signatures` | RC, RO e QUESTIONED |
| `signature_role_audit` | histórico de papéis/classificações |
| `datasets` | catálogo de bases |
| `trained_models` | catálogo de modelos |
| `calibrations` | execução/configuração caso+modelo |
| `calibration_samples` | amostras usadas na calibração |
| `calibration_results` | métricas e artifacts |
| `comparison_runs` | execução identificável |
| `similarity_results` | scores individuais |
| `questioned_results` | agregação por questionada |
| `reports` | versões de relatórios |

---

# 12. Relações principais

```text
USERS
├── EXPERT_PROFILES
└── CASES
    ├── CASE_METADATA
    ├── SIGNATURES
    │   └── SIGNATURE_ROLE_AUDIT
    ├── CALIBRATIONS ───── TRAINED_MODELS
    │   ├── CALIBRATION_SAMPLES
    │   └── CALIBRATION_RESULTS
    ├── COMPARISON_RUNS ── TRAINED_MODELS
    │   ├── SIMILARITY_RESULTS
    │   └── QUESTIONED_RESULTS
    └── REPORTS
```

Essa estrutura expressa dependências; não é apenas uma organização de armazenamento.

---

# 13. Proveniência do usuário e do perito

Quando uma atividade é atribuível a um agente humano, o sistema deve preservar o identificador disponível desse agente.

Exemplos:

- criador do caso;
- executor de uma calibração;
- executor de uma comparação;
- emissor de relatório;
- autor de mudança de papel pericial.

A rastreabilidade não exige replicar dados pessoais em todos os registros; IDs relacionais são preferíveis.

---

# 14. Proveniência do caso

O caso é a unidade agregadora principal.

Elementos relevantes incluem, conforme schema:

```text
case_uid
usuário/perito associado
nome/descrição
status
diretório de storage
timestamps
```

`case_uid` fornece identidade estável independente do ID inteiro local.

Artefatos científicos não devem perder sua relação com o caso de origem.

---

# 15. Proveniência das assinaturas

A tabela `signatures` distingue pelo menos:

```text
RC
RO
QUESTIONED
```

Cada assinatura deve poder ser relacionada a:

- caso;
- UID/ID;
- arquivo físico;
- nome original quando preservado;
- página/ordem quando aplicável;
- papel pericial;
- timestamps relevantes.

Um caminho de arquivo isolado não é uma identidade científica suficiente.

---

# 16. Auditoria de papel pericial

Mudanças de classificação humana das questionadas são eventos relevantes e devem ser preservadas pelo mecanismo `signature_role_audit` quando aplicável.

A finalidade é impedir que uma alteração de interpretação humana apague silenciosamente o estado anterior.

A definição das classes periciais utilizadas por protocolos científicos pertence a [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 17. Proveniência de datasets

A rastreabilidade de dataset deve preservar os elementos definidos canonicamente em [DATASETS-PTBR.md](DATASETS-PTBR.md), incluindo, quando aplicável:

```text
dataset_name
corpus/version
profile
source
writer population
split
seed
sample/pair provenance
```

Um profile não substitui a identificação do corpus físico.

---

# 18. Proveniência do modelo

Um modelo deve ser reconstruível a partir de pesos e metadados suficientes.

A proveniência mínima inclui, quando aplicável:

```text
model_id
model package
ModelConfig
TrainingConfig
training mode
backbone
loss
distance
embedding_dim
seed
dataset/protocol de treino
métricas de avaliação
versão da plataforma
```

O package do modelo e seu registro no `ModelRepository` formam parte da cadeia.

---

# 19. Proveniência da calibração

Uma calibração é vinculada a **caso + modelo** e não deve ser interpretada como threshold global sem contexto.

Campos documentados incluem:

```text
calibration_uid
case_id
model_id
created_by
calibration_mode
reference_type
distance_name
status
artifact_dir
configuration_path
parameters_json
is_selected
timestamps
```

Os modos centrais são documentados em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 20. Proveniência dos resultados de calibração

`calibration_results` pode relacionar:

```text
eer
eer_threshold
forensic_threshold / limiar calibrado
target_far
far
frr
genuine_count
impostor_count
genuine_mean / genuine_std
impostor_mean / impostor_std
bootstrap confidence interval
paths de métricas e gráficos
```


### Semântica de `target_far` no SFAAO 2.4.0

No SFAAO 2.4.0, `target_far` registra o FAR-alvo operacional utilizado pela calibração. Na interface oficial de calibração, esse valor é fixado por `CalibrationWindow.TARGET_FAR = 0.01`, correspondente a **1%**, e não é configurável pelo usuário na GUI.

A calibração procura, entre os thresholds avaliados, um limiar que satisfaça empiricamente:

$$
\widehat{\mathrm{FAR}}(\tau)\leq 0.01.
$$

Esse critério é distinto do EER. O `eer_threshold` é estimado independentemente no ponto em que FAR e FRR se aproximam. Portanto, `target_far = 0.01` **não força `EER <= 1%`**.

Na política operacional vigente, o threshold associado ao FAR-alvo e o threshold EER da calibração corrente são combinados de forma conservadora conforme a orientação da métrica: para similaridade, utiliza-se o maior limiar; para distância, o menor.

O campo interno legado `threshold_forensic`, quando presente em artefatos compatíveis, pode representar o threshold associado ao critério de FAR-alvo/calibração. Sua denominação histórica não deve ser confundida com uma redefinição do protocolo `FORENSIC_CASE`.

O requisito de FAR menor ou igual a 1% refere-se ao **FAR empírico observado na população de calibração**. Ele não constitui garantia matemática de FAR populacional futuro menor ou igual a 1%.

Nomes internos legados podem ser preservados por compatibilidade histórica; a documentação deve explicar sua semântica sem renomeação destrutiva do schema.

---

# 21. Proveniência da execução de comparação

Cada `comparison_run` liga explicitamente, quando aplicável:

```text
case_id
model_id
calibration_id
created_by
reference_type
distance_name
threshold
status
parameters/artifacts
timestamps
```

Essa relação impede que um score seja interpretado sem conhecer o modelo, métrica e calibração usados.

---

# 22. Scores individuais

`similarity_results` preserva a granularidade referência×questionada.

Uma entrada detalhada pode conter:

```text
comparison_run_id
reference_signature_id
questioned_signature_id
score
distance_value
threshold_used
pair_verdict
created_at
```

Também podem ser recuperados UID, caminhos, nomes e páginas das assinaturas relacionadas.

A rastreabilidade individual é necessária mesmo quando a interface mostra apenas valores agregados.

---

# 23. Resultado agregado por questionada

`questioned_results` relaciona uma questionada a uma execução e pode armazenar:

```text
score_count
mean_score
std_score
min_score
max_score
threshold
technical_decision
timestamps
```

O resultado agregado deve continuar ligado aos scores individuais que o originaram.

---

# 24. Regra de decisão e métrica

A proveniência deve registrar a métrica e a orientação de decisão.

Exemplos:

```text
cosine similarity:
aceitar se score >= threshold

euclidean distance:
aceitar se distance <= threshold
```

Sem `distance_name`, `threshold` e regra correspondente, um valor numérico isolado pode ser interpretado incorretamente.

A definição matemática das métricas pertence à documentação científica/arquitetural correspondente.

---

# 25. Proveniência de relatórios


### Fingerprint de proveniência de relatório — schema v3

O relatório 2.4.0 utiliza um fingerprint canônico de proveniência com `schema_version = 3`. O material canônico inclui, quando aplicável, domínio decisório, folds, thresholds, `alignment_status`, `stability_status` e identificadores necessários para auditar a decisão. Esse fingerprint identifica o conteúdo científico/operacional que fundamenta o relatório e não substitui o hash do arquivo final.

O relatório é um **artefato derivado** de dados e resultados previamente persistidos.

Regra normativa:

> O pipeline de relatório não deve recalcular silenciosamente scores, thresholds ou decisões com parâmetros diferentes dos associados às execuções registradas.

Um relatório deve preservar ou permitir resolver, conforme aplicável:

```text
case
modelo
calibração
comparison run
threshold
versão da plataforma
data de emissão
versão/status do relatório
```

---

# 26. Versões de relatório

Uma nova emissão não deve tornar uma versão anterior indistinguível.

Quando o schema/pipeline suporta versionamento, deve ser possível determinar:

- qual versão foi emitida;
- quando;
- por quem;
- de quais resultados derivou;
- se houve falha de geração.

Relatórios complementares ou regenerados devem referenciar a mesma ciência quando não houver nova execução científica.

---

# 27. Estados e falhas

Execuções devem possuir estado explícito quando suportado.

Exemplos:

```text
PENDING / RUNNING
COMPLETED
FAILED
```

Uma falha não deve ser convertida silenciosamente em sucesso nem apagada apenas porque uma tentativa posterior concluiu.

O registro de erro deve ser suficiente para distinguir:

```text
não executado
executado com falha
executado com sucesso
```

---

# 28. Timestamps

Timestamps são parte da proveniência temporal.

Devem ser usados para ordenar e contextualizar:

- criação;
- atualização;
- início;
- conclusão;
- falha;
- emissão;
- alteração de papel;
- seleção de calibração.

A confiança criptográfica/operacional no relógio e o mecanismo de **secure time** pertencem a [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 29. Integridade referencial

O runtime utiliza foreign keys e proteções para evitar destruição de dependências históricas.

Regra:

```text
registro utilizado por evidência histórica
→ não remover livremente
```

Exemplos:

- calibração já usada;
- assinatura já comparada;
- caso com execução científica;
- modelo referenciado por comparação.

A exclusão física deve respeitar dependências e políticas de retenção.

---

# 30. Artifacts no filesystem

Imagens, pesos, gráficos, JSONs e documentos podem permanecer no filesystem.

O banco deve possuir referências suficientes para localizar e contextualizar esses artifacts.

Um artifact relevante deve, quando aplicável, ser relacionado a:

```text
caso
execução
tipo
caminho autorizado
versão
hash/manifest
timestamps
```

A simples existência física de um arquivo não constitui registro de proveniência.

---

# 31. Hashes

Um hash criptográfico é uma função que mapeia um conteúdo de tamanho arbitrário para um resumo de tamanho fixo.

Conceitualmente:

$$
h = H(m)
$$

onde `m` é o conteúdo e `h` seu digest.

No SFAAO, hashes podem ser utilizados para:

- verificar alteração de arquivos;
- registrar integridade de packages;
- conferir manifests;
- comparar cópias.

Um hash não prova sozinho autoria, origem ou legitimidade.

Algoritmos permitidos e políticas de integridade são definidos em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 32. Manifests

Um **manifest** de integridade associa um conjunto de caminhos/identificadores a seus hashes e metadados.

Exemplo conceitual:

```text
artifact_1 → SHA-256(...)
artifact_2 → SHA-256(...)
artifact_3 → SHA-256(...)
```

O manifest deve ser versionado ou protegido de modo compatível com sua finalidade; caso contrário, alterar simultaneamente arquivo e manifest reduziria seu valor probatório.

A implementação concreta pertence à política de segurança.

---

# 33. Reprodutibilidade

Rastreabilidade é pré-condição para reprodutibilidade, mas não é equivalente a ela.

Uma execução reprodutível precisa, além das relações de proveniência, registrar parâmetros suficientes para reconstrução técnica:

```text
software/version
dataset/profile
modelo/configs
seed
modo
parâmetros
métrica
threshold/calibração
artifacts
```

Diferenças de hardware, bibliotecas e kernels podem impedir determinismo bit a bit mesmo quando a proveniência está completa.

---

# 34. Backup, exportação e migração

Backup ou migração não devem quebrar relações entre banco e filesystem.

Para SQLite aberto, deve-se utilizar mecanismo consistente como a SQLite Backup API quando aplicável.

Após restauração/migração devem ser verificadas:

- foreign keys;
- caminhos;
- UIDs;
- artifacts;
- manifests/hashes quando utilizados;
- versão do schema;
- compatibilidade do software.

---

# 35. Privacidade e minimização

Rastreabilidade não justifica duplicação indiscriminada de dados pessoais.

A cadeia deve preferir:

- IDs internos;
- UIDs;
- relacionamentos;
- metadados necessários;

em vez de replicar nomes, e-mails ou informações sensíveis em todos os artifacts.

Regras detalhadas de proteção pertencem a [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 36. Reconstrução mínima de uma decisão

Para explicar uma decisão técnica, deve ser possível recuperar, no mínimo:

```text
1. caso
2. questionada
3. conjunto RC ou RO usado
4. modelo e configuração
5. calibração selecionada
6. métrica
7. threshold
8. comparison_run
9. scores individuais
10. agregação
11. decisão técnica
12. usuário/agente e timestamps
13. versão da plataforma
```

Se algum desses elementos não se aplicar ao protocolo, a ausência deve ser explicável.

---

# 37. Fluxo de auditoria recomendado

```text
selecionar resultado
  ↓
identificar questioned_result
  ↓
abrir comparison_run
  ↓
listar similarity_results
  ↓
resolver assinatura questionada e referências
  ↓
resolver calibration_id
  ↓
resolver model_id
  ↓
revisar parâmetros/artifacts
  ↓
resolver caso e agente
  ↓
conferir relatório derivado
```

Esse fluxo deve ser possível sem recalcular a execução original.

---

# 38. Regra contra recomputação silenciosa

Nenhuma tela, relatório ou exportação deve substituir retrospectivamente uma execução persistida por novo cálculo sem criar ou identificar a nova atividade.

Se houver recomputação:

```text
nova execução
→ novo identificador
→ novos timestamps
→ novos parâmetros/artifacts
```

A execução anterior deve permanecer distinguível conforme política de retenção.

---

# 39. Relação com ARCHITECTURE.md

[ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) define onde as responsabilidades residem.

[TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) define como as entidades dessas camadas se relacionam ao longo do ciclo científico.

Em caso de conflito estrutural, a arquitetura normativa deve ser atualizada junto com este documento e o changelog.

---

# 40. Relação com DATASETS.md

[DATASETS-PTBR.md](DATASETS-PTBR.md) define a proveniência mínima de datasets, profiles, splits, writer IDs, sampling e PairRecord.

Este documento conecta esses elementos aos modelos, calibrações e resultados posteriores.

---

# 41. Relação com CALIBRATION.md

[CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) é canônico para protocolos, thresholds, FAR/FRR/EER e Independent External-Impostor Calibration.

Este documento apenas define como cada execução e seus artifacts devem permanecer ligados ao caso, modelo, amostras e resultados.

---

# 42. Relação com SECURITY.md

[SECURITY-PTBR.md](SECURITY-PTBR.md) será canônico para:

- autenticação;
- autorização;
- secure time;
- hashing policy;
- manifest protection;
- filesystem permissions;
- encryption when applicable;
- backup protection;
- hardening.

Este documento não duplica essas definições.

---

# 43. Regras normativas resumidas

1. Todo resultado científico deve possuir origem identificável.
2. `case_uid`, `signature_uid` e outros UIDs devem ser preservados quando disponíveis.
3. Dataset, modelo, calibração e comparison run são contextos distintos.
4. Score individual não deve existir conceitualmente sem sua execução.
5. Resultado agregado deve continuar ligado aos scores de origem.
6. Relatório deriva de resultados persistidos; não recalcula silenciosamente.
7. Alteração humana de papel pericial deve ser auditável quando suportada.
8. Estados de falha devem permanecer distinguíveis.
9. Foreign keys e políticas de retenção devem proteger dependências históricas.
10. Hash é evidência de integridade, não proveniência completa.
11. Timestamps precisam de contexto; secure time é definido em [SECURITY-PTBR.md](SECURITY-PTBR.md).
12. Recomputation exige nova atividade identificável.
13. Versionamentos de software, schema, protocolo, dataset e modelo não devem ser confundidos.
14. Rastreabilidade deve respeitar minimização de dados pessoais.

---

# 44. Referências

[1] W3C, *PROV-DM: The PROV Data Model*, W3C Recommendation, 30 Apr. 2013. https://www.w3.org/TR/2013/REC-prov-dm-20130430/

[2] ISO/IEC 27037:2012, *Information technology — Security techniques — Guidelines for identification, collection, acquisition and preservation of digital evidence*. International Organization for Standardization, 2012.

[3] K. Kent, S. Chevalier, T. Grance, and H. Dang, *Guide to Integrating Forensic Techniques into Incident Response*, NIST Special Publication 800-86, 2006. doi: 10.6028/NIST.SP.800-86.

[4] D. A. M. José, L. de Medeiros Marques, A. Bueno, and R. B. Z. da Silva, “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification,” artigo aceito para publicação no *IEEE Access*, 2026 (dados bibliográficos finais pendentes).

---

# 45. Estabilidade documental

Esta é a definição canônica de rastreabilidade e proveniência do SFAAO 2.4.0.

Mudanças futuras no schema, armazenamento ou pipelines devem manter a capacidade de explicar como cada resultado foi produzido.

Uma refatoração que preserve valores numéricos, mas destrua a ligação entre dados, agentes, configurações e execuções, deve ser considerada regressão de rastreabilidade.
