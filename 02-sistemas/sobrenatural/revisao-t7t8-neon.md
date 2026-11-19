# Revisão Independente — T7/T8 (Auras de Vampiro Feral e Sobrecarga da Bruxa)

- **Revisor:** Gemini 4 Neon (revisão independente, somente leitura no código)
- **Data:** 19/11/2026
- **Alvo revisado:**
  - Laboratório (`C:\Code\Aetherius - Servidor Local`):
    - Código: `89d4b201f851aaeb1fc681b22316a238202d6d4f` (`fix(sobrenatural): aplica auras Red e Purple do Colored Magic Auras`)
    - Documentação: `7b0a57485914cf0186773c1ab51aaec390e05492` (`docs(sobrenatural): investiga auras Feral e Sobrecarga`) e `2e0e464d010462f871c9e3a7f9a42cfcbf9104e9` (`docs(sobrenatural): registra aura Red na etapa de vampirismo`)
  - Investigação de referência: [INVESTIGACAO_19-11_auras-feral-sobrecarga.md](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_19-11_auras-feral-sobrecarga.md)

---

## Veredito

**APROVADO SEM ACHADOS.**

A mudança em `89d4b20` substitui de forma limpa os `EffectShader` de superfície (`SkyDeath-Ghosts.esp`) pelos `VisualEffect` (`RFCT`) de partículas do `Colored Magic Auras.esp` (`000D62` Red no Vampiro Feral e `000801` Purple na Sobrecarga da Bruxa), mantendo a autoridade no servidor, sem vazar keywords de maldição, sem risco de crash/`indexInPool` e com ganho real de performance no caminho ocioso por frame. A documentação em `7b0a574` e `2e0e464` é factual, honesta quanto ao que os testes não provam e registra corretamente o bloqueio de ativação do mod no perfil `AETHERIUS - LOCAL`.

Não há defeitos reais no código revisado. Abaixo constam a auditoria por eixo, observações opcionais (não bloqueantes) e pendências de modpack que dependem de houseCARL (indisponível ao Gemini).

---

## 1. Auditoria Técnica

| Eixo | Evidência verificada | Resultado |
|---|---|---|
| **Regressão** | Alteração restrita a `aura()` / `feral` / exports em `blood-effects-client.js:64-110` e ao argumento de `auraScript` em `veil-visuals.js:15`. `critical()` (`blood-effects-client.js:4-63`), `blood-effects.js`, `veil-magicka.js`, `veil-eyes.js` e `veil-ritual-fx.js` ficaram intocados. `git grep feralFunction` confirmou zero consumidores ativos para o export removido (restam apenas fixtures históricas em `overlays/sobrenatural/fixtures/`). Zero referências remanescentes a `SkyDeath-Ghosts` em `overlays/sobrenatural/files` e `source/server/gamemode/supernatural`. Chaves em `ctx.state` (`bloodFeral` e `veilAura`) não colidem entre si nem com o formato plano legado (`5effbd8`) em eventual hot-reload. | **Aprovado** |
| **Crash / `indexInPool` / nulidade** | Nenhum wrapper nativo (`Actor`, `VisualEffect`, `Cell`) é retido em `ctx.state` entre frames; `fx` guarda apenas primitivos (`sample`, `received`, `cell`, `lastAttempt`, `playing`). O `VisualEffect` é resolvido a cada operação via `sp.VisualEffect?.from(sp.Game.getFormFromFile(effectId, 'Colored Magic Auras.esp'))` (`blood-effects-client.js:92`), sem risco de `Invalid __skyrimPlatform__indexInPool`. A assinatura `effect.play(actor, 2.5, null)` / `effect.stop(actor)` bate com `@skyrim-platform/skyrim-platform/index.d.ts:3310-3314` e com o uso existente em `werewolf/client.js:139`. Ausência do plugin (`effect == null`) falha fechada (`effect?.stop(actor)`, `if (show && effect)`) sem lançar exceção. Em falha de `stop`, o `finally` (`:96-98`) zera `fx.playing` quando `!show`, `fx.lastAttempt` já impede repetição por frame e `GamemodeUpdateService` (`gamemodeUpdateService.ts:105-111, 212-218`) isola exceções. | **Aprovado** |
| **Sincronização — aplicar/remover e observadores (inclusive tardios)** | `blood-effects.js:3,34,38` (`aetheriusBloodFeral`) e `veil-visuals.js:5,16,34` (`aetheriusVeilAura`) publicam `{ sample, feral: true }` / `{ overload: true, sample }` ou `null` com `isVisibleByOwner: true`, `isVisibleByNeighbors: true` e o mesmo script em `updateOwner` e `updateNeighbor`. No servidor, `PartOne.cpp:823-851` envia propriedades públicas no `CreateActor` de quem entra em relevância; no cliente, `remoteServer.ts:583-595` preenche `form[propName]` e `formView.ts:219,346` cria `FormView` com `this.state = {}` e invoca `updateNeighbor`. Se o 3D do ator remoto ainda não estiver pronto no primeiro frame, `fx.lastAttempt` permanece `undefined` (`blood-effects-client.js:87`), disparando `play` no primeiro frame em que `is3DLoaded()` vira `true` enquanto o servidor renova `sample` a cada 1 s. Ao sair de relevância (`formViewArray.ts:17-25`, `formView.ts:350-377`), a réplica é descartada e a duração finita de 2,5 s impede vazamento. | **Aprovado** |
| **Sincronização — morte, relog, troca de célula e 3D** | Morte vanilla (`actor.isDead()`) interrompe no mesmo frame (`blood-effects-client.js:80,95`); queda em SkyDeath (`DOWNED`/`GHOST`) é encerrada pelo servidor no tick de 1 s (`blood-effects.js:13,31,34`; `veil-visuals.js:32-37`), que envia `null` a dono e vizinhos. Troca de célula (`cell !== fx.cell`, `:83`) e retorno de 3D (`fx.lastAttempt = undefined`, `:87`) reaplicam imediatamente. Queda do servidor, desconexão ou perda de atualização expiram em ≤2,5 s (`now - fx.received > 2500`, `:80`, somado à duração finita de 2,5 s). Em relog/remoção de maldição, o tick de 1 s publica `null`, apagando `ctx.state[key]` (`:69-71`) e chamando `stop`. | **Aprovado** |
| **Performance** | Ganho claro sobre `5effbd8`: no caminho ocioso (`!active && !ctx.state[key]`, `blood-effects-client.js:66`), retorna imediatamente sem chamar `Date.now()`, `Actor.from()`, `Game.getFormFromFile()` nem `VisualEffect.from()`. Quando ativo na mesma célula, o intervalo de 1,5 s (`:83`) evita `Game.getFormFromFile()` e `VisualEffect.from()` a 60 fps. Com o plugin ausente, as buscas ficam igualmente limitadas a 1 tentativa a cada 1,5 s. | **Aprovado** |
| **Dependência de plugin no load order** | Verificado via `source/server/tools/client-plugins.js` (`pluginInfo`): `C:\modOrganizer\mods\Colored Magic Auras\Colored Magic Auras.esp` possui 23.204 bytes, CRC32 `1435824785`, **`isLight: true`** (ESP com flag ESL) e `isMaster: false`. Os IDs locais `0x000D62` e `0x000801` estão na faixa ESL válida (`0x800..0xFFF`), compatíveis com `Game.getFormFromFile`. `runtime/server/data/aetherius-loadorder.json` tem 436 plugins (126 regulares, 310 light — ampla margem frente aos limites 254/4096) e ainda não inclui `Colored Magic Auras.esp`. O mod está desativado em `C:\modOrganizer\profiles\AETHERIUS - LOCAL\modlist.txt:3` (`-Colored Magic Auras`) e ausente nos perfis `AETHERIUS - LOCAL`, `AETHERIUS - PERFORMANCE` e `AETHERIUS - GRAFICO - QUALIDADE` em `client-source\client\profiles\`. O `runtime\server\gamemode\supernatural\blood-effects-client.js` também segue na revisão anterior (`SkyDeath-Ghosts.esp`) até o próximo `Prepare.ps1`. Tudo isso já está corretamente sinalizado como ação prévia do dev em `INVESTIGACAO_19-11_auras-feral-sobrecarga.md:37-38`. | **Aprovado** (com pré-requisito operacional já documentado) |
| **Paridade e documentação (`7b0a574`, `2e0e464`)** | SHA-256 idêntico entre `overlays/...` e `source/...` para `blood-effects-client.js` e `veil-visuals.js`, e entre o laboratório e `Sobrenatural\docs\investigacoes\` para `INVESTIGACAO_19-11_auras-feral-sobrecarga.md`. Links relativos em `ETAPA-3_vampirismo-mecanicas.md` e `ETAPA-4_bruxaria-vinculo-do-veu.md` resolvem corretamente. | **Aprovado** |

---

## 2. Observações Opcionais (Não Bloqueantes)

1. **Continuidade visual na renovação `stop` + `play(actor, 2.5, null)` a cada 1,5 s (`blood-effects-client.js:93-105`):**
   Chamar `effect.stop(actor)` imediatamente antes de `effect.play(actor, 2.5, null)` no mesmo frame evita empilhamento de instâncias de `ModelReferenceEffect`, mas dependendo dos tempos de emissão/fade dos `.nif` (`spgFXAuraParticlesRed.nif` e `spgFXAuraParticlesPurple.nif`), pode provocar um reinício perceptível ou leve piscada das partículas a cada 1,5 s. O próprio autor já incluiu essa verificação no roteiro de reteste (`INVESTIGACAO_19-11_auras-feral-sobrecarga.md:33,39`); só vale ajustar caso o teste visual em jogo mostre piscada incômoda.
2. **Sincronização de `Colored Magic Auras.esp` nos dois perfis oficiais (migração para produção):**
   Por ser um plugin light (`isLight: true`), habilitar `Colored Magic Auras.esp` apenas no cliente sem atualizar o manifesto do servidor desloca índices `FE xxx` de plugins light subsequentes. Além do `AETHERIUS - LOCAL` já documentado, ao levar para o servidor oficial será obrigatório ativá-lo igualmente em **ambos** os perfis oficiais (`AETHERIUS - PERFORMANCE` e `AETHERIUS - GRAFICO - QUALIDADE`), pois `source/server/tools/sync-client-plugins.js:47-48,130-138` exige paridade exata entre os dois.
3. **Falha silenciosa no boot se o ESP estiver fora da `loadOrder`:**
   Diferente de `veil-ritual-fx.js:177`, nem `blood-effects.js` nem `veil-visuals.js` checam `Colored Magic Auras.esp` na inicialização do servidor (opção intencional para manter resolução client-side e sem master novo). Se o plugin ficar desmarcado por engano em algum ambiente, não haverá crash nem log — apenas ausência visual das auras. Estreitamente opcional.

---

## 3. Pendências de Dados do Modpack (Sem houseCARL)

Gemini não possui acesso ao MCP houseCARL. Antes de ativar `Colored Magic Auras.esp` no perfil oficial (ou, opcionalmente, antes do teste no LOCAL), recomenda-se despachar **Luna MAX** com houseCARL somente leitura para confirmar:

1. **Masters e overrides de `Colored Magic Auras.esp`:** verificar a lista de masters do cabeçalho (`TES4`) e se o plugin sobrescreve qualquer record de outro mod/vanilla (`CELL`, `CONT`, `LVLI`, `NPC_`, `FLST`, etc.) nos perfis `AETHERIUS - LOCAL`, `AETHERIUS - PERFORMANCE` e `AETHERIUS - GRAFICO - QUALIDADE`.
2. **Exposição indesejada de magias/livros do mod no jogo:** confirmar se `Colored Magic Auras.esp` adiciona spell tomes (`BOOK`), baús, NPCs ou entradas em leveled lists que permitam a jogadores obter as magias originais de 180 s do mod em RP, para o dev decidir se ativa o ESP integralmente ou se isola/limpa esses records antes do servidor oficial.

---

## 4. Verificações Executadas na Revisão

Executadas em `C:\Code\Aetherius - Servidor Local`, sem editar código nem alterar runtime/MO2:

| Verificação | Resultado |
|---|---|
| `node --check overlays/sobrenatural/files/server/gamemode/supernatural/blood-effects-client.js` e `veil-visuals.js` | exit `0` |
| `node --test overlays/sobrenatural/teste-sangue-efeitos.cjs overlays/sobrenatural/teste-veu-mundo.cjs` | **25/25** (`fail 0`, exit `0`) |
| `pluginInfo('C:/modOrganizer/mods/Colored Magic Auras/Colored Magic Auras.esp')` via `source/server/tools/client-plugins.js` | `size: 23204`, `crc32: 1435824785`, `isLight: true`, `isMaster: false` |
| Inspeção de `runtime/server/data/aetherius-loadorder.json` e `client-source/client/profiles/*` + `C:\modOrganizer\profiles\*` | 436 plugins (126 regulares, 310 light); `Colored Magic Auras.esp` ausente em todos os perfis e desativado no `LOCAL` |
| Paridade SHA-256 (`overlays` vs `source`) — `blood-effects-client.js` | `75B13992767AAE3AE40830ACCD5B891A9DA5A4FD134F5818B809DB25DDA981DC` (idêntico) |
| Paridade SHA-256 (`overlays` vs `source`) — `veil-visuals.js` | `8826FFBF37922D930FFDF010D88A027B8F85119DBF5DEB7178E0F833AC825B75` (idêntico) |
| Paridade SHA-256 (`overlays/.../investigacoes` vs `Sobrenatural\docs\investigacoes`) | `2D99FBC342E390593D33ED8473C90B9D9BB50D4A68C5653BB79FAA3359E1BF01` (idêntico) |
