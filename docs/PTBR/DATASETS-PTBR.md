# SFAAO 2.4.0 — Datasets, Profiles e Governança de Dados

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Especificação técnica oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Objetivo

Este documento define a política oficial de datasets do SFAAO 2.4.0, a abstração por **Dataset Profiles**, os componentes de cadastro e validação, as regras de composição de amostras para treinamento e avaliação e os requisitos de proveniência, reprodutibilidade, privacidade e licenciamento.

A arquitetura geral da plataforma é definida em [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md). A calibração biométrica e seus protocolos são definidos em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md). Este documento não redefine esses conceitos; descreve exclusivamente como os dados e seus metadados são representados, validados e disponibilizados aos pipelines.


**Distribuição 2.4.0:** o instalador não concede licença nem direito de redistribuição sobre datasets de terceiros. Importação, caminhos e perfis devem ser conferidos na instalação e no contexto efetivamente selecionado.
---

# 2. Escopo

Este documento cobre:

- dataset físico;
- Dataset Profile;
- DatasetReference;
- DatasetProvider;
- DatasetCatalog;
- loaders e factories;
- cadastro e validação;
- identificação de escritor;
- classes genuína/falsificação;
- PAIR, TRIPLET e CLASSIFICATION sob a perspectiva dos dados;
- composição treino/validação;
- tipos de pares negativos;
- seeds e amostragem;
- proveniência;
- dataset shift;
- uso de datasets externos em calibração científica;
- política de aquisição e redistribuição;
- privacidade e cadeia de custódia.

---

# 3. Princípio central: profile não é dataset

O SFAAO separa explicitamente o **conteúdo físico de uma base** de seu **perfil estrutural**.

```text
DATASET FÍSICO
imagens + diretórios + arquivos + metadados de origem
        │
        ▼
DATASET PROFILE
regras para interpretar a estrutura
        │
        ▼
DatasetProvider
validação + acesso normalizado
        │
        ▼
pipelines do SFAAO
```

Um **Dataset Profile** descreve como interpretar uma base legitimamente obtida. Ele não contém, por definição, as imagens biométricas da base.

**Regra normativa:**

> Profiles/adapters podem ser distribuídos com o SFAAO quando autorizado. Datasets de terceiros e suas imagens não são redistribuídos pelo SFAAO.

---

# 4. Definição de dataset

Neste documento, **dataset** é uma coleção organizada de amostras e metadados utilizada por um processo computacional de treinamento, validação, avaliação ou calibração.

Para verificação de assinaturas offline, uma amostra é tipicamente uma imagem raster de uma assinatura manuscrita digitalizada ou sintetizada, associada, quando disponível, a:

- identificador do escritor;
- classe da amostra;
- origem;
- sessão;
- versão da base;
- informações de captura;
- metadados necessários à interpretação do protocolo.

O SFAAO não presume que todos os datasets possuam a mesma organização física nem a mesma taxonomia.

---

# 5. Dataset Profile

## 5.1 Definição

O **Dataset Profile** é a abstração que traduz a estrutura específica de uma base para o contrato interno do SFAAO.

Um profile pode definir ou inferir:

- estrutura de diretórios;
- padrões de nomes de arquivos;
- extensões aceitas;
- writer ID;
- classificação genuína;
- classificação falsificação;
- subconjuntos/splits quando existentes;
- regras de descoberta de imagens;
- metadados do corpus;
- particularidades necessárias ao loader.

## 5.2 Finalidade

A separação por profiles permite:

- integrar novas bases sem reescrever o núcleo científico;
- evitar caminhos hard-coded;
- manter a lógica de interpretação próxima ao dataset;
- preservar rastreabilidade;
- validar a base antes do treinamento;
- reutilizar pipelines de PAIR, TRIPLET e CLASSIFICATION.

## 5.3 Restrições

Um profile distribuído no release não deve conter:

- imagens do dataset;
- credenciais;
- tokens;
- caminhos privados do desenvolvedor;
- dados pessoais desnecessários;
- cópias de arquivos protegidos por terceiros;
- suposições silenciosas que alterem a semântica das classes.

---

# 6. Componentes do subsistema de datasets

Os componentes confirmados na arquitetura 2.x incluem:

| Componente | Responsabilidade |
|---|---|
| `ai/datasets/dataset_reference.py` / `DatasetReference` | referência identificável a uma base cadastrada |
| `ai/datasets/dataset_profile.py` / `DatasetProfile` | contrato estrutural do dataset |
| `ai/datasets/dataset_provider.py` / `DatasetProvider` | acesso normalizado e validação |
| `ai/datasets/dataset_catalog.py` / `DatasetCatalog` | catálogo de datasets disponíveis |
| `ai/datasets/dataset_factory.py` | construção de objetos de dataset |
| `ai/datasets/dataset_loader.py` | carregamento de amostras |
| `ai/datasets/signature_triplet_dataset.py` | materialização de dados para modo TRIPLET |
| `ai/datasets/signature_classification_dataset.py` | materialização de dados para modo CLASSIFICATION |
| `ai/datasets/profiles/` | profiles/adapters de estruturas suportadas |
| `controllers/dataset_controller.py` / `DatasetController` | orquestração da governança de datasets |
| `ui/dataset_manager_window.py` / `DatasetManagerWindow` | interface de cadastro e validação |

A construção de pares para PAIR e para populações estatísticas é centralizada pela infraestrutura de builders do SFAAO.

---

# 7. Cadastro e validação

O fluxo normativo é:

```text
ADMIN
  ↓
Gerenciador de Datasets
  ↓
Adicionar Dataset
  ↓
nome + profile + versão + diretório
  ↓
DatasetReference
  ↓
DatasetProvider
  ↓
validação estrutural/classificação
  ↓
varredura de imagens
  ├─ formato suportado?
  ├─ writer_id resolvido?
  ├─ genuína?
  ├─ falsificação?
  ├─ conflito de classe?
  └─ arquivo não classificado?
  ↓
estatísticas
  ↓
validação final
  ↓
persistência no catálogo SQLite
  ↓
dataset habilitado
```

Um dataset não deve ser tratado como operacional apenas porque seu diretório existe. A estrutura e a classificação precisam ser validadas pelo profile correspondente.

---

# 8. Catálogo local e persistência

O SQLite mantém um **catálogo local** de datasets legitimamente obtidos e cadastrados.

O registro no catálogo representa metadados e referências de acesso. Ele **não transfere propriedade**, licença ou direitos sobre a base.

Imagens permanecem no filesystem da instituição/usuário. O banco armazena a identificação e os metadados necessários ao relacionamento com os pipelines.

---

# 9. Identidade do escritor

O `writer_id` representa a identidade lógica do escritor dentro do domínio de um dataset/profile.

Regras:

1. IDs só são comparáveis dentro do contexto em que foram definidos.
2. O mesmo valor textual em datasets diferentes não implica a mesma pessoa.
3. A proveniência do dataset/profile deve acompanhar o ID.
4. Operações de PAIR/TRIPLET dependem da semântica correta do writer ID.
5. Um profile que não consiga resolver identidade de forma confiável não deve inventar IDs.

Uma chave conceitual segura é:

```text
(dataset_id, dataset_version, writer_id)
```

---

# 10. Classes de amostra

Para corpora de verificação offline, as classes mais comuns são:

- **genuine** — assinatura genuína do escritor declarado;
- **forgery** — falsificação atribuída ao escritor-alvo;
- **unknown/unclassified** — amostra que o profile não conseguiu classificar.

O nome físico do arquivo não deve ser usado diretamente pelos pipelines científicos. A interpretação é responsabilidade do profile/provider.

---

# 11. Dataset shift

## 11.1 Definição

Há **dataset shift** quando a distribuição estatística dos dados utilizada no desenvolvimento não coincide com a distribuição encontrada no domínio operacional. Formalmente, uma mudança pode ser representada por:

$$
P_{\mathrm{dev}}(X,Y) \neq P_{\mathrm{oper}}(X,Y)
$$

ou por alterações em suas distribuições marginais/condicionais [7].

Em assinaturas offline, diferenças podem decorrer de:

- população de escritores;
- idioma ou sistema de escrita;
- resolução;
- scanner;
- fundo;
- instrumento de escrita;
- compressão;
- pré-processamento;
- natureza das falsificações;
- condições de aquisição;
- dados sintéticos versus reais.

## 11.2 Aplicação no SFAAO

A arquitetura por profiles não elimina dataset shift. Ela garante que a **estrutura e a proveniência** da base sejam conhecidas e rastreáveis.

A mitigação estatística no domínio forense, quando aplicável, pertence aos protocolos definidos em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 12. Datasets de referência suportados por profiles

O termo **suportado** significa que a arquitetura possui ou pode possuir profile/adaptador capaz de interpretar uma cópia legitimamente obtida. Não significa redistribuição.

## 12.1 GPDSsyntheticSignature

Corpus offline sintético do Grupo de Procesado Digital de Señales (ULPGC).

Características de referência:

- 4.000 indivíduos sintéticos;
- 24 assinaturas genuínas por identidade;
- 30 falsificações por identidade;
- imagens equivalentes a 600 dpi;
- corpus voltado a assinatura offline.

O site oficial do GPDS também diferencia esse corpus do `GPDSsyntheticOnLineOffLineSignature` de 10.000 identidades e registra que corpora GPDS reais antigos podem possuir restrições de disponibilidade [1], [2].

No SFAAO, o profile deve registrar exatamente qual corpus/versão está sendo utilizado.

## 12.2 CEDAR

Base clássica de assinatura offline do Center of Excellence for Document Analysis and Recognition.

Características de referência:

- 55 escritores;
- 24 genuínas por escritor;
- 24 falsificações por escritor;
- 2.640 imagens no corpus descrito por Kalera, Srihari e Xu [3].

## 12.3 BHSig260

Corpus multi-script composto por dois subconjuntos:

- **Bengali:** 100 escritores;
- **Hindi:** 160 escritores;
- 24 genuínas por escritor;
- 30 falsificações por escritor.

Total: 260 escritores e 14.040 imagens. A literatura frequentemente avalia os subconjuntos Bengali e Hindi separadamente [4], [5].

O profile deve preservar o subconjunto/script como metadado e não colapsar silenciosamente as duas populações.

## 12.4 UTSig

Base persa de assinaturas offline.

Características de referência:

- 115 classes/escritores;
- 8.280 imagens;
- 27 genuínas por classe;
- 45 amostras de falsificação por classe;
- aquisição digitalizada a 600 dpi;
- imagens em escala de cinza no corpus descrito pelos autores [6].

O protocolo original inclui particularidades de coleta e tipos de falsificação. O profile deve preservar essas particularidades em metadados quando relevantes.

## 12.5 MCYT / MCYT-75

**MCYT** designa uma família de corpora biométricos. Para uso offline no contexto desta documentação, o corpus deve ser identificado explicitamente.

O **MCYT-75 offline signature corpus** contém, como referência:

- 75 escritores;
- 15 assinaturas genuínas por escritor;
- 15 falsificações habilidosas por escritor;
- 2.250 imagens.

Não se deve confundir `MCYT-75` com variantes online/bimodais como MCYT100 ou MCYT330, que possuem protocolos e quantidades diferentes [8], [9].

**Regra:** persistir o nome exato do corpus e sua versão/origem.

---

# 13. Datasets próprios e institucionais

O SFAAO também pode operar com:

- dataset próprio;
- base sintética própria;
- acervo institucional;
- dataset de terceiro autorizado.

Uma base nova requer profile/adaptador compatível e validação de:

- classificação;
- writer IDs;
- formatos;
- estrutura;
- proveniência;
- regras de acesso.

O fato de uma base ser institucional não dispensa os requisitos de rastreabilidade e proteção de dados.

---

# 14. PAIR — visão dos dados

No modo PAIR, cada registro representa duas amostras e um relacionamento.

Representação conceitual:

```text
(image_1, image_2, label, pair_type, writer_1, writer_2, provenance)
```

Uma convenção possível é:

```text
label = 1 → semelhante/match
label = 0 → diferente/non-match
```

A convenção concreta utilizada pelo pipeline/loss deve ser persistida; não deve ser inferida apenas pelo nome do arquivo.

---

# 15. Pares positivos

Para um escritor com $n$ amostras genuínas, o número máximo de pares genuína×genuína não ordenados é:

$$
N_{GG} = \binom{n}{2} = \frac{n(n-1)}{2}
$$

Exemplo: para 24 genuínas,

$$
\binom{24}{2}=276
$$

pares genuína×genuína distintos podem ser formados antes de qualquer estratégia de subamostragem.

---

# 16. Pares negativos

A infraestrutura deve permitir distinguir semanticamente tipos de negativos, por exemplo:

- genuína × falsificação do mesmo escritor-alvo;
- genuína × genuína de outro escritor;
- falsificação × falsificação;
- impostor de escritor distinto;
- impostor externo, quando o protocolo o exigir.

Negativos de naturezas diferentes não devem ser tratados como equivalentes sem registro de `pair_type`.

A distinção é importante porque a dificuldade estatística de uma falsificação habilidosa pode ser diferente da de um escritor aleatório.

---

# 17. `positive_ratio`

`positive_ratio` representa a proporção planejada de pares positivos na amostra construída pelo modo PAIR.

Exemplo:

```text
positive_ratio = 0.5
```

indica um objetivo aproximado de 50% de pares positivos, quando a disponibilidade de dados permitir.

O valor planejado e a composição efetivamente obtida devem ser rastreáveis.

---

# 18. `forgery_negative_ratio`

`forgery_negative_ratio` controla a fração planejada dos pares negativos formada por falsificações associadas ao escritor-alvo, quando o dataset permite essa classificação.

Ele possibilita distinguir:

```text
negativos por falsificação habilidosa
versus
negativos por escritor distinto
```

O valor não altera a semântica original do dataset; apenas orienta a estratégia de amostragem.

---

# 19. TRIPLET — visão dos dados

No modo TRIPLET, cada unidade possui:

```text
anchor
positive
negative
```

com:

- `anchor` e `positive` semanticamente associados à mesma identidade compatível com o protocolo;
- `negative` associado a identidade/classe incompatível.

O builder deve preservar writer IDs, origem e regra que produziu o negativo.

A definição matemática da Triplet Loss pertence à documentação arquitetural/científica correspondente; aqui interessa apenas o contrato dos dados.

---

# 20. CLASSIFICATION — visão dos dados

No modo CLASSIFICATION, cada imagem é associada a uma classe/identidade definida pelo dataset.

Conceitualmente:

```text
image → writer/class label
```

O mapeamento de classes deve ser:

- determinístico;
- persistível;
- reconstruível;
- compatível entre treino e validação.

A numeração interna de classes não deve substituir o writer ID original na proveniência.

---

# 21. Separação treino/validação

Treinamento e validação devem ser conjuntos logicamente independentes.

No modo PAIR, os parâmetros podem incluir:

```text
train_pairs
val_pairs
positive_ratio
forgery_negative_ratio
seed
```

A estratégia de split deve ser registrada. Quando o protocolo exigir generalização para escritores não vistos, a separação por identidade deve impedir que o mesmo escritor atravesse subconjuntos de forma indevida.

O SFAAO não deve assumir uma única política de split para todos os experimentos; a política utilizada é parte da proveniência experimental.

---

# 22. Prevenção de data leakage

**Data leakage** ocorre quando informação que deveria estar indisponível a uma etapa de treinamento/seleção influencia indevidamente o modelo ou a avaliação.

No contexto de datasets de assinatura, devem ser evitados, conforme o protocolo:

- mesma imagem em treino e validação;
- pares duplicados indevidamente entre splits;
- escritor de teste vazando para treino quando o protocolo é writer-independent;
- questionadas forenses utilizadas para estimar parâmetros que deveriam ser independentes;
- cópias transformadas da mesma amostra atravessando splits sem controle.

A separação específica entre calibração científica e questionadas é definida em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 23. Seeds e amostragem reprodutível

Toda operação aleatória relevante deve receber seed persistível quando tecnicamente possível.

A seed pode controlar:

- escolha de pares;
- escolha de triplets;
- seleção de negativos;
- seleção de impostores;
- seleção de subconjuntos;
- embaralhamento.

Exemplo:

```text
seed = 42
```

A seed melhora a reprodutibilidade, mas não garante determinismo absoluto entre todos os hardwares, kernels e versões de bibliotecas.

---

# 24. `PairRecord` e proveniência das populações

Registros de pares devem permitir reconstrução do processo de geração. Um contrato representativo do SFAAO contém:

```text
pair_id
image_1
image_2
writer_1
writer_2
label
pair_type
dataset_name
dataset_profile
source
seed
metadata
```

Quando aplicável, devem ser incluídos também versão do dataset/profile, split, execução e demais identificadores necessários.

O objetivo é que um resultado agregado nunca dependa de uma população de pares cuja origem seja desconhecida.

---

# 25. Pré-processamento e compatibilidade

O dataset fornece amostras; o pipeline científico define o pré-processamento.

O SFAAO exige que o tratamento usado na construção do modelo seja compatível com o usado nas etapas posteriores associadas ao mesmo modelo.

Operações possíveis incluem:

- grayscale;
- binarização;
- remoção de fundo;
- preservação de proporção;
- padding;
- resize;
- normalização;
- conversão para tensor.

A existência de uma resolução original específica no dataset não obriga o modelo a trabalhar diretamente nessa resolução.

---

# 26. Dataset externo na calibração científica


### Datasets externos no domínio LORO

No `SCIENTIFIC_EXTERNAL` 2.4.0, cada query externa gera folds A(N−1) excluindo sucessivamente uma referência, mantendo a população non-match no mesmo domínio estatístico da inferência. Questionadas do caso não participam dessa seleção nem da estimação do threshold.

Datasets externos podem fornecer população de impostores para protocolos científicos definidos em [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

Regras de dados:

1. a origem do dataset externo deve ser identificada;
2. o profile e a versão devem ser registrados;
3. as amostras efetivamente utilizadas devem ser rastreáveis;
4. a seed/subamostragem deve ser registrada;
5. as assinaturas questionadas não devem ser reclassificadas como amostras externas;
6. treinamento, calibração biométrica e inferência permanecem etapas distintas.

A metodologia de **Independent External-Impostor Calibration** é descrita no documento de calibração e no manuscrito científico revisado e ressubmetido ao IEEE Access, associado ao SFAAO [10].

---

# 27. Política de aquisição

Para utilizar uma base de terceiro, o usuário/instituição deve:

1. identificar a base correta;
2. consultar os termos de uso vigentes;
3. obter autorização quando necessária;
4. obter os arquivos de fonte legítima;
5. armazená-los em infraestrutura autorizada;
6. selecionar/configurar o profile;
7. validar a estrutura;
8. utilizar os dados somente dentro dos direitos concedidos.

O SFAAO não contorna autenticação, paywall, controle de acesso ou licenciamento de datasets.

---

# 28. Política de redistribuição

O release oficial não contém:

- GPDS;
- CEDAR;
- BHSig260;
- UTSig;
- MCYT;
- imagens provenientes desses datasets;
- outros datasets de terceiros.

A presença de um profile/adaptador **não constitui redistribuição do corpus** e não concede direitos de uso sobre o conteúdo.

---

# 29. Privacidade, proteção de dados e cadeia de custódia

Antes da importação de datasets próprios, institucionais ou reais, a instituição deve verificar:

- base legal;
- finalidade;
- direitos de uso;
- minimização de dados;
- retenção;
- anonimização ou pseudonimização quando aplicável;
- controle de acesso;
- cadeia de custódia quando aplicável;
- requisitos regulatórios e institucionais.

O SFAAO fornece mecanismos técnicos; a legitimidade jurídica da coleta e do tratamento depende do contexto de uso.

---

# 30. Integridade e caminhos

Profiles distribuídos não devem conter caminhos absolutos da máquina do desenvolvedor.

Referências físicas devem ser resolvidas a partir de configuração local/catálogo autorizado.

Mudanças de diretório não devem alterar a identidade científica do dataset. Por outro lado, alteração de conteúdo, corpus, versão ou classificação deve ser tratada como mudança relevante de proveniência.

---

# 31. Reprodutibilidade mínima de um experimento

Um experimento que dependa de dataset deve ser capaz de identificar, no mínimo:

```text
dataset_name
dataset_version/corpus
dataset_profile
dataset_source
split strategy
writer population
sample counts
pair/triplet/classification strategy
seed
preprocessing contract
training configuration
platform/version
```

Quando possível, hashes/manifests dos artefatos locais podem complementar a proveniência, conforme [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

---

# 32. Relação com TRACEABILITY.md

[TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) é a definição canônica para cadeia de proveniência e auditabilidade do SFAAO.

Este documento estabelece apenas quais informações de dataset devem estar disponíveis para essa cadeia.

Em caso de repetição, prevalece a definição canônica do documento de rastreabilidade.

---

# 33. Relação com CALIBRATION.md

[CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) define:

- calibração biométrica do limiar/ponto operacional;
- FAR, FRR e EER;
- protocolos FORENSIC_CASE e SCIENTIFIC_EXTERNAL;
- Independent External-Impostor Calibration;
- seleção e persistência dos thresholds.

[DATASETS-PTBR.md](DATASETS-PTBR.md) define a **origem, interpretação e governança dos dados** utilizados por esses protocolos.

---

# 34. Relação com ARCHITECTURE.md

[ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) define:

- separação de responsabilidades;
- pipelines;
- factories;
- controllers;
- persistência;
- regras arquiteturais.

[DATASETS-PTBR.md](DATASETS-PTBR.md) especializa o contrato do subsistema `ai/datasets/` sem modificar a arquitetura geral.

---

# 35. Checklist para novo Dataset Profile

Antes de considerar um profile pronto:

- [ ] nome e versão definidos;
- [ ] corpus exato identificado;
- [ ] fonte documentada;
- [ ] termos de uso revisados;
- [ ] diretórios interpretados;
- [ ] formatos de imagem identificados;
- [ ] writer ID validado;
- [ ] genuínas classificadas;
- [ ] falsificações classificadas;
- [ ] arquivos não classificados detectados;
- [ ] conflitos detectados;
- [ ] nenhum caminho privado do desenvolvedor;
- [ ] nenhum arquivo do dataset incorporado ao código/profile;
- [ ] split/protocolo documentado quando aplicável;
- [ ] proveniência preservada;
- [ ] validação funcional executada.

---

# 36. Regras normativas resumidas

1. **Profile não é dataset.**
2. **Dataset de terceiro não é redistribuído.**
3. **Suporte não implica licença.**
4. **Writer ID sempre possui contexto de dataset/profile.**
5. **Classes não são inferidas silenciosamente fora do profile.**
6. **PAIR/TRIPLET/CLASSIFICATION devem preservar proveniência.**
7. **Tipos de negativos devem permanecer distinguíveis.**
8. **Splits e seeds devem ser registráveis.**
9. **Dataset externo de calibração deve ser identificável.**
10. **Questionadas não podem contaminar protocolo científico independente.**
11. **MCYT deve indicar o corpus exato; MCYT-75 não é sinônimo de todas as variantes MCYT.**
12. **Mudanças de corpus/versão exigem atualização de proveniência.**

---

# 37. Referências

[1] M. A. Ferrer, M. Diaz-Cabrera, and A. Morales, “Static signature synthesis: A neuromotor inspired approach for biometrics,” *IEEE Transactions on Pattern Analysis and Machine Intelligence*, vol. 37, no. 3, pp. 667–680, 2015. doi: 10.1109/TPAMI.2014.2343220.

[2] Grupo de Procesado Digital de Señales — Universidad de Las Palmas de Gran Canaria, “GPDS — Toolbox & Databases,” seção `GPDSsyntheticSignature`. Acesso em 28 ago. 2026. https://gpds.ulpgc.es/downloadnew/download.htm

[3] M. K. Kalera, S. N. Srihari, and A. Xu, “Offline signature verification and identification using distance statistics,” *International Journal of Pattern Recognition and Artificial Intelligence*, vol. 18, no. 7, pp. 1339–1360, 2004. doi: 10.1142/S0218001404003630.

[4] S. Pal, U. Pal, and M. Blumenstein, “Off-line verification technique for Hindi signatures,” *IET Biometrics*, vol. 2, no. 4, pp. 182–190, 2013. doi: 10.1049/iet-bmt.2013.0016.

[5] IAPR TC4, “Signature Datasets — BHSig260,” International Association for Pattern Recognition Technical Committee 4. Acesso em 28 ago. 2026. https://iapr-tc4.org/signature-datasets/

[6] A. Soleimani, B. N. Araabi, and K. Fouladi, “UTSig: A Persian offline signature dataset,” *IET Biometrics*, 2017. doi: 10.1049/iet-bmt.2015.0058.

[7] J. G. Moreno-Torres, T. Raeder, R. Alaiz-Rodríguez, N. V. Chawla, and F. Herrera, “A unifying view on dataset shift in classification,” *Pattern Recognition*, vol. 45, no. 1, pp. 521–530, 2012. doi: 10.1016/j.patcog.2011.06.019.

[8] J. Ortega-Garcia et al., “MCYT baseline corpus: A bimodal biometric database,” *IEE Proceedings — Vision, Image and Signal Processing*, vol. 150, no. 6, pp. 395–401, 2003. doi: 10.1049/ip-vis:20031078.

[9] IAPR TC4, “Signature Datasets — MCYT-SignatureOff-75,” International Association for Pattern Recognition Technical Committee 4. Acesso em 28 ago. 2026. https://iapr-tc4.org/signature-datasets/

[10] D. A. M. José, L. de Medeiros Marques, A. Bueno, and R. B. Z. da Silva, “Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification,” manuscrito revisado e ressubmetido ao *IEEE Access*, 2026 (decisão editorial e dados bibliográficos finais pendentes).

---

# 38. Estabilidade documental

Este documento integra a especificação oficial do SFAAO 2.4.0.

Alterações futuras devem preservar:

- separação entre profile e conteúdo;
- compatibilidade com a arquitetura modular;
- rastreabilidade;
- reprodutibilidade;
- política de não redistribuição;
- semântica científica dos dados.

Novos datasets devem ser incorporados preferencialmente por novos profiles/adapters, sem reestruturação do núcleo.
