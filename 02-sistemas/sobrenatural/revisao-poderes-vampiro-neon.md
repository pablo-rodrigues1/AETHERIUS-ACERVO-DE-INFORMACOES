# REVISÃO INDEPENDENTE — Poderes do Vampiro (`415acde`, `827afe4`, `d8e5c94`, `90ec13e`)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura no laboratório e no código)
- **Autor revisado:** Sol High
- **Data:** 18/11/2026
- **Laboratório auditado:** `C:\Code\Aetherius - Servidor Local`
- **Commits sob revisão:**
  - `415acde9cca09c442c43e303d5ee9c44b4b5535b` (`fix(sobrenatural): ativar poderes vampiricos por voz e replicar som de entrada`)
  - `827afe4609521c4e48f105ce91dbf53cda85c27c` (`feat(sobrenatural): sincronizar dash visao e efeitos vampiricos`)
  - `d8e5c94cce4dba7ebaf6ad62e2284cc9932660a7` (`docs(sobrenatural): registrar audio e reteste dos poderes vampiricos`)
  - `90ec13ec62a9a5760999d584845fb96c3e20b957` (`docs(sobrenatural): registrar sincronizacao e validacao futura oficial`)
- **Commits paralelos em `power-input.js` auditados quanto a conflito/regressão:**
  - `74352820b01cf2255c86a69c449bc298e102e1fb`, `77ea350f5688251f614806f11f5ef698d42ef1e2`, `850211ce3c6886e8a90363d15a0dd86bc20162e8` (Opus 5.5 Low)
- **Documentos conferidos:** `AGENTS.md`, `Sobrenatural/RETOMADA.md`, `Sobrenatural/docs/TESTE-JOGO_18-11.md`, `Sobrenatural/docs/DECISOES.md`, `Sobrenatural/docs/ETAPA-3_vampirismo-mecanicas.md` (§ 18/11 V2–V5/V7), `Sobrenatural/docs/investigacoes/INVESTIGACAO_18-11_enxame-readback.md`, `Sobrenatural/docs/REVISAO-L1-V6_neon.md`, `Sobrenatural/research/cotv-extracted/scripts/source/cotv_vampirebatformscript.psc`.
- **Paridade de arquivos (SHA-256):**
  - `powers.js` (`source` e `overlays` idênticos): `0F0F468DCB7F59089A0B5F692718D348D94240FEF0965B09C7A87380E9A83388`
  - `power-input.js` (`source` e `overlays` idênticos): `1C928667EE84C8D016E1886C9E7A937FE95F62185C58D1C794FE7FDBBC9ABB98`
  - `runtime/server/gamemode/supernatural/{powers.js,power-input.js}` ainda estão nas versões pré-rodada (`A1DD0837...` / `A4477752...`) e exigem Build/Prepare antes de teste em jogo.

---

## Veredito

**REPROVADO PARA PRODUÇÃO EM V2 (1 bloqueador crítico: B1 — dash por teleporte sem colisão); APROVADO COM RESSALVAS EM V3, V4, V5, V7 E PARIDADE `power-input.js`.**

Resumo honesto por frente:
- **V2 (Bat Form — dash 128 u):** **BLOQUEADO (B1)** para o servidor oficial. O teleporte cego via `mp.set(id, 'locationalData', ...)` com `Z` fixo e `cooldownMs = 0` permite atravessar portas/paredes/grades/zonas seguras, derrubar o jogador sob o piso em rampas/escadas e sofre overwrite por pacotes `UpdateMovementMessage` em trânsito. Propostas concretas de mitigação detalhadas em B1.
- **V3 (Mist Form — invisibilidade e visual públicos):** **APROVADO.** Reaplica arte e `setAlpha(0, false)` em dono, vizinhos presentes e entrada tardia, preserva limpeza em perda/recarga de 3D e não replica travas de controle do dono nos vizinhos.
- **V4 (Devouring Swarm — visual, som, dano/dreno):** **APROVADO COM RESSALVA (M1).** Autoridade 100% no servidor, readback conservador de HP corrigido (`415acde`), sem dano duplicado, e gates SkySocial/SkyDeath/mist/invulnerabilidade funcionando. Falta apenas defesa em profundidade contra `core/safe-zones.js` (`blocksBetween`) para quando zonas seguras forem ativadas no oficial.
- **V5 (Vampiric Vision — privada, NPCs + jogadores):** **APROVADO.** Propriedade privada ao dono (`isVisibleByNeighbors: false`), resolução 64-bit `0x100000000 + target` validada contra `PartOne.cpp:807-811` e `worldViewMisc.ts:37-49`, pulsos finitos de `LifeDetectedEnemy` (`0xDC209`) sem conflito com Sede Crítica.
- **V7 (Sons de ativação — 5 SOUN COTV; parcial):** **APROVADO COM RESSALVA (M2).** Broadcast limitado a ouvintes presentes em até 3.000 u, anti-duplicação por `soundSeq` + cache `aetheriusPowerIntros` e `monitorIntro` sem retenção de objetos nativos. Porém `cleanup()` chama `stopIntro()` no fim natural de 1 s do Bat Form, podendo cortar `MAGPowersBatsMarker` antes do fim do áudio.
- **Conflito/regressão em `power-input.js` (`7435282`, `77ea350`, `415acde`, `850211c`):** **APROVADO SEM ACHADOS.** Nenhuma sobreposição, conflito ou regressão; os 6 poderes vampíricos (`!!d.kind`) e as 3 transformações (`!!d.voice`) convivem intactos.
- **Roteiros `d8e5c94` e `90ec13e`:** coerentes com o código e com a decisão de 18/11 (sem teste de 2 jogadores agora; validação física no oficial).

---

## Achados

### [CRÍTICO / BLOQUEADOR — V2] B1 — Dash por teleporte sem varredura de colisão/terreno, risco de exploit e dessincronização por `UpdateMovementMessage` em trânsito

- **Onde:**
  - `source/server/gamemode/supernatural/powers.js:4,167,207-221,249-250,271-272`
  - `source/server/cpp/addon/property_bindings/LocationalDataBinding.cpp:60-64`
  - `source/server/cpp/server_guest_lib/MpActor.cpp:1567-1579,1913-1919`
  - `source/server/cpp/server_guest_lib/ActionListener.cpp:206-255`
  - `source/server/cpp/server_guest_lib/MovementValidation.cpp:99-137`
  - `client-source/client/src/services/services/remoteServer.ts:359-456`
- **O que o código faz:**
  - `batDestination(id)` (`powers.js:207-213`) calcula `[x + sin(rot[2]*pi/180)*128, y + cos(rot[2]*pi/180)*128, z]` (convenção vetorial de `rot[2]` em graus confirmada compatível com `MpActor::GetViewDirection()` em `MpActor.cpp:1915-1919` e `movementGet.ts:81`).
  - `moveBat(id, destination)` (`powers.js:214-221`) escreve `mp.set(id, 'locationalData', destination)`, que chama `MpActor::Teleport` (`LocationalDataBinding.cpp:63`), envia `TeleportMessage` apenas ao dono (`MpActor.cpp:1574`) e, no cliente do dono, chama `TESModPlatform.moveRefrToPosition` (`remoteServer.ts:420-422`) após `ragdollService.safeRemoveRagdollFromWorld` (`remoteServer.ts:450-452`).
- **Defeitos e riscos concretos comprovados no código:**
  1. **Atravessamento de barreiras / exploit de noclip:** `moveRefrToPosition` posiciona diretamente a referência sem varredura física Havok. `128` unidades (≈ 1,83–1,92 m) superam a espessura de portas trancadas, grades de prisão, cercas e paredes internas de edifícios no Skyrim (tipicamente 16–96 u). Somado a `cooldownMs = 0` (`powers.js:167`) e cadência de 1 s (`power-input.js:146`), um vampiro com 100 de sangue pode encadear até **20 teleportes seguidos (2.560 u)** atravessando múltiplas barreiras, celas, portas trancadas e fronteiras de zonas seguras. Além disso, nem `power-input.js:136-153` nem `powers.js:246-250` consultam `core/action-policy.js` (`RESTRAINED` / `IMPRISONED`) ou `core/safe-zones.js`.
  2. **Queda no mundo / clip sob o piso (`Z` fixo):** `powers.js:211` preserva `origin.pos[2]` sem ajuste de terreno/piso. Em qualquer subida (rampa, escada ou encosta de 20°–30° = +46 a +74 u de elevação em 128 u horizontais), o destino cai **abaixo da malha de colisão do chão**, jogando o personagem para fora do mapa/void ou prendendo-o dentro da geometria. Em descida ou borda de precipício, projeta o ator no ar.
  3. **Dessincronização / rubber-band por `UpdateMovementMessage` em trânsito:**
     - `MpActor::Teleport` (`MpActor.cpp:1567-1579`) atualiza `SetPos(position.pos)` no servidor e envia `TeleportMessage` **apenas para o dono** — **sem** setar `teleportFlag` e **sem** número de sequência/janela de supressão.
     - No cliente, `onTeleportMessage` só aplica `moveRefrToPosition` após `once('update', ...)` + callback assíncrono `ragdollService.safeRemoveRagdollFromWorld` (`remoteServer.ts:375,450-452`).
     - Enquanto isso, qualquer `UpdateMovementMessage` que o cliente já tenha enviado com a posição pré-dash (distância de apenas `128 u`, bem abaixo do limite `kSqrMaxDistance = 4096²` de `MovementValidation.cpp:107-112`) é **aceito** em `ActionListener::OnUpdateMovement` (`ActionListener.cpp:226-238`), **retransmitido imediatamente aos vizinhos** (`SendToNeighbours` em `:209`) e **sobrescreve** `actor->SetPos(oldPos)` no servidor (`:246`). O resultado é recuo/flicker nas réplicas dos vizinhos e leitura de posição obsoleta no servidor até o próximo pacote pós-teleporte chegar (além de o ramo `idx !== undefined` em `remoteServer.ts:407-430` não chamar `realignCameraAfterTeleport`, presente só em `:444`).
- **Mitigação concreta proposta (em ordem de preferência):**
  1. **Opção 1 (recomendada — movimento físico com colisão real do motor, sem teleporte cego):**
     - No teste de 18/11 (`TESTE-JOGO_18-11.md:17`), a implementação anterior com `ShoutStart`/`ShoutSprintLongStart` (igual ao `cotv_vampirebatformscript.psc:9-10`) **nunca chegou a rodar** porque o servidor não aceitava o Z (`ETAPA-3:618`).
     - Reavaliar em jogo o deslocamento físico no dono disparado após aceitação do servidor: (a) testar `ShoutStart` + `ShoutSprintLongStart` com aceitação real confirmada, ou (b) aplicar um feitiço/MGEF de impulso estilo *Whirlwind Sprint* (`VoiceSprint` / `0002F7BA:Skyrim.esm`, ou `SpeedMult` temporário + animação de sprint durante o 1 s), executando o deslocamento pelo `hkCharacterProxy` do Skyrim.
     - **Vantagens:** colisão Havok real contra paredes/portas/grades, acompanhamento automático de escadas e rampas (sem queda no mundo), compatibilidade natural com `cooldownMs = 0` (`DECISOES.md:61`), e replicação contínua pelos `UpdateMovementMessage` normais sem race condition de `TeleportMessage`.
  2. **Opção 2 (fallback se o motor de animação/sprint não deslocar em jogo — sweep/raycast de colisão + solo antes do teleporte):**
     - **Não** mover cegamente pelo servidor. Expor via C++/SKSE no cliente (ou ponte nativa) um probe de colisão Havok (`bhkWorld::CastRay` / `CharacterController` sweep horizontal na altura do tronco + raycast vertical para encontrar o piso no ponto final), limitando o avanço à fração livre antes do primeiro obstáculo (com margem de raio do capsule do ator, ex. 24–32 u) e ajustando `Z` ao piso encontrado (recusando o passo se não houver piso válido ou se o desnível vertical exceder um teto seguro).
     - O servidor valida o destino proposto contra distância máxima (`<= BAT_DASH_UNITS`), mesma célula/mundo, direção compatível com o rumo do ator, e aplica o movimento (ou autoriza o deslocamento no cliente dentro da janela de 1 s).
  3. **Defesa em profundidade obrigatória no servidor (independente da Opção 1 ou 2):**
     - Em `power-input.js:136-153` (ou `powers.js:248`), bloquear poderes de deslocamento quando o personagem estiver `RESTRAINED` / `IMPRISONED` (`core/character-state.js` / `core/action-policy.js`) e, caso haja teleporte, impedir cruzamento de fronteira de `core/safe-zones.js` (se origem ou destino cair em zona com `blocks` de `'move'`/`'combat'`).
     - Se `MpActor::Teleport` continuar sendo usado para qualquer habilidade de combate/movimento, adicionar no `MpActor` uma marca/janela curta de descarte de `UpdateMovementMessage` pré-teleporte (ou número de sequência/epoch de teleporte confirmado pelo cliente em `UpdateMovementMessage`) em `ActionListener.cpp:206-255`, evitando overwrite por pacotes em trânsito.

---

### [MÉDIO — V4] M1 — `drain()` do Enxame não consulta `core/safe-zones.js` (`blocksBetween`)

- **Onde:** `source/server/gamemode/supernatural/powers.js:289-293,301-331` vs `source/server/gamemode/core/safe-zones.js:226-234`
- **Evidência:**
  - `powers.js:308-318` valida corretamente `mist(target)`, `aetheriusInvulnerableUntil`, `mp.__skySocial.canDamage(id, target)`, `mp.onSkySocialDamage(target, id, value.child)`, `GetRace`, `excludedKeywords` e `mp.onSkyDeathHit(target, id)`.
  - Porém, uma busca em todo `source/server/gamemode/` confirma que `core/safe-zones.js` (`blocksBetween(actorId, targetActorId, 'combat')`, regra dos dois lados) hoje só é chamado em `core/action-policy.js:159-160` — **não** está ligado a `onSkySocialDamage` nem é chamado por `powers.js:drain`.
  - **Cenário real quando `config/safe-zones.json` for ativado no oficial:** se A e B trocarem um golpe físico fora da zona segura, `hostile` (`powers.js:291,305`) permanece válido por **30 s**. Se A ou B entrar em uma zona segura (ou ficar dentro da borda de 213,333 u), `drain()` continuará aplicando 10 HP/s e curando A mesmo dentro da zona segura.
- **Severidade proporcional:** **MÉDIO** (não bloqueia o laboratório atual porque `safe-zones.json` nasce vazio/desativado, mas precisa ser fechado em `powers.js::drain` ou no hook central de `onSkySocialDamage` antes de ativar zonas seguras no servidor oficial).

---

### [MÉDIO — V7 / V2] M2 — `cleanup()` interrompe o som de ativação (`stopIntro()`) no fim natural de 1 s do Bat Form

- **Onde:** `source/server/gamemode/supernatural/powers.js:19-33,63-70,88,113-118,166`
- **Evidência:**
  - `Bat Form` tem `durationMs = 1000` (`powers.js:166`, `DECISOES.md:61`).
  - Ao tocar `MAGPowersBatsMarker` (`019D82:Dawnguard.esm`), o handle fica em `state.introHandle` e é vigiado por `monitorIntro` por até 5 s (`powers.js:116-117`), que já encerra o handle com `Sound.stopInstance` caso o ator perca o 3D ou a réplica desapareça (`:25-26`).
  - Quando o Bat Form termina naturalmente em `t = 1000 ms` (`!value || now >= state.until`, `:88`), `cleanup()` executa `stopIntro()` (`:69`), chamando `sp.Sound.stopInstance(state.introHandle)` imediatamente — cortando abruptamente o som dos morcegos caso o áudio dure mais de 1 s.
- **Mitigação simples:** manter `stopIntro()` apenas no ramo `!actor.is3DLoaded()` (`:82`) e em `monitorIntro` (`:26`), ou não chamar `stopIntro()` quando o poder termina por expiração natural (`now >= state.until` / `kind === 'bat'`), preservando o corte quando a réplica sai de relevância.

---

### Observações opcionais (baixa severidade — não bloqueiam)

1. **[BAIXO — `powers.js:198` + `ActorNeighborsBinding.cpp:10-26`] Ordem de `.slice(0, 128)` em `nearby()`:**
   - `ActorNeighborsBinding::Get` retorna os `actorNeighbors` a partir de um `std::set<MpActor*>` (ordenado por ponteiro de memória, não por distância). Em `powers.js:198`, `[...new Set(neighbors)].slice(0, 128).filter(...)` corta em 128 **antes** de aplicar `eligible(target)` e distância. Em um evento RP com >128 atores na área de streaming, um alvo hostil em `drain()` (onde `eligible` é um lookup O(1) em `hostile`) ou um jogador ouvinte próximo em `publish()` pode ser descartado antes do filtro. Filtrar por `eligible` (e `online.has` em `listeners`) antes do `.slice(0, 128)` elimina o risco a custo desprezível.
2. **[BAIXO — `powers.js:15-16,36,97,114` vs `ETAPA-3:631-634`] Possível som duplicado em `strength` e `celerity` se o VMAD COTV rodar no oficial:**
   - `powers.js:15-16` reutiliza os próprios SPELs do COTV (`0x33eaca`, `0x33eade`) em `visualLocal`, com o comentário `:36` afirmando *"native AV effects only"*. Porém `ETAPA-3:631-634` registra que seus MGEFs vencedores (`16CD6F`, `107950`) contêm o script VMAD `magicsoundplayintrooutro` apontando para os mesmos marcadores (`005902` e `010EC4`) que `powers.js:114` passa a tocar via `Sound.play(actor)`. No teste de 18/11 (`TESTE-JOGO_18-11.md:22`) nenhum poder tocou som (indicando que o VMAD provavelmente não executou áudio), mas vale manter alerta no roteiro de V7 no oficial (ou migrar ambos para records visuais sem script, como `bat`/`mist`/`swarm`).
3. **[BAIXO — `powers.js:268-275`] Acoplamento de `moveBat` a `if (value.introSound)`:**
   - Na linha 268, a microtask que executa `moveBat(id, dash)` só é agendada dentro de `if (value.introSound)`. Se `introSound` vier a ser opcional/desabilitado no futuro, o dash pararia silenciosamente. Trocar a guarda para `if (value.introSound || dash)` desacopla as duas responsabilidades.
4. **[EMPÍRICO / FORA DO ESCOPO DE CÓDIGO — já coberto nos roteiros]:**
   - Confirmação em jogo após Build/Prepare do `runtime/` de que `actionVoiceFire` dispara com o `FormID` esperado para os 6 `LesserPower` vampíricos (mesma pendência A1 de `REVISAO-L1-V6_neon.md`).
   - No servidor oficial: verificar visibilidade do `EffectShader` `0xDC209` sobre ator com `setAlpha(0, false)` (V5), solução para os dois `SNDR` sem `SOUN` (Visão `000F87:Skyrim.esm` e Lorde `07BBAE:Skyrim.esm`), e áudio nativo de Sedução/Reanimação.

---

## Matriz de verificação dos 5 focos da revisão

| Foco | Status | Evidência verificada |
|---|---|---|
| **(1) Dash por teleporte sem colisão (V2)** | **BLOQUEADO (B1)** | `powers.js:4,167,207-221,271-272`, `LocationalDataBinding.cpp:63`, `MpActor.cpp:1567-1579`, `ActionListener.cpp:206-255`, `MovementValidation.cpp:107-112`, `remoteServer.ts:420-422,450-452`. Fórmula de `rot[2]` em graus OK (`MpActor.cpp:1915-1919`, `movementGet.ts:81`), mas noclip/queda/desync reais. |
| **(2) Dano/dreno (V4): autoridade, PvP/zonas seguras, sem dano duplicado** | **APROVADO c/ ressalva (M1)** | 100% server-side (`powers.js:289-331`). Readback de HP corrigido (`:294-299,316-322`), sem cura em leitura ausente. Sem dano duplicado: `onSkySocialSpellCast` retorna `false` (`power-input.js:205`), `onSkySocialSpellHit` veta `d.child` (`powers.js:282`), `dispelSpell` no cliente (`:96`), `SN_Visual_Swarm` sem script (`:14`), e NPCs no cliente têm vida local de 1.000.000 + `startDeferredKill` (`formView.ts:323-326`). PvP/grupo/invocações OK (`__skySocial.canDamage`, `onSkySocialDamage`, `magic-service.js:159`), pendente `core/safe-zones.js` (M1). |
| **(3) Visibilidade para vizinhos e entrada tardia (V2/V3/V4/V5)** | **APROVADO** | `aetheriusSupernaturalPowerVisual` (`isVisibleByOwner: true, isVisibleByNeighbors: true`, `powers.js:347`): reaplica arte/alpha na entrada tardia (`:89-104`), preserva estado de limpeza através de unload/reload de 3D (`:80-88`) e restaura `setAlpha(1, false)` no cancelamento (`:71`). `aetheriusSupernaturalVision` (`isVisibleByOwner: true, isVisibleByNeighbors: false`, `:348`): privada ao dono (`:92,125`), resolve clones SkyMP 64-bit `0x100000000 + target` (`PartOne.cpp:807-811`, `worldViewMisc.ts:37-49`, `formViewArray.ts:98-105`), sem objetos nativos retidos entre frames. |
| **(4) Sons (V7): audíveis aos próximos, sem duplicar** | **APROVADO c/ ressalva (M2)** | `publish(id, value)` só roda na microtask pós-commit síncrono de sangue (`powers.js:268-275`, confirmado com `power-input.js:91-98`). `listeners` usa `actorNeighbors` + `onlinePlayers` em até 3.000 u (`:225-226`), compatível com `ctx.getMyFormIdInServerFormat()` para jogadores (`formId >= 0xff000000` em `PartOne.cpp:807-811` e `remoteServer.ts:600,1117`). Cache `aetheriusPowerIntros` + `soundSeq` evita duplicação em refresh, entrada tardia e reentrada; `monitorIntro` (`:19-33`) encerra handle na saída de relevância sem `indexInPool`. Ressalva M2 no fim de 1 s do Bat Form. |
| **(5) Conflito/regressão em `power-input.js` (`7435282`, `77ea350`, `850211c` × `415acde`)** | **APROVADO SEM ACHADOS** | Histórico linear verificado (`7435282` → `77ea350` → `415acde` → `850211c`). `power-input.js:6-9,25` mantém `voice: true` nas 3 transformações (`0x92c48`, `0xf8306`, `0x283b`) e `!!d.kind` nos 6 poderes de `powers.js::SPECS`. `indexInPool`, `refuse()`, `voiceOnly`, `evidence voice` e proteção contra `deny()` tardio preservados intactos; `source` e `overlays` byte-idênticos. |

---

## Saída dos testes executados pelo revisor

Ambiente: `C:\Code\Aetherius - Servidor Local`, **Node.js**, estado commitado (`HEAD = cebb42c`, sem modificações no workspace).

### 1. Recorte focado (`teste-poderes.cjs`, `teste-transformacoes-18-11.cjs`, `teste-porte-voz.cjs`)

```text
node --test overlays\sobrenatural\teste-poderes.cjs overlays\sobrenatural\teste-transformacoes-18-11.cjs overlays\sobrenatural\teste-porte-voz.cjs
# tests 46
# suites 0
# pass 46
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 106.8963
(exit 0)
```

### 2. Bateria completa (29 arquivos: 24 `overlays\sobrenatural\teste-*.cjs` + 5 Core — `werewolf.test.cjs`, `magic.test.cjs`, `survival-service.test.js`, `survival-effects.test.js`, `skysocial-service.test.js`)

```text
# AETHERIUS_VAMPIRE_FEED_ENABLED=false
# tests 486
# suites 19
# pass 486
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 1450.4657
EXIT_FALSE=0

# AETHERIUS_VAMPIRE_FEED_ENABLED=true
# tests 486
# suites 19
# pass 486
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 1428.6947
EXIT_TRUE=0
```

### 3. Scripts CLI de serviço / F7 / revisão F7

```text
node overlays\sobrenatural\teste-servico.cjs "C:\Code\Aetherius - Servidor Local\source\server\gamemode"
→ OK: serviço, restore throw/noop/reinício, falha de persistência, witch-grants e neutralização SSP

node overlays\sobrenatural\teste-painel-f7.cjs
→ OK F7: alvo, oito ações, autorização A+, sessão, catálogo, XP assinado/limites, regras de escolha, formas, replay e auditoria; F8 desligado.

node overlays\sobrenatural\teste-revisao-f7.cjs
→ OK revisão F7: fila bloqueada, reutilização de ator, reset suspenso, autorização revogada, retomada no login; restore com throw/no-op, recuperação preservada e painel sem sucesso falso; reserva e auditoria sanitizadas/clamp e regressão de outras ações.
```


---

## Reconfirmação (`01056e2`)

- **Revisor:** Gemini 4 Neon (reconfirmação independente, somente leitura no laboratório e no código)
- **Data:** 19/11/2026
- **Commits reconfirmados:**
  - `09c77673b619157327cf08fb5e867fe8747879a2` (`fix(sobrenatural): validar dash fisico e proteger o dreno`)
  - `01056e21f10d83bf79dcdf8d737b5a976f6ed5c3` (`fix(sobrenatural): validar celula do dash sem resolver ID remoto`)
  - `da31fb8a1d8ad5f047630420841ca612491b20e2` (`docs(sobrenatural): registrar correcoes B1 M1 M2 e revisao Neon`)
- **Artefatos conferidos:**
  - `powers.js` (`source` e `overlays` byte-idênticos): SHA-256 `72D1A3D4F099DA89CB0DCE1B61E999D5007F2E30F8CD45DF018CEFB4B6969A7C`
  - `overlays\sobrenatural\026-bat-dash-movement-guard.patch`: `git -C source apply --check --reverse` retornou **exit 0**
  - `source\build\dist\server\scam_native.node`: SHA-256 `4F622A37CE65D06D46A6053B61EC040E60EE037C3268E8F3DF84626E75CAABCB`, carregado via `require()` em Node (`[ 'ScampServer', 'writeLogs' ]`)

### Veredito da Reconfirmação

**APROVADO (sem novos achados bloqueantes).** Todos os achados bloqueantes e médios da revisão anterior (**B1**, **M1**, **M2**) — bem como as observações **O1** e **O3** — foram corrigidos de forma limpa, proporcional e verificável em código e testes. Restam apenas a atualização do `runtime/` (Prepare) e as validações empíricas em jogo/servidor oficial já descritas na Etapa 3.

### Avaliação ponto a ponto das correções

| Item | O que foi verificado | Status |
|---|---|---|
| **B1 — Remoção do teleporte cego e movimento físico no dono** | `powers.js:220-230,258-286`: escrita de `locationalData` removida. Após commit síncrono de sangue, revalida `actionPolicy`, arma `aetheriusBatDashGuard` com readback (`=== value.seq`) e dispara `ShoutStart` + `ShoutSprintLongStart` via `Debug.SendAnimationEvent` (`PapyrusDebug.cpp:21-33` envia o `SpSnippet` apenas ao dono). Movimento passa a respeitar colisão Havok/terreno do cliente e propaga pela via normal `UpdateMovementMessage`, eliminando o noclip por teleporte do servidor e a race condition de `MpActor::Teleport`. Fail-closed quando o addon antigo não expõe `aetheriusBatDashGuard` (`powers.js:260`). Política `['move', 'combat']` bloqueia algemas (`RESTRAINED`), prisão (`IMPRISONED`) e zonas seguras. Recarga `VAMPIRE_BAT_COOLDOWN_SECONDS` validada em 2–60 s (padrão 3 s). | **CORRIGIDO** |
| **B1 (C++ `026`) — Guarda nativa antes do relay, limite e `UpdateMovementMessage`** | `PropertyBindingFactory.cpp:68-88,97`, `MpActor.h:181-185`, `MpActor.cpp:91-101,1581,1594-1635`, `ActionListener.cpp:209-233`: (1) executa **antes** de `SendToNeighbours` (`:233`), portanto pacote fora do limite nunca é retransmitido a vizinhos nem sobrescreve `SetPos`; (2) limite radial 3D `<= 256.f` sobre `(position - guard.origin).SqrLength()` com `std::isfinite` (rejeita `NaN`/`±inf`), 16× mais estrito que o limite padrão de `4096.f` do SkyMP (`MovementValidation.cpp:107`), sem abrir nova brecha de noclip; (3) `01056e2` compara `msg.data.worldOrCell == currentCell.ToFormId(pluginIndices)` com fallback `FormDesc{}` sem resolver ID remoto arbitrário/inexistente (testado com `0xEE00003C` em `PartOne_MovementTest.cpp:206-212`); (4) janela monotônica `steady_clock` de 1.500 ms vinculada a `user`/`profile`/`cell`, invalidada em `MpActor::Teleport` (`:1581`) para não conflitar com teleportes legítimos; (5) rejeições enviam `TeleportMessage2` somente ao dono via `MovementValidation::Validate` com `{inf,inf,inf}` (sem tocar em `npcMovementCorrections` nem resetar a guarda) e rate-limit de 250 ms (`Drop` no intervalo). | **CORRIGIDO** |
| **M1 — Política de combate / zonas seguras no Enxame** | `powers.js:3,7,9,217-219,321`: registra `supernatural_swarm_drain` (`['combat']`) e consulta `actionPolicy.canPerform(characterOf(id), SWARM_ACTION, { actorId: id, targetActorId: target })` antes de alterar HP em cada tick de `drain()`, exercitando tanto estado do personagem quanto `safe-zones.blocksBetween` dos dois lados. Coberto com `global.mp` em `teste-poderes.cjs:56-71`. | **CORRIGIDO** |
| **M2 — Áudio do Morcego sem corte aos 1 s** | `powers.js:73-76,87-91,98`: `if (state.kind !== 'bat') stopIntro()` preserva `MAGPowersBatsMarker` ao término natural do visual de 1 s, mantendo parada imediata em `!actor.is3DLoaded()` (`:89`) e no `monitorIntro` (`:24-38`) quando o ator desaparece; novo uso após cooldown de 3 s cria novo `state` sem conflitar com o handle anterior. | **CORRIGIDO** |
| **O1 e O3 (observações opcionais anteriores)** | `powers.js:207-208,235` filtra `eligible` (`hostile` / `online.has`) antes do `.slice(0, 128)`. `powers.js:278-283` desacopla `moveBat` de `value.introSound` (`if (bat || value.introSound)`). | **CORRIGIDO** |

### Notas não bloqueantes (empíricas / operacionais)

1. **Validação empírica de V2 no motor (já registrada em `ETAPA-3:708,710`):** após Prepare do `runtime/` (com `scam_native.node` e `powers.js` novos), confirmar em jogo que `ShoutStart` + `ShoutSprintLongStart` produzem deslocamento real no perfil LOCAL e medir se a distância cabe no teto `256.f` sem disparar `bat dash bound rejected` em terreno aberto (Whirlwind Sprint vanilla pode superar 256 u; calibrar `maxDistance` ou revisar o gatilho físico conforme medição real).
2. **Ciência do dev sobre cooldown provisório de V2 (`DECISOES.md:61`):** alterado de "sem recarga" para 3 s provisórios (`VAMPIRE_BAT_COOLDOWN_SECONDS`, 2–60 s) como parte da mitigação de B1 — coerente com segurança; apenas confirmar se o valor padrão de 3 s está de acordo.
3. **Testes C++ nativos:** o executável `unit` citado pelo autor (`[BatDash]` 11 asserções, `[NpcMovementCorrection]` 72) não está persistido em `source/build/` para reexecução direta; a auditoria nativa cobriu inspeção integral do patch `026`, `git apply --check --reverse` (exit 0), hash e carregamento do addon Release `scam_native.node`.

### Saída dos testes reexecutados pelo revisor (`01056e2` / `da31fb8`)

```text
# Recorte poderes/voz/transformações
node --test overlays\sobrenatural\teste-poderes.cjs overlays\sobrenatural\teste-transformacoes-18-11.cjs overlays\sobrenatural\teste-porte-voz.cjs
→ # tests 50 | # pass 50 | # fail 0 (124.4 ms, exit 0)

# Core action-policy + safe-zones
node --test source\server\gamemode\core\action-policy.test.js source\server\gamemode\core\safe-zones.test.js
→ # tests 38 | # suites 14 | # pass 38 | # fail 0 (105.4 ms, exit 0)

# Bateria completa (29 arquivos: 24 overlay + 5 Core)
AETHERIUS_VAMPIRE_FEED_ENABLED=false → # tests 490 | # suites 19 | # pass 490 | # fail 0 (1425.8 ms, exit 0)
AETHERIUS_VAMPIRE_FEED_ENABLED=true  → # tests 490 | # suites 19 | # pass 490 | # fail 0 (1429.1 ms, exit 0)

# Scripts CLI (serviço / F7 / revisão F7)
node overlays\sobrenatural\teste-servico.cjs ... → OK (exit 0)
node overlays\sobrenatural\teste-painel-f7.cjs   → OK F7 (exit 0)
node overlays\sobrenatural\teste-revisao-f7.cjs  → OK revisão F7 (exit 0)

# Patch 026 e addon nativo
git -C source apply --check --reverse overlays\sobrenatural\026-bat-dash-movement-guard.patch → exit 0
node -e "require('.../source/build/dist/server/scam_native.node')" → loaded keys: [ 'ScampServer', 'writeLogs' ] (exit 0)
```
