# Revisão Cruzada — Unificação do ESP e Pasta do Sobrenatural

- **Documento revisado:** `Sobrenatural/docs/investigacoes/INVESTIGACAO_21-11_unificar-esp.md` (Sol High, 21/11/2026)
- **Revisor:** Gemini 4 Neon (`gemini-4-neon-high`)
- **Data:** 21/11/2026
- **Veredito:** **APROVADO COM RESSALVAS** — A arquitetura do plugin único `AETHERIUS - SOBRENATURAL.esp` (ESPFE v1.71, 167 records: 149 novos + 18 overrides, DVA Visuals remapeado `+0x100` para `0x900–0x932`) e o tratamento de SFX/DLL/fontes estão tecnicamente corretos e confirmados por leitura independente dos binários e do código. Porém há **1 defeito bloqueante (P1)** no plano de pasta única para o `MeridianUI` e **2 defeitos P2** (omissão/exclusão de asset obrigatório de runtime da HUD da Bruxa e mistura do impacto de migração do laboratório LOCAL com o ambiente de produção oficial) que precisam ser corrigidos antes da fase de implementação.

---

## Resposta direta à pergunta do coordenador: impacto LOCAL vs produção oficial

**Confirmado: o deslocamento dos 34 plugins regulares e a migração de slots (`5C → FE137` + remapeamento DVA `0x800 → 0x900`) afetam SOMENTE o laboratório (`AETHERIUS - LOCAL`) — NÃO a produção oficial.**

Evidência verificada diretamente nos arquivos do servidor e cliente oficiais (`C:\Code\Aetherius - OFICIAL\`):

| Arquivo / ambiente | Total | `SOBRENATURAL.esp` | `VISOES.esp` | `DVA Visuals.esp` | `Miggyluv's Eyes.esp` | `Colored Magic Auras.esp` | `Werewolf_New_Anim.esp` | Últimos slots travados |
|---|---|---|---|---|---|---|---|---|
| **OFICIAL** `server/config/loadorder-v0.42.json` (`d7351df4…`) | 436 (310 leves) | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | `AETHERIUS - LOBISOMEM.esp` presente; `Predator Vision.esp` regular |
| **OFICIAL** `server/config/plugin-slots.lock.json` (`18881597…`) | 437 | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | Regular `125` (`0x7D`, `EnemyModsGhostshelfKeep.esp`); Leves `308–310` (`FE134` `LOBISOMEM`, `FE135` `GHOSTSHELF`, `FE136` `SACERDOTES`) |
| **OFICIAL** `client/profiles/{GRAFICO - QUALIDADE, PERFORMANCE}/plugins.txt` | 426 ativos (idênticos) | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | **Ausente** | `*AETHERIUS - LOBISOMEM.esp`, `*AETHERIUS - GHOSTSHELF.esp`, `*AETHERIUS - SACERDOTES.esp` |
| **LOCAL** `runtime/server/data/aetherius-loadorder.json` | 440 | Regular `5C` | Regular (fim) | Leve `FE137` | Leve | Leve | Regular | **35 regulares após `5C`** (= 34 sobreviventes + `VISOES` removido) |
| **MO2 local** `AETHERIUS - GRAFICO - QUALIDADE/plugins.txt` | 428 ativos | Ausente | Ausente | Presente (pos 426) | Presente (pos 427) | Ausente | Ausente | 426 oficiais + `DVA Visuals` + `Miggyluv` instalados localmente |
| **MO2 local** `AETHERIUS - PERFORMANCE/plugins.txt` | 427 ativos | Ausente | Ausente | Ausente | Presente (pos 426) | Ausente | Ausente | 426 oficiais + `Miggyluv` instalado localmente |

**Consequência prática para produção:** ao publicar o sistema Sobrenatural no servidor oficial anexando `AETHERIUS - SOBRENATURAL.esp` (ESPFE) no final da lista leve (`FE137`, após `AETHERIUS - SACERDOTES.esp`), seguido das dependências novas (`Miggyluv's Eyes.esp` em `FE138`, `Colored Magic Auras.esp` em `FE139`) e `Werewolf_New_Anim.esp` no final dos regulares (`0x7E`), **zero dos 437 plugins travados em `plugin-slots.lock.json` sofrem deslocamento de slot** e **nenhuma migração de slots regulares ou de FormIDs DVA é necessária em produção**. O custo de migração descrito na investigação existe apenas para preservar dados/personagens do laboratório LOCAL (se o dev optar por migrá-los em vez de resetar o ambiente de teste).

---

## Achados da revisão

### [P1 — Bloqueante] Dividir `MeridianUI` entre `AETHERIUS - SOBRENATURAL` e `AETHERIUS - UI CORE` viola regra canônica e causa 404

- **Referência na proposta:** `INVESTIGACAO_21-11_unificar-esp.md:64` (tabela da pasta final: `MeridianUI\aetheriusui\modules\supernatural\*` loose dentro da pasta `AETHERIUS - SOBRENATURAL`, mantendo `hud.html`, `modules-config.js`, `shell.js` e `index.html` no `AETHERIUS - UI CORE`) e `:131` (*"O UI Core retém o shell e os runtimes. Retirar a duplicata do subdiretório do módulo do build compartilhado quando a nova release carregar a sua versão loose."*).
- **Evidência:**
  - `Sobrenatural/docs/DECISOES.md:43`: *"Regra técnica: no Meridian, uma pasta `MeridianUI` **nunca** pode vir de dois mods (dá 404). Alterações de interface só em cópias completas `(LOCAL)` dos mods."*
  - `Sobrenatural/docs/diario.md:78,88`: incidente real já enfrentado no projeto (15/11/2026) — colocar arquivos em `MeridianUI/` num segundo mod gerou **404 por raiz canônica** e travou o cliente na tela até fechar o jogo, sendo resolvido exigindo raiz única completa.
- **Risco:** A aplicação literal das linhas 64 e 131 quebrará a interface CEF (404 no módulo Sobrenatural ou no restante do UI Core, conforme qual mod vencer a resolução da raiz `MeridianUI\aetheriusui\`).
- **Correção necessária:** Remover `MeridianUI\aetheriusui\modules\supernatural\*` da pasta MO2 `AETHERIUS - SOBRENATURAL`. Todos os arquivos de interface do módulo Sobrenatural devem ser publicados **junto com o delta compartilhado dentro do único mod `AETHERIUS - UI CORE`** (e na cópia completa `AETHERIUS - UI CORE (LOCAL)` no laboratório).

### [P2] Omissão e exclusão explícita de `witch-gauge-reference.png`, que é asset de runtime obrigatório da HUD da Bruxa

- **Referência na proposta:** `INVESTIGACAO_21-11_unificar-esp.md:30` (*"15 arquivos exclusivos de runtime do módulo, mais PNG de referência"*), `:64` (lista apenas 15 arquivos JS/CSS) e `:65` (*"excluir backups locais, testes, referências e metadados copiados de outros mods do pacote de runtime"*).
- **Evidência:**
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-hud.js:4,16`:
    `const gaugeUrl=new URL('witch-gauge-reference.png',document.currentScript.src).href;` e `seal=window.createVeilSeal(gaugeUrl);`
  - `C:\Code\Aetherius - Servidor Local\client-source\client\ui-core\modules\supernatural\veil-seal.js:8,18-21`:
    o arquivo renderiza `<image href="${href}" ...>` em seis camadas SVG (`veil-left`, `veil-right`, `veil-metal-overload`, `light`, `veil-crystal-glow`) usando exatamente `witch-gauge-reference.png` como arte do Selo do Véu.
  - O diretório de build `client-source\client\ui-core\modules\supernatural\` contém exatamente **16 arquivos** (os 15 JS/CSS + `witch-gauge-reference.png`), enquanto o MO2 LOCAL soma 20 arquivos apenas porque acumula `supernatural-test.js` e 3 `.bak-lua-2111`.
- **Risco:** Seguir a linha 65 e excluir `witch-gauge-reference.png` quebra visualmente a HUD da Bruxa em runtime (selo invisível / imagem quebrada).
- **Correção necessária:** Corrigir o inventário de runtime de **15 para 16 arquivos**, mantendo `witch-gauge-reference.png` obrigatório no pacote de runtime (dentro de `AETHERIUS - UI CORE`, conforme P1).

### [P2] Mistura de impacto LOCAL vs produção oficial na análise de risco/alternativa e deslocamento evitável de `Miggyluv` em PERFORMANCE

- **Referência na proposta:** `INVESTIGACAO_21-11_unificar-esp.md:5,73,104,124,139,147`.
- **Evidência:** Conforme detalhado na seção inicial, `loadorder-v0.42.json`, `plugin-slots.lock.json` e ambos os perfis oficiais versionados em `aetherius-client-main/client/profiles/` (426 plugins ativos) **não contêm** `AETHERIUS - SOBRENATURAL.esp`, `AETHERIUS - SOBRENATURAL - VISOES.esp`, `AETHERIUS - DVA Visuals.esp`, `Miggyluv's Eyes.esp`, `Colored Magic Auras.esp` nem `Werewolf_New_Anim.esp`.
- **Problema:**
  1. A seção de decisão (`:104`, `:147`) apresenta o dilema *"ESPFE com migração vs manter slot regular `5C`"* e o risco de deslocar 34 plugins regulares (`:139`) como se pesasse sobre a entrega oficial, quando esse deslocamento existe **exclusivamente no laboratório LOCAL**. Em produção, o ESPFE unificado entra em slot livre no fim da lista leve (`FE137`) sem mover nenhum dos 437 plugins travados — o que elimina a principal razão técnica da alternativa regular (`5C`) para produção.
  2. Na linha 73, o deslocamento de `Miggyluv's Eyes.esp` (`FE137 → FE138`) em `PERFORMANCE` só existe no MO2 desta máquina (onde `Miggyluv` foi ativado localmente fora do perfil oficial). Ao promover para o repositório oficial (`aetherius-client/client/profiles/`), `AETHERIUS - PERFORMANCE` e `AETHERIUS - GRAFICO - QUALIDADE` recebem juntos a mesma lista final (requisito validado por `server/tools/sync-client-plugins.js:17-19,47-48`), sem deslocamento em produção.
- **Correção necessária:** Separar explicitamente na seção de migração e decisão o escopo **Produção (OFICIAL)** — zero deslocamento nos 437 plugins travados, sem necessidade de remapper de banco para slots regulares/DVA — do escopo **Laboratório (LOCAL)** — onde ocorre a troca `5C → FE137` + 34 regulares + `aetherius-veil-eyes.json` (com opção de migração ou reset limpo dos dados de teste locais).

---

## Verificações confirmadas (sem defeitos)

| Área verificada | Resultado da auditoria independente |
|---|---|
| **Contagem binária dos 3 ESPs** | **Confirmada (167 records = 149 próprios + 18 overrides):** `AETHERIUS - SOBRENATURAL.esp` v1.71 regular (`99` próprios em `0x800–0x82C` [45] e `0x833–0x868` [54], `17` overrides, `4` VMAD nos 4 overrides MGEF do COTV, 4 masters); `VISOES.esp` v1.71 regular (`0` próprios, `1` override `SPEL 0C4DE1:Skyrim.esm`, `0` VMAD, 2 masters); `DVA Visuals.esp` v1.70 ESL (`50` próprios em `0x800–0x82F` + `0x831–0x832`, `0` overrides, `0` VMAD, 2 masters). |
| **Colisões de FormID e remapeamento** | **Confirmado:** exatamente **45 colisões** entre Principal e DVA (`0x800–0x82C`). Com `+0x100` (`0x900–0x932`, buraco preservado em `0x930`): **0 colisões**, ocupando 149/2.048 slots da faixa ESL legada `0x800–0xFFF` (7,3%). |
| **Compatibilidade de código C++ e cliente com ESPFE** | **Confirmada:** `MpActor.cpp:1682-1692` e `MagicResistance.h:107-113` já usam `PluginIndexMapping::GetEspmFileIndexByFilename("AETHERIUS - SOBRENATURAL.esp")` + `RuntimeFormIdToFormDesc(id).shortFormId` (independe de regular vs ESL). `powers.js:91,133`, `vampire-lord-service.js:50`, `veil-ritual-fx.js:4` e `veilEffects.ts:4,82` usam `Game.getFormFromFile(local, 'AETHERIUS - SOBRENATURAL.esp')` com todos os IDs `<= 0x868` (`<= 0xFFF`). |
| **Mapa de consumidores de código** | **Completo:** varredura em `source/server/**` e `client-source/client/**` não encontrou nenhum consumidor de `AETHERIUS - SOBRENATURAL.esp`, `VISOES.esp` ou `DVA Visuals.esp` fora da tabela da proposta. `vampire-appearance.js:3-9,24-35`, `veil-eyes.js:97-116` (já usa `mp.getIdFromDesc` para `vampire.PLUGIN`), `appearance.ts:128-142,149,212,222` (`isDvaForm` já exige `id >>> 24 === 0xfe`) e `form-resolver.js:19-39` estão mapeados com precisão. |
| **Conflitos de áudio SFX e Hypertrichosis** | **Confirmados:** `AETHERIUS - Vampire Lord and Werewolf SFX` tem exatamente **61 WAVs**, com **25/25** de sobreposição com `Hypertrichosis -Werewolf Sounds Rework-`. Mantê-los loose na pasta final é obrigatório para preservar precedência sobre Hypertrichosis. |
| **DLL do céu e `security-manifest.json`** | **Confirmado:** `C:\Code\Aetherius - OFICIAL\aetherius-server-main\server\config\security-manifest.json` (`2c6463cb…`) contém apenas o hash upstream `979dba0b8ab2ada7…` (`po3_MoonMod.dll`) e não o hash do fork `7bad2a4c…`. A regeração do manifesto na promoção do fork está corretamente apontada. |
| **Fontes Papyrus e pacote kdu** | **Confirmado:** `AETHERIUS - SOBRENATURAL` não tem PEX próprio; retirar `PlayerWerewolfChangeScript.psc` e `COTV_VampireBatFormScript.psc` da árvore de runtime elimina a colisão de `.psc` com Manbeast sem afetar os `.pex` efetivos. `AETHERIUS - LOBISOMEM.esp` e `AETHERIUS - LOBISOMEM TRANSFORMACAO` seguem isolados conforme escopo do dev. |

---

## Observações opcionais (não bloqueantes)

1. **Contagem na pasta `AETHERIUS - SERVIDOR LOCAL` (`:29`):** atualmente há 9 arquivos (dois backups de bundle: `skymp5-client.js.bak-ceu-2111` e `skymp5-client.js.bak-dva-2111`, em vez de 8 arquivos / 1 backup). Não altera a ação planejada (extrair apenas o ESP de Visões e os 4 WAV da Bruxa).
2. **Perfis versionados no repositório do cliente (`sync-client-plugins.js`):** ao promover a pasta única para `AETHERIUS - PERFORMANCE` e `AETHERIUS - GRAFICO - QUALIDADE`, atualizar também `aetherius-client/client/profiles/` junto com o MO2 local; `server/tools/sync-client-plugins.js:100-118` aborta o build se houver drift entre o MO2 e os perfis commitados no repositório.
3. **Master `Update.esm` (`:102`):** na montagem final do ESPFE, conferir se `0C4DE1:Skyrim.esm` de `VISOES.esp` realmente referencia algum form de `Update.esm`; se não referenciar, remover `Update.esm` dos masters mantidos pela ferramenta conforme já sugerido no próprio documento.

---

## Resumo das ações antes da fase de implementação

1. **Corrigir P1:** remover `MeridianUI\aetheriusui\modules\supernatural\*` da pasta MO2 `AETHERIUS - SOBRENATURAL` e mantê-lo integralmente em `AETHERIUS - UI CORE` (`(LOCAL)` no laboratório).
2. **Corrigir P2 (UI):** incluir `witch-gauge-reference.png` na lista obrigatória de runtime (**16 arquivos**, não 15).
3. **Corrigir P2 (escopo de migração):** registrar explicitamente que o deslocamento de 34 plugins regulares e a migração de slots/DVA afetam **somente o laboratório LOCAL**, enquanto **produção (OFICIAL)** recebe o ESPFE unificado no fim da lista leve com **zero deslocamento** dos 437 plugins travados.

## Resposta do autor — Sol High, 21/11/2026

O orquestrador aceitou os três achados. Atualizei a investigação e conferi os seguintes pontos antes de preparar o candidato da Fase 2.

| Achado | Resposta |
|---|---|
| P1 · Raiz MeridianUI | Aceito. O plano e o roteiro mantêm o MeridianUI inteiro no `AETHERIUS - UI CORE (LOCAL)` no laboratório e no `AETHERIUS - UI CORE` na distribuição oficial. O conteúdo de jogo consolidado tem sua própria pasta. O aceite em CEF exige um único provedor da raiz, com as páginas compartilhadas e o módulo disponíveis sem 404. |
| P2 · PNG da HUD da Bruxa | Aceito. Conferi `veil-hud.js:4,16`, `veil-seal.js:8,18–21` e os 16 arquivos do módulo fonte. `witch-gauge-reference.png` é a arte do Selo do Véu em runtime e faz parte dos 16 ativos obrigatórios no UI Core. |
| P2 · Ensaio LOCAL e integração OFICIAL | Aceito. Conferi os 426 nomes ativos iguais nos dois perfis oficiais e os 437 slots do lock. Os 34 deslocamentos e a troca dos IDs DVA são efeitos do laboratório. A primeira publicação oficial acrescenta o ESPFE ao fim dos leves e preserva os 437 slots. Miggyluv e as demais dependências continuam separadas. |

As observações sobre o segundo backup do bundle e a atualização dos perfis versionados foram registradas. Os links do override de Visões são conferidos durante a montagem para determinar se `Update.esm` precisa constar no cabeçalho final. O dev aprovou ESPFE, dados de teste limpos após backup integral, fork do céu nos três perfis e redistribuição do derivado DVA e dos WAV. Instalação e reset aguardam a revisão do candidato e autorização. [Investigação corrigida](investigacoes/INVESTIGACAO_21-11_unificar-esp.md#revisão-e-correções)

---

## Revisão da Fase 2 — Gemini 4 Neon (21/11/2026)

- **Artefatos revisados:** `Sobrenatural/docs/ETAPA-4_unificacao-esp.md`, `Sobrenatural/entregas/Unificacao_ESP_21_11/` (ESPFE, BSA, 65 WAVs, `po3_MoonMod.dll`, `form-map.json`, `gerar-candidato.py`, `gerar-manifestos.cjs`, `integracao/{laboratorio,primeira-publicacao,ui-core-mod,*.json,*.patch}`), árvores de trabalho em `C:\Code\Aetherius - Servidor Local\{source,client-source,overlays\sobrenatural}` e espelho oficial `C:\Code\Aetherius - OFICIAL\`.
- **Veredito da Fase 2:** **APROVADO SEM ACHADOS** — Os três achados da Fase 1 (1 P1 e 2 P2) foram integralmente resolvidos, todos os artefatos binários/manifestos/patches foram auditados de forma independente e todas as suítes de testes (Node em ambos os modos, nativo Catch2 contra o ESPFE candidato, CTest do céu e TypeScript) passaram com 0 falhas. Pronto para autorização do dev para instalação no laboratório e homologação em jogo.

### 1. Fechamento dos achados da Fase 1 (P1 e P2)

| Achado anterior | Situação na Fase 2 | Evidência verificada |
|---|---|---|
| **P1 · Raiz `MeridianUI` dividida** | **Resolvido** | A pasta candidata `AETHERIUS - SOBRENATURAL\` contém **0 arquivos `MeridianUI`** (e 0 `.psc`/`.pex`). A árvore completa de UI (`401` arquivos, incluindo `hud.html`, `index.html`, `modules-config.js`, DLLs Meridian/Bridge/Calendar e bundle `skymp5-client.js` SHA-256 `976562ff…`) está consolidada em `integracao\ui-core-mod\` como raiz única, omitindo apenas os 7 arquivos descartáveis do LOCAL (`4 .bak-lua-2111`, `supernatural-test.js`, `MeridianUI.ini.disabled`, `aetherius-release-manifest.json`). |
| **P2 · `witch-gauge-reference.png` omitido** | **Resolvido** | `integracao\ui-core-mod\MeridianUI\aetheriusui\modules\supernatural\` contém todos os **16 arquivos** de runtime (incluindo `witch-gauge-reference.png`), com **16/16 hashes SHA-256 idênticos** entre `ui-core-assets.json`, `ui-core-mod` e `client-source\client\ui-core\modules\supernatural\`. |
| **P2 · Escopo LOCAL vs produção oficial** | **Resolvido** | `INVESTIGACAO_21-11_unificar-esp.md`, `ETAPA-4_unificacao-esp.md` e `gerar-manifestos.cjs` separam completamente `integracao\laboratorio\` (438 plugins, 34 deslocamentos regulares documentados em `slots-deslocados-laboratorio.json`, início limpo pós-backup) de `integracao\primeira-publicacao\` (441 plugins, 437 slots oficiais preservados sem deslocamento). |

### 2. Auditoria independente dos artefatos e do código

| Área auditada | Resultado | Detalhes verificados |
|---|---|---|
| **ESPFE candidato (`AETHERIUS - SOBRENATURAL.esp`)** | **OK** | SHA-256 `95d3ef67cfe680544f77f9ae25c46bd67168deb4289f82cdc12263eb36605061`, CRC32 assinado `2067681845`. TES4 v1.71, flags `0x200` (ESL sem ESM), `HEDR.numrec=174` (`167` records + `7` GRUPs), `HEDR.nextobj=0x933`. Masters: `Skyrim.esm`, `Dawnguard.esm`, `MasterSpellsRunes.esp`, `Curse of the Vampire.esp` (`Update.esm` dispensado porque os 5 FormLinks `MDOB`/`ETYP`/3×`EFID` de `VampireHuntersSight` `0C4DE1:Skyrim.esm` apontam todos para master `0`). `116/116` records da base preservados byte a byte (incluindo os 4 overrides MGEF do COTV com VMAD); `VampireHuntersSight` idêntico em subrecords/flags/ver; `50/50` records DVA remapeados para `0x900–0x932` (buraco `0x930` preservado, 0 colisões, todos `<= 0xFFF`), `12/12` HDPT `TNAM` → TXST próprios (`0x04000900–0x04000905`), `2/2` FLST `LNAM` → HDPTs remapeadas, 0 FormLinks remanescentes com índice `0x02`. |
| **Mapa de FormKeys, resolvedores, catálogos e F7** | **OK** | `form-map.json` confere `167/167` records e `70/70` arquivos da pasta candidata (0 divergências de SHA-256). `catalogo-aparencia.json` confere `42/42` entradas contra `form-map.json` e `vampire-appearance.js`. Em `veil-eyes.js`, a checagem de round-trip `actual.toLowerCase() === wanted.toLowerCase()` foi conferida contra o C++ real (`server_guest_lib/FormDesc.cpp:7`, formato `"%0x%c%s"`, hex sem zero-padding — 100% compatível). `appearance.ts` atualizado para `"AETHERIUS - SOBRENATURAL.esp"` e faixas `0x90e`/`0x914` e `0x906..0x90d`, `0x91a..0x92f`. Catálogos F7 (`item-catalog.cjs`, `catalog-validation.cjs`, `supernatural-admin.cjs`) têm 0 referências aos plugins retirados. Espelho em `overlays/sobrenatural/files` idêntico byte a byte a `source/`. Todos os 3 patches (`servidor-unificado.patch`, `cliente-unificado.patch`, `overlay-regressao-unificado.patch`) passaram em `git apply --reverse --check` (`rc=0`). |
| **BSA, arquivos loose e vencedores de VFS** | **OK** | `AETHERIUS - SOBRENATURAL.bsa` (SSE v105, `46` arquivos: `6 NIF + 40 DDS`) tem SHA-256 `3c17595c…` idêntico ao BSA DVA Visuals. Os `65` WAVs (`61 SFX + 4 Witch`) ficam loose sob `sound\`. Varredura de sobreposições contra todos os mods ativos do perfil proposto `PP GRAFICO` em `C:\modOrganizer\mods\` encontrou exclusivamente os dois conflitos pretendidos: `Hypertrichosis -Werewolf Sounds Rework-` (**25/25** WAVs vencidos pelo candidato loose) e `Moons and Stars - Sky Overhaul SKSE` (**1** arquivo: `SKSE\Plugins\po3_MoonMod.dll` vencido pelo fork, sem `po3_MoonMod.ini` concorrente). `New Creature Animation - Werewolf` vs `Pandora - Output`: 0 sobreposições. |
| **Manifests oficiais propostos (`primeira-publicacao`)** | **OK** | `plugin-slots.lock.json` tem `441` plugins: **0 faltando e 0 deslocados** entre os `437` slots travados no oficial (`C:\Code\Aetherius - OFICIAL\aetherius-server-main\server\config\plugin-slots.lock.json`), com 4 adições ao final (`AETHERIUS - SOBRENATURAL.esp` `FE137`, `Miggyluv's Eyes.esp` `FE138`, `Colored Magic Auras.esp` `FE139`, `Werewolf_New_Anim.esp` regular `0x7E`). `AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE` têm `plugins.txt` (`430`) e `loadorder.txt` (`441`) 100% idênticos, masters e `Predator Vision.esp` antes do ESPFE, e `+AETHERIUS - SOBRENATURAL` no topo de `modlist.txt` (acima de Hypertrichosis e MoonMod upstream). |
| **Manifests do laboratório (`laboratorio`)** | **OK** | `438` plugins, `AETHERIUS - SOBRENATURAL.esp` em `FE137`, exatamente `34` deslocamentos regulares registrados em `slots-deslocados-laboratorio.json`, mods antigos desmarcados (`-`), `+AETHERIUS - SERVIDOR LOCAL` mantido acima de `+AETHERIUS - UI CORE (LOCAL)`. |
| **DLL do céu e manifesto de segurança** | **OK** | `SKSE\Plugins\po3_MoonMod.dll` (`968.704` bytes, SHA-256 `7bad2a4c…`) contém os marcadores esperados (`po3_MoonMod`, `2.0.2`, `1.6.1170`, `Aetherius`) e acompanha `Docs\AetheriusCeu\{LICENSE,CREDITS.md}`. `security-manifest.delta.json` referencia o `baseSha256` real do `security-manifest.json` oficial e declara o hash e o caminho `mods/AETHERIUS - SOBRENATURAL/SKSE/Plugins/po3_MoonMod.dll`. |
| **Roteiro de instalação, backup, rollback e início limpo** | **OK** | Conferido `scripts\Reset.ps1`: para servidor e PostgreSQL antes de mover `state`, `world-local`, `storage` e `data\aetherius-*.json` (preservando `aetherius-loadorder.json`) para `backups\reset-<timestamp>`. O roteiro em `ETAPA-4_unificacao-esp.md:51-69` está coerente (backup integral pré-troca com PG parado, atualização do bundle em `AETHERIUS - SERVIDOR LOCAL`, UI Core em raiz única, `Prepare.ps1` após troca de perfil, `Reset.ps1` apenas no LOCAL, rollback atômico e checklist de homologação em jogo). |

### 3. Execução independente das suítes de testes

| Suíte executada | Resultado |
|---|---|
| **Node canônico (`AETHERIUS_VAMPIRE_FEED_ENABLED=true`)** | **569/569 PASS** (`19 suites`, `0 fail`, `rc=0`) |
| **Node canônico (`AETHERIUS_VAMPIRE_FEED_ENABLED=false`)** | **569/569 PASS** (`19 suites`, `0 fail`, `rc=0`) |
| **Painel F7 + revisão F7 (`teste-painel-f7.cjs`, `teste-revisao-f7.cjs`)** | **2/2 PASS** (`rc=0`) |
| **Serviço standalone (`teste-servico.cjs`)** | **PASS** (`rc=0`) |
| **Aparência vampírica (`teste-aparencia-vampiro.cjs`)** | **10/10 PASS** (`rc=0`, incluindo rejeição de EDID homônimo de outro plugin) |
| **Sync de plugins + HUD da Lua (`sync-client-plugins.test.js`, `moon-hud.test.cjs`)** | **13/13 PASS** (`rc=0`) |
| **Catch2 Release nativo (`unit.exe` com `AETHERIUS_LORD_TEST_ESP` → ESPFE candidato)** | **81 asserções em 4 casos PASS** (`[VampireStrength],[VampireLordStats],[VampireLordConcentration],[BatDash]`, `rc=0`; mapeamento `subst X:` removido ao final) |
| **MoonMod CTest (`tools/native/sky-moons-build`, `ctest -C Release`)** | **1/1 PASS** (`sky-clock`, `rc=0`) |
| **TypeScript (`npx tsc --noEmit` em `client-source/client`)** | **PASS** (`rc=0`) |

### 4. Observação opcional (não bloqueante)

- A pasta candidata `AETHERIUS - SOBRENATURAL\` não traz `meta.ini` (o MO2 gera um arquivo mínimo ao detectar a pasta, e a distribuição oficial usa os manifestos de release — não bloqueia instalação nem homologação).

