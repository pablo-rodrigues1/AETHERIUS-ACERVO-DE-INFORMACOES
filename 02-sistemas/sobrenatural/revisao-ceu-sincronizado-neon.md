# Revisão Cruzada — Opção A (Céu sincronizado sem tocar `GameDaysPassed`)

- **Data:** 21/11/2026
- **Revisor:** Gemini 4 Neon
- **Alvo:** Seção *"Opção A - viabilidade"* de `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_21-11_ceu-sincronizado.md` (Sol High) e protótipo `sky-visual-projection.cjs`.
- **Veredito:** **APROVADO COM RESSALVAS** — a **viabilidade da Opção A está confirmada** (fork mínimo MIT do MoonMod v2.0.2 para AE 1.6.1170 com relógio visual isolado é claramente a via mais segura e sustentável diante do ADR-0027). Não há bloqueio estrutural (0 P1), mas há **3 achados P2** de especificação/contrato e **2 achados P3** que precisam ficar fechados antes da implementação C++/TS para evitar bugs silenciosos de expiração, descarte de eventos e cobertura de teste.

## Artefatos verificados

| Item | Caminho / Referência | Verificação |
|---|---|---|
| Investigação (Sol) | `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_21-11_ceu-sincronizado.md` | Seções *"Contrato atual"*, *"Época, fase e precisão"* e *"Opção A - viabilidade"* (`:69-103`) |
| Upstream MoonMod v2.0.2 | `...\aetherius-ceu-task-66e9ab-...\moon-src\` (commit `4732799318176988f177757eebb514c95b76cb3f`) | `src/Hooks.cpp`, `src/main.cpp`, `src/Settings.cpp`, `src/Settings.h`, `src/PCH.cpp`, `CMakeLists.txt`, `LICENSE` |
| Protótipo Node | `C:\Users\pablo\AppData\Local\Temp\aetherius-ceu-task-66e9ab-5f4d275b7409454c89f2b5ca572d4980\sky-visual-projection.cjs` | SHA-256 `ABEE4B76EE9D2A406F225995AA7D5AC2047E8644F8C2C9DA3CECD8366B9EB85F`, executado com `node` a partir de `C:\Code\Aetherius - Servidor Local` |
| Cliente e ponte de referência | `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\timeService.ts` e `client\calendar-native\src\plugin.cpp` (`6c78e250`) | Leitura completa |
| Servidor | `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\environmentSystem.ts`, `environmentAuthority.ts` e `gamemode\supernatural\masser.js` (`a3ef672`) | Leitura completa |
| Binários e INI locais | `SkyrimSE.exe` (`1.6.1170.0`), `skse64_1_6_1170.dll` (`2.2.6`), `po3_MoonMod.dll` (SHA-256 `979DBA0B...`), `po3_MoonMod.ini` do Rudy ENB (SHA-256 `07927BD2...`) | Hashes e versões conferidos diretamente em disco |

---

## Achados

### 1. [P2] Conflito entre cadência de envio (`2 s` vs "uma vez por amostra"), reenvio pós-menu e expiração de 90 s

- **Evidência:**
  - `INVESTIGACAO_21-11_ceu-sincronizado.md:90` propõe: *"Reenviar a última amostra válida após `loadGame` e prontidão... Publicar uma vez por amostra nova ou retomada do jogo. ... Um prazo de validade de três intervalos (90 s, valor proposto) cobre perda de transporte"*.
  - `client\src\services\services\timeService.ts:64-73` interrompe `update()` com `return` em `Main Menu`, `Loading Menu`, perda de célula/3D (`nativeReadyAt = 0`) e `RaceSex Menu` (`applyChargenNoon(); return;`), enquanto o `update()` normal roda a cada 2 s (`:69`).
  - `sky-visual-projection.cjs:32-36` emite `AetheriusSkyClockSnapshot` a cada tick de 2 s sem deduplicação por amostra e sem enviar evento de invalidação em `disconnect()`.
- **Problema real (dois modos de falha se o contrato não for fechado antes de codar):**
  1. **Se o JS publicar a cada 2 s e o C++ renovar `steady_clock` em todo evento aceito:** em perda de transporte silenciosa (servidor congelado ou pacotes `gameTime` perdidos antes de `connectionDisconnect`), o JS continuaria extrapolando e reenviando a mesma `this.snapshot` a cada 2 s, **anulando a expiração de 90 s** no nativo.
  2. **Se o JS publicar só uma vez por `sequence` nova ou `loadGame`:** (a) fechar `RaceSex Menu` (`timeService.ts:73`) ou sair de `Loading Menu` sem evento `loadGame` **não** republicaria o céu após uma expiração > 90 s (ficando em fallback até o próximo broadcast de 30 s); (b) após um `loadGame`, se o C++ não zerar a última `sequence` aceita em `kPreLoadGame`/`kNewGame`, o reenvio da última amostra válida (mesma `sequence`) seria **rejeitado** pela própria checagem de sequência monotônica.
- **Diretriz para implementação:** manter a regra de publicar **uma vez por amostra nova** (preservando a expiração real de 90 s = 3 × 30 s de `environmentSystem.ts:176`), usando uma flag `skyPublished = false` no `TimeService` zerada em: (i) novo pacote `gameTime` aceito, (ii) `loadGame`, (iii) perda de prontidão (`timeService.ts:66`) e (iv) saída de `RaceSex Menu` (`:73`). No C++, `kPreLoadGame` e `kNewGame` devem incrementar `loadEpoch` **e** limpar a âncora + última `sequence` aceita.

### 2. [P2] Copiar o padrão `pending.exchange(true)` de `AetheriusCalendar` perderia invalidações e atualizações rápidas

- **Evidência:**
  - `INVESTIGACAO_21-11_ceu-sincronizado.md:88` cita `client\calendar-native\src\plugin.cpp:17–42,77–91` como modelo do canal `ModCallbackEvent → AddTask`.
  - Em `client\calendar-native\src\plugin.cpp:27`:
    ```cpp
    if (text.size() > 512 || pending.exchange(true)) {
        return RE::BSEventNotifyControl::kContinue;
    }
    ```
- **Problema real:** `pending.exchange(true)` descarta silenciosamente qualquer segundo `ModCallbackEvent` que chegue antes de a tarefa anterior rodar na thread do jogo. No `AetheriusCalendar` isso é mascarado porque o JS reenvia a cada 2 s. Na Opção A — com envio "uma vez por amostra nova" (a cada 30 s) mais mensagens explícitas de **invalidação** (desconexão / troca de sessão / pacote sem `skyDaysPassed`) — uma invalidação ou um ajuste `time.setHour`/`time.pause` que chegue no mesmo frame que o snapshot anterior seria descartado, deixando o céu dessincronizado por 30 s ou preso no estado anterior por até 90 s.
- **Diretriz para implementação:** no receptor do fork, usar um slot **latest-wins** protegido por mutex (sobrescrever o payload/invalidação pendente em `ProcessEvent` e só chamar `AddTask` quando `taskScheduled.exchange(true) == false`), garantindo que a última mensagem sempre vença sem enfileirar tarefas ilimitadas.

### 3. [P2] Escopo real do que `sky-visual-projection.cjs` comprova (escala 0, RTT 0 e ausência de invalidação ativa)

- **Evidência:**
  - `INVESTIGACAO_21-11_ceu-sincronizado.md:94-96` afirma que o protótipo *"valida o contrato TS→evento e a matemática"*, exercita rejeição de negativo e cessação após desconexão.
  - Executando `node sky-visual-projection.cjs` a partir de `C:\Code\Aetherius - Servidor Local`, o script passou com saída idêntica à descrita (`masser: 7`, `masserWithFloat: 0`, `secunda: 11`, ângulos `76.428°` e `255.0816°`, `saves: [10.25, 100.75]`).
  - Porém, no código do protótipo:
    - `sky-visual-projection.cjs:90` fixa `gameTimeScale: 0` e nunca envia `environmentSyncAck` (`rttMs === 0`). Assim, a compensação de meio RTT (`:31`) e a extrapolação temporal no `update()` (`:34`, multiplicada por `timeScale`) valem **zero** durante todo o ensaio — a matemática de projeção com `timeScale = 12`, avanço através de fronteira 7→0 e pausa/retomada (`12 → 0 → 12`) não foi exercitada.
    - `sky-visual-projection.cjs:146-148` só confirma que `A.modEvents.length` permanece `1` após `disconnect()`, sem testar emissão de evento de invalidação ao nativo nem o caso de um pacote `gameTime` posterior com `skyDaysPassed: null`/inválido após uma amostra válida (que no protótipo deixaria o nativo rodando na âncora antiga por até 90 s em vez de invalidar imediatamente).
- **Avaliação honesta:** o protótipo **prova bem** o que há de mais crítico no contrato estático (isolamento de `0x39`, preservação do relógio civil `0x38`, igualdade de `skyDaysPassed` com `masserPhaseAt`, e o bug real de `float` em `30191.9998`), e o próprio documento já ressalva que a entrega ao binário e o ciclo nativo ficam para o build SKSE. A ressalva aqui é que a futura suíte da Fase 2 precisará cobrir explicitamente `timeScale > 0`, `rttMs > 0`, `loadGame`, invalidação em desconexão e invalidação quando `skyDaysPassed` volta a `null`/inválido.

### 4. [P3] Segurança do `ModCallbackEvent`: `sender == 0x14` não autentica `TimeService` contra spoof por Papyrus

- **Evidência:**
  - `INVESTIGACAO_21-11_ceu-sincronizado.md:89`: *"Aceita apenas o evento previsto e o emissor local, valida o payload"*.
  - `client\calendar-native\src\plugin.cpp:22-23`: valida `event->sender->GetFormID() != 0x14`.
- **Análise honesta:** `SKSE::ModCallbackEvent` é um barramento global no processo do jogo. Qualquer script Papyrus local (de outro mod ou debug local) pode chamar `Game.GetPlayer().SendModEvent("AetheriusSkyClockSnapshot", json, 0.0)`, produzindo `sender->GetFormID() == 0x14`.
  - **Impacto real (baixo):** graças ao isolamento do ADR-0027, o fork **nunca** escreve `0x39` nem toca gameplay/Masser do servidor (`forced-werewolf.js` / `masser.js` são 100% server-side). O impacto máximo de um spoof local é cosmético na tela do próprio jogador (que já controla suas texturas/GPU localmente).
  - **Cuidado necessário no receptor:** não descrever `sender == 0x14` como autenticação forte do emissor, e garantir que um evento forjado com `serverSessionId` falso não trave o receptor (permitir substituição imediata quando um novo `serverSessionId` chegar — assim o próximo pacote real do servidor recupera o céu — além do fallback de 90 s e bounds rigorosos `0 <= days <= 3600000`, `0 <= timeScale <= 100`).

### 5. [P3] Travamento explícito de runtime AE `1.6.1170.0` no fork (`UsesNoStructs()` vs acesso a structs em `Hooks.cpp`)

- **Evidência:**
  - `moon-src\src\main.cpp:16-18` declara `v.UsesAddressLibrary(); v.CompatibleVersions({ SKSE::RUNTIME_LATEST }); v.UsesNoStructs();`.
  - Contudo, `moon-src\src\Hooks.cpp:20,47,128,154` acessa diretamente campos e offsets de structs do motor (`a_moon->moonNode`, `a_sky->currentClimate`, `a_moon->unkCC`, `dword[rsi + 0x1B0]`) e `Settings.cpp:61` lê `a_moon->stateTextures`.
  - `client\calendar-native\src\plugin.cpp:8,79` já adota trava explícita: `if (skse->IsEditor() || skse->RuntimeVersion() != "1.6.1170.0"_v) return false;`.
- **Problema real:** a investigação (`:83`) acerta ao exigir que a primeira entrega anuncie e valide apenas AE Steam `1.6.1170`. Se o fork mantiver `UsesNoStructs()` sem checagem explícita da versão em `SKSEPlugin_Load`, o SKSE 2.2.x aceitaria carregar a DLL em outro executável 1.6.x com offsets diferentes em `Moon::Update` ou `RE::Moon`, com risco de crash. Travar explicitamente `RuntimeVersion() == 1.6.1170.0` no `SKSEPlugin_Load` (além de revisar o port sobre a mesma `CommonLibSSE-NG` já usada em `client/calendar-native/CMakeLists.txt`) elimina esse risco.

---

## Verificação dos demais critérios solicitados

| Critério | Avaliação | Evidência |
|---|---|---|
| **Fonte do dia (mesma época de `masserPhaseAt`)** | **OK** | `environmentAuthority.ts:241,246` e `masser.js:8-11` usam `(gameMinutes - clockEpochMinutes()) / 1440` com `Math.floor`. Em `double`, `floor(skyDaysPassed)` coincide exatamente com `masserPhaseAt` (para `days >= 0`) e elimina o arredondamento de `float` (`Math.fround(30191.9998) === 30192`, fase 0 em vez de 7). Validar `days >= 0 && days <= 3600000` (e `std::isfinite`) antes de `static_cast<std::uint32_t>(std::floor(days))` evita UB em C++. |
| **Âncora `steady_clock` e projeção** | **OK** (com Achados 1–2) | `std::chrono::steady_clock` é monotônico e acompanha o tempo real da autoridade do servidor mesmo com pausas/menus locais. Projetar em JS só até o `sendModEvent` e ancorar em `steady_clock` na recepção nativa evita dupla contagem. Leitura de uma única amostra sincronizada em `Moon_UpdatePhase` (para Masser + Secunda) e `set_moon_angle` (em `double` até o `float` final) é O(1), sem alocação nem I/O por frame. |
| **Conflito com MoonMod original (dois provedores)** | **OK** | Confirmado em `moon-src\src\PCH.cpp:30-34` (`asm_replace` preenche `0x95` bytes com `INT3` + jump) e `Hooks.cpp:171` (`write_branch<6>` em `Moon::Update`): dois provedores ativos em paralelo corrompem os hooks e podem causar crash. Conservar o nome `po3_MoonMod.dll` garante vencedor único no VFS do MO2 e mantém `Settings.cpp:79` lendo `Data/SKSE/Plugins/po3_MoonMod.ini` (onde vence o INI do Rudy ENB). A checagem de empacotamento para garantir ausência de segunda DLL com outro nome é essencial. |
| **Versões SE / AE / VR** | **OK** (com Achado 5) | `moon-src\CMakeLists.txt:9-11,35-56` e `main.cpp:10-46` confirmados. Executável local `1.6.1170.0` e SKSE `2.2.6` confirmados em disco. Decisão de validar primeiro só AE Steam `1.6.1170` e tratar SE `1.5.97` / outros AE / GOG / VR como alvos ABI separados está tecnicamente correta. |
| **Licença** | **OK** | `moon-src\LICENSE:1-21` é **MIT License (Copyright (c) 2022 powerof3)** — permite fork, modificação e redistribuição preservando o aviso de copyright e texto da licença. Por ser DLL separada comunicando via `ModCallbackEvent` JSON (e MIT ser compatível com GPL-3.0), não há conflito de licença com `timeService.ts` (GPL-3.0) nem `environmentAuthority.ts` (AGPL-3.0-or-later). |

---

## Observações opcionais (não bloqueantes)

1. **Atraso visual inicial de ~5–6 s após spawn (`timeService.ts:68-69`):** reutilizar a guarda `nativeReadyAt = now + 5000` (pensada para evitar corrida de escrita no `RE::Calendar` durante `LoadGame`) faz a lua renderizar pela fase local do save nos primeiros 5–6 s após carregar e então saltar para a fase do servidor. Como o fork só escreve em memória própria (nunca em `RE::Calendar`), esse atraso é seguro, mas pode ser reduzido ou mantido conforme preferência visual no teste em jogo.
2. **Escrita de INI na inicialização do upstream (`moon-src\src\Settings.cpp:90`):** o construtor de `Settings` chama `(void)ini.SaveFile(path.c_str());` mesmo só lendo o arquivo. No MO2 isso pode gravar cópia em `overwrite/` ou alterar o arquivo vencedor durante testes locais; remover essa chamada no fork (ou mantê-la apenas se o arquivo não existir) evita sujeira no VFS.
3. **Alinhamento de `CommonLibSSE-NG` (`client/calendar-native/CMakeLists.txt`):** como o projeto já compila `AetheriusCalendar.dll` para AE `1.6.1170` com `CommonLibSSE-NG` + `nlohmann_json` (e o `CommonLibSSE` de 2022 fixado pelo MoonMod v2.0.2 antecede `1.6.1170`), avaliar na implementação compilar o fork diretamente contra a mesma `CommonLibSSE-NG` do laboratório — lembrando, como Sol já destacou, de conferir manualmente `RE::Moon` e os três pontos de patch em `1.6.1170`.

## Resposta do autor — implementação de 21/11/2026

O orquestrador aceitou os cinco achados. Candidato Sol High em servidor `79ce921`, cliente `8cfa553`, fork `18ef3eb` e overlay `81966c3`. O retorno abaixo registra a correção e a evidência do autor. A revisão do candidato final pelo Neon segue pendente.

| Achado | Tratamento e evidência |
|---|---|
| P2.1 — cadência, menus e prazo | `TimeService` emite por sequência nova e pode reenviar depois de `loadGame`, perda de prontidão ou `RaceSex`. A idade desde a chegada de `gameTime` acompanha a mensagem. `SkyClock` desconta essa idade do prazo de 90 s, de modo que reentrar em um menu não prolonga o mesmo pacote. Os testes do cliente cobrem a deduplicação, reenvio com a idade original e a expiração. O nativo limpa geração, ordem e âncora ao trocar de jogo/save. |
| P2.2 — perda de invalidações no receptor | `SkyClock::Receive` usa um slot protegido por mutex. Uma amostra válida mais recente ocupa o slot mesmo se já houver tarefa na fila. A invalidação limpa slot e âncora imediatamente, inclusive com a tarefa agendada, e deixa a tarefa existente disponível para uma próxima amostra. CTest cobre amostra seguida de invalidação antes do dreno, duas amostras com uma tarefa e callback obsoleto em nova geração. |
| P2.3 — cobertura com avanço, RTT e invalidação | O teste roda a `TimeService` real com escala 12 e RTT 400 ms, mede a projeção com meio RTT e atravessa o limite 7→0. Exercita pausa/retomada, retomada pós-carregamento/menu e invalidação por campo ausente, valor inválido e desconexão. A contagem `0x39` dos mocks fica intacta com laboratório de calendário desligado. Passaram 6/6 no cliente, CTest 1/1 e 557/557 por modo na bateria ampliada. |
| P3.4 — emissor local | `src\main.cpp` filtra o nome, o emissor `0x14` e comprimento. `SkyClock::Parse` valida variante, versão, sessão, sequência, dias, escala e idade. Uma nova sessão válida substitui a anterior. Papyrus local pode forjar o evento sob o jogador. O impacto fica na renderização do próprio processo, pois o canal não escreve no calendário nem alimenta a autoridade do servidor. |
| P3.5 — runtime e leitura do INI | O manifesto e `SKSEPlugin_Load` fixam Steam AE `1.6.1170.0`. O entry point registra erro e retorna antes de `SKSE::Init` em runtime incompatível, caso o próprio loader já não recuse pelo manifesto. O fork usa CommonLibSSE-NG flatrim, faz verificações nos pontos de patch antes de escrever e lê o INI sem salvá-lo. DLL Release compilada e exports conferidos. Carga, checagens em memória e logs de runtime ainda precisam de ensaio em Skyrim. |

A DLL tem SHA-256 `7BAD2A4CFC59259420CE39DC9A2FEE07F42052C70276F714B8724EB4F6AC2E8B`. Patches, compilação, hashes e teste de dois jogadores estão na entrega. [Etapa 2 — Céu sincronizado](ETAPA-2_ceu-sincronizado.md)

## Revisão da implementação — 21/11/2026

- **Revisor:** Gemini 4 Neon
- **Alvos revisados:** servidor `79ce921` (`source`), cliente `8cfa553` (`client-source`), fork AE `18ef3eb` (`tools\moonmod-source`, sobre upstream `4732799`), overlay `81966c3` (`C:\Code\Aetherius - Servidor Local`) e DLL Release `tools\native\sky-moons\po3_MoonMod.dll`.
- **Veredito:** **APROVADO (sem achados)** — todos os 3 P2 e 2 P3 da revisão de viabilidade foram resolvidos de forma limpa e comprovada por testes automatizados, sem regressão. Aprovado para avançar à validação em jogo (carga no Skyrim AE 1.6.1170 e ensaio com dois jogadores).

### 1. Conferência dos 5 achados anteriores

| Achado | Status | Evidência verificada no código |
|---|---|---|
| **P2.1 — Cadência, menus, `loadGame` e prazo de 90 s** | **Resolvido** | `client-source\client\src\services\services\timeService.ts:25-26,45-51,64-101,112-137`: publica 1× por `sequence` aceita (`skyPublished`) levando `ageMs = Math.max(0, now - s.receivedAt)`. `loadGame`, `RaceSex Menu`, `Main Menu`/`Loading Menu` e perda de prontidão chamam `invalidateSky()` + `flushSkyInvalidation()` para permitir republicação sem estender o prazo. `SkyClock::DaysPassed` (`tools\moonmod-source\src\SkyClock.cpp:90-91`) expira em `elapsedMs + active->ageMs >= 90000`, e `SkyClock::Reset` (`:37-47`) limpa `loadEpoch`, `session`, `sequence`, `pending` e `active` em `kPreLoadGame`, `kPostLoadGame` e `kNewGame` (`src\main.cpp:58-66`). |
| **P2.2 — Perda de invalidações / `pending.exchange(true)`** | **Resolvido** | `tools\moonmod-source\src\SkyClock.cpp:55-84`: substituído por slot *latest-wins* sob `std::mutex`. Uma amostra mais nova substitui `pending` mesmo com tarefa já agendada (`scheduled == true`); invalidação limpa `pending`, `active`, `session` e `sequence` imediatamente mantendo a tarefa pendente disponível para uma amostra subsequente no mesmo frame. Coberto em `tests\SkyClock.test.cpp:35-48,73-79`. |
| **P2.3 — Cobertura de projeção com `timeScale > 0`, RTT e invalidação** | **Resolvido** | `client-source\client\meridian\time-service.test.cjs:88-205` (6/6 OK): exercita `TimeService` real com `timeScale: 12`, RTT `400 ms` (compensação de meio RTT `+200 ms`), travessia da fronteira `30191.9998 → 30192`, pausa/retomada (`12 → 0 → 12`), deduplicação, reenvio pós-`RaceSex`/`Loading Menu`/`loadGame` preservando `ageMs`, expiração aos 90 s, invalidação por campo ausente/`-1`/desconexão com retry de VM e preservação do modo legado do laboratório. |
| **P3.4 — Segurança do `ModCallbackEvent` local** | **Resolvido** | `src\main.cpp:27-35` valida nome, `sender == 0x14` e `strnlen_s(..., 513)`. `src\SkyClock.cpp:11-35` usa parse não-lançador (`nlohmann::json::parse(..., false)`) e valida versão, variante, sessão hex de 32 chars (idêntica a `environmentSystem.ts:24`), sequência `1..MAX_SAFE_INTEGER`, `days` em `[0, 3_600_000]`, `scale` em `[0, 100]` e `ageMs < 90_000`. Troca de `serverSessionId` substitui a anterior sem trava permanente (`:68-71`), e o limite de ameaça (spoof local por Papyrus afetando apenas o visual do processo) está documentado honestamente em `ETAPA-2_ceu-sincronizado.md:39`. |
| **P3.5 — Trava de runtime AE `1.6.1170.0` e leitura do INI** | **Resolvido** | `src\main.cpp:8-14,18,90-93`: `SKSEPluginInfo` declara `StructCompatibility::Dependent` e `RuntimeCompatibility = { "1.6.1170.0"_v }`, e `SKSEPluginLoad` rejeita editor ou qualquer runtime `!= 1.6.1170.0` com erro em log antes de `SKSE::Init`. `src\Hooks.cpp:192-214` verifica vtable (`REL::ID(26168)`), instrução deslocada `8B 86 B0 01 00 00` em `Moon::Update + 0x146`, ausência de desvio prévio na entrada de fase (`26158`) e opcodes `0xE8` em `+0x2C5`/`+0x2EF` antes de aplicar qualquer patch. `src\Settings.cpp:56,90-102` exige `phaseLength > 0` e remove `ini.SaveFile(...)`. |

### 2. Verificação de isolamento de `0x39`, fork mínimo, licença e artefatos

- **Isolamento de `GameDaysPassed` (`0x39`):**
  - Servidor (`source\server\ts\systems\environmentAuthority.ts:246-249`): `gameDaysPassed` permanece condicionado a `this.dateChangesEnabled`; `skyDaysPassed` é campo separado `[0, 3_600_000]` ou `null`.
  - Cliente (`timeService.ts:139-148`): escrita em `0x39` e envio de `AetheriusCalendarSnapshot` continuam estritamente condicionados a `this.snapshot.gameDaysPassed !== null`; `skyDaysPassed` nunca alcança esses caminhos.
  - Nativo (`Hooks.cpp:7-15,67-70,137-143`): apenas lê `SkyClock::DaysPassed` (em `double`) com fallback somente-leitura para `RE::Calendar::GetSingleton()->GetDaysPassed()`, sem escrever em nenhum global ou campo de `RE::Calendar`.
- **Diff mínimo do fork (`4732799..18ef3eb`):** 9 arquivos (`CMakeLists.txt`, `README.md`, `src/PCH.h`, `src/Settings.cpp`, `src/Hooks.cpp`, `src/main.cpp`, `src/SkyClock.h`, `src/SkyClock.cpp`, `tests/SkyClock.test.cpp`). `LICENSE` **intocada** (diff vazio; SHA-256 `51E657EA7520301284BD80D64088F91063A37724DF36DD147CE6DF21CF00943D` idêntico em `tools\moonmod-source\LICENSE` e `tools\native\sky-moons\LICENSE`); créditos preservados em `SKSEPluginInfo` (`powerofthree; Aetherius`), log e `README.md`.
- **Integridade da DLL, bundle, espelho e patches:**
  - `tools\native\sky-moons\po3_MoonMod.dll`: PE64 válido, exports confirmados (`SKSEPlugin_Load`, `SKSEPlugin_Query`, `SKSEPlugin_Version`), SHA-256 `7BAD2A4CFC59259420CE39DC9A2FEE07F42052C70276F714B8724EB4F6AC2E8B` (idêntico ao relatório).
  - `tools\native\sky-moons-client\skymp5-client.js`: SHA-256 `24EA076F2BC4BF0D95939D3B7581B8928D555ECA484F75EA8B8005769EE8AFAC` (idêntico).
  - Espelho `masser.test.cjs`: SHA-256 `5BD636E26D325293446650C671738F4ACC0698E47C1D513A39CA5C941863DE47` idêntico entre `source` e `overlays\sobrenatural\files`.
  - Patches (`036-ceu-sincronizado.patch`, `041-ceu-sincronizado.patch`, `001-relogio-visual.patch`): SHA-256 conferidos e `git apply --reverse --check --whitespace=error` passou (`exit=0`) nos três repositórios.

### 3. Suítes executadas nesta revisão

| Verificação | Resultado |
|---|---|
| `ctest --test-dir tools/native/sky-moons-build -C Release --output-on-failure` | **1/1 OK** (`sky-clock`, 0.01 s) |
| Bateria direcionada (`masser`, `transformacao-forcada`, `painel-f7`, `revisao-f7`, `veu-mundo`, `werewolf`, `time-service`) | **112/112 OK** |
| `node overlays/sobrenatural/teste-servico.cjs` | **OK** |
| Bateria ampliada (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) | **557/557 OK** |
| Bateria ampliada (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) | **557/557 OK** |
| TypeScript `--noEmit` (`source/server` e `client-source/client`) | **OK** (`exit=0` em ambos) |
| Suíte geral Meridian (`client/meridian/*.test.cjs`) no candidato `8cfa553` | **127/146** (19 falhas; +6 novos de `time-service.test.cjs` todos OK) |
| Suíte geral Meridian em cópia limpa da base `6c78e250` (extraída via `git archive`) | **121/140** (**exatamente as mesmas 19 falhas por nome**: 13 de teleporte indexado de NPC e 6 de `BrowserService`/foco/PTT) — **confirmadas preexistentes, zero regressão** |

### Observação opcional (não bloqueante)
- Em `timeService.ts:112-116`, caso `flushSkyInvalidation()` falhe enquanto `!this.snapshot` (ex.: `player` indisponível após desconexão), `nextApply` permanece `0` e o retry ocorre a cada frame até a entrega ou nova conexão. O custo é desprezível (checa apenas `getPlayer()`/`sendModEvent`) e garante entrega rápida assim que o jogador fica disponível.
