---
titulo: Ambiente de servidor local do Aetherius
tipo: referencia
status: rascunho
responsaveis: [pablo-rodrigues1]
atualizado: 2026-11-15
tags: [sobrenatural, servidor-local, skymp, testes, infraestrutura]
relacionados: [investigacoes/INVESTIGACAO_15-11_servidor-local.md]
---

# Ambiente de servidor local — 15/11/2026

## Resumo e estado da entrega

Sol High preparou `C:\Code\Aetherius - Servidor Local` com o fonte completo sanitizado do servidor, runtime próprio, banco PostgreSQL portátil, 433 plugins copiados do perfil oficial e scripts de build/start/stop/reset/overlays. O boot confirmou AetheriusUI, gamemode e **16 módulos ativos, 2 substituídos/desligados, 0 módulos com falha**; dois bots nativos passaram pelo login real com tokens locais, spawn e emissão de sessão da UI.

**Não equivale a homologação de todas as funcionalidades convencionais.** A referência contém caminhos incompletos, o addon nativo reaproveitado não informa commit exato e nenhum Skyrim foi aberto nesta tarefa. A entrega está pronta para revisão cruzada e uso em testes locais; paridade integral e mecânicas em jogo permanecem pendentes.

**Decisão de encerramento — 15/11/2026:** o dev aceitou, por resposta do orquestrador, encerrar como **ambiente pronto para testes com homologação parcial**. Alma/market_stalls (migrations e INSERT IGNORE), capacidades UNSUPPORTED e addon v19 sem commit verificável ficam como bloqueios abertos de outra tarefa. Não baixar nem recompilar nada adicional nesta entrega. A revisão por Terra High e a consolidação/publicação no Acervo são responsabilidade do orquestrador; o aceite parcial não constitui revisão técnica nem resolução desses bloqueios.

## Escopo e integridade

- Fonte servidor somente leitura: `C:\Code\Aetherius - OFICIAL\aetherius-server-main`.
- Fonte cliente somente leitura: `C:\Code\Aetherius - OFICIAL\aetherius-client-main`.
- Modpack consultado, sem escrita: `C:\modOrganizer`.
- Ambiente novo: `C:\Code\Aetherius - Servidor Local`.
- Relatório operacional: `C:\Code\Aetherius - Servidor Local\README.md`.
- Nenhum código de Sobrenatural, patch de lobisomem ou mudança no painel foi aplicado ao oficial, cliente ou MO2.
- O fonte veio de um snapshot sem `.git`: **não há commit upstream verificável**. O manifest histórico v0.37.0 referencia uma revisão do cliente, não um commit do fonte do servidor.

| Identificador | Valor |
|---|---|
| Git inicial da cópia local `source/` | `310cb598fd382cf4d6598cb34eada5db1a3fa9f8` |
| Mensagem do commit inicial | `base: cópia de aetherius-server-main` |
| Arquivos do inventário sanitizado | 2778 |
| SHA-256 agregado do inventário | `a14e6741b9637aed8cddc956b8aec5e0b910d7ef4334923f141e80f61ec3ebce` |
| Kit local existente | `C:\Users\pablo\Downloads\AETHERIUS - KIT TESTE DANO 14-11 (v19).zip` |
| SHA-256 do ZIP | `70f267ba193ebac81bacca97f8ea82cb2246ac6328a1a84e34d986d93d41722f` |
| SHA-256 de `scam_native.node` | `7822668863712be2185bcbe8813f327b1872bd20c090110d10529b63d673f04f` |

Inventários detalhados: `C:\Code\Aetherius - Servidor Local\evidence\reference-inventory.json` e `kit-provenance.json`. O agregado é SHA-256 da concatenação ordenada de caminho relativo, tamanho e hash individual, separados por NUL e linhas LF. Foram excluídos o mundo persistido de exemplo (`server/world`), `.env*`, configurações `*.local.json`, material de chaves e `node_modules` da origem. Nenhum dado de jogador ou credencial de produção foi usado. Duas contas sintéticas são criadas no banco novo.

## Consulta documental realizada

Lidos `AGENTS.md`, `Sobrenatural/RETOMADA.md`, README do servidor, `build.sh`, `cmake_server_build.txt`, CMakeLists, package.json, guias de configuração/database em `docs/`, entradas TypeScript, gamemode, staging de gameplay, sincronizador de plugins, referência Meridian do cliente e LEIA-ME/patches de kdu. O kit v19 foi inspecionado como ZIP; seus instaladores não foram executados, pois escrevem no MO2.

Lidos no Acervo: INDICE, REGRAS, sistemas Sobrenatural/Lobisomem/UI Core/Painel/GameplayCore e ADRs 0001–0003. O estado documentado confirma que o pacote de lobisomem é separado, a UI de Sobrenatural é anterior às mecânicas e o código prevalece sobre material de design. A infraestrutura local não altera essas decisões.

Consulta houseCARL: servidor `2.0.0+5e3c744862bc17c85ebe850830603a9a3e6f0779`, perfil **AETHERIUS - GRAFICO - QUALIDADE**, 563 mods habilitados, 433 plugins ativos/resolvidos. Os cinco masters base vencem pelo Data do jogo. A configuração cliente encontrada vence em `Data/Platform/Plugins/skymp5-client-settings.txt`; não há vencedor em PluginsDev na consulta. Nenhuma ferramenta de escrita do houseCARL foi chamada.

## Como o servidor funciona — evidências no fonte copiado

Todos os caminhos abaixo têm prefixo `C:\Code\Aetherius - Servidor Local\source\` e correspondem byte a byte ao snapshot importado, salvo o ajuste local descrito adiante.

| Evidência | Consequência |
|---|---|
| `server/CMakeLists.txt:35` e seguintes | target `skymp5-server` é SHARED, saída `scam_native.node`; link nativo com N-API |
| `server/CMakeLists.txt`, geração de `launch_server.bat` | execução por `node dist_back/skymp5-server.js` |
| `server/package.json`, script build-ts | `tsc --noEmit` + esbuild; saída `build/dist/server/dist_back/` |
| `server/ts/scampNative.ts:1` | `require(process.cwd() + '/scam_native.node')` exige cwd correto |
| `server/ts/index.ts:213,226,327` | inicializa AetheriusUiSystem, cria servidor nativo e carrega gamemode |
| `server/ts/settings.ts:136` em diante | lê server-settings.json e aplica a loadOrder entregue |
| `server/ts/shippedLoadOrder.ts` | compara presença/tamanho dos plugins; sincroniza loadOrder, preservando settings |
| `server/ts/ui.ts:101–139` | HTTP em 3000 para jogo em 7777; `uiListenHost` controla bind |
| `server/cpp/addon/ScampServer.cpp:260–263,410` e `mp_common/Networking.cpp:155` | `listenHost` chega ao socket do jogo; `server-ip` sozinho não é bind |
| `server/gamemode/core/pg.js:22–29` | PostgreSQL via GAME_DATABASE_URL, com fallback DATABASE_URL |
| `server/gamemode/database.js:27,110–120` | schema-core.sql automático; tradutor `?` → parâmetros PG; não traduz INSERT IGNORE |
| `server/ts/systems/login.ts:71–91,318–414` | login por token validado no PG ou profileId offline; somente token cria identidade oficial |
| `server/ts/systems/official-identity.ts` | vínculo em memória, atrelado à conexão e ator; sem credencial em mp.set/persistência |

Trecho de execução efetivamente preservado:

```ts
// server/ts/scampNative.ts
const scampNativeNode = require(process.cwd() + "/scam_native.node");
// server/ts/index.ts
new AetheriusUiSystem();
// entrypoint efetivo: node <ambiente>/scripts/serve.cjs
// wrapper faz cwd(runtime/server), aplica somente env local e carrega o backend compilado.
```

O build C++ usa C++20, VS 2022 e vcpkg x64-windows-sp. CMake exige diretório `<source>/build`. As flags desligam targets do cliente, SkyrimPlatform, scripts e testes, mas o manifest Windows ainda lista portas de cliente como CEF/CommonLib. Não foi alterado o manifest para reduzir dependências, pois isso exige uma mudança de build própria.

## Ferramentas encontradas e dependências reaproveitadas

| Ferramenta | Versão/local |
|---|---|
| Node do sistema / npm | 22.14.0 / 10.9.2, `C:\Program Files\nodejs` |
| Node do kit, usado no runtime | 24.18.0, `<ambiente>\tools\node\node.exe` |
| PostgreSQL portátil | 18.4, `<ambiente>\tools\postgresql\bin` |
| Yarn | 1.22.22 |
| CMake | 4.3.0-rc3 |
| Git | 2.55.0.windows.5 |
| Python | 3.13.3 |
| VS Build Tools 2022 | 17.14.37614.0, com MSVC 14.44 |
| VS Community adicional | 2026 / 18.9.12120.119; não usado |
| vcpkg | `C:\dev\vcpkg`, commit `04a9d8e5212d01ee1dd9478eadd9caade4f8b0d4` |

Não havia addon/build nativo pronto nas cópias de `C:\Code`; ele foi encontrado no kit v19 em Downloads. O kit tem cerca de **0,34 GiB descompactado**, e dele foram extraídos 1779 arquivos selecionados: addon, Node, PostgreSQL e roteiros sem jogo. Não foram importados backend/gamemode antigos do kit, configs de produção, banco existente, cliente modificado ou o instalador de MO2. Pacotes npm do próprio projeto foram instalados apenas na cópia.

O sistema também possui um PostgreSQL instalado em `C:\Program Files\PostgreSQL\18`; ele não foi alterado, reiniciado nem usado. O cluster portátil é independente.

## Configuração local e decisões

O template `C:\Code\Aetherius - Servidor Local\config\server-settings.local.json` contém:

```json
{
  "port": 7777,
  "listenHost": "127.0.0.1",
  "uiListenHost": "127.0.0.1",
  "server-ip": "127.0.0.1",
  "maxPlayers": 10,
  "offlineMode": true,
  "master": "",
  "additionalServerSettings": [],
  "dataDir": "data",
  "databaseDriver": "file",
  "databaseName": "world-local",
  "gamemodePath": "gamemode/phase0-basic.js"
}
```

A loadOrder é preenchida pelo sincronizador e validada no backend. Os 433 plugins, incluindo CC e masters base, foram copiados do Skyrim/MO2 para `runtime/server/data`. A ferramenta oficial confirmou perfil, ordem, tamanho e CRC; não foi necessário baixar dados do Skyrim. O diretório do jogo é `C:\Games\Steam\steamapps\common\Skyrim Special Edition`; o cliente de referência usa os mesmos perfis.

1. **Isolamento de dados:** estado nativo em world-local; PG em state/postgresql; diários em storage. Reset move os três para backups, sem apagar nem tocar nas referências.
2. **Sem autenticação externa:** offlineMode desliga master/Discord; tokens locais validam contra o PG desta pasta e permitem o vínculo oficial exigido pelo Core/painel. Nenhum serviço externo do site é simulado como se fosse produção.
3. **Segredos locais:** Initialize gera SOUL_SECRET novo e tokens sintéticos, nunca os mostra no terminal e os exclui do Git. O wrapper limpa variáveis herdadas de banco/serviços/autenticação antes de carregar config/local.env.
4. **Bind explícito:** listenHost e uiListenHost são loopback; voz também. O wrapper recusa destinos de banco diferentes de `127.0.0.1:5442/aetherius_local`.
5. **Código convencional completo:** cópia preserva módulos e gates; não importa as flags agressivas/correções do kit de dano. Não se presume conhecer o `.env` privado efetivamente instalado em produção.
6. **Correção indispensável do loader:** o teste truthy em settings.ts ignorava master vazio, deixando o gateway público padrão no objeto Settings. `overlays/local-runtime/001-falsy-settings.patch` usa hasOwnProperty para respeitar propriedades presentes, inclusive false/null/"". Aplicado só na cópia, backend recompilado e log `No master server specified` confirmado. Revisão cruzada pendente.

As decisões de infraestrutura são locais e reversíveis; a publicação de eventual ADR no Acervo deve ser consolidada pelo orquestrador.

## Build e montagem executados

1. Importação sanitizada, hashes e commit inicial em source.
2. `npm ci --no-audit --no-fund` em source/server e source/server/gamemode.
3. `npm run build-ts`: typecheck e bundle aprovados.
4. `node tools/stage-gameplay.js --out <ambiente>/runtime/server/gameplay`: build dos pacotes do Core e classe/grupo, compilador de dano, migrations e políticas/catálogos; **failures=0**.
5. Apply-Overlay local-runtime: git apply --check, aplicação, recompilação backend.
6. Prepare.ps1: gamemode/config/vendor/profissões/tools, bundle backend, addon e plugins no runtime. Persistência de origem excluída.
7. Initialize.ps1: cluster PG em loopback, banco vazio, schema-core e duas contas fictícias; migrations gc_* aplicadas pelo gate DEV local já previsto no Core.

O build nativo não foi feito: `Build-Native.ps1` recusa continuar sem `-AllowDependencyDownloads`. Manifest com **29 dependências diretas**, além de features/transitivas e possíveis downloads de vários GB; estimativa de 30–120+ minutos, não medida. Instalação/download pesado depende de decisão explícita do orquestrador, se exigida paridade comprovada do binário.

## Funcionalidades e limites observados

Módulos que o registry marcou ativos: gameplay-core, enemy-spawns, durability, skydeath, skysocial, skyinventory, refinement, container-ownership, residences, shop, governance, market-stalls, player-panel, soul, survival, voip. NPC-cleaner fica desligado quando enemy-spawns assume o mundo; o death-service antigo é substituído pelo SkyDeath.

| Área | Prova e limite |
|---|---|
| GameplayCore | inicializou; banco SUPPORTED; classes/raças/pedras SUPPORTED. Isso descreve dependências/capacidade, não teste de toda a mecânica |
| Leveling | modo active, mas relatório registra `SERVER_CONFIRMED_DEATH_OWNER_NOT_DECIDED`; bridge de XP por abates instalado, progressão integral não homologada |
| Dano | domínio SUPPORTED, modo off; famílias nativas permanecem off conforme defaults. Não foram ativadas fórmulas de teste do kit |
| Religião/spells/perks | modos off, catálogos não entregues; declaração UNSUPPORTED preservada |
| Profissões | adaptadores copiados; modo audit, sem executores de mundo, UI bloqueada pelos gates originais |
| Loja | módulo/migrations ativos; vendas desligadas pelo rules.json original |
| Painel F7 | bootstrap official-readonly e ações locais do jogo; APIs externas não configuradas. Usuário sintético 1 é superadmin no PG local |
| Alma | bootstrap ativo, mas primeiro personagem falha por tabela character_soul ausente; também há dependências de schema e SQL MySQL não portado. Não funcional end-to-end |
| Comércio legado | boot ativo, mas a checagem periódica de market-stalls falhou por tabela market_stalls ausente; schema legado esperado em server/packages/database não foi distribuído |
| Voz | WebSocket escutando em loopback; captura/reprodução em Skyrim não testada |

O módulo de Alma demonstra por que contar módulos ativos não prova paridade funcional. Investigação detalhada em `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_15-11_servidor-local.md`. Não foram criadas migrations ad hoc para esconder o problema.

## Validação efetivamente feita

| Verificação | Resultado |
|---|---|
| PowerShell AST dos scripts | parse sem erros após correções locais |
| typecheck backend + esbuild | aprovados |
| staging oficial GameplayCore | todos os builds aprovados, failures=0 |
| addon do kit carregado pelo Node portátil | aprovado; exports ScampServer e writeLogs |
| 433 plugins | perfil da referência igual ao MO2, mesmos arquivos/ordem/CRC |
| listeners jogo/HTTP/voz/PG | apenas 127.0.0.1:7777 UDP / 3000 TCP / 7778 TCP / 5442 TCP |
| HTTP manifest | GET http://127.0.0.1:3000/manifest.json aprovado |
| AetheriusUI + gamemode | logs confirmados; 16 módulos, 0 falhas de boot |
| master externo | `No master server specified`; zero conexões TCP externas estabelecidas no snapshot do processo |
| dois bots no backend completo | login por tokens sintéticos, spawn, identidade oficial e logs de emissão de sessão da UI; duas identidades simultâneas |
| healthCheckAll | módulos ativos reportaram healthy=true; não inclui os caminhos de Alma que falham após login |
| stop/reset/restart | aprovados; backup em backups/reset-20261115-134407-776 e novo cluster/mundo vazios |
| boot final sem bots | sem erros fatais; coleta inicial teve zero linhas [error], porém a observação posterior capturou market_stalls ausente na checagem periódica. Verify registra o erro não fatal e não o oculta |
| guard do launcher | recusou perfil oficial ainda sem settings locais, como esperado; nenhum jogo foi aberto |
| kdu servidor patch --check | verificado sem aplicação; Sobrenatural/lobisomem não integrados |

Provas: `C:\Code\Aetherius - Servidor Local\evidence\verification.json`, `verification-with-bots.json`, `smoke-live-server.json` e logs `server-20261115-134100.stdout.log` (bots) / `server-20261115-134415.stdout.log` (boot limpo). As primeiras tentativas de teste também permanecem nos logs, sem serem apresentadas como êxito.

**O que os bots não validam:** o bot é o MockServer nativo integrado ao servidor real, como no kit, e não um cliente externo completando handshake UDP. A API Bot descarta pacotes recebidos; a emissão da sessão UI foi observada por logs, sem validar recebimento/renderização/snapshot no cliente. Um primeiro harness tentou interceptar sendCustomPacket, mas a propriedade nativa é somente leitura; foi corrigido para não alterar a API nativa. O teardown dos bots produziu aviso/erro não fatal de MockServer no logout; foi separado do boot limpo posterior.

## Cliente/MO2 — ponto preparado, sem instalação

Pacotes de settings, ambos fora do modpack:

- `C:\Code\Aetherius - Servidor Local\client-local-mod\Platform\Plugins\skymp5-client-settings.txt`: token local, recomendado para Core/painel.
- `C:\Code\Aetherius - Servidor Local\client-local-mod-profile-id\Platform\Plugins\skymp5-client-settings.txt`: porta de profileId offline do fonte cliente, com limitações de identidade.

O mod deve ser instalado manualmente em **perfil de teste separado**, com raiz de pasta equivalente a Data e sem trocar o cliente/dlls atuais. Start-Client.ps1 exige nome explícito de perfil, MO2 fechado, configurações efetivas de loopback e os mesmos plugins/CRC antes de lançar SKSE com o token fictício no ambiente do processo. Não modifica perfis, configs ou arquivos do jogo.

A referência cliente `C:\Code\Aetherius - OFICIAL\aetherius-client-main\MERIDIAN-COMPATIBILIDADE.md` registra alvo Skyrim 1.6.1170, SKSE 2.2.6, Meridian 1.5.0/CEF152 e par SkyrimPlatform/Impl/libnode. A porta local explícita já aparece em `client/src/services/services/authService.ts:129–134`; server-info-ignore é tratado por settingsService.ts. O ramo profileId não cria o vínculo oficial e, portanto, não substitui token local para testar esses recursos. Jogo/cliente não foram executados nem alterados.

## Overlays e próximas etapas

`Apply-Overlay.ps1` aplica somente em source, sempre após git apply --check. Novos arquivos usam files/server/... e ficam visíveis por intent-to-add; existentes exigem patch. Build e Prepare propagam o fonte local para o runtime. A pasta lobisomem-kdu apenas aponta ao patch em `C:\Code\Aetherius - New Systems\Sobrenatural\AETHERIUS - LOBISOMEM 14-11 - KDU\patches`; a pasta sobrenatural está reservada, sem payload. Somente local-runtime foi aplicado.

Pendências para o orquestrador:

1. Providenciar revisão cruzada por **Terra High** dos scripts, ajuste de settings e limites da entrega; registrar achados/decisões/versão final. Nenhum outro modelo revisou esta implementação dentro deste worker; ele não foi autorizado a criar helpers.
2. Tratar em **outra tarefa** os bloqueios abertos de Alma/market_stalls (migrations e SQL legado), as capacidades UNSUPPORTED e a equivalência/procedência do addon. O ambiente parcial foi aceito pelo dev para testes; não foi autorizada a reimplementação desses sistemas nem a remoção de gates.
3. Não executar downloads ou recompilação adicionais nesta entrega. Se outra tarefa exigir compilação nativa, obter autorização própria de downloads/toolchains pesados antes de executá-la; a alternativa atual é o addon local do kit.
4. Instalar manualmente o perfil/mod local após autorização própria de escrita no MO2 e homologar Skyrim, UI Core, login, poderes, progressão e voz; nada disso foi validado em jogo.
5. Consolidar diário/RETOMADA/Acervo, status/índice/changelog e eventual ADR. **Publicação no Acervo não foi executada por este worker:** o despacho autorizou escrita somente no ambiente novo e em Sobrenatural/docs; este relatório está pronto para a consolidação pelo coordenador.

Servidor e PG ficaram ativos apenas em loopback ao concluir a preparação, para inspeção pelo coordenador. Para encerrar: `& 'C:\Code\Aetherius - Servidor Local\scripts\Stop.ps1'`.

## Correções pós-revisão

Aplicadas em 15/11/2026 por **Opus 5.5 Low** (substituto temporário do Sol High, escalação de 2026-11-15) sobre `C:\Code\Aetherius - Servidor Local`, commit anterior `256e423`, resultado **commit `c8eb72e`**. Base: apontamentos 1–4 e "Arbitragem do orquestrador" de `C:\Code\Aetherius - New Systems\Sobrenatural\docs\REVISAO-AMBIENTE_terra.md`. Sem downloads nem recompilação; nada alterado em `C:\Code\Aetherius - OFICIAL` nem em `C:\modOrganizer`. Confirmação por Terra High (ou Opus Low independente, durante a escalação) pendente.

| # | Apontamento | Correção |
|---|---|---|
| 1 | README sem bootstrap | `README.md` dividido em **Parte 1 — Uso da instalação pronta** e **Parte 2 — Bootstrap do zero**: artefatos (snapshot oficial, kit v19, cliente oficial, modpack, Skyrim, ferramentas), caminhos, hashes (`importSha256` a14e6741…3ebce, ZIP 70f267ba…722f, addon 78226688…f04f), criação de pastas e ordem `provision.py` → `git init`/baseline do `source` → overlay `local-runtime` → `Build` → `Prepare` → `Start`/`Verify`, derivada de `scripts/provision.py` e de `Initialize.ps1` (depende de `source/server/node_modules` e `runtime/server/gamemode`). |
| 2 | Stop não esperava o PID | `scripts/Stop.ps1`: após `Stop-Process`, `Wait-Process -Timeout` (parâmetro `-TimeoutSeconds`, padrão 30); falha se o PID ou qualquer processo do servidor persistir, **sem** parar o PostgreSQL; após `pg_ctl stop` confere `pg_ctl status`. `scripts/Reset.ps1`: depois do Stop, re-verifica processo e cluster e aborta antes de mover qualquer pasta. |
| 3 | Start não idempotente / Smoke | `scripts/Start.ps1`: se a instância já existe com UDP 7777 e TCP 3000 em loopback pelo mesmo PID, retorna sucesso; se existe sem listeners, falha pedindo Stop; `-Smoke` com instância ativa falha. README documenta que `-Smoke`/`smoke.cjs` **alteram dados** (usar só em ambiente descartável ou após Reset). |
| 4 | Afirmação "sem credenciais" | README qualificado: a cópia exclui `.env*`, `*.local.json`, `.pem/.key/.pfx`, `server/world` e credenciais conhecidas, mas contém a fixture de teste unitário em `source/unit/OpenSSLSignerTest.cpp` (valor não reproduzido). |

### Evidências (PowerShell, 15/11/2026)

Estado inicial: servidor **ligado** (Node PID 7088, PostgreSQL em 5442). Estado final: **ligado** (PID 3724), mesma persistência.

```
== Start (ja ativo)
Local server already running and healthy: PID 7088 (127.0.0.1:7777 UDP / :3000 HTTP).
== Stop
Stopped local server PID 7088.
esperando o servidor desligar....feito
servidor está parado
0,58 s; procs: 0; pg5442: 0
== Stop (ja parado)
exit ok
== Start
Local schema and two synthetic accounts ready; local tokens renewed (24h).
Local server PID 11244: 127.0.0.1:7777 UDP / :3000 HTTP. Logs: ...\logs\server-20261115-141605.stdout.log
== Start duplo
Local server already running and healthy: PID 11244 (127.0.0.1:7777 UDP / :3000 HTTP).
== Verify
Verified listeners, HTTP manifest, UI, master-disabled and boot markers. Nonfatal error lines: 0; external established TCP connections: 0.
```

Reset (seguro: só move para `backups/`), seguido de restauração manual da persistência original:

```
== Reset
Stopped local server PID 11244.
esperando o servidor desligar....feito
servidor está parado
Persistence archived at ...\backups\reset-20261115-141633-974.
procs: 0; conteúdo: state, storage, world-local; state exists: False
== Restaurar (Move-Item de volta: state, world-local, storage, data/aetherius-*.json)
restante no backup: 0   (pasta vazia removida)
== Start + Verify
Local server PID 3724: 127.0.0.1:7777 UDP / :3000 HTTP.
Verified ... Nonfatal error lines: 0; external established TCP connections: 0.
```

Não testado: o caminho de aborto do Reset/Stop com processo travado (exigiria simular um Node que ignora o término); a lógica é coberta por leitura de código. Parte 2 do README não foi executada de ponta a ponta (exigiria reimportar e `npm ci`).
