# SFAAO 2.4.0 — Licenciamento, Perfis Operacionais e Cotas

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Especificação técnica oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Objetivo

Este documento define a arquitetura técnica de licenciamento do SFAAO 2.4.0, os perfis operacionais `TRIAL`, `ACADEMIC` e `FULL`, a semântica de features, protocolos e cotas e as regras de ativação, consumo e persistência.

Este documento **não substitui a licença jurídica de uso/distribuição do software**. Os arquivos legais `LICENSE`, `LICENSE.pt-BR.md` e `LICENSE.en.md`, quando publicados no repositório/release, prevalecem quanto à concessão de direitos e obrigações jurídicas.

---

**Escopo de versão:** este documento descreve o modelo de licenciamento 2.4.0. Valores concretos de cotas e permissões dependem da licença emitida e devem ser conferidos contra o perfil efetivo no aplicativo homologado; não inferir cotas universais a partir de exemplos. A ferramenta administrativa emissora de licenças e a chave privada não acompanham o cliente.

# 2. Quatro conceitos que não devem ser confundidos

No SFAAO, “licença” pode aparecer em contextos distintos:

1. **Licença jurídica do software** — define direitos de uso, cópia, distribuição e demais condições legais.
2. **Licença operacional do runtime** — arquivo autorizado, normalmente `storage/license.sfaao`, usado para ativar uma instalação.
3. **Perfil operacional** — `TRIAL`, `ACADEMIC` ou `FULL`, com features, protocolos e limites.
4. **Licença de dataset de terceiro** — direitos independentes sobre GPDS, CEDAR, BHSig260, UTSig, MCYT ou qualquer outra base.

A aquisição do SFAAO não concede automaticamente direitos sobre datasets de terceiros.

---

# 3. Arquitetura do licenciamento


### Fluxo oficial de ativação no release 2.4.0

1. `sfaao-license-request` executa o fingerprint da própria imagem 2.4.0 e gera `SFAAO_LICENSE_REQUEST_V1`;
2. o fabricante emite a licença assinada fora do ambiente do cliente;
3. `sfaao-license-install` valida assinatura e vínculo de máquina com a chave pública distribuída e só então instala `license.sfaao` no storage persistente.

O fingerprint atual utiliza `SFAAO-FP-V2:`. A chave privada do fabricante, o `license_generator.py` administrativo e demais segredos **não integram o cliente, a imagem OCI nem o repositório distribuível**.

A licença operacional fica em `runtime/storage/license.sfaao` no host e é montada em `/opt/sfaao/storage/license.sfaao` no container.

O fluxo conceitual é:

```text
arquivo de licença
      ↓
LicenseLoader
      ↓
verificação criptográfica
      ↓
vínculo com instalação
      ↓
estado temporal protegido
      ↓
LicenseManager
      ↓
LicenseContext
      ↓
LicensePolicy / QuotaManager
      ↓
operações autorizadas
```

[SECURITY-PTBR.md](SECURITY-PTBR.md) é canônico para mecanismos criptográficos, machine fingerprint, secure time e proteção de chaves.

---

# 4. LicenseService

`LicenseService` é o serviço central que:

- localiza/carrega a licença;
- solicita validação da assinatura;
- valida o contexto da instalação;
- valida o estado temporal associado;
- produz o `LicenseContext` utilizado pelas políticas da aplicação.

O caminho padrão operacional é:

```text
storage/license.sfaao
```

A chave pública de verificação pertence ao runtime protegido; a chave privada de emissão não.

---

# 5. LicenseContext

O contexto de licença representa o estado autorizado já validado.

Campos confirmados incluem:

```text
valid
license_id
organization
license_type
machine_id
version
issued_at
expiration
profile
features
protocols
limits
```

`license_type` e `profile` são campos distintos e não devem ser tratados como sinônimos.

---

# 6. Perfil operacional

O **perfil operacional** é o conjunto predefinido de capacidades e limites aplicável a uma licença.

Perfis homologados:

```text
TRIAL
ACADEMIC
FULL
```

A duração/expiração não é inferida apenas pelo nome do perfil. A validade é determinada pelos metadados assinados da licença e pelo estado temporal validado.

---

# 7. Feature

Uma **feature** é uma capacidade funcional controlada pelo contexto de licença.

Features homologadas incluem:

```text
forensic_analysis
batch_analysis
training
calibration
comparison
docx_report
pdf_report
audit_trail
```

Uma feature habilitada não substitui a verificação do protocolo ou da quota correspondente.

---

# 8. Protocol entitlement

O contexto também controla protocolos explicitamente.

Chaves operacionais:

```text
scientific
forensic
```

No domínio do SFAAO:

```text
scientific → SCIENTIFIC_EXTERNAL
forensic   → FORENSIC_CASE
```

As definições científicas desses protocolos pertencem a [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md). Este documento define somente a autorização de uso.

---

# 9. Matriz de protocolos

| Perfil | SCIENTIFIC_EXTERNAL | FORENSIC_CASE |
|---|---:|---:|
| `TRIAL` | permitido | permitido |
| `ACADEMIC` | permitido | bloqueado |
| `FULL` | permitido | permitido |

O bloqueio do protocolo forense no perfil `ACADEMIC` é uma política de licenciamento, não uma afirmação científica sobre o protocolo.

---

# 10. Audit trail comercial

A feature:

```text
audit_trail
```

é habilitada no perfil `FULL`.

Isso **não significa** que os demais perfis possam eliminar registros mínimos de proveniência necessários à consistência científica e transacional.

A rastreabilidade científica obrigatória é definida em [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) e permanece distinta da feature comercial `audit_trail`.

---

# 11. Perfil TRIAL

O perfil `TRIAL` disponibiliza as capacidades centrais do produto, sujeitas às cotas menores.

Ele permite:

- treinamento;
- calibração;
- comparação;
- análise em lote;
- DOCX;
- PDF;
- protocolo científico;
- protocolo forense.

`audit_trail` não integra a feature set comercial completa deste perfil.

A expiração concreta deve ser lida da licença válida; não existe neste documento uma duração fixa universal para `TRIAL`.

---

# 12. Perfil ACADEMIC

O perfil `ACADEMIC` é orientado a uso acadêmico/científico e possui cotas intermediárias.

Permite:

- treinamento;
- calibração;
- comparação;
- análise em lote;
- DOCX;
- PDF;
- `SCIENTIFIC_EXTERNAL`.

Por política, bloqueia:

```text
FORENSIC_CASE
```

A disponibilidade de outras features não deve contornar essa restrição de protocolo.

---

# 13. Perfil FULL

O perfil `FULL` habilita:

- protocolos científico e forense;
- features funcionais homologadas;
- `audit_trail`;
- recursos sem limite numérico nos campos atualmente definidos como `None`.

`None` significa **ilimitado no contexto da política atual**, e não ausência de verificação.

---

# 14. Matriz de cotas

| Recurso | TRIAL | ACADEMIC | FULL |
|---|---:|---:|---:|
| Usuários ativos | 1 | 10 | ilimitado |
| Peritos `EXAMINER` ativos | 1 | 5 | ilimitado |
| Casos | 2 | 50 | ilimitado |
| Comparações individuais | ilimitado | ilimitado | ilimitado |
| Execuções em lote | 3 | 100 | ilimitado |
| Laudos | 1 | 20 | ilimitado |
| Exportações CSV | 3 | 50 | ilimitado |
| Treinamentos | 3 | 100 | ilimitado |
| Calibrações | 2 | 50 | ilimitado |
| Datasets disponíveis | 2 | 10 | ilimitado |
| Modelos cadastrados | 2 | 20 | ilimitado |

Estes valores descrevem o release 2.4.0 documentado em 30/09/2026. Mudanças futuras exigem versionamento e atualização deste documento.

---

# 15. `None` significa ilimitado

Quando um recurso possui:

```text
limit = None
```

o `QuotaManager` interpreta o recurso como ilimitado.

O retorno de quota deve conservar a distinção entre:

```text
limit = None       → ilimitado
limit = número     → limitado
limit ausente      → configuração inválida/fail-closed
```

Ausência de um limite esperado não deve ser silenciosamente transformada em ilimitado.

---

# 16. Quota

Uma **quota** é um limite operacional associado a um recurso.

Se:

- $L$ = limite;
- $U$ = uso calculado;
- $A$ = quantidade solicitada;

uma nova operação limitada é autorizada quando:

$$
U + A \le L
$$

Para recursos ilimitados, $L$ é representado por `None`.

---

# 17. Três semânticas de quota

As cotas do SFAAO não possuem todas a mesma semântica.

Existem três grupos:

```text
CUMULATIVAS
ESTADO ATUAL
HÍBRIDAS
```

Essa distinção impede que exclusões administrativas alterem indevidamente consumo histórico.

---

# 18. Recursos cumulativos

Recursos cumulativos:

```text
training_runs
calibrations
batch_executions
reports
csv_exports
```

O uso é medido por eventos registrados no ledger `license_usage`.

Uma operação consumida permanece historicamente contabilizada mesmo que seu artifact posterior seja removido.

---

# 19. Recursos de estado atual

Recursos de estado atual:

```text
users
examiners
datasets
models
```

Seu consumo é calculado a partir do estado corrente do banco, como usuários ativos, datasets disponíveis e modelos cadastrados.

Desativação/remoção legítima pode alterar o uso atual quando a regra do recurso assim define.

---

# 20. Casos: quota híbrida

`cases` usa semântica híbrida.

Conceitualmente:

$$
U_{cases}=\max(U_{cumulativo}, U_{atual})
$$

Isso impede que criar casos até o limite, apagá-los e recriá-los seja usado como mecanismo artificial para contornar a licença.

---

# 21. Comparações individuais

No release documentado:

```text
individual_comparisons = None
```

Portanto, comparações individuais são ilimitadas nos três perfis.

Se futuramente esse recurso se tornar limitado, deverá receber mecanismo explícito de contabilização e migração compatível.

---

# 22. QuotaManager

`QuotaManager` é responsável por:

- obter o limite;
- calcular o uso segundo a semântica do recurso;
- decidir se a operação é permitida;
- informar limite, usado, solicitado e remanescente.

Ele não deve registrar consumo cumulativo sozinho; esse registro é responsabilidade do `UsageTracker`.

---

# 23. UsageTracker

`UsageTracker` mantém o ledger de consumo cumulativo.

Registros incluem, conforme o evento:

```text
license_id
resource
amount
event_uid
user_id
case_id
metadata
```

`event_uid` permite identificar o evento de consumo e deve ser único para evitar duplicidade acidental.

---

# 24. Atomicidade

Validação de quota e registro do consumo devem ocorrer de forma transacional quando a operação exige reserva autoritativa.

Padrão:

```text
BEGIN IMMEDIATE
  ↓
revalidar quota
  ↓
reservar/criar operação
  ↓
registrar usage ledger
  ↓
COMMIT
```

Essa estratégia reduz condições de corrida entre duas operações concorrentes.

---

# 25. Pré-check não consome quota

Uma interface pode consultar a disponibilidade antes de iniciar uma operação.

Esse **pré-check** deve ser não destrutivo:

```text
check() → informa possibilidade
```

Consumo só ocorre no ponto autoritativo definido para o recurso.

---

# 26. Treinamentos

Cada novo treinamento autorizado sujeito a limite deve consumir:

```text
training_runs
```

O consumo representa a autorização da execução, não o número de épocas executadas.

Reiniciar uma execução como novo treinamento deve produzir novo evento quando a política da aplicação assim o classificar.

---

# 27. Calibrações

Cada nova execução de calibração autorizada sujeita a limite consome:

```text
calibrations
```

A quota não substitui a verificação de protocolo:

```text
feature calibration habilitada
+
protocolo permitido
+
quota disponível
```

As três condições devem ser satisfeitas quando aplicáveis.

---

# 28. Execuções em lote

Análise em lote consome:

```text
batch_executions
```

Uma execução em lote é diferente de cada comparação individual interna ao lote.

Por isso, comparações individuais podem ser ilimitadas enquanto o número de execuções em lote é limitado.

---

# 29. Laudos

Um **novo laudo lógico** consome uma unidade de:

```text
reports
```

A reserva do laudo, sua versão e o lançamento no ledger devem ocorrer na mesma transação.

---

# 30. Formato complementar de laudo

Quando o mesmo laudo lógico já foi autorizado em um formato, a geração do formato complementar pode reutilizar:

```text
report_uid
version
logical_context_key
```

Exemplo:

```text
DOCX existente
→ gerar PDF do mesmo contexto
→ sem nova unidade de quota
```

Isso evita cobrança dupla por dois formatos do mesmo laudo lógico.

---

# 31. Falha após reserva de laudo

Se a geração falha **depois** de uma reserva autorizada:

- a quota já consumida não é devolvida;
- o registro permanece rastreável;
- o status deve indicar falha (`FAILED`) quando suportado.

Isso diferencia autorização consumida de artifact concluído.

---

# 32. Exportação CSV

A exportação CSV possui dois momentos:

```text
pré-check não destrutivo
  ↓
usuário escolhe destino
  ↓
autorização autoritativa
  ↓
consumo de csv_exports
```

Se o usuário cancela antes da autorização, não há consumo.

Depois que a exportação é autorizada e registrada, falha posterior de escrita não devolve automaticamente a quota.

---

# 33. Usuários e peritos

As cotas:

```text
users
examiners
```

são de estado atual.

`examiners` representa contas ativas no papel `EXAMINER`.

A política de papéis e autenticação é canônica em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 34. Datasets

A quota:

```text
datasets
```

controla o número de datasets disponíveis/cadastrados conforme a regra de estado atual.

Ela não concede qualquer licença jurídica sobre a base.

O usuário continua responsável por obter e utilizar cada dataset legitimamente, conforme [DATASETS-PTBR.md](DATASETS-PTBR.md).

---

# 35. Modelos

A quota:

```text
models
```

controla modelos cadastrados/disponíveis no estado atual.

O release comercial não precisa distribuir modelos treinados de homologação. A instituição pode treinar seu próprio modelo ou importar package autorizado.

---

# 36. Mensagem de quota

Quando uma operação é negada, a interface deve transformar o estado técnico em mensagem compreensível.

Informações úteis incluem:

```text
recurso
usado
limite
solicitado
remanescente
```

Mensagens não devem sugerir apagar evidência histórica como forma de contornar a limitação.

---

# 37. Validade e expiração

A licença contém informações temporais, incluindo:

```text
issued_at
expiration
```

A aplicação valida a temporalidade em conjunto com o estado seguro definido em [SECURITY-PTBR.md](SECURITY-PTBR.md).

**Regra:** `TRIAL`, `ACADEMIC` e `FULL` não possuem, por definição deste documento, duração fixa universal.

O prazo é propriedade da licença emitida.

---

# 38. Vínculo com a instalação

Uma licença pode ser vinculada ao:

```text
machine_id
```

derivado pela infraestrutura de `MachineFingerprint`.

Esse vínculo reduz reutilização não autorizada da mesma licença em outra instalação.

A definição técnica do fingerprint pertence a [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 39. Assinatura da licença

A licença operacional é autenticada por assinatura assimétrica:

```text
emissão → chave privada
verificação → chave pública
```

A chave privada deve permanecer fora do runtime distribuído.

O release contém apenas material de verificação necessário.

A implementação criptográfica concreta aceita pelo release é determinada por `LicenseLoader` e pelos componentes de segurança correspondentes; não se deve inferir o formato válido a partir de scripts históricos isolados.

---

# 40. Ferramentas históricas de emissão

Ferramentas anteriores do projeto registram uso de RSA-2048, expoente 65537, PKCS#1 v1.5 e SHA-256 para assinatura de payload JSON canônico.

Esses registros são úteis para proveniência histórica, mas **não substituem a especificação efetivamente aceita pelo `LicenseLoader` do release**.

Qualquer troca de algoritmo/formato deve ser versionada e compatível com a política de migração.

---

# 41. Material secreto

Nunca distribuir:

- `private_key.pem`;
- segredo de emissão;
- credencial de operador;
- licença de desenvolvimento;
- estado temporal da máquina de desenvolvimento.

A chave pública de verificação pode integrar o runtime.

As regras de proteção são canônicas em [SECURITY-PTBR.md](SECURITY-PTBR.md).

---

# 42. Instalação e ativação

Fluxo operacional:

```text
instalar release
  ↓
obter machine/install identifier quando exigido
  ↓
emitir/receber licença autorizada
  ↓
instalar license.sfaao
  ↓
iniciar aplicação
  ↓
SecurityBootstrap
  ↓
LicenseContext válido
```

Os passos operacionais detalhados pertencem a [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md).

---

# 43. Renovação ou troca de licença

Uma renovação/upgrade deve ocorrer pela instalação de nova licença válida emitida para o contexto autorizado.

A troca pode alterar:

- expiração;
- profile;
- features;
- protocols;
- limits.

Ela não deve apagar registros científicos nem reescrever a proveniência histórica das operações já executadas.

O comportamento de migração de consumo entre `license_id` diferentes deve ser definido explicitamente pela política comercial; não deve ser inferido ou resetado silenciosamente.

---

# 44. Ausência de revogação remota obrigatória

O SFAAO foi projetado como aplicação predominantemente local e não depende de serviço de nuvem obrigatório para a operação principal.

Portanto, este documento **não declara mecanismo universal de revogação remota em tempo real**.

Quando revogação, renovação ou substituição forem necessárias, devem seguir o procedimento autorizado da distribuição/licença correspondente.

---

# 45. Fail closed no licenciamento

A autorização deve falhar de modo fechado quando:

- a licença não é válida;
- a assinatura não pode ser confirmada;
- o vínculo com a instalação falha;
- o estado temporal é inválido;
- feature necessária está desabilitada;
- protocolo necessário não é permitido;
- limite esperado está ausente;
- quota numérica foi excedida.

Uma exceção de configuração não deve ser convertida silenciosamente em acesso ilimitado.

---

# 46. Licença operacional não é autenticação de usuário

Uma instalação com licença válida ainda exige autenticação e autorização humanas.

Portanto:

```text
LicenseContext válido
≠
usuário autenticado
≠
usuário autorizado à operação
```

As três camadas devem ser verificadas conforme o fluxo.

---

# 47. Licenciamento de datasets

As licenças de GPDS, CEDAR, BHSig260, UTSig, MCYT e demais datasets são independentes do SFAAO.

O produto pode fornecer profile/adaptador, mas:

- não redistribui a base;
- não concede direitos sobre imagens;
- não contorna controles de acesso;
- não substitui autorização do titular.

Ver [DATASETS-PTBR.md](DATASETS-PTBR.md).

---

# 48. Licenciamento de modelos

O uso de modelo importado também deve respeitar direitos e autorização aplicáveis ao package e aos dados de origem.

O fato de o runtime aceitar tecnicamente um modelo não constitui declaração jurídica de que seu uso é permitido.

---

# 49. Licença jurídica do software

Os direitos de uso e distribuição do SFAAO serão expressos pelos documentos jurídicos do release:

```text
LICENSE
LICENSE.pt-BR.md
LICENSE.en.md
```

Este [LICENSING-PTBR.md](LICENSING-PTBR.md) documenta **como o subsistema técnico de licenciamento funciona**.

Se houver divergência sobre direitos jurídicos, os termos legais aplicáveis prevalecem.

---

# 50. Backup e migração

Backup/restore deve preservar com cuidado:

- banco;
- artifacts;
- licença válida quando a política permitir;
- estado de instalação;
- estado temporal.

Copiar indiscriminadamente estado protegido entre máquinas pode invalidar vínculo/fingerprint.

Procedimentos concretos pertencem a [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md).

---

# 51. Rastreabilidade do consumo

Eventos de quota são parte da proveniência operacional.

Devem permitir responder:

```text
qual licença?
qual recurso?
quanto?
qual evento?
qual usuário?
qual caso?
quando?
```

As definições de proveniência e auditabilidade pertencem a [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

---

# 52. Política de atualização de perfis

Qualquer mudança em `TRIAL`, `ACADEMIC` ou `FULL` deve:

1. ser versionada;
2. atualizar testes;
3. atualizar este documento PT-BR/EN;
4. atualizar Manual/README quando aplicável;
5. preservar interpretação de licenças já emitidas ou estabelecer migração explícita.

Não alterar cotas silenciosamente em patch de manutenção.

---

# 53. Checklist de licenciamento do release

- [ ] `TRIAL` validado;
- [ ] `ACADEMIC` validado;
- [ ] `FULL` validado;
- [ ] features coerentes;
- [ ] protocolos coerentes;
- [ ] `ACADEMIC` bloqueia `FORENSIC_CASE`;
- [ ] `FULL` habilita `audit_trail`;
- [ ] cotas cumulativas validadas;
- [ ] cotas de estado atual validadas;
- [ ] quota híbrida de casos validada;
- [ ] `None` interpretado como ilimitado;
- [ ] limite ausente tratado fail-closed;
- [ ] CSV cancelado antes da autorização não consome;
- [ ] formato complementar de laudo não consome novamente;
- [ ] falha após reserva de laudo permanece `FAILED`;
- [ ] licença de desenvolvimento ausente do pacote;
- [ ] chave privada ausente do pacote;
- [ ] chave pública de verificação presente quando necessária;
- [ ] secure time/integridade validados;
- [ ] mensagens de quota revisadas.

---

# 54. Regras normativas resumidas

1. Licença jurídica, licença runtime, perfil e licença de dataset são conceitos distintos.
2. Os perfis oficiais são `TRIAL`, `ACADEMIC` e `FULL`.
3. `ACADEMIC` não autoriza `FORENSIC_CASE`.
4. `None` significa ilimitado; campo esperado ausente é erro.
5. Cotas cumulativas não são reduzidas pela exclusão de artifacts.
6. Cotas de estado atual refletem o estado corrente.
7. Casos usam regra híbrida.
8. Consumo autoritativo deve ser transacional quando aplicável.
9. Pré-check não deve consumir quota.
10. Formatos complementares do mesmo laudo lógico não consomem duas vezes.
11. Falha após reserva autorizada não apaga consumo.
12. Expiração é definida pela licença, não pelo nome do perfil.
13. Chave privada nunca integra o runtime distribuído.
14. Troca de licença não altera proveniência histórica.
15. A licença do SFAAO não concede direitos sobre datasets de terceiros.

---

# 55. Referências

[1] B. Kaliski, *PKCS #1: RSA Cryptography Specifications Version 2.2*, RFC 8017, IETF, Nov. 2016. https://www.rfc-editor.org/rfc/rfc8017

[2] NIST, *Secure Hash Standard (SHS)*, FIPS PUB 180-4, 2015. doi: 10.6028/NIST.FIPS.180-4.

[3] SFAAO 2.4.0, [SECURITY-PTBR.md](SECURITY-PTBR.md), especificação canônica de segurança do runtime.

[4] SFAAO 2.4.0, [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md), especificação canônica de proveniência e auditabilidade.

[5] SFAAO 2.4.0, [DATASETS-PTBR.md](DATASETS-PTBR.md), política de datasets e direitos independentes de terceiros.

---

# 56. Estabilidade documental

Esta é a especificação técnica canônica do subsistema de licenciamento do SFAAO 2.4.0.

Mudanças futuras em profile, feature, protocolo, quota, assinatura, ativação ou consumo devem ser tratadas como alterações controladas e documentadas.

Nenhuma alteração comercial deve modificar silenciosamente resultados científicos já produzidos.
