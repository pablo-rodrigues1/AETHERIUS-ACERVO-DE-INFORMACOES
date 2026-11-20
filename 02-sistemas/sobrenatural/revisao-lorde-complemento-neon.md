# Revisão Independente — Complemento do Lorde Vampiro (Garra, Ala de Sangue, Asas e Sons da Transformação)

- **Revisor:** Gemini 4 Neon (revisão cruzada honesta, somente leitura no código — regra de `AGENTS.md`, 18/11/2026)
- **Data:** 20/11/2026
- **Autor revisado:** Sol High
- **Alvo revisado:**
  - `C:\Code\Aetherius - Servidor Local` — commit `bb2510a27dfb9ed318ac7b32da0351d3b296add7` (`bb2510a`): `feat(sobrenatural): sincroniza Garra, Ala, Asas e sons do Lorde`
  - `C:\Code\Aetherius - Servidor Local\client-source` — commit `8e4b7585fdbb96aab6b0777cf021dcf0906b8b16` (`8e4b758`): `feat(sobrenatural): sincroniza concentracao e voo do Lorde`
  - Patches verificados contra o source aplicado:
    - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\033-lord-concentration.patch`
    - `C:\Code\Aetherius - Servidor Local\overlays\cliente-local\037-lord-concentration-flight.patch`
  - Espelho verificado:
    - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\vampire-lord-service.js`
    - `C:\Code\Aetherius - Servidor Local\source\server\gamemode\vampire-lord-service.js`
    - SHA-256 idêntico em ambos: `956790106AF4A0120538BE30ADFA72AB2DF54B9B9A259A525700056EF0A5FC11`
  - ESP candidato: `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\plugin\AETHERIUS - SOBRENATURAL.esp`
    - SHA-256 conferido: `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`
- **Fontes consultadas:**
  - [INVESTIGACAO_20-11_lorde-poderes.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_20-11_lorde-poderes.md#L72-L111)
  - Código-fonte do servidor (`ActionListener.cpp`, `ActionListener.h`, `PartOne.cpp`, `MpActor.cpp`, `MpObjectReference.cpp`, `vampire-lord-service.js`, `magic-service.js`, `magic/healing.js`, `skysocial/src/index.js`, `unit/HitTest.cpp`)
  - Código-fonte do cliente (`magicSyncService.ts`, `hitService.ts`, `lordFlight.ts`, `index.ts`, `remoteServer.ts`, `formView.ts`, `movementApply.ts`, `ragdollService.ts`, `gamemodeUpdateService.ts`, `spellReplayTarget.ts`, `worldViewMisc.ts`)
  - SkyrimPlatform (`EventHandler.cpp`, `MagicApi.cpp`, `skyrimPlatform.ts`) e scripts extraídos do COTV em `Sobrenatural/research/cotv-extracted/`
- **Limite explícito de ferramenta:** este revisor não tem acesso ao **houseCARL** (`AGENTS.md`, 18/11/2026). Todos os dados binários de records do ESP `7A1397FE…` e dos plugins do modpack ficam registrados na seção **Pendências de conferência de records (houseCARL)**.

---

## Veredito

**APROVADO COM RESSALVAS (0 Bloqueadores, 0 Altos, 1 Médio, 1 Baixo).**

A arquitetura do complemento está bem delimitada, segura contra manipulação do cliente e com todos os testes (Node em ambos os modos, Catch2 com o ESP candidato, `tsc` de servidor e cliente e `git apply --reverse --check`) passando. Não foram encontrados crashes, vazamentos de autoridade em PvP (jogadores online e offline são estritamente imunes à Garra), acúmulo indevido na Ala de Sangue nem regressões no serviço de Lorde ou no fluxo de queda/gravidade.

A inspeção linha a linha apontou **2 defeitos reais verificáveis em código** (nenhum bloqueador de segurança/estabilidade, mas recomendados para correção antes do teste multi-jogador em jogo):

1. **[Médio — sincronização espacial da Garra — `ActionListener.cpp:285-297,2166-2180` + `remoteServer.ts:359-431` + `formView.ts:495-507`]** Enquanto o NPC está preso na Garra, `OnUpdateMovement` descarta `UpdateMovementMessage` e `PullWithGrip` posiciona o NPC via `TeleportMessage` indexado somente enquanto `distance > 112 u`. Porém `onTeleportMessage` (`remoteServer.ts:420-430`) **não atualiza `receivedModel.movement.pos`**. Após 2 s sem `UpdateMovementMessage`, `formView.ts:498` reaplica `applyMovement(refr, model.movement)` com a posição pré-Garra congelada e `translateTo` arrasta o NPC visualmente de volta ao ponto inicial em todo cliente não-hoster (observadores e o próprio Lorde quando outro jogador hospeda o NPC) — e, como `PullWithGrip` para de enviar `TeleportMessage` ao atingir `≤ 112 u`, o NPC permanece visualmente longe enquanto o servidor continua drenando 5 HP/s.
2. **[Baixo — margem de jitter na renovação de lease — `ActionListener.cpp:44,1213-1214` + `magicSyncService.ts:78,125-129`]** O lease dura `750 ms`, o cliente envia `_aetheriusLordSustain` a cada `250 ms` em canal **`"unreliable"`** e o servidor exige `expires <= now + 500ms` (intervalo mínimo exato de `250 ms`). Qualquer jitter negativo de ≥ 1 ms entre dois pacotes consecutivos descarta a renovação silenciosamente; somado a uma única perda UDP (ou stutter > 250 ms), o lease expira no host sem aviso ao dono, deixando o cliente exibindo a Ala de Sangue/Garra ativa enquanto o servidor já encerrou a proteção/puxada.

---

## 1. Achados Reais (Com Evidência)

### Achado 1 (Médio) — Garra Vampírica: `onTeleportMessage` indexado não atualiza `receivedModel.movement.pos`, causando retorno visual do NPC à posição pré-Garra após 2 s nos não-hosters

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:285-297` (`OnUpdateMovement`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:2166-2180` (`PullWithGrip`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\MpObjectReference.cpp:551-562` (`SetPos`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\remoteServer.ts:359-431` (`onTeleportMessage`) e `:864-881` (`onUpdateMovementMessage`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\view\formView.ts:481-507` (`applyAll`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\sync\movementApply.ts:155-205` (`translateTo`)
- **Evidência verificável:**
  1. Em `ActionListener.cpp:285-297`, enquanto `HoldsGripTarget(*target)` retorna `true`, `OnUpdateMovement` retorna antes de `SendToNeighbours(msg.idx, rawMsgData)`. Nenhum `UpdateMovementMessage` é distribuído durante toda a concentração.
  2. Em `ActionListener.cpp:2166-2180`, `PullWithGrip` chama `target.SetPos(pos, SetPosMode::CalledByUpdateMovement)` (que atualiza `ChangeForm().position` sem emitir `UpdateMovementMessage`) e envia `TeleportMessage` com `pull.idx = target.GetIdx()` apenas enquanto `distance > kGripStop` (`112.f`).
  3. No cliente, o único ponto que atualiza `form.movement` é `onUpdateMovementMessage` (`remoteServer.ts:876`). No ramo indexado de `onTeleportMessage` (`remoteServer.ts:407-431`), após `TESModPlatform.moveRefrToPosition(...)` (linhas 420–422), a função retorna em `430` sem atualizar `receivedModel.movement.pos` nem `receivedModel.numMovementChanges`.
  4. Em `formView.ts:495-507`, em qualquer cliente com `model.isHostedByOther === true` (todos os observadores e também o próprio Lorde Vampiro caso outro jogador seja o hoster do NPC):
     ```ts
     if (
       +(model.numMovementChanges as number) !==
       this.movState.lastNumChanges ||
       Date.now() - this.movState.lastApply > 2000
     ) {
       this.movState.lastApply = Date.now();
       if (model.isHostedByOther || !this.movState.everApplied) {
         ...
         applyMovement(refr, model.movement, !!model.isMyClone);
     ```
     Como `numMovementChanges` fica congelado durante a Garra, aos 2 000 ms o ramo `Date.now() - this.movState.lastApply > 2000` dispara `applyMovement` com a posição pré-Garra ainda em `model.movement.pos`. Em `movementApply.ts:184-203`, `getDistanceNoZ(refrRealPos, gTempTargetPos) > 8` aciona `refr.translateTo(...)` em `0.2 s` de volta ao ponto pré-Garra.
  5. Do alcance máximo (`600 u`), a `80 u / 250 ms`, o NPC chega a `≤ 112 u` em `≤ 1,75 s`. A partir desse instante `distance > kGripStop` passa a ser `false` e o servidor para de emitir `TeleportMessage`, mantendo o dreno de 5 HP/s enquanto o jogador segura o botão. Aos `2,0 s`, `formView.ts:498` puxa o NPC visualmente de volta à posição pré-Garra (até 488 u de distância) em todos os não-hosters, onde ele permanece enquanto o servidor continua drenando vida como se estivesse junto ao Lorde. (Além disso, a partir de `1500 ms`, `formView.ts:483-490` passa a disparar `tryHostIfNeed` a cada 1 s nos não-hosters, rejeitado pelo servidor enquanto o hoster continuar enviando pacotes de movimento.)
- **Correção sugerida:** no ramo `idx !== undefined` de `onTeleportMessage` (`remoteServer.ts:420-425`), após `moveRefrToPosition`, atualizar `receivedModel.movement.pos`, `receivedModel.movement.rot`, `receivedModel.movement.worldOrCell` e incrementar `receivedModel.numMovementChanges` (ou manter o `movement` do modelo sincronizado quando o teleporte indexado é aplicado). Assim `FormView` converge para a posição autoritativa do passo tanto durante quanto após o fim da puxada.

---

### Achado 2 (Baixo) — Renovação de `_aetheriusLordSustain` no limiar exato (`750 ms − 500 ms = 250 ms`) sobre canal `"unreliable"` descarta pacotes com jitter negativo e encerra o lease sem aviso ao dono

- **Onde:**
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:44` (`kLordLease = 750ms`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:1204-1216` (`_aetheriusLordSustain`)
  - `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\ActionListener.cpp:2002-2019` (`RelayLordCast`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\magicSyncService.ts:78,118-130` (`updateLordCasts`)
  - `C:\Code\Aetherius - Servidor Local\source\unit\HitTest.cpp:552-555` (`sleep_for(255ms)`)
- **Evidência verificável:**
  1. `OnLordCast` fixa `expires = now + 750ms` (`ActionListener.cpp:2089`).
  2. `OnCustomEvent` só renova `_aetheriusLordSustain` quando `it->second.expires <= now + 500ms` (`ActionListener.cpp:1213`), ou seja, exige intervalo real de servidor `≥ 250 ms` desde o `SpellCast` ou desde a renovação anterior.
  3. O cliente envia `_aetheriusLordSustain` a cada `250 ms` (`sampleAt = now + 250` em `magicSyncService.ts:78,125`) com `reliability: "unreliable"` (`:128`), enquanto o `SpellCast` inicial usa `reliability: "reliable"` (`:96`).
  4. Como o piso de aceitação no servidor (`250 ms`) é **idêntico** ao período de envio do cliente (`250 ms`), qualquer variação negativa de latência de ≥ 1 ms entre dois pacotes consecutivos (ex.: `SpellCast` confiável ou renovação `k` atrasou +10 ms e a renovação `k+1` chegou sem atraso → intervalo de servidor `240 ms < 250 ms`) faz `expires <= now + 500ms` retornar `false` e descartar a renovação. Sob perda de 1 único pacote UDP `"unreliable"` (ou travadinha > 250 ms) logo após um descarte por jitter, o lease vence aos 750 ms.
  5. Quando o lease vence no host, `StopLordCast` só envia `interruptCast` para `cast.viewers` (que exclui `cast.user` em `ActionListener.cpp:2016`). No cliente do dono, `this.lordCasts` continua ativo enviando `_aetheriusLordSustain` (silenciosamente ignorado porque `it == lordCasts.end()`), de modo que a Ala de Sangue permanece visualmente ativa e consumindo magicka no cliente sem absorver dano no servidor até o jogador soltar e reconjurar.
- **Correção sugerida:** abrir margem para jitter no rate-limit do servidor — ex.: `expires <= now + std::chrono::milliseconds(600)` (aceita renovação após ≥ 150 ms em vez de ≥ 250 ms, preservando proteção anti-flood) — e/ou enviar `_aetheriusLordSustain` com `reliability: "reliable"` (4 pacotes/s somente durante concentração ativa).

---

## 2. Observações Opcionais (Não-Bloqueantes)

1. **`HitService.onBloodWardImpact` (`hitService.ts:18-28`) vs `WardHitStatus.Broken` (`EventHandler.cpp:831-853`) — conferir no teste em jogo:** `EventHandler::ProcessEvent(TESMagicWardHitEvent)` despacha `wardHit` de forma assíncrona via `AddUpdateTask` (próximo tick de update). Quando um feitiço forte **quebra** a barreira (`WardHitStatus.Broken`), caso o motor já tenha dissipado o `ActiveMagicEffect` `0x47179D` antes da execução da task no próximo frame (e não emita `hit` paralelo), `hasMagicEffect(effect)` em `hitService.ts:21` retornaria `false` e o impacto que quebrou a barreira não chegaria ao host. Como depende do ordenamento interno do motor em tempo real, registrar explicitamente no roteiro de teste em jogo (disparar feitiço > 100 de dano contra a Ala de Sangue e conferir se o excesso chega a `OnSpellHit`).
2. **Alinhamento de 250 ms na troca de faixa do áudio de transformação (`vampire-lord-service.js:17-22,182-184,322-328`):** `publishAudio` roda no `setInterval(tick, 250)` sem alinhamento com `audioAt`. Assim, a entrada das faixas `0x864` e `0x865` (previstas para `1761 ms`) ocorre no primeiro tick `≥ 1761 ms` (lacuna de 0–250 ms após `0x863`), e `track.until = now + remaining` corta até `250 ms` do fim de `0x864`/`0x865` mesmo para ouvintes presentes desde `t = 0`. Avaliar na escuta em jogo se o corte de cauda é perceptível; se for, agendar um `setTimeout` pontual para `1761 ms` ou não aplicar corte de cauda quando `age - cue.start < 250`.
3. **Higiene defensiva em `ActionListener.cpp:1728-1729`:** `isGripCloak` compara `mapping.GetEspmFileIndex(hitData.source) == mapping.GetEspmFileIndexByFilename("Dawnguard.esm")` sem testar `!= PluginIndexMapping::kInvalidFileIndex` (ao contrário de `IsCotvSpell` em `:58`). Sem impacto em produção (onde `Dawnguard.esm` sempre está carregado), apenas consistência para ambientes sintéticos sem Dawnguard.

---

## 3. Pontos Conferidos e Aprovados

- **Vampiric Grip — imunidade de jogadores e validações server-side (`ActionListener.cpp:53-92,1727-1731,1993-2141,2157-2196`):**
  - Jogadores online e offline (`IsCreatedAsPlayer() || GetUserId() != InvalidUserId`) são bloqueados em `GripCanReach`, `HoldsGripTarget` e nunca entram no replay visual (`cast.target = owner.GetFormId()`).
  - Bloqueio do cloak `00E7DA:Dawnguard.esm` (para jogadores) e de `33EAD6:COTV` em `OnHit` impede dano duplicado vindo do cliente.
  - `IsCotvSpell` resolve origem via `PluginIndexMapping::GetEspmFileIndex` (seguro com ESLs intercalados), valida sessão `lord`, raça `DLC1VampireBeastRace`, `IsSpellLearned`, mão equipada, célula/mundo, hemisfério frontal (`GetViewDirection()` = `{sin(z), cos(z), 0}`), alcance `≤ 600 u`, desnível `≤ 96 u`, `SkySocial`/`SkyDeath` e exclusividade do alvo em `gripTargets` (inclusive limpeza de claim expirado em `:2072-2075`).
  - `TickLordCasts` avança o iterador antes de `StopLordCast(key)` (`std::map` iterator-safe), e `PartOne.cpp:181,466` limpa os casts em troca de ator (`SetUserActor`) e desconexão (`ServerSideUserDisconnect`).
  - `onSkySocialSpellCast` em `magic-service.js:137-154` e `skysocial/src/index.js:41-45` não intercepta nem reinterpreta Garra ou Ala de Sangue como cura/invocação.
- **Ala de Sangue — absorção no host (`ActionListener.cpp:2143-2155,2373`):**
  - `ApplyBloodWard` atua exclusivamente em `OnSpellHit` (cobre `SPEL`, `SCRL` e `ENCH`), subtrai até `100.f` uma única vez por impacto mesmo com duas mãos equipadas, não toca `OnWeaponHit` nem `OnChangeValues`, e quando reduz dano a `0` `ApplySpellHealthAbsorb` não credita cura indevida.
- **Asas da Noite e imunidade a queda (`lordFlight.ts:1-75`, `index.ts:71,166`):**
  - Serviço registrado em `client/src/index.ts`, usa evento válido `loadGame` (`EventHandler.cpp:745`, `skyrimPlatform.ts:593`), lê `world.forms[world.playerCharacterFormIdx].aetheriusVampireLord` (gravado em `remoteServer.ts:987`) e valida `connected`, fase `lord`, `!model.isDead`, raça `0x283a:Dawnguard.esm` e `!player.isDead()`.
  - Escreve `2` em `015FC8:Dawnguard.esm` somente com `isLevitating === true`, `1` em terra e `0` ao sair da forma, morrer, desconectar ou em `loadGame`.
  - Gravidade (`fInAirFallingCharGravityMult:Havok`) só muda com `isSprinting()` + perk `025B1A:COTV`, capturando e restaurando o valor anterior (`before`) com guarda contra sobrescrita externa (`Math.abs(current - applied) < 1e-5`). Sem conflito com `COTV_VampireBatFormFXScript`/`COTV_VampireMistFormFXScript`, que não executam sob `blockPapyrusEvents(true)` + spells de apresentação sem script.
- **Sons da transformação (`vampire-lord-service.js:16-70,174-201,227-232,254,274,283-285,323`):**
  - Dono (`0x14`) e ouvintes vivos na mesma célula/mundo até `3000 u` recebem os cues; `seen` em `sp.storage` com chave única `id:at:seq:cueId` evita duplicação em updates repetidos e respawn de `FormView`; `watch()` chama `stopInstance` se o ator perder 3D, morrer ou expirar `track.until`; `finish()` e `recover()` limpam `SOUND_STATE = null` sem `TypeError` no cliente.

---

## 4. Verificações Executadas Pelo Revisor

| Verificação | Resultado |
|---|---|
| `git apply --reverse --check overlays/sobrenatural/033-lord-concentration.patch` (em `source`) | **OK** |
| `git apply --reverse --check overlays/cliente-local/037-lord-concentration-flight.patch` (em `client-source`) | **OK** |
| Espelho `vampire-lord-service.js` (`overlays/...` vs `source/...`) | **Idêntico** (`95679010…A5FC11`) |
| ESP candidato `AETHERIUS - SOBRENATURAL.esp` SHA-256 | **Confirmado** (`7A1397FE…2CE110`) |
| `npx tsc --noEmit` em `client-source/client` | **OK** (exit 0) |
| `npx tsc --noEmit` em `source/server` | **OK** (exit 0) |
| Catch2 Release (`unit.exe`, `AETHERIUS_LORD_TEST_ESP` apontando ao ESP, filtros `[VampireStrength],[VampireLordStats],[VampireLordConcentration],[BatDash]`) | **OK** — 71 asserções em 4 casos, 0 falhas |
| Node `node --test` (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) — 26 suítes `overlays/sobrenatural/teste-*.cjs` + `werewolf.test.cjs` + `survival-service.test.js` + `skysocial-service.test.js` + `magic.test.cjs` | **OK** — 514/514 passaram |
| Node `node --test` (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) — mesma bateria | **OK** — 514/514 passaram |
| Node `node --test` (`teste-lorde-mecanicas.cjs`, `teste-lorde.cjs`, `records-ctda.test.cjs`, `summon-friends.test.cjs`, ambos os modos) | **OK** — 12/12 em cada modo |
| Scripts standalone (`teste-servico.cjs`, `teste-painel-f7.cjs`, `teste-revisao-f7.cjs`, em `false` e `true`) | **OK** (exit 0 em todos) |

---

## 5. Pendências de Conferência de Records (Sem houseCARL) e Teste em Jogo

Como este modelo não possui acesso ao **houseCARL**, os pontos binários abaixo permanecem pendentes de auditoria independente via houseCARL (ou já conferidos apenas pelo autor):

1. ESP `AETHERIUS - SOBRENATURAL.esp` (`7A1397FE…`):
   - Override de `33EAD6:COTV` (`COTV_VampiricGrip`): remoção de `007EBD:Dawnguard.esm` (`GrabActor`) e do manto `00E7D7`, troca pelo MGEF visual `000862` (arquétipo Script sem VMAD), preservando arte/sons e `ETYP LeftHand`.
   - Override de `023375:COTV`: remoção do VMAD (`COTV_WingsOfTheNightScript`) sem quebrar condições/efeitos de `0FFAC0:COTV`.
   - Silenciamento dos SNDRs vanilla `005050`, `005051`, `005052:Dawnguard.esm` (overrides sem `ANAM`) e criação dos pares SOUN/SNDR `000863`/`000866`, `000864`/`000867`, `000865`/`000868` apontando aos WAVs vencedores do mod `AETHERIUS - Vampire Lord and Werewolf SFX`.
2. Records do COTV referenciados em código: `473F48` / `47179D` (Ala de Sangue, magnitude 100), `025B1A` / `025B1E` (perk e global de gravidade `0,005`), `33EB18` (`ModFallingDamage × 0` condicionado exclusivamente a `015FC8:Dawnguard.esm == 2`).
3. **Homologação em jogo:** colisão/escadas/IA do NPC sob Garra (e visualização por não-hoster após correção do Achado 1), quebra de Ala de Sangue com feitiço > 100 de dano (`WardHitStatus.Broken`), planar com Asas e dano de queda em terra vs levitação, e sincronia/volume real das 3 faixas de transformação para dono e observadores próximos/tardios.


## Resposta do autor — 20/11

1. **Aceito, posição replicada da Garra.** Em `remoteServer.ts`, o teleporte indexado atualiza posição, rotação, célula/mundo e `numMovementChanges` do modelo após a chamada nativa. Quando o ator ainda não existe localmente, a posição fica disponível para o spawn do `FormView`. O caminho assíncrono exige a mesma identidade do modelo e o último teleporte recebido, evitando que duas conclusões de ragdoll fora de ordem restaurem um passo antigo. O movimento normal volta a substituir o estado depois da Garra. O teste de cliente aplica dois passos, completa as chamadas na ordem inversa e verifica que o NPC fica na posição do último até chegar outra atualização normal.
2. **Aceito, renovação e encerramento.** O lease passou a 1 s, com piso de 150 ms na renovação. A amostra permanece a cada 250 ms e chega pelo canal confiável ordenado, junto da abertura e da liberação. O host continua conferindo o dono, a sessão, o slot e a magia. O identificador de cast enviado com a amostra correlaciona o aviso confiável de fim ao ator, à mão e à conjuração. O host avisa se encerra um cast conhecido ou quando recebe renovação sem lease válido. No cliente o aviso correspondente interrompe a concentração no motor e retira a renovação. Avisos de outro ator ou de uma conjuração anterior são descartados mesmo se a reabertura ocorrer antes do callback de update. O protocolo anterior de sustain tinha dois argumentos. Usar os patches 034 e 038 juntos.

O teste nativo renova com intervalo de 180 ms, atravessa o prazo original, confirma a expiração posterior e verifica entrega confiável ao dono. Também cobre saída após a renovação de um cast recusado. Os casos de Força, atributos, Garra/Ala e Morcego somaram **81 asserções em quatro casos**. Node 22.14.0 passou **530/530 por modo**. TypeScript do cliente e do servidor, webpack, Build-Native, carregamento em Node e reverse-check dos patches 034/038 concluídos.

Artefatos do candidato no laboratório `C:\Code\Aetherius - Servidor Local`. Cliente `ae7c172`, bundle `530F8AE10E78CFF38485541E14FB730C694448F8980999A7F313485F07F77831`. Addon `2A4E677911BE738A8EDE8563E05AB79AE0DF79B2B4341123167E6B4B2BC3DF37`. ESP no overlay com SHA-256 `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`, idêntico ao candidato auditado.

A reconfirmação independente e a homologação em jogo permanecem pendentes. O reteste do Ward deve incluir quebra com magia acima de 100 e comparar efeito visual com `OnSpellHit`. A troca de faixa e o volume real do áudio também ficam para a escuta em jogo.

---

## Reconfirmação Independente — 20/11/2026 (Gemini 4 Neon)

- **Alvo reconfirmado:**
  - `C:\Code\Aetherius - Servidor Local` — commit `d830ca7ae2e2f0b62e705a386c9b894a1c0ef3fd` (`d830ca7`): `fix(sobrenatural): mantém posição da Garra e sincroniza fim da concentração` (`overlays/sobrenatural/034-lord-lease-owner.patch`, `overlays/cliente-local/038-lord-teleport-and-cast-end.patch`, `overlays/sobrenatural/teste-lorde-mecanicas.cjs`)
  - `C:\Code\Aetherius - Servidor Local\client-source` — commit `ae7c1720bcb45912bd31b1885785e246cb25aebe` (`ae7c172`): `fix(sobrenatural): ancora teleporte e encerra concentração no dono` (`client/src/services/services/remoteServer.ts`, `client/src/services/services/magicSyncService.ts`)
  - ESP candidato inalterado: `AETHERIUS - SOBRENATURAL.esp` — SHA-256 confirmado `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110`
- **Veredito final:** **APROVADO SEM ACHADOS PENDENTES (escopo de código).** Os 2 defeitos da revisão inicial foram corrigidos de forma limpa, sem regressão nos testes e sem novos defeitos identificados no delta.

### Reconfirmação por achado

1. **Achado 1 (Médio) — Posição replicada da Garra em `remoteServer.ts:163,374-440` (`038-lord-teleport-and-cast-end.patch`):** **Resolvido sem regressão.**
   - `latestIndexedTeleport` (`WeakMap<FormModel, object>`) ancora cada `FormModel` ao último `TeleportMessage` indexado recebido, e `sameModel()` exige identidade do `FormModel` e igualdade com esse último teleporte — descartando callbacks assíncronos de `safeRemoveRagdollFromWorld` que concluam fora de ordem.
   - `updateMovementAnchor()` atualiza `receivedModel.movement` (`pos`, `rot`, `worldOrCell`, preservando os demais campos por spread) e incrementa `receivedModel.numMovementChanges` tanto após `TESModPlatform.moveRefrToPosition(...)` (`:439`) quanto no ramo `idx !== undefined && !refr` (`:404`, para `FormView` que nasça depois). Assim, o `FormView` em clientes não-hosters mantém o NPC ancorado na última posição autoritativa da Garra mesmo após atingir `≤ 112 u` e encerrar o envio de `TeleportMessage`, voltando a receber o movimento normal do hoster após a liberação.
2. **Achado 2 (Baixo) — Lease de concentração, margem de renovação e aviso correlacionado ao dono (`ActionListener.cpp:17-27,1216-1248,2046-2130`, `ActionListener.h:109,127-129`, `magicSyncService.ts:21,80-108,148-151`):** **Resolvido sem regressão.**
   - No servidor (`034-lord-lease-owner.patch`), `kLordLease` passou para `1 s` e `kLordRenewInterval` para `150 ms` (`now >= cast.expires - kLordLease + kLordRenewInterval`), dando 100 ms de folga abaixo da cadência de 250 ms e 750 ms acima; `ParseLordCastNumber` usa `std::from_chars` com validação estrita de fim de string e faixa `uint32_t`.
   - No cliente (`038-lord-teleport-and-cast-end.patch`), `_aetheriusLordSustain` agora carrega `[hand, spell, castId]` (com `castId` cíclico `1..0xffffffff`, nunca `0`) em canal `reliability: "reliable"`, ordenado com a abertura e liberação de `SpellCast`.
   - `NotifyLordCastEnded` envia `CustomPacket` confiável (`aetheriusLordCastEnded`) ao dono correlacionado por `(actor, spell, hand, castId)` quando o host encerra o cast (`StopLordCast` com `notifyOwner = true`, omitido quando o próprio dono já mandou `interruptCast = true` em `:2087`) ou quando chega renovação sem lease ativo/compatível (cobrindo inclusive rejeição inicial e encerramento antes do primeiro sustain de 250 ms).
   - No cliente, `onLordCastEnded` escuta `customPacketMessage` (confirmado em `events.ts:81` / `networkingService.ts:166`), valida `hand`, `castId`, `spell` e `actor`, revalida `this.lordCasts.get(hand) === cast` no `once('update', ...)` para não interromper reconjuração subsequente e chama `interruptCast(player.getFormID(), hand, getAnimationVariablesFromActor(player.getFormID()))` — compatibilidade nativa conferida em `JsUtils.h:82-88` (`Napi::Uint8Array`) e `MagicApi.cpp:18-37,157-189` (`ExtractUInt8Array` + `FinishCast()`).

### Verificações independentes executadas na reconfirmação

| Verificação | Resultado |
|---|---|
| `git apply --reverse --check overlays/sobrenatural/034-lord-lease-owner.patch` (em `source`) | **OK** (exit 0) |
| `git apply --reverse --check overlays/cliente-local/038-lord-teleport-and-cast-end.patch` (em `client-source`) | **OK** (exit 0) |
| ESP `AETHERIUS - SOBRENATURAL.esp` SHA-256 | **Inalterado** (`7A1397FE…2CE110`) |
| Espelho `vampire-lord-service.js` (`overlays/...` vs `source/...`) | **Idêntico** (`95679010…A5FC11`) |
| `npx tsc --noEmit` em `client-source/client` e `source/server` | **OK** (exit 0 em ambos) |
| Catch2 Release (`unit.exe`, `AETHERIUS_LORD_TEST_ESP` apontando ao ESP, filtros `[VampireStrength],[VampireLordStats],[VampireLordConcentration],[BatDash]`) | **OK** — 81 asserções em 4 casos, 0 falhas |
| Node `node --test` (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) — 26 suítes `overlays/sobrenatural/teste-*.cjs` + `werewolf.test.cjs` + `survival-service.test.js` + `skysocial-service.test.js` + `magic` (`magic`, `records-ctda`, `summon-friends`) | **OK** — 523/523 passaram (`teste-lorde-mecanicas.cjs`: 4/4) |
| Node `node --test` (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) — mesma bateria | **OK** — 523/523 passaram (`teste-lorde-mecanicas.cjs`: 4/4) |
| Scripts standalone (`teste-servico.cjs`, `teste-painel-f7.cjs`, `teste-revisao-f7.cjs`, em `false` e `true`) | **OK** (exit 0 em todos) |

### Pendências fora do escopo de código desta reconfirmação

- Conferência independente dos records binários do ESP `7A1397FE…` via **houseCARL** (fora do alcance deste modelo; itens listados na seção 5 acima).
- Homologação em jogo (Garra com colisão/escadas/IA e visão de não-hoster, quebra da Ala de Sangue com feitiço > 100 de dano / `WardHitStatus.Broken`, planar com Asas + dano de queda em terra vs levitação, e escuta real das 3 faixas de áudio da transformação).
- Nota operacional registrada pelo autor: aplicar os patches `034` (servidor) e `038` (cliente) em conjunto por conta do terceiro argumento (`castId`) em `_aetheriusLordSustain`.
