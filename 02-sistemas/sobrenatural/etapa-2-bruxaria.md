# Etapa 2 — Bruxaria (Catálogo de Records, Auditoria do Modpack e Projeto da Fase 2)

## Atualização 16/11 — integração e revisão crítica por Sol High

O catálogo de Argon (`603dea7`) foi ligado ao serviço em `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural-service.js` depois de substituir o reconciliador destrutivo por `supernatural/grant-origin.js`. O estado desta seção substitui a pendência de ligação descrita no histórico abaixo. Revisão final independente por Terra e homologação em jogo continuam pendentes; o dev adiou os testes humanos.

### Achados aceitos e correções

| Revisão de Sol sobre Argon | Tratamento |
|---|---|
| Catálogo isolado não concedia nada | Ligados spellsFor/perksFor/allSpells/allPerks; validação do boot inclui todos os 60 records Bruxa |
| União de catálogos apagava magias pessoais | Origem persistida por personagem e plugin/id local; remoção apenas de adições confirmadas, sem adoção das preexistentes |
| Perks previamente presentes eram registradas como próprias no cliente | Reconciliador compartilhado com GameplayGrantService, claims por fonte, proteção externa e ACK de autoria por nonce; manter proteção/verificação tardia original do Core |
| Neutralização SSP marcava sucesso mesmo sem resolver quests/métodos; uma falha repetia todas | Conclusão individual das três quests e retry das que falham; só marca completo quando as três foram resolvidas/paradas |
| Pisos de habilidade podiam ser sobrescritos por caminho paralelo | Confirmado uso de ClassSkillsProjection e patch 005 já existentes; novo teste executa receptor real compilado. Não acrescentado SetActorValue paralelo |
| Descrição de isolamento usava `characterKey` como se fosse sempre UUID | Este host usa profile:N no Core; originScope usa UUID do diretório para separar os caches de perks. Banco de classes por perfil não foi migrado |
| Inferência dos asteriscos da planilha | Mantida apenas como hipótese informativa; não foi tratada como regra nova de jogo |

Não se rejeitaram os records nem o desenho dos pisos. Os IDs de Necromancy/Necromaster, duas variantes de Keeper, Dark Arts, Shadowbolt e Atronach Expert conferem com o catálogo previamente auditado; o boot real resolveu todos. A parada global das quests SSP continua uma decisão autorizada do dev e continua afetando personagens sem Bruxaria; não foi inventada neutralização seletiva de scripts neste lote. A correção de assinatura não remove o bloqueio global de Papyrus.

### Comportamento integrado

- Neófito recebe 4 Mastery e Eldritch Blast. Escolha de vertente continua exclusiva no estágio 1, no servidor.
- TENEBRAE: Shadowbolt `002E2A` no estágio 1, `002E2C` nos 2..5 e `002E2F` no 6; Dark Arts `0028AE` a partir do 3; Atronach Expert `04F121` direto no 5. Remoção de versão antiga só se ela foi concedida pelo serviço; uma versão pessoal anterior é preservada, mesmo que coexistam duas versões nesse caso.
- MORTUI/DAEMONES: acumulação dos grants do catálogo. Remover/trocar maldição retira somente adições próprias. Falha/no-op de remoção conserva o ledger para nova tentativa no login.
- Pisos do patch 005: Math.max(classe, piso) nas quatro escolas, inclusive sem classe; restauração dos valores de classe na remoção. Nenhuma mudança no nativo.
- Migração conservadora: portadores antigos sem ledger não têm toda a magia retroativamente adotada. Concessões de outras fontes feitas depois da mesma adição, sem declarar autoria, são inobserváveis; a união explícita de perks cobre o caminho GameplayCore.

### Evidência

Na raiz do servidor local, `node --test overlays/sobrenatural/teste-origem.cjs` cobre 13 cenários, incluindo personagens sem maldição com magias próprias, as três concessões sobre magias já conhecidas, remoção/troca/reconexão, progressão/redução TENEBRAE, falhas de persistência e claims de perks nas duas ordens. O conjunto maior tem 93 testes e o conjunto de transporte/perks/pisos do Core tem 29; os scripts de serviço/F7 passaram. Builds de cliente/servidor/Core sem addon passaram. Detalhes, comandos, hashes e roteiro em `ETAPA-2_formas-e-poderes.md`.

Overlays: `files/server/gamemode/supernatural-service.js`, `supernatural/{witch-grants,grant-origin,perk-origin,client}.js`, patch 005 preservado e complemento `006-perk-origin-core.patch`; cliente em `overlays/cliente-local/003-perk-origins.patch`. A revisão original de Argon não equivale a aprovação deste lote; **Terra revisa depois**.

---


**Data:** 2026-11-15  
**Autor:** Gemini 4 Argon (Fase 1 e Fase 2 concluídas; ligação de `witch-grants.js` ao `supernatural-service.js` **PENDENTE** do mecanismo por origem)  
**Fontes auditadas:**
- Planilha oficial transcrita em [`Sobrenatural/docs/ESPECIFICACAO.md`](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ESPECIFICACAO.md) (§5 Bruxaria — tabela revisada prevalece sobre o resumo textual)
- Auditoria de concorrência e origem em [`Sobrenatural/docs/investigacoes/INVESTIGACAO_15-11_bruxaria-concorrencia-grants.md`](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/investigacoes/INVESTIGACAO_15-11_bruxaria-concorrencia-grants.md)
- Padrões de serviço e formas em [`Sobrenatural/docs/ETAPA-2_vampirismo.md`](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ETAPA-2_vampirismo.md) e [`Sobrenatural/docs/ETAPA-2_formas-e-poderes.md`](file:///C:/Code/Aetherius%20-%20New%20Systems/Sobrenatural/docs/ETAPA-2_formas-e-poderes.md)
- Modpack MO2 (`C:\modOrganizer`, perfis `AETHERIUS - GRAFICO - QUALIDADE` [433 plugins ativos] e `AETHERIUS - LOCAL` [435/436 plugins ativos]) via MCP `houseCARL` (somente leitura)

---

## Records (levantamento Luna MAX / Gemini 4 Argon via houseCARL)

### 1. Convenção de Tuplas `[plugin, id local, editorId, tipo]`

> **Lacunas do levantamento inicial resolvidas via `houseCARL`:**
> - **Perk `Necromancy` (Estágio 2):** localizado em `['Skyrim.esm', 0x0581dd, 'VKR_Con_030_Necromancer_Perk_WasNecromancy', 'PERK']` (vencedor: `Vokrii - Minimalistic Perks of Skyrim.esp`, Nome PT-BR: `Necromancia`, EditorID com `Necromancer` mas sufixo `_WasNecromancy`).
> - **`Conjure Keeper*` (MORTUI, Estágio 6):** além de Archer (`0x0008d0`, `Simple_SummonKeeperArcherSpell`) e Warrior (`0x0008d2`, `Simple_SummonKeeperWarriorSpell`), existe uma 3ª variante em `PuddingFace_SimpleSpellsPackage.esp` (vencedor `AETHERIUS - BALANCE.esp`) que não aparece em buscas por `"Keeper"` devido a um typo no EditorID original: `['PuddingFace_SimpleSpellsPackage.esp', 0x0008d1, 'Simple_SummonKeepeKnight', 'SPEL']` (`Conjure Guardião Knight`).
> - **`Necromaster` (Estágio 4):** definido em `Vokrii - Minimalistic Perks of Skyrim.esp` (`0x317213`, `VKR_Con_090_Necromaster_Perk`), sobrescrito por `Mysticism - Vokrii Compatibility Patch.esp` com EditorID vencedor `'VKR_Con_090_MYSTICISMThrallLord_Perk'` (`Senhor Thrall`).

No serviço do servidor (`source/server/gamemode/supernatural/form-resolver.js`) e no aplicador de perks do cliente (`source/server/gamemode/supernatural/client.js`):
- `plugin` é o **plugin definidor (master)** onde o FormID base foi originalmente criado (`Skyrim.esm`, `Dawnguard.esm`, `Vokrii - Minimalistic Perks of Skyrim.esp`, `MysticismMagic.esp`, `Triumvirate - Mage Archetypes.esp`, `ShadowSpellPackage.esp`, `PuddingFace_SimpleSpellsPackage.esp`).
- `id local` é o FormID relativo (`0x000001`..`0xFFFFFF`) dentro do plugin definidor. `sp.Game.getFormFromFile(localId, plugin)` no cliente e `createFormResolver` no servidor resolvem a partir do plugin definidor + `id local`.
- `editorId` é o **EditorID do record vencedor (winner)** na load order ativa, validado por `mp.lookupEspmRecordById(id)?.record?.editorId === editorId`.
- Nos dois perfis (`AETHERIUS - GRAFICO - QUALIDADE` e `AETHERIUS - LOCAL`), todos os records da Bruxaria possuem exatamente os mesmos vencedores.

---

## 2. Perks Globais por Estágio (Vokrii)

Todas as Bruxas (independentemente da Vertente escolhida) recebem cumulativamente os perks abaixo conforme o estágio atingido.

> **Atenção (`Necromaster` → `Thrall Lord` no Estágio 4):** O perk de nível 90 de Conjuração definido em `Vokrii - Minimalistic Perks of Skyrim.esp` (`0x317213`, EditorID original `VKR_Con_090_Necromaster_Perk`) é sobrescrito pelo patch `Mysticism - Vokrii Compatibility Patch.esp`, que altera o **EditorID vencedor** para `VKR_Con_090_MYSTICISMThrallLord_Perk` (Nome: `Senhor Thrall`). Para que `form-resolver.js` valide `record.editorId === editorId`, a tupla deve usar `'VKR_Con_090_MYSTICISMThrallLord_Perk'`.

| Estágio | Nome na Especificação (§5) | `[plugin, id local, editorId, tipo]` | Plugin Vencedor (Winner) | Nome no Jogo (Winner) |
|---|---|---|---|---|
| **1 — Neófito** | Alteration Mastery | `['Skyrim.esm', 0x0f2ca6, 'VKR_Alt_000_AlterationMastery_Perk_WasAlteration1', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Domínio de Alteração` |
| **1 — Neófito** | Conjuration Mastery | `['Skyrim.esm', 0x0f2ca7, 'VKR_Con_000_ConjurationMastery_Perk_WasConjuration1', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Maestria da Conjuração` |
| **1 — Neófito** | Destruction Mastery | `['Skyrim.esm', 0x0f2ca8, 'VKR_Des_000_DestructionMastery_Perk_WasDestruction1', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Destruição Maestria` |
| **1 — Neófito** | Illusion Mastery | `['Skyrim.esm', 0x0f2ca9, 'VKR_Ill_000_IllusionMastery_Perk_WasIllusion1', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Ilusão Maestria` |
| **2 — Acólito** | Necromancy | `['Skyrim.esm', 0x0581dd, 'VKR_Con_030_Necromancer_Perk_WasNecromancy', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Necromancia` |
| **2 — Acólito** | Rift Summoner (rank 1) | `['Skyrim.esm', 0x105f30, 'VKR_Con_040_RiftSummoner1_Perk_WasSummoner1', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Invocador de Rift` (1/2) |
| **2 — Acólito** | Rift Summoner (rank 2) | `['Skyrim.esm', 0x105f31, 'VKR_Con_040_RiftSummoner2_Perk_WasSummoner2', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Invocador de Rift` (2/2) |
| **2 — Acólito** | Atromancy | `['Skyrim.esm', 0x0cb419, 'VKR_Con_030_Atromancer_Perk_WasAtromancy', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Atromancia` |
| **2 — Acólito** | Raw Power (+10%) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x63237f, 'VKR_Des_030_RawPower1_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Energia bruta` (1/3) |
| **3 — Feiticeiro Negro** | Ghoul Frenzy | `['Skyrim.esm', 0x0581de, 'VKR_Con_050_GhoulFrenzy_Perk_WasDarkSouls', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Ghoul Frenesi` |
| **3 — Feiticeiro Negro** | Oblivion Stone (rank 1) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x36833b, 'VKR_Con_050_OblivionStone1_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Pedra do Esquecimento` (1/2) |
| **3 — Feiticeiro Negro** | Oblivion Stone (rank 2) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x372556, 'VKR_Con_050_OblivionStone2_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Pedra do Esquecimento` (2/2) |
| **3 — Feiticeiro Negro** | Conjuration Dual Casting | `['Skyrim.esm', 0x0153ce, 'VKR_Con_020_ConjurationDualCasting_Perk_WasConjurationDualCasting', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Conjuração com Duas Mãos` |
| **4 — Magus Diaboli** | Necromaster (Thrall Lord) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x317213, 'VKR_Con_090_MYSTICISMThrallLord_Perk', 'PERK']` | `Mysticism - Vokrii Compatibility Patch.esp` | `Senhor Thrall` |
| **4 — Magus Diaboli** | Blood Zombie | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x016436, 'VKR_Con_070_BloodZombie_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Zumbi Sangrento` |
| **4 — Magus Diaboli** | Raw Power (+20%) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x632380, 'VKR_Des_030_RawPower2_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Energia bruta` (2/3) |
| **5 — Alto Teurgo** | *(nenhum perk global)* | — | — | — |
| **6 — Hierofante** | Twin Souls | `['Skyrim.esm', 0x0d5f1c, 'VKR_Con_100_TwinSouls_Perk_WasTwinSouls', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Almas Gêmeas` |
| **6 — Hierofante** | Raw Power (+30%) | `['Vokrii - Minimalistic Perks of Skyrim.esp', 0x632381, 'VKR_Des_030_RawPower3_Perk', 'PERK']` | `Vokrii - Minimalistic Perks of Skyrim.esp` | `Energia bruta` (3/3) |

---

## 3. Feitiços Globais por Estágio

Concedidos a todas as Bruxas independentemente da Vertente:

| Estágio | Nome na Especificação (§5) | `[plugin, id local, editorId, tipo]` | Plugin Vencedor (Winner) | Nome no Jogo (Winner) |
|---|---|---|---|---|
| **1 — Neófito** | Eldritch Blast | `['Triumvirate - Mage Archetypes.esp', 0x19b465, 'TVR_Warlock_D000_Spell_EldritchBlast', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Eldritch Blast` |
| **2 — Acólito** | Balefire | `['Triumvirate - Mage Archetypes.esp', 0x1a0577, 'TVR_Warlock_D025_Spell_Balefire', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Balefire` |
| **3 — Feiticeiro Negro** | Witch Bolt | `['Triumvirate - Mage Archetypes.esp', 0x1af89a, 'TVR_Warlock_D050_Spell_WitchBolt', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Feiticeira Parafuso` |
| **4 — Magus Diaboli** | Equilíbrio* | `['Skyrim.esm', 0x0da746, 'MAG_Equilibrium', 'SPEL']` | `MysticismMagic.esp` | `Equilíbrio` |
| **5 — Alto Teurgo** | *(nenhum feitiço global)* | — | — | — |
| **6 — Hierofante** | *(nenhum feitiço global)* | — | — | — |

---

## 4. Feitiços e Perks por Vertente e Estágio (§5)

### 4.1. Vertente MORTUI (Necromancia — *Mysticism* + *Dawnguard* + *Simple Spells Package*)

| Estágio | Nome na Especificação (§5) | `[plugin, id local, editorId, tipo]` | Plugin Vencedor (Winner) | Nome no Jogo (Winner) |
|---|---|---|---|---|
| **1 — Neófito** | Conjure Skeleton | `['MysticismMagic.esp', 0x413541, 'MAG_ConjureSkeleton', 'SPEL']` | `MysticismMagic.esp` | `Conjurar esqueleto` |
| **1 — Neófito** | Reanimate Corpse | `['Skyrim.esm', 0x065bd7, 'MAG_ReanimateCorpse', 'SPEL']` | `MysticismMagic.esp` | `Reanimar Cadáver` |
| **2 — Acólito** | Conjure Skeleton Archer | `['MysticismMagic.esp', 0x413545, 'MAG_ConjureSkeletonArcher', 'SPEL']` | `MysticismMagic.esp` | `Conjure Esqueleto Archer` |
| **2 — Acólito** | Conjure Skeleton Champion (estágio 2: Warrior)* | `['MysticismMagic.esp', 0x40933c, 'MAG_ConjureSkeletonWarrior', 'SPEL']` | `MysticismMagic.esp` | `Conjure Esqueleto Guerreiro` |
| **3 — Feiticeiro Negro** | Conjure Boneman* | `['Dawnguard.esm', 0x0045ba, 'MAG_ConjureBoneman', 'SPEL']` | `MysticismMagic.esp` | `Conjurar Homem de Ossos` |
| **3 — Feiticeiro Negro** | Revenant | `['Skyrim.esm', 0x096d94, 'MAG_Revenant', 'SPEL']` | `MysticismMagic.esp` | `Redivivo` |
| **4 — Magus Diaboli** | Daedric Cure | `['MysticismMagic.esp', 0xdb04d0, 'MAG_DaedricCure', 'SPEL']` | `MysticismMagic.esp` | `Daedric Cure` |
| **4 — Magus Diaboli** | Dread Zombie | `['Skyrim.esm', 0x096d95, 'MAG_DreadZombie', 'SPEL']` | `MysticismMagic.esp` | `Zumbi Terrível` |
| **5 — Alto Teurgo** | Conjure Mistman* | `['Dawnguard.esm', 0x0045b8, 'MAG_ConjureMistman', 'SPEL']` | `MysticismMagic.esp` | `Conjurar Homem de Névoa` |
| **5 — Alto Teurgo** | Conjure Skeleton Champion | `['MysticismMagic.esp', 0x41864c, 'MAG_ConjureSkeletonChampion', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure Campeão de Esqueleto` |
| **5 — Alto Teurgo** | Oblivion's Embrace | `['MysticismMagic.esp', 0xdb04d2, 'MAG_OblivionsEmbrace', 'SPEL']` | `MysticismMagic.esp` | `Abraço do Esquecimento` |
| **6 — Hierofante** | Conjure Wrathman* | `['Dawnguard.esm', 0x0045b3, 'MAG_ConjureWrathman', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjurar Homem de Ira` |
| **6 — Hierofante** | Dead Thrall | `['Skyrim.esm', 0x07e8df, 'MAG_DeadThrall', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Servo Morto-Vivo` |
| **6 — Hierofante** | Conjure Keeper* *(variante 1: Archer)* | `['PuddingFace_SimpleSpellsPackage.esp', 0x0008d0, 'Simple_SummonKeeperArcherSpell', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure o Guardião Archer` |
| **6 — Hierofante** | Conjure Keeper* *(variante 2: Knight)* | `['PuddingFace_SimpleSpellsPackage.esp', 0x0008d1, 'Simple_SummonKeepeKnight', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure Guardião Knight` |
| **6 — Hierofante** | Conjure Keeper* *(variante 3: Warrior)* | `['PuddingFace_SimpleSpellsPackage.esp', 0x0008d2, 'Simple_SummonKeeperWarriorSpell', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure o Guardião Guerreiro` |

> **Observações e Decisões sobre MORTUI (confirmadas pelo dev/orquestrador em 15/11/2026):**
> 1. **Estágio 2 vs Estágio 5 (`Conjure Skeleton Warrior` vs `Conjure Skeleton Champion`):** Na planilha (`ESPECIFICACAO.md` §5, nota de rodapé 1), "Conjure Skeleton Champion" aparece repetido nos estágios 2 e 5, enquanto o resumo textual lista `Conjure Skeleton Warrior` no estágio 2 (`MAG_ConjureSkeletonWarrior`, `0x40933c`, nível Adept/Acólito no Mysticism) e `Conjure Skeleton Champion` no estágio 5 (`MAG_ConjureSkeletonChampion`, `0x41864c`, nível Expert).
> 2. **`Conjure Keeper*` no Estágio 6 (Decisão):** Conceder **apenas** as variantes **Archer** (`['PuddingFace_SimpleSpellsPackage.esp', 0x0008d0, 'Simple_SummonKeeperArcherSpell', 'SPEL']`) e **Warrior** (`['PuddingFace_SimpleSpellsPackage.esp', 0x0008d2, 'Simple_SummonKeeperWarriorSpell', 'SPEL']`). A variante **Knight** (`0x0008d1`, `Simple_SummonKeepeKnight`) **não entra**.

---

### 4.2. Vertente DAEMONES (Demonologia — *Mysticism* + *Triumvirate*)

| Estágio | Nome na Especificação (§5) | `[plugin, id local, editorId, tipo]` | Plugin Vencedor (Winner) | Nome no Jogo (Winner) |
|---|---|---|---|---|
| **1 — Neófito** | Conjure Dremora (Warrior) | `['MysticismMagic.esp', 0x41d74f, 'MAG_ConjureDremoraWarrior', 'SPEL']` | `MysticismMagic.esp` | `Conjure Dremora Guerreiro` |
| **2 — Acólito** | Conjure Ravagor | `['Triumvirate - Mage Archetypes.esp', 0x22e324, 'TVR_Warlock_C050_ConjureRavagor', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Conjure Ravagor` |
| **2 — Acólito** | Weaken | `['Triumvirate - Mage Archetypes.esp', 0x22411c, 'TVR_Warlock_I000_Spell_Weaken', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Weaken` |
| **3 — Feiticeiro Negro** | Conjure Dremora Warlock | `['MysticismMagic.esp', 0x42795a, 'MAG_ConjureDremoraWarlock', 'SPEL']` | `MysticismMagic.esp` | `Conjure Dremora Warlock` |
| **3 — Feiticeiro Negro** | Conjure Dremora Hunter | `['MysticismMagic.esp', 0x00fff4, 'MAG_ConjureDremoraHunter', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure Dremora Caçador` |
| **4 — Magus Diaboli** | Daedric Cure | `['MysticismMagic.esp', 0xdb04d0, 'MAG_DaedricCure', 'SPEL']` | `MysticismMagic.esp` | `Daedric Cure` |
| **4 — Magus Diaboli** | Cloudkill | `['Triumvirate - Mage Archetypes.esp', 0x1a0588, 'TVR_Warlock_D075_Spell_Cloudkill', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Cloudkill` |
| **5 — Alto Teurgo** | Conjure Magister | `['Triumvirate - Mage Archetypes.esp', 0x22e326, 'TVR_Warlock_C075_ConjureOathbreaker', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Conjure Magister` |
| **5 — Alto Teurgo** | Frailty | `['Triumvirate - Mage Archetypes.esp', 0x224119, 'TVR_Warlock_I025_Spell_Frailty', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Frágil` |
| **6 — Hierofante** | Conjure Leviathan | `['Triumvirate - Mage Archetypes.esp', 0x22e328, 'TVR_Warlock_C100_ConjureLeviathan', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Conjure Leviatã` |
| **6 — Hierofante** | Oblivion's Embrace | `['MysticismMagic.esp', 0xdb04d2, 'MAG_OblivionsEmbrace', 'SPEL']` | `MysticismMagic.esp` | `Abraço do Esquecimento` |
| **6 — Hierofante** | Decrepify | `['Triumvirate - Mage Archetypes.esp', 0x224113, 'TVR_Warlock_I100_Spell_Decrepify', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Decrepificar` |

---

### 4.3. Vertente TENEBRAE (Umbramancia — *Shadow Spell Package* + *Triumvirate*)

Conforme `ESPECIFICACAO.md` §5 (incluindo os upgrades de estágio e o perk de estágio 3):

| Estágio | Nome na Especificação (§5) | Ação no Estágio | `[plugin, id local, editorId, tipo]` | Plugin Vencedor (Winner) | Nome no Jogo (Winner) |
|---|---|---|---|---|---|
| **1 — Neófito** | Shadowbolt (inicial / Apprentice) | Concede versão nv. 25 | `['ShadowSpellPackage.esp', 0x002e2a, '_SSPShadowbolt01', 'SPEL']` | `Shadow Spell Package - Tweaks and Bug Fixes.esp` | `Shadowbolt` |
| **2 — Acólito** | Crushing Darkness | Concede versão base | `['ShadowSpellPackage.esp', 0x002e30, '_SSPCrush01', 'SPEL']` | `Shadow Spell Package - Tweaks and Bug Fixes.esp` | `Esmagando a Escuridão` |
| **2 — Acólito** | Shadow Stride | Concede feitiço | `['Triumvirate - Mage Archetypes.esp', 0x381af8, 'TVR_Shadow_A000_Spell_ShadowStride', 'SPEL']` | `Triumvirate - Mage Archetypes.esp` | `Shadow Stride` |
| **2 — Acólito** | Upgrade do Shadowbolt (nv. 50) | Remove `0x002e2a` (`_SSPShadowbolt01`), concede `0x002e2c` | `['ShadowSpellPackage.esp', 0x002e2c, '_SSPShadowbolt02', 'SPEL']` | `Shadow Spell Package - Tweaks and Bug Fixes.esp` | `Shadowbolt` |
| **3 — Feiticeiro Negro** | Curse of Exhaustion | Concede feitiço | `['ShadowSpellPackage.esp', 0x00a161, '_SSPCurseOfExhaustion', 'SPEL']` | `ShadowSpellPackage.esp` | `Maldição da exaustão` |
| **3 — Feiticeiro Negro** | Dark Arts: Shadow Mastery (+25% dano de sombra) | Concede PERK | `['ShadowSpellPackage.esp', 0x0028ae, '_SSPDarkArts', 'PERK']` | `ShadowSpellPackage.esp` | `Artes das Trevas` |
| **4 — Magus Diaboli** | Curse of Fragility | Concede feitiço | `['ShadowSpellPackage.esp', 0x00c908, '_SSPCurseOfFragility', 'SPEL']` | `ShadowSpellPackage.esp` | `Maldição da Fragilidade` |
| **4 — Magus Diaboli** | Shadowform | Concede feitiço | `['ShadowSpellPackage.esp', 0x01caab, '_SSPShadowform', 'SPEL']` | `ShadowSpellPackage.esp` | `Forma das sombras` |
| **5 — Alto Teurgo** | Conjure Shadow Atronach + Upgrade (nv. 75) | Ver seção 5.2 (versões `0x04e0e6` / `0x04f120` / `0x04f121`) | `['ShadowSpellPackage.esp', 0x04f121, '_SSPConjureShadowAtronachExpert', 'SPEL']` | `ShadowSpellPackage.esp` | `Conjure Shadow Atronaca` |
| **6 — Hierofante** | Shadow Rend | Concede feitiço | `['ShadowSpellPackage.esp', 0x0028ab, '_SSPRend', 'SPEL']` | `AETHERIUS - BALANCE.esp` | `Shadow Rend` |
| **6 — Hierofante** | Upgrade do Shadowbolt (nv. 75) | Remove `0x002e2c` (`_SSPShadowbolt02`), concede `0x002e2f` | `['ShadowSpellPackage.esp', 0x002e2f, '_SSPShadowbolt03', 'SPEL']` | `Shadow Spell Package - Tweaks and Bug Fixes.esp` | `Shadowbolt` |

---

## 5. Versões Escalonadas do *Shadow Spell Package* e Perk *Dark Arts: Shadow Mastery*

### 5.1. Todas as versões de `Shadowbolt`

| Versão / Tier no Mod | Requisito original no Mod | `[plugin, id local, editorId, tipo]` | Dano / Efeito Base (Winner `Shadow Spell Package - Tweaks and Bug Fixes.esp`) | Estágio na Bruxaria (§5) |
|---|---|---|---|---|
| Nível 1 (Apprentice) | Inicial (Tomo) | `['ShadowSpellPackage.esp', 0x002e2a, '_SSPShadowbolt01', 'SPEL']` | `20` pts de dano de sombra (`_SSPShadowboltEffect25`, `0x002e29`) | **Estágio 1 — Neófito** |
| Nível 2 (Adept / nv. 50) | `Destruction >= 50` | `['ShadowSpellPackage.esp', 0x002e2c, '_SSPShadowbolt02', 'SPEL']` | `35` pts de dano de sombra (`_SSPShadowboltEffect50`, `0x002e2b`) | **Estágio 2 — Acólito** (substitui `0x002e2a`) |
| Nível 3 (Expert / nv. 75) | `Destruction >= 75` | `['ShadowSpellPackage.esp', 0x002e2f, '_SSPShadowbolt03', 'SPEL']` | `50` pts de dano de sombra (`_SSPShadowboltEffect75`, `0x002e2d`) | **Estágio 6 — Hierofante** (substitui `0x002e2c`) |

### 5.2. Todas as versões de `Conjure Shadow Atronach`

| Versão / Tier no Mod | Requisito original no Mod | `[plugin, id local, editorId, tipo]` | Efeito Base (Winner `ShadowSpellPackage.esp`) | Estágio na Bruxaria (§5) |
|---|---|---|---|---|
| Nível 1 (Apprentice) | Inicial (Tomo) | `['ShadowSpellPackage.esp', 0x04e0e6, '_SSPConjureShadowAtronach', 'SPEL']` | Invoca `_SSPShadowAtronachEffect` (`0x04dfaa`) por `60s` | Ver nota abaixo |
| Nível 2 (Adept / nv. 50) | `Conjuration >= 50` | `['ShadowSpellPackage.esp', 0x04f120, '_SSPConjureShadowAtronachAdept', 'SPEL']` | Invoca `_SSPShadowAtronachEffectAdept` (`0x04f11e`) por `60s` | Ver nota abaixo |
| Nível 3 (Expert / nv. 75) | `Conjuration >= 75` | `['ShadowSpellPackage.esp', 0x04f121, '_SSPConjureShadowAtronachExpert', 'SPEL']` | Invoca `_SSPShadowAtronachEffectExpert` (`0x04f11f`) por `60s` | **Estágio 5 — Alto Teurgo** |

> **Decisão sobre `Conjure Shadow Atronach` no Estágio 5 (confirmada em 15/11/2026):** Como na tabela de `ESPECIFICACAO.md` §5 tanto o desbloqueio de `Conjure Shadow Atronach` quanto o seu upgrade de nível 75 ocorrem no **Alto Teurgo (Estágio 5)**, o servidor concede **diretamente** a versão **Expert** (`['ShadowSpellPackage.esp', 0x04f121, '_SSPConjureShadowAtronachExpert', 'SPEL']`) no Estágio 5. As três versões (`0x04e0e6`, `0x04f120`, `0x04f121`) permanecem listadas em `SHADOW_ATRONACH_VERSIONS` / `allSpells()` para reconciliação e limpeza segura pelo servidor.

### 5.3. Outros feitiços escalonados de `TENEBRAE` no *Shadow Spell Package*

Embora a planilha (§5) só especifique upgrades explícitos para `Shadowbolt` (estágios 2 e 6) e `Conjure Shadow Atronach` (estágio 5), o mod possui versões superiores para outros 3 feitiços da vertente:

| Feitiço | Versão Base (`[plugin, id local, editorId, tipo]`) | Versão Aprimorada no Mod (`[plugin, id local, editorId, tipo]`) | Gatilho Original no Mod |
|---|---|---|---|
| **Crushing Darkness** | `['ShadowSpellPackage.esp', 0x002e30, '_SSPCrush01', 'SPEL']` (50 dano) | `['ShadowSpellPackage.esp', 0x002e32, '_SSPCrush02', 'SPEL']` (65 dano) | `Destruction >= 75` |
| **Curse of Exhaustion** | `['ShadowSpellPackage.esp', 0x00a161, '_SSPCurseOfExhaustion', 'SPEL']` | `['ShadowSpellPackage.esp', 0x00f0ab, '_SSPCurseOfExhaustionExpert', 'SPEL']` | `HasPerk(_SSPHexMaster)` (`0x0028b5`) |
| **Curse of Fragility** | `['ShadowSpellPackage.esp', 0x00c908, '_SSPCurseOfFragility', 'SPEL']` | `['ShadowSpellPackage.esp', 0x00f0ac, '_SSPCurseOfFragilityExpert', 'SPEL']` | `HasPerk(_SSPHexMaster)` (`0x0028b5`) |

### 5.4. Perk `Dark Arts: Shadow Mastery` (+25% dano de sombra)

No *Shadow Spell Package*, o bônus "Dark Arts: Shadow Mastery (+25% dano de sombra)" envolve dois records distintos porque o mod original usa um Spell Tomo para disparar um script Papyrus que adiciona o Perk real:

| Papel no Mod | `[plugin, id local, editorId, tipo]` | Plugin Vencedor | Detalhes Técnicos (via `houseCARL`) |
|---|---|---|---|
| **PERK real (deve ser concedido na Bruxaria)** | `['ShadowSpellPackage.esp', 0x0028ae, '_SSPDarkArts', 'PERK']` | `ShadowSpellPackage.esp` | Nome: `Artes das Trevas`. Descrição: `Shadow spells deal 25% more damage.` Possui EntryPoint `ModSpellMagnitude` (`MultiplyValue`, fator `1.25`) filtrado por keyword `_SSPDamageKeyword` (`0x0028ad`). Condição do perk: `HasPerk(0028B0:_SSPDarkArts60) == 0`. |
| **Ability/Spell de Tomo (NÃO usar)** | `['ShadowSpellPackage.esp', 0x0521c8, '_SSPDarkArtsShadowMastery', 'SPEL']` | `ShadowSpellPackage.esp` | Nome: `Dark Arts: Shadow Mastery`. Aponta para MGEF `0x051c5e` (`_SSPAddPerkDarkArtsEffect`), que anexa o script Papyrus `_SSPAddPerkScript` (`OnEffectStart` → `AddPerk(_SSPDarkArts)` e `RemoveSpell(_SSPDarkArtsShadowMastery)`). Como `blockPapyrusEventsService.ts` bloqueia `OnEffectStart` no cliente, conceder o SPEL `0x0521c8` **não** concederia o perk. |
| **Variante +50% (referência)** | `['ShadowSpellPackage.esp', 0x0028b0, '_SSPDarkArts60', 'PERK']` | `ShadowSpellPackage.esp` | Nome: `Artes das Trevas` (`Shadow spells deal 50% more damage.`, fator `1.50`). Associado ao SPEL `0x0521c9` (`_SSPDarkArtsShadowMastery2`). Não listado na planilha (§5 pede explicitamente `+25% dano de sombra`). |

**Conclusão técnica:** A Bruxaria no Estágio 3 (`TENEBRAE`) deve conceder diretamente o **PERK** `['ShadowSpellPackage.esp', 0x0028ae, '_SSPDarkArts', 'PERK']` pelo aplicador de perks no cliente (`Actor.AddPerk`), garantindo que o EntryPoint `ModSpellMagnitude × 1.25` funcione sem depender de `OnEffectStart` Papyrus.

---

## 6. Quest e Script de Upgrades Automáticos do *Shadow Spell Package*

### 6.1. Records de Quests Gerenciadoras no Mod

| `[plugin, id local, editorId, tipo]` | Flags | Script Anexado | Função |
|---|---|---|---|
| `['ShadowSpellPackage.esp', 0x001870, '_SSPSpellManagerQuest', 'QUST']` | `17` (`StartGameEnabled \| 0x10`) | `_SSPSpellManagerScript` | Faz polling a cada 10s e substitui versões de feitiços de sombra conforme `GetAV("Destruction")`, `GetAV("Conjuration")` e `HasPerk(_SSPHexMaster)`. |
| `['ShadowSpellPackage.esp', 0x0028b6, '_SSPPerkManagerQuest', 'QUST']` | `17` (`StartGameEnabled \| 0x10`) | `_SSPPerkManagerScript` | Faz polling a cada 10s e troca `_SSPDarkArts` (`+25%`) por `_SSPDarkArts60` (`+50%`) se `Destruction >= 60`, ou concede `_SSPHexMaster` / `_SSPNightblade` se o mod for configurado no modo "automatic perks". |
| `['ShadowSpellPackage.esp', 0x0521e0, '_SSPDarkArtTomesManagerQuest', 'QUST']` | `17` (`StartGameEnabled \| 0x10`) | `QF__SSPDarkArtTomesManagerQu_020521E0` | Quando `Stage 10` é acionado, remove os perks `_SSPDarkArts`, `_SSPDarkArts60`, `_SSPHexMaster`, `_SSPNightblade` e injeta os Tomos de Dark Arts nas leveled lists. |

### 6.2. Análise de `_SSPSpellManagerScript` (`.psc` fonte e `.pex` decompilado via `houseCARL`)

O binário `C:\modOrganizer\mods\Shadow Spell Package\scripts\_sspspellmanagerscript.pex` foi decompilado via `housecarl_decompile_script` e comparado ao fonte `scripts\source\_sspspellmanagerscript.psc` (100% equivalente).

**O que `_SSPSpellManagerScript` faz:**
1. **`OnInit()`:** Registra um timer de 10 segundos (`RegisterForSingleUpdate(10.0)`).
2. **`OnUpdate()` (loop perpétuo a cada 10s):**
   - **Bloco 1 — Leveled Lists e Versão:** Compara `internal_version` com a Global `_SSPVersionNumber` (`0x013e04`, valor `2.0`). Na primeira execução, chama `(leveledmanager as _SSPLeveledItemManagerScript).PopulateLists()` (`0x0138a1`) para injetar os tomos nas listas de mercadores/loot, verifica `SKSE.GetVersion()`, e atualiza `internal_version = 2.0`.
   - **Bloco 2 — Upgrades Automáticos Fora de Combate (`if !playerRef.IsInCombat()`):** Lê `playerRef.GetAV("Destruction")`, `playerRef.GetAV("Conjuration")` e `playerRef.HasPerk(hexmaster)` (`0x0028b5`):
     - **Sear:** `0x001dd3` → `0x002e26` (`Dest >= 25`) → `0x002e27` (`Dest >= 50`) → `0x002e28` (`Dest >= 75`)
     - **Drain Soul:** `0x051c54` → `0x051c57` (`Dest >= 25`) → `0x051c58` (`Dest >= 50`) → `0x051c5a` (`Dest >= 75`)
     - **Shadowbolt:** `0x002e2a` (`_SSPShadowbolt01`) → `0x002e2c` (`_SSPShadowbolt02`, se `Dest >= 50`) → `0x002e2f` (`_SSPShadowbolt03`, se `Dest >= 75`). Em cada passo chama `playerRef.removeSpell(anterior)` e `playerRef.addSpell(novo, true)`.
     - **Crushing Darkness:** `0x002e30` (`_SSPCrush01`) → `0x002e32` (`_SSPCrush02`, se `Dest >= 75`).
     - **Curse of Exhaustion:** `0x00a161` → `0x00f0ab` (se `HasPerk(_SSPHexMaster)`).
     - **Curse of Fragility:** `0x00c908` → `0x00f0ac` (se `HasPerk(_SSPHexMaster)`).
     - **Conjure Shadow Atronach:** `0x04e0e6` (`_SSPConjureShadowAtronach`) → `0x04f120` (`_SSPConjureShadowAtronachAdept`, se `Conj >= 50`) → `0x04f121` (`_SSPConjureShadowAtronachExpert`, se `Conj >= 75`).
   - Ao final, chama `RegisterForSingleUpdate(10.0)` novamente.

**O que `_SSPPerkManagerScript` (`0x0028b6`) faz:**
- Também agenda `RegisterForSingleUpdate(10.0)` no `OnInit()`.
- Em `OnUpdate()`, se `!playerRef.IsInCombat()`:
  - Se `playerRef.HasPerk(_SSPDarkArts)` (`0x0028ae`, +25%) **e** `playerRef.GetAV("Destruction") >= 60`, e a Global `_SSPAutomaticPerks` (`0x050157`) for `1` (ou no bloco de upgrade do perk): no modo manual (`automatic.GetValue() == 0`, que é o padrão `0.0` em `ShadowSpellPackage.esp`), ele não dá o perk sozinho, mas se por algum motivo `automatic` fosse `1`, trocaria `_SSPDarkArts` (`+25%`) por `_SSPDarkArts60` (`+50%`) assim que o piso de habilidade chegasse a `60` no Estágio 4!

### 6.3. Neutralização dos Upgrades Automáticos Sem Editar o Modpack (Autorizada e Implementada)

Existem **duas camadas complementares** no ambiente Aetherius que neutralizam `_SSPSpellManagerQuest`, `_SSPPerkManagerQuest` e `_SSPDarkArtTomesManagerQuest` sem tocar em nenhum arquivo de `C:\modOrganizer`:

1. **Camada 1 — Bloqueio Nativo de Eventos Papyrus já existente no Cliente (`FridaHooks.cpp` + `blockPapyrusEventsService.ts`):**
   - `client-source/client/src/services/services/blockPapyrusEventsService.ts:12,16` ativa `TESModPlatform.blockPapyrusEvents(true)`.
   - Em `source/skyrim-platform/src/platform_se/skyrim_platform/FridaHooks.cpp:43-76` (`OnSendEventEnter`), quando `blockEvents` é `true`:
     - Todo evento `"OnUpdate"` tem o nome substituído por `""` na linha 75 (`strcmp(*eventName, "OnUpdate") != 0` pula até a whitelist de `SKI_`).
     - Qualquer outro evento (`"OnInit"`, `"OnEffectStart"`) em scripts que não começam com `SKI_` nem são `defaultDisableHavokOnLoad` também é substituído por `""`.
   - Nenhuma alteração foi feita em `FridaHooks.cpp` ou código nativo.

2. **Camada 2 — Parada Explícita das Quests no Cliente (`source/server/gamemode/supernatural/client.js`) + Reconciliação no Servidor (`witch-grants.js`):**
   - **No cliente (`source/server/gamemode/supernatural/client.js`):** No `updateOwner` de `aetheriusSupernaturalPerks`, um bloco isolado e facilmente reversível para uma única vez por sessão (`!state.sspQuestsStopped`) as três quests `[0x001870, 0x0028b6, 0x0521e0]` de `'ShadowSpellPackage.esp'` chamando `q.unregisterForUpdate()` e `q.stop()`.
   - **Efeito colateral conhecido e documentado (autorizado pelo dev em 15/11/2026):** Como as quests são paradas para todos os jogadores na sessão, jogadores **sem** a maldição de Bruxaria que porventura aprendessem magias do *Shadow Spell Package* por outros meios também deixam de ter o upgrade automático por nível de skill do mod; todas as versões de `Shadowbolt` e `Conjure Shadow Atronach` ficam sob reconciliação exclusiva do servidor.
   - **No servidor (`witch-grants.js`):** O catálogo gerenciado da Bruxa (`allSpells()`) inclui todas as versões de `Shadowbolt` (`0x002e2a`, `0x002e2c`, `0x002e2f`) e `Conjure Shadow Atronach` (`0x04e0e6`, `0x04f120`, `0x04f121`). A função `spellsFor(stageIndex, 'tenebrae')` retorna apenas a versão de `Shadowbolt` correspondente ao estágio (`0x002e2a` no estágio 1, `0x002e2c` nos estágios 2..5, `0x002e2f` no estágio 6) e `_SSPConjureShadowAtronachExpert` (`0x04f121`) a partir do estágio 5, permitindo que o reconciliador do servidor remova automaticamente a versão anterior ao avançar de estágio.

---

## 7. Identificação dos Itens com `*` na Planilha (`ESPECIFICACAO.md` §5)

Na aba "Maldições - Progressão", cinco feitiços da Bruxaria são marcados com asterisco (`*`):

| Item na Planilha | Escopo | `[plugin, id local, editorId, tipo]` | Por que possui `*` na planilha |
|---|---|---|---|
| **Equilíbrio*** | Global — Estágio 4 (Magus Diaboli) | `['Skyrim.esm', 0x0da746, 'MAG_Equilibrium', 'SPEL']` | Feitiço único de missão vanilla (encontrado apenas em *Labyrinthian*, não vendido em mercadores normais); sobrescrito por `MysticismMagic.esp`. |
| **Conjure Boneman*** | MORTUI — Estágio 3 (Feiticeiro Negro) | `['Dawnguard.esm', 0x0045ba, 'MAG_ConjureBoneman', 'SPEL']` | Feitiço exclusivo do *Soul Cairn* (*Dawnguard*), não disponível em mercadores comuns; sobrescrito por `MysticismMagic.esp`. |
| **Conjure Mistman*** | MORTUI — Estágio 5 (Alto Teurgo) | `['Dawnguard.esm', 0x0045b8, 'MAG_ConjureMistman', 'SPEL']` | Feitiço exclusivo do *Soul Cairn* (*Dawnguard*); sobrescrito por `MysticismMagic.esp`. |
| **Conjure Wrathman*** | MORTUI — Estágio 6 (Hierofante) | `['Dawnguard.esm', 0x0045b3, 'MAG_ConjureWrathman', 'SPEL']` | Feitiço exclusivo do *Soul Cairn* (*Dawnguard*); sobrescrito por `MysticismMagic.esp` e `AETHERIUS - BALANCE.esp`. |
| **Conjure Keeper*** | MORTUI — Estágio 6 (Hierofante) | `['PuddingFace_SimpleSpellsPackage.esp', 0x0008d0 / 0x0008d2, ...]` | Quarto tipo de morto-vivo do *Soul Cairn* (Keeper), vindo de `PuddingFace_SimpleSpellsPackage.esp` (rebalanceado em `AETHERIUS - BALANCE.esp`). Conforme decisão de 15/11/2026, entram apenas **Archer** (`0x0008d0`) e **Warrior** (`0x0008d2`); **Knight** (`0x0008d1`) fica de fora. |

---

## 8. Pisos de Habilidade `30/40/50/60/60/60` no GameplayCore Local (Fase 2 — Implementado)

### 8.1. Regra de Design (`ESPECIFICACAO.md` §5)
- **Escolas afetadas:** `Alteration`, `Conjuration`, `Destruction`, `Illusion` (as 4 escolas da Bruxaria; `Restoration` não entra).
- **Piso por estágio (0..5 / Estágios 1..6):**
  - Estágio 1 (Neófito, `stage = 0`, `0` XP): **30**
  - Estágio 2 (Acólito, `stage = 1`, `20.000` XP): **40**
  - Estágio 3 (Feiticeiro Negro, `stage = 2`, `60.000` XP): **50**
  - Estágio 4 (Magus Diaboli, `stage = 3`, `190.000` XP): **60**
  - Estágio 5 (Alto Teurgo, `stage = 4`, `610.000` XP): **60**
  - Estágio 6 (Hierofante, `stage = 5`, `2.000.000` XP): **60**
- **Composição com a Classe:** Prevalece o **maior valor** entre o patamar da classe no nível atual e o piso da maldição (`Math.max(skillClasse, pisoBruxa)`). Se o personagem não tiver classe ativa (`classId = null`, ex.: logo após o reset de classe na concessão da maldição), a base da classe é `0` nas 18 skills e as 4 escolas da Bruxa recebem `30/40/50/60/60/60`.
- **Isolamento:** Estritamente por personagem (`characterId` / `characterKey` / `epoch`), entregue pelo servidor ao cliente via `ClassSkillsProjection`, sem código nativo (C++) e sem alterar `skymp5-client.js`.

### 8.2. Implementação Realizada (`source/server/gameplay/core/runtime/src/class-skills.ts` e `index.ts`)
1. **`source/server/gameplay/core/runtime/src/class-skills.ts`:**
   - Exportados `WITCH_SKILL_NAMES = ['Alteration', 'Conjuration', 'Destruction', 'Illusion']`, `WITCH_XP_THRESHOLDS = [0, 20000, 60000, 190000, 610000, 2000000]`, `WITCH_SKILL_FLOORS = [30, 40, 50, 60, 60, 60]`, `witchSkillFloorForXp(xpTotal)` e `applyWitchSkillFloors(baseSkills, supernatural)`.
   - `ClassSkillsProjection` recebeu o leitor opcional `readSupernatural(binding)` e os métodos `refresh(binding)` e `needsSupernaturalSync(binding)`.
   - Quando o personagem é Bruxa (`supernatural?.curse === 'witch'`), aplica `Math.max(baseSkills[name], floor)` nas 4 escolas (usando `ZERO_SKILLS` como base se `facts?.classId` for nulo).
   - Quando a maldição é concedida, sobe de estágio, é removida ou há troca de personagem no mesmo ciclo de spawn, `effectsOffset` incrementa monotonicamente `revision.effects`, garantindo que `ClassSkillProjection.receive` no cliente aceite imediatamente a atualização e restaure os valores puros da classe (ou `0` se sem classe) na remoção.
2. **`source/server/gameplay/core/runtime/src/index.ts`:**
   - `readSupernatural(binding)` consulta `host.mp?.__aetheriusSupernatural?.stateOf?.(actorId)` e valida o personagem ativo em `globalThis.aetheriusCharacterDirectory` (recusando se `creationPending === true`).
   - Encadeia `globalThis.aetheriusSupernaturalProjection.apply(actorId)` (preservando qualquer handler anterior e restaurando no shutdown do `scope`) para chamar imediatamente `classSkills.refresh(binding)` e `composition.inspect(binding)` sempre que o serviço Sobrenatural comitar (`grant`, `remove`, `addXp`, `choose`).
   - Inclui verificação periódica leve (`500 ms` no `scope`) com `classSkills.needsSupernaturalSync(binding)` para cobrir casos em que `onCharacterActive` ocorra após `actors.onReady`.
3. **Bundle e Testes:**
   - Regenerado `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core\dist\aetherius-gameplay-core.cjs` via `node runtime/build.mjs` (`npm run typecheck:runtime` sem erros).
   - Estendido `source/server/gameplay/core/runtime/tests/class-skills-delivery.test.mjs` (`10/10` testes passando, incluindo os 6 estágios da Bruxa sem classe, `Math.max(classe, pisoBruxa)` com Piromante nível 40, remoção da maldição e isolamento na troca de personagem).

---

## 9. Arquivos Entregues nesta Etapa (e Pendência de Integração em `supernatural-service.js`)

| Arquivo | Status | Descrição |
|---|---|---|
| `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\witch-grants.js` (+ espelho em `overlays\sobrenatural\files\server\gamemode\supernatural\witch-grants.js`) | **Concluído** | Catálogo completo de perks globais (`Necromancy` `0x0581dd`, `Necromaster` `0x317213`), feitiços globais, feitiços por vertente (`Keeper` apenas Archer `0x0008d0` + Warrior `0x0008d2`; `Conjure Shadow Atronach` Expert `0x04f121` direto no estágio 5), upgrades de `Shadowbolt`, perk `_SSPDarkArts` (`0x0028ae`) e pisos `30/40/50/60/60/60`. |
| `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural\client.js` (+ espelho no overlay) | **Concluído** | Parada única por sessão (`!state.sspQuestsStopped`) das 3 quests gerenciadoras do `Shadow Spell Package` (`0x001870`, `0x0028b6`, `0x0521e0`). |
| `C:\Code\Aetherius - Servidor Local\source\server\gameplay\core\runtime\src\class-skills.ts`, `index.ts`, `tests\class-skills-delivery.test.mjs` e `dist\aetherius-gameplay-core.cjs` | **Concluído** | Pisos de habilidade `30/40/50/60/60/60` com `Math.max(classe, pisoBruxa)`, reversão na remoção, isolamento por personagem e testes automatizados. |
| `C:\Code\Aetherius - New Systems\Sobrenatural\ui-core-module\modules\supernatural\supernatural-data.js` (+ cópia em `C:\modOrganizer\Mods\AETHERIUS - UI CORE (LOCAL)\MeridianUI\aetheriusui\modules\supernatural\supernatural-data.js`) | **Concluído** | Estágio 4 da Bruxa atualizado com `'Necromestre'` e listas de magias/perks por estágio nas 3 vertentes (`mortui`, `daemones`, `tenebrae`). |
| `C:\Code\Aetherius - Servidor Local\overlays\sobrenatural\teste-servico.cjs` | **Concluído** | Testes Node cobrindo `witch-grants.js` e a neutralização das quests do `Shadow Spell Package` em `client.js`. |
| `C:\Code\Aetherius - Servidor Local\source\server\gamemode\supernatural-service.js` (+ espelho no overlay) | **PENDENTE (mecanismo por origem)** | A ligação de `witch-grants.js` (`witch.spellsFor(stage, c.vertente)`, `witch.perksFor(stage, c.vertente)`, `witch.allSpells()`, `witch.allPerks()`) ao `supernatural-service.js` está **PENDENTE** do mecanismo de rastreamento por **origem** (o Sol High foi parado pelo limite do Codex e o mecanismo por origem ainda não foi entregue). |

