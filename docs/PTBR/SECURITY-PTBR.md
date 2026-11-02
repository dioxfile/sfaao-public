# SFAAO 2.4.0 — Segurança da Aplicação e Proteção do Runtime

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Especificação técnica oficial  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

# 1. Objetivo

Este documento define a política canônica de segurança do SFAAO 2.4.0 no nível da aplicação e do runtime.

Seu objetivo é preservar confidencialidade, integridade, disponibilidade, autenticidade operacional e rastreabilidade dos ativos processados pelo sistema, sem confundir mecanismos de segurança com metodologia científica.

[TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) é canônico para proveniência e auditabilidade; [LICENSING-PTBR.md](LICENSING-PTBR.md) para regras comerciais e perfis de licença; [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) para implantação.

---

# 2. Escopo

Este documento cobre:

- bootstrap de segurança;
- autenticação;
- armazenamento de senhas;
- proteção contra tentativas repetidas;
- autorização e papéis;
- princípio do menor privilégio;
- permissões de filesystem;
- proteção do SQLite e arquivos auxiliares;
- licença como barreira de autorização do runtime;
- identidade da instalação;
- estado temporal protegido;
- proteção contra retrocesso de relógio;
- verificação de integridade;
- hashes e manifests;
- proteção de chaves e segredos;
- hashes de relatórios;
- hardening;
- dependências;
- backups;
- logs/auditoria;
- limitações conhecidas.

---

# 3. Objetivos de segurança

Os objetivos tradicionais de segurança da informação são:

- **confidencialidade** — impedir divulgação não autorizada;
- **integridade** — impedir ou detectar modificação não autorizada;
- **disponibilidade** — manter acesso autorizado quando necessário.

O SFAAO acrescenta requisitos operacionais de:

- autenticidade do usuário;
- autorização por função;
- integridade do runtime;
- preservação de proveniência;
- consistência temporal;
- proteção de evidências e artifacts.

ISO/IEC 27001:2022 estabelece requisitos para sistemas de gestão de segurança da informação e ISO/IEC 27002:2022 fornece orientação sobre controles, incluindo acesso, criptografia e desenvolvimento seguro [1], [2].

---

# 4. Modelo de ameaça

A segurança do SFAAO considera, entre outros, riscos de:

- acesso local não autorizado;
- adivinhação de senha;
- uso indevido de privilégios administrativos;
- alteração do banco ou artifacts;
- remoção/substituição de arquivos protegidos;
- adulteração de licença;
- cópia da licença para máquina não autorizada;
- retrocesso artificial de relógio;
- alteração do runtime;
- inclusão acidental de segredos no release;
- exposição de casos, assinaturas ou datasets;
- dependências comprometidas.

Este documento não afirma resistência absoluta contra atacante com controle administrativo completo do sistema operacional, hardware ou ambiente de execução.

---

# 5. Defesa em profundidade


### Inventário histórico de proteção (não universal entre plataformas)

- Um build Linux documentado utilizou **PyArmor 9.2.6 Pro**; não atribuir automaticamente esta versão aos pacotes Windows ou RPM;
- runtime `pyarmor_runtime_015424`;
- inventário histórico do payload Linux: 184 arquivos após inclusão do manifesto (confirmar contra o pacote ao citar este número);
- manifesto histórico desse payload: 183 arquivos cobertos (o próprio manifesto é excluído da lista);
- chave pública distribuída em `security/crypto/public_key.pem`;
- ausência de chave privada e de `license_generator.py` no cliente.

SHA-256 canônico da chave pública:

```text
349bcae57cdb6a63f5a5ddbee53962a9b4d03f693995452d91861f7fc3845c5e
```

SHA-256 histórico do manifesto interno do payload Linux documentado em etapa de build (não confundir com o SHA-256 validado do manifesto de distribuição):

```text
f967766e7b84f877ea46942224b8802d8a60afde8eb5257af9cc522d2000772f
```

A licença PyArmor possui capacidade Pro/BCC/RFT; a documentação não presume que BCC ou RFT estejam habilitados em um payload sem opção explícita de build e validação correspondente.


**Registro por plataforma:** o histórico de homologação do Windows contém PyArmor Pro 9.2.4 e auditoria de 192 entradas; o inventário acima descreve uma etapa de build Linux e não deve ser tratado como contagem universal. Os hashes verificáveis dos instaladores encontram-se em [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md).

O SFAAO adota camadas complementares:

```text
PERMISSÕES DO SISTEMA OPERACIONAL
        ↓
SECURITY BOOTSTRAP
        ↓
LICENÇA / IDENTIDADE DA INSTALAÇÃO
        ↓
ESTADO TEMPORAL / TIME GUARD
        ↓
INTEGRIDADE DO RUNTIME
        ↓
AUTENTICAÇÃO
        ↓
AUTORIZAÇÃO POR PAPEL
        ↓
REGRAS DE APLICAÇÃO
        ↓
RASTREABILIDADE / AUDITORIA
```

Nenhuma camada isolada deve ser interpretada como proteção total.

---

# 6. SecurityBootstrap

`SecurityBootstrap` centraliza verificações internas de segurança anteriores ao uso normal da aplicação. **O bootstrap é executado em segundo plano (background), sem tela ou janela específica para o usuário.**

O fluxo homologado inclui:

```text
1. proteger storage/runtime
2. validar licença
3. gerar/resolver identidade da máquina
4. validar estado temporal protegido
5. executar TimeGuard
6. verificar integridade contra manifest
7. criar SecurityContext
```

Falha em etapa crítica deve impedir a criação de um contexto válido.

---

# 7. Fail closed

**Fail closed** significa que, quando uma verificação crítica não pode confirmar estado autorizado, o acesso protegido é negado em vez de continuar permissivamente.

No SFAAO:

```text
licença inválida      → bootstrap inválido
secure time inválido  → bootstrap inválido
time guard inválido   → bootstrap inválido
integridade inválida  → bootstrap inválido
```

Erros devem informar a etapa sem expor segredos ou material criptográfico.

---

# 8. Autenticação

Autenticação é o processo de verificar a identidade declarada por um usuário.

O SFAAO utiliza autenticação local por:

```text
username + password
```

Não existem credenciais administrativas padrão distribuídas. Na instalação sem usuários, o primeiro administrador é criado por fluxo específico e depois o acesso ocorre pela tela de login.

---

# 9. Armazenamento de senhas

Senhas não são armazenadas em texto claro nem de forma reversível.

A implementação 2.4.0 utiliza:

```text
PBKDF2-HMAC-SHA-256
salt aleatório: 16 bytes
iterações: 600.000
```

A representação persistida inclui identificador do algoritmo, work factor, salt e digest.

PBKDF2 é uma função de derivação de chave baseada em senha definida na família PKCS #5/RFC 8018 [3]. O uso de salt e de um fator de custo torna ataques offline mais dispendiosos.

---

# 10. Equação conceitual de PBKDF2

De forma simplificada:

$$
DK = PBKDF2(PRF, P, S, c, dkLen)
$$

onde:

- $P$ é a senha;
- $S$ é o salt;
- $c$ é o número de iterações;
- `PRF` é a função pseudoaleatória baseada em HMAC;
- `DK` é a chave/digest derivado.

No SFAAO, a PRF utiliza HMAC-SHA-256 e $c = 600000$ no comportamento implementado e testado.

---

# 11. Salt

Um **salt** é um valor aleatório associado individualmente ao hash de uma senha.

Finalidades:

- evitar hashes idênticos para senhas iguais;
- dificultar tabelas pré-computadas;
- aumentar o custo de ataques em massa.

O SFAAO gera 16 bytes aleatórios por senha no caminho homologado, superando o mínimo de 32 bits indicado pela NIST SP 800-63B-4 [4].

Salt não é segredo e pode ser armazenado junto ao hash.

---

# 12. Comparação constante

A verificação de senha utiliza comparação apropriada de digests (`hmac.compare_digest`), evitando comparação ingênua byte a byte controlada pelo conteúdo.

Essa prática reduz diferenças de tempo diretamente dependentes do ponto da primeira divergência.

---

# 13. Rehash progressivo

O formato de senha armazena algoritmo e número de iterações.

Quando uma autenticação válida encontra hash com parâmetros inferiores à política atual, o repositório pode gerar novo hash com o work factor vigente.

Essa estratégia permite evolução de custo sem migração destrutiva de todas as senhas em uma única etapa.

---

# 14. Proteção contra enumeração

O caminho de autenticação utiliza cálculo dummy quando necessário para reduzir diferenças triviais entre:

```text
usuário inexistente
usuário existente + senha inválida
```

A interface apresenta mensagem genérica de credenciais inválidas e não deve revelar se determinado username existe.

---

# 15. Rate limiting e bloqueio

O comportamento implementado e testado registra:

```text
MAX_FAILED_ATTEMPTS = 5
LOCKOUT_SECONDS = 60
```

Após o limite de falhas, a conta entra em bloqueio temporário. Quando o período expira, o estado pode ser normalizado.

Os campos `failed_login_attempts` e `locked_until` fazem parte do hardening do schema.

A NIST SP 800-63B-4 recomenda mecanismo de limitação de tentativas para reduzir adivinhação online [4].

---

# 16. Política futura de senha

A força do **armazenamento** da senha não substitui política adequada de **escolha** da senha.

A NIST SP 800-63B-4, publicada em julho de 2025, recomenda para senha usada como único fator:

- mínimo de 15 caracteres;
- comparação contra lista de senhas comuns/esperadas/comprometidas;
- ausência de regras artificiais de composição;
- armazenamento salted + hashed com fator de custo [4].

Esses requisitos devem ser considerados no próximo hardening funcional. Este documento não declara que todos eles já são impostos pela GUI 2.x congelada.

---

# 17. PBKDF2 e referência OWASP

O OWASP Password Storage Cheat Sheet indica atualmente, para cenários que utilizam PBKDF2-HMAC-SHA-256, work factor de **600.000 ou superior**, particularmente quando há requisito de compatibilidade FIPS [5].

O valor homologado do SFAAO coincide com essa referência na data deste documento.

Isso não constitui certificação FIPS nem certificação OWASP.

---

# 18. Autorização

Autorização determina **o que um usuário autenticado pode fazer**.

O SFAAO separa autenticação de autorização:

```text
credencial válida
≠
permissão para qualquer operação
```

A aplicação deve verificar papel/capacidade nas operações relevantes, e não confiar apenas na ocultação visual de botões.

---

# 19. RBAC

O SFAAO utiliza controle de acesso baseado em papéis — **Role-Based Access Control (RBAC)**.

No RBAC, permissões são associadas a papéis e usuários recebem esses papéis, em vez de manter permissões arbitrárias diretamente por usuário [6].

Papéis implementados:

```text
ADMIN
EXAMINER
```

---

# 20. Papel ADMIN

O papel `ADMIN` é destinado à governança da aplicação.

Inclui, conforme a operação 2.4.0:

- administração de usuários;
- governança de datasets;
- governança de modelos;
- treinamento;
- ativação de modelo;
- operações administrativas compatíveis com a licença.

O papel não transforma automaticamente toda ação do sistema operacional em ação autorizada dentro da aplicação.

---

# 21. Papel EXAMINER

O papel `EXAMINER` é destinado à operação pericial:

- casos;
- assinaturas;
- calibração autorizada;
- comparação;
- resultados;
- relatórios.

Menus administrativos não devem ser disponibilizados a esse papel.

---

# 22. Menor privilégio

O princípio do **menor privilégio** estabelece que usuários e processos recebam apenas as autorizações e recursos mínimos necessários à sua função [7].

Aplicação no SFAAO:

```text
EXAMINER → operação pericial
ADMIN    → governança necessária
```

A separação reduz impacto de erro humano ou comprometimento de uma conta não administrativa.

---

# 23. Primeiro administrador

Em instalação nova:

```text
0 usuários
  ↓
FirstAdminWindow
  ↓
criação explícita de ADMIN
  ↓
login convencional
```

O release não deve conter username/senha padrão.

A criação inicial precisa respeitar a mesma política de hashing usada pelos demais usuários.

---

# 24. Desativação de usuários

Contas desativadas não devem autenticar.

Mudanças administrativas relevantes devem ser realizadas por fluxo autorizado e permanecer compatíveis com a rastreabilidade definida em [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

Exclusão física de histórico não deve ser usada como substituto para desativação quando houver dependências.

---

# 25. Proteção do storage em POSIX

O runtime 2.4.0 aplica política restritiva em sistemas POSIX, incluindo:

```text
umask 077
diretórios privados: 700
arquivos privados:   600
```

Semântica:

- `700`: somente proprietário lê/escreve/executa o diretório;
- `600`: somente proprietário lê/escreve o arquivo.

Isso reduz exposição local acidental a outros usuários do mesmo host.

---

# 26. SQLite, WAL e SHM

A proteção deve incluir não apenas o arquivo principal SQLite, mas também arquivos auxiliares que possam conter conteúdo do banco, como:

```text
*.db
*.db-wal
*.db-shm
```

Permissões inconsistentes nesses arquivos podem anular a proteção do banco principal.

---

# 27. Windows ACL

O SFAAO possui caminho de proteção de storage para Windows, mas ACLs dependem do ambiente real, identidade de execução, filesystem e instalador.

**Regra de release:** a instalação final Windows deve validar as ACLs efetivamente aplicadas.

A existência do código de ACL não equivale, sozinha, à prova de configuração correta em todas as máquinas.

---

# 28. Caminhos e isolamento

Caminhos recebidos pelo núcleo científico devem ter sido autorizados pela camada de aplicação.

A GUI não deve conceder acesso irrestrito ao filesystem apenas por permitir escolha de arquivos.

Profiles, configs e release não devem conter caminhos privados da máquina de desenvolvimento.

---

# 29. Local-first e superfície de rede

O SFAAO é predominantemente uma aplicação desktop local.

Gestão de casos, persistência, inferência com modelos disponíveis, calibração com dados locais, comparação e geração de relatórios não exigem API de nuvem obrigatória.

Isso reduz uma classe de superfícies remotas, mas não elimina riscos locais, de supply chain ou de atualização.

---

# 30. Pesos pretrained

Quando `pretrained=True`, frameworks como `torchvision` podem tentar obter pesos externos caso o checkpoint não esteja em cache.

Esse acesso:

- não deve ser confundido com telemetria do SFAAO;
- deve ocorrer apenas a partir das fontes configuradas pela biblioteca;
- deve ser documentado para ambientes offline;
- pode exigir pré-carregamento autorizado de dependências.

---

# 31. Segurança da licença

A licença é uma credencial de autorização comercial/runtime, não uma credencial de usuário.

`LicenseService` é responsável por:

- carregar a licença;
- validar assinatura;
- validar vínculo com a instalação;
- validar estado temporal;
- disponibilizar features/perfis/limites ao contexto.

A semântica comercial e os perfis são definidos em [LICENSING-PTBR.md](LICENSING-PTBR.md).

---

# 32. Assinatura assimétrica de licença

A infraestrutura de licenciamento utiliza separação entre:

```text
chave privada → emissão/assinatura
chave pública → verificação no runtime
```

A chave pública pode integrar o runtime porque sua posse não permite emitir novas licenças.

A chave privada de emissão é um segredo e **não pode ser distribuída no release**.

A implementação criptográfica concreta e sua evolução são documentadas em [LICENSING-PTBR.md](LICENSING-PTBR.md).

---

# 33. Licença local

O caminho padrão operacional pode utilizar:

```text
storage/license.sfaao
```

O arquivo de licença da máquina de desenvolvimento **não integra o pacote de release**.

Cada instalação deve receber licença correspondente ao seu contexto autorizado.

---

# 34. Machine fingerprint

`MachineFingerprint` produz um identificador da instalação usado pelo subsistema de licenciamento.

O fingerprint:

- não é senha;
- não é chave privada;
- não deve ser tratado como autenticador humano;
- serve para vínculo lógico da licença/estado à instalação.

Mudanças de implementação precisam preservar política explícita de compatibilidade com licenças legadas.

---

# 35. Estado temporal protegido

O SFAAO mantém estado temporal para evitar que validade/licenciamento dependa apenas de leitura ingênua do relógio atual.

Conceitualmente:

```text
machine_id
timestamp/last_seen
estado anterior
integridade do estado
```

No primeiro uso válido, o estado inicial é criado em UTC.

---

# 36. TimeGuard

`TimeGuard` detecta retrocesso inconsistente do relógio.

Objetivo:

```text
tempo observado anteriormente
        ↓
retrocesso anômalo?
        ├─ não → continuar
        └─ sim → bloquear contexto protegido
```

Esse mecanismo é defesa contra adulteração temporal simples, não substitui serviço de tempo confiável externo, TPM ou hardware seguro.

---

# 37. IntegrityChecker

O bootstrap verifica a base protegida usando um manifest de integridade.

Fluxo:

```text
manifest esperado
  ↓
calcular/verificar arquivos protegidos
  ↓
modified?
missing?
  ↓
falha se estado não for válido
```

A verificação deve ocorrer antes da criação do contexto de segurança normal.

---

# 38. Hash criptográfico

Uma função hash criptográfica produz digest:

$$
h = H(m)
$$

onde $m$ é o conteúdo e $h$ o resumo.

Para proteção operacional e manifests do SFAAO são observados hashes **SHA-256** em artifacts e relatórios.

SHA-256 pertence à família Secure Hash Algorithm especificada no FIPS 180-4 [8].

Hash não fornece confidencialidade.

---

# 39. Manifests de artifacts científicos

Artifacts de calibração podem possuir `manifest.json` com:

```text
manifest_version
calibration_uid
generated_at
artifact_type
artifact_version
files[]
    name
    sha256
```

O objetivo é detectar alteração e ligar integridade ao artifact correto.

A proveniência do manifest é definida em [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md).

---

# 40. Hashes de relatórios

O pipeline rastreável de relatórios calcula SHA-256 para artifacts DOCX/PDF e pode persistir os digests junto aos metadados do relatório.

Isso permite verificar posteriormente se o arquivo entregue ainda corresponde ao artifact registrado.

O hash não transforma o laudo automaticamente em documento com assinatura digital qualificada.

---

# 41. SHA-512 do registro INPI

O certificado/depósito de registro de programa de computador utiliza resumo **SHA-512** do material submetido ao INPI.

Esse SHA-512 tem finalidade de identificação do material registrado e **não deve ser confundido** com:

- PBKDF2-HMAC-SHA-256 de senhas;
- SHA-256 dos manifests do runtime;
- SHA-256 dos relatórios.

Finalidades distintas podem usar algoritmos distintos sem contradição.

---

# 42. Segredos e material proibido no release

O release não deve conter:

- chave privada de licenciamento;
- credenciais;
- tokens;
- senhas;
- licença vinculada à máquina do desenvolvedor;
- estado temporal da máquina do desenvolvedor;
- bancos de desenvolvimento;
- casos/assinaturas reais;
- backups privados;
- documentos internos confidenciais de PI.

A exclusão deve ser verificada antes da criação do pacote final.

---

# 43. Backups

Backups podem conter a mesma sensibilidade do ambiente ativo.

Portanto:

- devem possuir controle de acesso;
- não devem entrar acidentalmente no release;
- devem preservar consistência banco/filesystem;
- devem seguir política institucional de retenção e descarte.

Detalhes de procedimento de backup/restore pertencem a [INSTALLATION-PTBR.md](INSTALLATION-PTBR.md).

---

# 44. Logs e mensagens de erro

Logs não devem registrar:

- senha;
- chave privada;
- token secreto;
- conteúdo de credencial desnecessário.

Mensagens de autenticação devem evitar enumeração de usuário.

Erros científicos podem ser detalhados para diagnóstico, mas devem respeitar privacidade e não expor caminhos/segredos em contextos públicos.

---

# 45. Dependências e supply chain

Bibliotecas de terceiros fazem parte da superfície de segurança.

O release deve:

- fixar dependências em `requirements.txt`;
- revisar atualizações antes de adotar;
- remover dependências não utilizadas;
- manter origem legítima;
- executar testes após atualização;
- evitar instalação automática de pacotes arbitrários em runtime.

O NIST SSDF recomenda integrar práticas de desenvolvimento seguro ao ciclo de software [9].

---

# 46. Hardening futuro

Sem alterar o release congelado sem análise, as seguintes melhorias devem permanecer no backlog de segurança:

- política de senha alinhada integralmente à NIST SP 800-63B-4;
- blocklist de senhas comprometidas/comuns;
- avaliação de MFA para funções privilegiadas;
- testes automatizados adicionais de autorização;
- validação Windows ACL em instalador final;
- revisão periódica do work factor de senha;
- análise periódica de dependências;
- procedimentos formais de resposta a vulnerabilidades.

Esses itens são melhorias de hardening, não declaração de falha dos resultados científicos existentes.

---

# 47. Desenvolvimento seguro

Mudanças de segurança devem seguir:

```text
requisito
→ implementação isolada
→ testes
→ revisão
→ atualização de documentação
→ changelog
→ novo manifest/release
```

Não se deve desabilitar verificação de segurança apenas para contornar erro operacional sem diagnóstico da causa.

---

# 48. Incidente de integridade

Quando o bootstrap reportar arquivo `modified` ou `missing`:

1. não assumir automaticamente ataque;
2. interromper operação protegida;
3. preservar o estado para diagnóstico;
4. comparar com release/manifest autorizado;
5. identificar atualização legítima ou alteração indevida;
6. restaurar apenas a partir de fonte confiável;
7. gerar novo manifest somente como parte de release autorizado.

---

# 49. Limitações

A segurança do SFAAO depende também de:

- segurança do sistema operacional;
- controle físico da estação;
- malware local;
- privilégios administrativos;
- proteção de backups;
- proteção da chave privada fora do runtime;
- política institucional;
- atualização de dependências.

O software não pode garantir confidencialidade contra administrador do host que possua acesso irrestrito aos arquivos em texto claro enquanto a aplicação os utiliza.

---

# 50. Relação com TRACEABILITY.md

[TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md) define canonicamente:

- proveniência;
- auditabilidade;
- relação entre entities/activities/agents;
- hashes como evidência de integridade;
- reconstrução das execuções.

[SECURITY-PTBR.md](SECURITY-PTBR.md) define os mecanismos que protegem o runtime e esses registros.

---

# 51. Relação com LICENSING.md

[LICENSING-PTBR.md](LICENSING-PTBR.md) será canônico para:

- tipos/perfis de licença;
- features;
- protocolos habilitados;
- limites/quotas;
- emissão;
- ativação;
- validade;
- semântica comercial.

Este documento trata apenas da fronteira de segurança: verificação, vínculo, chave pública/privada e estado protegido.

---

# 52. Relação com INSTALLATION.md

[INSTALLATION-PTBR.md](INSTALLATION-PTBR.md) deve especificar:

- permissões necessárias;
- criação do primeiro ADMIN;
- colocação de licença;
- validação do bootstrap;
- instalação offline/pretrained;
- backup/restore;
- verificações pós-instalação.

Não deve duplicar as definições criptográficas canônicas deste documento.

---

# 53. Checklist de segurança do release

- [ ] nenhuma credencial padrão;
- [ ] PBKDF2-HMAC-SHA-256 operacional;
- [ ] salts aleatórios;
- [ ] lockout operacional;
- [ ] ADMIN/EXAMINER validados;
- [ ] EXAMINER sem governança administrativa;
- [ ] storage protegido;
- [ ] DB/WAL/SHM protegidos;
- [ ] licença de desenvolvimento ausente;
- [ ] chave privada ausente;
- [ ] estado temporal de desenvolvimento ausente;
- [ ] manifest final correspondente ao release;
- [ ] nenhuma modificação/ausência no integrity check;
- [ ] casos reais ausentes;
- [ ] datasets de terceiros ausentes;
- [ ] backups/caches ausentes;
- [ ] requirements revisado;
- [ ] hashes de artifacts quando aplicável;
- [ ] ACL Windows validada no ambiente-alvo quando distribuído para Windows.

---

# 54. Regras normativas resumidas

1. Senha nunca em texto claro.
2. Autenticação e autorização são responsabilidades distintas.
3. ADMIN e EXAMINER devem respeitar menor privilégio.
4. Não distribuir credenciais padrão.
5. Falha crítica de bootstrap deve bloquear o contexto protegido.
6. A chave privada de licenciamento nunca integra o runtime distribuído.
7. Licença do desenvolvedor não integra o release.
8. Secure time e TimeGuard não devem ser ignorados silenciosamente.
9. Manifest de integridade deve corresponder ao release autorizado.
10. SHA-256 operacional e SHA-512 do INPI possuem finalidades diferentes.
11. Logs não devem conter segredos.
12. Backups devem receber proteção equivalente à sensibilidade dos dados.
13. Mudanças de segurança exigem testes, documentação e novo release/manifest.
14. Não declarar conformidade/certificação normativa sem validação correspondente.

---

# 55. Referências

[1] ISO/IEC 27001:2022, *Information security, cybersecurity and privacy protection — Information security management systems — Requirements*. ISO/IEC, 2022.

[2] ISO/IEC 27002:2022, *Information security, cybersecurity and privacy protection — Information security controls*. ISO/IEC, 2022.

[3] K. Moriarty, B. Kaliski, and A. Rusch, *PKCS #5: Password-Based Cryptography Specification Version 2.1*, RFC 8018, IETF, Jan. 2017. https://www.rfc-editor.org/rfc/rfc8018

[4] D. Temoshok et al., *Digital Identity Guidelines: Authentication and Authenticator Management*, NIST SP 800-63B-4, July 2025. doi: 10.6028/NIST.SP.800-63b-4.

[5] OWASP Foundation, *Password Storage Cheat Sheet*, Cheat Sheet Series. Acesso em 28 ago. 2026. https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html

[6] D. F. Ferraiolo, J. A. Cugini, and D. R. Kuhn, “Role-Based Access Control (RBAC): Features and Motivations,” *11th Annual Computer Security Applications Conference*, 1995.

[7] NIST, “Least Privilege,” *Computer Security Resource Center Glossary*, fontes incluindo NIST SP 800-53 Rev. 5.

[8] NIST, *Secure Hash Standard (SHS)*, FIPS PUB 180-4, 2015. doi: 10.6028/NIST.FIPS.180-4.

[9] M. Souppaya, K. Scarfone, and D. Dodson, *Secure Software Development Framework (SSDF) Version 1.1*, NIST SP 800-218, 2022. doi: 10.6028/NIST.SP.800-218.

---

# 56. Estabilidade documental

Esta é a definição canônica de segurança da aplicação SFAAO 2.4.0.

Alterações futuras devem distinguir explicitamente:

```text
comportamento implementado e testado do release
requisito normativo
recomendação de hardening futuro
```

Uma melhoria de segurança não deve apagar a rastreabilidade de releases anteriores nem alterar silenciosamente a interpretação de resultados científicos já produzidos.
