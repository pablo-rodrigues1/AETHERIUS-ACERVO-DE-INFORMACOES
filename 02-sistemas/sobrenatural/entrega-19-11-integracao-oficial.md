# Entrega — Sistema Sobrenatural (Maldições) — 19/11/2026

Pacote para integrar o **Sistema Sobrenatural** (Vampirismo, Licantropia, Bruxaria) no **servidor e no cliente oficiais** do Aetherius.
Compilado por Opus 5.5 (orquestrador) a partir do laboratório `C:\Code\Aetherius - Servidor Local`.

Documentação viva: [Acervo → 02-sistemas/sobrenatural](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural).

---

## 0. Resumo rápido

| Item | Valor |
|---|---|
| Laboratório (servidor) | `C:\Code\Aetherius - Servidor Local`, HEAD `147e84f` |
| Base do patch do servidor | `310cb59` (cópia de `aetherius-server-main`, 15/11/2026) |
| Base do patch do cliente | `bc305e5` (cópia de `aetherius-client-main/client`); client-source HEAD `5e8b491` |
| Addon nativo de referência | `scam_native.node` SHA-256 `23669DE235A7A3B480B013326B1638A013C5DAC6D265FA86D51131D7C77F9F2B` |
| Bundle do cliente de referência | `skymp5-client.js` SHA-256 `FD61C198FAA52E1A821106DFBCCC23CCBFCCA95F3B9A63AFFD9F900AD2935709` |
| ESP do sistema | `AETHERIUS - SOBRENATURAL.esp` SHA-256 `C8B1320710873AD2F8203498990B24305A28D274D8D4D6C4BB2264FC15B4B06A` |
| Testes no laboratório | 517/517 Node em cada modo de alimentação; testes nativos da fera 13/13; TypeScript e webpack OK |
| Revisão | Toda entrega foi revisada pelo Gemini 4 Neon (revisão honesta); relatórios em `05-documentacao/REVISAO-*.md` |

> ⚠️ **Precision Creatures foi REMOVIDO do jogo** (dev, 19/11/2026). Ele fazia a fera atacar com uma só mão e quebrava os combos. Foi substituído por **New Creature Animation - Werewolf** (`Werewolf_New_Anim.esp`). Remova o Precision Creatures dos perfis oficiais (`AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE`) e **regere o Pandora**. O Precision Creatures não tem ESP (é DLL/arquivos), então o load order de plugins não muda com a remoção.

---

## 1. Conteúdo do pacote

```
Entrega_Sobrenatural_19_11/
├─ LEIA-ME_INTEGRACAO.md            ← este arquivo
├─ 01-servidor/
│  ├─ sobrenatural-servidor.patch   ← patch cumulativo (git, binário) sobre 310cb59
│  ├─ LISTA-ARQUIVOS.txt            ← 81 arquivos tocados
│  ├─ arquivos/                     ← versão final completa de cada arquivo (referência / cópia direta)
│  └─ conflitos-com-oficial/        ← 4 arquivos já mesclados em 3 vias com o oficial atual, COM marcadores de conflito
├─ 02-cliente/
│  ├─ sobrenatural-cliente.patch    ← patch cumulativo sobre bc305e5 (sem os perfis LOCAL)
│  ├─ LISTA-ARQUIVOS.txt            ← 31 arquivos (client/src + client/ui-core)
│  ├─ arquivos/
│  └─ conflitos-com-oficial/        ← 2 arquivos mesclados em 3 vias, com marcadores
├─ 03-mods-jogo/                    ← mods MO2 prontos para copiar em C:\modOrganizer\mods
│  ├─ AETHERIUS - SOBRENATURAL/     ← ESP do sistema + VISOES.esp + sons da Bruxa + fontes .psc
│  ├─ AETHERIUS - LOBISOMEM TRANSFORMACAO/  ← animação/OAR/sons da transformação (kdu)
│  ├─ Colored Magic Auras/          ← NOVO (Nexus 25773) auras Red/Purple
│  ├─ New Creature Animation - Werewolf/    ← NOVO (substitui Precision Creatures)
│  ├─ Miggyluv's Eyes/              ← olhos do vampiro/bruxa (416 MB)
│  ├─ Pandora - Output/             ← OUTPUT REGERADO 19/11 20:13 (perfil LOCAL)
│  └─ AETHERIUS - LOCAL - PANDORA/  ← OUTPUT REGERADO 19/11 20:13 (perfil LOCAL)
├─ 04-binarios-referencia/          ← scam_native.node e skymp5-client.js usados no teste
├─ 05-documentacao/                 ← todos os .md do sistema (especificação, decisões, etapas, revisões, investigações)
└─ 06-painel-adm/                   ← painel administrativo: servidor, frontend F7 (patch 004 + app.js) e contrato das ações
```

---

## 2. Arquivos do servidor (`01-servidor`)

### 2.1 O que muda
**Novos:**
- `server/gamemode/supernatural-service.js`: serviço central, com maldição, estágio, XP e estado de apresentação.
- `server/gamemode/supernatural/`: Reserva de Sangue, alimentação, sol, cura do vampiro, Vínculo do Véu e Ritual, Terror, grants por origem, journal da transformação, botão Forma da Besta, efeitos do cliente.
- `server/gamemode/werewolf-service.js` e `server/gamemode/werewolf/`: lobisomem do kdu adaptado.
- `server/gamemode/vampire-lord-service.js`.
- `server/gamemode/admin-panel/supernatural-admin.cjs`: setagem pelo painel F7.
- Testes do GameplayCore: `perk-origin-barrier`, `perk-ownership-retention`.

**Alterados:**
- Gamemode: `phase0-basic.js`, `commands.js`, `magic-service.js`, `survival-service.js`, `whitelist.js`, SkySocial (`identity`, `nameplate`, `native-plates`, `system`, `ui`, `client`, `index`).
- Painel administrativo: `admin-panel/*`.
- GameplayCore: `class-skills`, `client-delivery`, `index`, `projection/perks`.
- `server/ts/systems/aetheriusUiSystem.ts`.

**C++ nativo (exige recompilar o addon):**
- `ActionListener.cpp`
- `Equipment.cpp/.h`, com a limpeza de mãos da fera
- `MpActor.cpp/.h`
- `DevAttributes.cpp/.h`
- `CropRegeneration.cpp`
- bindings `Appearance`, `AttributeBonuses` e `PropertyBindingFactory`
- testes `unit/PartOne_MovementTest.cpp` e `unit/PartOne_UpdateEquipmentTest.cpp`

**Excluídos de propósito:**
- `server/ts/settings.ts`: patch exclusivo do ambiente local (`master: ""` offline). **Não aplicar no oficial.**
- `server/yarn.lock`: só ruído de build.

### 2.2 Conflitos com o oficial atual (`aetherius-server-main`, modificado em 17/11)
O patch aplica **limpo** na base `310cb59`. O oficial atual mudou desde então: o lobisomem v0.42 do kdu entrou nele, e o painel, o survival e o MpActor ganharam pequenos acréscimos. Na mesclagem em 3 vias, os arquivos ficaram assim:

| Arquivo | Situação |
|---|---|
| `server/gamemode/werewolf-service.js` | **conflito**: o oficial tem o v0.42 do kdu. O Sobrenatural fez um *porte seletivo* do v0.42 (ver `05-documentacao/COMPARACAO_lobisomem-oficial-v0.42.md` §12/§13). Prevalece a versão do pacote; conferir se algo do oficial posterior a 17/11 precisa ser mantido. |
| `server/gamemode/werewolf/client.js` | **conflito**: mesma causa. |
| `server/gamemode/werewolf/tests/werewolf.test.cjs` | **conflito**: mesma causa. |
| `server/gamemode/skysocial/src/identity.js` | **conflito** pequeno: o oficial acrescentou 4 linhas. |
| `admin-panel/aetherius-tools.cjs`, `phase0-basic.js`, `survival-service.js`, `MpActor.cpp` | o oficial divergiu, mas a mesclagem automática resolveu sem conflito. **Revisar o resultado.** |

Os arquivos mesclados, com marcadores `<<<<<<<`, estão em `01-servidor/conflitos-com-oficial/`.

### 2.3 Passo a passo
```powershell
cd <repo oficial do servidor>
git checkout -b sobrenatural
git apply --3way --ignore-whitespace "<pacote>\01-servidor\sobrenatural-servidor.patch"
# resolver os 4 conflitos (tabela acima); referência final em 01-servidor\arquivos\
git diff --stat
```
Se o `--3way` reclamar de blob ausente, aplique o patch em uma cópia de `310cb59` e faça merge; ou copie os arquivos de `01-servidor/arquivos/` e resolva os 4 conflitos à mão.

Depois de aplicar:
1. **Recompilar o addon nativo** (`scam_native.node`). Os patches nativos 026, 027 e 029 são obrigatórios para a guarda de movimento do Morcego e para a limpeza das mãos da fera. Use o `scam_native.node` de `04-binarios-referencia` só para comparar.
2. Build do backend e do GameplayCore (TypeScript), como no fluxo oficial.
3. Rodar os testes: `node --test` em `server/gamemode/werewolf/tests`, `server/gamemode/magic/tests`, `survival-service.test.js` e nos testes do GameplayCore (`server/gameplay/core/runtime/tests`). O teste nativo `PartOne_UpdateEquipmentTest` (caso legado `UpdateEquipment`) **já falhava na base oficial `310cb59`**, por causa do fixture `PluginIndexMapping`. Isso não é regressão.

### 2.4 Variáveis de ambiente (servidor)
| Variável | Valor no teste | Efeito |
|---|---|---|
| `AETHERIUS_VAMPIRE_FEED_ENABLED` | `true` (padrão `false`) | Liga "Beber Sangue" (menu X) com a regra D7 (na dúvida, não credita). |
| `AETHERIUS_VAMPIRE_SERVER_SUN` | padrão | Dano solar calculado pelo servidor. |
| `AETHERIUS_VAMPIRE_NEEDS_RESTORE`, `AETHERIUS_VAMPIRE_CRITICAL_EXIT`, `AETHERIUS_VEIL_OVERLOAD_START`, `AETHERIUS_SKYRIM_UNITS_PER_METER` | padrão | Ajustes finos (ver `DECISOES.md`). |
| `VAMPIRE_POWER_COOLDOWN_SECONDS` | `60` | Recarga dos poderes do vampiro (duração de 40 s). |
| `VAMPIRE_BAT_COOLDOWN_SECONDS` | padrão (3 s) | Recarga do avanço do Morcego. |
| `WEREWOLF_FORCE_ONLINE_HOURS` | `72` | Horas online até a transformação forçada (à noite, das 20h às 5h). |
| `WEREWOLF_COOLDOWN_SECONDS` | `0` (teste) | Recarga do Z da fera. **Defina o valor de produção.** |
| `WEREWOLF_ANIMATION`, `WEREWOLF_NAME`, `WEREWOLF_REQUIRE_KNOWN_SPELL` | padrão | Lobisomem do kdu. |

> Não copie `config/local.env` do laboratório: ele tem tokens e segredos locais.

---

## 3. Cliente e UI Core (`02-cliente`)

**Muda:**
- `client/src/sobrenatural/*` (novo): HUD, efeitos do Véu, Magicka do Véu.
- Serviços em `client/src/services/services/*`: `remoteServer`, `gameplayGrantService`, `loadGameService`, `spSnippetService`, `gamemodeEventSourceService`, `perk-origin.js`.
- `client/src/sync/appearance.ts`.
- `client/ui-core/modules/supernatural/*`: módulo do TAB, com 4 telas, botão Forma da Besta, tela do Ritual, selo, HUDs.
- `client/ui-core/modules.json`, `frontend/hud.html` e `frontend/hud.js`.
- `client/index.html`.

**Não incluído:** os perfis `client/profiles/AETHERIUS - LOCAL` (exclusivos do teste).

**Conflitos com o oficial atual:** `client/src/services/services/loadGameService.ts` e `client/src/sync/appearance.ts`. A causa é a proteção de headparts do v0.42, que já existe no oficial; ver COMPARACAO §12. As versões mescladas estão em `02-cliente/conflitos-com-oficial/`.

```powershell
cd <repo oficial do cliente>
git apply --3way --ignore-whitespace "<pacote>\02-cliente\sobrenatural-cliente.patch"
# resolver 2 conflitos; build (TypeScript + webpack) → skymp5-client.js
```
Distribuição: o `skymp5-client.js` gerado vai no mod do cliente oficial (Platform\Plugins). A UI Core atualizada vai no mod `AETHERIUS - UI CORE`, e o módulo `supernatural` usa a rota `/supernatural`, slot 8 do radial.

---

## 4. Mods do jogo (`03-mods-jogo`) e load order

### 4.1 Mods para instalar em `C:\modOrganizer\mods` e ativar nos perfis oficiais (QUALIDADE **e** PERFORMANCE)
| Mod | Situação | Plugin | Observação |
|---|---|---|---|
| `AETHERIUS - SOBRENATURAL` | **novo no oficial** | `AETHERIUS - SOBRENATURAL.esp`, `AETHERIUS - SOBRENATURAL - VISOES.esp` | Perks, abilities por estágio, +50% de velocidade na fera (MGEF `000844`), records do Vínculo do Véu. Inclui `Sound\FX\Aetherius\Witch\*.wav` (sussurros e grito da Bruxa) e as fontes `.psc` só como referência. No LOCAL, o VISOES.esp e os sons ficavam no mod "AETHERIUS - SERVIDOR LOCAL" e foram reunidos aqui. |
| `AETHERIUS - LOBISOMEM TRANSFORMACAO` | novo no oficial (pacote kdu) | — | Animação da transformação (FNIS/OAR `ww_howl`) e sons (coração, gritos). |
| `Colored Magic Auras` | **NOVO** (Nexus 25773) | `Colored Magic Auras.esp` (ESL-flag) + BSA | Aura Red `000D62` (Vampiro Feral) e Purple `000801` (Sobrecarga da Bruxa). 88 records novos (tomos, magias, anéis), **sem** listas, vendedores ou overrides. Ver `05-documentacao/DADOS-HOUSECARL_19-11_colored-magic-auras.md`. |
| `New Creature Animation - Werewolf` | **NOVO** (substitui Precision Creatures) | `Werewolf_New_Anim.esp` | Exige regerar o Pandora. |
| `Miggyluv's Eyes` | novo no oficial | `Miggyluv's Eyes.esp` | Olhos do vampiro e da Bruxa. Ver `05-documentacao/INSTALACAO_olhos-miggyluv.md`. |
| ~~Precision Creatures~~ | **REMOVIDO** | — | Desativar e remover dos perfis oficiais. |

### 4.2 Ordem dos plugins (como no perfil LOCAL testado)
```
... Curse of the Vampire.esp
    Manbeast.esp
    AETHERIUS - LOBISOMEM.esp
    AETHERIUS - SOBRENATURAL.esp          ← logo após o LOBISOMEM
... (restante da ordem oficial)
    AETHERIUS - SOBRENATURAL - VISOES.esp ← final da lista
    Miggyluv's Eyes.esp
    Werewolf_New_Anim.esp
    Colored Magic Auras.esp               ← último
```
Atualize também `client/profiles/AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE` (modlist, plugins e loadorder) no repo do cliente. O `Prepare`/`sync-client-plugins.js` do servidor gera o `aetherius-loadorder.json` a partir desses perfis, e **o servidor precisa ter exatamente os mesmos plugins, na mesma ordem e com os mesmos bytes do cliente.** Sem isso, a validação do manifesto falha no boot.

### 4.3 Outputs regerados (Pandora)
- `Pandora - Output` e `AETHERIUS - LOCAL - PANDORA` foram regerados em 19/11 às 20:13, depois da remoção do Precision Creatures e da entrada do New Creature Animation - Werewolf.
- **Atenção:** os dois foram gerados sobre o perfil **LOCAL**, que não tem `AETHERIUS - FISICA`, `AETHERIUS - GHOSTSHELF` nem `Ghostshelf Keep`, que existem no QUALIDADE.
- Use-os para comparar ou como solução provisória, mas o certo é **regerar o Pandora em cada perfil oficial**, depois de instalar os mods da §4.1. Pela regra do projeto, output gerado nunca é base de patch.

---

## 5. Painel administrativo (F7) — **ver `06-painel-adm/LEIA-ME_PAINEL-ADM.md`**
A pasta `06-painel-adm/` traz:
- os arquivos de servidor do painel;
- o **patch do frontend F7** (`client/admin-panel/app.js`), que **não está** no patch do cliente porque o painel é distribuído pelo mod `AETHERIUS - MERIDIAN - COMPATIBILIDADE`;
- o contrato das 8 ações `supernatural.*`, para o painel web oficial (eksmurf).

A maldição é concedida e removida **somente** pelo Painel Administrativo (`admin-panel/supernatural-admin.cjs`): setar maldição, vertente da Bruxa, estágio, transformação forçada e encerrar forma. Ao setar, o servidor grava **uma** keyword permanente (`JogadorVampiro`, `JogadorLobisomem` ou `JogadorBruxa`), só no servidor. O cliente recebe apenas o estado de apresentação (`curse` + progresso). Repositório do painel: https://github.com/eksmurf/Painel_Admin_Aetherius. Ver `05-documentacao/ETAPA-2_painel-f7.md`.

---

## 6. Estado funcional (honesto) no momento da entrega

**Validado em jogo (solo, 19/11):**
- HUD da Reserva de Sangue e Visão Vampírica.
- Selo e HUD/timer do Ritual da Bruxa, sussurros.
- Transformação forçada do lobisomem pelo F7.
- Raça vampírica.

**Corrigido, revisado e instalado no LOCAL, ainda sem reteste em jogo:**
- Botão **Forma da Besta** no TAB: 10 min de forma e recarga de 2 h persistida no servidor.
- Fera: mãos limpas ao transformar e +50% de velocidade.
- Auras Red e Purple.
- Runa do Ritual.
- Fim do loop `fx.cleanup`.

**Problemas conhecidos (NÃO resolvidos):**
- **T1/T2:** o Z (Beast Form e Lorde Vampiro) não transforma. O comando de voz não chega ao servidor. Só há diagnóstico, no commit `bcb0c02`. Para o lobisomem, o botão Forma da Besta contorna o problema. Ver `05-documentacao/investigacoes/INVESTIGACAO_19-11_voz-z-e-dash.md`.
- **T10:** o avanço do Morcego não funciona (mesma investigação).
- Uivo por estágio (Terror no Ômega, Fúria no Alfa, nenhum no Iniciado): decidido, mas depende do Z.
- Pendentes: XP de maldição, P10 (Lorde só pelo painel; hoje liberado no Neófito para teste), teste com 2 ou mais jogadores (observadores, Beber Sangue).

**Roteiro de validação no oficial:**
- `05-documentacao/ETAPA-3_vampirismo-mecanicas.md`
- `05-documentacao/ETAPA-4_bruxaria-vinculo-do-veu.md`
- `05-documentacao/PENDENCIAS.md` §4
- `05-documentacao/TESTE-JOGO_19-11.md`

---

## 7. Checklist de integração
- [ ] Branch nova no servidor; aplicar `sobrenatural-servidor.patch` e resolver os 4 conflitos.
- [ ] Não aplicar `settings.ts`/`yarn.lock` (já excluídos).
- [ ] Recompilar `scam_native.node`; build TS e GameplayCore; testes.
- [ ] Branch nova no cliente; aplicar `sobrenatural-cliente.patch` e resolver os 2 conflitos; build do `skymp5-client.js` e da UI Core.
- [ ] Painel ADM: aplicar o patch 004 no `client/admin-panel/app.js`, distribuir pelo mod MERIDIAN - COMPATIBILIDADE e seguir o checklist de `06-painel-adm/LEIA-ME_PAINEL-ADM.md`.
- [ ] Instalar os mods da §4.1 e remover o **Precision Creatures**.
- [ ] Ajustar o load order (§4.2) nos dois perfis oficiais e nos `client/profiles`.
- [ ] Regerar o **Pandora** em cada perfil oficial.
- [ ] `Prepare` do servidor e conferir o manifesto de plugins (contagem e hashes).
- [ ] Definir as variáveis de ambiente de produção (§2.4).
- [ ] Teste com 2 jogadores pelos roteiros da §6.
- [ ] Registrar a integração no Acervo (`02-sistemas/sobrenatural/diario.md`).
