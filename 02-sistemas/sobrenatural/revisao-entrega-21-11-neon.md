# Revisão Cruzada — Pacote Definitivo `Entrega_Sobrenatural_21_11`

- **Pacote revisado:** `C:\Code\Aetherius - New Systems\Entrega_Sobrenatural_21_11` (montado por Opus 5.5, 21/11/2026)
- **Documentos base:** `LEIA-ME_INTEGRACAO.md`, `REBASE_OFICIAL_21-11.md`, `06-painel-adm/LEIA-ME_PAINEL-ADM.md`
- **Revisor:** Gemini 4 Neon (`gemini-4-neon-high`)
- **Data:** 21/11/2026
- **Veredito:** **APROVADO COM RESSALVAS (corrigir P1 e P2 antes de enviar ao integrador oficial)** — O código mesclado em 3 vias (`01-servidor`, `02-cliente/arquivos`, `06-painel-adm`), os binários (`ESPFE`, `BSA`, `po3_MoonMod.dll`, `skymp5-client.js`, `scam_native.node`), os hashes e os testes sobre cópias limpas do oficial estão sólidos. Porém a verificação binária após `git apply` e a auditoria dos perfis propostos revelaram **1 defeito P1 bloqueante** (o patch do cliente corrompe `witch-gauge-reference.png` removendo todos os bytes `0x0D`), **1 defeito P2** (`modlist.txt` da `primeira-publicacao` mantém `+Precision Creatures` ativo e omite `+AETHERIUS - LOBISOMEM TRANSFORMACAO`, contradizendo o guia) e **1 defeito P3** (3 arquivos residuais na pasta do mod em relação ao `form-map.json`).

---

## Achados da revisão

### [P1 — Bloqueante] `02-cliente/sobrenatural-cliente.patch` corrompe o binário `witch-gauge-reference.png` (1.617 bytes `0x0D` removidos)

- **Onde:** `Entrega_Sobrenatural_21_11/02-cliente/sobrenatural-cliente.patch` (a partir do byte `220909`, bloco `GIT binary patch` de `client/ui-core/modules/supernatural/witch-gauge-reference.png`).
- **Evidência reproduzida em cópia limpa de `C:\Code\Aetherius - OFICIAL\aetherius-client-main`:**
  - Arquivo em `02-cliente/arquivos/client/ui-core/modules/supernatural/witch-gauge-reference.png` (e no laboratório): **395.195 bytes**, SHA-256 `02e7abfa2771ad99c8200d6e6e260e2bb3497dede0cf9edd8ccc110d0a769cf7`, cabeçalho PNG válido (`89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52`), 15 chunks válidos, 0 erros de CRC.
  - Bloco no patch: `GIT binary patch` com **`literal 393578`** (blob `2b4d04230c8ceb56c05b2bb91e2a6645b87be347`).
  - Após `git apply` (tanto puro quanto com `--ignore-whitespace`), o arquivo gerado tem **393.578 bytes** (SHA-256 `5be69a91ce9a1bdd1c96a8d66e9341edd728d0685815a88b70652d595e299656`) e cabeçalho **`89 50 4e 47 0a 1a 0a 00 00 00 49 48 44 52`** (`INVALID PNG SIGNATURE`). Todos os **1.617 bytes `0x0D` (`\r`)** foram removidos do binário antes de gerar o diff — provavelmente pela normalização de fim de linha descrita em `REBASE_OFICIAL_21-11.md:11,29`, que também mascarou a diferença na conferência normalizada para LF.
  - Conferido: `sobrenatural-servidor.patch` tem `0` blocos `GIT binary patch` e `sobrenatural-cliente.patch` tem apenas este (`1`), portanto nenhum outro arquivo binário foi afetado.
- **Impacto:** O integrador que seguir `LEIA-ME_INTEGRACAO.md:151` (`git apply --ignore-whitespace sobrenatural-cliente.patch`) e empacotar o UI Core a partir do repositório do cliente distribuirá um PNG inválido, quebrando visualmente o Selo do Véu da Bruxa (`veil-hud.js:4`, `veil-seal.js:8,18–21`).
- **Correção requerida:** Regerar `02-cliente/sobrenatural-cliente.patch` com `git diff --binary` preservando o binário original de `witch-gauge-reference.png` (sem normalização de fim de linha em arquivos binários), atualizar o SHA-256 do patch em `LEIA-ME_INTEGRACAO.md:16`, e confirmar que o arquivo extraído por `git apply` tem exatamente `395.195` bytes e SHA-256 `02e7abfa2771ad99c8200d6e6e260e2bb3497dede0cf9edd8ccc110d0a769cf7`.

### [P2] `modlist.txt` em `07-load-order-e-manifestos/primeira-publicacao/profiles/` contradiz `LEIA-ME_INTEGRACAO.md`: mantém `+Precision Creatures` ativo e omite `+AETHERIUS - LOBISOMEM TRANSFORMACAO`

- **Onde:**
  - `07-load-order-e-manifestos/primeira-publicacao/profiles/AETHERIUS - GRAFICO - QUALIDADE/modlist.txt`
  - `07-load-order-e-manifestos/primeira-publicacao/profiles/AETHERIUS - PERFORMANCE/modlist.txt`
- **Evidência verificada:**
  - Em ambos os arquivos, a linha 128 (`índice 127`) ainda traz **`+Precision Creatures`** ativo, contradizendo `LEIA-ME_INTEGRACAO.md:166,173,203,245` (*"Precision Creatures: removido — Desativar e remover"*, substituído por `New Creature Animation - Werewolf`).
  - Em ambos os arquivos, **`+AETHERIUS - LOBISOMEM TRANSFORMACAO`** está **ausente** (aparece apenas `+AETHERIUS - LOBISOMEM` na linha 22/`índice 21`), contradizendo `LEIA-ME_INTEGRACAO.md:164` (tabela *"Mods para instalar e ativar nos perfis oficiais (QUALIDADE **e** PERFORMANCE)"*), `:196` (release a publicar) e `:245`.
- **Impacto:** O checklist (`LEIA-ME_INTEGRACAO.md:246`) instrui o integrador a aplicar `07-load-order-e-manifestos/primeira-publicacao` nos dois perfis oficiais e em `client/profiles`. Aplicar esses `modlist.txt` como estão deixaria `Precision Creatures` ativo junto com `New Creature Animation - Werewolf` e deixaria a animação/sons da transformação do lobisomem (`AETHERIUS - LOBISOMEM TRANSFORMACAO`) inativos em produção.
- **Correção requerida:** Nos dois `modlist.txt` da `primeira-publicacao`, desativar/remover `+Precision Creatures` e incluir `+AETHERIUS - LOBISOMEM TRANSFORMACAO` junto de `+AETHERIUS - LOBISOMEM`. Nenhum plugin é afetado (`plugins.txt`, `loadorder.txt`, `aetherius-loadorder.json` e os `437` slots travados em `plugin-slots.lock.json` permanecem idênticos).

### [P3] `03-mods-jogo/AETHERIUS - SOBRENATURAL/` inclui `Source/Scripts/*.psc` e `meta.ini` obsoleto de 19/11, divergindo de `07-load-order-e-manifestos/form-map.json`

- **Onde:** `03-mods-jogo/AETHERIUS - SOBRENATURAL/` (`73` arquivos) vs `07-load-order-e-manifestos/form-map.json` (`70` arquivos).
- **Evidência verificada:**
  - Além dos 70 arquivos aprovados na Fase 2, a pasta de jogo contém `Source/Scripts/PlayerWerewolfChangeScript.psc` (colide em path com o `.psc` de Manbeast com bytes diferentes — ver `INVESTIGACAO_21-11_unificar-esp.md:65`), `Source/Scripts/COTV_VampireBatFormScript.psc` e `meta.ini` antigo (`comments=Generated by houseCARL - load-order patch`, `editedInPlace=2026-11-19T02:19:59.9110632Z`).
- **Impacto:** Não afeta o runtime do jogo (Skyrim lê `.pex`, e `LEIA-ME_INTEGRACAO.md:195` observa que `Source\` é opcional), mas gera divergência de 3 arquivos contra `form-map.json` e mantém metadado de 19/11 anterior à unificação.
- **Correção sugerida:** Remover `Source/Scripts/*.psc` da pasta de runtime (ou movê-los para `Docs/Sources/`) e remover/atualizar o `meta.ini` antigo, alinhando `03-mods-jogo/AETHERIUS - SOBRENATURAL/` aos 70 arquivos de `form-map.json`.

---

## Verificações confirmadas (6 critérios do despacho)

| Critério | Resultado | Evidência verificada |
|---|---|---|
| **(1) Paridade com `Entrega_Sobrenatural_20_11`** | **OK** | `01-servidor/arquivos`: `87 → 92` (`0` removidos, `+5` novos, `9` alterados). `02-cliente/arquivos`: `34 → 39` (`0` removidos, `+5` novos, `5` alterados). `03-mods-jogo`: `737 → 739`; os `67` removidos são exatamente `VISOES.esp` (`1`), `Sound/FX/Aetherius/Witch/*.wav` (`4`), os `61` WAVs de `AETHERIUS - Vampire Lord and Werewolf SFX` (`61`) e seu `meta.ini` (`1`). Todos os **65/65 WAVs** estão em `03-mods-jogo/AETHERIUS - SOBRENATURAL/sound/` com **SHA-256 100% idêntico** à 20/11. Demais mods de terceiros e kdu sem perda. `05-documentacao`: `93 → 116` (`0` removidos). `06-painel-adm`: `10/10` idênticos (patch `004-admin-panel-client.patch` aplica limpo no oficial e reproduz `app.js`; 7/7 arquivos de `servidor/` idênticos a `01-servidor/arquivos/server/gamemode/admin-panel/`). `LISTA-ARQUIVOS.txt`: `92/92` (servidor) e `39/39` (cliente) sem discrepâncias. |
| **(2) Aplicação limpa dos patches sobre cópias limpas do oficial** | **Servidor OK · Cliente com P1** | `01-servidor/sobrenatural-servidor.patch` aplica limpo (`git apply --ignore-whitespace`, `rc=0`) sobre `aetherius-server-main` oficial e reproduz **92/92** arquivos idênticos (LF) a `01-servidor/arquivos/`. `02-cliente/sobrenatural-cliente.patch` aplica sem erro de sintaxe (`rc=0`) e reproduz **38/39** arquivos de texto idênticos (LF), mas corrompe `witch-gauge-reference.png` (**P1 acima**). `ENTREGA_PERFORMANCE_UI_2026-11-20/patch.diff` aplica limpo (`git apply --check --ignore-whitespace`, `rc=0`) sobre o par `aetherius-server-main` + `aetherius-client-main` já com o Sobrenatural. |
| **(3) Mesclagem 3 vias (oficial 19/11 + delta 21/11)** | **OK** | Conferidos os diffs do laboratório `source` (`9e313e4..5bc981d`, 14 arquivos), `client-source` (`ae7c172..c6134b2`, 10 arquivos) e `overlays` (`d830ca7..a7b3a3b`). Todos os 14 arquivos de servidor e 9/10 de cliente são byte a byte idênticos (LF) ao laboratório. Em `client/src/view/formView.ts`, o diff 3 vias preservou integralmente a mudança do oficial de 19/11 (`isInHostileFaction`, `hostileCheckedRefr`, `Aggression = 2`) **e** o delta DVA de 21/11 (`applyDvaDelta`, `tryApplyDvaDelta`). O bundle `04-binarios-referencia/skymp5-client.js` contém `AETHERIUS - SOBRENATURAL.esp`, `0x90e`, `applyDvaDelta`, `AetheriusSkyClockSnapshot`, `isInHostileFaction` e não contém `AETHERIUS - DVA Visuals.esp`. |
| **(4) Hashes citados em `LEIA-ME_INTEGRACAO.md`** | **OK** | Todos conferidos e exatos: `sobrenatural-servidor.patch` (`3161af26…`), `sobrenatural-cliente.patch` (`6ef70753…` — precisará atualizar após correção do P1), `AETHERIUS - SOBRENATURAL.esp` (`95d3ef67…`, CRC32 `2067681845`), `AETHERIUS - SOBRENATURAL.bsa` (`3c17595c…`), `po3_MoonMod.dll` (`7bad2a4c…`), `skymp5-client.js` (`9d8e4599…`), `scam_native.node` (`2a4e6779…`) e `po3_MoonMod.ini` do Rudy ENB (`07927BD2…C89B`). |
| **(5) Consistência do guia com código, manifests e decisões do dev** | **OK exceto P2** | Todas as variáveis de ambiente de §2.4 conferem em nome e padrão com `01-servidor/arquivos/` (`forced-werewolf.js:19–21`, `vampire-appearance.js:4`, `powers.js:231–233`, `werewolf-service.js:36–60`, `feeding.js:8–9`, `blood-effects.js:4–5`, `veil-ritual.js:30`, `survival-service.js:56`, `vampire-sun.js:6`, `environmentSystem.ts:65`). Decisões do dev (ADR-0027, ADR-0028, keywords só no servidor, raiz `MeridianUI` única com 16 ativos, `0x39` só no laboratório, limites de AE 1.6.1170 e Argoniano/Khajiit) fielmente refletidas. Divergência localizada nos `modlist.txt` da `primeira-publicacao` (**P2 acima**). |
| **(6) Testes executados na árvore oficial mesclada (cópias limpas + patches)** | **OK** | Gamemode (`werewolf.test.cjs`, `magic.test.cjs`, `records-ctda.test.cjs`, `summon-friends.test.cjs`, `survival-service.test.js`, `supernatural/tests/masser.test.cjs`): **163/163 PASS** (`17 suites`, `rc=0`). `teste-servico.cjs` contra `server/gamemode` mesclado: **PASS** (`rc=0`). Cliente (`moon-hud.test.cjs`, `time-service.test.cjs`): **8/8 PASS** (`rc=0`). `npx tsc --noEmit` no servidor mesclado: **PASS** (`rc=0`). `npx tsc --noEmit` no cliente mesclado: **PASS** (`rc=0`). |

---

## Observação opcional (não bloqueante)

- **Contagem citada para os testes de gamemode (`LEIA-ME_INTEGRACAO.md:22`, `REBASE_OFICIAL_21-11.md:31`):** o guia cita `164/164` para o conjunto `werewolf, magic, records-ctda, summon-friends, survival, Masser`; na execução direta sobre esses 6 arquivos na árvore oficial mesclada, o `node --test` reportou **163/163** (`0 fail`, `rc=0`). Diferença apenas cosmética na contagem escrita, sem falha funcional.

---

## Resumo do que falta antes de liberar o pacote ao outro dev

1. **[P1 — obrigatório]** Regerar `02-cliente/sobrenatural-cliente.patch` preservando os bytes originais de `witch-gauge-reference.png` (`395.195` bytes, SHA-256 `02e7abfa…`) e atualizar o SHA-256 do patch em `LEIA-ME_INTEGRACAO.md:16`.
2. **[P2 — obrigatório]** Ajustar os dois `modlist.txt` em `07-load-order-e-manifestos/primeira-publicacao/profiles/` para desativar/remover `+Precision Creatures` e adicionar `+AETHERIUS - LOBISOMEM TRANSFORMACAO`.
3. **[P3 — recomendado]** Limpar `Source/Scripts/*.psc` e o `meta.ini` de 19/11 de `03-mods-jogo/AETHERIUS - SOBRENATURAL/` para bater 70/70 com `07-load-order-e-manifestos/form-map.json`.

---

## Reconfirmação — Gemini 4 Neon (21/11/2026)

- **Veredito final:** **APROVADO SEM ACHADOS** — Os três achados (P1, P2 e P3) foram corrigidos e verificados de forma independente. O pacote `Entrega_Sobrenatural_21_11` está pronto para entrega ao integrador oficial.

| Achado | Situação | Evidência reconfirmada |
|---|---|---|
| **P1 · `sobrenatural-cliente.patch` / PNG** | **Resolvido** | Novo SHA-256 `ad6d40e66c52c901388f8512fcb76f3080df2457ed6fe252f7107580700d8f15` conferido no disco e atualizado em `LEIA-ME_INTEGRACAO.md` (hash antigo `6ef70753` removido). Aplicado com `git apply --ignore-whitespace` sobre cópia limpa de `aetherius-client-main` (`rc=0`), reproduziu **39/39** arquivos (`0` diferenças); `client/ui-core/modules/supernatural/witch-gauge-reference.png` saiu em match binário bruto exato (`395.195` bytes, SHA-256 `02e7abfa2771ad99c8200d6e6e260e2bb3497dede0cf9edd8ccc110d0a769cf7`, assinatura PNG válida, `15` chunks, `0` erros de CRC). |
| **P2 · `modlist.txt` da `primeira-publicacao`** | **Resolvido** | Conferidos `AETHERIUS - GRAFICO - QUALIDADE/modlist.txt` e `AETHERIUS - PERFORMANCE/modlist.txt`: ambos trazem `-Precision Creatures` (linha 129/`índice 128`) e `+AETHERIUS - LOBISOMEM TRANSFORMACAO` (linha 22/`índice 21`) imediatamente acima de `+AETHERIUS - LOBISOMEM` (linha 23/`índice 22`), mantendo `+AETHERIUS - SOBRENATURAL` no topo. |
| **P3 · Pasta do mod vs `form-map.json`** | **Resolvido** | `03-mods-jogo/AETHERIUS - SOBRENATURAL/` tem exatamente **70 arquivos**, sem `Source/Scripts/*.psc` e sem o `meta.ini` antigo, conferindo **70/70 por SHA-256** com `07-load-order-e-manifestos/form-map.json` (`0` extras, `0` faltantes, `0` divergências). Referências a `Source\`/`.psc` limpas de `LEIA-ME_INTEGRACAO.md`. |

