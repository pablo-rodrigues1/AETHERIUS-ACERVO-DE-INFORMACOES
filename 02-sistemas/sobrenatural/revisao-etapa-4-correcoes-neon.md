# Revisão independente: Correções A1, B2, B3 e C1 (Etapa 4 — Vínculo do Véu)

**Revisor:** Gemini 4 Neon (High), revisão cruzada independente (`task_3cef31d69491`). **Autor revisado:** Opus 5.5 Low (`task_0890c3c11f2b`). · **Data:** 18/11/2026 · **Modo:** somente leitura (nenhum código, servidor, MO2 ou jogo alterado/executado).
**Commits revisados:**
- Cliente (`C:\Code\Aetherius - Servidor Local\client-source`): `b7f92f1` (comparado contra `c27afd4`, base `e82ac03` e raiz `bc305e5`).
- Laboratório (`C:\Code\Aetherius - Servidor Local`): `c65b1e7` (e conferência do patch incremental `1ba58ee` → `overlays\cliente-local\021-isolamento-veu.patch`).
- Referências lidas: `AGENTS.md`, `Sobrenatural\RETOMADA.md`, `Sobrenatural\docs\REVISAO-ETAPA-4_opus.md`, `Sobrenatural\docs\ETAPA-4_bruxaria-vinculo-do-veu.md` (seção de correções), `Sobrenatural\docs\DECISOES.md` (regra de 18/11).

---

## Veredito

**REQUER CORREÇÃO antes do teste em jogo e da compilação final.**

| Item | Situação | Resumo |
|---|---|---|
| **A1 — Isolamento do núcleo** | **✅ Aprovado (estrutura)** / **⚠️ Com 1 regressão funcional (R1)** | Diff fora do Sobrenatural ficou restrito à integração mínima (`index.ts` +2, `spSnippetService.ts` +3, `index.html` +7/−1, `modules.json` +16). `shell.js`, `actorvalues.ts` e `aetheriusUiService.ts` voltaram integralmente ao base `e82ac03`/`bc305e5`. Fechamento do Ritual preservado. Porém o novo `SobrenaturalService` introduziu corrida de login que zera a Magicka do Véu no primeiro `PACKET_SESSION` (**R1**). |
| **B2 — Magicka efetiva / Fortificar / Estágio V** | **❌ Reprovado (R2, R3, R4)** | Estágio V (`0.01` e regen `0`, sem divisão por zero) e compensação básica funcionam no fixture isolado, mas há **dois defeitos de alta severidade** na interação com o servidor e com `actorvalues.ts` (**R2** e **R3**) e um **efeito colateral de design** no Estágio III e na Sobrecarga (**R4**). |
| **B3 — Reaplicação após `loadGame`** | **⚠️ Parcial (bloqueado por R2)** | Não duplica efeitos nem causa replay de terror ou crash. Porém `onLoadGame()` zera `vanilla` e `update()` recaptura a base crua do save (sem bônus de classe), acionando **R2** e prendendo a supressão de Fortificar mesmo após retornar ao perfil normal. |
| **C1 — Plugins (`MasterSpellsRunes` / `SkyDeath-Ghosts`)** | **✅ Confirmado** | Ambos presentes e ativos em todos os perfis MO2, espelhos de `client-source` e `runtime\server\data\aetherius-loadorder.json`, com `MasterSpellsRunes.esp` antes de `AETHERIUS - SOBRENATURAL.esp`. |

---

## Verificação detalhada por frente

### 1. A1 — Diff do núcleo e fechamento do Ritual

Verificação via `git diff e82ac03..b7f92f1` em `C:\Code\Aetherius - Servidor Local\client-source`:
- **Revertidos integralmente ao base** (0 linhas de diferença contra `e82ac03` **e** contra a raiz `bc305e5`):
  - `client\ui-core\frontend\shell.js`
  - `client\src\sync\actorvalues.ts`
  - `client\src\services\services\aetheriusUiService.ts`
  - `client\src\services\services\veilEffects.ts` removido (movido para `client\src\sobrenatural\veilEffects.ts`).
- **Únicos arquivos fora de `client\src\sobrenatural\*` e `client\ui-core\modules\supernatural\*` alterados desde `e82ac03`**:
  - `client\src\index.ts` (**+2/−0**, linhas 44 e 136): import e registro de `SobrenaturalService`. Integração mínima.
  - `client\src\services\services\spSnippetService.ts` (**+3/−0**, linhas 12, 68–69): import e delegação `this.controller.lookupListener(SobrenaturalService).runSnippet(...)`. Justificado como integração mínima: como `SpSnippetService` não possui registro dinâmico e cairia em `runStatic` (exceção em `spAny["AetheriusVeil"]`), um gancho de 2 linhas é o mínimo possível.
  - `client\index.html` (**+7/−1**, linhas 1328 e 1335–1339): 2 `<link>`, 3 `<script>` (bloco inline movido para `veil-hud.js`; regra CSS de ocultação movida para `veil-seal.css:36`) e a guarda `window.aetheriusVeilHudActive||` dentro de `setBloodReserveHud` (que já é HUD do próprio Sobrenatural). Integração mínima.
  - `client\ui-core\modules.json` (**+16/−0**): registro do módulo `supernatural` no slot 8 (permitido por `DECISOES.md:186`).
- **Fechamento do Ritual (`client\ui-core\modules\supernatural\supernatural-module.js:54-58`)**:
  - `closeShell()` chama `cleanup()`, depois `ui.nativeFocusChanged(false)` e `window.aetheriusUiSetFocus(JSON.stringify(false))`.
  - No `shell.js:704-717`, `nativeFocusChanged(false)` zera `focusRequest`/`nativeFocused` e chama `setState({ kind: 'gameplay', selectedId: null })`.
  - Em `shell.js:362-365`, a transição `workspace → gameplay` **executa `unmountEntry(oldEntry)`** (limpa `entry.active`, aborta `active.controller`, chama `active.disposers` e chama `cleanup()` — idempotente por `if (disposed) return;`). Em seguida, `aetheriusUiSetFocus("false")` dispara `OnSetFocus` → `CloseMainView()` em `client\ui-core\native\src\main.cpp:210-224,319-334`, removendo foco, escondendo a view e emitindo `AetheriusUI.FocusState = "false"`.
  - Comportamento funcional preservado (pendente validação no CEF). Ver ressalva documental **R6**.

### 2. C1 — Conferência independente em disco

| Arquivo | `MasterSpellsRunes.esp` | `AETHERIUS - SOBRENATURAL.esp` | `SkyDeath-Ghosts.esp` |
|---|---|---|---|
| `C:\modOrganizer\profiles\AETHERIUS - LOCAL\plugins.txt` | linha 133 (`*`) | linha 255 (`*`) | linha 418 (`*`) |
| `C:\modOrganizer\profiles\AETHERIUS - GRAFICO - QUALIDADE\plugins.txt` | linha 133 (`*`) | ausente (esperado) | linha 418 (`*`) |
| `C:\modOrganizer\profiles\AETHERIUS - PERFORMANCE\plugins.txt` | linha 133 (`*`) | ausente (esperado) | linha 418 (`*`) |
| `client-source\client\profiles\AETHERIUS - LOCAL\plugins.txt` | linha 133 (`*`) | linha 255 (`*`) | linha 418 (`*`) |
| `client-source\client\profiles\{QUALIDADE,PERFORMANCE}\plugins.txt` | linha 133 (`*`) | ausente (esperado) | linha 416 (`*`) |
| `runtime\server\data\aetherius-loadorder.json` | linha 149 | linha 271 | linha 434 |

Ordem correta em todos os ambientes ativos: `MasterSpellsRunes.esp` sempre precede `AETHERIUS - SOBRENATURAL.esp`.

---

## Achados priorizados

### R1 — ALTA (dessincronização no login, regressão de A1/B2). Primeiro `PACKET_SESSION` zera a Magicka do Véu já confirmada por ACK

**Onde:**
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\sobrenaturalService.ts:43-46`
- `C:\Code\Aetherius - Servidor Local\source\server\ts\systems\aetheriusUiSystem.ts:371-396`
- `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\aetherius_combat\DevAttributes.cpp:118-120,148-159`

**Causa:**
```ts
// sobrenaturalService.ts:43-46
if (content.customPacketType !== PACKET_SESSION || content.protocolVersion !== 1 ||
  typeof content.sessionId !== "string" || !SESSION_ID.test(content.sessionId)) return;
if (this.sessionId !== content.sessionId) { this.effects.reset(); this.magicka.reset(); }
this.sessionId = content.sessionId;
```
No login, `this.sessionId` começa em `null`. No servidor, `aetheriusUiSystem.ts:376-380` faz polling a cada **500 ms** esperando `whitelist.js` registrar o personagem antes de emitir `PACKET_SESSION`. Enquanto isso, assim que o personagem existe, `veil-visuals.js` (ciclo de 1 s) já pode sincronizar `aetheriusVeilMagicka`, enviando `SpSnippet("AetheriusVeil", "ApplyMagicka", ...)`:
1. `VeilMagicka.apply()` executa no cliente, aplica penalidade/bônus e devolve `true`.
2. O servidor recebe o ACK e marca `actor->veilMagickaPending = false` (`DevAttributes.cpp:156`).
3. Até 500 ms depois, chega o primeiro `PACKET_SESSION` (`content.sessionId = "<uuid>"`).
4. `null !== "<uuid>"` avalia `true` → `sobrenaturalService.ts:45` chama `this.magicka.reset()`, que limpa `this.target = null` e restaura `Magicka` e `MagickaRate` vanilla.
5. Como `modifiers` não mudaram e `IsVeilMagickaSyncPending()` já é `false`, `DevAttributes.cpp:120` retorna sem reenviar. A bruxa passa a jogar **sem** a penalidade (Estágios III–V) ou **sem** o bônus (Sobrecarga) no cliente até cruzar outro limiar de Vínculo.

**Correção sugerida:** Só resetar em troca real de sessão (`if (this.sessionId !== null && this.sessionId !== content.sessionId)`), mantendo o reset completo em `connectionDisconnect`, `connectionFailed`, `connectionDenied`, `uiDisconnect` e adicionando `connectionAccepted` (ver **R5**).

---

### R2 — ALTA (B2 e B3 — dessincronização permanente). Retorno ao perfil vanilla por `maximum === vanilla.base && rate === vanilla.rate` falha com bônus de classe, `skyDeathAidWeakness` e após `loadGame`

**Onde:**
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\veilMagicka.ts:28-31,50-59,68-83`
- `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\MpActor.cpp:1724-1752`
- `C:\Code\Aetherius - Servidor Local\source\server\cpp\server_guest_lib\aetherius_combat\DevAttributes.cpp:40-68,140-147`

**Causa:**
`veilMagicka.ts:51` tenta inferir se o perfil enviado pelo servidor é o perfil vanilla (`maximumPercent === 100 && regenPercent === 100`) comparando:
```ts
if (maximum === vanilla.base && rate === vanilla.rate) {
  this.target = null; ...
```
Porém `MpActor::GetMaximumValues()` (`MpActor.cpp:1726-1745`) compõe `magicka` a partir de `GetBaseValues()` + `aetheriusAttributeBonuses` (classe) / `DevAttributes` × `0.5` (se `skyDeathAidWeakness`) × `maximumPercent / 100`. Isso falha de forma determinística em três cenários reais:
1. **Após `loadGame` sem desconexão (B3):** `onLoadGame()` (`veilMagicka.ts:81`) zera `this.vanilla = null`. No primeiro `update()` (`veilMagicka.ts:74`), o cliente recaptura `player.getBaseActorValue("Magicka")` recém-restaurado do save (ex.: `100`, sem o bônus de classe `+50` que o servidor não reenviou). Quando a bruxa sai do Estágio IV para Estágio I/II — ou tem a maldição removida — o servidor envia `ApplyMagicka(150, rate, current)`. Como `150 !== 100`, `this.target` **nunca é limpo**; `enforce()` mantém `target = { maximum: 150, rate }` e continua suprimindo todo Fortificar Magicka pelo resto da sessão.
2. **Mudança de bônus de classe/atributo ou fim de `skyDeathAidWeakness` durante Véu ativo:** `SetAttributeBonuses` chama `SendAttributeMaximumValues()` (`DevAttributes.cpp:57`), que envia `Actor.SetActorValue("Magicka", ...)` e **não** `AetheriusVeil.ApplyMagicka`. Em até 250 ms, `VeilMagicka.update()` sobrescreve a Magicka de volta para o `this.target.maximum` obsoleto. E ao voltar para Estágio I/II, o novo `maximum` diverge do `vanilla.base` antigo, novamente nunca limpando `this.target`.
3. **Precisão `float`→`double` e ordem de login:** `DevAttributes.cpp:145-146` converte `float` para `double`, e a comparação usa `===` sem tolerância; se `ApplyMagicka` rodar antes de `SendAttributeMaximumValues`, `vanilla.base` captura o valor pré-classe.

**Correção sugerida:** O servidor (addon 025, ainda em compilação pelo Sol) deve enviar explicitamente se o perfil está modificado (ex.: 4º argumento booleano `modified`, ou `maximumPercent`/`regenPercent`) — ou um contrato explícito de limpeza — em vez de o cliente adivinhar comparando com `vanilla.base`; e `SendAttributeMaximumValues()` não deve conflitar com `VeilMagicka` (ou deve re-acionar a sincronização do Véu quando `GetVeilMagicka()` estiver modificado).

---

### R3 — ALTA (B2 — quebra de restauração de Magicka e máximo negativo temporário). `base <= 0` quebra `actorvalues.ts:28` no Estágio IV com Fortificar ≥ máximo

**Onde:**
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\veilMagicka.ts:34-40`
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sync\actorvalues.ts:25-46`
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\remoteServer.ts:1070-1072`

**Causa:**
Para compensar Fortificar na base (`veilMagicka.ts:36`: `desired = Math.max(target.maximum, 0.01) - fortify`), uma bruxa em **Estágio IV** (`target.maximum = 50`) usando equipamento comum de Fortificar Magicka `+50` ou `+70` fica com `base = 0` ou `base = -20` (máximo efetivo `50`).
1. Quando a bruxa gasta toda a Magicka (`current = 0`, `getActorValuePercentage("magicka") === 0`) e o servidor envia qualquer atualização `ChangeValues(magicka > 0)` (regeneração, poção, ACK), `remoteServer.ts:1071` chama `setActorValuePercentage` (`actorvalues.ts:32-46`):
   ```ts
   // actorvalues.ts:25-30
   export const getMaximumActorValue = (ac: Actor, avName: string): number => {
     const currentPercentage = ac.getActorValuePercentage(avName);
     return currentPercentage === 0 ?
       ac.getBaseActorValue(avName) :
       Math.ceil(ac.getActorValue(avName) / currentPercentage);
   }
   ```
   Como `currentPercentage === 0`, `getMaximumActorValue` retorna `getBaseActorValue("magicka")` = **`0`** ou **`-20`** em vez de `50`. Na linha 42, `ac.restoreActorValue("magicka", deltaPercentage * currentMaxValue)` chama `restoreActorValue` com **`0`** (barra travada em 0) ou **valor negativo** (`-20 * delta`).
2. Ao **desequipar** um item de Fortificar quando `base < 0` (Estágio IV com Fortificar > máximo, ou Estágio V com qualquer Fortificar), durante a janela de até **250 ms** (`ENFORCE_EVERY_MS`) até o próximo `update()`, `getActorValueMax("Magicka")` cai para um valor **negativo** (ex.: `-49.99`). Nesse intervalo, `sendInputsService.ts:160` lê percentual inválido/negativo e, se o Skyrim zerar o dano acumulado ao cruzar teto `<= 0`, o reajuste seguinte pode preencher Magicka indevidamente até o próximo crop do servidor.

**Observação:** O teste `B2` em `teste-efeitos-veu-cliente.cjs:118-127` não detectou isso porque não exercita `actorvalues.ts` (`setActorValuePercentage`) nem o caso `fortify >= target.maximum` no Estágio IV.

---

### R4 — MÉDIA (B2 — discrepância funcional/design). Estágio III e Sobrecarga passam a anular Fortificar Magicka, criando queda abrupta

**Onde:**
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\veilMagicka.ts:51-59`
- `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\veil-magicka.js:4-11`
- `C:\Code\Aetherius - New Systems\Sobrenatural\docs\ETAPA-4_bruxaria-vinculo-do-veu.md:20,108-116`

**Causa:**
- No Estágio I/II (`maximumPercent = 100, regenPercent = 100`), `this.target` é `null` e `veilMagicka.ts:52` preserva explicitamente a semântica vanilla ("Fortify included"): uma bruxa com `base = 100` e anel `+50` tem **150** de Magicka máxima.
- Ao cruzar `bond <= 50` (**Estágio III**), a especificação aplica **apenas −50% de regeneração** (`maximumPercent = 100, regenPercent = 50`). Porém, como `rate !== vanilla.rate`, `veilMagicka.ts:56-58` ativa `this.target = { maximum: 100, rate }` e chama `enforce()`, que **subtrai todo o Fortificar** — fazendo a bruxa perder 50 de Magicka máxima de uma vez só ao entrar no Estágio III.
- Da mesma forma, ao entrar em **Sobrecarga** (`bond > 100`, `+50%` máximo), uma bruxa com `base = 100` e `+100` de Fortificar (200 no Estágio I) **cai de 200 para 150** ao "ganhar" o buff de +50% de Magicka máxima.

**Recomendação:** Alinhar com o orquestrador/dev se a compensação de Fortificar deve valer (a) só quando `maximumPercent !== 100` (e nunca no Estágio III), (b) só em penalidades (`maximumPercent < 100`, especialmente Estágio V), ou (c) globalmente, assumindo que o servidor nunca soma Fortificar em `GetMaximumValues()`.

---

### R5 — BAIXA (B3 / sessão). Reconexão pós-RaceMenu não dispara `SobrenaturalService.resetSession()` até chegar o novo `PACKET_SESSION`

**Onde:**
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\sobrenatural\sobrenaturalService.ts:26-28`
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\sendInputsService.ts:295-297`
- `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\aetheriusUiService.ts:312-313`

**Causa:**
Em `reconnectAfterRaceMenu()`, `networking.close()` não emite `connectionDisconnect`. `AetheriusUiService` é resetado diretamente por `ui.resetSession()` (`sendInputsService.ts:295`), mas `SobrenaturalService` (isolado) não é notificado até o `PACKET_SESSION` seguinte. Entre o `loadGame` do reload e a chegada do novo `PACKET_SESSION`, `this.target` antigo sobrevive a `onLoadGame()`.

**Correção sugerida:** Ouvir `connectionAccepted` em `SobrenaturalService` (padrão já usado em `timeService.ts:37`) para chamar `this.resetSession()` na abertura de nova conexão, sem tocar em arquivos do núcleo.

---

### R6 — BAIXA (detalhes de robustez, performance e documentação)

1. **Tolerância do ACK no Estágio V (`veilMagicka.ts:64`):** `Math.abs(player.getActorValue("Magicka") - current) < 1` aceita `0.01` como igual a `0` no Estágio V (`EMPTY_MAXIMUM = 0.01`). Mesmo se `damageActorValue` falhasse em zerar o resquício `0.01` (deixando o percentual em `1.0`), o ACK ainda retornaria `true`. Para `target.maximum === 0`, exigir `player.getActorValue("Magicka") < 0.001` (ou `<= 0`).
2. **Double `JSON.parse` sem guarda de tamanho (`sobrenaturalService.ts:37-41`):** diferente de `aetheriusUiService.ts:135`, `onCustomPacket` faz `JSON.parse` em todo `customPacketMessage` sem pré-filtro. Checar `dump.length > 17408` ou `!dump.includes("aetherius-ui:v1:")` antes do parse evita trabalho desnecessário em pacotes de outros sistemas.
3. **Comentário/doc inexato sobre `closeShell()` (`supernatural-module.js:53`, `ETAPA-4:306`):** ambos afirmam que o shell não chama `unmount` no caminho `nativeFocusChanged(false)`, mas `shell.js:362-365` efetivamente chama `unmountEntry(oldEntry)` na transição `workspace → gameplay`. (O comportamento é melhor que o documentado, mas convém corrigir o comentário para evitar confusão futura.)
4. **Notas documentais em `ETAPA-4`:**
   - Linha 324 cita linha 418 para `SkyDeath-Ghosts.esp` no servidor; no `runtime\server\data\aetherius-loadorder.json` é a linha **434** (418 é nos `plugins.txt` do MO2; 416 nos espelhos `QUALIDADE`/`PERFORMANCE` do `client-source`).
   - Linha 344 diz que o patch incremental não foi gerado, mas ele já foi commitado em `1ba58ee` (`overlays\cliente-local\021-isolamento-veu.patch`, validado abaixo com reverse-check).
5. **Lembrete para a compilação final (`DECISOES.md:187`, fora do escopo de `b7f92f1`):** `client\ui-core\modules\supernatural\supernatural-module.css:4-12` ainda mantém as regras `.shell.is-supernatural-workspace ...` da Etapa 1.4 (não alteram arquivos do shell, mas `DECISOES.md:187` exige auditá-las antes da compilação final).
6. **Cobertura de testes (`c65b1e7`):** não há teste automatizado cobrindo `closeShell()` em `supernatural-module.js` nem o ciclo `SobrenaturalService` (`ApplyMagicka` + `PACKET_SESSION` + `actorvalues.ts`).

---

## Saída dos testes executados pelo revisor

Ambiente: `C:\Code\Aetherius - Servidor Local`, **Node v22.14.0**, somente leitura (webpack não executado).

```text
# Bateria ETAPA-3 (overlays/sobrenatural/teste-*.cjs exceto servico/F7/revisao-F7 + 5 Core)
AETHERIUS_VAMPIRE_FEED_ENABLED=false → # tests 454 | # suites 19 | # pass 454 | # fail 0 | # skipped 0 (exit 0, 1443.8 ms)
AETHERIUS_VAMPIRE_FEED_ENABLED=true  → # tests 454 | # suites 19 | # pass 454 | # fail 0 | # skipped 0 (exit 0, 1415.5 ms)

# 5 arquivos Core isolados (werewolf, magic, survival-service, survival-effects, skysocial-service)
node --test --test-timeout=10000 @coreFiles → # tests 151 | # suites 19 | # pass 151 | # fail 0 (exit 0, 490.4 ms)

# Teste específico da rodada
node --test --test-timeout=10000 overlays/sobrenatural/teste-efeitos-veu-cliente.cjs → # tests 10 | # pass 10 | # fail 0 (exit 0, 444.1 ms)

# Scripts de integração / F7
node overlays/sobrenatural/teste-servico.cjs "C:\Code\Aetherius - Servidor Local\source\server\gamemode"
  → OK: serviço, restore throw/noop/reinício, falha de persistência, witch-grants e neutralização SSP (exit 0)
node overlays/sobrenatural/teste-painel-f7.cjs
  → OK F7: alvo, oito ações, autorização A+, sessão, catálogo, XP assinado/limites, regras de escolha, formas, replay e auditoria; F8 desligado. (exit 0)
node overlays/sobrenatural/teste-revisao-f7.cjs
  → OK revisão F7: fila bloqueada, reutilização de ator, reset suspenso, autorização revogada, retomada no login; ... (exit 0)

# Sintaxe, TypeScript e patch
node --check client-source\client\ui-core\modules\supernatural\supernatural-module.js → exit 0
node --check client-source\client\ui-core\modules\supernatural\veil-hud.js            → exit 0
client-source\client: npx tsc --noEmit                                               → exit 0
git -C client-source apply --check --reverse overlays\cliente-local\021-isolamento-veu.patch → exit 0
```

---

## Ações necessárias para aprovar

1. **Corrigir R1:** em `sobrenaturalService.ts:45`, não resetar `magicka` (nem `effects`) quando `this.sessionId === null` na chegada do primeiro `PACKET_SESSION`; adicionar reset em `connectionAccepted` (**R5**) e cobrir com teste.
2. **Corrigir R2:** substituir a heurística `maximum === vanilla.base && rate === vanilla.rate` por sinalização explícita do estado modificado/vanilla (ou revisão do contrato com o addon 025), evitando prender a compensação de Fortificar após `loadGame` ou mudança de bônus de classe, e impedir `VeilMagicka.update()` de sobrescrever atualizações de `SendAttributeMaximumValues()`.
3. **Corrigir R3 e decidir R4:** tratar a interação de `base <= 0` com `actorvalues.ts:28` (`getMaximumActorValue` quando `currentPercentage === 0`) e o intervalo de máximo negativo ao desequipar Fortificar, além de confirmar com o dev/orquestrador a política de Fortificar no Estágio III e na Sobrecarga.
4. **Atualizar testes e notas menores (R6):** incluir casos de regressão para R1, R2 (`loadGame` + bônus de classe), R3 (Estágio IV com Fortificar ≥ máximo e `setActorValuePercentage`) e `closeShell()`, além de validar fechamento do Ritual no CEF e reprodução B2/B3 em jogo após compilar o addon 025.


---

## Reconfirmação (7fb11d8)

**Revisor:** Gemini 4 Neon (High) (`task_deb5f33df3e5`). **Commits reconfirmados:** cliente `7fb11d8` (`C:\Code\Aetherius - Servidor Local\client-source`), laboratório `f4b9303` (`C:\Code\Aetherius - Servidor Local`, incluindo `overlays\cliente-local\022-veu-magicka-total.patch`). **Modo:** somente leitura.
**Premissas consideradas (decisão Opus / dev):**
- **Política R4:** Fortificar Magicka nunca é anulado; os multiplicadores do Véu aplicam-se sobre o máximo total (`base + Fortificar`); Estágio V mantém `EMPTY_MAXIMUM = 0.01` e `MagickaRate = 0`.
- **Save game:** não existe save game no Aetherius; o único `loadGame` é o spawn interno (`remoteServer.ts:786` → `LoadGameService`).

### Veredito da reconfirmação

**APROVADO NO ESCOPO DE CÓDIGO (com ressalvas de validação em jogo/CEF e compilação do addon 025).**

Todos os achados bloqueantes (**R1, R2, R3**), a política **R4**, **R5** e os ajustes de código de **R6** foram corrigidos de forma limpa e coerente. O isolamento do núcleo (**A1**) permanece intacto.

### Reconfirmação item a item

| Achado | Status | Evidência no código (`7fb11d8` / `f4b9303`) |
|---|---|---|
| **A1 — Isolamento do núcleo** | ✅ **Mantido** | `git diff e82ac03..7fb11d8` fora de `client\src\sobrenatural\*` e `client\ui-core\modules\supernatural\*` continua restrito a `client\src\index.ts` (+2/−0), `client\src\services\services\spSnippetService.ts` (+3/−0), `client\index.html` (+7/−1) e `client\ui-core\modules.json` (+16/−0). `shell.js`, `actorvalues.ts` e `aetheriusUiService.ts` seguem com **0 linhas de diff** contra `e82ac03`. `7fb11d8` tocou apenas `sobrenaturalService.ts`, `veilMagicka.ts` e um comentário em `supernatural-module.js`. |
| **R1 — Corrida do 1º `PACKET_SESSION`** | ✅ **Resolvido** | `sobrenaturalService.ts:51` agora exige `this.sessionId !== null && this.sessionId !== content.sessionId` antes de chamar `effects.reset()` e `magicka.reset()`. O primeiro pacote de sessão após o ACK no login não desfaz mais a Magicka aplicada; troca real de sessão continua resetando. Coberto em `teste-efeitos-veu-cliente.cjs:227-235`. |
| **R2 — Heurística de vanilla / bônus de classe / `loadGame` (B3)** | ✅ **Resolvido** | A heurística `maximum === vanilla.base && rate === vanilla.rate` foi eliminada. Em `veilMagicka.ts:28-56,81-93`, a base pertence exclusivamente ao servidor (`ApplyMagicka` e `SendAttributeMaximumValues`); `VeilMagicka` apenas ajusta seu próprio modificador permanente via `modActorValue("Magicka", modifier - this.applied)` a partir de `maximumFactor(aetheriusVeilPresentationV1)`. `update()` nunca reescreve a base; `onLoadGame()` zera `this.applied = 0` (alinhado ao template save do spawn interno) e recomputa no frame seguinte sem duplicar efeitos. Coberto em `teste-efeitos-veu-cliente.cjs:175-191`. |
| **R3 — `base <= 0` no Estágio IV / `actorvalues.ts:28`** | ✅ **Resolvido (código)** | Como a escala de Fortificar vai em `modActorValue` e a base fica no valor positivo do servidor (`0.5 * serverBase`), `actorvalues.ts:28` (`getMaximumActorValue` quando `currentPercentage === 0`) volta a ler `base > 0`, permitindo `setActorValuePercentage` restaurar Magicka normalmente (validado com o `actorvalues.ts` real em `teste-efeitos-veu-cliente.cjs:193-201`). Além disso, `update()` passou a rodar a cada frame (sem janela de 250 ms). |
| **R4 — Política de Fortificar sobre o máximo total** | ✅ **Resolvido** | `maximumFactor` (`veilMagicka.ts:12-18`) espelha `veil-magicka.js`: Estágio III (`factor = 1`) preserva 100% do Fortificar (`base + fortify`); Estágio IV (`0.5`) produz `base(0.5*B) + 0.5*fortify = 0.5*(B + fortify)`; Sobrecarga (`1.5`) produz `base(1.5*B) + 1.5*fortify = 1.5*(B + fortify)`; Estágio V (`0`) mantém `EMPTY_MAXIMUM = 0.01`, `MagickaRate = 0` e `current = 0`. Em `apply()` (`veilMagicka.ts:71-74`), `target = current / maximum * getActorValueMax("Magicka")` preserva a fração do servidor sobre o total sem preencher Magicka ao trocar de estágio. Coberto em `teste-efeitos-veu-cliente.cjs:140-173`. |
| **R5 — Reconexão pós-RaceMenu (`connectionAccepted`)** | ✅ **Resolvido** | `sobrenaturalService.ts:32` agora registra `this.controller.emitter.on("connectionAccepted", () => this.resetSession())`, fechando a lacuna do `networking.close()` em `sendInputsService.ts:296`. Coberto em `teste-efeitos-veu-cliente.cjs:237-244`. |
| **R6 — Robustez, pré-filtro e comentário `closeShell()`** | ✅ **Resolvido (código)** | `veilMagicka.ts:76` usa validação estrita `now < 0.001` no Estágio V; `sobrenaturalService.ts:42` pré-filtra tamanho (`> 17408`) e substring `"aetherius-ui:v1:"` antes do `JSON.parse`; `supernatural-module.js:53` corrige o comentário para `(o unmount do shell depois é idempotente)`. |

### Ressalvas não bloqueantes (para observação em jogo e limpeza futura)

1. **Validação em motor (Estágio V + desequipar Fortificar — `veilMagicka.ts:44`):** no Estágio V com Fortificar `+50`, `base = 0` e `this.applied = -49.99`. Ao desequipar o item, a soma `base + temp + perm` fica `-49.99` por 1 frame. Se `player.getActorValueMax("Magicka")` no Skyrim SE retornar o float negativo sem clamp (comportamento esperado do `ActorValueOwner`), `fortify = -49.99 - 0 - (-49.99) = 0` e o modificador se corrige no frame seguinte para `+0.01`. Caso alguma camada nativa faça clamp para `0`, `fortify` seria lido como `+49.99` até sair do Estágio V (onde se autocorrige assim que `base > 49.99`). **Confirmar esse cenário na rodada em jogo.**
2. **Janela transitória de ≤1 s ao sair do Estágio V sem Fortificar (`veilMagicka.ts:45`):** se `aetheriusVeilPresentationV1` atualizar para `factor = 1` antes de `ApplyMagicka` restaurar `base > 0` (intervalos separados de `veil-bond.js` e `veil-visuals.js`), `effective = base + fortify` vale `0` e remove `+0.01`, deixando máximo `0` por até 1 s até `ApplyMagicka` chegar (sem impacto no servidor porque `ActionListener.cpp:1170` descarta `NaN`, mas fácil de blindar com `Math.max(base + fortify, EMPTY_MAXIMUM)` quando `base <= 0`).
3. **Micro-otimização opcional (`veilMagicka.ts:83-86`):** `update()` consulta `this.player()` antes de `if (factor === 1 && this.applied === 0) return;`. Inverter a ordem poupa 3 chamadas nativas por frame em não-bruxas e bruxas nos estágios I–III (custo atual é baixo).
4. **Pendências operacionais já conhecidas:** compilação do addon 025 (B1), validação no CEF de `closeShell()` (sem teste unitário dedicado), auditoria das regras `.shell.is-supernatural-workspace` em `supernatural-module.css:4-12` antes da compilação final (`DECISOES.md:187`), e atualização das 3 notas cosméticas em `ETAPA-4` (linhas 306, 324, 344).

### Testes executados na reconfirmação (Node v22.14.0)

```text
# Bateria ETAPA-3 (overlays/sobrenatural/teste-*.cjs exceto servico/F7/revisao-F7 + 5 Core)
AETHERIUS_VAMPIRE_FEED_ENABLED=false → # tests 459 | # suites 19 | # pass 459 | # fail 0 | # cancelled 0 | # skipped 0 (exit 0, 1591.9 ms)
AETHERIUS_VAMPIRE_FEED_ENABLED=true  → # tests 459 | # suites 19 | # pass 459 | # fail 0 | # cancelled 0 | # skipped 0 (exit 0, 1552.3 ms)

# 5 arquivos Core isolados
node --test --test-timeout=10000 @coreFiles → # tests 151 | # suites 19 | # pass 151 | # fail 0 (exit 0, 1661.1 ms)

# Teste do cliente do Véu (cobrindo R1–R5, B2, B3 e isolamento)
node --test --test-timeout=10000 overlays/sobrenatural/teste-efeitos-veu-cliente.cjs → # tests 15 | # pass 15 | # fail 0 (exit 0, 1426.5 ms)

# Integração, F7, sintaxe, TypeScript e patch 022
node overlays/sobrenatural/teste-servico.cjs "C:\Code\Aetherius - Servidor Local\source\server\gamemode" → OK (exit 0)
node overlays/sobrenatural/teste-painel-f7.cjs  → OK (exit 0)
node overlays/sobrenatural/teste-revisao-f7.cjs → OK (exit 0)
node --check client-source\client\ui-core\modules\supernatural\supernatural-module.js → exit 0
node --check client-source\client\ui-core\modules\supernatural\veil-hud.js            → exit 0
client-source\client: npx tsc --noEmit                                               → exit 0
git -C client-source apply --check --reverse overlays\cliente-local\022-veu-magicka-total.patch → exit 0
```
