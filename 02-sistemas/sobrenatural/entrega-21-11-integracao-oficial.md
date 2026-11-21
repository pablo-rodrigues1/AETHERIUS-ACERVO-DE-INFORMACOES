# Entrega — Sistema Sobrenatural (Maldições) — 21/11/2026 · versão definitiva

Pacote para integrar o **Sistema Sobrenatural** (Vampirismo, Licantropia, Bruxaria) no **servidor e no cliente oficiais** do Aetherius. Substitui a `Entrega_Sobrenatural_20_11` e **contém tudo o que ela trazia**, mais as entregas de 21/11.

Compilado por Opus 5.5 (orquestrador) a partir do laboratório `C:\Code\Aetherius - Servidor Local`. Documentação viva: [Acervo → 02-sistemas/sobrenatural](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural) e ADR-0027 (`03-decisoes/ADR-0027-ceu-sincronizado-pelo-gamedayspassed.md`).

---

## 0. Resumo rápido

| Item | Valor |
|---|---|
| Base dos patches | **oficial de 19/11/2026** (`C:\Code\Aetherius - OFICIAL\aetherius-server-main` e `aetherius-client-main`, arquivos de 19/11 15:19; sem mudanças desde então). Aplicam **sem conflito**. |
| Origem | Laboratório: overlays `a7b3a3b`, `source` `5bc981d`, `client-source` `c6134b2`. Método de mesclagem em **`REBASE_OFICIAL_21-11.md`** (e o histórico de 19/11 em `REBASE_OFICIAL_19-11.md`). |
| Patch do servidor | `01-servidor/sobrenatural-servidor.patch`, **92 arquivos**, SHA-256 `3161af26d162f6f1d21b916ad0fdc784048c8e8f488c5a2f53f2695d559f0f1a` |
| Patch do cliente | `02-cliente/sobrenatural-cliente.patch`, **39 arquivos**, SHA-256 `ad6d40e66c52c901388f8512fcb76f3080df2457ed6fe252f7107580700d8f15` |
| Plugin do sistema | **um único** `AETHERIUS - SOBRENATURAL.esp`, **ESPFE** (flag ESL), 167 records (149 novos + 18 overrides), SHA-256 `95d3ef67cfe680544f77f9ae25c46bd67168deb4289f82cdc12263eb36605061`, CRC32 com sinal `2067681845`. Masters: `Skyrim.esm`, `Dawnguard.esm`, `MasterSpellsRunes.esp`, `Curse of the Vampire.esp`. |
| BSA do sistema | `AETHERIUS - SOBRENATURAL.bsa` (6 NIF + 40 DDS do visual vampírico), SHA-256 `3c17595c49db14eaf8d2dd45651d60b21f483d66dacb6ff249afcf6dbb85cf15` |
| DLL do céu | `SKSE\Plugins\po3_MoonMod.dll` (fork do Moons and Stars, MIT), **só Skyrim Steam AE 1.6.1170**, SHA-256 `7bad2a4cfc59259420ce39dc9a2fee07f42052c70276f714b8724eb4f6ac2e8b` |
| Bundle do cliente de referência | `04-binarios-referencia/skymp5-client.js`, compilado **sobre o oficial de 19/11 já mesclado**, SHA-256 `9d8e459936ab850c9e954284f2cef7bddb0850cf80637fb65b36b879564bffaa` |
| Addon nativo de referência | `04-binarios-referencia/scam_native.node`, SHA-256 `2a4e677911be738a8ede8563e05ab79ae0df79b2b4341123167e6b4b2bc3df37` (sem mudança nativa desde 20/11; recompile no fluxo oficial) |
| Testes na árvore mesclada (oficial 19/11 + patches) | Sobrenatural **391/391 em cada modo de alimentação** + F7 2/2 + serviço OK; gamemode **164/164** (werewolf, magic, records-ctda, summon-friends, survival, Masser); cliente 8/8 (HUD da Lua, TimeService); `tsc` servidor e cliente OK; webpack OK |
| Revisão | Toda entrega revisada pelo Gemini 4 Neon (revisão honesta). Relatórios em `05-documentacao/REVISAO-*.md` e auditoria de clima/horário em `AUDITORIA-CLIMA-HORARIO_21-11_terra.md` |

> ⚠️ **Mudança estrutural de 21/11:** os plugins e pastas **nossos** do Sobrenatural foram unificados em **uma pasta de mod e um ESP leve**: `AETHERIUS - SOBRENATURAL`. Deixam de existir como entregáveis: `AETHERIUS - SOBRENATURAL - VISOES.esp`, `AETHERIUS - DVA Visuals` e a pasta `AETHERIUS - Vampire Lord and Werewolf SFX` (o conteúdo está dentro da pasta única). Como o Sobrenatural **ainda não está no oficial**, isso não desloca nenhum slot dos 437 plugins travados (§4.2).

> ⚠️ **Releases do `aetherius-mods`:** os jogadores só recebem mods publicados como releases. Passo a passo em §4.4.

## 0.1 Novidades de 21/11/2026

Todos os itens abaixo foram revisados pelo Neon; os relatórios citados estão em `05-documentacao/`.

- **Lua de Masser** (`ETAPA-2_lua-de-masser.md`, `REVISAO-LUA-MASSER_neon.md`):
  - fase calculada pelo servidor a partir do relógio autoritativo (`masser.js`), ciclo de 24 dias, 8 fases de 3 dias, 0 = Lua Cheia (mesma fórmula do `po3_MoonMod.ini` vigente do Rudy ENB);
  - **transformação obrigatória dos lobisomens nas 3 noites de Lua Cheia** (20h–5h), uma por noite, pela mesma sequência da compulsão de 72 h; qualquer transformação confirmada na noite cumpre a noite;
  - **olhos Bronze** (Miggyluv's Eyes): Light como padrão; **Glow** após 24 h online sem transformar ou durante os 3 dias de Lua Cheia; Argoniana feminina mantém os olhos originais (o mod não tem variante);
  - **HUD da Lua** (proposta A aprovada pelo dev): ~56 px, acima da HUD de sobrevivência, só para lobisomens; propriedade privada `aetheriusMoon`.
- **Céu sincronizado** (ADR-0027, `ETAPA-2_ceu-sincronizado.md`, `REVISAO-CEU-SINCRONIZADO_neon.md`):
  - o servidor envia `skyDaysPassed` no `gameTime` v1 existente; o fork do Moons and Stars desenha Masser e Secunda iguais para todos;
  - **o GameDaysPassed do jogo (`0x39`) nunca é escrito** fora do laboratório: escrever nele vencia prazos do Curse of the Vampire, da Fera e de quests (veto registrado no ADR);
  - auditoria do Terra: sem regressão no clima, horário e ações de ambiente do painel.
- **Aparência do Vampiro por estágio da Reserva** (Dynamic Vampire Appearance, `ETAPA-3_aparencia-vampiro.md`, `REVISAO-APARENCIA-VAMPIRO_neon.md`):
  - rosto (TXST) e olhos por estágio: **1** = 100–70, **2** = 69–50, **3** = 49–31, **4** = 30–0;
  - sangue na boca por **3 min reais** após alimentação validada pelo servidor (renova a cada alimentação);
  - olhos de combate (Feral, Immeasurable Strength, Unnatural Celerity) > visão noturna (Vampiric Vision) > estágio;
  - Argoniano e Khajiit mantêm a aparência racial; troca de olho/rosto aplicada no ator existente, sem recriar o ator remoto;
  - só os assets do DVA entram (sem as quests, scripts e overrides de `DLC1VampireChangeEffect`/`DLC1RevertEffect` do mod original).
- **Unificação** (`ETAPA-4_unificacao-esp.md`, `REVISAO-UNIFICAR-ESP_neon.md`): ESP + BSA + sons + DLL numa única pasta; mapa completo de FormIDs em `07-load-order-e-manifestos/form-map.json`.
- O **Cálice da Noite** e o restante da entrega de 20/11 continuam incluídos (§0.2).

## 0.2 Herdado da entrega de 20/11 (continua valendo)

HUD Cálice da Noite; Lorde Vampiro pelo painel (`supernatural.lordGrant`/`lordRevoke`, keyword `JogadorLordeVampiro` só no servidor, botão no TAB, 10 min / 2 h); forma de Lorde 1,25×, menu Q automático, 15 perks, bônus por estágio só na forma; sons do Lorde e dos poderes; Devouring Swarm 20 HP/s; Immeasurable Strength no motor de dano; dash do Morcego; correção `userEventName` do Z; Precision Creatures removido e substituído por New Creature Animation - Werewolf. Detalhes nos documentos de 20/11 em `05-documentacao/`.

---

## 1. Conteúdo do pacote

```
Entrega_Sobrenatural_21_11/
├─ LEIA-ME_INTEGRACAO.md             ← este arquivo
├─ REBASE_OFICIAL_21-11.md           ← como o delta de 21/11 foi mesclado sobre a entrega de 20/11 e o oficial
├─ REBASE_OFICIAL_19-11.md           ← histórico: mesclagem com o oficial de 19/11
├─ 01-servidor/
│  ├─ sobrenatural-servidor.patch    ← patch cumulativo sobre o OFICIAL de 19/11 (92 arquivos)
│  ├─ LISTA-ARQUIVOS.txt
│  └─ arquivos/                      ← versão final de cada arquivo (cópia direta)
├─ 02-cliente/
│  ├─ sobrenatural-cliente.patch     ← patch cumulativo sobre o OFICIAL de 19/11 (39 arquivos)
│  ├─ LISTA-ARQUIVOS.txt
│  └─ arquivos/
├─ 03-mods-jogo/
│  ├─ AETHERIUS - SOBRENATURAL/      ← PASTA ÚNICA: ESPFE + BSA + 65 WAV + DLL do céu + créditos
│  ├─ AETHERIUS - LOBISOMEM TRANSFORMACAO/   ← pacote kdu (separado, sem mudança)
│  ├─ Colored Magic Auras/           ← terceiro (Nexus 25773)
│  ├─ New Creature Animation - Werewolf/     ← terceiro (substitui Precision Creatures)
│  ├─ Miggyluv's Eyes/               ← terceiro (olhos Bruxa e Bronze do lobisomem)
│  ├─ Fangs and Eyes - A Vampire Appearance Mod/  ← terceiro (versão nova de 19/11)
│  ├─ Pandora - Output/              ← referência (gerado sobre o perfil LOCAL; regere no oficial)
│  └─ AETHERIUS - LOCAL - PANDORA/   ← referência
├─ 04-binarios-referencia/           ← scam_native.node e skymp5-client.js
├─ 05-documentacao/                  ← todos os .md do sistema + prévias HTML das HUDs
├─ 06-painel-adm/                    ← painel F7: servidor, frontend (patch 004 + app.js), contrato (sem mudança desde 20/11)
└─ 07-load-order-e-manifestos/       ← NOVO: proposta de load order/lock/perfis oficiais, delta do manifesto de segurança, form-map
```

---

## 2. Servidor (`01-servidor`)

### 2.1 O que muda
Tudo o que a entrega de 20/11 trazia (serviço `supernatural-service.js`, pasta `supernatural/`, `werewolf-service.js` + `werewolf/`, `vampire-lord-service.js`, `admin-panel/supernatural-admin.cjs`, alterações em `phase0-basic.js`, `commands.js`, `magic-service.js`, `survival-service.js`, `whitelist.js`, SkySocial, painel, GameplayCore, `aetheriusUiSystem.ts` e o C++ nativo), mais em 21/11:

- **Novos:** `supernatural/masser.js` (fase de Masser), `supernatural/vampire-appearance.js` (estágios DVA, boca, combate/visão), `supernatural/tests/masser.test.cjs`.
- **Alterados:** `forced-werewolf.js` (Lua Cheia, olhos Bronze por 24 h/cheia, propriedade `aetheriusMoon`), `veil-eyes.js` e `veil-visuals.js` (precedência única de olhos Bruxa/Lobisomem/Vampiro, resolução por FormKey do ESP unificado), `supernatural-service.js`, `powers.js`, `power-input.js`, `feeding.js` (gatilho da boca só pelo crédito autoritativo), `werewolf-transform-journal.js`, `werewolf-service.js`.
- **Clima/horário:** `server/ts/systems/environmentAuthority.ts` (`clockEpochMinutes()` e campo `skyDaysPassed` no snapshot) e `environmentSystem.ts` (expõe a época). `gameDaysPassed` continua `null` fora do laboratório.
- **C++:** sem mudança desde 20/11 (as alterações nativas de 20/11 continuam no patch e exigem recompilar o addon).

**Excluídos de propósito:** `server/ts/settings.ts` (só laboratório, `master: ""`) e `server/yarn.lock` (ruído de build).

### 2.2 Compatibilidade
- Aplica sem conflito sobre o oficial de 19/11 (`git apply --check --ignore-whitespace` em cópia limpa; o resultado é byte a byte igual a `arquivos/`).
- **Entrega de performance de UI de 20/11** (`C:\Code\Aetherius - OFICIAL\ENTREGA_PERFORMANCE_UI_2026-11-20`, outro dev): toca 2 arquivos em comum (`aetheriusUiSystem.ts`, `aetheriusInventoryService.ts`). O `patch.diff` dela **aplica sem conflito** sobre a árvore já com o Sobrenatural. Ordem sugerida: Sobrenatural, depois performance de UI; rode os testes das duas.
- Pontos de atenção herdados de 19/11: autoria conservadora das magias raciais em `appearance.ts`; poções que curam por segundo no `MpActor.cpp` (validar que o vampiro continua sem cura por poção).

### 2.3 Passo a passo
```powershell
cd <repo oficial do servidor>
git checkout -b sobrenatural
git apply --ignore-whitespace "<pacote>\01-servidor\sobrenatural-servidor.patch"
git diff --stat
```
O patch usa LF; em repositório CRLF use `--ignore-whitespace`. Alternativa: copiar `01-servidor/arquivos/`.
1. **Recompilar o addon nativo** (`scam_native.node`).
2. Build do backend e do GameplayCore (TypeScript) no fluxo oficial.
3. Testes: `node --test` em `server/gamemode/werewolf/tests`, `server/gamemode/magic/tests`, `server/gamemode/supernatural/tests`, `survival-service.test.js` e GameplayCore (`server/gameplay/core/runtime/tests`).

### 2.4 Variáveis de ambiente (servidor)
| Variável | Valor | Efeito |
|---|---|---|
| `AETHERIUS_VAMPIRE_FEED_ENABLED` | `true` no teste (padrão `false`) | "Beber Sangue" (menu X), regra D7. |
| `AETHERIUS_VAMPIRE_SERVER_SUN` | padrão | Dano solar no servidor. |
| `AETHERIUS_VAMPIRE_NEEDS_RESTORE`, `AETHERIUS_VAMPIRE_CRITICAL_EXIT`, `AETHERIUS_VEIL_OVERLOAD_START`, `AETHERIUS_SKYRIM_UNITS_PER_METER` | padrão | Ajustes finos (`DECISOES.md`). |
| `VAMPIRE_POWER_COOLDOWN_SECONDS` | `60` | Recarga dos poderes do vampiro. |
| `VAMPIRE_BAT_COOLDOWN_SECONDS` | padrão (3 s) | Recarga do avanço do Morcego. |
| `AETHERIUS_VAMPIRE_BLOODY_MOUTH_SECONDS` | padrão `180` | **Novo.** Duração do sangue na boca. |
| `WEREWOLF_FORCE_ONLINE_HOURS` | `72` | Horas online até a compulsão (noite 20h–5h). |
| `WEREWOLF_FORCE_NIGHT_START` / `_END` | `20` / `5` | Janela da noite (compulsão e Lua Cheia). |
| `WEREWOLF_EYES_GLOW_HOURS` | padrão `24` | **Novo.** Horas online sem transformar até o olho Bronze Glow. |
| `WEREWOLF_COOLDOWN_SECONDS` | `0` no teste | Recarga do Z da fera. **Defina o valor de produção.** |
| `WEREWOLF_ANIMATION`, `WEREWOLF_NAME`, `WEREWOLF_REQUIRE_KNOWN_SPELL` | padrão | Lobisomem do kdu. |
| `AETHERIUS_WEATHER_LAB_CALENDAR` | **não definir em produção** | Já existe no oficial. Com `1`, libera ajuste de data e o envio do GameDaysPassed (só laboratório, saves descartáveis). |

> Não copie `config/local.env` do laboratório (tokens locais).

---

## 3. Cliente e UI Core (`02-cliente`)

Tudo o que a entrega de 20/11 trazia (`client/src/sobrenatural/*`, serviços, `appearance.ts`, `formView.ts`, módulo `client/ui-core/modules/supernatural/*`, `modules.json`, `hud.html`/`hud.js`, `index.html`), mais em 21/11:
- `client/src/services/services/timeService.ts`: projeta `skyDaysPassed` e envia ao fork do céu pelo evento local `AetheriusSkyClockSnapshot` (invalidação, reenvio pós-load/RaceSex, validade de 90 s); `0x39` segue reservado ao laboratório.
- `client/src/sync/appearance.ts` e `client/src/view/formView.ts`: olhos e rosto DVA aplicados no ator existente (FormKeys do ESP unificado); mantida a mudança do oficial de 19/11 em `formView.ts` (facções hostis).
- `client/ui-core/modules/supernatural/moon-hud.js` (novo), `supernatural-hud.js`, `supernatural-hud.css`, `frontend/hud.html`: HUD da Lua; a chamada `window.setMoonHud?.()` é protegida.
- `client/src/sobrenatural/supernaturalHud.ts`.
- Testes novos: `client/ui-core/tests/moon-hud.test.cjs`, `client/meridian/time-service.test.cjs`.

**Não incluído:** perfis `client/profiles/AETHERIUS - LOCAL` (só teste). Os perfis oficiais propostos estão em `07-load-order-e-manifestos/primeira-publicacao/profiles/`.

```powershell
cd <repo oficial do cliente>
git apply --ignore-whitespace "<pacote>\02-cliente\sobrenatural-cliente.patch"
# build (TypeScript + webpack) → skymp5-client.js
```
Distribuição: `skymp5-client.js` no mod do cliente oficial (`Platform\Plugins`); UI Core no mod `AETHERIUS - UI CORE`, inteiro (a raiz `MeridianUI` não deve ser dividida entre mods; o módulo `supernatural` precisa dos 16 ativos, inclusive `witch-gauge-reference.png`).

---

## 4. Mods do jogo (`03-mods-jogo`) e load order

### 4.1 Mods para instalar e ativar nos perfis oficiais (QUALIDADE **e** PERFORMANCE)
| Mod | Situação | Plugin | Observação |
|---|---|---|---|
| `AETHERIUS - SOBRENATURAL` | **novo no oficial, pasta única** | `AETHERIUS - SOBRENATURAL.esp` (ESPFE) + `.bsa` | Spells, perks e estágios das 3 maldições, disciplinas, poderes do COTV adaptados, Visão Vampírica, visual DVA (36 TXST, 12 HDPT, 2 FLST), runa do Ritual, SOUN/SNDR. Soltos: 61 WAV de SFX (Lorde/vampiro/fera, em `sound\fx\...`, precisam vencer o Hypertrichosis) e 4 WAV da Bruxa. `SKSE\Plugins\po3_MoonMod.dll` (céu). `Docs\AetheriusCeu\` (licença MIT/créditos). **Prioridade no modlist: acima de Hypertrichosis e de Moons and Stars.** |
| `AETHERIUS - LOBISOMEM TRANSFORMACAO` | novo no oficial (kdu) | — | Animação/sons da transformação. |
| `Colored Magic Auras` | novo (Nexus 25773) | `Colored Magic Auras.esp` (ESL) + BSA | Auras do Feral e da Sobrecarga. |
| `New Creature Animation - Werewolf` | novo (substitui Precision Creatures) | `Werewolf_New_Anim.esp` | Exige regerar o Pandora. |
| `Miggyluv's Eyes` | novo no oficial | `Miggyluv's Eyes.esp` | Olhos violeta da Bruxa e Bronze do lobisomem. |
| `Fangs and Eyes - A Vampire Appearance Mod` | atualizado (19/11) | `BVFE.esp` (inalterado) + BSAs | Substituição direta da versão antiga. |
| `Moons and Stars - Sky Overhaul SKSE` | **já existe** no oficial | `MoonsAndStars.esp` | Continua ativo (ESP e texturas); a DLL dele perde para a da pasta única. O `po3_MoonMod.ini` que vale é o do **Rudy ENB SE for Obsidian Weathers** (SHA-256 `07927BD2…C89B`). |
| `AETHERIUS - LOBISOMEM` (kdu) | sem mudança | `AETHERIUS - LOBISOMEM.esp` | Continua separado. |
| ~~`AETHERIUS - Vampire Lord and Werewolf SFX`~~, ~~`AETHERIUS - SOBRENATURAL - VISOES.esp`~~, ~~`AETHERIUS - DVA Visuals`~~ | **absorvidos** | — | Não instalar; conteúdo dentro de `AETHERIUS - SOBRENATURAL`. |
| ~~`Dynamic Vampire Appearance`~~ (original) | **não usar** | — | Iniciaria 2 quests e sobrescreveria 2 MGEF do Lorde. |
| ~~Precision Creatures~~ | removido | — | Desativar e remover. |

### 4.2 Ordem dos plugins
Proposta completa e verificada em `07-load-order-e-manifestos/primeira-publicacao/` (`aetherius-loadorder.json`, `plugin-slots.lock.json`, `profiles/AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE`): preserva **os 437 slots travados**, acrescenta no fim, iguais nos dois perfis:
```
... (ordem oficial inalterada)
AETHERIUS - SOBRENATURAL.esp   ← FE137 (ESPFE)
Miggyluv's Eyes.esp            ← FE138
Colored Magic Auras.esp        ← FE139
Werewolf_New_Anim.esp          ← 7E (regular)
```
Os masters do ESP (`Skyrim.esm`, `Dawnguard.esm`, `MasterSpellsRunes.esp`, `Curse of the Vampire.esp`) já estão antes. Atualize também `client/profiles/*` no repo do cliente. Confirme no `Prepare`/`sync-client-plugins.js` que o `aetherius-loadorder.json` gerado bate com a proposta (no laboratório, a proposta LOCAL bateu com 0 diferenças).

### 4.3 Pandora
Regere o Pandora em cada perfil oficial depois de instalar §4.1. Os outputs do pacote foram gerados sobre o perfil LOCAL e servem só de referência (output gerado nunca é base de patch).

### 4.4 Releases do `aetherius-mods`
Funcionamento do launcher (inalterado): uma release por mod, título = nome exato da pasta, asset `<Nome>.zip` com a pasta como raiz (espaços viram ponto), partes `.zip.001…` acima de 2 GB, troca por `digest`; ativação vem dos perfis `client/profiles/*` da release do `aetherius-client`.

**A) Publicar — novos**
| Release | Asset | Conteúdo |
|---|---|---|
| `AETHERIUS - SOBRENATURAL` | `AETHERIUS.-.SOBRENATURAL.zip` | a pasta única inteira de `03-mods-jogo` (ESP, BSA, `sound\`, `SKSE\`, `Docs\`) |
| `AETHERIUS - LOBISOMEM TRANSFORMACAO` | `AETHERIUS.-.LOBISOMEM.TRANSFORMACAO.zip` | pacote kdu |
| `Colored Magic Auras` | `Colored.Magic.Auras.zip` | ESP + BSA |
| `New Creature Animation - Werewolf` | `New.Creature.Animation.-.Werewolf.zip` | ESP + animações |
| `Miggyluv's Eyes` | nome gerado pelo GitHub (confira o título **exato** `Miggyluv's Eyes`) | 416 MB |

**B) Nova versão:** `Fangs and Eyes - A Vampire Appearance Mod` (apague a antiga), `Pandora - Output` (regerado no oficial), `AETHERIUS - UI CORE` (módulo `supernatural` com HUD da Lua, Cálice, selo, ritual, botões), `AETHERIUS - MERIDIAN - COMPATIBILIDADE` (aba Sobrenatural do F7, `06-painel-adm`).

**C) Remover/não publicar:** `Precision Creatures`; versão antiga do Fangs and Eyes; **não** publicar `AETHERIUS - Vampire Lord and Werewolf SFX` nem `AETHERIUS - DVA Visuals` (se algum chegou a ser publicado em teste, apague). O launcher não remove pastas antigas do disco do jogador; o que as desativa é o perfil.

**D) Depois de publicar**
- `Prepare`/`sync-client-plugins.js` com o perfil oficial atualizado.
- **Manifesto de segurança:** rode `server/tools/build-security-manifest.js --previous`. Agora há uma **DLL** nova (`po3_MoonMod.dll` da pasta única): o hash e o provedor a autorizar estão em `07-load-order-e-manifestos/security-manifest.delta.json`.
- Teste de instalação limpa com o launcher num MO2 vazio.

---

## 5. Painel administrativo (F7)
Sem mudança desde 20/11. Ver `06-painel-adm/LEIA-ME_PAINEL-ADM.md`: arquivos de servidor, patch do frontend F7 (`client/admin-panel/app.js`, distribuído pelo `AETHERIUS - MERIDIAN - COMPATIBILIDADE`) e o contrato das 10 ações `supernatural.*`. A maldição é concedida/removida **somente** pelo painel; as keywords `JogadorVampiro`, `JogadorLobisomem`, `JogadorBruxa` e `JogadorLordeVampiro` ficam só no servidor.

---

## 6. Estado funcional (honesto)

**Validado em jogo (solo, 19/11):** HUD da Reserva e Visão Vampírica; selo e Ritual da Bruxa; transformação forçada pelo F7; raça vampírica.

**Revisado, testado por automação e instalado no laboratório (perfil LOCAL, 21/11), ainda sem teste em jogo:**
- Lua de Masser, HUD da Lua, olhos Bronze e transformação na Lua Cheia;
- céu sincronizado (fork do Moons and Stars) — carga da DLL e céu com dois jogadores pendentes;
- aparência do vampiro por estágio, boca, olhos de combate e visão;
- ESP/pasta únicos (instalados também nos perfis locais GRAFICO e PERFORMANCE);
- tudo o que a entrega de 20/11 listava como pendente de reteste (Forma da Besta, mãos da fera, auras, runa, Lorde, áudio para observadores, Grip).

**Problemas conhecidos (herdados, NÃO resolvidos):** T1/T2 (o Z não transforma: comando de voz não chega ao servidor; o botão Forma da Besta contorna para o lobisomem); T10 (avanço do Morcego); uivo por estágio depende do Z; XP de maldição pendente.

**Limites conhecidos de 21/11:**
- A DLL do céu só carrega em **AE 1.6.1170** (recusa limpa com log em outra versão; o céu volta ao comportamento local).
- Argoniana feminina sem olho Bronze; Argoniano/Khajiit sem visual DVA (decisões do dev).
- O evento local do céu pode ser forjado por script no próprio cliente; o efeito é só visual naquele cliente (o servidor não usa o evento).

**Roteiros de validação:** `05-documentacao/ETAPA-2_lua-de-masser.md`, `ETAPA-2_ceu-sincronizado.md` (dois jogadores, F7 de hora/pausa/clima regional), `ETAPA-3_aparencia-vampiro.md`, `ETAPA-4_unificacao-esp.md` (aceite), `ETAPA-3_vampirismo-mecanicas.md`, `ETAPA-4_bruxaria-vinculo-do-veu.md`, `PENDENCIAS.md`, `TESTE-JOGO_19-11.md`. Para chegar à Lua Cheia sem esperar, use `AETHERIUS_WEATHER_LAB_CALENDAR=1` **só em laboratório**, com saves descartáveis.

---

## 7. Checklist de integração
- [ ] Branch nova no servidor; aplicar `sobrenatural-servidor.patch` (sem conflitos sobre o oficial de 19/11). Não aplicar `settings.ts`/`yarn.lock`.
- [ ] Se for integrar também a entrega de performance de UI de 20/11: aplicar depois e rodar os testes das duas.
- [ ] Recompilar `scam_native.node`; build TS e GameplayCore; testes (§2.3).
- [ ] Branch nova no cliente; aplicar `sobrenatural-cliente.patch`; build do `skymp5-client.js` e da UI Core (raiz `MeridianUI` inteira).
- [ ] Painel ADM: patch 004 em `client/admin-panel/app.js` (`06-painel-adm`).
- [ ] Instalar os mods da §4.1 (pasta única `AETHERIUS - SOBRENATURAL` acima de Hypertrichosis e Moons and Stars); remover Precision Creatures; não instalar SFX/DVA Visuals/VISOES/DVA original.
- [ ] Aplicar a ordem de `07-load-order-e-manifestos/primeira-publicacao` nos dois perfis oficiais e em `client/profiles`; conferir os 437 slots.
- [ ] Regerar o Pandora em cada perfil oficial.
- [ ] Releases do `aetherius-mods` (§4.4) e manifesto de segurança com a DLL nova.
- [ ] `Prepare` do servidor; conferir o manifesto de plugins (contagem, CRC do ESP `2067681845`).
- [ ] Conferir em cada perfil: um único `po3_MoonMod.dll` vencedor (pasta única), INI do Rudy, Address Library 1.6.1170; log `Documents\My Games\Skyrim Special Edition\SKSE\po3_MoonMod.log`.
- [ ] Variáveis de ambiente de produção (§2.4); **não** ligar `AETHERIUS_WEATHER_LAB_CALENDAR`.
- [ ] Teste com 2 jogadores pelos roteiros da §6.
- [ ] Registrar a integração no Acervo (`02-sistemas/sobrenatural/diario.md`).
