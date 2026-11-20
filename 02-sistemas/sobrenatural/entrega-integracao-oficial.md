# Entrega — Sistema Sobrenatural (Maldições) — 20/11/2026

Pacote para integrar o **Sistema Sobrenatural** (Vampirismo, Licantropia, Bruxaria) no **servidor e no cliente oficiais** do Aetherius.
Compilado por Opus 5.5 (orquestrador) a partir do laboratório `C:\Code\Aetherius - Servidor Local`.

Documentação viva: [Acervo → 02-sistemas/sobrenatural](https://github.com/pablo-rodrigues1/AETHERIUS-ACERVO-DE-INFORMACOES/tree/main/02-sistemas/sobrenatural).

---

## 0. Resumo rápido

| Item | Valor |
|---|---|
| Laboratório (servidor) | `C:\Code\Aetherius - Servidor Local`, HEAD `147e84f` |
| Base dos patches | **oficial de 19/11/2026** (`C:\Code\Aetherius - OFICIAL\aetherius-server-main` e `aetherius-client-main`, arquivos de 19/11 15:19). Aplicam **sem conflito**. Detalhes da mesclagem em **`REBASE_OFICIAL_19-11.md`** |
| Origem do Sobrenatural | laboratório `147e84f` (servidor), client-source `5e8b491` |
| Addon nativo de referência (laboratório) | `scam_native.node` SHA-256 `2A4E677911BE738A8EDE8563E05AB79AE0DF79B2B4341123167E6B4B2BC3DF37`. Compilado no laboratório (base 310cb59 + Sobrenatural), **sem** as mudanças nativas do oficial de 19/11: recompile no fluxo oficial |
| Bundle do cliente de referência | `skymp5-client.js` SHA-256 `A65B9E3CD1E62F4F5A24A396D8619AC7BC6F2816365BABDFDDF05063790FDFDE`, compilado **sobre o oficial de 19/11 já mesclado** |
| ESP do sistema | `AETHERIUS - SOBRENATURAL.esp` SHA-256 `7A1397FE2B1DE84016E9CE688C3F06DFC19E9F9143C144806FDE1972DF2CE110` |
| Testes | No laboratório: 530 Node em cada modo de alimentação e 81 asserções Catch2. Na árvore mesclada com o oficial: 376/376 do Sobrenatural em cada modo, 142/142 do gamemode, `tsc` do servidor e do cliente OK, webpack OK |
| Revisão | Toda entrega foi revisada pelo Gemini 4 Neon (revisão honesta); relatórios em `05-documentacao/REVISAO-*.md` |

> ⚠️ **Releases do `aetherius-mods`:** os jogadores só recebem os mods novos se eles forem publicados como releases no `aetherius-mods`. Publique os 6 mods novos e as versões novas. **Apague** as releases dos mods que deixaram de ser usados (Precision Creatures e a versão antiga do Fangs and Eyes). O passo a passo completo está na **§4.4**.

> ⚠️ **Precision Creatures foi REMOVIDO do jogo** (dev, 19/11/2026). Ele fazia a fera atacar com uma só mão e quebrava os combos. Foi substituído por **New Creature Animation - Werewolf** (`Werewolf_New_Anim.esp`). Remova o Precision Creatures dos perfis oficiais (`AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE`) e **regere o Pandora**. O Precision Creatures não tem ESP (é DLL/arquivos), então o load order de plugins não muda com a remoção.


## 0.1 Novidades de 20/11/2026
Todos os itens foram revisados pelo Gemini 4 Neon e aprovados após correções. Relatórios: `REVISAO-HUD-CALICE_neon.md`, `REVISAO-LORDE-20-11_neon.md` e `REVISAO-LORDE-COMPLEMENTO_neon.md`.
- **HUD da Reserva de Sangue "Cálice da Noite"** (aprovada pelo dev):
  - relicário vertical que brilha mais quanto mais vazio;
  - transições suaves, como no selo da Bruxa;
  - respeita movimento reduzido;
  - o visual só se atualiza a cada 0,5 ponto, para economizar no CEF.
- **Lorde Vampiro pelo painel (P10):**
  - o poder não é mais concedido automaticamente;
  - novas ações `supernatural.lordGrant` e `supernatural.lordRevoke` no F7;
  - keyword `JogadorLordeVampiro` **só no servidor**;
  - botão **"Transformar em Lorde Vampiro"** no TAB, visível só para quem recebeu a concessão; o servidor força a transformação, que dura 10 min, com recarga de 2 h persistida.
- **Forma de Lorde:**
  - tamanho **1,25×**, sincronizado para todos;
  - os 10 poderes vão **automaticamente para o menu Q** como favoritos temporários (os favoritos do jogador são restaurados ao sair);
  - Vampiric Drain vira o míssil do Lorde na mão direita;
  - recebe **todas as 15 perks** do Lorde;
  - bônus **só na forma**, somado, por estágio: vida, vigor e magicka de +200 a +450, armadura de +100 a +350 e resistência mágica de 20% a 45%;
  - Vampiric Grip só em NPCs;
  - Blood Ward desvia dano mágico no servidor;
  - Wings of the Night: imune a queda só enquanto levita.
- **Sons** do mod **AETHERIUS - Vampire Lord and Werewolf SFX**:
  - Devouring Swarm: entrada, loop e saída;
  - Bat Form, Mist Form, Unnatural Celerity e Immeasurable Strength: ativar e encerrar;
  - transformação em Lorde (`npc_vampirelord_transformation_01_2d`, `02_2d`, `02_3d`), para todos os jogadores próximos.
- **Devouring Swarm:** dreno dobrado, **20 de vida por segundo** por 15 s.
- **Immeasurable Strength:** agora entra no **motor de dano autoritativo** do servidor.
- **Dash do Morcego**, humano e do Lorde, revisado.
- **Correção do Z:** o leitor do Z usava `userEvent` e foi corrigido para `userEventName`, com guardas de menu e foco. O teste em jogo do Z e do dash continua pendente.
- **Pendente:** validação em jogo de tudo isto, inclusive o áudio para quem observa e a colisão do Grip.
---

## 1. Conteúdo do pacote

```
Entrega_Sobrenatural_19_11/
├─ LEIA-ME_INTEGRACAO.md            ← este arquivo
├─ REBASE_OFICIAL_19-11.md          ← como o pacote foi mesclado com o oficial de 19/11 (conflitos, testes)
├─ 01-servidor/
│  ├─ sobrenatural-servidor.patch   ← patch cumulativo sobre o OFICIAL de 19/11
│  ├─ LISTA-ARQUIVOS.txt            ← 81 arquivos tocados
│  └─ arquivos/                     ← versão final completa de cada arquivo (referência / cópia direta)
├─ 02-cliente/
│  ├─ sobrenatural-cliente.patch    ← patch cumulativo sobre o OFICIAL de 19/11 (sem perfis LOCAL e sem o app.js do F7)
│  ├─ LISTA-ARQUIVOS.txt            ← 31 arquivos (client/src + client/ui-core)
│  └─ arquivos/
├─ 03-mods-jogo/                    ← mods MO2 prontos para copiar em C:\modOrganizer\mods
│  ├─ AETHERIUS - SOBRENATURAL/     ← ESP do sistema + VISOES.esp + sons da Bruxa + fontes .psc
│  ├─ AETHERIUS - LOBISOMEM TRANSFORMACAO/  ← animação/OAR/sons da transformação (kdu)
│  ├─ Colored Magic Auras/          ← NOVO (Nexus 25773) auras Red/Purple
│  ├─ New Creature Animation - Werewolf/    ← NOVO (substitui Precision Creatures)
│  ├─ Miggyluv's Eyes/              ← olhos do vampiro/bruxa (416 MB)
│  ├─ Fangs and Eyes - A Vampire Appearance Mod/ ← NOVA VERSÃO (olhos mais vermelhos), substitui a antiga
│  ├─ AETHERIUS - Vampire Lord and Werewolf SFX/ ← NOVO: sons do Lorde, dos poderes vampíricos e do lobisomem
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

### 2.2 Compatibilidade com o oficial de 19/11
O patch foi **mesclado e regerado sobre o oficial de 19/11** e aplica sem conflito (`git apply --check`). Os 7 arquivos em que oficial e Sobrenatural mexeram nas mesmas linhas foram resolvidos à mão, mantendo os dois lados:
- servidor: magic-service.js, o teste do magic e werewolf/client.js;
- cliente: loadGameService.ts, emoteServer.ts, ppearance.ts;
- painel: pp.js.

A tabela com a resolução de cada um e as baterias de teste está em **REBASE_OFICIAL_19-11.md**.

Dois pontos pedem atenção do dev:
- Em client/src/sync/appearance.ts ficou a **autoria conservadora das magias raciais** do Sobrenatural, e não a remoção ampla que o oficial reintroduziu.
- No MpActor.cpp do oficial, as poções agora curam por segundo. É preciso validar em jogo se o vampiro continua sem conseguir se curar com poção.

### 2.3 Passo a passo
```powershell
cd <repo oficial do servidor>
git checkout -b sobrenatural
git apply --ignore-whitespace "<pacote>\01-servidor\sobrenatural-servidor.patch"
git diff --stat
```
O patch usa LF. Por isso, em repositório CRLF, use --ignore-whitespace. Alternativa: copiar as versões finais de  1-servidor/arquivos/.
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

**Compatibilidade:** mesclado sobre o oficial de 19/11, sem conflitos pendentes (ver `REBASE_OFICIAL_19-11.md`). `tsc` e `webpack` passaram na árvore mesclada.

```powershell
cd <repo oficial do cliente>
git apply --ignore-whitespace "<pacote>\02-cliente\sobrenatural-cliente.patch"
# build (TypeScript + webpack) → skymp5-client.js
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
| `Fangs and Eyes - A Vampire Appearance Mod` | **ATUALIZADO**: substituição direta da versão antiga (Nexus 6842, arquivo `Fangs and Eyes Red-6842-1-0.7z`, de 19/11) | `BVFE.esp` (posição inalterada) + `BVFE.bsa` + `BVFE - Textures.bsa` | Olhos do vampiro em vermelho mais intenso. O mod já está ativo nos perfis QUALIDADE e PERFORMANCE: **troque a pasta inteira**. O `BVFE.esp` é idêntico ao que o servidor já usa (SHA-256 `43205137…`); só mudaram as texturas nos BSA. Por isso **não altera o manifesto de plugins do servidor**. Os BSAs novos vão apenas para o cliente. |
| `AETHERIUS - Vampire Lord and Werewolf SFX` | **NOVO** | — (só arquivos soltos em `sound\fx\...`) | São 62 WAVs: poderes do vampiro, Lorde (transformação, asas, ataques) e lobisomem. Os SOUN/SNDR que tocam esses arquivos estão no `AETHERIUS - SOBRENATURAL.esp`. Alguns arquivos substituem sons vanilla de mesmo caminho. |
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

### 4.4 Releases do `aetherius-mods` (OBRIGATÓRIO para os jogadores receberem os mods)

Os jogadores **não** recebem mods pelo MO2 do servidor: quem instala é o **launcher** (`aetherius-launcher-main/src/Components/Home.tsx`, `getModsAssets`/`computePendingMods`). Funcionamento, conforme o código:
- **Uma release por mod** no repositório privado `aetherius-mods`.
- O **título da release** é o **nome exato da pasta do mod no MO2**. O launcher usa esse título como `folder` no `aetherius-mods-lock.json`.
- O **asset** é `<NomeDoMod>.zip`, com **a pasta do mod como única raiz** dentro do zip. O nome do asset é sanitizado: espaços viram ponto, por exemplo `Fangs.and.Eyes.-.A.Vampire.Appearance.Mod.zip`.
- Mods acima de 2 GB são divididos em `<NomeDoMod>.zip.001`, `.002`… **na mesma release**.
- O launcher compara o `digest` do asset com o `aetherius-mods-lock.json` local. Quando o digest muda, ou a pasta não existe, ele baixa e reinstala. Havendo várias releases do mesmo mod, **vale a mais nova**: a API devolve as mais novas primeiro.
- A **ativação** dos mods (modlist, plugins e loadorder) vem dos perfis em `client/profiles/*` da release do `aetherius-client`, e não do `aetherius-mods`. **Publicar o mod não basta: o perfil também precisa ser atualizado (§4.2).**

#### A) Publicar — mods NOVOS (não existem no `aetherius-mods-lock.json` atual)
| Release (título = pasta) | Asset | Conteúdo (de `03-mods-jogo/`) |
|---|---|---|
| `AETHERIUS - SOBRENATURAL` | `AETHERIUS.-.SOBRENATURAL.zip` | `AETHERIUS - SOBRENATURAL.esp`, `AETHERIUS - SOBRENATURAL - VISOES.esp`, `Sound\FX\Aetherius\Witch\*.wav`, `Source\Scripts\*.psc`. A pasta `Source` é opcional (são só fontes de referência). |
| `AETHERIUS - LOBISOMEM TRANSFORMACAO` | `AETHERIUS.-.LOBISOMEM.TRANSFORMACAO.zip` | Animação da transformação (FNIS/OAR) e sons do kdu |
| `Colored Magic Auras` | `Colored.Magic.Auras.zip` | ESP (ESL-flag) + BSA (Nexus 25773) |
| `New Creature Animation - Werewolf` | `New.Creature.Animation.-.Werewolf.zip` | `Werewolf_New_Anim.esp` + animações. Substitui o Precision Creatures. |
| `AETHERIUS - Vampire Lord and Werewolf SFX` | `AETHERIUS.-.Vampire.Lord.and.Werewolf.SFX.zip` | 62 WAVs em `sound\fx\` (dlc01 mag/npc vampirelord, werewolf) |
| `Miggyluv's Eyes` | o nome que o GitHub gerar (o apóstrofo é removido na sanitização). Confira se o título da release ficou **exatamente** `Miggyluv's Eyes`. | 416 MB, `Miggyluv's Eyes.esp` |

#### B) Publicar nova versão — mods que JÁ estão no `aetherius-mods` e mudaram
| Release | Por quê |
|---|---|
| `Fangs and Eyes - A Vampire Appearance Mod` | Versão nova com olhos mais vermelhos. Mudaram só os BSA; o `BVFE.esp` é idêntico. Publique uma release nova e **apague a antiga**. |
| `Pandora - Output` | **Regere o Pandora no perfil oficial** depois de entrar o New Creature Animation e sair o Precision Creatures, e publique o resultado. **Não use** o `Pandora - Output` do pacote: ele foi gerado sobre o perfil LOCAL, sem FISICA nem Ghostshelf. Serve só para conferência. |
| `AETHERIUS - UI CORE` | Módulo `supernatural` (TAB, telas, HUDs, selo, ritual, botão Forma da Besta), gerado a partir de `02-cliente` (`client/ui-core`). |
| `AETHERIUS - MERIDIAN - COMPATIBILIDADE` | `MeridianUI\aetherius-client\admin-panel\app.js` com a aba Sobrenatural do F7 (`06-painel-adm`). Se essa pasta também levar o `Platform\Plugins\skymp5-client.js`, atualize o bundle gerado de `02-cliente`. |
| `AETHERIUS - LOBISOMEM` | **Sem mudança.** Continua como está (`AETHERIUS - LOBISOMEM.esp`). |

O bundle do cliente (`skymp5-client.js`) segue o fluxo normal da release do **`aetherius-client`**, junto com os perfis atualizados.

#### C) Remover — mods que estão no `aetherius-mods` e NÃO serão mais usados
| Release | Motivo | O que fazer |
|---|---|---|
| `Precision Creatures` (`Precision.Creatures.zip`) | Removido pelo dev em 19/11: a fera atacava com uma mão só e sem combos. Substituído pelo New Creature Animation - Werewolf. | 1) **Apagar a release** do `aetherius-mods`, para que novas instalações não o baixem. 2) **Tirar do `modlist.txt`** dos perfis `AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - PERFORMANCE` em `client/profiles` e publicar a release do `aetherius-client`. Ele não tem ESP, então `plugins.txt` e `loadorder.txt` não mudam. 3) **Regerar o Pandora** (item B). |
| *(versão antiga do)* `Fangs and Eyes - A Vampire Appearance Mod` | Substituído pela versão nova (item B). | Apagar a release antiga, para não restar duas versões do mesmo mod. |

⚠️ **O launcher não apaga do disco do jogador** a pasta de um mod que saiu do `aetherius-mods`, nem a entrada no `aetherius-mods-lock.json`. Não há código de remoção para mods obsoletos. O que tira o mod do jogo é o **perfil**: a pasta continua em `mods\`, mas desativada no `modlist.txt`, e o MO2 não a carrega. Se quiser limpar o disco dos jogadores, é preciso implementar a remoção no launcher. Isso fica fora deste pacote.

#### D) Depois de publicar
- **Servidor:** rode o `Prepare`/`sync-client-plugins.js` com o perfil oficial atualizado. O `aetherius-loadorder.json` precisa listar os novos ESPs nos mesmos bytes que os jogadores baixam:
  - `AETHERIUS - SOBRENATURAL.esp`;
  - `AETHERIUS - SOBRENATURAL - VISOES.esp`;
  - `Colored Magic Auras.esp`;
  - `Werewolf_New_Anim.esp`;
  - `Miggyluv's Eyes.esp`.
- **Manifesto de segurança:** rode `server/tools/build-security-manifest.js` com `--previous`. Ele lê as pastas a partir do `aetherius-mods-lock.json`. Nenhum dos mods novos traz DLL, mas o manifesto precisa refletir a lista de pastas oficial nova.
- **Teste de instalação limpa:** use o launcher num MO2 vazio. Ele deve baixar os 6 mods novos e as versões novas, e o jogo deve abrir com o perfil novo, sem o Precision Creatures.
---

## 5. Painel administrativo (F7) — **ver `06-painel-adm/LEIA-ME_PAINEL-ADM.md`**
A pasta `06-painel-adm/` traz:
- os arquivos de servidor do painel;
- o **patch do frontend F7** (`client/admin-panel/app.js`), que **não está** no patch do cliente porque o painel é distribuído pelo mod `AETHERIUS - MERIDIAN - COMPATIBILIDADE`;
- o contrato das 10 ações `supernatural.*` (incluindo `lordGrant` e `lordRevoke`), para o painel web oficial (eksmurf).

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
- [ ] Branch nova no servidor; aplicar `sobrenatural-servidor.patch` (sem conflitos sobre o oficial de 19/11).
- [ ] Não aplicar `settings.ts`/`yarn.lock` (já excluídos).
- [ ] Recompilar `scam_native.node`; build TS e GameplayCore; testes.
- [ ] Branch nova no cliente; aplicar `sobrenatural-cliente.patch`; build do `skymp5-client.js` e da UI Core.
- [ ] Painel ADM: aplicar o patch 004 no `client/admin-panel/app.js`, distribuir pelo mod MERIDIAN - COMPATIBILIDADE e seguir o checklist de `06-painel-adm/LEIA-ME_PAINEL-ADM.md`.
- [ ] Substituir a pasta do **Fangs and Eyes** pela versão nova (§4.1).
- [ ] **Releases do `aetherius-mods` (§4.4):** publicar os 6 mods novos e as versões novas (Fangs and Eyes, Pandora regerado, UI CORE, MERIDIAN - COMPATIBILIDADE); apagar as releases do **Precision Creatures** e do Fangs and Eyes antigo; atualizar os perfis no `aetherius-client`.
- [ ] Instalar os mods da §4.1 e remover o **Precision Creatures**.
- [ ] Ajustar o load order (§4.2) nos dois perfis oficiais e nos `client/profiles`.
- [ ] Regerar o **Pandora** em cada perfil oficial.
- [ ] `Prepare` do servidor e conferir o manifesto de plugins (contagem e hashes).
- [ ] Definir as variáveis de ambiente de produção (§2.4).
- [ ] Teste com 2 jogadores pelos roteiros da §6.
- [ ] Registrar a integração no Acervo (`02-sistemas/sobrenatural/diario.md`).
