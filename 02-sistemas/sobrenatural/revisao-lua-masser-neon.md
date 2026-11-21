# Revisão Cruzada — Lua de Masser (Servidor) e HUD da Lua (Cliente)

- **Data:** 21/11/2026
- **Revisor:** Gemini 4 Neon
- **Veredito:** **APROVADO COM RESSALVAS** (0 achados P1, 0 achados P2, **1 achado P3** — regressão de fixture CEF no overlay + guarda defensiva em `supernatural-hud.js`; pronto para teste local em jogo após atualizar o fixture e, opcionalmente, proteger a chamada em `supernatural-hud.js`).

## Escopo e artefatos auditados

| Alvo | Repositório / Caminho | Referência |
|---|---|---|
| Servidor (Sol High) | `C:\Code\Aetherius - Servidor Local\source` | Commits `48fb5fe`, `7dab4e2`, `a3ef672` sobre `9e313e4` (base `310cb59`) |
| Espelho do overlay (servidor) | `C:\Code\Aetherius - Servidor Local` | Commit `8b233e3`, patch `overlays\sobrenatural\035-masser-clock.patch` e 7 arquivos espelhados em `overlays\sobrenatural\files\server\gamemode\` |
| Cliente (Terra High) | `C:\Code\Aetherius - Servidor Local\client-source` | Commit `7e175bb` sobre `ae7c172`, patch `overlays\cliente-local\039-hud-lua-masser.patch` |
| Especificações e relatórios | `C:\Code\Aetherius - New Systems\Sobrenatural\docs\` | `investigacoes\INVESTIGACAO_21-11_fases-da-lua.md` (§7–9), `specs\SPEC_21-11_sol_lua-de-masser.md`, `specs\SPEC_21-11_terra_hud-lua.md`, `ETAPA-2_lua-de-masser.md`, `ETAPA-2_hud-lua.md`, `investigacoes\INVESTIGACAO_21-11_fixture-hud-lua.md` |
| Design aprovado | `C:\Code\Aetherius - New Systems\Sobrenatural\preview\hud-lua-propostas.html` | Seção *"A escolhida — versão integrada"* (`hudFinal`, `litPath`, `.hud-final`) |
| Modpack (consulta houseCARL) | `C:\Code\Aetherius - New Systems\Sobrenatural\docs\coleta\DADOS-HOUSECARL_21-11_hdpt-bronze.md` | Coleta independente via Luna MAX / houseCARL dos 33 records `HDPT` Bronze de `Miggyluv's Eyes.esp` |

---

## Achados

### 1. [P3] Suíte do overlay quebrada (451/453) em `teste-ritual-hud-nativo.cjs` e chamada sem guarda em `supernatural-hud.js:38`

- **Classificação sobre a pergunta da revisão:** trata-se **primariamente de fixture desatualizado** no teste legado do overlay (que não foi atualizado junto ao lote do cliente), combinado a um ponto menor de **resiliência defensiva** no projetor da HUD. **Não ocorre em jogo com o build completo**, pois `client\ui-core\frontend\hud.html:24` carrega `./modules/supernatural/moon-hud.js` imediatamente antes de `./modules/supernatural/supernatural-hud.js` (`:25`).
- **Evidência:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\supernatural-hud.js:38`:
    ```js
    window.setMoonHud(validMoon ? moon : null);
    window.setVeilBondHud(validMoon ? null : state?.veil ?? null);
    window.setBloodReserveHud(validMoon ? null : state?.blood ?? null);
    ```
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\teste-ritual-hud-nativo.cjs:115-120` e `:146` fornecem apenas `setVeilBondHud`, `setBloodReserveHud` e `setVeilTerrorBlackout` no `window` simulado, e a linha `:111` ainda verifica apenas os três scripts antigos (`veil-hud.js`, `blood-reserve-hud.js`, `supernatural-hud.js`).
  - Ao executar `project()`, `window.setMoonHud(...)` lança `TypeError: window.setMoonHud is not a function` na linha 38; o `catch {}` da linha 66 engole silenciosamente a exceção e aborta o restante do frame (impedindo projeção de Selo, Sangue, Ritual e Blackout). Note que `veil-hud.js:20` já adota chamada defensiva (`window.setBloodReserveHud?.(null)`).
- **Reprodução (confirmada nesta revisão):**
  ```powershell
  Set-Location "C:\Code\Aetherius - Servidor Local"
  node --test overlays/sobrenatural/teste-ritual-hud-nativo.cjs
  ```
  Resultado: `5 pass, 2 fail` — falhas exatas nas linhas `:106` (`AssertionError`: `calls` vazio `[]`) e `:141` (`AssertionError`: `assert.ok(overlay)` falso).
- **Correção recomendada:**
  1. Atualizar `overlays\sobrenatural\teste-ritual-hud-nativo.cjs` (incluir `'moon-hud.js'` na checagem de `hud.html:111`, mockar `setMoonHud` nos dois fixtures e validar também o despacho de `moon`).
  2. Em `client\ui-core\modules\supernatural\supernatural-hud.js:38-40`, considerar optional chaining (`window.setMoonHud?.(...)`, `window.setVeilBondHud?.(...)`, `window.setBloodReserveHud?.(...)`) para que uma ausência isolada de sub-script nunca derrube silenciosamente as demais HUDs sobrenaturais.

---

## Verificação detalhada por critério

### 1. Fórmula de Masser, época e janela lunar (Servidor) — OK
- **Ciclo e fórmula (`source\server\gamemode\supernatural\masser.js:8-12`):** `DAY_MINUTES = 1440`, `CYCLE_DAYS = 24`, `PHASE_DAYS = 3`, `day = Math.floor((gameMinutes - epochMinutes) / 1440)`, `phase = Math.floor(((day % 24 + 24) % 24) / 3)`. Trata corretamente valores negativos, `NaN`/não-finitos (retorna `null`) e mantém a ordem exata do `po3_MoonMod.ini` do Rudy ENB (`0 = Lua Cheia` nos dias `0..2`, até `7 = Crescente Gibosa` nos dias `21..23`).
- **Mesma época de `clockSnapshot()` (`environmentAuthority.ts:241-248`, `environmentSystem.ts:83`):** `clockEpochMinutes()` retorna `initialClock(0, this.options.clockConfig).anchorGameMinutes`, reutilizado por `clockSnapshot()`. Com `allowDateChanges = false`, `gameDaysPassed` permanece `null` no snapshot e `masser.js:27-38` segue calculando a fase a partir de `gameMinutes` e `clockEpochMinutes()`. Autoridade indisponível ou exceção → `null` (nada dispara).
- **Chave da noite e 3 noites (`masser.js:18-25`):** calcula `nightBeginning = (Math.floor(gameMinutes / 1440) - Number(afterMidnight)) * 1440 + nightStart * 60` e retorna `Math.floor((nightBeginning - epochMinutes) / 1440)` quando `masserPhaseAt(nightBeginning, epochMinutes) === 0`. Horas `< nightEnd` (00h00–04h59) caem na chave do dia anterior, preservando a mesma chave antes e depois da meia-noite inclusive com `gameTimeEpochHour !== 0`. Como a chave é o dia absoluto desde a época (`0, 1, 2`, depois `24, 25, 26`), não há colisão entre ciclos de 24 dias.
- **Uma vez por noite, recibo por qualquer via e compatibilidade JSON (`forced-werewolf.js:41-45,89-109,190-214`, `werewolf-service.js:207-244,267-283`, `werewolf-transform-journal.js:17,33-42,61-78`):**
  - Pré-aviso de Lua Cheia (`via=fullmoon`) reaproveita `healthy()`, `retryMs`, cancelamento em amanhecer/perda de autoridade/inelegibilidade e animação sincronizada do `werewolf-service`.
  - Na confirmação da raça (`session.raceApplied`), `werewolf-service.js:207-228` captura `moonNightNow()` e grava `moon: { night, stamp }` no diário durável (`werewolf-transform-journal.js:66`), valendo para **qualquer** via (Lua Cheia, 72 h, Z, F7, botão).
  - O botão **Forma da Besta** preserva `onlineMs` (72 h e Glow ≥ 24 h) via `buttonResetStamp`, enquanto `syncMoonNight()` (`forced-werewolf.js:192`) copia o recibo lunar para `row.lastFullMoonNight` sem acionar `reset()`.
  - Recuperação de intenção interrompida (`werewolf-transform-journal.js:33-42`) e `raceUncertain` sem `raceApplied` não fabricam recibo lunar falso.
  - Registros JSON v1 antigos (sem `lastFullMoonNight` em `aetherius-werewolf-compulsion.json` e sem `moon` em `.transforms.json`) passam na validação de boot.

### 2. Olhos Bronze — regras, borda, restauração, troca e entrada tardia — OK
- **Validação independente dos 33 records HDPT (`veil-eyes.js:10-44` × `DADOS-HOUSECARL_21-11_hdpt-bronze.md`):** cruzamento automatizado de todas as saídas de `bronzeSpecs()` para as 7 famílias × 2 sexos × 2 tiers contra a coleta houseCARL de `Miggyluv's Eyes.esp`: **33/33 records coincidem integralmente** em FormID local, EDID exato (exigido por `form-resolver.js:29,33`), `PNAM = Eyes` e flags `Playable, Male/Female`, sem nenhum record ausente ou divergente.
- **Regras Light/Glow (`forced-werewolf.js:227-231`, `veil-visuals.js:25-33`):** retorna `'glow'` se `phase === 0` (os 3 dias inteiros de Lua Cheia, mesmo após reset das 72 h) **ou** `row.onlineMs >= config.glowMs` (`WEREWOLF_EYES_GLOW_HOURS`, padrão 24 h). Fora disso, `'light'`. Fallbacks para raças sem Glow (Orc, Khajiit, Argonian M) escolhem `Bronze01` (Light); Argoniana F retorna `[]` e mantém olhos originais.
- **Envio só na borda e proteção durante forma de fera (`veil-eyes.js:76-121`):** `target === headpartIds[index]` retorna sem gravar `appearance`; ignora `phase === 'turning'` (`:79`) e forma de fera (`headpartIds: []` → `indices.length !== 1`, `:84`). Ao voltar à forma mortal (`finish`), a reconciliação atualiza Bronze Glow → Light se houve reset, preservando `previous.before`.
- **Troca Bruxa ↔ Lobisomem e restauração:** ambos os sistemas compartilham `aetherius-veil-eyes.json` e o campo `before` (olho original). Trocar paleta preserva `before`; perder a maldição restaura `previous.before` e limpa o registro após confirmar readback (`:91-99`).
- **Entrada tardia:** `appearance` atualizado no ator C++ é enviado aos observadores atuais via `UpdateAppearanceMessage` (`source\server\cpp\addon\property_bindings\AppearanceBinding.cpp:57-65`) e a observadores tardios via `CreateActorMessage.appearance` (`source\server\cpp\server_guest_lib\PartOne.cpp:797-800`).

### 3. Sincronização cliente↔servidor, privacidade, performance e regressões — OK
- **Contrato ponta a ponta:** `forced-werewolf.js:221-222` publica `aetheriusMoon` (`isVisibleByOwner: true`, `isVisibleByNeighbors: false`, `updateNeighbor: ''`, `updateOwner: 'ctx.sp.storage.aetheriusMoonV1 = ctx.value ?? null;'`), lido por `client\src\sobrenatural\supernaturalHud.ts:8,78,133,145` (`aetheriusMoonV1`) e despachado por `supernatural-hud.js:35-38` para `moon-hud.js:21-50`. `GamemodeUpdateService.onUpdate()` reexecuta `updateOwner` a cada frame a partir do `ownerModel`, garantindo recuperação mesmo após `loadGame` → `hud.reset()`.
- **Privacidade:** payload restrito a `{ v: 1, phase: 0..7, full: boolean }` ou `null`; nenhuma keyword (`JogadorLobisomem`), `curse` crua, `grantId` ou contador é enviada. `publishedMoon` evita reenvios fora de borda ou troca de sessão.
- **Performance do tick de 250 ms (`forced-werewolf.js:169-219`):** `readEnvironmentClock()` e `fullMoonNightAt()` executam 1× por tick (aritmética O(1)), independente do número de jogadores; o loop por jogador faz apenas consultas em `Map` em memória, sem I/O em regime estacionário.
- **Espelho e patches:** os 7 arquivos em `overlays\sobrenatural\files\server\gamemode\` têm SHA-256 idêntico aos de `source`; tanto `035-masser-clock.patch` quanto `039-hud-lua-masser.patch` passam `git apply --check --reverse`.

### 4. Cliente — HUD da Lua (`moon-hud.js`, `supernatural-hud.css`, `supernatural-hud.js`, `supernaturalHud.ts`) — OK
- **Fidelidade ao design aprovado:** SVG `viewBox="0 0 100 100"`, anel `r=34`, 8 marcas `r=38..44`, losango âmbar no topo, disco `r=28`, `litPath` idêntico ao de `hud-lua-propostas.html`, olho de lobo em fenda e anel respirando (`3.2s ease-in-out infinite`, `0.35 ↔ 0.95`) na Lua Cheia; opacidade `0.82` (cheia `1`), sombra e brilho âmbar fiéis; sem fundo, número ou tooltip.
- **Validação, ocultação e sem reinício de animação (`moon-hud.js:15-50`):** valida `v === 1`, inteiro `0..7`, booleano `full === (phase === 0)`; qualquer outro valor ou `null` chama `hide()` (que também cancela `readyFrame`). Primeira exibição e restauração após `hide()` são imediatas (sem `.sn-moon-ready`), ativando transições de `1500ms` após 2 `requestAnimationFrame`. Renovações repetidas da mesma fase (a cada 1 s por `supernaturalHud.ts:136`) não alteram classes nem reconstroem o SVG — apenas renovam o watchdog de `3500ms`. `prefers-reduced-motion: reduce` desliga transições e animações (`supernatural-hud.css:40`); `html.aetherius-browser-hidden #sn-moon-hud` oculta em menus (`:41`).
- **Posição acima da sobrevivência (`supernatural-hud.css:29` × `client\index.html:1031-1037`):** `#survival-hud` usa `right: 2vw`, `bottom: 3vh`, `--sv-d: clamp(42px, 3.2vw, 78px)`, largura `2.82 × --sv-d` e altura `2.52 × --sv-d`. Conferência matemática:
  - **1920×1080:** sobrevivência `x=1708,34..1881,60`, `y=892,77..1047,60` (centro `1794,97`); lua `56×56 px`, `x=1766,97..1822,97` (centro `1794,97`), `y=828,77..884,77`, folga `8,0 px` acima da caixa (e `11,1 px` acima do círculo de frio).
  - **1280×720:** sobrevivência `118,44×105,84 px` (`--sv-d = 42 px`); lua `37,33×37,33 px`, centrada horizontalmente, folga `5,33 px`.
  - **2560×1440:** sobrevivência `219,96×196,56 px` (`--sv-d = 78 px`); lua `56×56 px`, centrada horizontalmente, folga `10,67 px`.
  - Sem sobreposição calculada em nenhuma resolução padrão (permanece pendente apenas a validação visual em jogo das escalas de superfície entre `index.html` e `hud.html` no CEF).

---

## Testes executados na revisão

| Comando | Resultado |
|---|---|
| `node --test source/server/gamemode/supernatural/tests/masser.test.cjs overlays/sobrenatural/teste-transformacao-forcada.cjs overlays/sobrenatural/teste-painel-f7.cjs overlays/sobrenatural/teste-revisao-f7.cjs overlays/sobrenatural/teste-veu-mundo.cjs source/server/gamemode/werewolf/tests/werewolf.test.cjs` | **106/106 OK** (423 ms) |
| `node overlays/sobrenatural/teste-servico.cjs (Resolve-Path 'source/server/gamemode').Path` | **OK** |
| `node --test overlays/sobrenatural/teste-ritual-hud-nativo.cjs` | **5/7 OK, 2 falhas** (`:106` e `:141`, ver Achado 1) |
| `node --test client/ui-core/tests/moon-hud.test.cjs` | **2/2 OK** |
| `source\server\node_modules\.bin\tsc.cmd -p tsconfig.json --noEmit` | **OK** (exit 0) |
| `client-source\client\node_modules\.bin\tsc.cmd --noEmit` | **OK** (exit 0) |
| `git apply --check --reverse` (patches `035-masser-clock.patch` e `039-hud-lua-masser.patch`) | **OK** |
| Conferência SHA-256 dos 7 arquivos espelhados em `overlays\sobrenatural\files\server\gamemode\` | **7/7 idênticos** |
| Cruzamento automatizado dos 33 `bronzeSpecs()` contra `DADOS-HOUSECARL_21-11_hdpt-bronze.md` | **33/33 OK, 0 erros** |

---

## Observações opcionais (não bloqueantes)

1. **Custom property não utilizada (`supernatural-hud.css:29`):** `--sn-sv-d:clamp(42px,3.2vw,78px);` está declarada em `#sn-moon-hud`, mas não referenciada porque os `clamp(...)` já foram expandidos numericamente em `right` e `bottom`. Sem impacto funcional.
2. **Impacto de GPU da animação contínua no Meridian CEF (`supernatural-hud.css:36-37`):** durante a Lua Cheia (3 dias de jogo ≈ 6 h reais a `timeScale=12`), `.sn-moon-breathe` roda animação contínua `3.2s ease-in-out infinite`, fiel à prévia aprovada e ao padrão de `#sn-blood-reserve-hud`. Como `client\index.html:42-45` registra histórico de sensibilidade do Meridian a pulsos contínuos (VOIP), vale monitorar frame time durante o teste em jogo na Lua Cheia.
3. **Opacidade estática em `prefers-reduced-motion` (`supernatural-hud.css:40` vs `hud-lua-propostas.html:59`):** a prévia fixava `.breathe` em `opacity: .7` sob `reduced-motion`; o CSS final apenas desliga a animação, mantendo o anel estático em `opacity: 1`. Esteticamente aceitável e cumpre a regra de desligar animação.
4. **Fronteira das 05h00 durante `turnMs` (`werewolf-service.js:207`):** se uma transformação começar nos últimos segundos antes das 05h00 (ex.: 04h59:55), os 10,5 s de `turnMs` (≈ 2,1 min de jogo em `timeScale=12`) fazem `becomeBeast()` rodar já após as 05h00, quando `moonNightNow()` devolve `null`. Como a noite cheia já terminou e o tempo só avança, não há re-disparo em produção (apenas efeito observável se um admin retroceder o relógio no laboratório).

## Resposta do autor — 21/11/2026

**Terra High — achado P3 aceito pelo orquestrador.** No cliente `C:\Code\Aetherius - Servidor Local\client-source`, commit `6c78e250c31d3f4c9e7fbc2b7369944105c4800c` sobre `7e175bb45c0d57a7441420fb89f077805bf6dba1`, `supernatural-hud.js` chama `window.setMoonHud?.(...)`: ausência isolada do script da Lua não impede a projeção do Véu, sangue, ritual ou blackout. As outras chamadas permanecem como antes, conforme o escopo aceito; não se afirma que todas as pontes estejam protegidas. O patch incremental é `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\040-hud-lua-guarda.patch` (SHA-256 `5CB0A6CA11EC5140905DFC5825F254F33D13E87F64B90E05948926E9BE469D20`), aplicado após o 039; `git apply --check --reverse` passou sobre o commit do cliente.

No repositório `C:\Code\Aetherius - Servidor Local`, commit `3f3b9d8d9c494d1e4f74cf93775dd8133fc02370`, os dois objetos `window` de `overlays/sobrenatural/teste-ritual-hud-nativo.cjs` receberam o mock; o primeiro fixture também verifica inclusão do script, encaminhamento da Lua Cheia e exclusividade com sangue/Véu, e comprova que o ritual prossegue após remover o mock. A checagem de blackout anterior é preservada. O commit inclui o patch 040; não inclui o patch 039 preexistente não rastreado.

**Validação:** Node `teste-ritual-hud-nativo.cjs` 7/7; bateria ampliada **453/453** em cada modo `AETHERIUS_VAMPIRE_FEED_ENABLED=false` e `true`; `client/ui-core/tests/moon-hud.test.cjs` 2/2; em `client-source/client`, `npx tsc --noEmit` e `npm run build` passaram. A bateria usa todos os `overlays/sobrenatural/teste-*.cjs` exceto o standalone `teste-servico.cjs`, mais `source/server/gamemode/werewolf/tests/werewolf.test.cjs`, `source/server/gamemode/supernatural/tests/masser.test.cjs` e `source/server/gamemode/magic/tests/magic.test.cjs`.

O parecer original do Neon permanece registrado acima; esta resposta não representa reconfirmação independente da correção. Permanecem a reconfirmação pelo revisor e o teste visual no CEF/jogo, sem instalação ou deploy nesta correção.

## Reconfirmação — 21/11/2026

- **Revisor:** Gemini 4 Neon
- **Veredito atualizado:** **APROVADO** (Achado 1 [P3] encerrado; 0 achados abertos).

### Conferência do ajuste P3
1. **Guarda em `supernatural-hud.js:38` (`client-source` commit `6c78e250c31d3f4c9e7fbc2b7369944105c4800c`):** confirmada alteração cirúrgica para `window.setMoonHud?.(validMoon ? moon : null);`. Patch incremental `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\040-hud-lua-guarda.patch` conferido com `git apply --check --reverse` (exit 0) e SHA-256 `5CB0A6CA11EC5140905DFC5825F254F33D13E87F64B90E05948926E9BE469D20`.
2. **Fixtures CEF (`C:\Code\Aetherius - Servidor Local` commit `3f3b9d8d9c494d1e4f74cf93775dd8133fc02370`):** confirmada atualização em `overlays\sobrenatural\teste-ritual-hud-nativo.cjs`:
   - Inclusão de `'moon-hud.js'` na verificação de `hud.html` (`:111`);
   - Mock `setMoonHud` adicionado aos dois fixtures (`:117` e `:150`);
   - Asserção de encaminhamento de Lua Cheia e exclusividade com Sangue/Véu (`:132-133`), seguida de `delete env.window.setMoonHud` (`:134`) comprovando que Véu, Ritual e limpeza de desconexão seguem operando sem a ponte lunar.
3. **Execução independente das suítes:**
   - `node --test overlays/sobrenatural/teste-ritual-hud-nativo.cjs`: **7/7 OK** (0 falhas).
   - `node --test client/ui-core/tests/moon-hud.test.cjs`: **2/2 OK** (0 falhas).
   - Bateria ampliada (`overlays/sobrenatural/teste-*.cjs` exceto `teste-servico.cjs`, + `werewolf.test.cjs`, `masser.test.cjs`, `magic.test.cjs`):
     - `AETHERIUS_VAMPIRE_FEED_ENABLED=false`: **453/453 OK** (0 falhas).
     - `AETHERIUS_VAMPIRE_FEED_ENABLED=true`: **453/453 OK** (0 falhas).

Com o P3 resolvido e verificado, o lote servidor + cliente está **aprovado** na revisão cruzada, restando apenas a validação visual e funcional em jogo (CEF/laboratório).
