# Revisão Independente — Lote Sol High #1 (Bruxa / HUD / Ritual / Spawn)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura)
- **Data:** 19/11/2026
- **Lote revisado:**
  - Cliente (`C:\Code\Aetherius - Servidor Local\client-source`): commit `61d754731b9421fd152a390573b41c6c8460ed42` (`fix(sobrenatural): monta HUD Meridian e restaura a entrega no spawn`)
  - Laboratório (`C:\Code\Aetherius - Servidor Local`): commit `42315d16f657d67dab4e198e3de2435fbc5b42d1` (`fix(sobrenatural): corrige HUD ritual e replica efeitos do Veu`)
  - Documentação (`C:\Code\Aetherius - Servidor Local`): commit `cebb42c5ef928ce024c7fab15f3b748682e9cfb2` (`docs(sobrenatural): registra deploy e validacao do Ritual v2`)
  - Investigação de referência: [INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md)
  - Dados houseCARL (Luna MAX, somente leitura, perfil `AETHERIUS - LOCAL`): [DADOS-HOUSECARL_19-11_ritual.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/DADOS-HOUSECARL_19-11_ritual.md)

---

## Veredito

**APROVADO COM CORREÇÕES PONTUAIS (2 defeitos médios no cliente `supernaturalHud.ts`, sem bloqueios no servidor/ESP/HUD base).**

- **Aprovados sem achados bloqueantes:** servidor (`42315d1`: `veil-ritual.js`, `veil-ritual-fx.js`, `veil-bond.js`, `veil-eyes.js`, `veil-visuals.js`, `blood-presentation.js`), contrato v2 (`CONTRATO_vinculo-do-veu.md`), ESP (`000843`), deploy dos 4 WAVs, `gamemodeEventSourceService.ts`, `gameplayGrantService.ts`, `remoteServer.ts`, `hud.html`, `hud.js`, `blood-reserve-hud.js`, `veil-hud.js`, `supernatural-hud.css`, isolamento do UI Core, ausência de vazamento de keyword e cancelamento autoritativo com crédito zero.
- **Requerem ajuste pontual em `supernaturalHud.ts` antes de consolidar o bundle:**
  1. **R1 (Médio — regressão funcional):** A migração do `blackout` do terror para `SupernaturalHud` (`supernaturalHud.ts:53-55, 114-129` + `supernatural-hud.js:43-50`) causa descarte silencioso quando o throttle de 200 ms reduz `blackoutMs` abaixo de `1000 ms` (`veil-terror-presentation.js:9`) e corta bruscamente o fade de retorno de 1.200 ms em 100% dos casos.
  2. **R2 (Médio — falso cancelamento de entrada):** `SupernaturalHud` (`supernaturalHud.ts:44-51, 101-102`) não verifica foco do chat/F7 (`sp.browser?.isFocused()`) nem menus nativos (`Utility.isInMenuMode()`, `Ui.isMenuOpen("MeridianUI_FocusMenu")`), podendo cancelar o ritual de 5 min se o jogador digitar no chat ou interagir com menu/console.

---

## 1. Achados Verificados (Defeitos Reais)

### R1 — Regressão funcional na tela preta do terror (`blackout`): descarte silencioso e corte do retorno gradual (Médio)

- **Arquivos e linhas:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\veilEffects.ts:122-130`
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\sobrenaturalService.ts:22`
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\supernaturalHud.ts:53-55, 114-129`
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\supernatural-hud.js:43-50`
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-terror-presentation.js:9`
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\veil-terror.js:94`
- **Causa e prova por fluxo:**
  1. O servidor gera `durationMs = 1000 + Math.floor(random() * 501)` (`veil-terror.js:94`, faixa `[1000, 1500]`).
  2. `VeilEffects.play()` (`veilEffects.ts:128-130`) toca `breathing_witch`, chama `this.blackout(duration)` → `this.hud.blackout(duration)` (`sobrenaturalService.ts:22`) e programa o fim do efeito em `endAt: now + duration + 1200` (preto + 1.200 ms de retorno gradual CSS).
  3. **Descarte silencioso:** `SupernaturalHud.blackout()` (`supernaturalHud.ts:54`) grava `this.blackoutUntil = duration > 0 ? Date.now() + duration : 0`. Na `update()`, a linha `114` impõe throttle de `200 ms` (`if (now - this.sentAt < 200 || !player.is3DLoaded()) return;`), e a linha `128` envia `blackoutMs: blackout ? blackout - now : 0`. Com qualquer atraso ≥ 1 ms para `duration = 1000` (ou atraso dentro da janela de 200 ms para `duration ∈ [1000, 1200]`), `blackoutMs` chega `< 1000` em `supernatural-hud.js:45`, que chama `window.setVeilTerrorBlackout(Math.min(1500, duration))` → `veil-terror-presentation.js:9`:
     ```js
     if(disposed||!Number.isFinite(durationMs)||durationMs<1000||durationMs>1500)return false;
     ```
     O componente rejeita o valor e retorna `false` sem criar o overlay, enquanto `supernatural-hud.js:46` marca `blackout = true`, o áudio `breathing_witch` toca sem tela preta e o cooldown de 90 min já foi gravado no servidor.
  4. **Corte abrupto do fade de retorno (100% dos casos que iniciam):** mesmo quando `blackoutMs` chega em `[1000, 1500]`, `supernaturalHud.ts:54` definiu `blackoutUntil = Date.now() + duration` sem os `+ 1200 ms` do retorno gradual. Assim que `now >= blackoutUntil` (exatamente quando `fadeTimer` em `veil-terror-presentation.js:9` adicionaria `.is-returning`), `supernaturalHud.ts:117` zera `blackout`, `key` muda e envia `blackoutMs: 0`. `supernatural-hud.js:48` chama `window.setVeilTerrorBlackout(0)` → `terror.clear()`, removendo o overlay bruscamente sem o retorno gradual.
- **Direção de correção:** manter o estado de blackout em `SupernaturalHud` com a duração validada (`[1000, 1500]`) preservada até `VeilEffects` chamar `blackout(0)` (já feito em `clearFx()` ao atingir `endAt = now + duration + 1200` ou em interrupções/reset), ou definir o teto local para `duration + 1200` enviando a duração nominal da fase preta sem decrementá-la abaixo de `1000`. Adicionar teste em `teste-ritual-hud-nativo.cjs` exercitando `blackout(1000)` após throttle de 150 ms e confirmando que `blackoutMs: 0` não é enviado antes dos `+ 1200 ms`.

### R2 — Cancelamento por movimento no Ritual não verifica foco do chat (`sp.browser`) nem menus nativos (Médio)

- **Arquivos e linhas:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\supernaturalHud.ts:44-51, 101-102`
  - Comparação com o padrão do cliente: `aetheriusUiService.ts:247`, `emoteControl.ts:235-238`, `helmetToggleService.ts:31`, `Voicerangeservice.ts:13`, `Voiptoggleservice.ts:15-28`
- **Causa e prova por fluxo:**
  - `supernaturalHud.ts:45-46` marca `this.movementHeld = true` em qualquer `buttonEvent` cujo `userEventName` esteja em `MOVEMENT` (`Forward`, `Back`, `Strafe Left`, `Strafe Right`, `Move`, `Jump`, `Auto-Move`), filtrando apenas `code === 0x25` (`EMOTE_KEY`).
  - `supernaturalHud.ts:101-102` só suprime/limpa `movementHeld` quando `focused === true` (`aetheriusUiFocusChanged`, que representa apenas o foco da view principal do UI Core/TAB).
  - Em Aetherius, o chat (e o painel F7) roda em `sp.browser`, onde `aetheriusUiFocusChanged` é `false` (explicitamente documentado em `Voiptoggleservice.ts:15-17`). Da mesma forma, menus nativos (`Console`, `Journal`, `Pause`, etc.) e janelas transitórias de `MeridianUI_FocusMenu` (comentado em `emoteControl.ts:234-238`) não são cobertos apenas por `focused`.
  - Durante um Ritual de 5 minutos em servidor RP, digitar no chat (`sp.browser.isFocused()`) ou no console/menu letras mapeadas em movimento (`W/A/S/D/Espaço/C`) pode disparar `ritual.cancel { reason: "movement" }` indevidamente.
- **Direção de correção:** tratar `uiOrMenuBusy = focused || this.sp.browser?.isFocused?.() || this.sp.Utility?.isInMenuMode?.() || this.sp.Ui?.isMenuOpen?.("MeridianUI_FocusMenu")` (com `try/catch` defensivo como em `emoteControl.ts:236-240`) para ignorar/limpar `movementHeld` enquanto o jogador estiver no chat ou em menu. (Opcional: usar `keybind("emotes")` de `../../aetherius/keybinds` em vez de `0x25` fixo para respeitar remapeamento no launcher.)

---

## 2. Observações Opcionais (Não Bloqueantes)

1. **B4 — Alinhamento de duração do som do Ritual (`DADOS-HOUSECARL_19-11_ritual.md` §1):**
   `SN_Veu_RitualCharge` (`000843:AETHERIUS - SOBRENATURAL.esp`) referencia `SNDR 073FD9:Skyrim.esm` (`MAGDestructionFireCharge300`), vencedor `Skyrim.esm`, com `SOPM 07E5DC:Skyrim.esm` (`SOMMono01800`, 150–1.800 u, atenuação espacial). O houseCARL confirmou `LoopAndRumble.Loop = None` e WAV vencedor `Sonorum Arcana - The Magic Sound Compendium\sound\fx\mag\destruction\mag_destruction_fire_charge_300.wav` com duração de **`3,351020 s`**. Como `veil-ritual-fx.js:88-93` chama `Sound.play(actor)` uma única vez na montagem de cada réplica (incluindo vizinho tardio), o som toca por ~3,35 s no início/entrada e silencia no restante dos 5 minutos. Isso está coerente com o que Sol documentou em `INVESTIGACAO_18-11_excecoes-hud-ritual-pisos.md` e `ETAPA-4`, mas vale confirmação explícita do dev se o disparo inicial de ~3,35 s satisfaz B4 ou se desejava áudio em loop contínuo durante todo o ritual.
2. **B5 — Ordem `dispelSpell` vs `removeSpell` (`veil-ritual-fx.js:16-18`):**
   `cleanup()` chama `actor.dispelSpell(spell)` *antes* de `actor.removeSpell(spell)`. Como `SN_Veu_RitualRune` (`00083A`) é Ability `ConstantEffect` apontando para `00000F` (`FXSummonMassBodyHolderRune`, flags `NoDuration, FXPersist, HideInUI`), o padrão adotado em `client/src/sync/appearance.ts:227-228` — `removeSpell` primeiro e `dispelSpell` depois — evita qualquer chance de a Ability reaplicar o efeito antes da remoção e varre eventual `ActiveEffect` persistente. A causa raiz original de B5 (wrapper `Actor` expirado em `stop()` lançando `indexInPool`) foi corrigida.
3. **Adjacente fora do diff `42315d1` (`veil-terror.js:31`):**
   Em `veil-eyes.js:7, 37`, Sol corrigiu corretamente a validação de chave de `!/^\d+$/.test(key)` para aceitar UUIDs (`CHARACTER_KEY`). O arquivo irmão `overlays/sobrenatural/files/server/gamemode/supernatural/veil-terror.js:31` (não tocado neste lote) ainda mantém `!/^\d+$/.test(key)`: assim que um personagem com `characterId` UUID sofrer o primeiro blackout e gravar `data/aetherius-veil-terror.json`, o próximo boot do servidor lançará `Error('Registro de tela preta inválido')`. Recomenda-se aplicar o mesmo `CHARACTER_KEY` em `veil-terror.js:31` no próximo micro-lote.
4. **Micro-otimização em `veil-bond.js:38-39`:**
   No script `updateOwner`, `JSON.stringify(value)` executa a cada frame antes da guarda `if (ctx.sp.storage.aetheriusSupernaturalHudNative || !ctx.sp.browser?.executeJavaScript) return;`. Mover o `return` antes do `JSON.stringify` elimina a serialização por frame quando a HUD nativa está ativa (custo atual é desprezível, ~80 bytes/frame).

---

## 3. Verificação Item a Item (Causa Raiz, Segurança e Regressão)

| Item | Avaliação da causa raiz e correção | Status |
|---|---|---|
| **Exceção `argsJsonDumps`** (`gamemodeEventSourceService.ts:17, 98-114`) | `CustomEventMessage.h` exige `std::vector<std::string>`. O código anterior deixava `JSON.stringify(undefined) === undefined`, que serializava como `null` no array JSON e falhava no simdjson com `INCORRECT_TYPE`. A correção substitui qualquer resultado `undefined` pela string `"null"`, preserva posições e loga `eventSource argument encoded as null` uma vez por `eventName` para identificar o emissor concreto no próximo teste. | **Aprovado** |
| **V1 (HUD Reserva de Sangue) & B1 (Selo da Bruxa)** (`supernaturalHud.ts`, `hud.html`, `hud.js`, `supernatural-hud.js`, `blood-reserve-hud.js`, `veil-hud.js`, `blood-presentation.js`, `veil-bond.js`) | Causa raiz confirmada: `aetherius-client/index.html` no mod de compatibilidade não continha `setBloodReserveHud` nem os scripts do selo, enquanto `hud.html` no `AETHERIUS - UI CORE (LOCAL)` não os carregava. A nova rota usa o canal nativo já existente em `AetheriusUIBridge.dll` (`main.cpp:884-891`: `AetheriusUI.ToView` com `moduleId: "hud"`, `kind: "event"` → `aetherius-hud-state` em `g_hudView`), com `hud.js:14` ignorando `payload.state.supernatural` e `veil-hud.js:4` resolvendo `witch-gauge-reference.png` via `new URL(..., document.currentScript.src)`. `blood-presentation.js` e `veil-bond.js` passam a gravar em `sp.storage` e suprimem o fallback `sp.browser` quando `aetheriusSupernaturalHudNative` está ativo. `survival-service.js:478-482` segue publicando `needs: []` para vampiros. Arquivos instalados em `AETHERIUS - UI CORE (LOCAL)\MeridianUI\aetheriusui` conferidos com hash idêntico ao `client-source`. **Ressalva:** R1 no `blackout` trafegado pela mesma ponte. | **Aprovado com R1** (V1 e B1 OK; `blackout` afetado) |
| **B2 (Timer do Ritual fora do TAB)** (`veil-ritual.js:79-104, 172-200`, `veil-ritual-fx.js:99-106, 126-127, 140`, `supernaturalHud.ts`, `supernatural-hud.js`) | Causa raiz confirmada: fechar o TAB desmontava o polling de `ritual.status`. Agora o servidor publica propriedade privada `aetheriusVeilRitualStatus` (`isVisibleByNeighbors: false`) no início, a cada 1 s e no encerramento; `supernatural-hud.js` renderiza decorridos + restantes (`4 Hz` só enquanto ativo) com expiração de 3,5 s e limpeza ao encerrar/desconectar. `styles.css` não sobrescreve `[hidden]` em `<section>`. | **Aprovado** |
| **B3 (Imobilidade, movimento exceto K, mensagem e contrato v2)** (`supernaturalHud.ts`, `veil-ritual.js:9-13, 96-104, 151-156`, `CONTRATO_vinculo-do-veu.md`) | Cliente aplica `setDontMove(true)` durante ritual ativo e libera respeitando SkyDeath (`if (alive)`). Movimento envia `ritual.cancel { ritualId, reason: "movement" }` pelo envelope autenticado `aetherius-ui:v1:request`. Servidor continua autoritativo: valida sessão/maldição/ID, cancela também por deslocamento `> 12` unidades ou troca de célula a cada 250 ms, preenche sempre `endReason` (mapeando `reason` desconhecido para `'player'`), mantém campos legados v1 (`outcome`/`reason`) e **garante crédito zero** em todo cancelamento (inclusive se ocorrer durante `await consume()` em `veil-ritual.js:120-133`). Notificação deduplicada por `observedId`. **Ressalva:** R2 (chat/menus não filtrados em `supernaturalHud.ts`). | **Aprovado com R2** |
| **B4 (Som do Ritual — `SOUN 000843`)** (`veil-ritual-fx.js:5, 24-27, 88-93`, `AETHERIUS - SOBRENATURAL.esp`) | `DADOS-HOUSECARL_19-11_ritual.md` §1 confirmou `000843` (`SN_Veu_RitualCharge`) vencedor no ESP, apontando para `073FD9` (`MAGDestructionFireCharge300`) e `SOPM 07E5DC` (`SOMMono01800`, 150–1.800 u). Cada réplica (dono, vizinho presente e vizinho tardio) toca na montagem e interrompe via `Sound.stopInstance` ao encerrar/expirar. Hash do ESP idêntico entre overlay e `AETHERIUS - SOBRENATURAL` (`65bb3aae...`). Ver Observação 1 sobre duração de 3,35 s (`Loop = None`). | **Aprovado** (com Obs. 1) |
| **B5 (Remoção da runa e réplica tardia) & B7 (Feitiços comuns invocando runa)** (`veil-ritual-fx.js:10-97, 152-169`) | Causa raiz confirmada: a versão anterior guardava `Actor` e `Spell` entre frames no estado e na closure, fazendo `stop()` lançar `Invalid _skyrimPlatform_indexInPool` dentro de `try {} catch {}` — nunca executando `removeSpell` nem `MLh_Equipped_Event` após enviar `MLh_SpellAimedConcentrationStart`. houseCARL confirmou `00000F` (`FXSummonMassBodyHolderRune`) sem VMAD/scripts e sem vínculo além de `00083A` e `AbSummonMassCastBodyFX` (62 MGEFs de invocação mestre). A nova versão armazena apenas primitivos (`actorId`, `cell`, `id`, `at`, `sample`, `sound`, `ended`, `retryAt`), resolve `Actor`/`Spell` por FormID a cada frame, remove os dois eventos de animação do grafo, expira réplicas após 2,5 s sem amostra (com limpeza de `seenOnline` no login) e permite remontagem em amostra nova. Ver Observação 2 sobre ordem `removeSpell`/`dispelSpell`. | **Aprovado** (com Obs. 2) |
| **B8 (Sussurros na Sobrecarga)** (`veilEffects.ts`, 4 WAVs em `AETHERIUS - SERVIDOR LOCAL`) | Causa raiz confirmada: os 4 WAVs não haviam sido copiados ao mod ativo. `DADOS-HOUSECARL_19-11_ritual.md` §3 e conferência de SHA-256 confirmam os 4 arquivos (`breathing_witch.wav`, `scream_witch.wav`, `whisper2_witch.wav`, `whispering_witch.wav`) como loose 16-bit/44.1 kHz/mono vencedores no perfil `AETHERIUS - LOCAL`, batendo com os caminhos dos SNDR `00083B..00083E`. Alternância privada em `veilEffects.ts:161-168` preservada (30–90 s inicial, 180–360 s seguintes). | **Aprovado** |
| **B9 (Pisos de habilidade da Bruxa)** (`remoteServer.ts:189-191, 598, 614, 731, 818-819, 853, 921-937, 1249-1263`, `gameplayGrantService.ts:24-36`) | Causa raiz consistente com `server-20261118-222056.stdout.log:187-193`: `begin` foi enviado em `aetherius.gameplay.grants`, mas `ready` nunca chegou, bloqueando `ClassSkillsProjection.publish` (`class-skills.ts:83`). No código anterior, `onGameLoad` completava `placement` com `!!Game.getPlayer()` antes do 3D e um `UpdateAppearance` concorrente invalidava `appearanceIsCurrent` sem assumir a conclusão da tarefa `appearance`. Agora `onGameLoad` aguarda `whenPlayerReady` (3D carregado), `onUpdateAppearanceMessage` assume o `appearanceGrantCycle` (ou aguarda `spawnAfterLoad` ler o `form.appearance` já atualizado) e `GameplaySpawnBarrier` usa `failedTasks: Set<string>` permitindo recuperação de falha transitória antes de `done` sem corromper tarefas já concluídas (`line 28`). `class-skills.ts` confirmado com `30/40/50/60/60/60` em Alteration, Conjuration, Destruction e Illusion via `Math.max(classe, piso)`. Como Sol registrou com honestidade, a confirmação definitiva de que não existe outra causa para a ausência de `ready` depende do reteste em jogo conferindo `begin` + `ready` no log. | **Aprovado** |
| **Sincronização pública (olhos, aura, BRUXA)** (`veil-eyes.js`, `veil-visuals.js`, `teste-veu-mundo.cjs`) | `veil-eyes.js:7, 37` passa a aceitar UUIDs de personagem além de chaves numéricas legadas; `veil-visuals.js:13-50` garante limpeza de propriedade `aetheriusVeilAura` persistida já na primeira leitura de ator sem sobrecarga (`cleared` Set). Testes simulam dono, vizinho presente, vizinho tardio, saída e reentrada conforme decisão do dev (validação com 2 jogadores no servidor oficial pela tabela da ETAPA-4). | **Aprovado** |

---

## 4. Auditoria de Segurança, Vazamento de Keyword e Performance

- **Vazamento de keyword (`JogadorVampiro`/`JogadorLobisomem`/`JogadorBruxa`):** nenhum. Conferidas todas as propriedades e envelopes tocados:
  - `aetheriusBloodPresentation`: `{ v: 1, blood, state }` (`isVisibleByOwner: true, isVisibleByNeighbors: false`)
  - `aetheriusVeilPresentation`: `{ v: 1, bond, stage, overload, overloadBlocks }` (`isVisibleByOwner: true, isVisibleByNeighbors: false`)
  - `aetheriusVeilRitualStatus`: `{ v: 1, active, result }` (`isVisibleByOwner: true, isVisibleByNeighbors: false`)
  - `aetheriusVeilRitualFx`: `{ id, sample } | null` (`isVisibleByOwner: true, isVisibleByNeighbors: true`)
  - `aetheriusVeilAura`: `{ overload: true, sample } | null` (`isVisibleByOwner: true, isVisibleByNeighbors: true`)
- **Autoridade no servidor e crédito zero:** validado em `veil-ritual.js:96-183`. O cliente não decide conclusão, crédito nem consumo; `ritual.cancel` sempre chama `finish(..., 'interrupted', 'cancelled', 0, ...)` e um cancelamento/interrupção durante `await consume()` aborta o crédito em `veil-ritual.js:120-133`.
- **Performance da HUD:**
  - `supernaturalHud.ts:114-132`: sai imediatamente em ~92% dos frames (`< 200 ms`); quando estável, envia no máximo 1 pacote/s (heartbeat necessário para os timeouts de segurança de 3,5 s); para jogadores sem maldição/ritual/blackout (`empty && key === lastPayload`), envia **0 pacotes/s**.
  - `supernatural-hud.js:10-32`: `setTimeout(showRitual, 250)` roda a 4 Hz somente durante ritual ativo e encerra imediatamente ao fim.
  - Paridade `source/server/gamemode/supernatural/*` vs `overlays/sobrenatural/files/server/gamemode/supernatural/*`: 6/6 arquivos editados com SHA-256 idêntico.

---

## 5. Auditoria de Isolamento do Aetherius UI (`DECISOES.md`)

Regra de compilação final (`DECISOES.md`, 18/11/2026): nada fora do módulo `supernatural` além da integração mínima (registro do módulo e ponto de montagem da HUD própria).

### Arquivos tocados fora de `client/src/sobrenatural/` e `client/ui-core/modules/supernatural/` no commit `61d7547`

| Arquivo | Diff | Classificação per `DECISOES.md` |
|---|---|---|
| `client/ui-core/frontend/hud.html` | `+8` (3 `<link>` + 5 `<script>` apontando para `./modules/supernatural/*`) | **Integração mínima permitida** (ponto de montagem da HUD própria). |
| `client/ui-core/frontend/hud.js` | `+1` (`if (payload.state.supernatural) return;`) | **Integração mínima permitida** (evita que a HUD de necessidades sobrescreva/processe o envelope sobrenatural). |
| `client/src/services/services/gamemodeEventSourceService.ts` | `+13 / -1` | Núcleo SkyMP (não é UI Core): correção de bug do serializador `CustomEventMessage`. |
| `client/src/services/services/gameplayGrantService.ts` | `+9 / -2` | Núcleo SkyMP (não é UI Core): correção de bug da barreira `GameplaySpawnBarrier`. |
| `client/src/services/services/remoteServer.ts` | `+33 / -16` | Núcleo SkyMP (não é UI Core): correção de bug do ciclo de spawn `loadGame`/`UpdateAppearance`. |

### Inventário acumulado em `client/ui-core/` e `client/index.html` desde a base `bc305e5`

- `client/ui-core/modules.json` (`+16`): apenas o registro do módulo `supernatural` (slot 8) — **permitido**.
- `client/ui-core/frontend/hud.html` (`+8`) e `client/ui-core/frontend/hud.js` (`+1`) — **integração mínima permitida**.
- **Nenhum** arquivo de `shell.js`, `styles.css`, menu radial ou outros módulos do UI Core foi alterado contra a base `bc305e5`.
- **Atenção para a compilação final (herdado de lotes anteriores, não introduzido em `61d7547`):**
  - `client/index.html` ainda contém `+44` linhas (`#blood-reserve-hud` inline + links/scripts do selo para a view legada `sp.browser`, ainda testadas por `teste-sangue-hud.cjs:16`). Como a HUD agora vive em `hud.html`, avaliar remover esse bloco legado na compilação final para não tocar o `index.html` de compatibilidade.
  - Permanece pendente para a compilação final a auditoria já registrada em `ETAPA-4:380` da classe `.shell.is-supernatural-workspace` (`supernatural-module.js:50`).

---

## 6. Saída das Baterias de Testes Executadas na Revisão

Todas executadas em `C:\Code\Aetherius - Servidor Local`, sem alterar código nem fazer deploy no MO2/runtime:

| Bateria / Verificação | Comando / Escopo | Resultado |
|---|---|---|
| **Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`)** | `node --test overlays/sobrenatural/teste-*.cjs` (exceto serviço/F7/revisão F7) | **335/335** (`fail 0`, exit `0`) |
| **Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`)** | Idem | **335/335** (`fail 0`, exit `0`) |
| **Survival / Magia (`false`)** | `survival-service.test.js` (80) + `magic.test.cjs` (22) | **102/102** (`fail 0`, exit `0`); com `survival-effects.test.js` (14): **116/116** |
| **Survival / Magia (`true`)** | Idem | **102/102** (`fail 0`, exit `0`); com `survival-effects.test.js` (14): **116/116** |
| **Core (skills/delivery)** | `node --experimental-strip-types --test class-skills-delivery.test.mjs client-delivery.test.mjs` | **13/13** (`fail 0`, exit `0`) |
| **Serviço Sobrenatural** | `node overlays/sobrenatural/teste-servico.cjs "...\source\server\gamemode"` | `OK: serviço, restore throw/noop/reinício, falha de persistência, witch-grants e neutralização SSP` (exit `0`) |
| **Painel F7** | `node overlays/sobrenatural/teste-painel-f7.cjs` | `OK F7: ...` (exit `0`) |
| **Revisão F7** | `node overlays/sobrenatural/teste-revisao-f7.cjs` | `OK revisão F7: ...` (exit `0`) |
| **Patch 030 reverse-check** | `git -C client-source apply --check --reverse ...\030-hud-ritual-spawn.patch` | exit `0` |
| **TypeScript cliente** | `npx tsc --noEmit` em `client-source\client` | exit `0` |
| **TypeScript servidor** | `npx tsc --noEmit` em `source\server\ts` | exit `0` |
| **Webpack cliente (sem deploy)** | `npx webpack` em `client-source\client` | `webpack 5.94.0 compiled successfully` (exit `0`), `skymp5-client.js` SHA-256 `3F6BC76753024576E3A2B73CEDC4C3B2FA16B431FD4450CAA5080B77506CF78B` (idêntico ao reportado por Sol) |
| **Sintaxe JS HUD** | `node --check` em `hud.js`, `blood-reserve-hud.js`, `supernatural-hud.js`, `veil-hud.js` | 4/4 exit `0` |
| **Paridade de artefatos** | SHA-256 `source` vs `overlays`, 4 WAVs no mod LOCAL, ESP `000843` no mod SOBRENATURAL, `hud.html`/`hud.js`/`supernatural-hud.js` no `UI CORE (LOCAL)` | Todos idênticos |

---

## Reconfirmação (295f02e)

- **Revisor:** Gemini 4 Neon (reconfirmação independente, somente leitura)
- **Data:** 19/11/2026
- **Commits reconfirmados:**
  - Cliente (`client-source`): `295f02eed3addda81900ba3589784b11f2bf6fc7` (`fix(sobrenatural): preserva fade e ignora movimento com UI focada`)
  - Laboratório: `1887fd0782164741c543f80f70512f69d46b1e1c` (`fix(sobrenatural): integra retorno Neon sobre blackout e movimento`; patch `overlays/cliente-local/031-ritual-foco-blackout.patch`)
  - Documentação: `e07feb30ec1693cb86a2d92fd4ec7ca29efdb2e0` (`docs(sobrenatural): registra correcoes da revisao da HUD`)
- **Fora do escopo desta reconfirmação:** som B4 (aguardando decisão do dev).

### Veredito da Reconfirmação

**APROVADO SEM ACHADOS.** R1, R2 e a aceitação de UUID em `veil-terror.js` estão integralmente corrigidos, isolados dentro do módulo Sobrenatural e cobertos por testes de regressão.

### Verificação item a item

- **R1 (blackout e fade de retorno):** resolvido em `veilEffects.ts:121-149`, `supernaturalHud.ts:36, 65-67, 74, 131-142` e `supernatural-hud.js:43-51`.
  - `SupernaturalHud` preserva o valor nominal (`1000–1500 ms`) mesmo sob atraso do throttle de 200 ms, evitando descarte em `veil-terror-presentation.js:9`.
  - `VeilEffects.clearFx(true)` envia `null` no término natural (`now >= endAt`), e `supernatural-hud.js:44-45` apenas libera `blackout = false` sem chamar `setVeilTerrorBlackout(0)`, permitindo ao CEF concluir integralmente o retorno gradual de 1.200 ms.
  - Interrupções (`clearFx()` → `0`), morte/desconexão (`!sessionId || !alive` → `0`) e `push({})` (`!state`) continuam limpando o overlay imediatamente via `setVeilTerrorBlackout(0)`.
- **R2 (cancelamento por movimento com chat/menus):** resolvido em `supernaturalHud.ts:44-63, 113-116`.
  - `movementBlocked()` verifica `sp.browser?.isFocused()`, `sp.Utility.isInMenuMode()`, `sp.Ui.isMenuOpen("MeridianUI_FocusMenu")` e `sp.Ui.isMenuOpen("Console")` com fallback seguro (`catch { return true; }`).
  - A guarda é aplicada tanto na captura (`queueMovement()` em `onButton`/`onStick`) quanto no envio em `update()`, descartando intenções pendentes se chat/menu abrir entre o evento e o tick. Tecla K (`EMOTE_KEY`) e cancelamento autoritativo no servidor preservados.
- **UUID em `veil-terror.js:11, 32`:** `CHARACTER_KEY` aceita chaves numéricas legadas e UUIDs de personagem, igual a `veil-eyes.js:7`; `source/server/gamemode/supernatural/veil-terror.js` e o espelho em `overlays/` são byte-idênticos (SHA-256 igual).
- **Isolamento do UI Core (`295f02e`):** 3 arquivos alterados, todos estritamente dentro do módulo (`client/src/sobrenatural/supernaturalHud.ts`, `client/src/sobrenatural/veilEffects.ts`, `client/ui-core/modules/supernatural/supernatural-hud.js`). Zero arquivos fora do módulo tocados.
- **Lembrete de deploy (já registrado por Sol em `e07feb3`):** `supernatural-hud.js` no `client-source` (`CADF79B3D099...`) difere da cópia anterior em `AETHERIUS - UI CORE (LOCAL)` (`DAF37E1EE62B...`) — distribuir em par com `skymp5-client.js` ao consolidar para o reteste.

### Testes reexecutados na reconfirmação

| Verificação | Resultado |
|---|---|
| Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) | **343/343** (`fail 0`, exit `0`) |
| Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) | **343/343** (`fail 0`, exit `0`) |
| Patch `031-ritual-foco-blackout.patch` (`git apply --check --reverse`) | exit `0` |
| `node --check supernatural-hud.js` | exit `0` |
| `npx tsc --noEmit` (`client-source\client`) | exit `0` |
| `npx webpack` sem deploy (`client-source\client`) | exit `0`; `skymp5-client.js` SHA-256 `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709` (idêntico ao de `e07feb3`) |
| Paridade `veil-terror.js` (`source` vs `overlays`) | SHA-256 idêntico |

---

## B4 (65edf8c)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura)
- **Data:** 19/11/2026
- **Commits revisados:**
  - Laboratório: `65edf8cb6ea2443ae1f84d1cfec1217cf44bd93c` (`fix(sobrenatural): toca Charge no inicio e na conclusao do Ritual`)
  - Documentação: `5effbd8` (`ETAPA-4_bruxaria-vinculo-do-veu.md`, `diario.md`)
- **Decisão do dev verificada:** tocar o som espacial vanilla (`SOUN 000843:AETHERIUS - SOBRENATURAL.esp` → `SNDR 073FD9:Skyrim.esm`, `3,351 s`, `SOMMono01800`) no **início aceito** e na **conclusão bem-sucedida**, nunca no cancelamento ou falha.

### Veredito de B4

**APROVADO SEM ACHADOS.** Implementação limpa, desacoplada da vida da runa, sem risco de `indexInPool`, sem vazamento de keyword e fiel à decisão do dev.

### Verificação técnica

- **Disparo estritamente autoritativo (`veil-ritual.js:101`):** `rune?.stop(id, active.id, outcome === 'completed')` só sinaliza `completed = true` após `consume()` confirmado, ausência de interrupção e `credit() > 0` (`veil-ritual.js:134-136`). Cancelamentos (`player`/`movement`), combate, morte, desconexão, perda de maldição e falhas (`materialsChanged`/`uncertain`) passam `false`, encerrando o trecho inicial sem disparar o som de conclusão.
- **Separação de propriedades e janela de 3,351 s (`veil-ritual-fx.js:7-9, 174-234`):**
  - `aetheriusVeilRitualCharge` (`isVisibleByOwner: true, isVisibleByNeighbors: true`) publica `[{ id: "<ritualId>:start" | "<ritualId>:complete", remainingMs }]` por até `3.351 ms`, atualizada no `tick()` de 750 ms e limpa por `setTimeout` (`unref()`) ao expirar, ou imediatamente em `stop(id, ritualId, false)` (cancelamento corta `row.start` sem afetar eventual `row.complete` recente) e em desconexão/dispose.
  - A runa (`aetheriusVeilRitualFx`) sai imediatamente em todo fim sem depender do encerramento do áudio de conclusão; a limpeza legada em `receive()` (`veil-ritual-fx.js:41-44`) ainda encerra eventual `active.sound` da versão anterior na transição.
- **Cliente `receiveCharge` (`veil-ritual-fx.js:95-144`):**
  - **Segurança `indexInPool`:** armazena apenas primitivos (`handle`, `until`, `actorId`, strings de `id`) e resolve `sp.Actor.from(sp.Game.getFormEx(actorId))` a cada frame no `check` (no máximo `3,351 s`).
  - **Deduplicação e remount:** `ctx.state.ritualChargeSeen` evita repetição em heartbeats; `sp.storage.aetheriusVeilHeardCharges` (TTL `durationMs * 2`, podado em cada novo disparo) evita repetir o som após sair/reentrar no raio de visão durante a mesma janela; entrada tardia dentro da janela toca uma vez e corta no `remainingMs` projetado; após a janela expirar, `CHARGE_PROPERTY` é `null`.
  - **Performance e privacidade:** caminho rápido O(1) quando ocioso (`veil-ritual-fx.js:98`, sem alocações nem chamadas nativas); payload público contém apenas `{ id, remainingMs }` sem keyword, FormIDs ou identidade de persistência.
  - **Isolamento do UI Core:** nenhum arquivo de cliente ou UI Core alterado neste lote.

### Testes e paridade verificados

| Verificação | Resultado |
|---|---|
| Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) | **345/345** (`fail 0`, exit `0`) |
| Overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) | **345/345** (`fail 0`, exit `0`) |
| Paridade `veil-ritual-fx.js` e `veil-ritual.js` (`source` vs `overlays`) | SHA-256 idêntico em ambos |
