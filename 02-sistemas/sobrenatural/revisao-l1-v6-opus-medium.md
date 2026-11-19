# REVISÃO L1/V6 — segundo revisor (Opus 5.5 Medium)

- **Revisor:** Opus 5.5 Medium (independente; o primeiro revisor é o Gemini 4 Neon, `REVISAO-L1-V6_neon.md`, não consultado).
- **Autor revisado:** Opus 5.5 Low. Tarefa `task_facfa161ba4d`, 18/11/2026.
- **Laboratório:** `C:\Code\Aetherius - Servidor Local`, commits `7435282` (indexInPool + logs `refused reason=`) e `77ea350` (Opção A). HEAD = `77ea350`.
- **Arquivo central:** `overlays/sobrenatural/files/server/gamemode/supernatural/power-input.js` no HEAD (o `source/server/gamemode/supernatural/power-input.js` era idêntico ao HEAD no início da revisão). Números de linha abaixo = HEAD `77ea350`.
- **Lido:** `AGENTS.md`, `docs/TESTE-JOGO_18-11.md`, `docs/investigacoes/INVESTIGACAO_18-11_transformacoes.md`, `DECISOES.md` (18/11, Opção A), `power-input.js`, `werewolf-service.js:166-197`, `vampire-lord-service.js:30-150`, `supernatural/forced-werewolf.js:60-135`, `supernatural/powers.js`, `ActionListener.cpp:389-425,1735-1826`, testes `teste-transformacoes-18-11.cjs` e `teste-porte-voz.cjs`, scripts de cliente em `supernatural/*client*.js`, `werewolf/client.js`, `veil-ritual-fx.js`.
- **Somente leitura.** Nada foi alterado no laboratório. houseCARL não foi necessário.

## Veredito

**APROVADO COM RESSALVAS para L1 (Beast Form). REPROVADO para V6 (Lorde Vampiro): a Opção A, como foi entregue, não alcança o Lorde.**

- O modelo de confiança novo é **equivalente** ao anterior em segurança: não abre forja nova (ver §1).
- A validação server-side cobre tudo que o `OnSpellCast` nativo validava, e mais.
- Anti-replay, fail-closed do leitor legado (porte v0.42), journal B1 e transformação forçada: sem regressão.
- **Bloqueante (P1):** o leitor do cliente só marca `via: 'voice'` para os dois SPEL de Skyrim.esm. O `DLC1VampireChange` (Dawnguard 0x283B) nunca recebe `activate` por `actionVoiceFire`, logo o Lorde continua sem transformar. A investigação §8 afirma o contrário ("Z como Lorde deve transformar") — afirmação sem teste e incorreta.

## 1. Modelo de confiança: um cliente pode forjar a transformação?

**Antes:** `activate` (nonce do cliente) **e** `SpellCast` nativo (`MsgType.SpellCast`, também enviado pelo cliente).
**Agora (voz):** só o `activate` com `via: 'voice'` + `equipment.voiceSpell === spell` no servidor.

O `SpellCast` nativo **nunca foi prova de que o motor conjurou**: é uma mensagem que o próprio cliente monta. O que o servidor fazia com ela (`ActionListener::OnSpellCast`, `source/server/cpp/server_guest_lib/ActionListener.cpp:1735-1826`):

| Verificação no `OnSpellCast` | Equivalente no `gate` (`power-input.js:130-147`) |
|---|---|
| SkyDeath bloqueia ações (`:1740`) | `skyDeath.phase !== 'ALIVE'` (`:135`) |
| SkySocial inventário travado (`:1741`) | `skySocialInventoryLocked`, `__skySocial.isLocked`, `byTrader` (`:135`) |
| caster = ator do usuário (`:1755-1770`) | o evento chega pelo canal do próprio ator; `channel.character === characterOf(id)` (`:133`, `:187`) |
| `IsDead` (`:1776`) | `isDead !== false` (`:133`) |
| `IsSpellEquipped` (`:1805`) | `[left,right,voice,instant].includes(spell)` (`:139`) e, na voz, `voiceSpell === spell` (`:150`) |

Mais o que só o gate tinha: `knownSpells` (concessão por estágio), maldição certa (`:142-145`), `minXp` dos poderes de vampiro, sem forma ativa, travas de profissão, cadência de 1 s; e os domínios: recarga real de 1800 s do lobisomem (`werewolf-service.js:171`), journal pendente (`:168`), keyword `JogadorLobisomem` (`:169`), `knows`/`canUse` do Lorde (`vampire-lord-service.js:89`), 5 de sangue (`power-input.js:76-100`).

O `equipment.voiceSpell` também é dado do cliente (`UpdateEquipment`), mas o servidor só o aceita se o feitiço estiver aprendido (`ActionListener.cpp:420-425`), e `knownSpells` é concedido pelo servidor. Conclusão: **um cliente modificado já podia, antes, forjar as duas mensagens**; a Opção A só deixa de exigir uma redundância que nunca foi autoridade. O ganho perdido é apenas "o cliente honesto viu o motor conjurar", que o teste em jogo provou não existir para o Z. **Aceito.**

Observação: no servidor a dispensa vale para **qualquer** poder reconhecido no slot de voz (`voiceOnly`, `:150`), inclusive os de `powers.js` SPECS (Morcegos, Névoa, Enxame…), não só fera/Lorde. Isso é coerente com a decisão ("um `activate` vindo de `actionVoiceFire`") e seguro pelos motivos acima, mas não está coberto por teste (ver P3-c).

## 2. Achados priorizados

### P1 — V6 não corrigido: o cliente nunca marca o Lorde como voz
- `power-input.js:23`: `voice: d.file === 'Skyrim.esm' && [0x92c48, 0xf8306].includes(d.local)`. A definição do Lorde (`:7`, `{ file: 'Dawnguard.esm', local: 0x283b, effectLocal: 0x283c }`) dá `voice: false`.
- `power-input.js:49`: o `actionVoiceFire` só chama `activate` se `d.voice`. Para o `DLC1VampireChange` (Greater Power, slot de voz) **nada é enviado**.
- A investigação (§2.3) mediu que o `spellCast` do Lorde também não chega. Logo, no HEAD, o Z do Lorde continua sem `activate` e sem proposta nativa → nenhuma linha `command`; nem sequer `refused` (o comando nunca existe).
- Os testes não pegam: `teste-transformacoes-18-11.cjs` só usa 0x92c48 e chama `bridge.request` direto (sem passar pelo leitor `input`); não há caso do Lorde.
- **Correção sugerida (para o autor; não aplicada):** marcar como voz pela definição, não pelo arquivo — p.ex. `voice: true` na entrada do Lorde em `DEFINITIONS` (`:7`) e `voice: !!d.voice || …` em `:23`; teste que alimente `actionVoiceFire` com `source` = 0x283B no `input` real e espere `activate` com `via: 'voice'` e `lord.start` + `pendingBlood`. Corrigir a afirmação da investigação §8.
- Nota: há no working tree uma alteração **não commitada** de outro trabalho em andamento (`voice: !!d.kind || …`) que inclui os SPECS de vampiro, mas **ainda exclui o Lorde** (a entrada 0x283B não tem `kind`).

### P2 — Premissa de L1 ainda não medida: `actionVoiceFire.source` é o SPEL?
- L1 só fica corrigido se `actionVoiceFire` disparar para poderes e trouxer o SPEL em `event.source` (`:48`). A própria investigação (§3, "Ponto não fechado") registra que no período lobisomem não houve nenhum `evidence cast` do leitor legado, que também ouve `actionVoiceFire` (`werewolf/client.js:35-43`).
- Se o `source` vier nulo/forma diferente (p.ex. o `TESShout` ou nada), o Z continua mudo e **sem log nenhum** (o `try {}` em `:47-50` engole). Recomendo, antes do teste em jogo, um log de diagnóstico limitado no servidor para `evidence` do canal legado, ou que o próprio leitor envie um `observe` com o FormID recebido em `actionVoiceFire` quando o ator for o jogador e o FormID não for reconhecido — para que o teste distinga "não disparou" de "disparou com outra forma".
- Plano B já existente: o canal `effect` (`:58-67`), agora funcional após a correção do indexInPool, detecta o `MagicEffect` do poder; hoje ele é só evidência (`observe`).

### P2 — Proposta nativa tardia depois da voz: `deny` despacha `dispelSpell` da Forma de Fera
- Ordem plausível no motor: `actionVoiceFire` antes de `spellCast`. Se algum dia o `spellCast` chegar depois (o teste `teste-porte-voz.cjs:31-35`, caso `nativeFirst=false`, simula exatamente isso), `onSkySocialSpellCast` (`:194-198`) vê o gate falhar (sessão ativa/cadência) → `refuse native_gate` → `deny(id, spell)`.
- `deny` (`:112-116`) só se cala para poder de vampiro ativo e sessão de Lorde; **não** para sessão de lobisomem. Resultado: o permit é republicado com `rejected` e o cliente executa `dispelSpell(WerewolfChange)` (`:12-15`) durante a transformação que o servidor acabou de aceitar. Antes da Opção A essa ordem completava a junção (aceito), sem `deny`.
- Impacto provável baixo (a transformação é por aparência no servidor; o dispel remove o efeito local/ícone), mas é mudança de comportamento não testada. Sugestão: em `deny`, também retornar se `mp.__aetheriusWerewolf?.sessions.has(id)` e o feitiço for de `BEAST_SPELLS`; ou não chamar `deny` em `native_gate` quando `lastUse` do mesmo feitiço for < 1 s.

### P2 — Outro objeto nativo guardado entre quadros (fora do caminho L1/V6)
- `supernatural/feeding-client.js:34` guarda `actor: a` (Actor do quadro) em `sp.storage.aetheriusFeedPairs.pairs`, e `:40` guarda `entry.target = target`. Ambos são usados em quadros posteriores por `registry.clean` (`:28`, chamado do `update` em `:29`): `actor?.is3DLoaded()` / `sp.Debug.sendAnimationEvent(actor,'IdleStop')`.
- Pelo mesmo mecanismo do indexInPool, isso lança; o `try{}catch{}` engole → o `IdleStop` do par de alimentação pode **nunca** ser enviado ao expirar. Não é a origem do índice fixo 738 (essa entrada é criada no momento da alimentação), mas é o mesmo defeito. Correção: guardar FormIDs (`a.getFormID()`, `target.getFormID()`) e resolver com `sp.Actor.from(sp.Game.getFormEx(id))` dentro de `clean`.
- Demais scripts conferidos sem retenção indevida: `power-input.js` (corrigido), `werewolf/client.js`, `vampire-lord-service.js` (`input`/`effects`), `forced-werewolf-client.js`, `blood-effects-client.js`, `veil-ritual-fx.js` (já resolve por id em `resolveActor`/`resolveSpell`), `supernatural/client.js`, `powers.js` (`effects`).

### P3 — Itens menores
- **a) Log enganoso:** um `activate` com `via: 'voice'` cujo poder não está no slot de voz cai na junção nativa e expira como `reason=no_native_proposal` (`:221-223`). Para o diagnóstico em jogo seria mais útil um motivo próprio (`voice_slot_mismatch`).
- **b) `voiceOnly` descarta proposta nativa alheia:** `:154` faz `native.delete(id)` sem conferir o feitiço; uma proposta nativa pendente de **outro** poder gerenciado (mão) é perdida e esse comando expira. Raro (proposta nativa sem comando só fica pendente até 750 ms). Trocar por `if (native.get(id)?.spell === command.spell) native.delete(id)` — de toda forma `:156` apaga de novo; o ideal é só apagar se for do mesmo feitiço.
- **c) Cobertura:** faltam testes de (1) Lorde por voz com sangue reservado e commit/cancel via `pendingBlood`; (2) poder de vampiro (SPECS) por voz; (3) proposta nativa tardia após voz aceita não gerar `deny` (P2 acima); (4) o caminho completo `input` → `actionVoiceFire` → `request` com `via` (os testes novos chamam `request` direto).
- **d) Pré-existente, não regressão:** com o dispatcher ausente, o evento legado do Lorde (`vampire-lord-service.js:138-145`) ainda executa `start` em `kind === 'cast'` **sem custo de sangue**; o equivalente do lobisomem é fail-closed e testado (`teste-porte-voz.cjs:62-73`). Registrar para alinhar o Lorde ao mesmo fail-closed.
- **e) Documento:** `INVESTIGACAO_18-11_transformacoes.md` §8 e `DECISOES.md` dizem que o Lorde passa a transformar; ajustar após corrigir P1.

## 3. Pontos verificados sem regressão

- **Anti-replay:** nonce/geração/sequência são consumidos em `request` (`:187-188`) **antes** do gate; replay do mesmo payload → `stale_permit` (teste `teste-transformacoes-18-11.cjs:57-63`). `via` é comparado por igualdade estrita (`:190`); qualquer outro valor vira `undefined`. Cadência autoritativa de 1 s (`:140`) mantida; a deduplicação de 250 ms do cliente (`:34`) é só conveniência.
- **Fail-closed da voz Z (porte v0.42):** os dois testes alterados em `teste-porte-voz.cjs` (`:35` e `:56`) trocaram "sem nativo não transforma" por "voz no slot transforma", que é exatamente a decisão. Os casos de segurança permanecem: relato direto `_aetheriusBeast('cast')` → `false` (`:61`), dispatcher ausente/descartado/parcial → nada transforma (`:62-73`), gates SkySocial (`:58-60`), outro ator/forma inválida/geração antiga ignorados (`:50-53`).
- **Journal B1:** a voz chama o mesmo `wolf.start(id, { animate: true, spell })` (`:160`); `werewolf-service.js:168` continua recusando com intenção pendente e `:193-194` grava a recarga com readback. Sem caminho novo.
- **Transformação forçada:** `forced-werewolf.js` chama `wolf.start` diretamente (`:128`) e considera não-saudável quem já está em sessão (`:71`). Agora que o Z de fato transforma, um uso voluntário durante o aviso cancela a sequência e o contador zera pela confirmação de raça (`reset`, `:84-91`) — comportamento previsto pelo desenho; só passa a ser alcançável na prática. Vale incluir no teste em jogo.
- **Custo de sangue do Lorde:** caminho inalterado — `withBlood(..., waitForLord = true)` reserva 5, inicia sessão `confirmed:false` e só faz `commit` quando `lord.isLord(id)` em `tick` (`:203-216`), com cancelamento por personagem/grant/sessão/14 s. Uma segunda tentativa é barrada pela sessão (`gate :136`, `lord.start :88`). Fica correto **quando** P1 for corrigido.
- **indexInPool (`7435282`):** o leitor guarda só `[local, arquivo]` e resolve o `MagicEffect` no quadro (`:21-26`, `:58-67`), em `try` por poder. Custo: até ~9 `getFormFromFile` a cada 100 ms — desprezível.

## 4. Testes executados

Ambiente: Node do sistema, `C:\Code\Aetherius - Servidor Local`. **Atenção:** durante a revisão o working tree estava sendo alterado por outro trabalho em paralelo (não commitado): `power-input.js`, `powers.js`, `teste-poderes.cjs` (overlay e `source/`) e `client-source/.../supernaturalHud.ts`, `sobrenaturalService.ts` e outros.

1. `node --test overlays/sobrenatural/teste-*.cjs` — **primeira execução** (working tree já com `powers.js`/`teste-poderes.cjs` alterados):
   ```
   # tests 323
   # pass 322
   # fail 1      (teste-servico.cjs — script que exige argumento; falha conhecida e documentada)
   ```
   Os 322 = 319 esperados no HEAD + 3 testes novos da alteração não commitada em `teste-poderes.cjs`. Consistente com 319/319 no HEAD.
2. **Execução posterior** (após o trabalho paralelo mexer no cliente):
   ```
   # tests 324
   # pass 321
   # fail 3      teste-servico.cjs (conhecido) +
                 teste-efeitos-veu-cliente.cjs R1 e R5: "supernaturalHud_1.SupernaturalHud is not a constructor"
   ```
   R1/R5 falham de forma determinística (3/3) por causa de `client-source/client/src/sobrenatural/supernaturalHud.ts`, modificado depois dos commits revisados — **não relacionado a L1/V6**; avisar o dono desse trabalho.
3. Suítes do caminho revisado contra os arquivos do **HEAD isolado** (cópia em scratch, `power-input.js` do HEAD): `teste-transformacoes-18-11.cjs`, `teste-porte-voz.cjs`, `teste-poderes.cjs`:
   ```
   # tests 35
   # pass 35
   # fail 0
   ```
4. Werewolf: `node --test source/server/gamemode/werewolf/tests/werewolf.test.cjs`:
   ```
   # tests 29
   # pass 29
   # fail 0
   ```

## 5. O que pedir no teste em jogo (depois de corrigir P1)

1. Lobisomem: Z com Beast Form → esperado `[supernatural-power] command actor=… spell=0x92c48 accepted=true` e `[werewolf] … começou a transformação`. Se não houver linha alguma, P2 (o `actionVoiceFire` não traz o SPEL) está confirmado.
2. Lorde (estágio mínimo + ≥5 de sangue): Z → `command … spell=0x…283b accepted=true`, sangue −5 só após a raça de Lorde confirmar.
3. Lobisomem durante o aviso da transformação forçada: Z → transforma, sequência forçada cancelada, contador zerado.
4. Console do cliente sem `indexInPool` em loop.
