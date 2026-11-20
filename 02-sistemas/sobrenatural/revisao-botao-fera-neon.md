# Revisão Independente — Botão "Forma da Besta" (TAB Sobrenatural / Lobisomem)

- **Revisor:** Gemini 4 Neon (revisão cruzada honesta, somente leitura no código — regra de `AGENTS.md`, 18/11/2026)
- **Data:** 19/11/2026
- **Autor revisado:** Sol High
- **Alvo revisado:**
  - `C:\Code\Aetherius - Servidor Local` — commit `19a6b0fad3a23caae5317b67ad457b1b32e848b6` (`19a6b0f`): `feat(sobrenatural): valida e persiste Forma da Besta do TAB`
  - `C:\Code\Aetherius - Servidor Local\client-source` — commit `5c1bb42d3d630ec904ccc55943e0e7decaf67a8d` (`5c1bb42`): `feat(sobrenatural): destaca Forma da Besta na tela do lobisomem`
  - `C:\Code\Aetherius - Servidor Local` — commit `a7abb032da18a57431b113c4c59ff659cee0d86` (`a7abb03`): `docs(sobrenatural): registra Forma da Besta e deploy local parado`
- **Fontes consultadas:**
  - [ETAPA-2_transformacao-forcada.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ETAPA-2_transformacao-forcada.md)
  - [DECISOES.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/DECISOES.md)
  - Código-fonte do servidor, cliente, patches, testes, runtime preparado e capturas de prévia no laboratório e em `Sobrenatural/ui-core-module/`.

---

## Veredito

**APROVADO COM RESSALVAS MENORES (sem bloqueadores para o teste em jogo).**

A implementação cumpre o pedido do dev no escopo de código: o botão fica no topo da primeira tela do Lobisomem, aciona o mesmo pré-aviso privado e a mesma animação sincronizada da transformação forçada (F7), não zera nem perturba o contador de 72 h (inclusive em queda/reinício com mutação ambígua), mantém a forma de fera por 10 min reais a partir da troca de raça, persiste a recarga autoritativa de 2 h em disco antes dos efeitos, preserva as mãos limpas e a velocidade +50% já ligadas à raça `0xCDD84`, não expõe keywords e não introduz regressão na forçada de 72 h nem impacto relevante de performance.

Foram identificados **dois achados reais**, nenhum deles bloqueador do teste solo:

1. **[Médio — pré-existente em `werewolf-service.js`, não introduzido pelo lote]** Corrida na desconexão entre `phase0-basic.js:717` (`commands.removeActiveCharacter`) e `werewolf-service.js:344,364` (`cancelStale` antes de `online(id)`): quando a desconexão limpa `activeCharacters` antes do tick de 200 ms do serviço de lobisomem (~10% das desconexões), `cancelStale` trata a desconexão como troca de personagem, pula `finish(id)`, apaga `aetheriusBeastOriginal` e deixa `raceId = 0xCDD84` no ator offline; no relog, `whitelist.js:359` grava `race = 'WerewolfBeast'` no PostgreSQL antes de `recover()` restaurar a aparência mortal pelo journal.
2. **[Baixo — introduzido em `5c1bb42`]** `supernatural-module.js:118-128,218-219`: `beastMessage` não é limpo ao clicar em **ATUALIZAR** nem quando um `snapshot` posterior confirma `beastForm` novamente disponível/ativo, podendo manter uma mensagem de recusa antiga ao lado do status `"Disponível"`.

---

## 1. Achados Reais (Com Evidência)

### Achado 1 (Médio — pré-existente) — Corrida de desconexão em `werewolf-service.js:344,364` pode pular restauração imediata e gravar `WerewolfBeast` no banco no relog

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\werewolf-service.js:336-349,364-371` (idêntico em `overlays/sobrenatural/files/` e `runtime/server/gamemode/`)
  - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\phase0-basic.js:621,700-723`
  - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\commands.js:15-20,71-78`
  - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\whitelist.js:167-176,207-235,347,359`
- **Evidência / raciocínio verificável:**
  - O loop de desconexão em `phase0-basic.js:621` roda a cada 2000 ms e, ao detectar `!mp.isConnected(userId)`, chama `commands.removeActiveCharacter(actorId)` (`phase0-basic.js:717`), que executa `activeCharacters.delete(actorId)` (`commands.js:77`). A partir desse instante, `globalThis.aetheriusCharacterDirectory.get(actorId)` retorna `null` (`commands.js:17`).
  - O tick de `werewolf-service.js:379` roda em paralelo a cada 200 ms. Na linha 364, **antes** de verificar `online(id)` (linha 365), o serviço executa:
    ```js
    if (session.phase !== 'leaving' && !sessionCurrent(id, session)) { cancelStale(id, session); continue; }
    ```
  - Se `phase0-basic.js` disparar na janela de 0–200 ms anterior ao tick do `werewolf-service`, `sessionCurrent(id, session)` retorna `false` (tanto em `:337` via `session.button.isCurrent()` quanto no cheque pré-existente `:340` `aetheriusCharacterDirectory?.get(id)?.characterId === session.characterId`).
  - `cancelStale(id, session)` (`:344-347`) então executa:
    ```js
    if (session.characterId && globalThis.aetheriusCharacterDirectory?.get(id)?.characterId !== session.characterId) {
      settleCancelledIntent(session);
      mp.set(id, STATE, null); mp.set(id, ORIGINAL, null); sessions.delete(id); saveRegistry(); return;
    }
    ```
  - Como `aetheriusCharacterDirectory.get(id)` é `null` (`undefined !== session.characterId`), `cancelStale` confunde desconexão comum com substituição por outro personagem: **não chama `finish(id)`**, deixa `appearance.raceId === 0xCDD84` (`WerewolfBeastRace`) no ator offline, zera `aetheriusBeastOriginal = null` e remove o ator de `aetherius-werewolf.json`.
  - No próximo login do mesmo personagem, `whitelist.js:347` registra o personagem e `whitelist.js:359` chama imediatamente `_watchAppearance` → `_applyAppearance` → `_syncRaceGender(character.id, appearance)` (`whitelist.js:167-176,232`), gravando `race = 'WerewolfBeast'` na tabela `characters` do PostgreSQL antes que o próximo tick de 200 ms do `werewolf-service` execute `recover(id)` e restaure a aparência mortal pelo `transformJournal.recovery()` (`werewolf-service.js:285-294`). Adicionalmente, se a concessão/maldição tiver sido alterada antes do relog (ou em configuração sem arquivo de journal), a aparência mortal não seria recuperada porque `aetheriusBeastOriginal` já foi apagado.
- **Atribuição e severidade:** Defeito **pré-existente** em `werewolf-service.js` (não introduzido por `19a6b0f`, que apenas acrescentou `:337` no mesmo caminho), mas pertinente ao requisito de encerramento na desconexão para a forma de 10 min (e também para Z/forçada). Severidade **Médio** porque a aparência em jogo ainda se autorrecupera via `transformJournal` no relog com a mesma concessão, mas vaza `WerewolfBeast` para `characters.race` no banco até um login posterior.
- **Correção sugerida (em lote próprio ou ajuste cirúrgico):** Em `cancelStale`, só pular `finish(id)` quando houver **outro personagem ativo** no ator (`const active = globalThis.aetheriusCharacterDirectory?.get(id)?.characterId; if (session.characterId && active && active !== session.characterId) ...`) — ou quando o ator já não tiver `appearance.raceId === BEAST_RACE` (ex.: PD que zerou `appearance` para `null` em `skymp-host.cjs:147`).

---

### Achado 2 (Baixo) — Mensagem de erro do botão (`beastMessage`) permanece visível após recuperação/atualização

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\supernatural-module.js:118-128,218-219` (também presente no espelho `Sobrenatural/ui-core-module/modules/supernatural/supernatural-module.js` e no mod MO2 `AETHERIUS - UI CORE (LOCAL)`)
- **Evidência / raciocínio verificável:**
  - Quando `beast.start` falha (por exemplo, recusa `BEAST_BLOCKED` enquanto o jogador está com trava de inventário/comércio), `supernatural-module.js:235` define `beastMessage = result.error?.message || ...`.
  - A variável `beastMessage` só é limpa na troca de `grantId`/maldição (`:110`) ou no início de um novo `beast.start` (`:219`).
  - Se o jogador desfaz o bloqueio e clica em **ATUALIZAR** (`request('snapshot')`, com `silent: false`), `:218` limpa apenas `message = ''`, mantendo `beastMessage`. O mesmo ocorre no polling de 10 s: `receiveBeast` (`:118-128`) atualiza `beastView` para `{ status: 'available', remainingMs: 0, canStart: true }`, habilita o botão e exibe `"Disponível"`, mas `[data-beast-message]` continua exibindo a recusa anterior (`"A Forma da Besta está indisponível neste momento."`) ao lado de `"Disponível"`.
- **Severidade:** **Baixo** (UX/clareza apenas; não afeta autorização, estado nem cliques).
- **Correção sugerida:** Limpar `beastMessage = ''` em `!silent` (clique em **ATUALIZAR**) e/ou dentro de `receiveBeast` quando `!activatingBeast` e `view?.status === 'active' || (view?.status === 'available' && view?.canStart === true)`.

---

## 2. Auditoria por Eixo Requisitado

| Eixo | Evidência verificada | Resultado |
|---|---|---|
| **1. Validação no servidor (sem confiar no cliente; sem expor keyword)** | `aetheriusUiSystem.ts:153,320-355` autentica `sessionId`, limita taxa (30/10 s) e payload (18 KiB), deduplica `messageId` (`router.ts:81-88`) e resolve `actorId = ctx.svr.getUserActor(userId)` no servidor. `supernatural-service.js:213-229` resolve `characterId`, valida `checkCurrent` e aceita do cliente apenas `{ grantId }`. `forced-werewolf.js:139-155` revalida `disposed`, `binding` (personagem ativo, `!creationPending`, `curse === 'werewolf'`, `grantId`), `online`, `healthy` (vivo, `skyDeath.phase === 'ALIVE'`, raça não Fera/Lorde, sem sessões de Fera/Lorde, sem travas SkySocial/profissão/comércio), `!sequences.has(id)`, `werewolf.canStart(id, { button: true })` (keyword `JogadorLobisomem`, magia conhecida `0x92c48`, sem `pending`, sem intenção pendente no journal) e `cooldownRemaining`. Nem `state` (`supernatural-service.js:68-77`), nem `beastForm` (`forced-werewolf.js:121-138`), nem `aetheriusBeastCompulsion` (`:99-100,198`, `isVisibleByNeighbors: false`) expõem `JogadorLobisomem` ou `onlineMs`. | **Aprovado** |
| **2. Persistência da recarga (relog, reinício, relógio)** | `forced-werewolf.js:9,147-153` grava `characters[characterId].buttonUntil = wall() + 7_200_000` (UTC ms) com `flush(true, true)` (`fs.writeFileSync(..., { flush: true })` — suportado nativamente no Node v22.14.0 — seguido de `fs.renameSync`) **antes** de chamar `beginSequence`. Falha de disco desfaz `row.buttonUntil` e retorna `PERSISTENCE` sem efeitos nem consumo. Boot (`:47-50`) aceita registros legados sem `buttonUntil` e rejeita JSON corrompido ou `buttonUntil` inválido. Relog e reinício preservam o prazo por `characterId` + `grantId`; nova concessão (`grantId` distinto) inicia sem recarga herdada (`:81,119`). Uso de `Date.now()` permite o tempo offline correr na recarga de 2 h, com a limitação inerente de relógio de parede caso o relógio do SO seja ajustado manualmente. | **Aprovado** |
| **3. Encerramento em 10 min (morte, desconexão, F7, forçada concorrente)** | `forced-werewolf.js:10,187` passa `{ durationMs: 600_000, isCurrent }`. `werewolf-service.js:223,241-245` marca `session.until = now() + 600_000` exatamente na primeira troca de raça (`raceApplied`/`raceUncertain`), sem estender em retries posteriores (`formStarted`), e encerra `turning` preso após `session.until` (`:367`). Expiração normal (`:371`) chama `finish(id, { animate: true })` → devolve aparência original, publica `leaving` (3 s) e limpa `STATE`/`ORIGINAL`. **Morte/caído:** antes de t=20 s, `forced-werewolf.js:177` cancela aviso; em `turning`/`beast`, `werewolf-service.js:367,370` chama `finish(id, { animate: false })`; recarga de 2 h permanece gasta. **F7 `endForm`:** `supernatural-admin.cjs:49-54` chama `beast.finish(actorId, { animate: true })` em `turning`/`beast`, preservando a recarga. **Forçada concorrente / F7 `forceTransform`:** bloqueada durante aviso (`sequences.has(id)` em `:178,208`) e durante `turning`/`beast`/`leaving` (`!healthy(id)` em `:75,177,206`); após volta a humano à noite, a compulsão dispara normalmente mesmo durante as 2 h de recarga. **Desconexão:** durante aviso (`<20 s`), `:159-160` cancela e limpa; durante `beast`, ver **Achado 1** (corrida pré-existente em `werewolf-service.js:344,364`). | **Aprovado com ressalva** (Achado 1 pré-existente) |
| **4. Corridas e duplo clique** | No cliente (`supernatural-module.js:213,217,221,254`), `busy` bloqueia requisições concorrentes de forma síncrona antes do `await send(...)`. No servidor, `supernatural-service.js:218-229` e `forced-werewolf.js:139-155` são inteiramente síncronos: a primeira chamada persiste `buttonUntil` e insere `id` em `sequences` antes de ceder o event loop; qualquer segundo pacote imediato (inclusive forjado com novo `messageId`) cai em `sequences.has(id)` → `BEAST_BLOCKED` ou `BEAST_COOLDOWN` sem duplicar efeitos nem estender a recarga. Remoção/troca de maldição concorrente cancela sequência (`:167,170`) e sessão (`werewolf-service.js:337-340,364`). | **Aprovado** |
| **5. Estado de apresentação (UI TAB, fechamento, isolamento CSS)** | `supernatural-module.js:196-197` renderiza `.sn-beast` logo após `.sn-breadcrumb` somente quando `curse === 'werewolf'` (confirmado visualmente nas 3 capturas em `preview/screens/forma-besta-*.png`). Botão inicia `disabled` e só habilita com confirmação do servidor (`status === 'available' && canStart === true`); timer local (`performance.now()`, tick 1 s) interpola a contagem sem autorizar uso ao zerar (`00:00`). Polling de 10 s atualiza somente os nós do botão (`updateBeast`) sem recriar DOM nem perder scroll. No aceite (`ok === true && beastView?.status === 'active'`), libera o foco do TAB (`context.close` em testes ou `cleanup() + ui.nativeFocusChanged(false) + window.aetheriusUiSetFocus('false')` no `shell.js` real). `cleanup()` limpa `beastTimer`. CSS (`supernatural-module.css:26-60`) está 100% escopado sob `.aetherius-supernatural .sn-beast`, respeitando a regra de isolamento do UI Core (18/11). Ver **Achado 2** (`beastMessage`). | **Aprovado com ressalva menor** (Achado 2) |
| **6. Regressão da forçada de 72 h e do Z** | `forced-werewolf.js:88-96,177-192,203-214`: todas as regras da forçada de 72 h (contagem `hrtime`, janela `[20h, 5h)`, falha fechada sem `EnvironmentAuthority`, linha do tempo 0/5/5,25/10/15/20 s, `retry` 30 s, cancelamento em amanhecer/morte/queda/troca/trava, F7) permanecem preservadas. `werewolf-transform-journal.js:38-54` grava `buttonResetStamp` no modo botão para atualizar `original` (recuperação) sem perder nem avançar o último stamp elegível da forçada, inclusive em crash antes de `confirm()`. `werewolf-service.js:168,173,192,226-238` isola `aetheriusBeastCooldownUntil` (recarga do Z) e recusa Z durante o aviso do botão. | **Aprovado** |
| **7. Mãos limpas e +50% velocidade nesta forma** | A transformação do botão usa o mesmo `werewolf-service.start` → `setAppearance` com `raceId: 0xCDD84` (`WerewolfBeastRace`) e `aetheriusBeast.phase === 'beast'`. Portanto, dispara integralmente: (a) limpeza e bloqueio de mãos em C++ (`AppearanceBinding.cpp:19-33`, `ActionListener.cpp:412-415`, addon `23669DE2…`); (b) limpeza local no dono (`werewolf/client.js:114-143`); (c) `SN_Lobisomem_Estagio1..6` (`werewolf-grants.js:36`, MGEF `000844` em `AETHERIUS - SOBRENATURAL.esp` `C8B13207…`, condicionado a `WerewolfBeastRace`). Validação efetiva no motor continua pendente do próximo teste em jogo autorizado. | **Aprovado no escopo de código** (jogo pendente) |
| **8. Performance** | Sem novo I/O em loops periódicos: `flush(true, true)` ocorre somente em `startButton` (no máximo 1× a cada 2 h por personagem). `buttonView` e `canStart` fazem apenas leituras em memória O(1) (+ `online().includes(id)` sobre ≤128 jogadores, apenas no polling de 10 s com TAB aberto ou clique). No cliente, `beastTimer` (1 s) roda apenas com o módulo montado, é limpo em `cleanup()` e evita escrita no DOM quando `textContent` não muda. | **Aprovado** |

---

## 3. Verificações Independentes Executadas Nesta Revisão

- **Consistência de espelhos e runtime preparado (SHA-256):**
  - `forced-werewolf.js` (`F590D7EB…`), `werewolf-service.js` (`2C12BCA5…`), `supernatural-service.js` (`02556FBF…`) e `werewolf-transform-journal.js` (`0C13C139…`): **byte-idênticos** entre `source/server/gamemode/`, `overlays/sobrenatural/files/server/gamemode/` e `runtime/server/gamemode/`.
  - `supernatural-module.js` (`0E3A41BC…`) e `supernatural-module.css` (`E1F2B3BA…`): **byte-idênticos** entre `client-source/client/ui-core/modules/supernatural/` e o mod instalado no MO2 `AETHERIUS - UI CORE (LOCAL)`.
  - `runtime/server/dist_back/skymp5-server.js`: confirmado que o bundle compilado do servidor registra `"beast.start"` em `"supernatural"`.
- **Integridade dos patches:**
  - `git -C source apply --reverse --check overlays/sobrenatural/028-forma-besta-ui.patch` → **OK (exit 0)**.
  - `git -C client-source apply --reverse --check overlays/cliente-local/032-forma-besta-ui.patch` → **OK (exit 0)**.
- **Testes automatizados reexecutados:**
  - `node --test overlays/sobrenatural/teste-transformacao-forcada.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs` → **70/70 passaram**.
  - `node overlays/sobrenatural/teste-servico.cjs` → **OK (exit 0)**.
  - `node Sobrenatural/ui-core-module/preview/test-ordering.cjs` → **12/12 PASS (exit 0)**.

---

## 4. Observações Opcionais (Não Bloqueantes) e Decisões Pendentes do Dev

1. **Push `changed` sem `beastForm` (`aetheriusUiSystem.ts:171`, `supernatural-module.js:294-298`):** Se a staff conceder Lobisomem enquanto o jogador já estiver com o TAB Sobrenatural aberto, o evento push atualiza `state` mas não carrega `beastForm`; o botão exibe `"Indisponível agora"` até o próximo polling (≤10 s) ou clique em **ATUALIZAR**.
2. **Defesa adicional em `forced-werewolf.js:126`:** `wolf.turnMs` assume `mp.__aetheriusWerewolf` ainda montado enquanto `sequence?.button` existir; usar `(wolf?.turnMs || 0)` evitaria TypeError num cenário atípico de descarte parcial de serviços.
3. **Alinhamento de espelhos pré-existentes (para consolidação):**
   - `Sobrenatural/ui-core-module/modules/supernatural/supernatural-module.js` (`1A7A4E9E…`) e `client-source/.../supernatural-module.js` (`0E3A41BC…`) possuem o mesmo código da Forma da Besta (`5c1bb42`), diferindo apenas no trecho anterior do fechamento do Ritual da Bruxa (`context.close()` vs `closeShell()`).
   - `overlays/sobrenatural/docs/DECISOES.md` omite duas seções anteriores presentes em `Sobrenatural/docs/DECISOES.md` (`18/11 — Sem teste com 2 jogadores` e `19/11 — Recarga do Morcego`); a seção `5.3 Forma da Besta no TAB` está idêntica nos dois.
4. **Suposições registradas em `DECISOES.md §5.3` pendentes de confirmação explícita do dev:**
   - Botão utilizável a qualquer hora (sem exigir janela noturna 20h–5h).
   - Recargas independentes entre o botão (2 h) e o poder Z legado (`aetheriusBeastCooldownUntil`), mantendo o Z com suas regras legadas (duração/reset de 72 h) e recusado durante o aviso de 20 s do botão.
   - Compulsão automática de 72 h aguardando retorno a humano caso vença durante a forma do botão, podendo disparar durante as 2 h de recarga do botão.
   - Validação presencial no próximo teste em jogo autorizado (fechamento do TAB no CEF, pré-aviso, transição de 10,5 s, expiração em 10 min, mãos limpas e velocidade +50%).

## 5. Resposta do autor — 19/11/2026

Sol High confirmou e aceitou os dois achados. A correção está no laboratório `036678d` (base `a7abb03`) e no cliente local `5e8b491` (base `5c1bb42`). A aprovação do Neon acima se refere ao candidato original. A reconfirmação independente deste delta e o reteste em jogo seguem pendentes.

### Desconexão e persistência da aparência

Com a remoção simulada da entrada em `activeCharacters` antes do tick, os testes no código original deixaram a raça `0xCDD84` no ator offline tanto na entrada legada quanto na entrada pelo botão. Em outra simulação, `_watchAppearance` disparou uma atualização de raça enquanto o ator ainda estava em fera. Esses testes reproduzem a ordem do defeito. A frequência da corrida em jogo continua sem medição.

Em `C:\Code\Aetherius - Servidor Local\source\server\gamemode\werewolf-service.js:342`, o serviço distingue o personagem substituto confirmado, a aparência já limpa pelo PD e a ausência de vínculo de um ator ainda em fera. Para a fera offline chama `finish`, que verifica a restauração antes da limpeza. Se o ator estiver online à espera do vínculo, o serviço deixa a sessão pendente até identificar o personagem ou observar a saída. A sessão é descartada sem reaplicar o rosto anterior quando há um substituto confirmado ou quando a aparência já foi limpa.

Em `C:\Code\Aetherius - Servidor Local\source\server\gamemode\whitelist.js:216`, a raça transitória da fera adia a sincronização. O observador existente volta a consultar a aparência e só grava raça e gênero após a restauração. O teste executou o observador real com o banco simulado e verificou zero chamadas antes da restauração e uma com a raça mortal depois. Fichas que já tenham sido gravadas com a raça transitória serão corrigidas quando voltarem a sincronizar uma aparência mortal. A mudança da whitelist é entregue pelo patch `overlays/sobrenatural/029-forma-besta-relog.patch`.

### Mensagem de recusa da interface

O teste de interação primeiro reproduziu `Bloqueada` após **ATUALIZAR**. Na correção, a recusa é removida imediatamente ao atualizar manualmente e ao receber um snapshot válido que confirme o botão disponível ou a forma ativa. A recusa da tentativa atual ainda aparece até o próximo comando ou até um snapshot de recuperação. O patch `overlays/cliente-local/033-forma-besta-feedback.patch` corresponde ao JS instalado no UI CORE (LOCAL). A mesma alteração foi aplicada à fonte em `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module`.

### Validação e deploy LOCAL

- 74/74 testes direcionados. 517/517 em cada modo de `AETHERIUS_VAMPIRE_FEED_ENABLED` (`false` e `true`). Também passaram serviço, F7, revisão-F7, 12 cenários de interação da UI, TypeScript, webpack e a verificação reversa dos patches. O teste de interação cobre tanto **ATUALIZAR** quanto a recuperação por polling. As alterações são em JS. O addon compilado do lote anterior foi reaproveitado.
- `Stop.ps1` → `Build.ps1 -SkipInstall` → `Prepare.ps1 -PluginClient <cópia do perfil LOCAL>`. O Prepare verificou os 439 plugins do perfil atual. A instalação gravou o bundle em `AETHERIUS - SERVIDOR LOCAL` e JS/CSS em `AETHERIUS - UI CORE (LOCAL)`. Servidor, PostgreSQL e Skyrim ficaram parados.
- O backup `C:\Code\Aetherius - Servidor Local\backups\forma-besta-neon-20261119-212144` contém os arquivos anteriores, a cópia do perfil, o log do Prepare e `sha256.txt` com os caminhos absolutos e hashes. A tabela identifica o deploy deste delta.

| Arquivo instalado | SHA-256 |
|---|---|
| JS do módulo no MO2 | `B517D7001D181302C6B5B4D73E24D98D6754754D7BAB1D1470723D1F74543D85` |
| CSS do módulo no MO2 | `E1F2B3BA44F5EB0065C14960761075130DFAB4E12F3EBCDFB29C3F7AEA42EBF8` |
| Bundle do cliente no MO2 | `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709` |
| Bundle do servidor | `6BD128FC275E173DD01C8E7EBA99783F64AB7CE97C17F95BE5067368DCD10E5C` |
| `werewolf-service.js` no runtime | `9C28426D1348350ED19392464951A535840DF3C9C8B5ED113E99077CB43B5BA0` |
| `whitelist.js` no runtime | `DFDBC81247BBFA1A96A5A4E83EC9F3BF689FCC5937B9A954DD74CA77CDAF75F6` |
| Addon no runtime | `23669DE235A7A3B480B013326B1638A013C5DAC6D265FA86D51131D7C77F9F2B` |
| ESP no MO2 e no runtime | `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A` |
| Manifesto de plugins no runtime | `8CB1C31682C60B77F3A1ED9641A7F1F67463A4A1D7234AEA900F5B9A5757C7B0` |

---

## 6. Reconfirmação Independente — 19/11/2026 (Gemini 4 Neon)

- **Alvo reconfirmado:** laboratório `036678d205cd1790a2be528640979b7653fb0dd3` (`036678d`) + `147e84f6b88cb95ce36106e267235fce451489f0` (`147e84f`) e `client-source` `5e8b491c7288578eb7b91437aa507a14a3a0103e` (`5e8b491`), somente leitura no código.
- **Veredito final:** **APROVADO SEM ACHADOS PENDENTES (escopo de código).** Os dois achados da revisão inicial estão resolvidos sem regressão; pronto para validação em jogo no próximo Start autorizado.

### Reconfirmação por ponto

1. **Achado 1 — Desconexão, relog e `recover` pelo journal (`werewolf-service.js:342-357`, `whitelist.js:34,216`):** **Resolvido sem regressão.**
   - **Desconexão com `removeActiveCharacter` antes do tick (legado e botão):** com `active == null`, `replaced === false`, `appearance?.raceId === BEAST_RACE` e `!online(id)`, `cancelStale` agora chama `finish(id)` (`:350`), restaurando a aparência mortal verificada antes de limpar `STATE`/`ORIGINAL`/sessão e preservando a recarga de 2 h do botão. Em `turning` antes da troca de raça (`raceId !== BEAST_RACE`), limpa a sessão sem sobrescrever a aparência mortal.
   - **Proteção contra substituição e PD:** personagem substituto confirmado (`active != null && active !== session.characterId`) e ator limpo por PD (`appearance === null`) seguem caindo no descarte sem reaplicar o rosto antigo (`:344,355-356`).
   - **Relog rápido e recuperação pós-crash pelo journal:** se o ator estiver online aguardando o vínculo (`online(id) === true`, `active == null`, `raceId === BEAST_RACE`), `:350-351` preserva a sessão/recuperação até o vínculo chegar ou o ator cair. Na recuperação pós-crash pelo journal (`recover` em `:280-303`), `whitelist.js:216` adia `_syncRaceGender` e a imposição de nome enquanto `appearance.raceId === BEAST_RACE`, mantendo `_hasAppearance` verdadeiro (`:180,253,346` → `creationPending: false`, `acceptTyped: false`) e gravando raça/gênero mortais assim que `recover()` restaura a aparência. `require('./werewolf-service')` em `whitelist.js:34` importa apenas constantes sem efeito colateral nem dependência circular.
2. **Achado 2 — Limpeza de `beastMessage` na UI (`supernatural-module.js:128,219-223`):** **Resolvido sem regressão.**
   - **ATUALIZAR (`!silent`):** limpa `message` e `beastMessage` e atualiza o painel imediatamente (`:219-223`).
   - **Polling silencioso / `snapshot`:** `receiveBeast` limpa `beastMessage` assim que o servidor confirma `status === 'active'` ou `status === 'available' && canStart === true` (`:128`), enquanto uma recusa da tentativa atual de `beast.start` continua sendo gravada depois (`:239`) e exibida até nova ação ou recuperação. Em maldições sem botão, `updateBeast()` retorna imediatamente.
3. **Suposições de `DECISOES.md §5.3` confirmadas pelo dev:**
   - Botão utilizável a qualquer hora, compulsão de 72 h podendo disparar durante a recarga de 2 h do botão e Z mantido com regras/recarga independentes conferem com o código revisado (`forced-werewolf.js`, `werewolf-service.js`, `werewolf-transform-journal.js`).

### Evidências reexecutadas nesta reconfirmação

- **Hashes conferidos:** `werewolf-service.js` (`9C28426D…`) idêntico em `source/`, `overlays/sobrenatural/files/` e `runtime/`; `whitelist.js` (`DFDBC812…`) idêntico em `source/` e `runtime/`; `supernatural-module.js` (`B517D700…`) idêntico em `client-source/` e MO2 `AETHERIUS - UI CORE (LOCAL)` (com o mesmo fix aplicado ao espelho `Sobrenatural/ui-core-module/`).
- **Patches (`--reverse --check`):** `029-forma-besta-relog.patch` → OK (`0`); `033-forma-besta-feedback.patch` → OK (`0`).
- **Testes reexecutados:** `teste-transformacao-forcada.cjs` + `werewolf.test.cjs` (**74/74**), `teste-servico.cjs` (**OK**) e `test-ordering.cjs` (**12/12 PASS**).
- **Pendente fora do escopo de código:** validação em jogo no próximo Start autorizado (CEF, pré-aviso, animação, 10 min, mãos limpas, +50% velocidade e relog).

