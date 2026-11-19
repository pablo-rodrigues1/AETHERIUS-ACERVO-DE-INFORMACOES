# INVESTIGAÇÃO 18/11 — L1 (Beast Form) e V6 (Lorde Vampiro) não transformam

- **Investigador:** Opus 5.5 Low (pedido explícito do dev), tarefa `task_882110c7683b`.
- **Sintoma (dev, `docs/TESTE-JOGO_18-11.md`):** equipar o poder e apertar **Z** → ícone do efeito no canto superior direito, mas a raça/forma não muda. O mesmo vale para Beast Form e Lorde Vampiro.
- **Laboratório analisado:** `C:\Code\Aetherius - Servidor Local`, Git raiz `764c46a` antes desta investigação; `source\` = `overlays\sobrenatural\files\` (conferido com `cmp`); `runtime\server\gamemode\` idêntico ao `source\` nos arquivos do caminho (`power-input.js`, `powers.js`, `werewolf-service.js`, `vampire-lord-service.js`, `supernatural-service.js`, `magic-service.js`, `phase0-basic.js`, `werewolf/client.js`). Cliente `client-source` `6e011b3`.
- **Log:** `C:\Code\Aetherius - Servidor Local\logs\server-20261118-222056.stdout.log` (2499 linhas) e `.stderr.log` (só aviso de `punycode`).
- Servidor e jogo **não** foram iniciados. houseCARL não foi necessário: os FormIDs batem com o código (ver §2).

## 1. Caminho completo (como está no código)

| Etapa | Onde | O que exige |
|---|---|---|
| Concessão | `source/server/gamemode/supernatural/werewolf-grants.js:27` (`WerewolfChange` Skyrim 0x92C48); `vampire-grants.js:37,67` (`DLC1VampireChange` Dawnguard 0x283B, só a partir de `VAMPIRE_LORD_MIN_STAGE`) | — |
| Leitor do cliente | `supernatural/power-input.js:17-62` (`input`, event source `_aetheriusSupernaturalPower`) | Ouve `spellCast` (`:38-41`) e `actionVoiceFire` (só poderes de voz Skyrim.esm, `:42-48`). Envia `activate {generation, nonce, sequence, spell}` se houver permit em `sp.storage.aetheriusPowerPermit` (`:27-37`). |
| Permit | `power-input.js:10-16` (`permit`), publicado por `refresh` (`:104-116`) a cada tick de 250 ms para quem tem maldição | — |
| Comando | `power-input.js:162-174` (`request`) | nonce/geração/sequência corretos → `gate` → guarda em `pending` e chama `complete`. |
| Gate | `power-input.js:117-134` | online, canal do personagem, vivo, SkyDeath ALIVE, sem trava SkySocial/profissão, sem forma ativa, `knownSpells` contém o poder, poder no `equipment` (`leftSpell/rightSpell/voiceSpell/instantSpell`), 1 s de cadência, maldição certa (Lorde: `curse === 'vampire'`). |
| **Proposta nativa** | `power-input.js:175-182` (`mp.onSkySocialSpellCast`), alimentado por `ActionListener::OnSpellCast` (`source/server/cpp/server_guest_lib/ActionListener.cpp:1735-1826`), que só existe quando o cliente envia `MsgType.SpellCast` em `client-source/client/src/services/services/magicSyncService.ts:58-70` — e esse envio **só acontece no evento `spellCast`**. | — |
| **Junção** | `power-input.js:135-151` (`complete`) | `pending` (comando) **e** `native` (proposta) do mesmo feitiço com no máximo **750 ms** de diferença (`:137`). Sem os dois, nada acontece; `tick` descarta após 750 ms (`:203`). |
| Domínio | Lobo: `werewolf-service.js:166` `start(id, {animate, spell})`; Lorde: `vampire-lord-service.js:87-103` `start` via `withBlood` (`power-input.js:69-93`, reserva de 5 de sangue, commit após `isLord`) | troca de aparência/raça autoritativa (`setAppearance`, `vampire-lord-service.js:81-84,104-109`). |
| Leitores legados | `werewolf/client.js:19-63` (EVENT `_aetheriusBeast`), `vampire-lord-service.js:32-55` | Com o dispatcher instalado, tudo vira `observe` → só log `evidence <kind> (no transition)` (`werewolf-service.js:316-321`; `vampire-lord-service.js:138-139`; `power-input.js:152-161`). |

## 2. Evidência do log

1. **Nenhuma linha `[supernatural-power] command actor=...`** em toda a sessão (nem em nenhum log anterior de `logs\`). `complete` registra essa linha sempre que passa da junção (`power-input.js:149`), aceito ou não. Logo, a junção comando+proposta **nunca** aconteceu — para nenhum poder gerenciado (inclusive Morcegos/Névoa, cujos efeitos vistos pelo dev em V2/V3 são, portanto, o caminho nativo do mod, não o do servidor).
2. **Nenhuma linha `ActionListener::OnSpellCast - spell ... not found in equipment`**, e **nenhuma `evidence cast`**. Só aparecem `evidence race` (linhas 212, 2007, 2065) — prova de que os event sources do cliente estão carregados, assinados e enviando eventos; o que nunca chega é o relato de *uso* do poder.
3. O leitor legado do Lorde só ouve `spellCast` (`vampire-lord-service.js:37-44`). Nenhum `evidence cast` no período vampiro (22:27:14 → 22:37:12, linhas 2005/2063) ⇒ o `spellCast` do `DLC1VampireChange` não foi emitido pelo motor/SP para o Z.
4. Exceções `resolved context ... aetheriusProfessionInventoryLocked` (352×, `ScampServer.cpp:1249 Get`) existem só em 22:25–22:27 (período lobisomem, ~4/s = tick de `forced-werewolf.js:66-74`) e uma em 23:07. `gate` faz o mesmo `get` (`power-input.js:122`); no período vampiro **não há nenhuma**, coerente com `gate` nunca ter rodado até essa linha. (Essas exceções são capturadas pelo `get` seguro; ruído, não causa.)
5. FormIDs conferem: servidor `vampire-lord ... poder 0x200283b` (linha 155); `BEAST_SPELLS = [0x92c48, 0xf8306]` (`werewolf-service.js:23`) = concessão.

## 3. Causa raiz

**O poder usado pelo Z não gera `spellCast` no cliente; sem `spellCast` não há mensagem `SpellCast`, logo não há proposta nativa no servidor, e `complete` (`power-input.js:137`) falha fechado — em silêncio.**

- Isso é exatamente o que o servidor oficial v0.42 registrou ao adicionar `actionVoiceFire` (“`spellCast` não chegava para o Power Z”, `docs/COMPARACAO_lobisomem-oficial-v0.42.md` §143). No nosso porte o `actionVoiceFire` foi ligado ao **comando** (`power-input.js:42-48`), mas a **proposta nativa** continuou dependendo só de `spellCast`/`OnSpellCast`. O próprio documento de comparação já dizia que a necessidade “deve ser medida no motor, não presumida” — o teste de 18/11 é essa medição.
- Os testes `overlays/sobrenatural/teste-porte-voz.cjs:30-41` sempre simulam a proposta nativa (`native()` → `mp.onSkySocialSpellCast`), por isso passavam.
- O ícone no canto superior direito é o efeito do próprio SPEL aplicado pelo motor local; a transformação vanilla depende de script Papyrus/quest que o cliente SkyMP não executa, e o servidor descarta a réplica (`power-input.js:181` retorna `false`).

**Ponto não fechado:** o leitor legado do lobisomem também ouve `actionVoiceFire` (`werewolf/client.js:35-43`) e não houve `evidence cast` no período lobisomem (22:25:37 → 22:27:14, ~1,5 min). Ou o dev não apertou Z nesse intervalo (as requisições `spells equipAbility` só começam às 22:28:00, já como vampiro), ou `actionVoiceFire.source` não é o SPEL para poderes. Isso **precisa ser medido em jogo** com os logs novos (§5).

## 4. Console do cliente

- **`Invalid __skyrimPlatform__indexInPool: 738`** — vem do leitor de poderes, mas **não** é ele que impede a transformação. Em `power-input.js` (antes desta correção, linhas 24 e 57) o `MagicEffect` era criado uma vez na montagem do event source (índice baixo no pool, 738) e usado em `player.hasMagicEffect(power.effect)` a cada 100 ms, fora de `try`. O pool de objetos nativos do SP3 vale só para o quadro corrente (faixa `[436393, 436427)` crescendo), então a chamada lançava em todo ciclo, interrompendo a detecção de efeito (`sendEvent('effect')`). **Corrigido** (§5).
- **`failed to get key 'argsJsonDumps': couldn't get array index 0 ... (type in:n ...)`** — erro de serialização de uma `CustomEventMessage` cujo primeiro argumento falta/é `null`. Os `sendEvent` do caminho da transformação sempre mandam string no índice 0 (`power-input.js:36,54,58`; `werewolf/client.js:30,41,50,64`; `vampire-lord-service.js:42,52`). Candidato encontrado fora deste caminho: `supernatural/veil-ritual-fx.js:59` `ctx.sendEvent()` **sem argumentos**, a cada 750 ms enquanto o jogador está em combate com apresentação do Véu ativa. **Não é** do caminho da transformação; registrar para o dono do Véu (Etapa 4). Não alterado.

## 5. Correção aplicada (localizada e segura)

Arquivo: `source/server/gamemode/supernatural/power-input.js` (espelho idêntico em `overlays/sobrenatural/files/server/gamemode/supernatural/power-input.js`).

1. **indexInPool:** o leitor guarda só `[local, arquivo]` do efeito e resolve o `MagicEffect` no próprio quadro, dentro de `try` por poder.
2. **Diagnóstico das recusas (sem mudar decisão):** função `refuse` com log limitado a 1/s por ator+motivo:
   `[supernatural-power] refused actor=0x.. spell=0x.. reason=<motivo>` com motivos `no_channel`, `stale_permit`, `gate`, `gate_at_join`, `native_hook`, `native_interrupt`, `native_gate`, **`no_native_proposal`** (comando chegou, SpellCast não) e `no_command` (SpellCast chegou, comando não).
   Nenhuma regra de aceitação mudou; tudo continua falhando fechado.

Teste novo: `overlays/sobrenatural/teste-transformacoes-18-11.cjs` (2 casos: objeto nativo não sobrevive ao quadro; Z sem SpellCast registra `no_native_proposal` e não transforma). Bateria `node --test overlays/sobrenatural/teste-*.cjs`: **312/312** suítes de teste passam; `teste-servico.cjs` é um script que exige caminho por argumento e falha só por ser carregado sob `node --test` (não alterado, mesma condição antes).

## 6. Correção proposta (NÃO aplicada — decisão de arquitetura/segurança)

Aceitar o uso por Z sem `SpellCast` nativo muda o modelo de confiança aprovado (P1 do porte: “entrada da fera falha fechada”, `RETOMADA.md` 17/11). Proposta para o orquestrador decidir e mandar revisar:

- **Opção A (recomendada):** para poderes de **voz** (`voiceSpell`), tratar o `activate` vindo de `actionVoiceFire` como suficiente quando o servidor confirma `equipment.voiceSpell === spell`, `knownSpells`, nonce/geração/sequência de uso único, cadência 1 s e maldição/estágio (já todos em `gate`). Implementação: marcar no payload a origem (`via: 'voice'`) e, em `request`, se `via === 'voice'` e o poder está no slot de voz, executar `complete` sem `native`. Ainda falha fechado para mãos/instant.
- **Opção B:** o cliente enviar `MsgType.SpellCast` também em `actionVoiceFire` (alteração em `magicSyncService.ts`), mantendo a junção do servidor. Mais invasivo (cliente oficial, réplica a vizinhos, `OpenBoundWeaponCast`).
- Antes de qualquer opção: um teste em jogo com o log novo, para confirmar `reason=no_native_proposal` (A resolve) versus ausência total de linha (então nem `actionVoiceFire` chega com o SPEL e o leitor precisa de outro gatilho, p.ex. detectar o `MagicEffect` do poder — o canal `effect` já existe e agora funciona).

## 7. O que exige teste em jogo

1. Instalar o gamemode com este `power-input.js` (Stop → Build → Prepare → Start).
2. Lobisomem e Vampiro (estágio do Lorde): equipar o poder, Z; anotar a hora e enviar o log. Esperado com o bug: `refused ... reason=no_native_proposal` (ou nenhuma linha, ver §6).
3. Conferir que o console do cliente **não** mostra mais `indexInPool` em loop.
4. Conferir se `argsJsonDumps` só aparece em combate com o Véu ativo (confirma a origem em `veil-ritual-fx.js:59`).

## 8. Opção A aplicada (decisão do orquestrador, 18/11)

- **Modelo de confiança P1 — antes:** comando + `SpellCast` nativo obrigatórios. **Agora:** para poder no slot de voz do servidor, comando (`via: 'voice'`, nonce/seq de uso único) + validação server-side bastam. Registro em `DECISOES.md` (18/11, Opção A).
- Código: `power-input.js` — cliente marca `via: 'voice'` no `activate` vindo de `actionVoiceFire`; `request` guarda `via`; `complete` usa `voiceOnly(id, command)` (`equipment.voiceSpell === spell` lido do servidor) para dispensar a proposta nativa. Todas as checagens de `gate` e do domínio continuam. Uma proposta nativa que chegue depois é recusada pelo gate (forma já ativa / cadência).
- Testes novos (`teste-transformacoes-18-11.cjs`): caminho válido transforma; forjado sem poder, maldição errada, sem maldição e fora do slot de voz → recusados; recarga do domínio (`accepted=false`) e cadência de 1 s → recusados; replay do mesmo nonce → recusado. `teste-porte-voz.cjs` ajustado (2 casos que exigiam proposta nativa agora esperam a transformação). Bateria: **319/319** (+ `teste-servico.cjs`, script com argumento, inalterado).
- **Teste em jogo:** Z como lobisomem (Beast Form) e como Lorde (estágio mínimo + 5 de sangue) deve transformar; log esperado `command actor=... accepted=true`.

### 8.1 Correções da revisão Opus Medium (`REVISAO-L1-V6_opus-medium.md`)

- **Correção da afirmação acima:** em `77ea350` o Lorde **não** transformava por Z — o leitor só marcava voz para os SPEL do Skyrim.esm (P1 da revisão). Corrigido agora.
- **P1:** `DEFINITIONS` em `power-input.js` ganha `voice: true` para `WerewolfChange` (Skyrim 0x92C48), `WerewolfChangeRingOfHircine` (0xF8306) e `DLC1VampireChange` (Dawnguard 0x283B); o cliente resolve cada um por `[arquivo, local]` e marca `voice: !!d.voice || !!d.kind` (os SPECS de vampiro já eram voz desde `415acde`, Sol).
- **P2a:** `actionVoiceFire` do jogador com fonte não gerenciada ou nula envia `voice <FormID|0>` (limitado a 1/s no cliente); o servidor registra `[supernatural-power] evidence voice actor=… value=0x…` (só evidência, sem transição). No teste em jogo isso separa “Z não disparou” de “Z disparou com outra forma”.
- **P2b:** `deny` não republica `rejected` (que faz o cliente dar `dispelSpell`) para feitiço de fera quando o ator já tem sessão de lobisomem — a proposta nativa tardia após a voz aceita fica só recusada.
- **P2c:** `feeding-client.js` guarda só FormIDs (`actorId`, `targetId`) e re-resolve o `Actor` em `registry.clean` no quadro corrente (mesmo padrão do indexInPool).
- Testes novos em `teste-transformacoes-18-11.cjs`: Lorde por voz pelo `input` real (activate `via:'voice'` → `lord.start` + reserva de 5 de sangue, `accepted=true`); evidência `voice`; proposta nativa tardia sem `rejected`; feeding sem Actor guardado. Ajustes: `teste-porte-voz.cjs` (ignora o pacote de diagnóstico `voice`), `teste-alimentacao.cjs` (mock de `getFormEx` para o ator re-resolvido).
- Bateria `node --test overlays/sobrenatural/teste-*.cjs` + `werewolf.test.cjs`: **355/359**. As 4 falhas são alheias e de trabalho paralelo não commitado: `teste-ritual-hud-nativo.cjs` (arquivo novo não rastreado), `teste-sangue-hud.cjs` (`blood-presentation.js` modificado no working tree), `teste-veu-mundo.cjs` (modificado no working tree) e `teste-servico.cjs` (script que exige argumento).
- **Teste em jogo:** Z como lobisomem e como Lorde → `command actor=… accepted=true`; se não transformar, procurar `evidence voice` para ver o FormID que o Z reporta.
