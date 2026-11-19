# REVISÃO INDEPENDENTE — Correção L1/V6 (`7435282`, `77ea350` e `850211c`)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura no código)
- **Autor revisado:** Opus 5.5 Low (com commit intermediário `415acde` de trabalho paralelo nos 6 poderes vampíricos)
- **Data:** 18/11/2026
- **Laboratório auditado:** `C:\Code\Aetherius - Servidor Local`
- **Commits sob revisão:**
  - `74352820b01cf2255c86a69c449bc298e102e1fb` (`fix(sobrenatural): indexInPool no leitor de poderes e log das recusas (L1/V6)`)
  - `77ea350f5688251f614806f11f5ef698d42ef1e2` (`feat(sobrenatural): Opcao A - voz Z executa poder sem SpellCast nativo (L1/V6)`)
  - `850211ce3c6886e8a90363d15a0dd86bc20162e8` (`fix(sobrenatural): Lorde por voz Z, evidencia voice, deny tardio e feeding sem Actor guardado`)
- **Documentos conferidos:** `AGENTS.md`, `Sobrenatural/docs/TESTE-JOGO_18-11.md`, `Sobrenatural/docs/investigacoes/INVESTIGACAO_18-11_transformacoes.md` (§1–§8.1), `Sobrenatural/docs/DECISOES.md` (18/11 Opção A) e log `C:\Code\Aetherius - Servidor Local\logs\server-20261118-222056.stdout.log`.
- **Paridade `overlays` ↔ `source` ↔ `runtime`:** em `850211c`, `overlays/sobrenatural/files/server/gamemode/supernatural/power-input.js` e `feeding-client.js` são byte-idênticos às cópias em `source/server/gamemode/supernatural/`. O `runtime/server/gamemode/supernatural/power-input.js` ainda está na versão pré-`7435282` e precisará de Build/Prepare antes do teste em jogo.

---

## Veredito

- **Sobre `7435282` + `77ea350` (antes de `850211c`):** **REPROVADO** — bloqueador crítico em V6 (`DLC1VampireChange`, `Dawnguard.esm 0x283b`, mantinha `voice: false` em `power-input.js:23` e era descartado por `actionVoiceFire` no cliente; inclusive após `415acde`).
- **Sobre o estado final em `850211c`:** **APROVADO COM CONDIÇÃO DE VALIDAÇÃO EM JOGO (A1).**
  - O bloqueador de V6 foi corrigido em `850211c` (`power-input.js:6-9,25,47-56`) com teste ponta-a-ponta do cliente (`input` + `actionVoiceFire`) até `vampire-lord-service` + reserva de sangue.
  - A validação server-side da Opção A cobre integralmente todos os requisitos de segurança/autoridade, sem nenhum caminho que permita ao cliente transformar sem satisfazer todas as condições.
  - Sem regressão no fail-closed da voz Z (porte v0.42), no journal B1 (`werewolf-transform-journal.js`) nem na transformação forçada (`forced-werewolf.js`).
  - `indexInPool` está sanado tanto em `power-input.js` (`7435282`) quanto no achado colateral em `feeding-client.js` (`850211c`).
  - **Condição operacional para fechar L1/V6 de vez:** preparar o `runtime/` e confirmar em jogo o disparo de `actionVoiceFire` (ver A1 abaixo; agora o servidor já registra `evidence voice` para fechar o diagnóstico na primeira tentativa).

---

## Avaliação das correções de `850211c`

| Item | O que foi corrigido em `850211c` | Verificação |
|---|---|---|
| **B1 / P1 — V6 (Lorde Vampiro) por voz Z** | `power-input.js:6-9,25`: `voice: true` explícito em `DEFINITIONS` para `Skyrim.esm` `0x92c48` (`WerewolfChange`), `0xf8306` (`WerewolfChangeRingOfHircine`) e `Dawnguard.esm` `0x283b` (`DLC1VampireChange`); `voice: !!d.voice || !!d.kind` (mantendo também os 6 poderes de `powers.js` vindos de `415acde`). | **CORRIGIDO.** `teste-transformacoes-18-11.cjs:86-93` exercita `powers.input` (`actionVoiceFire` com `0x200283b`) → `activate(via:'voice')` → `lord.start` + reserva de 5 de sangue (`accepted=true`). |
| **Diagnóstico `voice` (`P2a`)** | `power-input.js:50-55,179-183`: quando `actionVoiceFire` dispara no jogador (`0x14`) com fonte nula ou não gerenciada, cliente envia `('voice', spell || 0)` (limitado a 1/s); servidor aceita `'voice'` em `observe()` e loga `[supernatural-power] evidence voice actor=... value=0x... (no transition).` (limitado a 1/s, sempre retorna `false`). | **APROVADO.** Nunca dispara transição; rate-limited nos dois lados e limpo em `tick()` (`:232`). Resolve a ambiguidade diagnóstica de A1 no teste em jogo. |
| **M1 / P2b — `deny()` em `SpellCast` tardio do Lobisomem** | `power-input.js:119-120`: `deny()` agora também retorna sem publicar `rejected` (nem `dispelSpell`) quando `mp.__aetheriusWerewolf?.BEAST_SPELLS.includes(spell) && mp.__aetheriusWerewolf.sessions.has(id)`. | **CORRIGIDO.** Verificado em `teste-transformacoes-18-11.cjs:101-113`. |
| **I1 / P2c — `indexInPool` em `feeding-client.js`** | `feeding-client.js:25,29,35,41`: guarda apenas `actorId: local` e `targetId: target.getFormID()` (números) e re-resolve ambos via `sp.Actor.from(sp.Game.getFormEx(formId))` no quadro da limpeza (`registry.clean`). | **CORRIGIDO.** `teste-alimentacao.cjs` (62/62) e `teste-transformacoes-18-11.cjs:114-118` passam. |

---

## Achados remanescentes (não bloqueantes para o código; A1 obrigatório em jogo)

### [ALTO / RISCO EMPÍRICO] A1 — Log real de 18/11 contradiz a hipótese sobre `equipAbility` no período Lobisomem; confirmar `actionVoiceFire` no motor

- **Onde:**
  - `Sobrenatural/docs/investigacoes/INVESTIGACAO_18-11_transformacoes.md:39`
  - `C:\Code\Aetherius - Servidor Local\logs\server-20261118-222056.stdout.log:465`
  - `C:\Code\Aetherius - Servidor Local\runtime\server\gamemode\werewolf\client.js:35-43`
- **Evidência:**
  - `INVESTIGACAO_18-11_transformacoes.md:39` afirmou que não houve `evidence cast` no período Lobisomem (22:25:37–22:27:14) porque *"as requisições `spells equipAbility` só começam às 22:28:00, já como vampiro"*.
  - Porém, a linha **465** do log real registra:
    ```
    [2026-11-18 22:25:49.974] [console] [info] [AetheriusUI] request spells equipAbility 14f2ce6d-0807-4908-b9fd-56180f955c74
    ```
    — 12 s após conceder Lobisomem (`22:25:37.758`), coerente com `TESTE-JOGO_18-11.md:13` (dev equipou Beast Form, apertou Z e viu o ícone do efeito).
  - Naquela sessão, `runtime/server/gamemode/werewolf/client.js:35-43` **já ouvia `actionVoiceFire`** (estando carregado e comunicando, provado pelos `evidence race` nas linhas 212, 2007 e 2065) e **nenhum `evidence cast` foi registrado**.
- **Impacto e ação:**
  - Há risco real de `actionVoiceFire` no Skyrim Platform não disparar para LesserPower/Power ou vir com `source` diferente do `SPEL`.
  - Com `850211c`, o primeiro teste em jogo (após Build/Prepare do `runtime/`) responde de imediato:
    - `command actor=... accepted=true` → L1/V6 resolvidos;
    - `evidence voice actor=... value=0x...` → `actionVoiceFire` dispara, mas com outra forma/nulo;
    - silêncio total → `actionVoiceFire` não dispara para Z em poderes (exigirá outro gatilho, como `actionVoiceCast` ou `MagicEffect` em `effect`).

### [BAIXO / ENDURECIMENTO] M2 — `via: 'voice'` retido em `pending` quando o poder não está no slot de voz em `request()`

- **Onde:** `overlays/sobrenatural/files/server/gamemode/supernatural/power-input.js:156,160,196`
- **Detalhe:** se um cliente enviar `{ spell: A, via: 'voice' }` enquanto `A` está em `rightSpell` (e não em `voiceSpell`), `gate()` passa e `pending` retém `via: 'voice'` por 750 ms; se dentro dessa janela o jogador mover `A` para `voiceSpell` e disparar `SpellCast` de outro poder gerenciado `B`, `complete()` executa `A` via `voiceOnly`. Não há escalação de privilégio (todas as autoridades server-side são válidas na execução), mas é mais limpo só gravar `via: 'voice'` quando `get(id, 'equipment')?.voiceSpell === payload.spell` já no `request()`.

### [BAIXO / PRÉ-EXISTENTE] M3 — Assimetria de fail-closed em `vampire-lord-service.js` quando dispatcher está ausente

- **Onde:** `source/server/gamemode/vampire-lord-service.js:138-151` vs `source/server/gamemode/werewolf-service.js:316-322`
- **Detalhe:** pré-existente (não introduzido por `7435282`/`77ea350`/`850211c`). Diferente de `werewolf-service.js:319-321` (P1 Terra: falha fechado sem `mp.__aetheriusPowerActions.observe`), `vampire-lord-service.js:139` ainda cai no fallback legado se o dispatcher estiver ausente/descartado. Em produção `supernatural-service.js:300` sempre instala `power-input` na sequência, mas vale alinhar futuramente.

### [BAIXO / COBERTURA & RUÍDO] M4 — Itens opcionais de acabamento

- **Onde:** `overlays/sobrenatural/teste-transformacoes-18-11.cjs:6-118`, `power-input.js:54,179-183`
- **Detalhes:**
  1. Em `teste-transformacoes-18-11.cjs`, o caminho do Lorde por voz testa o caso feliz (`accepted=true`, reserva de 5 de sangue); sangue < 5 / módulo ausente / `VAMPIRE_LORD_MIN_STAGE` continuam cobertos via `teste-poderes.cjs` (mesmo `complete()`/`withBlood()`), mas seria útil espelhar ao menos um teste negativo de sangue insuficiente diretamente na rota `via: 'voice'`.
  2. No teste de `indexInPool` (`:6-19`), não há injeção de exceção no primeiro poder para validar que o segundo continua rodando (a proteção `try/catch` por poder em `power-input.js:65-71` está correta por inspeção).
  3. O log `evidence voice` (`power-input.js:54,179-183`) dispara (no máximo 1/s por ator) também para jogadores sem maldição usando Shouts comuns; excelente agora para diagnóstico em jogo, mas pode ser limitado a `channels.has(id)` após fechar A1 para evitar ruído no log em produção.

---

## Matriz de verificação server-side (`power-input.js` + domínios em `850211c`)

| Condição verificada | Status | Evidência (`arquivo:linha`) |
|---|---|---|
| **Maldição** | **OK** | `power-input.js:147-151` (`curse === 'werewolf'` / `'vampire'`); `werewolf-service.js:169` (`keywordOf === 'JogadorLobisomem'`); `supernatural-service.js:295` (`curse === 'vampire'`); `powers.js:109`. Bruxa/Ritual excluídos. |
| **Poder concedido / equipado** | **OK** | `power-input.js:143-145` (`knownSpells` + `equipment`), `:156` (`equipment.voiceSpell === command.spell`); reconfirmado em `werewolf-service.js:175`, `vampire-lord-service.js:89`, `powers.js:109`. |
| **Estágio / `minXp`** | **OK** | Poderes vampíricos: `power-input.js:151` e `powers.js:109` (`state.xpTotal >= power.minXp`). Lorde Vampiro: `vampire-lord-service.js:89` chama `canUse(id)` (`supernatural-service.js:293-296`: `stageOf(c.xpTotal) >= vampire.VAMPIRE_LORD_MIN_STAGE`); falha em `canUse` faz `withBlood` cancelar a reserva sem cobrar sangue (`power-input.js:92`). |
| **Slot (`voiceSpell`)** | **OK** | `voiceOnly` (`power-input.js:156`) exige `command?.via === 'voice' && get(id, 'equipment')?.voiceSpell === command.spell`. Mãos e instant continuam exigindo proposta nativa `SpellCast` em até 750 ms (`:161`). |
| **Vivo (`isDead` / `skyDeath`)** | **OK** | `power-input.js:139-141` (`get(id, 'isDead') !== false`, `death && death.phase !== 'ALIVE'`); reconfirmado nos domínios (`werewolf-service.js:74-78`, `vampire-lord-service.js:68-72`, `powers.js:76`). |
| **Travas** | **OK** | `power-input.js:141`: `skySocialInventoryLocked`, `aetheriusProfessionInventoryLocked`, `mp.__skySocial?.isLocked?.(id)` (profissão, journal de troca e feeding em `skysocial/src/index.js:16`) e `mp.__skySocial?.byTrader?.has(id)`. |
| **Forma ativa** | **OK** | `power-input.js:142`: bloqueia se já houver sessão em `__aetheriusWerewolf.sessions`, `__aetheriusVampireLord.sessions` ou `__aetheriusVampirePowers.mist(id)`. |
| **Canal / ator / personagem** | **OK** | `power-input.js:137-139,163,187-193,224-226`: `disposed`, `mp.__aetheriusPowerActions !== api`, `online(id)`, `channels.get(id)`, `channel.character === characterOf(id)`, `payload.generation === channel.generation`. |
| **Cadência (1 s)** | **OK** | `power-input.js:146,172`: bloqueia usos com `now() - lastUse < 1000` (atualizado apenas quando `accepted === true`). |
| **Recarga do domínio** | **OK** | Lobisomem: `werewolf-service.js:171-174,193-195` (`aetheriusBeastCooldownUntil` + readback). Poderes vampíricos: `powers.js:123-129` (`aetheriusPowerCooldowns` + readback). |
| **Sangue do Lorde** | **OK** | `power-input.js:81-105,168-171,209-222`: `withBlood` falha fechado se `__aetheriusBloodReserve` faltar/estiver incompleto; reserva 5 de sangue, aguarda `lord.isLord(id)` por até 14 s (`pendingBlood`), faz `commit` somente após confirmação da fase `lord` e `cancel` + `rollback` em falha/timeout/desconexão/troca de personagem ou `grantId`. |
| **Nonce / seq anti-replay** | **OK** | `power-input.js:193-194`: exige `generation`, `nonce` e `sequence === channel.sequence + 1`, rotacionando `sequence` e `nonce` imediatamente antes do `gate()`. Replay falha com `stale_permit`. |
| **Hooks anteriores (`oldCast`)** | **OK** | Na rota `via: 'voice'`, `oldCast` não é chamado; todos os hooks registrados em `source/server/gamemode/` (`skysocial/src/index.js:41` e `magic-service.js:137-141`) foram auditados e nenhum veta poderes gerenciados (`magic-service.js:141` retorna `true` para `owns(spell)`). |

---

## Verificação de regressões e `indexInPool`

1. **Fail-closed da voz Z (porte v0.42):** **sem regressão.** `werewolf-service.js:316-322` intocado; sem dispatcher (`absent`/`disposed`/`partial`), `mp._aetheriusBeast` continua retornando `false`. Os ajustes em `teste-porte-voz.cjs:35,53,56` refletem exatamente a Opção A (`DECISOES.md:196`) e o pacote diagnóstico `'voice'` (sem transição).
2. **Journal B1 (`werewolf-transform-journal.js`):** **sem regressão.** Intocado; `prepare`/`confirm`/`abort`/`recovery` e `hasPending` íntegros.
3. **Transformação forçada (`forced-werewolf.js`):** **sem regressão.** Intocado; continua acionando `mp.__aetheriusWerewolf.start` exclusivamente no servidor e zerando em `onTransformed`.
4. **`indexInPool` (objetos nativos entre quadros):** **limpo em todo o caminho e em `feeding-client.js`.**
   - `power-input.js`: `:15` resolve `Spell` no quadro; `:23-27` guarda apenas `id` e `[effectLocal, effectFile]`; `:66-70` resolve `MagicEffect` e `player` no quadro dentro de `try/catch` por poder.
   - `feeding-client.js`: `:29,35,41` agora guarda apenas `actorId` e `targetId` e re-resolve `Actor` por quadro em `registry.clean`.
   - `werewolf/client.js`, `vampire-lord-service.js`, `forced-werewolf-client.js`, `powers.js`, `supernatural/client.js`, `blood-effects-client.js`, `veil-ritual-fx.js`: nenhum objeto nativo retido entre quadros.
   - Logs de recusa (`power-input.js:108-112,227-230`): limitados a 1/s por `ator:motivo`, limpos após 60 s, disparados apenas após `online(id)` e `recognized(payload.spell)`.

---

## Saída dos testes (após `850211c`)

### 1. Bateria completa do estado commitado (`overlays/sobrenatural/teste-*.cjs` + `werewolf.test.cjs`)

Desconsiderando exclusivamente os 4 arquivos de trabalho paralelo não commitado / script CLI (justificados pelo coordenador e confirmados na execução):
- `teste-servico.cjs` (script CLI que exige caminho por argumento, inalterado)
- `teste-ritual-hud-nativo.cjs` (arquivo novo não rastreado de trabalho paralelo)
- `teste-sangue-hud.cjs` (falha por edição não commitada de `blood-presentation.js` no working tree)
- `teste-veu-mundo.cjs` (falha por edição não commitada de `veil-ritual-fx.js` no working tree)

Resultado da bateria commitada + `werewolf.test.cjs`:
```
1..351
# tests 351
# suites 0
# pass 351
# fail 0
# cancelled 0
# skipped 0
# todo 0
# duration_ms 1209.5731
```

### 2. Suítes individuais do escopo L1/V6, voz Z, alimentação e regressões

| Suíte | Arquivo | Resultado |
|---|---|---|
| L1/V6 (`7435282` + `77ea350` + `850211c`) | `overlays\sobrenatural\teste-transformacoes-18-11.cjs` | **13/13 pass, 0 fail** |
| Porte voz Z (v0.42 + P1 Terra) | `overlays\sobrenatural\teste-porte-voz.cjs` | **12/12 pass, 0 fail** |
| Formas (Fera + Lorde + gates) | `overlays\sobrenatural\teste-formas.cjs` | **17/17 pass, 0 fail** |
| Poderes vampíricos + reserva | `overlays\sobrenatural\teste-poderes.cjs` | **18/18 pass, 0 fail** |
| Transformação forçada + journal B1 | `overlays\sobrenatural\teste-transformacao-forcada.cjs` | **29/29 pass, 0 fail** |
| Alimentação (`feeding-client.js`) | `overlays\sobrenatural\teste-alimentacao.cjs` | **62/62 pass, 0 fail** |
| Lobisomem (kdu + prata + cliente) | `source\server\gamemode\werewolf\tests\werewolf.test.cjs` | **29/29 pass, 0 fail** |
