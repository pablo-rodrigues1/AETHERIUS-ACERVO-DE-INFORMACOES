# Revisão Cruzada — T1/T2/T10 (Instrumentação Diagnóstica de Voz Z e Dash do Morcego)

- **Revisor:** Gemini 4 Neon (High)
- **Autor revisado:** Sol High
- **Data:** 19/11/2026
- **Escopo revisado:**
  - Código/testes: commit `bcb0c02f03e5438139b38246e1d6f5828af603db` em `C:\Code\Aetherius - Servidor Local`
  - Documentação: commits `ade130eeaaf1fcd2637b11c0a50b92e2108b0842`, `d58fec223d2d4c017147a3594756b58c36ce68ef`, `84b087b139542ad679121bbf2f6776687da32664`
  - Relatório principal: `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_19-11_voz-z-e-dash.md`
  - Evidências auditadas diretamente: `server-20261119-003042.stdout.log` / `.stderr.log`, `skyrim-platform.log`, `EventHandler.cpp:1619-1691`, `animDebugService.ts`, `spSnippetService.ts` e `026-bat-dash-movement-guard.patch`.

## Veredito

**APROVADO SEM ACHADOS.**

Nenhum defeito funcional, risco de spam/performance, vazamento de dados ou lacuna bloqueante no diagnóstico ou no roteiro de reteste foi encontrado.

---

## 1. Plausibilidade do diagnóstico diante dos logs

Todas as referências factuais do relatório foram conferidas diretamente nos arquivos citados:

- **Log do servidor (`server-20261119-003042.stdout.log` e `.stderr.log`):**
  - T1 (`0x92c48`): confirmados `evidence effect` nas linhas 593, 1087 e 3333; controle via F7 na linha 4229 (`[werewolf-forced] ... accepted=true`) e chegada à raça `0xcdd84` na linha 4235.
  - T2 (`0x200283b`): confirmados `evidence effect` nas linhas 5033 e 6436.
  - T10 (`0x5b68cb8f`): confirmados `evidence effect` nas linhas 4845–4846, 4868–4877 e 5072.
  - Confirmado zero `evidence voice`, zero `[supernatural-power] refused`, zero `[supernatural-power] command ... accepted=`, zero `bat dash ... animation=sent` e zero `bat dash bound rejected` tanto no `stdout` quanto no `stderr`.
  - Confirmado que os `accepted=true` das linhas 4229, 4801 e 5721 pertencem a `[werewolf-forced]` e que as linhas 4866–4867 e 6418–6420 são `ActionListener::OnSpellCast - MpObjectReference not found for spellCastData.target 0` sem FormID.
- **Lacunas reais na revisão anterior (`5effbd8`):**
  - Em `5effbd8:overlays/sobrenatural/files/server/gamemode/supernatural/power-input.js:32,48-50,191`, havia três pontos cegos silenciosos capazes de produzir o mesmo padrão observado (`evidence effect` presente, zero `voice`/`refused`/`command`): (a) ausência de callback `actionVoiceFire`, (b) `actionVoiceFire` com fonte reconhecida mas `!token` (`sp.storage.aetheriusPowerPermit` ausente), e (c) divergência de FormID no servidor (`!recognized(payload.spell)` antes de qualquer `refuse`).
- **Risco A1 no nativo (`EventHandler.cpp:1627-1688`):**
  - Confirmado em `C:\Code\Aetherius - Servidor Local\source\skyrim-platform\src\platform_se\skyrim_platform\EventHandler.cpp:1631,1646-1647,1651`: a lambda passada a `AddUpdateTask` captura o ponteiro cru `const SKSE::ActionEvent* event` e dereferencia `event->slot.get()` / `event->type.get()` assincronamente na fila de update (bug real de tempo de vida/dangling pointer).
  - Confirmado em `C:\Users\pablo\Documents\My Games\Skyrim Special Edition\SKSE\skyrim-platform.log:1,274-277`: o log anuncia `platform_se v2.9.0` sem hash/commit e registra `ProcessEvent TESSpellCastEvent error! spell not a MagicItem` entre `00:43:43` e `00:43:46` (fora das janelas das tentativas), string ausente no `EventHandler.cpp` analisado. A cautela do autor em tratar A1 como hipótese a verificar antes de mexer em binário é adequada.
- **T10 (Morcego):**
  - Confirmado em `powers.js:224-234,264-297`: guarda 026, `actionPolicy` e snippets COTV só executam após o dispatcher entregar o comando a `powers.start`. Logo o teste solo não chegou a medir a guarda nem o deslocamento.

---

## 2. Auditoria da instrumentação (`bcb0c02`)

Verificada paridade byte a byte entre `overlays\sobrenatural\files\server\gamemode\supernatural\{power-input,powers}.js` e `source\server\gamemode\supernatural\{power-input,powers}.js`, além de `INVESTIGACAO_19-11_voz-z-e-dash.md` entre laboratório e `Sobrenatural\docs\investigacoes\`.

### Spam e performance
- **`power-input.js` (cliente, `:30-35,50-72`):** `trace(kind, spell)` impõe cadência de 1 s por `kind`. `spellCast` (`:55`) e `actionSpellFire` (`:67`) filtram por `0x14` (jogador local) e por `powers` gerenciados, evitando qualquer tráfego para feitiços comuns ou NPCs. `actionVoiceFire` (`voice`) e `actionVoiceCast` (`voice_cast`) filtram por `0x14`, disparam apenas em ação discreta de voz (Z) e respeitam a cadência de 1 s. O mapa `sent` mantém no máximo 5 chaves `string` + os FormIDs de `DEFINITIONS` (crescimento limitado).
- **`power-input.js` (servidor, `:192-208,247-249`):** `observe` restringe `kind` a lista branca fechada de 8 literais e exige `Number.isSafeInteger(value)`, com cadência > 1 s por `id:kind` e limpeza periódica em `tick()` após 60 s. `refuse(id, 'unrecognized_spell', payload.spell)` usa cadência de 1 s por `id:reason`, também limpa em 60 s.
- **`powers.js` (`:220-234,264-268,288-291`):** `batRefused` não possui cadência própria, mas só executa em caminhos já gated por `power-input.js:request` / `complete` (onde `command ... accepted=false` já emitia uma linha por recusa na linha `:189`), não abrindo novo vetor de spam.

### Vazamento de dados
- Nenhum log novo expõe keywords permanentes (`JogadorVampiro`, `JogadorLobisomem`, `JogadorBruxa`), tokens/nonces/generations de permit, credenciais ou dados pessoais. Apenas `actor=0x...`, FormIDs numéricos e motivos literais.

### Mudança de comportamento
- **`power-input.js`:**
  - `trace` usa chaves `string` em `sent`, enquanto `activate` usa o FormID numérico (`number`), sem colisão no `Map` — a cadência diagnóstica não bloqueia nem atrasa `activate` (`:30-49`).
  - `trace` isola erros em `try {} catch {}`, não interrompendo o fluxo de `activate`.
  - `actionVoiceCast` e `actionSpellFire` são estritamente observacionais (não chamam `activate`, não consomem permit/nonce, não alteram forma e não debitam sangue).
  - `unrecognized_spell` (`:208`) é avaliado antes da rotação do nonce (`:211`) e preserva o retorno `false`.
- **`powers.js`:** todas as condições de recusa em `start`, `moveBat` e `queueMicrotask` mantêm ordem de avaliação, efeito colateral e retorno idênticos à revisão anterior.
- **Testes reexecutados na revisão:** `node --test overlays/sobrenatural/teste-poderes.cjs overlays/sobrenatural/teste-porte-voz.cjs overlays/sobrenatural/teste-transformacoes-18-11.cjs` → **52/52 passando**.

---

## 3. Suficiência do roteiro de reteste

O roteiro em `INVESTIGACAO_19-11_voz-z-e-dash.md:39-45` separa adequadamente todas as hipóteses em aberto:

| Hipótese | Evidência esperada no reteste |
|---|---|
| `ActionEvent` quebrado no binário SP (ex.: A1) vs. especificidade de Poderes/LesserPowers | `evidence effect` sem `voice`/`voice_cast`/`spell_fire`/`spell_cast` + comparação com o **grito comum** (passo 3). |
| `actionVoiceFire` chega com fonte nula ou inesperada | `evidence voice ... value=0x0` (ou FormID divergente). |
| Permit ausente no cliente (`!token`) | `evidence voice` + `evidence permit_missing`. |
| Divergência de FormID / load order cliente↔servidor | `refused ... reason=unrecognized_spell`. |
| Recusa de canal, slot, gate ou proposta nativa | `refused ... reason=stale_permit \| no_channel \| gate \| gate_at_join \| no_native_proposal` (com pausa de 2 s após equipar para descartar atraso de sync). |
| T10 barrado antes do envio da animação | `bat dash ... phase=start\|dispatch refused reason=action_policy \| guard_unavailable \| guard_busy \| guard_readback \| activation_invalidated`. |
| T10 enviado mas rejeitado pelo cliente/grafo ou pelo limite da guarda 026 | `guard=armed animation=sent` + `AnimDebugService` (`animDebugService.ts:32-48`), log `SpSnippet Debug SendAnimationEvent failed` (`spSnippetService.ts:60`) ou `bat dash bound rejected` (`026-bat-dash-movement-guard.patch:71,151`). |

---

## Observações opcionais (não bloqueantes)

1. **Ativação do `AnimDebugService` (passo 4):** para facilitar a execução em jogo, as flags `animDebug.isActive` e `animDebug.textOutput.isActive` ficam em `skymp5-client-settings.txt`, e o HUD exibe o nome do evento em **branco** quando `animationSucceeded === true` e **vermelho** quando `false` (`animDebugService.ts:12,60-61`).
2. **Silêncio esperado de `reader ready`:** em `power-input.js:205`, `[supernatural-power] reader ready` só é logado se `!channels.has(id)`. Quando o `tick()` de 250 ms cria o canal antes de o pacote `ready` chegar, a linha não é emitida mesmo com o leitor operando normalmente (como ocorreu no log de 19/11). Não tratar essa ausência como falha do leitor no reteste.
3. **`actionSpellCast`:** o commit instrumenta `spellCast`, `actionVoiceFire`, `actionVoiceCast` e `actionSpellFire`, deixando de fora `actionSpellCast` (`EventHandler.cpp:1673-1675`). Como os usos registrados completaram e aplicaram o `MagicEffect`, é improvável que apenas `kSpellCast` dispare sem nenhum dos outros quatro eventos; só valeria considerar em uma rodada posterior caso todos os quatro venham zerados enquanto o grito comum funcione.
