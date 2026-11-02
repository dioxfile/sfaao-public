# Calibração Biométrica no SFAAO 2.4.0

## 1. Objetivo

Este documento define o conceito de **calibração biométrica do limiar de decisão** e descreve sua aplicação no SFAAO 2.4.0.

A regra editorial adotada é:

1. definir e fundamentar o conceito;
2. explicar sua finalidade e interpretação;
3. somente então apresentar sua aplicação concreta no SFAAO.

A calibração tratada aqui é, principalmente, **calibração de threshold/ponto operacional**. Ela não deve ser confundida com técnicas de calibração probabilística, como Platt scaling ou temperature scaling, cujo objetivo é transformar saídas de modelos em probabilidades calibradas.


**Nota de versão:** o artigo metodológico relacionado passou por avaliação por pares e teve sua versão revisada ressubmetida ao IEEE Access após atendimento às solicitações dos revisores; os parâmetros efetivos de cada calibração, protocolo e métrica continuam dependentes do contexto persistido no SFAAO.
---

## 2. Score biométrico

### 2.1 Definição e fundamentação

Em sistemas de verificação biométrica, uma comparação entre duas amostras produz um **score** que expressa o grau de correspondência segundo a representação e a métrica utilizadas. Esse score pode ser uma **similaridade**, em que valores maiores indicam maior proximidade, ou uma **distância**, em que valores menores indicam maior proximidade. A interpretação de scores, thresholds e taxas de erro é parte central da avaliação de sistemas biométricos (Jain, Ross e Prabhakar, 2004; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

### 2.2 Finalidade e interpretação

O score é uma variável contínua. Isoladamente, ele não constitui uma decisão categórica. Para converter o score em uma região de aceitação ou rejeição é necessário um **limiar de decisão**.

### 2.3 Aplicação no SFAAO

No SFAAO, os scores são produzidos a partir de embeddings extraídos por modelos de Deep Learning. A plataforma suporta, entre outras configurações, **Cosine Similarity** e **Euclidean Distance**. A orientação da decisão é persistida com o modelo e a calibração para impedir a aplicação de uma regra fixa incompatível com a métrica selecionada.

---

## 3. Populações genuína e impostora

### 3.1 Definição e fundamentação

Uma avaliação biométrica de verificação necessita distinguir duas classes de comparações:

- **genuine/match**: comparações entre amostras atribuídas à mesma identidade;
- **impostor/non-match**: comparações entre amostras atribuídas a identidades diferentes.

As distribuições resultantes são utilizadas para estimar o comportamento do sistema em diferentes thresholds (Jain, Ross e Prabhakar, 2004; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

Na verificação de assinaturas offline, a construção dessas populações exige atenção especial à identidade do escritor, às falsificações habilidosas, ao conjunto de dados e à separação entre desenvolvimento e avaliação (Plamondon e Lorette, 1989; Hafemann, Sabourin e Oliveira, 2017a; Hafemann, Sabourin e Oliveira, 2017b).

### 3.2 Finalidade e interpretação

A distribuição genuína caracteriza os scores obtidos quando a identidade declarada é compatível com a referência. A distribuição impostora caracteriza scores produzidos por comparações não correspondentes. A sobreposição entre essas distribuições determina o compromisso possível entre falsas aceitações e falsas rejeições.

### 3.3 Aplicação no SFAAO

O SFAAO constrói populações conforme o protocolo de calibração selecionado. As assinaturas, seus papéis periciais, os pares utilizados, a origem dos impostores, as quantidades e os parâmetros de seleção devem permanecer rastreáveis.

---

## 4. Threshold ou limiar de decisão

### 4.1 Definição e fundamentação

Um **threshold** $\tau$ é uma fronteira no espaço de scores utilizada para converter uma variável contínua em uma decisão operacional. A escolha do ponto operacional determina diretamente as taxas de erro do sistema (Jain, Ross e Prabhakar, 2004; Fawcett, 2006; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

Para uma métrica de similaridade:

$$
s \ge \tau \Rightarrow \text{aceitação}
$$

Para uma métrica de distância:

$$
d \le \tau \Rightarrow \text{aceitação}
$$

### 4.2 Finalidade e interpretação

Não existe, em geral, um threshold universal independente do modelo, da métrica, das populações e do contexto de avaliação. A escolha do limiar deve ser interpretada conjuntamente com o protocolo que o produziu.

### 4.3 Aplicação no SFAAO

No SFAAO:

- **Cosine Similarity** usa orientação de maior score como maior compatibilidade;
- **Euclidean Distance** usa orientação de menor distância como maior compatibilidade;
- o threshold de treinamento permanece como informação de proveniência do modelo;
- o threshold utilizado na decisão operacional deriva da calibração selecionada para o contexto correspondente.

---

## 5. FAR — False Acceptance Rate

### 5.1 Definição e fundamentação

A **False Acceptance Rate (FAR)** é a proporção de comparações impostoras/non-match classificadas incorretamente na região de aceitação. É uma métrica fundamental de desempenho biométrico (Jain, Ross e Prabhakar, 2004; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

Para similaridade:

$$
FAR(\tau)=
\frac{\left|\{s_I \ge \tau\}\right|}{N_I}
$$

Para distância:

$$
FAR(\tau)=
\frac{\left|\{d_I \le \tau\}\right|}{N_I}
$$

em que $N_I$ é o número de comparações impostoras.

### 5.2 Finalidade e interpretação

A FAR quantifica a frequência de aceitação indevida na população impostora avaliada. Seu valor não deve ser extrapolado para populações diferentes sem considerar protocolo, amostragem e dataset shift.

### 5.3 Aplicação no SFAAO

O SFAAO calcula FAR durante avaliação e calibração respeitando a semântica da métrica. O valor é persistido nos artefatos científicos e associado ao threshold correspondente.

---

## 6. FRR — False Rejection Rate

### 6.1 Definição e fundamentação

A **False Rejection Rate (FRR)** é a proporção de comparações genuínas/match classificadas incorretamente na região de rejeição (Jain, Ross e Prabhakar, 2004; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

Para similaridade:

$$
FRR(\tau)=
\frac{\left|\{s_G < \tau\}\right|}{N_G}
$$

Para distância:

$$
FRR(\tau)=
\frac{\left|\{d_G > \tau\}\right|}{N_G}
$$

em que $N_G$ é o número de comparações genuínas.

### 6.2 Finalidade e interpretação

A FRR quantifica rejeições indevidas dentro da população genuína avaliada. Assim como a FAR, depende da população, da métrica, do modelo e do protocolo.

### 6.3 Aplicação no SFAAO

A plataforma calcula FRR no mesmo sweep de thresholds utilizado para FAR, permitindo analisar o compromisso entre os dois tipos de erro.

---

## 7. ROC e ponto operacional

### 7.1 Definição e fundamentação

A análise **Receiver Operating Characteristic (ROC)** representa o comportamento de um sistema de decisão à medida que o threshold varia. Ela permite visualizar o compromisso entre taxas de acerto e de erro e comparar pontos operacionais (Fawcett, 2006).

Em biometria, a seleção de um ponto operacional deve ser acompanhada das populações, condições de teste e métricas que o produziram (ISO/IEC 19795-1:2021).

### 7.2 Finalidade e interpretação

A ROC não determina, sozinha, qual threshold deve ser utilizado. Ela oferece uma visão do comportamento do sistema em diferentes regiões de decisão. A escolha operacional depende do objetivo do protocolo.

### 7.3 Aplicação no SFAAO

O SFAAO persiste curvas e gráficos associados à avaliação/calibração, incluindo a relação FAR × FRR e distribuições de scores quando disponíveis.

---

## 8. EER — Equal Error Rate

### 8.1 Definição e fundamentação

O **Equal Error Rate (EER)** é a taxa associada ao ponto em que as taxas de falsa aceitação e falsa rejeição são iguais ou, em uma implementação discreta, tão próximas quanto possível. O EER é amplamente utilizado como medida resumida de desempenho em sistemas biométricos, embora não substitua a análise completa do ponto operacional e das populações avaliadas (Jain, Ross e Prabhakar, 2004; Jain, Flynn e Ross, 2008; ISO/IEC 19795-1:2021).

No SFAAO, o threshold correspondente é estimado por:

$$
\tau_{\mathrm{EER}} =
\underset{\tau}{\mathrm{arg\,min}}
\left|
\mathrm{FAR}(\tau)-\mathrm{FRR}(\tau)
\right|
$$

### 8.2 Finalidade e interpretação

O EER fornece uma referência sintética da separabilidade das populações sob um protocolo definido. Um EER baixo em uma população específica não constitui garantia de igual desempenho em outra população.

Um EER igual a zero significa separação perfeita **na população avaliada e no conjunto de thresholds considerados**; não implica taxa de erro universal igual a zero.

### 8.3 Aplicação no SFAAO

O SFAAO registra separadamente:

- a taxa EER;
- o threshold $\tau_{EER}$;
- a métrica;
- as populações utilizadas;
- os parâmetros do protocolo;
- o modelo correspondente.

Dessa forma, `EER` e `tau_EER` não são tratados como sinônimos.

---

## 9. Calibração biométrica do limiar de decisão

### 9.1 Definição e fundamentação

Neste documento, **calibração biométrica** significa o processo pós-treinamento no qual os scores produzidos por um modelo fixo são analisados em populações genuínas e impostoras para estimar taxas de erro e determinar um threshold ou ponto operacional apropriado ao protocolo.

Essa interpretação decorre dos fundamentos de decisão biométrica, avaliação por scores, thresholds e taxas de erro descritos por Jain, Ross e Prabhakar (2004), Jain, Flynn e Ross (2008), Fawcett (2006) e ISO/IEC 19795-1:2021.

A calibração tratada pelo SFAAO não deve ser confundida com **probability calibration**. O objetivo principal aqui não é transformar o score em uma probabilidade posterior calibrada, mas estabelecer e documentar a fronteira operacional de decisão.

### 9.2 Finalidade e interpretação

A calibração separa três etapas conceitualmente distintas:

```text
TREINAMENTO
    ↓
aprende os parâmetros do modelo

CALIBRAÇÃO
    ↓
mantém o modelo fixo
analisa scores de populações definidas
estima taxas de erro e threshold

INFERÊNCIA / COMPARAÇÃO
    ↓
aplica modelo + calibração
às evidências destinadas à avaliação
```

Essa separação é importante porque um threshold obtido durante o desenvolvimento do modelo pode não preservar o mesmo comportamento quando a distribuição operacional muda.

### 9.3 Aplicação no SFAAO 2.4.0 — domínio decisório alinhado

A calibração 2.4.0 não estima o threshold em um domínio estatístico e o aplica em outro. O domínio decisório é explicitamente registrado.

#### A1 — `PAIRWISE_SCORE`

A1 é um único score entre duas assinaturas. Em LORO ele é **componente/proveniência**, não a unidade que recebe o threshold A(N−1).

#### A(N−1) — `LORO_MEAN`

Com $N$ referências, cada fold exclui uma referência e agrega a pseudo-query ou questionada contra as $N-1$ restantes:

$$
A_{-k}(Q)=\frac{1}{N-1}\sum_{j\ne k}s(Q,R_j).
$$

Com cinco referências, a unidade decisória é A4.

#### A(N) — estatística descritiva

$$
A_N(Q)=\frac{1}{N}\sum_{j=1}^{N}s(Q,R_j).
$$

A(N) pode ser persistida como descrição, mas **não é classificada novamente** com o threshold LORO.

### 9.4 Construção LORO das populações

Com cinco referências genuínas não existe uma sexta genuína independente para construir uma unidade genuína A5 sem auto-comparação. O SFAAO 2.4.0 usa **Leave-One-Reference-Out (LORO)**:

- cada referência atua uma vez como pseudo-query;
- a pseudo-query é comparada com as quatro referências restantes;
- obtêm-se cinco valores genuínos A4;
- para cada query non-match, cada referência é excluída sucessivamente, produzindo cinco valores non-match A4;
- FAR, FRR, EER e thresholds são estimados no mesmo domínio A4.

Auto-comparação não é usada para fabricar uma unidade genuína A5.

### 9.5 Thresholds e FAR-alvo

O release 2.4.0 distingue:

- $\tau_{EER}$ — threshold do ponto EER;
- $\tau_{forensic}$ — threshold associado ao FAR-alvo operacional (1% na configuração oficial atual);
- $\tau_{operacional}$ — threshold conservador final da política de três zonas.

Para Cosine Similarity:

$$
\tau_{operacional}=\max(\tau_{forensic},\tau_{EER}).
$$

Para Euclidean Distance:

$$
\tau_{operacional}=\min(\tau_{forensic},\tau_{EER}).
$$

O alias legado `threshold_operational` pode aparecer em artifacts históricos para o threshold calculado a partir do target-FAR. Na documentação 2.4.0 esse valor é denominado canonicamente **$\tau_{forensic}$**, evitando confusão com o threshold final $\tau_{operacional}$.

### 9.6 Política de três zonas

Cosine Similarity:

- score $\ge\tau_{operacional}$ → `COMPATIBLE`;
- $\tau_{EER}\le$ score $<\tau_{operacional}$ → `INCONCLUSIVE`;
- score $<\tau_{EER}$ → `INCOMPATIBLE`.

Euclidean Distance:

- distância $\le\tau_{operacional}$ → `COMPATIBLE`;
- $\tau_{operacional}<$ distância $\le\tau_{EER}$ → `INCONCLUSIVE`;
- distância $>\tau_{EER}$ → `INCOMPATIBLE`.

Quando $\tau_{forensic}=\tau_{EER}$ não existe zona cinza.

## 10. Dataset shift

Dataset shift motiva a separação entre threshold de treinamento e calibração operacional. O threshold de treinamento é preservado como proveniência do modelo, não como decisão automática do caso.

## 11. Independência da evidência questionada — `SCIENTIFIC_EXTERNAL`

No protocolo científico, as questionadas permanecem **cegas durante a calibração**. As populações são construídas com referências e impostores externos independentes; somente após o fechamento dos thresholds as questionadas entram na inferência.

```text
REFERÊNCIAS + IMPOSTORES EXTERNOS
        ↓
LORO A(N-1) DE CALIBRAÇÃO
        ↓
τ_EER / τ_forensic / τ_operacional
        ↓
QUESTIONADAS (somente inferência)
```

## 12. `FORENSIC_CASE` no release 2.4.0

### 12.1 RC — referências contratuais

- referências genuínas do caso formam os folds genuínos LORO;
- a população non-match usa somente `QUESTIONED_IMPOSTOR` previamente classificada pelo perito grafoscópico;
- questionadas compatíveis **não são promovidas a genuínas** para calibração;
- questionadas sem classificação pericial não entram silenciosamente na população non-match;
- se não houver non-match pericial suficiente, o threshold RC não deve ser estimado artificialmente.

### 12.2 RO — padrão oficial

No cenário RO, todas as `QUESTIONED` são intencionalmente tratadas como non-match para a calibração, pois o padrão oficial pode representar uma assinatura institucional distinta do padrão contratual/questionado. Essa regra é específica do protocolo e permanece explícita na proveniência.

## 13. Inferência LORO e consolidação

Cada questionada é avaliada fold a fold no mesmo domínio A(N−1) da calibração. Os folds não são novamente promediados e não há maioria de votos.

- todos `COMPATIBLE` → `STABLE_COMPATIBLE`;
- todos `INCOMPATIBLE` → `STABLE_INCOMPATIBLE`;
- todos `INCONCLUSIVE` → `STABLE_INCONCLUSIVE`;
- qualquer mistura → `REFERENCE_SENSITIVE`, consolidado como `INCONCLUSIVE`.

A persistência registra folds, referência excluída, score agregado, thresholds, decisão técnica e estabilidade. `ALIGNED` indica compatibilidade entre domínio de calibração e inferência; `MISMATCH` bloqueia a execução; `LEGACY_MISMATCH` identifica registros históricos incompatíveis.

## 14. Limitações de interpretação

A calibração deve ser interpretada dentro de seu protocolo.

Não se deve concluir que:

- um EER observado é uma taxa de erro universal;
- um threshold é válido para qualquer modelo;
- um threshold é válido para qualquer métrica;
- um threshold permanece ótimo após mudança relevante de distribuição;
- um resultado computacional substitui a conclusão grafoscópica;
- uma calibração científica pode utilizar silenciosamente as questionadas para ajustar o próprio threshold que será aplicado a elas.

---

## 15. Referências

**FAWCETT, T.** An Introduction to ROC Analysis. *Pattern Recognition Letters*, v. 27, n. 8, p. 861–874, 2006. DOI: 10.1016/j.patrec.2005.10.010.

**HAFEMANN, L. G.; SABOURIN, R.; OLIVEIRA, L. S.** Offline Handwritten Signature Verification — Literature Review. In: *7th International Conference on Image Processing Theory, Tools and Applications (IPTA)*, 2017, p. 1–8. DOI: 10.1109/IPTA.2017.8310112. (2017a)

**HAFEMANN, L. G.; SABOURIN, R.; OLIVEIRA, L. S.** Learning Features for Offline Handwritten Signature Verification Using Deep Convolutional Neural Networks. *Pattern Recognition*, v. 70, p. 163–176, 2017. DOI: 10.1016/j.patcog.2017.05.012. (2017b)

**ISO/IEC.** ISO/IEC 19795-1:2021 — *Information technology — Biometric performance testing and reporting — Part 1: Principles and framework*. 2. ed., 2021.

**JAIN, A. K.; FLYNN, P.; ROSS, A. A. (eds.).** *Handbook of Biometrics*. Springer, 2008. DOI: 10.1007/978-0-387-71041-9.

**JAIN, A. K.; ROSS, A.; PRABHAKAR, S.** An Introduction to Biometric Recognition. *IEEE Transactions on Circuits and Systems for Video Technology*, v. 14, n. 1, p. 4–20, 2004. DOI: 10.1109/TCSVT.2003.818349.

**JOSÉ, D. A. M.; MARQUES, L. de M.; BUENO, A.; SILVA, R. B. Z. da.** Independent External-Impostor Calibration for Mitigating Dataset Shift in Forensic Offline Signature Verification. Manuscrito revisado e ressubmetido ao *IEEE Access* após avaliação por pares, 2026; decisão editorial pendente.

**MORENO-TORRES, J. G.; RAEDER, T.; ALAIZ-RODRÍGUEZ, R.; CHAWLA, N. V.; HERRERA, F.** A Unifying View on Dataset Shift in Classification. *Pattern Recognition*, v. 45, n. 1, p. 521–530, 2012. DOI: 10.1016/j.patcog.2011.06.019.

**PLAMONDON, R.; LORETTE, G.** Automatic Signature Verification and Writer Identification — The State of the Art. *Pattern Recognition*, v. 22, n. 2, p. 107–131, 1989. DOI: 10.1016/0031-3203(89)90059-9.

---

## 16. Nota terminológica

Neste documento, a expressão preferencial é:

> **calibração biométrica do limiar de decisão**

ou, quando o contexto já estiver claro:

> **calibração do ponto operacional**

Evita-se a expressão genérica “calibração da rede neural”, pois o procedimento descrito não altera os pesos do modelo e não corresponde, por definição, a uma técnica de calibração probabilística.
