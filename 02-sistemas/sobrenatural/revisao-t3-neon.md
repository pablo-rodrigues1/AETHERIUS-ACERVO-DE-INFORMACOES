# REVISÃO INDEPENDENTE — Correção T3 (`632869b` e `e71771d`)

- **Revisor:** Gemini 4 Neon (revisão cruzada independente, somente leitura no código)
- **Autor revisado:** Terra High
- **Data:** 19/11/2026
- **Laboratório auditado:** `C:\Code\Aetherius - Servidor Local`
- **Commits sob revisão:**
  - `632869b1ddf86890c0fe937612832898fb53cb1c` (`fix(sobrenatural): migrar efeitos legados da compulsao`)
  - `e71771d895d8a96dea1a64c509df55923c383a2d` (`docs(sobrenatural): investigar storage legado da compulsao`)
- **Contexto e referências conferidos:**
  - `C:\Code\Aetherius - New Systems\Sobrenatural\docs\TESTE-JOGO_19-11.md` (item T3 e prints `9.png` / `10.png`)
  - `C:\Code\Aetherius - New Systems\Sobrenatural\docs\investigacoes\INVESTIGACAO_19-11_compulsao-storage.md`
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\forced-werewolf-client.js` (e histórico `856232a`)
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\forced-werewolf.js`
  - `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\files\server\gamemode\supernatural\blood-effects-client.js` (padrão de referência `93d991a` / `2fffaf9`)
  - `C:\Code\Aetherius - Servidor Local\client-source\client\src\services\services\gamemodeUpdateService.ts`
  - `C:\Code\Aetherius - Servidor Local\logs\server-20261119-003042.stdout.log`
- **Paridade `overlays` ↔ `source` ↔ `runtime`:**
  - `overlays/sobrenatural/files/server/gamemode/supernatural/forced-werewolf-client.js` e `source/server/gamemode/supernatural/forced-werewolf-client.js` são idênticos (SHA-256 `B2EF4FCAC98C1E49C965CEDCF0C08527D02454AFAEADBB617D8E0EAFE0590776`).
  - `INVESTIGACAO_19-11_compulsao-storage.md` é idêntico nos dois repositórios (SHA-256 `C2C37A6DC9241D3500362BEEC996027532C373C755F02B7522BFE4A64B0CD1F9`).
  - `runtime/server/gamemode/supernatural/forced-werewolf-client.js` segue na revisão `856232a`, conforme declarado na investigação (requer `Prepare.ps1` / deploy antes do reteste em jogo).

---

## Veredito

**APROVADO (sem defeitos bloqueantes).**

A correção `632869b` elimina o loop `TypeError: fx.cleanup is not a function` em `updateOwner aetheriusBeastCompulsion`, protege a migração de objetos legados ou malformados em `sp.storage.aetheriusCompulsionFx` sem introduzir regressão, crash ou dessincronização cliente↔servidor, e segue fielmente o padrão de storage versionado e retry de áudio legado já consolidado em `blood-effects-client.js`. A investigação `e71771d` é precisa e honesta quanto ao que foi demonstrado em mock vs. o que permanece pendente de validação em jogo.

---

## Checklist técnico

| Critério | Avaliação | Evidência |
|---|---|---|
| **Erro / regressão** | **OK.** | `forced-werewolf-client.js:9-35` invalida e substitui qualquer `fx` com `fx.version !== 1`, `typeof fx.cleanup !== 'function'` ou valor truthy não-objeto antes de chegar à chamada `fx.cleanup()` em `:69`. Em estado já migrado (`version === 1` e `cleanup` função), o caminho quente com `ctx.value === null` apenas zera `fx.packet` e executa `cleanup()` sem chamadas nativas. |
| **Crash / exceções não tratadas** | **OK.** | Remoção de `ImageSpaceModifier` (`0x801`, `0x807`) em `:13-15` e `sp.Sound.stopInstance` em `:22,29` estão isoladas em `try/catch`. Listeners `sp.on('update', ...)` são registrados no máximo uma vez por `pending` (`retryRegistered`, `:24-25`) e uma vez por `fx` v1 (`:35,46`), sem vazamento de listeners. |
| **Dessincronização cliente↔servidor** | **OK.** | `forced-werewolf.js` não foi alterado; `aetheriusBeastCompulsion` segue privada do dono (`isVisibleByOwner: true`, `isVisibleByNeighbors: false`). Durante falha de `stopInstance` do handle legado, o cliente cerca (`return` em `:32`) sem iniciar novo batimento; ao recuperar, a próxima atualização do servidor (a cada 250 ms) ou `null` reinstala `v1` e sincroniza normalmente (`fx.sample !== value.sample` em `:71`). |
| **Coerência com o padrão `blood-effects-client.js`** | **OK.** | Espelha `blood-effects-client.js:6-36` (`93d991a` / `2fffaf9`): captura `heartId` antes de invalidar `fx.heartId`/`fx.packet`/`storage.aetheriusCompulsionFx` (impedindo o listener legado de `856232a:20` de perder o handle no watchdog de 2,5 s), cerca o retry em `storage.aetheriusCompulsionLegacyStop` com listener independente de 1 s, e ainda acrescenta a guarda explícita `typeof fx.cleanup !== 'function'` em `:9`. |
| **Performance** | **OK (com 1 observação opcional abaixo).** | Em operação normal (`pending` ausente), o custo extra por frame é uma leitura de `storage.aetheriusCompulsionLegacyStop` e duas comparações locais (`fx.version !== 1 \|\| typeof fx.cleanup !== 'function'`), desprezível. |
| **Documentação (`e71771d`)** | **OK.** | `INVESTIGACAO_19-11_compulsao-storage.md` distingue corretamente o que foi observado (prints/relato, ausência da linha nos logs do servidor) da hipótese reproduzida em código e dos limites dos testes simulados. |

---

## Verificações executadas pelo revisor

1. `node --test "C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\teste-transformacao-forcada.cjs"`: **31/31** passaram (exit 0), cobrindo migração legada, cerca em falha de `stopInstance`, retry, retomada sem duplicar listeners e valores malformados (`true`, `'old'`, `{ version: 1, packet: null }`).
2. Compilação do script serializado `client.effects` via `new Function('ctx', client.effects)` (mesmo mecanismo de `gamemodeUpdateService.ts:249`): compilou sem erro (3871 bytes).
3. Simulação independente em Node carregando o script serializado **real** de `856232a` (via `git show`) e trocando em memória para o script serializado **real** de `632869b`, mantendo o mesmo `sp.storage` e os callbacks `sp.on('update')`:
   - Migração com `heartId = 1` ativo e `stopInstance` falhando além do watchdog de 2500 ms do listener antigo de `856232a`: o listener antigo não apagou `aetheriusCompulsionLegacyStop`, `632869b` não abriu segundo áudio nem duplicou listener de retry; após recuperação do `stopInstance`, o handle legado encerrou, `v1` foi instalado e o novo ciclo tocou e limpou normalmente.
   - Reprodução do T3 (`aetheriusCompulsionFx` sem `cleanup` + `ctx.value = null`): `856232a` lançou `TypeError: fx.cleanup is not a function`; `632869b` recuperou no 1º frame e permaneceu silencioso e com 1 único listener ao longo de 60 frames consecutivos.

---

## Observações opcionais (não bloqueantes)

1. **Desempenho sob falha prolongada de `stopInstance` (`forced-werewolf-client.js:20-34`):**
   - Assim como já registrado por Sol High na revisão de `blood-effects-client.js:19-22` (`ETAPA-3_vampirismo-mecanicas.md:605`), enquanto `pending` permanecer ativo e `stopInstance` continuar lançando exceção, o bloco `if (pending)` tenta parar o áudio em toda chamada de `updateOwner` (que `gamemodeUpdateService.ts:178-219` executa a cada frame quando `ownerModel.aetheriusBeastCompulsion !== undefined`, inclusive `null`), sem aplicar o throttle de 1000 ms que existe no listener de `:27`.
   - Não é bloqueante: só acontece no cenário excepcional de falha contínua da API de áudio durante uma migração com `heartId != null`, não duplica som nem vaza listeners. Se desejarem uniformizar depois (aqui e em `blood-effects-client.js`), bastaria guardar a tentativa síncrona com `Date.now() - pending.lastAttempt >= 1000` (já que `lastAttempt` inicia em `-Infinity`).
2. **Ponto de atenção para o reteste em jogo (origem do objeto sem `cleanup`):**
   - No histórico Git, `forced-werewolf-client.js` já definia `fx.cleanup = cleanup` desde a primeira versão (`856232a:18`), e no log da sessão de 19/11 (`logs/server-20261119-003042.stdout.log:20`) o hot-reload do cliente estava desabilitado (`clientside hot-reload is disabled`), tendo ocorrido 3 transformações forçadas (`00:45:25`, `00:47:18`, `00:55:30`) seguidas de troca de maldição para Vampiro (`00:58:55`).
   - A investigação `INVESTIGACAO_19-11_compulsao-storage.md:7,18` já sinaliza honestamente que a causa exata de `fx.cleanup` ter sumido não foi observada. No reteste em jogo (após preparar `runtime/`), confirmar após o fluxo **forçada → retorno mortal → troca de maldição/poderes** que o loop não reaparece e que o estado v1 não é recriado repetidamente.
3. **Higiene de diff (`e71771d`):** `git show --check e71771d` aponta apenas 1 linha em branco extra no fim de `overlays/sobrenatural/docs/ETAPA-2_transformacao-forcada.md:133` (puramente cosmético; `632869b` passou limpo no `--check`).
