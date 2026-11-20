# Rebase do pacote para o oficial de 19/11/2026 (Opus 5.5)

O dev substituiu `C:\Code\Aetherius - OFICIAL\aetherius-server-main` e `aetherius-client-main` pela versão nova (arquivos datados de 19/11/2026 às 15:19). Os patches deste pacote foram **regerados sobre essa versão**:
- aplicam **sem conflito**, conferido com `git apply --check --ignore-whitespace` contra cópias limpas do oficial;
- as pastas `conflitos-com-oficial/` deixaram de existir.

## Método
1. **Base comum:** os arquivos em `310cb59` (servidor) e `bc305e5` (cliente), com o pacote do lobisomem do kdu aplicado (`AETHERIUS - LOBISOMEM 14-11 - KDU/patches/servidor-lobisomem.patch` e `cliente-lobisomem.patch`). Os dois lados partem dessa versão, e isso eliminou os falsos conflitos "add/add" do `werewolf-*`.
2. **Ramos comparados:** `oficial` (o oficial novo) e `sn` (o Sobrenatural final do laboratório: servidor `147e84f`, client-source `5e8b491`, painel F7 testado). Mesclagem `oficial ← sn`, com finais de linha normalizados para LF.
3. **Conflitos:** resolvidos à mão (tabela abaixo). Patches gerados como `diff oficial..merged`.

## Conflitos e resolução
| Arquivo | Do oficial | Do Sobrenatural | Resolução |
|---|---|---|---|
| `server/gamemode/magic-service.js` | Log de diagnóstico de invocação (`diagLog`), motivo em `end(id, motivo)`, Invisibilidade replicada aos vizinhos | Vagas de invocação da Bruxa (`creatureSlots`/`admission`/`witchOwners`) e desvio dos poderes geridos (`__aetheriusPowerActions.owns`) | **Os dois.** O desvio dos poderes roda antes da Invisibilidade. `diagLog` registra também a recusa por limite. O diagnóstico de `summon.created` usa `slots.limit`, porque `capacity()` deixou de existir. |
| `server/gamemode/magic/tests/magic.test.cjs` | Teste da Invisibilidade (19/11) | Teste do desvio dos poderes geridos | **Mantidos os dois testes.** |
| `server/gamemode/werewolf/client.js` | Comentário de 14/11; `reached = 0` | Comentário do v0.42; `reached = ''` (o código abaixo compara com string) | **Sobrenatural.** |
| `client/src/services/services/loadGameService.ts` | Comentário do CTD de 17/11 | `cancelPendingPlayerAppearance()` antes do load | **Sobrenatural.** O oficial e o Sobrenatural fazem a mesma proteção de headparts; o Sobrenatural acrescenta o cancelamento da fila. |
| `client/src/services/services/remoteServer.ts` | Aplicação direta da aparência no próximo update | `applySpawnAppearance` com reserva e geração (descarta callbacks antigos) | **Sobrenatural.** |
| `client/src/sync/appearance.ts` | `syncPlayerRacialSpells` "agressivo" (remove as raciais de todas as outras raças, inclusive Nórdico); removeu `switchPlayerCreatureRace` e o import `Keyword` | Autoria conservadora das raciais (decisão de 17/11, ver `05-documentacao/COMPARACAO_lobisomem-oficial-v0.42.md` §13); `switchPlayerCreatureRace` (troca real de raça para fera ou Lorde) | **Sobrenatural, com o import `Keyword` restaurado.** Sem `switchPlayerCreatureRace`, fera e Lorde travam no grafo humano. ⚠️ **Decisão para o dev:** o oficial voltou a remover as raciais de outras raças (o problema do "Nórdico base"). A versão conservadora não remove o que não adicionou. Se o problema do Nórdico voltar a aparecer, é preciso combinar as duas abordagens. |
| `client/admin-panel/app.js` | Aba **Compras** (`purchases`) | Aba **Sobrenatural** | **As duas.** Ícone `supernatural:'skull'` e filtro da aba Sobrenatural somados aos filtros de Compras. |

**Mesclados automaticamente** (o oficial mudou, sem conflito; revisados):
- `ActionListener.cpp`: segurança de comandos de console, autoridade de stamina, `NoteSpellCast` e dano de stamina por magia.
- `MpActor.cpp`: restauração ao longo do tempo para poções com duração.
- `admin-panel/aetherius-store.cjs`, `aetherius-tools.cjs`, `service.cjs` e `staff-policy.cjs`.
- `phase0-basic.js`, `survival-service.js`, `skysocial/src/identity.js` e `werewolf-service.js`.
- `client/index.html`, `client/src/index.ts`.

Nenhum desses pontos substitui comportamento do Sobrenatural.

⚠️ **Ponto para testar em jogo:** a nova restauração ao longo do tempo no `MpActor.cpp` (poções de 10 s ou mais curam por segundo, no C++). Confirme que o bloqueio de **cura do vampiro** (`supernatural/vampire-healing.js`) continua recusando poção de cura pura, já que agora os ticks vêm de timers nativos.

## Testes executados na árvore mesclada
Montei cópias completas do oficial novo com os arquivos mesclados por cima. Usei os `node_modules` do laboratório.

| Bateria | Resultado |
|---|---|
| Gamemode: `werewolf.test.cjs`, `magic.test.cjs`, `records-ctda`, `summon-friends`, `survival-service.test.js` | **142/142** |
| Testes do Sobrenatural (`teste-*.cjs`, 27 arquivos), com alimentação `false` e `true` | **319/319 + 44 de cliente**, em cada modo |
| Cliente: `tsc --noEmit` | **OK**, depois de restaurar o import `Keyword` |
| Cliente: `webpack` | **OK** |
| Cliente: `meridian/*.test.cjs` | 142 aprovados, 7 com falha. **As mesmas 7 falham no oficial puro** (Teleport2, BrowserService/PTT/hotkeys). Não é regressão. |
| Backend: `tsc --noEmit` | **OK** |
| GameplayCore | 174 aprovados, 22 com falha, contra 111/24 no oficial puro. As falhas são de ambiente: arquivos inteiros que dependem de banco ou artefatos do monorepo, e `runtime.test` "B2 domains", que também falha no laboratório. Os testes do Sobrenatural (`perk-origin-barrier`, `perk-client`) passam. |
| Addon nativo (C++) | **Não recompilado nesta rodada** (vcpkg e toolchain levam horas). Compile no fluxo oficial. A mesclagem do C++ foi automática e sem conflito; o `scam_native.node` de `04-binarios-referencia` corresponde ao laboratório **antes** das mudanças nativas do oficial de 19/11. |

## Como aplicar
```powershell
git apply --ignore-whitespace "<pacote>\01-servidor\sobrenatural-servidor.patch"   # no aetherius-server-main
git apply --ignore-whitespace "<pacote>\02-cliente\sobrenatural-cliente.patch"     # no aetherius-client-main
git apply --ignore-whitespace "<pacote>\06-painel-adm\cliente-f7\004-admin-panel-client.patch"
```
Os patches usam LF. Por isso use `--ignore-whitespace` se o repositório estiver em CRLF. Também é possível copiar as versões finais de `01-servidor/arquivos/` e `02-cliente/arquivos/`.
