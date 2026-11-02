# SFAAO 2.4.0 — Tutorial Operacional

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Tutorial oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Objetivo


### Regra operacional específica da 2.4.0

Uma comparação LORO é válida somente quando calibração e inferência usam o mesmo domínio A(N−1). A interface deve expor domínio, `alignment_status`, número de referências/folds, $\tau_{EER}$ e $\tau_{operacional}$. `REFERENCE_SENSITIVE` deve ser interpretado como `INCONCLUSIVE`; não se usa votação por maioria e a média A(N) não é classificada novamente.

Este tutorial conduz um usuário autorizado pelo fluxo operacional principal do SFAAO 2.4.0:

```text
instalação funcional
→ login
→ dataset
→ modelo
→ caso
→ assinaturas
→ calibração
→ comparação
→ revisão
→ relatório
```

O tutorial não redefine conceitos científicos. Para fundamentos e regras formais, consulte [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md), [DATASETS-PTBR.md](DATASETS-PTBR.md), [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md), [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md), [SECURITY-PTBR.md](SECURITY-PTBR.md) e [LICENSING-PTBR.md](LICENSING-PTBR.md).

---

# 2. Instalação resumida

O Tutorial pressupõe o **pacote oficial Windows ou Linux**, com runtime incorporado e código proprietário encapsulado/protegido.

```text
obter pacote oficial
→ verificar versão e integridade
→ instalar/executar o pacote da plataforma
→ ativar licença quando solicitado
→ iniciar SFAAO
→ criar primeiro ADMIN ou efetuar login
```

Os detalhes pertencem a [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md), ao Manual e ao README/Git.

---

# 3. Pré-condições

Antes de iniciar:

- SFAAO instalado conforme [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md);
- `SecurityBootstrap` válido;
- licença ativa;
- usuário autorizado;
- storage funcional;
- dataset legitimamente obtido quando necessário;
- nenhum caso real usado apenas para teste sem autorização.

---

# 4. Perfis de usuário

O tutorial utiliza dois papéis:

```text
ADMIN
EXAMINER
```

`ADMIN` governa usuários, datasets, modelos e treinamento.

`EXAMINER` executa operações periciais permitidas.

As permissões canônicas pertencem a [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 5. Perfis de licença

Antes de executar um protocolo, verifique o perfil:

```text
TRIAL
ACADEMIC
FULL
```

Ponto crítico:

```text
ACADEMIC
→ permite SCIENTIFIC_EXTERNAL
→ bloqueia FORENSIC_CASE
```

As cotas e demais features pertencem a [LICENSING-PTBR.md](LICENSING-PTBR.md).

---

# 6. Primeiro acesso

Em instalação limpa:

```text
0 usuários
→ FirstAdminWindow
→ criar ADMIN
→ login
```

Em instalação já configurada:

```text
LoginWindow
→ username + password
```

Não existem credenciais padrão.

---

# 7. Janela principal

Após autenticação, a `MainWindow` apresenta recursos compatíveis com:

- papel do usuário;
- licença;
- estado da aplicação.

Se um `EXAMINER` não visualizar menus administrativos, esse é o comportamento esperado.

---

# 8. Fluxo recomendado para nova instituição

Após instalar o produto:

```text
1. criar ADMIN
2. cadastrar usuários
3. obter dataset legitimamente
4. cadastrar/validar dataset
5. treinar ou importar modelo
6. ativar modelo
7. cadastrar perfil profissional
8. criar caso
9. adicionar assinaturas
10. calibrar
11. comparar
12. revisar
13. gerar laudo
```

---

# 9. Cadastrar usuários

Com `ADMIN`:

```text
Administração de Usuários
→ criar usuário
→ definir role
→ salvar
```

Use `EXAMINER` para atividade pericial quando o usuário não necessita governança.

Desativação é preferível à remoção destrutiva quando houver histórico relacionado.

---

# 10. Preparar um dataset

O SFAAO não fornece datasets de terceiros.

Antes do cadastro:

1. obtenha a base legitimamente;
2. identifique o corpus exato;
3. confirme os direitos de uso;
4. armazene-a em diretório autorizado;
5. identifique o profile correspondente.

Exemplos documentados incluem GPDS, CEDAR, BHSig260, UTSig e MCYT/MCYT-75.

---

# 11. Abrir o Gerenciador de Datasets

Com `ADMIN`:

```text
Gerenciar Datasets
→ Adicionar Dataset
```

Informe, conforme a interface:

```text
nome
profile
versão/corpus
diretório
```

---

# 12. Validar o dataset

Após selecionar a base:

```text
DatasetProvider
→ varredura
→ classificação
→ writer IDs
→ estatísticas
→ validação
```

A validação pode percorrer muitas imagens e exibir progresso de 0–100%.

Não cadastre manualmente no SQLite para contornar falhas.

---

# 13. Revisar estatísticas do dataset

Antes de aceitar o cadastro, confira:

- quantidade de arquivos;
- writers/classes;
- genuínas;
- falsificações;
- arquivos não classificados;
- conflitos;
- corpus/profile selecionado.

Uma estrutura carregável não garante que o profile escolhido seja semanticamente correto.

---

# 14. Dataset disponível

Após validação e persistência:

```text
dataset
→ catálogo local
→ disponível para treinamento
→ disponível para SCIENTIFIC_EXTERNAL quando aplicável
```

A disponibilidade técnica não altera os direitos jurídicos sobre a base.

---

# 15. Abrir o Gerenciador de Modelos

Com `ADMIN`:

```text
Gerenciar Modelos
```

Existem duas rotas:

```text
treinar novo modelo
ou
importar package compatível
```

O release limpo não contém modelo de homologação ativo.

---

# 16. Treinar novo modelo

Abra a janela de treinamento e selecione o dataset.

Defina o modo:

```text
PAIR
TRIPLET
CLASSIFICATION
```

A compatibilidade entre modo, loss e arquitetura deve ser respeitada pela plataforma.

---

# 17. Escolher backbone

Backbones documentados:

```text
ResNet18
ResNet34
ResNet50
EfficientNet-B0
ConvNeXt-Tiny
MobileNetV3
```

Escolher outro backbone no futuro exige suporte explícito do release.

---

# 18. Configurar pesos pretrained

Opção:

```text
pretrained = true/false
```

Se `true`, o `torchvision` pode tentar baixar pesos quando o checkpoint não estiver no cache.

Em ambiente offline, prepare previamente a disponibilidade dos pesos.

---

# 19. Configurar congelamento do backbone

Opção:

```text
freeze_backbone = true/false
```

Ela controla se parâmetros do extrator permanecem congelados durante a etapa correspondente do treinamento.

A escolha faz parte da identidade técnica do modelo e deve permanecer persistida.

---

# 20. Configurar embedding

Defina:

```text
embedding_dim
dropout
```

A dimensão precisa ser compatível com a arquitetura e permanecer a mesma quando o modelo for reconstruído para inferência/calibração.

---

# 21. Escolher loss

Losses documentadas:

```text
Contrastive
Cosine Contrastive
Triplet
ArcFace
```

Não combine livremente qualquer loss com qualquer modo.

Exemplos de compatibilidade conceitual:

```text
PAIR           → losses de pares
TRIPLET        → Triplet Loss
CLASSIFICATION → classificação/angular compatível
```

---

# 22. Escolher métrica

Métricas de comparação documentadas:

```text
Cosine Similarity
Euclidean Distance
```

Regra operacional crítica:

```text
cosine    → maior é mais semelhante
euclidean → menor é mais próximo
```

A regra formal está em [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md)/[CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 23. Configurar otimização

Defina, conforme o modo:

```text
optimizer: Adam / AdamW
learning_rate
weight_decay
batch_size
epochs
```

Esses parâmetros devem ser preservados no `TrainingConfig`.

---

# 24. Configurar scheduler

Opções documentadas:

```text
none
StepLR
cosine
plateau
```

A escolha do scheduler faz parte da configuração reprodutível do treinamento.

---

# 25. Configurar amostragem

No modo PAIR, parâmetros podem incluir:

```text
train_pairs
val_pairs
positive_ratio
forgery_negative_ratio
seed
```

A semântica dos pares e negativos é canônica em [DATASETS-PTBR.md](DATASETS-PTBR.md).

---

# 26. Configurar early stopping

Parâmetros disponíveis podem incluir:

```text
min_epochs
patience
min_delta
```

O monitoramento pode utilizar a estratégia/métrica definida pelo pipeline.

O melhor modelo não precisa ser a última época.

---

# 27. Iniciar treinamento

Após revisar a configuração:

```text
Iniciar
→ TrainingPipeline
→ treino
→ validação
→ métricas
→ seleção best_state_dict
→ avaliação final
→ model package
```

Não fechar a aplicação abruptamente durante persistência de artifacts.

---

# 28. Monitorar treinamento

A janela de monitoramento pode apresentar:

- época;
- train loss;
- validation loss;
- EER;
- threshold;
- learning rate;
- melhor estado;
- progresso.

Os valores mostrados pertencem ao treinamento/validação e não substituem a calibração do caso.

---

# 29. Revisar artifacts do treinamento

O package pode conter:

```text
pesos
model_config.json
training_config.json
evaluation_metrics.json
history
plots
metadata
```

O package é um artifact científico e deve permanecer íntegro/rastreável.

---

# 30. Registrar e ativar modelo

Após treinamento ou importação:

```text
validar package
→ registrar
→ selecionar modelo
→ ativar
```

Somente o modelo operacional apropriado deve ser usado para novas operações.

Ativar um modelo novo não altera comparações históricas.

---

# 31. Criar um caso

Com usuário autorizado:

```text
Casos
→ Novo Caso
→ preencher identificação
→ salvar
```

O caso é a unidade central que relaciona assinaturas, calibrações, comparações e relatórios.

---

# 32. Perfil do perito

Cadastre/revise o perfil profissional antes do laudo.

Esses dados podem ser atualizados de forma controlada sem recalcular automaticamente os resultados científicos já persistidos.

---

# 33. Abrir operações do caso

No caso selecionado:

```text
Abrir
→ CaseOperationsWindow
```

O painel reúne ações como:

- dados do laudo;
- assinaturas;
- calibração;
- comparação;
- resultados;
- relatório.

---

# 34. Adicionar RC

Faça upload das assinaturas de referência classificadas como:

```text
RC
```

Antes de avançar:

- confira imagens;
- confira ordem/página;
- confira papel pericial.

Não troque silenciosamente o papel depois de produzir resultados.

---

# 35. Adicionar RO

Faça upload do conjunto:

```text
RO
```

Revise as mesmas propriedades.

RC e RO são contextos distintos e podem produzir calibrações e comparações distintas.

---

# 36. Adicionar QUESTIONED

Faça upload das assinaturas questionadas:

```text
QUESTIONED
```

Revise:

- imagem;
- página;
- identificação;
- papel pericial quando o protocolo exigir classificação humana.

As questionadas não devem ser usadas indevidamente como população de calibração científica independente.

---

# 37. Revisar papéis periciais

Antes de calibrar:

```text
RC corretas?
RO corretas?
QUESTIONED corretas?
classificações humanas revisadas?
```

Mudanças relevantes devem permanecer auditáveis quando suportadas.

---

# 38. Abrir calibração

No caso:

```text
Calibração
→ selecionar modelo
→ selecionar RC ou RO
→ selecionar protocolo
```

Protocolos:

```text
FORENSIC_CASE
SCIENTIFIC_EXTERNAL
```

Use apenas protocolo autorizado pela licença.

---

# 39. FORENSIC_CASE

Se o perfil de licença permitir:

```text
caso
→ referências/papéis periciais
→ parâmetros do protocolo
→ executar
→ revisar
```

Não reproduzimos aqui a metodologia; consulte [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

O perfil `ACADEMIC` bloqueia esse protocolo.

---

# 40. SCIENTIFIC_EXTERNAL

Fluxo operacional:

```text
caso
→ RC ou RO
→ dataset externo legitimamente cadastrado
→ impostores externos
→ calibração
```

As `QUESTIONED` permanecem reservadas à etapa posterior de inferência/avaliação, conforme [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

---

# 41. Revisar calibração

Após a execução, confira:

- protocolo;
- `calibration_uid`;
- modelo;
- referência RC/RO;
- métrica;
- EER;
- FAR/FRR quando aplicável;
- threshold;
- quantidades de amostras;
- gráficos;
- artifacts.

Não selecione uma calibração apenas porque o threshold “parece bom”.

---

# 42. Selecionar calibração

Quando houver mais de uma calibração válida:

```text
revisar
→ selecionar explicitamente a calibração operacional
```

A calibração pertence a:

```text
caso + modelo
```

Não reutilize indiscriminadamente threshold de outro modelo/caso.

---

# 43. Comparação de Assinaturas — janela da versão 2.4.0

Após selecionar o caso e o **contexto ativo** (modelo, calibração, referências e assinaturas questionadas pertinentes), acesse a janela **Comparação de Assinaturas**. A versão 2.4.0 apresenta **quatro opções**, e não a interface anterior de três ações:

1. **Comparação Individual (LORO/A4):** compara uma assinatura questionada no contexto ativo, respeitando o domínio A(N−1) da calibração. Quando há cinco referências, A(N−1) corresponde a A4. O protocolo e a classificação das referências (RC ou RO) devem corresponder ao contexto selecionado.
2. **Inspecionar Score Pairwise:** permite examinar os scores elementares entre referências e a assinatura questionada. Esses scores são componentes de análise e proveniência; não substituir a decisão LORO pela aplicação direta do limiar A(N−1) a um par A1.
3. **Comparar em Lote:** realiza múltiplas comparações sob os controles de contexto, autorização, registro de resultados e cotas aplicáveis. Conferir os elementos selecionados antes da execução.
4. **Atualizar Contexto:** atualiza o contexto operacional apresentado pela janela após alterações pertinentes. Conferir novamente modelo, calibração, referências e conjunto de questionadas antes de comparar.

**Pré-condição:** não executar uma decisão LORO quando o domínio da calibração e o domínio de inferência não estiverem alinhados. `REFERENCE_SENSITIVE` deve permanecer `INCONCLUSIVE`; a média A(N) é descritiva, não uma segunda classificação. Os scores são apoio técnico, e a conclusão pericial requer interpretação humana.

**Acesso aos arquivos:** o inicializador Linux `/usr/bin/sfaao` mantém o atalho `~/SFAAO_Data`, normalmente apontando para `~/.local/share/SFAAO/runtime/data/{datasets,images,models}`. O script interno `/opt/sfaao/run-sfaao.sh`, usado em homologação isolada, não cria esse atalho automaticamente. Não substituir o destino do atalho principal pelos dados de teste.

---

# 44. Comparação com cosine

Quando a métrica for cosine:

```text
score >= threshold
→ região compatível segundo a regra binária configurada
```

Maior score representa maior similaridade.

A interpretação pericial final não é determinada exclusivamente pelo número.

---

# 45. Comparação com Euclidean

Quando a métrica for Euclidean:

```text
distance <= threshold
→ região compatível segundo a regra binária configurada
```

Menor distância representa maior proximidade.

Não aplique a regra do cosseno à distância Euclidiana.

---

# 46. Revisar scores individuais

A execução preserva resultados referência×questionada.

Revise quando necessário:

- referência;
- questionada;
- score/distance;
- threshold;
- verdict de par;
- execução.

Isso permite investigar o agregado em vez de olhar apenas a média.

---

# 47. Revisar resultado por questionada

O painel consolidado pode apresentar:

```text
score_count
mean
std
min
max
threshold
technical_decision
```

Confirme que o resultado pertence à execução e calibração corretas.

---

# 48. Executar RC e RO separadamente

Quando ambos os conjuntos fizerem parte do caso:

```text
executar RC × QUESTIONED
revisar
executar RO × QUESTIONED
revisar
```

Não misture scores RC e RO como se pertencessem à mesma população sem protocolo que o defina.

---

# 49. Resultados em lote

A janela de resultados em lote permite consolidar múltiplas questionadas e revisar:

- conjunto;
- score agregado;
- dispersão;
- threshold;
- decisão técnica;
- execução.

Análise em lote está sujeita à quota do perfil de licença.

---

# 50. Exportação CSV

Quando disponível:

```text
Resultados
→ Exportar CSV
→ selecionar destino
→ confirmar
```

Se o usuário cancelar antes da autorização autoritativa, não há consumo de quota.

O CSV é artifact auxiliar e não substitui o relatório pericial.

---

# 51. Dados do laudo

Antes da emissão:

- revisar identificação do caso;
- revisar demanda;
- revisar perfil do perito;
- revisar modelo;
- revisar calibrações;
- revisar comparações;
- revisar conclusão humana quando aplicável.

Dados descritivos não devem provocar recomputação silenciosa da ciência.

---

# 52. Gerar DOCX/PDF

Após revisão:

```text
Gerar Laudo
→ escolher formato
→ produzir artifact
→ registrar versão
```

Um formato complementar do mesmo laudo lógico pode reutilizar a mesma autorização, conforme [LICENSING-PTBR.md](LICENSING-PTBR.md).

---

# 53. Conteúdo técnico do relatório

O relatório pode incluir:

- modelo;
- dataset;
- backbone;
- loss;
- métrica;
- embedding;
- métricas originais;
- calibração RC/RO;
- FAR/FRR/EER;
- threshold operacional;
- UIDs;
- resultados;
- anexos.

O relatório deriva de resultados persistidos e não deve recalcular silenciosamente scores/thresholds.

---

# 54. Revisão humana obrigatória

Antes de entregar/arquivar:

- ler o documento completo;
- conferir identificação;
- conferir páginas/anexos;
- conferir modelo/calibração;
- conferir tabelas;
- conferir síntese/conclusão;
- conferir versão.

**O SFAAO apoia a perícia; não substitui a conclusão do profissional habilitado.**

---

# 55. Arquivamento

Preserve em conjunto, conforme política institucional:

```text
caso
assinaturas
modelo/package
calibrações
comparison runs
scores
artifacts
relatórios
hashes/manifests
auditoria
```

A política canônica de proveniência pertence a [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

---

# 56. Boas práticas operacionais

- trabalhar sempre no caso correto;
- preservar originais;
- revisar papéis periciais;
- confirmar modelo ativo;
- calibrar antes de comparar;
- não reutilizar calibração incompatível;
- não alterar evidência depois da execução sem criar nova atividade;
- preservar UIDs;
- preservar artifacts;
- usar backup consistente;
- revisar o relatório.

---

# 57. Erros que não devem ser cometidos

Não:

- usar dataset sem direito de uso;
- tratar profile como dataset;
- usar questionadas na calibração `SCIENTIFIC_EXTERNAL`;
- confundir threshold de treino com threshold operacional;
- aplicar `>=` à Euclidiana;
- aplicar `<=` ao cosine;
- apagar histórico para liberar quota cumulativa;
- editar artifact científico e atualizar o hash “para combinar”;
- emitir laudo sem revisão;
- tratar resultado computacional como conclusão grafoscópica automática.

---

# 58. Checklist rápido por caso — formulário operacional

As caixas abaixo devem ser marcadas pelo usuário **durante cada caso**; não representam pendências do release.

- [ ] usuário correto;
- [ ] caso correto;
- [ ] perito correto;
- [ ] modelo correto/ativo;
- [ ] RC conferidas;
- [ ] RO conferidas;
- [ ] QUESTIONED conferidas;
- [ ] papéis revisados;
- [ ] protocolo permitido;
- [ ] calibração executada;
- [ ] calibração selecionada;
- [ ] threshold conferido;
- [ ] RC comparada;
- [ ] RO comparada quando aplicável;
- [ ] resultados revisados;
- [ ] laudo gerado;
- [ ] laudo revisado;
- [ ] anexos conferidos;
- [ ] artifacts preservados.

---

# 59. Onde encontrar cada definição

| Tema | Documento canônico |
|---|---|
| arquitetura, factories, modos, métricas | [ARCHITECTURE-PTBR.md](ARCHITECTURE-PTBR.md) |
| datasets, profiles, PairRecord | [DATASETS-PTBR.md](DATASETS-PTBR.md) |
| thresholds, FAR/FRR/EER, protocolos | [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md) |
| proveniência, UIDs, executions, hashes | [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) |
| autenticação, RBAC, integrity, secure time | [SECURITY-PTBR.md](SECURITY-PTBR.md) |
| perfis, protocols entitlement, quotas | [LICENSING-PTBR.md](LICENSING-PTBR.md) |
| instalação do produto distribuído e primeira inicialização | [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) |
| termos curtos | [GLOSSARY-PTBR.md](GLOSSARY-PTBR.md) |

---

# 60. Estabilidade documental

Este tutorial descreve o fluxo operacional homologado do SFAAO 2.4.0.

Novas telas podem reorganizar a navegação visual, mas devem preservar a sequência lógica e as regras canônicas dos documentos técnicos.

O tutorial deve ser atualizado quando uma mudança de interface alterar o caminho real de uma tarefa.

---

**Homologação de laudos 2.4.0:** geração de relatórios DOCX e PDF confirmada nos ambientes Windows 11, Linux Mint e Red Hat. As capturas PDF e DOCX representam formatos diferentes; o bootstrap de segurança não possui tela própria.
