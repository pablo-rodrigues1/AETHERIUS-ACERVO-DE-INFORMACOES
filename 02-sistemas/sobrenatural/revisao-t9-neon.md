# Revisão Independente — T9 (Runa de Conjuração no Ritual do Véu)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura no código)
- **Data:** 19/11/2026
- **Alvo revisado:**
  - Laboratório (`C:\Code\Aetherius - Servidor Local`):
    - Código: `47835dc0a8aa395eb21f4d08906b4f5de9ccb03b` (`fix(sobrenatural): restaura runa e encerra a pose do ritual`)
    - Documentação: `7a22c8ef20942ce054f4aff6e6d545cd5716b628` (`docs(sobrenatural): investiga desaparecimento da runa do ritual`)
  - Investigação de referência: [INVESTIGACAO_19-11_runa-ritual.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_19-11_runa-ritual.md)
  - Contexto: [TESTE-JOGO_19-11.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/TESTE-JOGO_19-11.md), [TESTE-JOGO_18-11.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/TESTE-JOGO_18-11.md), [DADOS-HOUSECARL_19-11_ritual.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/DADOS-HOUSECARL_19-11_ritual.md), [REVISAO-BRUXA-HUD-RITUAL_neon.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/REVISAO-BRUXA-HUD-RITUAL_neon.md)

---

## Veredito

**APROVADO SEM ACHADOS.**

A causa raiz de T9 foi comprovada por evidência direta (log do teste de 19/11, malha `RuneConjuration02.nif`, records houseCARL e histórico de `42315d1^` → `42315d1`). A correção em `47835dc` é cirúrgica, segura contra `Invalid __skyrimPlatform__indexInPool`, preserva a correção de B5/B7 com ordem melhorada (`removeSpell` antes de `dispelSpell`), mantém intactos B4 (`aetheriusVeilRitualCharge`), o servidor autoritativo e o isolamento do cliente/UI Core. Não há defeitos reais bloqueantes ou médios. Seguem abaixo apenas a auditoria técnica e três observações opcionais (não bloqueantes) para acompanhamento no teste em jogo.

---

## 1. Verificação da Causa Raiz (T9)

- **Log do teste solo (`server-20261119-003042.stdout.log:254-261`):** confirmado o recebimento de `ritual.start` (00:35:03.227), `ritual.cancel` por movimento (00:35:10.683) e segundo `ritual.start` (00:35:31.793).
- **Malha e records:** inspecionado diretamente `C:\modOrganizer\Mods\Master Spells Runes\meshes\Magic\Runes\RuneConjuration02.nif` (6.578 bytes), confirmando `BSBehaviorGraphExtraData`, `NiControllerSequence` e as referências `Magic\CastingRitualBody.hkx`, `AttachT`, `mIntro`, `mCharge`, `mReady`, `mReadyTrans`, `mCast` e `mIdle`. Em `DADOS-HOUSECARL_19-11_ritual.md`, `SPEL 00083A` (`SN_Veu_RitualRune`, Ability `ConstantEffect`/`Self`) aponta para `MGEF 00000F:MasterSpellsRunes.esp` (`FXSummonMassBodyHolderRune`, `HitEffectArt = 00000E`, sem VMAD/scripts).
- **Histórico de código:** `42315d1^:44-45` enviava `MLh_SpellAimedConcentrationStart` após `addSpell` (e a runa apareceu no teste de 18/11); `42315d1` removeu os eventos de animação ao reescrever o ciclo para corrigir `indexInPool`, deixando apenas `addSpell`, o que explica o desaparecimento da runa no teste de 19/11.

---

## 2. Auditoria Técnica (Regressão B5/B7, Crash, Sincronização e Performance)

| Eixo | Evidência verificada | Resultado |
|---|---|---|
| **Regressão B5/B7 (remoção da runa e feitiços comuns)** | Em `veil-ritual-fx.js:17-36`, `cleanup()` resolve `actor` (`sp.Game.getFormEx`) e `spell` (`sp.Game.getFormFromFile`) **no frame corrente**, chama `removeSpell` **antes** de `dispelSpell` (atendendo à Obs. 2 de `REVISAO-BRUXA-HUD-RITUAL_neon.md`, impedindo reativação da Ability `ConstantEffect` antes da remoção) e executa `MLh_Equipped_Event` no bloco `finally` quando `active.resetPose` está armado. Se `actor` vier nulo (`:18`), retorna `false` antes do `try/finally`, preservando `active.resetPose = true` para retentativa a cada 250 ms. Falha em `addSpell` (`:94`) retorna com `ended: true` e `resetPose: false` (sem disparar pose); exceção em `sendAnimationEvent` na montagem (`:97`) mantém `ended: true` e `resetPose: true` para limpeza pelo `lease`. Migração v1/v2 → v3 (`:43-51`) desarma leases antigos, limpa `previous.sound` de v2 e aplica `resetPose` somente se houver estado legado v1. | **Aprovado** (validação final do behavior graph em jogo pendente, conforme já registrado por Sol) |
| **Crash / `indexInPool`** | O objeto de estado `state.veilRitualFxV3` (`veil-ritual-fx.js:45-46, 73`) guarda exclusivamente valores primitivos (`actorId`, `cell`, `id`, `at`, `sample`, `ended`, `resetPose`, `retryAt`). A closure `lease` (`:75-92`) não retém `Actor` nem `Spell` entre frames e envolve as chamadas nativas em `try {} catch {}`. `receive()` inteiro está protegido por `try {} catch {}`. Zero risco de `Invalid __skyrimPlatform__indexInPool`. | **Aprovado** |
| **Sincronização cliente↔servidor e B4** | Apenas `receive()` (`veil-ritual-fx.js:13-102`) foi alterado; o lado servidor (`install`, `start`, `stop`, `tick`, `dispose`), `receiveCharge` (B4), `statusOwner`, `combatSource` e `veil-ritual.js` permaneceram intocados. `PROPERTY` (`aetheriusVeilRitualFx`) segue pública (`isVisibleByOwner: true, isVisibleByNeighbors: true`) com `{ id, sample } | null`, sem vazamento de keyword (`JogadorBruxa`). `formView.ts:219` reseta `this.state = {}` ao recriar réplica de vizinho, impedindo que `active.actorId !== actorId` fique preso em referência destruída. Guarda `ritualExpiredId`/`ritualExpiredSample` evita remontagem com amostra expirada após queda de 3D/troca de célula. | **Aprovado** |
| **Performance** | Durante o ritual ativo (`veil-ritual-fx.js:63-69`), heartbeats apenas renovam `active.sample` e `active.at` sem reenviar `MLh_SpellAimedConcentrationStart` nem resolver/reaplicar o feitiço. O watchdog `lease` agenda `sp.once('update', lease)` somente enquanto ativo e encerra em no máximo 10 s após a última amostra (`:91`) caso a referência desapareça. Nenhum arquivo de `client-source` ou UI Core foi alterado (`client-source` permanece em `295f02e`). | **Aprovado** |
| **Paridade e documentação (`7a22c8e`)** | SHA-256 idêntico entre `overlays/.../veil-ritual-fx.js` e `source/.../veil-ritual-fx.js` (`714381699727845F3CC36A4C6517C02C5531B7769D467E05F5FF45B0B6E78445`). `INVESTIGACAO_19-11_runa-ritual.md` idêntico entre o laboratório e `Sobrenatural\docs\investigacoes\` (`0C90846B18825F9A94103FBEC2E096863E5F8D3FB49C1A007A9A41BC31B375B7`). Escopo das afirmações (incluindo o que só o teste em jogo confirma) está rigoroso e honesto. | **Aprovado** |

---

## 3. Observações Opcionais (Não Bloqueantes)

1. **Interação de `MLh_Equipped_Event` com `AnimationSource` no dono (`client-source/client/src/sync/animation.ts:269-272` e `movementApply.ts:130-134`):**
   Quando o dono encerra o Ritual e `veil-ritual-fx.js:26` chama `sp.Debug.sendAnimationEvent(actor, 'MLh_Equipped_Event')`, caso o motor sinalize `ctx.animationSucceeded === true`, `AnimationSource.onSendAnimationEvent` casa `.toLowerCase().includes("equip")`, ativa `weapDrawnBlocker` por 300 ms e envia `"SkympFakeEquip"` aos observadores (`applyWeapDrawn(ac, true)`). Isso não impede a limpeza nos vizinhos (pois o próprio `updateNeighbor` envia `MLh_Equipped_Event` localmente na réplica), não gera crash nem reintroduz B7; no máximo pode causar um breve piscar cosmético de mãos/arma sacadas por ~300 ms na réplica vista por outros jogadores ao sair do Ritual. Vale apenas observar no teste multiplayer (servidor oficial).
2. **Emote `K` durante o Ritual (`veil-ritual-fx.js:97`):**
   Como `MLh_SpellAimedConcentrationStart` é disparado uma única vez na montagem (correto para não interferir com o emote `K`), iniciar um emote durante o Ritual pode tirar o ator da pose de conjuração e ocultar a arte `RuneConjuration02.nif` enquanto o emote durar, sem cancelar o Ritual no servidor. O roteiro em `INVESTIGACAO_19-11_runa-ritual.md:30` já prevê testar o emote `K` no LOCAL para confirmar se o comportamento visual agrada ao dev.
3. **Micro-otimização no caminho ocioso (`veil-ritual-fx.js:38-62`, herdada de `42315d1`):**
   Como `tick()` grava `PROPERTY = null` para todos os jogadores online (`:252`), `GamemodeUpdateService` invoca `receive()` a cada frame com `value === null` (`null !== undefined`), chamando `Actor.from`, `getFormID` e `getParentCell()?.getFormID()` antes de retornar na linha 62. Um retorno antecipado no topo quando `!value?.id && !state.veilRitualFx && !state.veilRitualFxV2 && !state.veilRitualFxV3` (no padrão de `receiveCharge` na linha 107) evitaria essas chamadas nativas em atores ociosos. Custo atual é baixo — estritamente opcional.

---

## 4. Verificações Executadas na Revisão

Executadas diretamente em `C:\Code\Aetherius - Servidor Local`, sem editar código nem fazer deploy:

| Verificação | Resultado |
|---|---|
| `node --check overlays/sobrenatural/files/server/gamemode/supernatural/veil-ritual-fx.js` | exit `0` |
| `node --test overlays/sobrenatural/teste-veu-mundo.cjs overlays/sobrenatural/teste-ritual-servidor.cjs` | **15/15** (`fail 0`, exit `0`) |
| Bateria overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`) | **352/352** (`fail 0`, exit `0`) |
| Bateria overlay (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`) | **352/352** (`fail 0`, exit `0`) |
| `teste-servico.cjs` (apontado a `source/server/gamemode`), `teste-painel-f7.cjs`, `teste-revisao-f7.cjs` | **3/3 aprovados** (exit `0`) |
| Paridade SHA-256 (`overlays` vs `source` em `veil-ritual-fx.js`; lab vs `Sobrenatural\docs` em `INVESTIGACAO_19-11_runa-ritual.md`) | Idênticos |
