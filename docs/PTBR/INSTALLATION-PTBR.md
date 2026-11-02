# SFAAO 2.4.0 — Instalação e distribuição homologadas

**Versão documental:** 2.4  
**Idioma:** PT-BR  
**Projeto:** SFAAO — Software Forense para Análise de Assinaturas Offline  
**Status:** Documentação de entrega do release 2.4.0  
**Revisão documental:** 29/10/2026  
**Base documental anterior:** 30/09/2026

---

## 1. Objetivo e escopo

Este documento orienta o uso dos **instaladores concluídos e homologados** nas plataformas **Windows 11, Linux Mint e Red Hat**. O SFAAO é fornecido como aplicativo protegido; o cliente não precisa montar uma árvore Python de desenvolvimento. Os pacotes foram reunidos em `SFAAO_DISTRIBUTION_2.4.0/` e verificados por SHA-256. Não confundir homologação relatada do produto com certificação independente ou aprovação de desempenho para qualquer situação pericial.

## 2. Artefatos da distribuição e integridade

| Plataforma | Arquivo em `SFAAO_DISTRIBUTION_2.4.0/` | SHA-256 |
|---|---|---|
| Windows 11 | `03_WINDOWS_EXE/SFAAO_Setup_2.4.0_CPU_FINAL_PRO_LAUNCHER_RC1.exe` | `5de43c5dba6559e55442e4c25c6ba13303c95561aef09d2630059b160f78c66d` |
| Linux Mint / Debian / Ubuntu (amd64) | `01_DEB/sfaao_2.4.0_amd64.deb` | `5e7c730ed0cdd59426d896f31dc8a277ef897fba104aa38511cec0e3abc522a5` |
| Red Hat e distribuições RPM compatíveis (x86_64) | `02_RPM/sfaao-2.4.0-1.x86_64.rpm` | `d5ead5e0fe03e2fb10fef602afc93200ff568398cda20745dc237ebfb8de4d4e` |

Os três hashes constam do manifesto oficial **`06_CHECKSUMS/SHA256SUMS`** e foram verificados com `sha256sum -c`. **O sufixo `RC1` faz parte do nome do instalador Windows efetivamente homologado**; não o renomeie sem atualizar os registros. Para conferir os pacotes, acesse a raiz da distribuição e execute:

```bash
sha256sum -c 06_CHECKSUMS/SHA256SUMS
```

A comparação de hash verifica igualdade de bytes com o manifesto confiável, **não** substitui verificação de origem/autenticidade, política de assinatura ou inspeção antimalware.

## 3. Conteúdo de segurança do release

O cliente recebe o launcher, os recursos protegidos, os componentes públicos de verificação da licença e a estrutura operacional necessária. Não acompanha a distribuição: chave privada do fabricante, emissor administrativo de licenças (`license_generator.py`), credenciais, casos reais, bancos de desenvolvimento, assinaturas reais, datasets de terceiros nem modelos internos de homologação. O mecanismo `SecurityBootstrap` realiza verificações **internamente, em background, sem janela ou tela de bootstrap**. Falhas críticas impedem a operação protegida.

A proteção contra engenharia reversa eleva a dificuldade de inspeção do código; não constitui garantia absoluta contra engenharia reversa. A licença operacional é distinta da licença jurídica e das licenças dos datasets de terceiros.

## 4. Windows 11 — instalação e operação

1. Verifique o hash do `.exe` da distribuição usando ferramenta SHA-256 de sua confiança.
2. Execute o instalador Windows `SFAAO_Setup_2.4.0_CPU_FINAL_PRO_LAUNCHER_RC1.exe` de acordo com as políticas da instituição.
3. Inicie pelo atalho/launcher instalado, sem executar scripts internos de desenvolvimento.
4. Solicite e instale uma licença válida, quando exigido, por meio das ferramentas de cliente disponibilizadas.
5. Na primeira utilização de um ambiente operacional sem usuários, cadastre o administrador inicial; **não existem credenciais administrativas padrão**.

O armazenamento operacional deve permanecer separado da base protegida. Em atualizações ou desinstalações, preserve os dados forenses segundo a política institucional. A geração de relatórios **DOCX e PDF foi homologada em Windows 11**, conforme confirmação da equipe responsável pelo release.

## 5. Linux Mint / Red Hat — requisitos

Os pacotes `.deb` e `.rpm` incluem a **imagem OCI protegida** do SFAAO, mas o sistema ainda precisa dispor de **Podman ou Docker operacional**. Para a interface gráfica atual, é necessária uma sessão **X11** disponível; o launcher repassa a variável `DISPLAY` e o socket gráfico conforme as regras do sistema. O uso de contêiner não dispensa os requisitos do sistema operacional, bibliotecas necessárias ao runtime do contêiner ou configuração de permissões/SELinux onde aplicável.

O launcher Linux instalado em `/usr/bin/sfaao` utiliza a imagem `localhost/sfaao-linux:2.4.0-cpu`. A imagem de fechamento conferida no Podman tem identificador OCI `06fe901499d23db24b4c9027e7feac30531139fd2828db1d013a27cbbe8fc479`. A imagem TAR incluída nos pacotes DEB e RPM tem **SHA-256 `e2d04f319fb57ff712af26deb5efe666bb2763d26495f287e53bd9c029dba362`**. **Image ID e hash do arquivo TAR são identificadores distintos.**

Exemplos de instalação por gerenciador de pacotes, sujeitos às permissões e regras institucionais:

```bash
# Debian / Ubuntu / Linux Mint — dentro da pasta da distribuição
sudo apt install ./01_DEB/sfaao_2.4.0_amd64.deb

# Red Hat — dentro da pasta da distribuição
sudo dnf install ./02_RPM/sfaao-2.4.0-1.x86_64.rpm
```

Antes de executar `sfaao`, assegure que Podman ou Docker esteja instalado/configurado para o usuário. O script de instalação fornecido no pacote poderá auxiliar a configurar o ambiente; revise seu conteúdo antes de execução. A habilitação de acesso X11, gestão de DNS e rótulos SELinux é dependente do host.

## 6. Diretórios, licença e atalhos Linux

No Linux Mint, os dados da instalação principal ficam normalmente em `~/.local/share/SFAAO/runtime/`, com:

```text
runtime/storage              casos, banco e licença operacional
runtime/models/default       modelos predefinidos autorizados
runtime/models/custom        modelos personalizados autorizados
runtime/security_state       manifesto/estado de segurança operacional
runtime/home                 diretório persistente do usuário do contêiner
runtime/data/datasets        datasets importados pelo usuário
runtime/data/images          imagens importadas pelo usuário
runtime/data/models          modelos importados pelo usuário
```

O launcher **`/usr/bin/sfaao`** cria o atalho `~/SFAAO_Data` para `~/.local/share/SFAAO/runtime/data`, contendo `datasets`, `images` e `models`. Já a execução direta de `/opt/sfaao/run-sfaao.sh` **não cria esse atalho**: é uma distinção importante para homologação isolada. Em ambientes de teste, a variável `SFAAO_ROOT` pode apontar para outro armazenamento, sem alterar o principal; não copie casos nem credenciais reais para testes sem autorização.

Comandos de cliente disponíveis no empacotamento Linux: `sfaao`, `sfaao-license-request`, `sfaao-license-install`. A licença é externa à imagem do contêiner e é verificada no runtime. A autenticação por usuário permanece necessária mesmo com licença válida.

## 7. Inicialização e segurança

Fluxo conceitual:

```text
launcher → validação de integridade do pacote → runtime de contêiner (Linux)
         → SecurityBootstrap interno/background (sem tela própria)
         → validação de licença, identidade, estado temporal e integridade
         → primeiro administrador (ambiente sem usuários) ou login
```

A sequência interna exata depende do runtime. Não apresentar o bootstrap como uma interface gráfica ou etapa que exija clique do usuário. A aplicação pode interromper a inicialização caso uma verificação crítica falhe.

## 8. Treinamento, comparação e relatórios

O produto inclui fluxos de treinamento, calibração científica e forense, comparação individual LORO/A4, inspeção de score pairwise, comparação em lote e atualização de contexto. O uso depende de autorização, licença, dados apropriados, modelo e calibração selecionados. A calibração deve permanecer no mesmo domínio A(N−1) da inferência, conforme [CALIBRATION-PTBR.md](CALIBRATION-PTBR.md).

A geração dos dois formatos de laudo, **DOCX e PDF**, foi homologada em **Windows 11, Linux Mint e Red Hat**, conforme confirmação do responsável pelo release. Capturas de um formato não devem ser apresentadas como imagens do outro. A emissão de laudo não substitui avaliação e revisão pelo perito responsável.

## 9. Dados, privacidade, backup e atualização

O armazenamento operacional deve permanecer apartado do software protegido. O pacote não concede direitos de redistribuição de datasets GPDS, CEDAR, BHSig260, MCYT, UTSig ou de outros terceiros. Os modelos utilizados devem ser obtidos, treinados ou importados legitimamente, com registro de proveniência.

Antes de qualquer atualização, migração ou desinstalação, faça **backup consistente** dos bancos SQLite (considerando arquivos WAL/SHM ou a Online Backup API), casos, modelos, calibrações, relatórios e metadados necessários. A mudança de host poderá requerer nova emissão ou vínculo de licença. Não misturar manualmente os recursos protegidos de versões diferentes. **A remoção do programa não autoriza a remoção automática de evidências e dados do cliente.**

## 10. Registro da homologação e limitações documentais

| Item | Situação documentada |
|---|---|
| Instalador Windows 11 | Homologado conforme testes comunicados pela equipe |
| Instalador Linux Mint / DEB | Homologado conforme testes comunicados pela equipe |
| Instalador Red Hat / RPM | Homologado conforme testes comunicados pela equipe |
| Geração DOCX / PDF | Homologada nas três plataformas |
| Conteúdo estrutural DEB/RPM, imagem TAR e SHA-256 | Conferido no fechamento da distribuição |
| Manifesto `SHA256SUMS` dos três instaladores | Verificado com resultado `OK` para todos |

Este quadro não substitui o **dossiê de evidências de homologação**. A versão exata do Red Hat, datas e registros de testes individuais devem constar de evidências institucionais quando exigidos. Não inferir, a partir da homologação geral, a execução de cada teste científico, migração, política de ACL ou experimento não documentado separadamente.

## 11. Documentação relacionada

- [TUTORIAL-PTBR.md](TUTORIAL-PTBR.md): operação, contextos e quatro ações da janela de comparação.
- [LICENSING-PTBR.md](LICENSING-PTBR.md): solicitação, instalação, perfis e limites de licença.
- [SECURITY-PTBR.md](SECURITY-PTBR.md): verificações internas e políticas de segurança.
- [DATASETS-PTBR.md](DATASETS-PTBR.md): perfis e governança de dados.
- [TRACEABILITY-PTBR.md](TRACEABILITY-PTBR.md): cadeia de proveniência e auditabilidade.

**Regra de entrega:** o usuário recebe um pacote homologado, não um projeto Python para compilar; os segredos e instrumentos administrativos do fabricante permanecem separados da distribuição ao cliente.
