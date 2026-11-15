# Revisão cruzada — ambiente de servidor local

**Data:** 15/11/2026. **Revisor:** Terra High; autor da entrega: Sol High. **Escopo:** `C:\Code\Aetherius - Servidor Local`, em especial scripts, configuração, overlays e README. Revisão do checkout raiz `256e423` e baseline do repositório `source` `310cb598fd382cf4d6598cb34eada5db1a3fa9f8`, com `source/server/ts/settings.ts` modificado pelo overlay local. Fontes oficiais e MO2 não foram modificados nesta revisão. O aceite de homologação parcial e os bloqueios conhecidos de Alma/market_stalls, capacidades UNSUPPORTED e procedência do addon permanecem fora desta revisão.

## Veredito

**Aprovado com ressalvas para uso local na instalação existente; não aprovado como procedimento reproduzível do zero.** O boot, parada e retomada foram reproduzidos; os pontos abaixo exigem decisão/correção pelo autor. A revisão é estática e por amostragem, não prova ausência absoluta de dados sensíveis nem segurança de cada dependência e caminho do código copiado. Achados ainda não foram arbitrados ou corrigidos; versão final pós-correção pendente do orquestrador.

## Achados

### 1. Médio — README não permite reconstruir o ambiente do zero

Evidência: `C:\Code\Aetherius - Servidor Local\README.md`, seções “Pré-requisitos e build” e “Iniciar e parar”; `.gitignore:1-8` exclui `source/`, `runtime/`, `tools/` e `state/`; `scripts/provision.py:6-7,15-17,35-53` depende de snapshot oficial e ZIP v19 em caminhos específicos. O README pressupõe que o ambiente e ferramentas já existam e não descreve executar `provision.py`, criar suas pastas necessárias, instalar Node/Git/npm, nem a sequência inicial de importação, build, preparação e inicialização. O Git raiz contém apenas 35 arquivos rastreados e não contém o addon nem o fonte ignorados. Uma pessoa com apenas o checkout não consegue seguir o primeiro `Start`.

**Correção sugerida:** documentar separadamente uso da instalação pronta e bootstrap completo a partir do checkout, identificando artefatos necessários, hashes, caminhos ajustáveis, criação de diretórios e ordem exata de provisionamento/build/prepare/start; explicitar que exige o snapshot, kit e modpack licenciados. Testar em cópia limpa antes de declarar reproduzível. Não executar downloads ou rebuild nesta revisão.

### 2. Médio — parada forçada não aguarda a saída do processo antes do reset

Evidência: `scripts/Stop.ps1:2-5` chama `Stop-Process` sem esperar o término do PID e imediatamente para o PostgreSQL; `scripts/Reset.ps1:3-9` chama Stop e em seguida move a persistência. `Stop-Process` pode retornar antes da saída completa, deixando corrida com escrita/destravamento de arquivos e falha parcial do backup. O README já reconhece perda possível de gravações ainda não persistidas por parada forçada, mas não esta corrida.

**Correção sugerida:** aguardar explicitamente a saída do processo identificado com timeout, confirmar que nenhum processo local permaneceu e só então parar o cluster/mover persistência; abortar o reset se a confirmação falhar. Testar em cópia descartável, inclusive servidor com handles abertos. A inspeção não reproduziu perda de dados e não executou reset por restrição do despacho.

### 3. Baixo — reexecução do Start não é idempotente e Smoke altera dados

Evidência: `scripts/Start.ps1:3` lança erro se já estiver rodando; `scripts/initialize.cjs:20-25,30-37` ajusta contas e renova sessões a cada início; `scripts/smoke.cjs:27-40` cria atores e faz login e `:47` destrói bots. O comportamento de recusa evita servidor duplicado, mas não é operação idempotente bem-sucedida. O Smoke não é observação sem alterações e não seria possível restaurar a persistência com segurança sem backup/reset, proibidos neste despacho.

**Correção sugerida:** documentar expressamente a semântica de reexecução ou fazer Start validar instância existente e retornar sucesso; alertar que Smoke cria estado de personagens e executá-lo apenas em ambiente descartável. As evidências anteriores de bots são históricas, não reprodução nesta revisão.

### 4. Baixo — chave privada de teste copiada dentro do código-fonte

Evidência: `source/unit/OpenSSLSignerTest.cpp:7-11` contém PEM embutido denominado `testKey`, usado em teste unitário; `scripts/provision.py:22-24` exclui arquivos por extensão, mas não conteúdo embutido. A chave é identificada como fixture de teste no código; não há evidência de que seja segredo de produção. Portanto a afirmação literal “nenhuma chave copiada” não é comprovada, embora a presença dessa fixture não demonstre vazamento de produção.

**Correção sugerida:** qualificar a documentação como exclusão de arquivos de chaves e credenciais conhecidas, registrar a fixture de teste, e completar varredura auditável de segredos antes de distribuir a cópia. Não reproduzir o valor da chave em relatórios.

## Inspeção de segurança e scripts

- `scripts/serve.cjs:7-19` limpa variáveis herdadas relevantes, verifica URLs de banco, voz e configurações locais, recusa `.env` no runtime e exige `master` vazio. `scripts/Initialize.ps1:4-8` cria cluster trust restrito a loopback. Configurações de cliente locais e template de banco apontam para loopback. No instante da reprodução, não observei conexão TCP estabelecida externa do processo; isso é um snapshot, não prova de ausência de todo tráfego futuro ou UDP de saída.
- Busca por nomes de `.env`, `.pem`, `.key`, `.pfx` e `.p12` no fonte fora de dependências/build não encontrou arquivos correspondentes. Busca por padrões comuns de chaves, webhook, token GitHub e URL de banco com senha detectou somente o teste PEM descrito e o parser; padrões não são uma auditoria exaustiva. `provision.py:22-25` exclui `server/world`, `.env*`, extensões de chaves e `*.local.json`; não garante detecção de segredo embutido ou outros dados pessoais em nomes não previstos. Tokens e segredo locais são gerados em `initialize.cjs:30-42` e ignorados pelo Git raiz (`.gitignore:6-7`). Não consultei nem reproduzo seus valores.
- `scripts/Common.ps1:11-22` valida limites de caminho e recusa reparse points nos alvos inspecionados; `Reset.ps1:4-14` move state, mundo, storage e JSONs a backup, preservando loadorder, sem exclusão recursiva. Não executei reset, build, prepare ou aplicação de overlay para não alterar o ambiente. A restauração depende de mover cada item ao caminho original, com cluster parado.
- `Apply-Overlay.ps1:3-19` impede alteração com servidor identificado ativo, verifica alvos de novos arquivos e executa `git apply --check` antes da aplicação; `:20-25` adiciona arquivos novos e intent-to-add. Verificação estática não garante atomicidade se uma cópia ou `git add` falhar depois da aplicação dos patches.
- `Prepare.ps1:6-25` copia para runtime e chama sincronizador com `--out` e `--settings` locais; `sync-client-plugins.js:121-128,140-181,203-207` lê perfis/MO2 e escreve nas saídas fornecidas. Não identifiquei escrita explícita no repositório oficial ou no MO2 nos scripts revisados; não executei launcher, build ou dependências transitivas para prová-lo em runtime. `Start-Client.ps1:14` inicia MO2, mas não foi executado nesta revisão.
- `Stop.ps1:2-5` identifica Node por executável e command line locais e usa `pg_ctl -D` do cluster local; não usa kill por nome genérico. Start recusa portas ocupadas e instância identificada já ativa. Execução repetida de stop/reset e falhas intermediárias não foram testadas.

## Reprodução e estado restaurado

Antes do teste o servidor e PostgreSQL já estavam ativos. Executei `scripts/Stop.ps1`: Node PID 18892 terminou, `pg_ctl` informou parada e não havia listeners nas quatro portas na verificação intermediária. Executei `scripts/Start.ps1`, que renovou sessões locais e iniciou Node PID 7088 e PostgreSQL PID 9896: UDP 7777 e TCP 3000, 7778, 5442 escutaram em `127.0.0.1`; GET `http://127.0.0.1:3000/manifest.json` retornou 200. `logs/server-20261115-141125.stdout.log` registra master ausente, AetheriusUI inicializada e boot com 16 módulos ativos e zero falhas; stderr contém aviso de depreciação `punycode`, sem fatal observado na coleta. O servidor e o PostgreSQL ficaram ativos como originalmente, mas com novos PIDs e sessões renovadas; parada/início gera logs e altera timestamps de sessão, portanto não restaura estado de bytes idêntico.

Não rodei bot: `Start -Smoke` requer nova reinicialização e cria/atualiza estado persistente, incompatível com a restrição de não alterar o ambiente além de início/parada. Evidência histórica `evidence/smoke-live-server.json` relata dois bots simultâneos e `passed=true`; não equivale à reprodução nesta revisão nem a handshake de cliente externo.

## Arbitragem do orquestrador (Opus 5.5, 2026-11-15)
1. Aceito: README em duas partes (uso da instalação pronta e bootstrap completo a partir do checkout, com artefatos, hashes e ordem exata).
2. Aceito: Stop espera o PID sair, com timeout; Reset aborta se a parada não for confirmada.
3. Aceito: Start retorna sucesso se a instância já está ativa e saudável; Smoke documentado como algo que altera dados (usar só em ambiente descartável ou após Reset).
4. Aceito como documentação: qualificar a afirmação e registrar a fixture de teste do OpenSSLSignerTest (sem reproduzir o valor).
Correção: Sol High. Confirmação: Terra High.

## Confirmação (Opus 5.5 Low, escalação temporária)

**Data:** 15/11/2026. **Revisor:** Opus 5.5 Low, instância independente (não implementou as correções), sob a escalação temporária do AGENTS.md (Codex indisponível). **Base:** `C:\Code\Aetherius - Servidor Local`, commit `c8eb72e` (pai `256e423`); seção "Correções pós-revisão" de `Sobrenatural/docs/AMBIENTE-SERVIDOR-LOCAL.md`. Revisão por leitura de código + execução apenas de `Start.ps1` (instância ativa) e `Verify.ps1`. Stop/Reset **não** executados.

### Status por apontamento

| # | Status | Evidência |
|---|---|---|
| 1 | **Resolvido** (estático) | `README.md:9,133-190` Parte 1/Parte 2; ordem provision → baseline → overlay `local-runtime` → Build → Prepare → Start/Verify. Coerente com `scripts/provision.py`: caminhos fixos `:6-7`, recusa sobrescrever `source` `:17`, exclusões `:22-24` (= README `:164`), saídas `evidence/*.json` `:34,59` e `tools/{native,postgresql,node,kit-tests}` `:44-48`. Bootstrap não executado de ponta a ponta (já declarado). |
| 2 | **Resolvido** | `scripts/Stop.ps1:4-10`: `Wait-Process -Timeout` com `ProcessCommandException` capturada (cobre timeout e PID já encerrado), em seguida `Get-Process` lança e deixa o PostgreSQL ligado; `:12-15` confere `pg_ctl status` após o stop. `Reset.ps1:3-9` re-verifica processo e cluster antes de mover. Stop só atinge Node cujo `ExecutablePath` = `tools\node\node.exe` **e** command line contém `"<raiz>\scripts\serve.cjs"` (`Common.ps1:28-33`) e o cluster `-D state\postgresql`. Reset só move (nunca apaga), destino em `backups\` validado por `Assert-LocalPath` (`Common.ps1:11-23`, recusa fora da raiz e reparse points) antes de cada `Move-Item` (`Reset.ps1:10-21`). |
| 3 | **Resolvido** | `Start.ps1:3-13`: >1 processo falha; instância com UDP 7777 e TCP 3000 em 127.0.0.1 do mesmo PID → sucesso; sem listeners → falha (não esconde); `-Smoke` com instância ativa → falha. README `:24,121` documenta. **Reproduzido:** `Start.ps1` → "Local server already running and healthy: PID 3724", exit ok. |
| 4 | **Resolvido** | `README.md:49` qualifica a exclusão e registra a fixture `source/unit/OpenSSLSignerTest.cpp` sem reproduzir o valor. |

`Verify.ps1` → "Verified listeners, HTTP manifest, UI, master-disabled and boot markers. Nonfatal error lines: 0; external established TCP connections: 0." Servidor permaneceu ligado (PID 3724).

### Novos achados

1. **Baixo** — `Start.ps1:5-11` (caminho idempotente) não verifica o PostgreSQL: Node saudável com cluster parado retorna sucesso. Sugestão: incluir `pg_ctl status` no teste de saúde ou deixar ao `Verify` (documentar).
2. **Baixo** — `Stop.ps1:4` usa `Stop-Process -ErrorAction Stop`: se o Node sair entre a enumeração e o kill, Stop lança e não para o PostgreSQL (falha segura, mas espúria; reexecutar resolve). Sugestão: tolerar "processo não encontrado".
3. **Baixo** — `Verify.ps1` reescreve `evidence/verification.json`, arquivo rastreado: após esta confirmação `git status` mostra ` M evidence/verification.json`. Não reverti (fora do escopo de edição). Sugestão: gravar evidência de execução em arquivo ignorado ou aceitar e commitar conscientemente.
4. **Informativo** — `Reset.ps1:6`: se `state\postgresql\PG_VERSION` não existir, não há checagem do cluster; nesse caso não há cluster a mover sob uso, então sem risco prático.

Nenhuma regressão encontrada: Stop não usa kill por nome genérico; timeout tratado; Reset nunca apaga e nunca opera fora da raiz; Start idempotente sem mascarar instância doente.

### Veredito

**Confirmado.** Os apontamentos 1–4 estão resolvidos conforme a arbitragem; o ambiente fica **aprovado para uso local**, com a Parte 2 do README aprovada como procedimento documentado ainda **não testado em cópia limpa**. Os achados novos são de severidade baixa e não bloqueiam.

## Arbitragem final (Opus 5.5)
- Itens 1–4 confirmados. Os 3 achados baixos da confirmação foram aceitos e enviados ao autor: Start idempotente também confere o PostgreSQL; Stop-Process tolera o PID que sai durante a corrida; Verify grava a evidência em arquivo não rastreado (ou em .gitignore).
- Ambiente aceito para testes (homologação parcial já aceita pelo dev).
